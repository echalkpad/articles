# How a matchmaking algorithm saved lives

[Original URL](https://medium.com/@UofCalifornia/how-a-matchmaking-algorithm-saved-lives-2a65ac448698#.wjkggv616)

> Long before dating sites, a pair of economists delved into the question of matchmaking, and hit upon a formula with applications far beyond romance.Would you let an economist set you up on a date?

Long before dating sites, a pair of economists delved into the question of matchmaking, and hit upon a formula with applications far beyond romance.

![](https://cdn-images-2.medium.com/max/1200/1*raxup8WJ9oXOyYGQIuw2Sw.jpeg)

Would you let an economist set you up on a date?

![](https://cdn-images-2.medium.com/max/800/1*eUmrMbOqvJDZVZR4VACeAQ.png)

Economics is often associated with the idea of money. But the field extends beyond what can be (or should be) monetized.

In the 1960s, researchers David Gale and Lloyd Shapley embarked upon research to take up an unlikely subject: matchmaking.

Funded in part by the [Office of Naval Research](http://www.goldengooseaward.org/awardees/zfh0utmzft7uewzc3lscuvdp21ogw2), they were interested in the math behind pairing people up _**_ with partners who returned their affections.

![](https://cdn-images-2.medium.com/max/1200/1*KyXDDH007fDh2pZ0GA81Tw.jpeg)

Suppose you had a group of men and a group of women who wanted to get married. Gale and Shapely wanted to see if they could develop a formula to pair everyone off as happily as possible.

Here's [an example](http://wordplay.blogs.nytimes.com/2013/01/21/stabl/) inspired by Jane Austen's "Pride and Prejudice":

![](https://cdn-images-2.medium.com/max/1200/1*uD6C_sJORbJ6jFBZqOkH6Q.png)

The goal is to find stable matches between two sets of people who have different preferences and opinions on who is their best match.

The central concept is that the matches should be stable: There should be no two people who prefer each other to the partners they actually got.

![](https://cdn-images-2.medium.com/max/800/1*T_wJcwVUPkB2I-IyAW2yXg.gif)

Gale and Shapely developed the deferred acceptance algorithm (also known as the Gale-Shapley algorithm).

It [establishes a system](http://mathsite.math.berkeley.edu/smp/smp.html) by which everyone is able to find the person they _most_ prefer from _among those_ who prefer them.

The men and women each rank their preferences.

![](https://cdn-images-2.medium.com/max/800/1*mNtOmBvaWPI0h-ujmh2mNA.jpeg)

And then they are sorted using the algorithm:

![](https://cdn-images-2.medium.com/max/800/1*4LMVIviLozFECMLkTHKZWQ.gif)

For any number of partners, no matter how they rank each other, it is possible to use the Gale-Shapley algorithm to find at least one stable partnership for each person.

![](https://cdn-images-2.medium.com/max/800/1*sbnV6fXmAm3s4BcqFjx-tA.jpeg)

You may have noticed that out in the real world, this isn't exactly how dating or marriage works. For example, the model doesn't take into account gay couples, bisexuality, or people who prefer to be single.

![](https://cdn-images-2.medium.com/max/800/1*pqASghqwxf0jyNDrCxXxgQ.jpeg)

So what's the value of this kind of research? A lot, as it turns out.

Gale and Shapely weren't really trying to crack the code on romance. What they were seeking was an approach to so-called matching markets -- where there is supply and demand, but no money changes hands. Marriage was simply a way to illustrate the problem.

When they began, their work was purely theoretical. But as is often the case with basic research, it ended up having applications in practical and important ways.

![](https://cdn-images-2.medium.com/max/600/1*CG9GPE_JNo95clfIIoNKpw.png)

In the 1980s, a Harvard economist named Alvin Roth (now at Stanford) was interested in approaching economics like an engineering discipline -- using theoretical ideas to improve real-world systems.

He wanted to diagnose matching markets that weren't working and adapt the Gale-Shapely algorithm to help them work more efficiently.

Roth, with [backing from the National Science Foundation](http://www.goldengooseaward.org/awardees/zfh0utmzft7uewzc3lscuvdp21ogw2), began looking at the National Residency Match Program (NRMP), a system that assigns new doctors to hospitals around the country.

In the 1990s, the NRMP was struggling because new doctors and hospitals were often both unsatisfied with its assignments.

Roth used Gale and Shapely's work to [reshape the NRMP](http://www.nobelprize.org/nobel_prizes/economic-sciences/laureates/2012/popular-economicsciences2012.pdf) matching algorithm so that it produced matches that were more stable.

The Gale-Shapley algorithm also proved useful in helping large urban school districts assign students to schools.

New York, like many cities, enables students to select a high school by ranking their preferred choices from among all its schools.

Before Roth and his colleagues redesigned it, the public high school assignment process was a mess. [About 30,000 students a year](http://www.nytimes.com/2014/12/07/nyregion/how-game-theory-helped-improve-new-york-city-high-school-application-process.html?_r=2) were left unmatched and ended up at schools they hadn't even listed.

![](https://cdn-images-2.medium.com/max/800/1*4L79fCFUmQW9Lab_iuZ8FQ.png)

The process of matching doctors or students is a little more complex than matching romantic partners since hospitals and schools -- unlike most couples -- accept many proposals.

But the underlying principle of deferred acceptance that Gale and Shapley defined is the same.

The real breakthrough came in 2004\. That is when Roth developed the matchmaking principle to help transplant patients find donors.

At the time, [less than 20 people](http://forward.com/news/breaking-news/164327/alvin-roth-transformed-kidney-donation-system/) each year received kidneys from living donors, even though transplants from living donors produce much better patient outcomes.

The frequency of these life-saving procedures was limited by a simple, heartbreaking problem: Many people are willing to donate a kidney to a loved one but they cannot because blood type and other factors make them incompatible.

![](https://cdn-images-2.medium.com/max/1200/1*8i7PPrbNHldaxAO0uFggNw.jpeg)

Roth devised an exchange system to help incompatible donor-recipient pairs find others in the same situation. Through complex chains of exchange, all participants had the promise of finding a suitable match.

![](https://cdn-images-2.medium.com/max/800/1*EI4SVLKYx1bT6bRt5opnqQ.gif)

The result: [thousands of people](http://forward.com/news/breaking-news/164327/alvin-roth-transformed-kidney-donation-system/) have been able to receive kidneys who otherwise might not have been able to get them.

It was a leap that earned Shapley and Roth the Nobel Prize in 2012\. (David Gale passed away in 2008.)

![](https://cdn-images-2.medium.com/max/800/1*E_o_2SAyqmoX73d-KpuVXA.jpeg)

The formula is now being employed for other uses, such as helping kids in foster care find adoptive parents.

It has even found [21st century applications in romance](http://www.theguardian.com/business/2012/oct/15/nobel-economics-prize-winners-dating-problem), influencing approaches to online dating and speed dating.

Take any invention or modern innovation and in its history you'll find decades -- or even centuries -- of odd and obscure research that led to its creation.

One of the hallmarks of science is that the path to knowledge is often indirect. In addition to rigorous investigation, discovery is often shaped by serendipity and human curiosity.

When Gale and Shapley began, their work was theoretical and abstract. Their research may have seemed obscure or even pointless, but the insights they gleaned built the foundation for breakthroughs that have improved countless people's lives.

Today, [roughly 5,500 transplant patients](https://www.kidney.org/news/newsroom/factsheets/Organ-Donation-and-Transplantation-Stats) in the U.S. receive kidneys each year from living donors.

These happy matches wouldn't be possible without the work of Roth, Gale and Shapely.

Like love, research works in mysterious ways. The results and impacts are sometimes unpredictable and unexpected -- and that's a big part of what makes it so important.

![](https://cdn-images-2.medium.com/max/2000/1*pXolVy-RCwUvomWMAaaUcQ.png)

![](https://cdn-images-2.medium.com/max/800/1*KPaAMNjvKC_RB0eSMVNiaQ.gif)

[![](https://cdn-images-2.medium.com/max/400/1*plVVoRU1KDy2FOAE535IuA.png)](http://universityofcalifornia.edu/subscribe)

[![](https://cdn-images-2.medium.com/max/400/1*P57HPc9birKPqDxatKUilg.png)](https://medium.com/@uofcalifornia)

[![](https://cdn-images-2.medium.com/max/400/1*LlzCv9ybCzNQy0FXfjrxUg.png)](http://twitter.com/@uc_newsroom)
