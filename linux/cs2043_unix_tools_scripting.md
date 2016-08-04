# CS2043: Unix Tools & Scripting

[Original URL](http://www.cs.cornell.edu/courses/CS2043/2014sp/)

> Spring 2014 Time & Place: MWF 11:15am - 12:00pm (Holister B14) Announcements: 1/29/2014: Hussam Abu-Libdeh, Atheendra PT, Sangha Im, and Harsh Shah joined the course staff. See their hours and...

## Spring 2014

**Time & Place**: MWF 11:15am - 12:00pm (Holister B14) **Announcements**:

- **1/29/2014**: Hussam Abu-Libdeh, Atheendra PT, Sangha Im, and Harsh Shah joined the course staff. See their hours and contact info below.
- **1/24/2014**: Couldn't register for the course? ~~Enter your info here: Waiting List~~. **Update 1/29:** Sorry, registration is now closed.

## Overview

UNIX-like systems are increasingly being used on personal computers, mobile phones, web servers, and many other systems. They represent a wonderful family of programming environments useful both to computer scientists and to people in many other fields, such as computational biology and computational linguistics, in which data is naturally represented by strings. This course provides an intensive training to develop skills in Unix command line tools and scripting that enable the accomplishment and automation of large and challenging computing tasks. The syllabus takes students from shell basics and piping, to regular-expression processing tools, to shell scripting and Python.

CS2043 is a six week, two credit, S/U only course. It runs from **January 22** through **March 5, 2014**. The drop deadline is **February 5, 2014**, two weeks into the course.

## Course Staff

**Instructor**: | **[Bruno Abrahao](http://www.cs.cornell.edu/~abrahao)** | [last_name@cs.cornell.edu]  | **Office hours**: | Wednesday 3-4pm, Gates Hall, room G21
--------------- | ------------------------------------------------------- | --------------------------- | ----------------- | ----------------------------------------
**TAs**:        | **[Hussam Abu-Libdeh](http://0xff.co/)**                | [first_name@cs.cornell.edu] | **Office hours**: | Tuesday 2-3pm, Gates Hall, room 440
                | Atheendra PT                                            | [ap778@cornell.edu]         | **Office hours**: | Monday 1:30-2:30pm, Upson Hall, room 360
                | Sangha Im                                               | [si237@cornell.edu]         | **Office hours**: | Thursday 2-3pm, Gates Hall, room G17
                | Harsh Shah                                              | [hs634@cornell.edu]         | **Office hours**: | Friday 2-3pm, Gates Hall, room G11

Join the discussion on **[Piazza](http://www.piazza.com/cornell/spring2014/cs2043)** **<br>
Note:** Please don't disclose parts of your solution on piazza. You can ask for clarifications without revealing your attempts to solve the problem.

## Organization

There is no official textbook for this course; we will post pointers to some books and websites that might be helpful.

The course prerequisites include elementary-level background in computer programming without assuming mastery in any particular language.

The work for the course will consist primarily of 5 or 6 homework assignments. You must successfully complete all these assignments to pass this class. Coursework should be handed in through [CMS](https://cms.csuglab.cornell.edu/web/auth/?action=loginview).

## Lectures

- **1/22/2014** _[Lecture 1](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture01.pdf)_: A brief intro to Unix.
- **1/24/2014** _[Lecture 2](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture02.pdf)_: The Unix file system.
- **1/27/2014** _[Lecture 3](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture03.pdf)_: More on the Unix file system, text editing: nano and vim (basics).
- **1/29/2014** _[Lecture 4](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture04.pdf)_: Shortcuts keys, history, aliasing, character expansion, and file compression.
- **1/31/2014** _[Lecture 5](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture05.pdf)_: wc, sort, uniq, tr, pipes and redirection, tee, backticks
- **2/03/2014** _[Lecture 6](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture06.pdf)_: cut, paste, split, join, bc, find, grep
- **2/05/2014** _[Lecture 7](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture07.pdf)_: ssh, scp, processes: nice, kill, top; jobs.
- **2/07/2014** _[Lecture 8](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture08.pdf)_: Regular Expressions
- **2/10/2014** _[Lecture 9](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture09.pdf)_: sed, cron
- **2/12/2014** _[Lecture 10](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture10.pdf)_: diff, awk (1/3)
- **2/14/2014** _[Lecture 11](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture11.pdf)_: awk (2/3)
- **2/19/2014** _[Lecture 12](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture12.pdf)_: screen, gnuplot
- **2/21/2014** _[Lecture 13](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture13.pdf)_: awk (3/3), scripting preliminaries
- **2/24/2014** _[Lecture 14](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture14.pdf)_: conditionals
- **2/26/2014** _[Lecture 15](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture15.pdf)_: loops
- **2/28/2014** _[Lecture 16](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture16.pdf)_: Bash tips, arrays, and functions
- **3/03/2014** _[Lecture 17](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture17.pdf)_: Python 1/2
- **3/05/2014** _[Lecture 18](http://www.cs.cornell.edu/courses/CS2043/2014sp/lectures/lecture18.pdf)_: Python 2/2

## Homeworks

- **1/24/2014** _[Homework 1](http://www.cs.cornell.edu/courses/CS2043/2014sp/assignments/hw1.pdf)_. Due Wednesday, January 29, 2014.
- **2/03/2014** _[Homework 2](https://cms.csuglab.cornell.edu/web/auth/?action=download&downloadtype=1&id=39)_. Due Sunday, February 9, 2014, 11:59pm EST. [**Solutions**](https://cms.csuglab.cornell.edu/web/auth/?action=download&downloadtype=0&id=11)
- **2/10/2014** _[Homework 3](https://cms.csuglab.cornell.edu/web/auth/?action=download&downloadtype=1&id=64)_. Due Sunday, February 16, 2014, 11:59pm EST. [**Solutions**](http://www.cs.cornell.edu/courses/CS2043/2014sp/assignments/hw3_solution.pdf)
- **2/21/2014** _[Homework 4](https://cms.csuglab.cornell.edu/web/auth/?action=download&downloadtype=1&id=89)_. Due Friday, February 28, 2014, 11:59pm EST. [**Solutions**](http://www.cs.cornell.edu/courses/CS2043/2014sp/hw4-solutions.html)
- **3/02/2014** _[Final Project](https://cms.csuglab.cornell.edu/web/auth/?action=download&downloadtype=1&id=115)_. Due Sunday, March 9, 2014, 11:59pm EST. [**Solutions**](http://www.cs.cornell.edu/courses/CS2043/2014sp/final_project_solutions.html)

**<br>
New: Updated Late Homework Policy** (comes into effect starting with Homework 2): Each student will be granted a total of two late days that can be spent towards submitting homeworks late. We will deduct a late day from your account whenever you submit an assignment after the deadline, and we will grade assignments only if you have enough credit to account for the late days. If you are unable to submit your homework because of extenuating circumstances (medical or family emergency), contact an instructor beforehand. **Important**: You will need to notify the course staff whenever you make a late submission, otherwise we won't notice your assignment in the system.

## Useful references (not required)

- _The Linux Command Line book [(Free)](http://linuxcommand.org/tlcl.php)_
- _Learning Python (5th Edition)_, Mark Lutz, O'Reilly, 2013
- _Python Pocket Reference_, Mark Lutz, O'Reilly, 2014
- _Python in a Nutshell (2nd Edition)_, Alex Martelli, O'Reilly, 2006
- _Mastering Regular Expressions_, Jeffrey Friedl, O'Reilly, 2006
