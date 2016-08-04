# Lessons Learned in Software Development

[Original URL](http://henrikwarne.com/2015/04/16/lessons-learned-in-software-development/)

> Here is my list of heuristics and rules of thumb for software development that I have found useful over the years: Development 1\. Start small, then extend. Whether creating a new system, or...

Here is my list of heuristics and rules of thumb for software development that I have found useful over the years:

[![Programming bookshelf](https://henrikwarne1.files.wordpress.com/2015/04/dsc_0566.jpg?w=466&h=350)](https://henrikwarne1.files.wordpress.com/2015/04/dsc_0566.jpg)

## Development

**1\. Start small, then extend.** Whether creating a new system, or adding a feature to an existing system, I always start by making a very simple version with almost none of the required functionality. Then I extend the solution step by step, until it does what it is supposed to. I have never been able to plan everything out in detail from the beginning. Instead, I learn as I go along, and this newly discovered information gets used in the solution.

I like this quote from John Gall: _"A complex system that works is invariably found to have evolved from a simple system that worked."_

**2\. Change one thing at a time.** When you develop, and some tests fail, or a feature stops working, it's much easier to find the problem if you only changed one thing. In other words, use short iterations. Do one thing, make sure it works, repeat. This applies down to the level of commits. If you have to refactor the code before you add a new feature, commit the refactoring first, then (in a new commit) add the new feature.

**3\. Add logging and error handling early.** When developing a new system, one of the first things I do is adding logging and error handling, because both are useful from the very beginning. For all systems that are bigger than a handful of lines of code, you need some way of knowing what happens in the program. Perhaps not when it is working as expected, but as soon as it doesn't, you must be able to see what's happening. The same goes for error handling – errors and exceptions happen in the beginning too, so the sooner you handle them in a systematic way, the better.

**4\. All new lines must be executed at least once.** Before you are done with a feature, you have to test it. Otherwise, how do you know that it does what it is supposed to do? Often, the best way is by automatic tests, but not always. But no matter what, _every new line of code has to be executed at least once_.

Sometimes it can be hard to trigger the right conditions. Fortunately, it's easy to cheat a bit. For example, the error handling on a database call can be checked by temporarily misspelling a column name. Or, an if-statement can be temporarily inverted ("if error" becomes "if not error") in order to trigger something that rarely happens, just to make sure that code is run and does what it should.

Sometimes I see bugs that show that a certain line of code can never have been run by the developer. It can look fine when reviewed, but still not work. You avoid embarrassment if your policy is to always execute every new line you write.

**5\. Test the parts before the whole.** Well-tested parts save time. Often there are problems with integrating different parts, for example from mismatched or misunderstood interfaces between modules. If you can trust that the parts work as expected, it becomes much easier to track down the integration problems.

**6\. Everything takes longer than you think.** Especially in programming. It is hard to estimate how much time a feature will take even if everything goes smoothly. But when developing software, it is quite common to run in to unexpected problems: a simple merge turns out to cause a subtle bug, an upgrade of a framework means some functions must be changed or an API call doesn't work as promised.

I think there is a lot of truth in Hofstadter Law: _It always takes longer than you expect, even when you take into account Hofstadter's Law._

**7\. First understand the existing code.** Most coding requires changing existing code in some way. Even if it is a new feature, it needs to fit into the existing program. And before you can fit the new stuff in, you need to understand the current solution. Otherwise you may accidentally break some of the existing functionality. This is means that _reading_ code is a skill that is as necessary as _writing_ code. It is also part of the reason why seemingly small changes can still take a long time – you must understand the context in which you make the change.

**8\. Read and run.** Fortunately, there are two complementary methods for understanding code. You can read the code, and you can run the code. Running the code can be a great help when figuring out what it does. Be sure to make use of both methods.

## Troubleshooting

**9\. There will always be bugs.** I don't like approaches to software development that claim to "get it right the first time". No matter how much effort you put in, there will always be bugs (the definition of a bug pretty much is: "we didn't think of that"). A much better approach is to have a system in place that lets you quickly troubleshoot problems, fix the bugs and deploy the fixes.

**10\. Solve trouble reports.** Every developer should spend a portion of their time handling trouble reports from customers and fixing bugs. It gives you a much better understanding of what the customers are trying to do, how the system is used, how easy or hard it is to troubleshoot and how well the system is designed. It's also a great way of taking responsibility for what you develop. Don't miss out on all these benefits.

**11\. Reproduce the problem.** The first step when fixing a bug is to reproduce the problem. Then you make sure that when the fix is added, the problem is gone. This simple rule makes sure you are not assuming something is a problem when it isn't, and makes sure the solution actually does what you think it does.

**12\. Fix the known errors, then see what's left.** Sometimes there are several problems present that you know about. The different bugs can interact with each other and cause strange things to happen. Instead of trying to work out what happens in those cases, fix all the know problems and then see what symptoms remain.

**13\. Assume no coincidences.** When testing and troubleshooting, never believe in coincidences. You changed a timer value, and now the system restarts more often. Not a coincidence. A new feature was added, and an unrelated feature becomes slower? Not a coincidence. Instead, investigate.

**14\. Correlate with timestamps.** When troubleshooting, use the timestamp of events as a help. Look for even increments. For example, if the system restarted, and a request was sent out around 3000 milliseconds before, maybe a timer triggered the action that lead to the restart.

## Cooperation

**15\. Face to face has the highest bandwidth.** When discussing how to solve a problem, being face to face beats video, call, chat and email. I am often amazed at how much better the solutions are after discussing them in person with colleagues.

**16\. Rubber ducking.** Whenever you are stuck, go to a colleague and explain the problem to them. Many times, as you talk, you realize what the problem is, even if your colleague doesn't say a word. Sounds like magic, but works surprisingly often.

**17\. Ask.** Reading and running the code is often great for figuring out what it does and how it works. But if you have the possibility to ask someone knowledgeable (perhaps the original author), use that option too. Being able to ask specific questions, and follow-up questions to those, can give you information in minutes that would otherwise take days to get.

**18\. Share credit.** Make sure to give credit where credit is due. Say: "Marcus came up with the idea to try..." (if he did), instead of "we tried ...". Go out of your way to mention who else helped or contributed.

## Miscellaneous

**19\. Try it.** If you are unsure of how a certain language feature works, it is easy to write a little program that shows how it works. The same applies when testing the system you are developing. What happens if I set this parameter to -1? What happens if this service is down when I reboot the system? Explore how it works – fiddling around often reveals bugs, and at the same time it deepens your understanding of how the system works.

**20\. Sleep on it.** If you are working on a difficult problem, try to get in a night's sleep before you decide. Then your subconscious mind works on the problem even when you aren't actively thinking about it. As a result, the solution can seem obvious the next day.

**21\. Change.** Don't be afraid to change roles or jobs every once in a while. It is stimulating to work with different people, on a different product or in a different company. In my view, too many people just passively stay at the same job year after year, only changing if they are forced to.

**22\. Keep learning.** One of the great things with software development is that there is always room to learn and know more. Try out different programming languages and tools, read books on software development, take MOOC courses. Small improvements soon add up to make a real difference in your knowledge and abilities.

## Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

## _Related_
