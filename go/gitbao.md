
[Original URL](https://medium.com/@maxmcdonnell/gitbao-bcab6bd15b2b)

> I participated in Recurse Center (formerly known as Hacker School) at the beginning of this year. One of the more surprising realizations while hacking away was the general hesitation to deploy code...

I participated in [Recurse Center](https://www.recurse.com) (formerly known as Hacker School) at the beginning of this year. One of the more surprising realizations while hacking away was the general hesitation to deploy code to the web. It wasn't really about the complexity, it was more about the overhead and cost of deploying code.

The cost of Digital Ocean's $5/mo and Heroku's $7/mo plans initially seem trivial for growth companies, but can be a real nuisance for small personal and hobby projects. Who wants to pay even a penny to host an application that has five users? No one. This especially affects individual and beginner developers the most. The very developers we should be helping are getting hit the hardest.

As a hobbyist, it would be ideal if hosting had the following qualities:

1. Nearly zero overhead to deploy my project.

2. Little ongoing maintenance and upkeep.

3. Super-friendly cost structure that provides a generous free tier.

[**Gitbao**](https://gitbao.com) _**_ is a little hack of an application that attempts to show what deploying small projects could be like (for now, it only works with Go applications 😊).

Here's what it's like deploying an application on Gitbao:

![](https://cdn-images-1.medium.com/max/800/1*rNPRDsT8s0nPOY7sTyOMhw.gif)

Runtime: 15 seconds ([source video](https://www.youtube.com/watch?v=ZiP3SG5D3xQ))

Gitbao applications are deployed from Github Gists. Gitbao grabs the Gist files, confirms they are a Go application, downloads any dependencies and then packages and deploys the application.

Once your application is deployed, you can use it whenever you want. Don't worry about uptime or scaling. Your application is only run when you use it, and you can just forget about the application if you ever stop using it.

And for now...all of this is free.

There are a few costs to providing these benefits.

1. Gitbao is weird. It proxies HTTP requests through Go binaries run by a NodeJS script in AWS Lambda. Eeek!

2. As a result of #1, requests take a little longer, anywhere from 100ms to 1s, depending on how often requests are made.

3. No memory persistence. [A free datastore](https://github.com/maxmcd/baodata) is provided to _slightly_ ease this pain point.

4. Gitbao is an experiment. That means it might go down, and proxying requests creates quite a few bugs and restrictions.

5. Gitbao is free. Mostly because it is unbelievably cheap to run. If that changes, and a ton of people use it, it might cost money, but certainly not much.

Gitbao is a hack, but I think it's a cool hack, and maybe it points to a different future for how we run services on the internet.

[Check out Gitbao](https://gitbao.com), or the [Bao datastore](https://github.com/maxmcd/baodata).

Gitbao is[open source](https://github.com/maxmcd/gitbao), pull requests, issues, and feedback and welcome!
