# Cloudless: An Open Source Computer Vision Tool for Satellite Imagery -- Planet Stories

[Original URL](https://medium.com/planet-stories/cloudless-an-open-source-computer-vision-tool-for-satellite-imagery-6f4daaa4851f#.ahicvggr4)

> Cloudless: An Open Source Computer Vision Tool for Satellite ImageryI'm proud to announce the 1.0 release of Cloudless, an open source computer vision pipeline for orbital satellite data, powered by...

Cloudless: An Open Source Computer Vision Tool for Satellite Imagery

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*i7x05DzAc0RHtp3yBIHgGQ.jpeg)

I'm proud to announce the 1.0 release of [Cloudless](http://codinginparadise.org/ebooks/html/blog/introducing_cloudless.html), an open source computer vision pipeline for orbital satellite data, powered by data from Planet Labs and using deep learning under the covers. The Cloudless project was born during Dropbox's week-long Hack Week, a tradition where engineers and invited guests get to work on any project, no matter how blue sky. Cloudless was created by [Johann Hauswald](http://web.eecs.umich.edu/~jahausw/), [Max Nova](https://www.linkedin.com/in/maxnova), and [myself](http://codinginparadise.org/).

One of the most exciting goals of Planet Labs' fleet of satellites is to produce daily imagery of the earth's surface, creating a flood of visual data that is greater than any person can efficiently process. Can we have computers do the detection and localization of items in orbital satellite data instead?

Deep learning applies biologically-inspired computer neural networks to solving difficult problems in computer vision, language understanding, and more. It is a natural fit for automated detection in satellite imagery.

In Cloudless we decided to focus initially on applying deep learning to detecting clouds in Planet Labs' satellite imagery. Eliminating or ignoring clouds is an important pre-processing step for nanosat imagery, as we want to track changes on the ground -- cars appearing, forests disappearing, etc. -- instead of changes in cloud cover. Being able to first detect clouds is therefore important.

Note that even though the Cloudless pipeline is currently focused on cloud detection and localization, the entire pipeline focuses on the needs of any visual orbital detection and localization task and can be applied to other problems with some modification.

The Cloudless pipeline consists of three pieces:

- The first is an annotation tool that feeds in Planet Lab and RapidEye imagery via the Planet Lab API and allows users to draw bounding boxes around clouds to generate training data necessary to train models

- The second piece is a training pipeline that takes the annotated data, runs it on Amazon Web Services to fine-tune a neural network using [Caffe](http://caffe.berkeleyvision.org/), and then generates statistics on how well the trained model performs.

- The final portion is a bounding box system that takes the trained neural network and attempts to draw bounding boxes around the target class on orbital satellite data, in this case clouds.

Here are examples showing the original image and detected clouds; detected clouds are overlaid with yellow boxes below:

![](https://d262ilb51hltx0.cloudfront.net/max/800/0*k8lkMEwRjTVpQjjF.)

![](https://d262ilb51hltx0.cloudfront.net/max/800/0*oiB1hdKQxFNv3lOA.)

I've enjoyed working with Planet Lab's API while creating Cloudless, whether it is the ease-of-use and power of the API, the scope and availability of Planet Lab's satellite imagery, or how helpful everyone at Planet Labs was with our questions. If you're thinking of using Planet Labs' API, I highly recommend it!

Learn more about Cloudless [here](http://codinginparadise.org/ebooks/html/blog/introducing_cloudless.html).
