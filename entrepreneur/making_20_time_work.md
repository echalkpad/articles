# Making 20% Time Work

[Original URL](http://begriffs.com/posts/2016-01-29-making-twenty-percent-time-work.html)

> Following Google's lead many companies have instituted "20% time" for their employees. This is one day per week in which employees can work on projects of their own choosing. The...

![20% time image from lifehacker.com](http://begriffs.com/images/20ptime.png)

Following Google's lead many companies have instituted "20% time" for their employees. This is one day per week in which employees can work on projects of their own choosing. The practice keeps developers motivated and sharp, produces tools to solve company problems, and in the best case creates open source which builds prestige and helps recruitment.

But there's one problem with twenty-percent time: it takes discipline to pull it off. Without clear direction the open-source day can devolve into an unstructured quagmire, wasting money and demotivating all involved.

I'd like to share a technique I developed to organize teamwork on open-source days. It helps each person do the work they love while coordinating everyone into an efficient machine. I noticed remarkable results after applying it for a few weeks. My coworkers went from diffuse experiments to regularly making tools that would trend on Github. It felt great and best of all it is reproducible.

We'll need some background to understand the technique. Open source – in fact any project – requires various kinds of contributions to succeed. A thriving project is more than a pile of code. It's the packaging, explanation, outreach, and empathy of maintainers that make a good project great. I'll begin by classifying contributions and then explain how to put them into a "contribution matrix" to coordinate the team.

## The Acronym (BASEDEF)

There are seven main types of project contributions. To make them easy to remember I use the acronym **BASEDEF**. This acronym will form the kernel of organizing an inspiring hack day. We'll see how that works later, but first let's examine each letter.

**B** is for **Blog**. The first way to contribute to a project is to Blog about it. When you stumble upon a project written by someone else (your coworker, say) you have a fresh perspective on how it works. You tend to see a clear high-level overview of the project and this makes you the perfect person to write an introductory blog post explaining the project. You can compare it to other projects, or explain how to get started using it. Healthy projects need the perspective of many people.

The next way to contribute is to **Apply** the project in one of your own. The best thing for a new tool is to be put into use. For example my friend wrote a shellscript testing framework. I decided to try testing some shellscripts on my system with this framework. Doing so revealed missing features that he subsequently added.

The next type of contribution, **Suggest** is as useful as it is simple to do. Try to follow the project documentation and see if you can have one small victory in less than five minutes. This is called the "five-minute test." After trying it out, tell the project maintainer how it went, what was confusing. Suggest improvements.

Now we get into coding territory. Letter **E** stands for **Extend**. This is where you add a new feature, either of your own invention or inspired either by an existing feature request on Github. Not much to say about this one. Consider adding a CHANGELOG if the project does not have it.

**Document**. It's everything from writing a separate suite of docs to improving error messages to designing tutorials. This type of contribution overlaps somewhat with Blog, but skews more toward in-depth technical writing. Making concrete examples goes a long way toward leading new users deeper into a project.

Nearing the end of the list we have **Evangelize**. This means emailing your friends about the project, or submitting it to news aggregation sites. However check with the project leader before publicizing the project; they may prefer to fix certain problems before getting a rush of new users. But don't forget the old saying that if you're not a little embarrassed by your project then you've waited too long to share it.

The final major type of contribution is a **Fix**. This covers both fixing specific bugs and improving the general process of project development. That latter includes adding a test suite, enabling continuous integration, or using static code analyzers. Good automated tests that run against pull requests reduce the project maintainers' burden.

## The Process

Now we have a vocabulary of contributions. We'll use it in a diagram I call the "contribution matrix." Here's how it works.

It's Friday morning, the start of company 20% time. Some people know exactly what they want to work on, others aren't as sure, and perhaps unaware of the current project possibilities. To start we ask each person to add to a vertical list of project names on the left side of a whiteboard. These are projects the group can work on. It's fine, and in fact common, for people to list their own pet projects. Here's an example of the whiteboard with four projects:

- ACME Widget
- PrivacyBuilder
- Testtron 3000
- Project X

When everyone is satisfied that no relevant projects are omitted, go around the group and have each person give a very quick explanation of what each project is so that everyone is up to speed. Now write a horizontal list at the top of the whiteboard to form a grid:

               | B | A | S | E | D | E | F
-------------- | - | - | - | - | - | - | -
ACME Widget    |   |   |   |   |   |   |
PrivacyBuilder |   |   |   |   |   |   |
Testtron 3000  |   |   |   |   |   |   |
Project X      |   |   |   |   |   |   |

Ask each person to add their initials to three cells that interest them. Perhaps JN wants to blog about Project X, or AB wants to document Testtron 3000\. The grid will fill with initials.

Here's where the team gets direct feedback of the most effective way to focus their effort. Some projects will emerge as more popular than others. They will have more columns of their row filled with initials. Remember each person has volunteered for three possible contributions. Each person now chooses from their original three the task which consolidates the collective work on as few projects as possible.

This technique assures that all projects are considered, that everyone is excited with their task, and that the group makes big progress on a focused list of projects.

If your company has a 20% time policy I'd encourage you to try this technique. It works well for an in-person team, but you can do it with a distributed team via a Google Spreadsheet. If you do not yet provide time for employees to contribute to open source code this approach provides a good framework for a trial day. It's also useful to apply to internal projects for improving documentation or paying off technical debt.
