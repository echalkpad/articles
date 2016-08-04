# Concept learnt while solving a simple python problem "Hackerone- Finding the percentage solution"

[Original URL](http://www.ownlocal.xyz/2015/12/concept-learnt-while-solving-simple.html)

> I was brushing up my Python skill on Hackerone, they have extremely good Python exercises. I was stuck in a question which just needed to find percentage but I learned other concepts in process of...

I was brushing up my Python skill on Hackerone, they have extremely good Python exercises. I was stuck in a question which just needed to find percentage but I learned other concepts in process of solving this.

Hackerone link(<https://www.hackerrank.com/challenges/finding-the-percentage>):<br>
**Problem Statement**

You have a record of 

<span id="MathJax-Element-1-Frame" class="MathJax">
  <span id="MathJax-Span-1" class="math">
  <span>
  <span>
  <span id="MathJax-Span-2" class="mrow">
  <span id="MathJax-Span-3" class="mi">N</span>
</span>
</span>
</span>
</span>
</span>

 students. Each record contains the student's name, and their percent marks in Maths, Physics and Chemistry. The marks can be floating values. The user enters some integer 

<span id="MathJax-Element-2-Frame" class="MathJax">
  <span id="MathJax-Span-4" class="math">
  <span>
  <span>
  <span id="MathJax-Span-5" class="mrow">
  <span id="MathJax-Span-6" class="mi">N</span>
</span>
</span>
</span>
</span>
</span>

 followed by the names and marks for 

<span id="MathJax-Element-3-Frame" class="MathJax">
  <span id="MathJax-Span-7" class="math">
  <span>
  <span>
  <span id="MathJax-Span-8" class="mrow">
  <span id="MathJax-Span-9" class="mi">N</span>
</span>
</span>
</span>
</span>
</span>

 students. You are required to save the record in a dictionary data type. The user then enters a student's name. Output the average percentage marks obtained by that student, correct to two decimal places.<br>
**Input Format**<br>
The first line contains the integer 

<span id="MathJax-Element-4-Frame" class="MathJax">
  <span id="MathJax-Span-10" class="math">
  <span>
  <span>
  <span id="MathJax-Span-11" class="mrow">
  <span id="MathJax-Span-12" class="mi">N</span>
</span>
</span>
</span>
</span>
</span>

, the number of students. The next 

<span id="MathJax-Element-5-Frame" class="MathJax">
  <span id="MathJax-Span-13" class="math">
  <span>
  <span>
  <span id="MathJax-Span-14" class="mrow">
  <span id="MathJax-Span-15" class="mi">N</span>
</span>
</span>
</span>
</span>
</span>

 lines contains the name and marks obtained by that student separated by a space. The final line contains the name of a particular student previously listed.<br>
**Output Format**<br>
Print one line: The average of the marks obtained by the particular student correct to 2 decimal places.<br>
**Constraints**<br>
2 <= N <= 10<br>
0 <= Marks <= 100<br>
**Sample Input**<br>
3 Krishna 67 68 69 Arjun 70 98 63 Malika 52 56 60 Malika

**Sample Output**<br>
56.00

## Solution process:

## Please try to solve yourself first.

The question is quite simple, you have to use a dictionary mapping name to the marks and find the average. Avg=sum of marks/number of subjects.<br>
[]()

As a novice programmer I was facing few problem such as:

How to take multiple inputs(it was more than 2) in a line

Converting inputs in two types of data Name(str) and number of marks(float) in a single line.

Printing result upto 2 decimal.

I solved those as follows:

As raw_input take string and string can be indexed and sliced.

I did the slicing like

_data=raw_input()_

_split_data=data.split(' ')_

_name=split_data[0]_

_mark=split_data[1:]_

Now I have name and list of marks _* but both strings_.*

converted str list to float using map()

_mark=map(float,mark)_

_avg=sum(mark)/len(mark)_

Also I was taking third input inside while loop which was disturbing the loop flow so my solution failed for few test cases. Took the third input outside while and it worked for all test cases.

Final solution look like this:

`` ` `` # Enter your code here. Read input from STDIN. Print output to STDOUTN=int(raw_input())marks={}if N in range(2,10): while N>0: data=raw_input() z=data.split(' ') name=z[0] mark=z[1:] mark=map(float,mark) if len(mark)!=0: avg=sum(mark)/len(mark) marks[name]="%.2f" % avg N-=1 new=raw_input() if new in marks: print marks[new] `

``

## Highly recommended for novice Python programmers. Here is the [link](https://www.hackerrank.com/domains/python/py-introduction)
