# Clever New GitHub Tool Lets Coders Build Software Like Bridges

[Original URL](http://www.wired.com/2016/02/rebuilding-modern-software-is-like-rebuilding-the-bay-bridge/)

> Getty Images Jesse Toth says that upgrading an Internet service is like building a new bridge across San Francisco Bay. In building the new eastern span of the Bay Bridge, engineers didn't...

[![github-bay-bridge-branch-abstraction-184627797](https://www.wired.com/wp-content/uploads/2016/02/github-bay-bridge-branch-abstraction184627797-582x860.jpg)](https://www.wired.com/wp-content/uploads/2016/02/github-bay-bridge-branch-abstraction184627797.jpg) 

<span class="credit link-underline-sm"> Getty Images</span>

Jesse Toth says that upgrading an Internet service is like building a new bridge across San Francisco Bay.

In building the new eastern span of the Bay Bridge, engineers didn't tear down the old one and erect the new one in its place. They built the new span alongside the old one, before making sure the new bridge could handle the same traffic. Only then did they switch all the cars over and start tearing down the old span. As Toth explains, when it comes time to rebuild software that underpins a service like Google or Facebook or Uber, the process should work in much the same way. "You battle-test this new bridge--this new code path--while the original one is still being used," she says.

Toth is an engineer at GitHub--[the company at the heart of the modern software world](http://www.wired.com/2015/03/github-conquered-google-microsoft-everyone-else/)--and today, she and her fellow GitHub engineers officially released a tool designed to ensure that your new code is ready before you disconnect your old code--in some cases, very old code. The tool is called Scientist, and as open source software, [it's freely available to all](https://github.com/github/scientist). Toth and others believe it could potentially help anyone upgrade even the largest of online services.

"I feel like the scope for this is huge. As soon as you write code, it becomes legacy code. Somebody has to maintain it, and eventually, you will need to change it," Toth says. "It's hard for people to make these changes and feel confident in them."

## Do No Harm

Scientist uses a clever engineering technique called [Branch By Abstraction](http://martinfowler.com/bliki/BranchByAbstraction.html). Basically, it wraps your old service in an extra layer of software that handles communication with the outside world, juggling all inputs and outputs. GitHub calls this an abstraction layer, or _an experiment_. You can then can write your new code to fit the abstraction layer, ensuring that it can handle all the same inputs and outputs. Once this is done and properly tested, you can flip a switch so that the abstraction points not to your old code but to the new.

The trick is that, during the testing phase, the abstraction layer can run the old code and the new _in parallel_. The same live data streams into both systems, and Scientist records any differences in behavior. Using this data, you can make any needed tweaks to your new code. "It guides code changes," Toth explains. "It makes sure they are done safely and that they're not destroying what's there already or introducing new bugs."

Toth and her colleagues built Scientist as a way of upgrading GitHub's own service, an online code repository that has become the world's primary means of sharing and editing open source software. They rewrote the permissions system that controls access to the service's thousands of repositories, and with Scientist, they could not only build the system on the fly but properly test it before it went live. "We were having trouble rewriting and replacing the code in a way we felt was safe," Toth says. "With thousands of repositories, testing whether one small change or one thing that breaks it was really hard to do."

## Old Code Is Everywhere

Scientist is designed to work with Ruby, the programming language that underpins GitHub. But according to Toth, the same ideas can be applied to any other language--or even help you move a service from one language to another. She envisions aging banks using it to upgrade decades-old [Fortran code](https://en.wikipedia.org/wiki/Fortran) to Ruby or any other modern language.

Nate Holland, an engineer with software company [SpiceWorks](https://www.spiceworks.com/), has used a earlier version of Scientist, and he calls it "an immensely useful way to do otherwise dangerous [refactors](https://en.wikipedia.org/wiki/Code_refactoring) in a relatively safe and controlled manner." That said, he points out that it is far more useful when you're upgrading much older pieces of code, as opposed to code that was built in recent years with more modern tools. "There aren't as many knots or twists that may be dangerous," he says.

But much like Toth, he sees Scientist as "an elegant, abstract concept" that could be quickly applied to any language. And in a world ever-more dependent on code, much older pieces of code are still everywhere.

[Go Back to Top. Skip To: Start of Article.](http://www.wired.com/2016/02/rebuilding-modern-software-is-like-rebuilding-the-bay-bridge/#start-of-content)
