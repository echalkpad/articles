# Data Science tricks: Simple anomaly detection for metrics with a weekly pattern

[Original URL](https://medium.com/@iliasfl/data-science-tricks-simple-anomaly-detection-for-metrics-with-a-weekly-pattern-2e236970d77#.9wgkp1scg)

> A recurring problem that engineers have to deal with is creating an alarm system to give them early warnings when things start to go wrong. For example, they may need to monitor CPU or memory usage...

A recurring problem that engineers have to deal with is creating an alarm system to give them early warnings when things start to go wrong. For example, they may need to monitor CPU or memory usage for a set of virtual machines or user-behaviour signals. Things may go wrong for a variety of reasons like the introduction of bugs in new code releases or random hardware failures. Simple heuristics can cover the most common cases. However, there are times where simple heuristics may be inadequate, while at the same time the metric may look "obviously" problematic in the eyes of an experienced engineer. This confidence is related to the fact that most aggregated metrics are quite well-behaved and follow predictable patterns.

Today I discuss a scenario where there's a well behaved metric with roughly predictable values, at least for the eyes of a human. Note that predictability is not the same as stability (a flat line). The metric is not necessarily constant -- with some minor fluctuations -- around some baseline value. Typically we may find:

- a **daily cycle:** where the metric increases to high values during working hours and it is significantly decreased during non-working hours

- a **weekly cycle:** where the metric is significantly increased during working days compared to weekends.

- a rather stable **growth pattern**: for example we may constantly add new users that create a stable upward -- combined with the daily and weekly cycles.

We will ignore some rare but well expected events like public holidays.

Here's an example of a metric that presents this kind behaviour. The blue line is the metric as measured every hour, and the shadow area represents what is normal and expected behaviour. In this article I describe how to create that "shadow" area. Observe that weekends have very low activity and indeed the shadow is quite narrow at that time. At the end of the week something goes wrong and it is the time the alarm should go off.

![](https://cdn-images-1.medium.com/max/800/1*fPqSx7v648XjnAXVi_wE5g.png)

A final requirement we have is that the system should be quite robust and automatically **adapt** to new baselines. For example, if a new release improves the CPU usage we don't want to have to re-tune the alarm system. Ideally it should warn us that CPU usage was reduced, which is expected, and then it should adapt to the new reality.

What kind of solution can we deploy? Rule based heuristics do not scale and do not adapt easily, especially if we have thousands of alarms to setup. Some statistical approach is needed that is generic enough to handle many different metric behaviours.

Our solution is a based on three components. The Exponential Moving Average (EMA), the Exponential Moving Standard Deviation (EDS), and the deployment of a set of simple models instead of a single model that tries to capture all variations.

First we need to track what's the expected normal value fro our metric. We can estimate that using the Exponential Moving Average (EMA), which is defined as:

```
EMA <- w articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii EMA + (1 - w) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii x
```

where x is the current real value measurement, and w is a small value like 0.05 that controls the influence of the new measurement to the EMA compared to the learned expectation. EMA provides the expectation of the metric at any given time and it updates anytime we have a new measurement. It has the nice property that it can automatically adapt to a new reality and needs little supervision.

EMA is a good start. However, it doesn't quantify the range around the expectation that should be considered normal. To quantify that range we need to track not only the average value but also the standard deviation (SD). We will track SD using a similar approach as with the mean value, namely the Exponential Moving Standard Deviation (EMS):

```
EMS <- sqrt( w articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii EMS^2 + (1 - w) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii (x - EMA)^2 )
```

Similarly to EMA, the EMS will automatically adapt to any new reality without needing re-training. Same as EMA, parameter w controls how fast EMS adapts to radical changes.

The two numbers, EMA and EMS define a simple model that can be used to build the alarm logic. This is as simple as:

```
Alarm = abs(x - EMA) > n articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii EMS
```

where n controls the sensitivity of the alarm to the value measurement x. If the absolute difference of EMA from x is higher than n number of standard deviations we raise the alarm. A low value for n, like 1 or 2, may lead to high number of false positives, that is alarm goes off when it shouldn't. A high value of n may lead to high number of false negatives, that is alarm doesn't go off when it should.

Now it is time for the real trick! So far we have all the basic components is place but we still haven't solved the seasonality requirements. The trick we are going to use is very simple: instead of using one single model comprised of the EMA and EMS, we are going to use multiple models, _one for each hour and day of the week_.

For example, we will have a different EMA and EMS for tracking what is normal on Mondays at 9am and a different EMA and EMS for tracking what is normal on Mondays at 10am. Using the hourly resolution we will end up with 24x7 models, again with each model having its own EMA and EMS. If the metric is indeed well-behaved then the EMA and EMS of these sub-models should be quite _stable_ over time! Depending on the problem and the desired time-resolution we may define more or less models. For example we may have a different model for every 5mins interval in the weekly cycle.

Few tips to have in mind before implementing this approach. When we make a new measurement we need first to check if the alarm should go off and then to update the EMA and EMS.

There are three parameters that need tuning. The easiest to tune is the time resolution of the alarm. We need to make sure that we can have a reliable measurement for the time window. Parameter w controls how back in history should the alarm look up to pick what is the expectation of normal behaviour. Parameter n should be manually picked and it controls the trade off between false positives and false negatives.

The total memory requirements for this solution is very low since only two real numbers are stored for each model: one for the EMA and one for the EMS. There's no need to keep the full history of measurements since any new measurement is factored in the estimations of EMA and EMS and then it can be safely discarded.