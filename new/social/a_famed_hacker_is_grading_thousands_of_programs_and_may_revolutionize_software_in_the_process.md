# A Famed Hacker Is Grading Thousands of Programs -- and May Revolutionize Software in the Process

[Original URL](https://theintercept.com/2016/07/29/a-famed-hacker-is-grading-thousands-of-programs-and-may-revolutionize-software-in-the-process/)

> At the Black Hat cybersecurity conference in 2014, industry luminary Dan Geer, fed up with the prevalence of vulnerabilities in digital code, made a modest proposal: Software...

At the Black Hat

<span class="s2"> cybersecurity conference in 2014, industry luminary Dan Geer, fed up with the prevalence of vulnerabilities in digital code, made a <a href="https://www.wired.com/2014/08/cia-0day-bounty/">
  <span class="s3">modest proposal</span>
</a>: Software companies should either make their products open source so buyers can see what they’re getting and tweak what they don’t like, or suffer the consequences if their software failed. He likened it to the ancient Code of Hammurabi, which says that if a builder poorly constructs a house and the house collapses and kills its owner, the builder should be put to death.</span>

<span class="s2">No one is suggesting putting sloppy programmers to death, but holding software companies liable for defective programs, and nullifying licensing clauses that have effectively disclaimed such liability, may make sense, </span>

<span class="s2">given the increasing prevalence of online breaches.</span>

<span class="s2">The only problem with Geer’s scheme is that no formal metrics existed in 2014 for assessing the security of software or distinguishing between code that is merely bad and code that is negligently bad. Now, that may change, thanks to a new venture from another cybersecurity legend, Peiter Zatko, known more commonly by his hacker handle “Mudge.”</span>

<span class="s2">Mudge and his wife, Sarah, a former NSA mathematician, have developed a first-of-its-kind method for testing and scoring the security of software — a method inspired partly by Underwriters Laboratories, that century-old entity responsible for the familiar circled UL seal that tells you your toaster and hair dryer have been tested for safety and won’t burst into flames.</span>

<span class="s2">Called the Cyber Independent Testing Lab, the Zatkos’ operation won’t tell you if your software is literally incendiary, but it will give you a way to comparison-shop browsers, applications, and antivirus products according to how hardened they are against attack. It may also push software makers to improve their code to avoid a low score and remain competitive.</span>

<span class="s2">“There are applications out there that really do demonstrate good [security] hygiene … and the vast majority are somewhere else on the continuum from moderate to atrocious,” Peiter Zatko says. “But the nice thing is that now you can actually see where the software package lives on that continuum.”</span>

<span class="s2">Joshua Corman, founder of <a href="https://www.iamthecavalry.org/tag/josh-corman/">
  <span class="s3">I Am the Cavalry</span>
</a>, a group aimed at improving the security of software in critical devices like cars and medical devices, and head of the Cyber Statecraft Initiative for the Atlantic Council, says the public is in sore need of data that can help people assess the security of software products.</span>

<span class="s2">“Markets do well when an informed buyer can make an informed risk decision, and right now there is incredibly scant transparency in the buyer’s realm,” he says.</span>

<span class="s2">Corman cautions, however, that the Zatkos’ system is not comprehensive, and although it will provide one indicator of security risk, it’s not a conclusive indicator. He also says vendors are going to hate it.</span>

<span class="s2">“I have scars to show how much the software industry resists scrutiny,” he says.</span>

![theintercept_MUDGE_2](https://prod01-cdn07.cdn.firstlook.org/wp-uploads/sites/1/2016/07/theintercept_MUDGE_2-1000x800.jpg) Photo: Cole C Wilson

## Software Seal of Approval

<span class="s2">When Mudge <a href="https://twitter.com/dotMudge/status/615506197479882752">
  <span class="s3">announced on Twitter</span>
</a> last year that the White House had asked him to create a cyber version of Underwriters Laboratories, praise poured in from around the security community.</span>

<span class="s2">No one knew the details, but people were confident if he was involved, it would be great.</span>

<span class="s2">“Excellent! Something everyone has talked about for decades!” the Def Con hacker conference <a href="https://twitter.com/defcon/status/615514119446228992">tweeted</a> after his announcement.</span>

<span class="s2">“That’s a concept that really could make a difference if executed well,” <a href="https://twitter.com/gdead/status/615561090756427776">wrote</a> Bruce Potter, </span>

<span class="s1">founder of the Shmoo Group crypto-security collective, which runs the annual Shmoocon security conference</span>

<span class="s2">Mudge has been tightlipped about the nature of the cyber UL ever since, but he agreed to discuss the details in advance of a <a href="https://www.blackhat.com/us-16/briefings/schedule/#measuring-adversary-costs-to-exploit-commercial-software-the-government-bootstrapped-non-profit-citl-4514">talk he’s presenting</a> next week at the Black Hat conference in Las Vegas.</span>

> "To use the car analogy, does it have seatbelts, does it have air bags, does it have anti-lock brakes?" -- Peiter Zatko

<span class="s2">He says the method their lab uses to evaluate software is based on one he taught NSA hackers in the 1990s about how to find the softest targets on an adversary’s network. (During his run back then with the famed hacker think tank L0pht Heavy Industries, Mudge and his L0pht colleagues regularly <a href="http://www.nytimes.com/1999/10/03/magazine/hack-counterhack.html?scp=2&amp;sq=l0pht%20heavy&amp;st=cse&amp;pagewanted=all">
  <span class="s3">provided advice</span>
</a> to various parts of the government.)</span>

<span class="s2">The technique involves, in part, analyzing binary software files using algorithms created by Sarah to measure the security hygiene of code. During this sort of examination, known as “static analysis” because it involves looking at code without executing it, the lab is not looking for specific vulnerabilities, but rather for signs that developers employed defensive coding methods to build armor into their code.</span>

<span class="s2">“To use the car analogy, does it have seatbelts, does it have air bags, does it have anti-lock brakes? All the things that are going to make [a hacker’s] life more difficult,” Mudge says.</span>

<span class="s2">The Zatkos say a code’s security hygiene, measured by the programming methods developers use, as well as by the tools and settings used to compile the resulting software, are good predictors of whether a software application will have serious security vulnerabilities and reliability issues.</span>

<span class="s2">
</span>

[![](https://prod01-cdn07.cdn.firstlook.org/wp-uploads/sites/1/2016/07/Bar-graph-540x424.png)](https://prod01-cdn07.cdn.firstlook.org/wp-uploads/sites/1/2016/07/Bar-graph.png)

Their algorithms run through a checklist of more than 300 items, such as whether the compiler used to convert the source code into binary inserted common protective features, like preventing portions of memory reserved for program data -- the 

[<span class="s3">“stack” and “heap”</span>](https://stackoverflow.com/questions/79923/what-and-where-are-the-stack-and-heap)

 -- from being used to hold additional software. 

<span class="s2">“Things like ASLR [<a href="https://en.wikipedia.org/wiki/Address_space_layout_randomization">
  <span class="s3">address space layout randomization</span>
</a>] and having a nonexecutable stack and heap and stuff like that, those are all determined by how you compiled [the source code],” says Sarah. “Those are the technologies that are really the equivalent of airbags or anti-lock brakes [in cars]. They’re the things that make software better than it used to be.”</span>

<span class="s2">Modern compilers of Linux and OS X not only add protective features, they automatically swap out bad functions in code with safer equivalent ones when available. Yet some companies still use old compilers that lack security features.</span>

<span class="s2">The lab’s initial research has found that Microsoft’s Office suite for OS X, for example, is missing fundamental security settings because the company is using a decade-old development environment to build it, despite using a modern and secure one to build its own operating system, Mudge says. Industrial control system software, used in critical infrastructure environments like power plants and water treatment facilities, is also primarily compiled on “ancient compilers” that either don’t have modern protective measures or don’t have them turned on by default.</span>

Asked about the findings, a Microsoft spokesperson would only say, "We are focused on security as a core component in the software development process. We developed and are committed to the [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl/default.aspx), and continue to lead the industry in creating the most secure products across all platforms."

<span class="s2">The Zatkos’ algorithms also assess the number of branches in a program; more branches mean more complexity and more potential for error. And they look at the presence of complex algorithms that could be susceptible to <a href="https://www.usenix.org/conference/12th-usenix-security-symposium/denial-service-algorithmic-complexity-attacks">
  <span class="s3">algorithmic complexity attacks</span>
</a>.</span>

<span class="s2">The lab is also looking at the number of external software libraries a program calls on and the processes it uses to call them. Such libraries make life more convenient for programmers, because they allow them to repurpose useful functions written by other coders, but they also increase the amount of potentially vulnerable code, increasing what security experts refer to as the “attack surface.” There are about 200 specific external library calls, Mudge says, that are particularly difficult to implement in a manner that ensures a given program executes safely.</span>

> If they get a really low score, "we can guarantee that ... they're doing so many things wrong that there _are_ vulnerabilities" in their code. -- Sarah Zatko

<span class="s2">The process they use to evaluate software allows them to easily compare and contrast similar programs. Looking at three browsers, for example — Chrome, Safari, and Firefox — Chrome came out on top, with Firefox on the bottom. Google’s Chrome developers not only used a modern build environment and enabled all the default security settings they could, Mudge says, they went “above and beyond in making things even more robust.” </span>

<span class="s1">Firefox, by contrast, “had turned off [ASLR], one of the fundamental safety features in their compilation.”</span>

<span class="s2">Mudge worked for Google previously, so some might accuse him of bias, but he says their algorithms, which have been vetted by an outside technical board, ensure that the automated assessments aren’t biased.</span>

<span class="s2">Software vendors will no doubt object to the methods they’re using to score their code, arguing that the use of risky libraries and old compilers doesn’t mean the vendors’ programs have actual vulnerabilities. But Sarah disagrees.</span>

<span class="s2">“If they get a really good score, we’re not saying there are no vulnerabilities,” says Sarah. But if they get a really low score, “we can guarantee that … they’re doing so many things wrong that there <em>are</em> vulnerabilities [in their code].”</span>

<span class="s2">The lab aims to prove such vulnerabilities with the second part of its testing regimen, which uses <a href="https://www.wired.com/2016/06/hacker-lexicon-fuzzing/">
  <span class="s3">fuzzing</span>
</a>, a method that involves throwing a lot of data at a program to see if it crashes or does something else it shouldn’t do.</span>

<span class="s2">“In actually executing it and crashing it, we’re confirming that, yes, this thing has bugs, this thing crashed,” Mudge says. “We were able to give it input and it behaved abhorrently.”</span>

<span class="s1">Not all crashes indicate the presence of a bug that hackers can exploit, but they do, at a minimum, indicate that a program may be unreliable for users. In the lab reports the Zatkos plan to make available to the public, they will note which crashes they found were potentially exploitable.</span>

<span class="s2">The Zatkos don’t plan to fuzz every program, only enough to show a direct correlation between programs that score low in their algorithmic code analysis and ones shown by fuzzing to have actual flaws. They want to be able to say with 90 percent accuracy that one is indicative of the other.</span>

## Mudges Storied Hacking History

<span class="s2">Mudge has a long history in the hacker and security communities. While a member of L0pht, he </span>

<span class="s1">and his L0pht colleagues </span>

<span class="s2">testified to federal lawmakers in 1998 that the group could </span>

<span class="s4">bring down the internet in 30 minutes</span>

<span class="s2"> using a serious flaw that <a href="https://www.wired.com/2008/08/revealed-the-in/">
  <span class="s3">still exists</span>
</a>.</span>

![theintercept_MUDGE_3](https://prod01-cdn07.cdn.firstlook.org/wp-uploads/sites/1/2016/07/theintercept_MUDGE_3-540x720.jpg) Photo: Cole C Wilson

<span class="s2">He also advised the Clinton administration on cybersecurity issues; was a program manager for DARPA’s Cyber FastTrack initiative, which offered fast-turnaround grants for short cybersecurity projects; and more recently, worked for Google’s Advanced Technologies and Projects Group, a sort of rapid-response skunkworks group, before leaving to launch the testing lab.</span>

<span class="s2">His interest in doing software security assessments dates back to a paper one of his L0pht colleagues wrote in 1998 about such evaluations. The idea moved from theory to practice when L0pht merged with a security startup called @Stake and began developing an automated way to do static analysis of code. That method became the basis for what a company called VeraCode does today: assess software for government and corporate clients before they buy it.</span>

<span class="s2">Chris Wysopal, CTO of VeraCode and a former L0pht colleague of Mudge’s, says clients generally won’t purchase software his company finds problematic until the software maker fixes the problems, which he says is great for other buyers.</span>

<span class="s2">“To me that’s like actually finishing the job; we’re not just pointing out the problems but helping make better software,” he says.</span>

<span class="s2">But these assessments are done privately and often on enterprise software, leaving the rest of the public with no way to assess the security of software and little leverage to force vendors to fix other poorly secured code. The Zatkos’ venture could fill that gap, Wysopal says.</span>

<span class="s2">Two years ago, Mudge says someone from the White House technology office approached him about helping to set up a government program to evaluate software. He had no interest in working inside the government and decided to set up a nonprofit instead. Although his tweet last year said the White House asked him to create the lab, the White House isn’t involved in his project.</span>

<span class="s2">Instead, with $600,000 in funding from DARPA, the Ford Foundation, and Consumers Union, he and Sarah set up the lab in the basement of their home. </span>

<span class="s1">The outside technical board that vets their methodology and algorithms includes security notables such as former NSA hacker Charlie Miller; Dino Dai Zovi, a security engineer with Square; and Frank Rieger, CTO of the German firm GSMk, which makes the Cryptophone.</span>

<span class="s2">Vendors don’t pay for the evaluations. The Zatkos choose the software they evaluate and either buy it or obtain free evaluation copies from vendor websites. They’re examining both commercial software programs and open-source ones. For each software package they test, they produce three reports. The first, automatically generated by their algorithms, scores the software on a scale between 0 and 100\. The second contains a detailed breakdown of what they found in the software and will be available for free on their website. The third report, which they plan to sell, will contain raw data from their assessments for anyone who wants to recreate them.</span>

<span class="s2">They’ve examined about 12,000 programs so far and plan to release their first reports in early 2017\. They also plan to release information about their methodology and are willing to share the algorithms they use for their predictive fuzzing analysis if someone wants them.</span>

<span class="s2">There’s already a growing interest in their work. They’re working with <em>Consumer Reports</em>, another inspiration for the lab, to develop a way to use their data to evaluate products the magazine tests. They’ve also had interest from AIG and other insurers who want to use the data to do risk-assessments of companies seeking cyber insurance.</span>

<span class="s2">But there is at least one downside to scoring software like this: Attackers can use it to gauge where they should focus their energy to find vulnerabilities, targeting low-scoring applications. Lawyers will also likely want to use the data to assess liability for companies that get hacked. Did they install risky software on their network when a measurably more secure one was available?</span>

<span class="s2">Mudge says he’s not upset about the prospect of lawyers finding joy in their scores. “We’ve been begging people to give a shit about security for a decade. … [But] there’s very little incentive if they’ve already got a product to change a product. If you come out with a quantifier saying what you’ve got is not as secure as this other one, that’s going to be an incentive for them to go out and get the other one.”</span>
