# GitHub's Scientist Softens the Terror of Updating Legacy Code

[Original URL](http://thenewstack.io/githubs-scientist-softens-terror-updating-legacy-code/)

> Every developer knows that moment of dread -- throwing the switch to move from an old system to one with updated code. No matter how many tests are run, there are always unexpected glitches...

Every developer knows that moment of dread -- throwing the switch to move from an old system to one with updated code. No matter how many tests are run, there are always unexpected glitches that appear, leaving clients unsatisfied and developers scrambling to fix the bugs as quickly as possible.

"It's the most terrifying moment when you flip the switch," said GitHub principal engineer Jesse Toth,

On Wednesday, GitHub released [Scientist](https://github.com/github/scientist) 1.0, an open source Ruby-based tool developed by Toth's team to address this problem .

Scientist is targeted to developers who are in production with a legacy system and are working on transitioning to a more modern one. It can be used for bringing massive amounts of critical old code into more modern formats, such as smoothly moving a Fortran application into Ruby. Developers to test out the new code in a real environment with real data before officially turning it on.

"Now I can watch it change in increments so when the moment comes to flip the switch, I can do so with full confidence," said Toth, who has been working with the Scientist pattern for a couple of years.

Scientist was born when GitHub engineers needed to rewrite the permissions code -- one of the most critical systems in the GitHub application. The system had grown to the point it simply could not be scaled again. Toth said there are a lot of companies in similar situations, as big data moves from legacy systems to ones using more agile, modern code.

Scientist provides metrics on the differences between production code (the control) and the code in the test environment (the candidate). The pattern wraps around both code sets to provide metrics on the differences between the two during runtime.

GitHub does make use of [Branch by Abstraction](http://martinfowler.com/bliki/BranchByAbstraction.html), an architectural pattern that makes large-scale code changes by creating an abstraction layer around the code to be exchanged. However, that pattern is limited, according to Toth. "It doesn't really ensure that the behavior of the new system will match the old system -- just that the new system will be called in all places where the old system was called."

What it does _not_ do is verify the data or validate that the new system actually works once it is called.

Enter Scientist, which creates a lightweight abstraction around the candidate (the new code). This abstraction, called an experiment, is run along side the control (production code), comparing the two sets of code at runtime. Any differences are recorded, as are execution durations, which is vital data for system administrators. Scientist produces a wealth of information for the sysadmin and developers while being invisible to the end user.

[<embed src="http://thenewstack.io/wp-content/uploads/2016/02/scientist-flow-diagram-300x166.png%20300w,%20http://thenewstack.io/wp-content/uploads/2016/02/scientist-flow-diagram-768x426.png%20768w,%20http://thenewstack.io/wp-content/uploads/2016/02/scientist-flow-diagram.png%20973w" class="alignnone wp-image-1026042" width="640">](http://thenewstack.io/wp-content/uploads/2016/02/scientist-flow-diagram.png)

During the time the code for Scientist was being written, Toth watched the building of the new Bay Bridge from Oakland to San Francisco during her commute. She saw a lot of similarities in the two processes.

The pattern lets testing and bug fixes happen incrementally and side-by-side of production code, while making sure the traffic is reaching its destination on a day-to-day basis. Running Scientist removes the uncertainty of the success of the new code.

There's a lot to get excited about with Scientist. For one thing, it catches bugs both ways. Not only will it show you where the new code is differing from the production code to ensure the new code performs as expected, it also catches data bugs in the source code.

Scientist provides data for errors that simply are not captured by traditional testing methods, no matter how well the test are written or executed. Errors caused by buggy source data.

[<embed src="http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.16-AM-300x249.png%20300w,%20http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.16-AM-768x638.png%20768w,%20http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.16-AM.png%20804w" class="wp-image-1025908 size-full" width="804">](http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.16-AM.png)

 A Scientist experiment returning data on errors.

"Since software has bugs, given enough time and volume, your data will have bugs, too," explains Toth on the blog. "Data quality problems may cause your system to behave in unexpected ways that are not tested or explicitly part of the specifications... So, while test coverage of a rewritten system is hugely important, how the system behaves with production data as the input is the only true test of its correctness compared to the legacy system's behavior."

In order to have confidence after the switch, it comes down to data, says Toth. "Data has to be correct and data has to be safe."

In addition, not only does Scientist handle happy path scenarios, it can provide data on exceptions as well. It is sophisticated enough to provide data only when the exceptions are mismatched (e.g., if an exception in the production code and in the new code behave the same way will be ignored).

"Once you have some mismatch data," clarifies Toth, "you can begin investigating individual mismatches to see why the control and candidate aren't behaving the same way. Usually you'll find that the new code has a bug or is missing a part of the behavior of the legacy code, but sometimes you'll find that the bug is actually in the legacy code or in your data. After the source of the error has been corrected, you can start the experiment again and repeat this process until there are no more mismatches between the two code paths."

But wait, it gets better. Scientist is completely scalable and is flexible enough to handle something as small as a single method to as large as a multi-year project like the transfer of the GitHub permission code.

[<embed src="http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.00-AM-300x122.png%20300w,%20http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.00-AM-768x312.png%20768w,%20http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.00-AM.png%20808w" class="wp-image-1025910 size-full" width="808">](http://thenewstack.io/wp-content/uploads/2016/02/Screen-Shot-2016-02-04-at-10.15.00-AM.png)

 Monitoring runtime usage.

A recent post on the GitHub engineering blog, [Move Fast and Fix Things](http://githubengineering.com/move-fast/) provides examples using Scientist to make a short rewrite made easier.

Toth has been working with Science live for a couple of years now and her confidence level in throwing the switch has soared. The team at GitHub wants to share this confidence with other developers.

While Scientist is written in Ruby, Toth said it's possible to use this pattern in any language. She knows there's already a version in Node js and C#, and a .net version is in development. She's excited about these translations and hopes to see more in the future.

There are a few caveats.

Scientist only provides data; it will not tell you what to do or suggest changes to fix any of the runtime issues, it. It wisely leaves that up to the engineers.

It is most effective on read operations, and is not meant to be used for any code that has side-effects. "A candidate code path that writes to the same database as the control, invalidates a cache, or otherwise modifies data that affects the original, production behavior is dangerous and incorrect," explained Toth.

She also warns that there is a performance hit when using Scientist in production. "New experiments should be introduced slowly and carefully and their impact on production performance should be closely monitored, advised Toth. "They should run for just as long as is necessary to gain confidence rather than being left to run indefinitely, especially for expensive operations."

Still, this is exciting stuff.

"I love that I can build software the same way that the bridge was built," Toth said.

What she might have dismantled is that decades-old developer rite of passage, the feeling of fear when flipping the switch on new code. That experience will fade into a tale for us OG developers to pass on to the new kids who will not have to feel the terror as they close their eyes and flip the switch.

Feature Image [via](https://pixabay.com/en/san-francisco-bay-bridge-bridge-fog-410414/) Pixabay.
