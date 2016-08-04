# Report from the VMware GPL court hearing

[Original URL](http://laforge.gnumonks.org/blog/20160225-vmware-gpl/)

> Today, I took some time off to attend the court hearing in the GPL violation/infringement case that Christoph Hellwig has brought against VMware. I am not in any way legally involved in the lawsuit....

Today, I took some time off to attend the court hearing in the [GPL violation/infringement case that Christoph Hellwig has brought against VMware](https://sfconservancy.org/copyleft-compliance/vmware-lawsuit-faq.html).

I am not in any way legally involved in the lawsuit. However, as a fellow (former) Linux kernel developer myself, and a long-term Free Software community member who strongly believes in the copyleft model, I of course am very interested in this case - and of course in an outcome in favor of the plaintiff. Nevertheless, the below report tries to provide an un-biased account of what happened at the hearing today, and does not contain my own opinions on the matter. I can always write another blog post about that :)

I [blogged about this case before](http://laforge.gnumonks.org/blog/20151029-vmware_gpl/) briefly, and there is a lot of information publicly discussed about the case, including the information published by the Software Freedom Conservancy (see the link above, the [announcement](https://sfconservancy.org/news/2015/mar/05/vmware-lawsuit/) and the associated [FAQ](https://sfconservancy.org/copyleft-compliance/vmware-lawsuit-faq.html).

Still, let's quickly summarize the facts:

- VMware is using parts of the Linux kernel in their proprietary ESXi product, including the entire SCSI mid-layer, USB support, radix tree and many, many device drivers.
- as is generally known, Linux is licensed under GNU GPLv2, a copyleft-style license.
- VMware has modified all the code they took from the Linux kernel and integrated them into something they call _vmklinux_.
- VMware has modified their proprietary virtualization OS kernel _vmkernel_ with specific API/symbol to interact with _vmklinux_
- at least in earlier versions of ESXi, virtually any block device access has to go through _vmklinux_ and thus the portions of Linux they took
- _vmklinux_ and _vmkernel_ are dynamically linked object files that are linked together at run-time
- the Linux code they took runs in the same execution context (address space, stack, control flow) like the _vmkernel_.

Ok, now enter the court hearing of today.

Christoph Hellwig was represented by his two German Lawyers, [Dr. Till Jaeger](http://www.jbb.de/en/attorneys/dr-till-jaeger) and [Dr. Miriam Ballhausen](http://www.jbb.de/en/attorneys/dr-miriam-ballhausen). VMware was represented by three German lawyers lead by [Matthias Koch](http://www.freshfields.com/profiles/matthias_koch/), as well as a US attorney, [Michael Jacobs](http://www.mofo.com/people/j/jacobs-michael-a) (by means of two simultaneous interpreters). There were also several members of the in-house US legal team of VMware present, but not formally representing the defendant in court.

As is unusual for copyright disputes, there was quite some audience following the court. Next to the VMware entourage, there were also a couple of fellow Linux kernel developers as well as some German IT press representatives following the hearing.

## General Introduction of the presiding judge

After some formalities (like the question whether or not a ',' is missing after the "Inc." in the way it is phrased in the lawsuit), the presiding judge started with some general remarks

- the court is well aware of the public (and even international public) interest in this case
- the court understands there are novel fundamental legal questions raised that no court - at least no German court - had so far to decide upon.
- the court also is well aware that the judges on the panel are not technical experts and thus not well-versed in software development or computer science. Rather, they are a court specialized on all sorts of copyright matters, not particularly related to software.
- the court further understands that Linux is a collaborative, community-developed operating system, and that the development process is incremental and involves many authors.
- the court understands there is a lot of discussion about interfaces between different programs or parts of a program, and that there are a variety of different definitions and many interpretations of what interfaces are

## Presentation about the courts understanding of the subject matter

The presiding judge continued to explain what was their understanding of the subject matter. They understood VMware ESXi serves to virtualize a computer hardware in order to run multiple copies of the same or of different versions of operating systems on it. They also understand that vmkernel is at the core of that virtualization system, and that it contains something called _vmkapi_ which is an interface towards Linux device drivers.

However, they misunderstood that this case was somehow an interface between a Linux guest OS being virtualized on top of vmkernel. It took both defendant and plaintiff some time to illustrate that in fact this is not the subject of the lawsuit, and that you can still have portions of Linux running linked into vmkernel while exclusively only virtualizing Windows guests on top of vmkernel.

The court went on to share their understanding of the GPLv2 and its underlying copyleft principle, that it is not about abandoning the authors' rights but to the contrary exercising copyright. They understood the license has implications on derivative works and demonstrated that they had been working with both the German translation a well as the English language original text of GPLv2\. At least I was sort-of impressed by the way they grasped it - much better than some of the other courts that I had to deal with in the various cases I was bringing forward during my gpl-violations.org work before.

They also illustrated that they understood that Christoph Hellwig has been developing parts of the Linux kernel, and that modified parts of Linux were now being used in some form in VMware ESXi.

After this general introduction, there was the question of whether or not both parties would still want to settle before going further. The court already expected that this would be very unlikely, as it understood that the dispute serves to resolve fundamental legal question, and there is hardly any compromise in the middle between using or not using the Linux code, or between licensing vmkernel under a GPL compatible license or not. And as expected, there was no indication from either side that they could see an out-of-court settlement of the dispute at this point.

## Discussion of specific Legal Issues (standing)

In terms of the legal arguments brought forward in hundreds of pages of legal briefs being filed between the parties, the court summarized:

- they do not see a problem in the fact that the lawsuit by Christoph Hellwig may be funded or supported by the Software Freedom Conservancy. Christoph is acting on his own behalf, using his own rights.
- they do not see any issues regarding the place of jurisdiction being placed in Hamburg, Germany, as the defendant is providing the disputed software via the Internet, which according to German law permits the plaintiff to choose any court within Germany. The court added, of course, that whatever verdict it may rule, this verdict will be limited to the German jurisdiction.
- In terms of the type of authors' right being claimed by the plaintiff, there was some discussion about paragraph 3 vs. 8 vs. 9 of German UrhG (the German copyright law). In general it is understood that the development method of the Linux kernel is a sequential, incremental development process, and thus it is what we call _Bearbeiterurheberecht_ (loosely translated as _modifying/editing authors right_) that is used by Christoph to make his claim.

## Right to sue / sufficient copyrighted works of the plaintiff

There was quite some debate about the question whether or not the plaintiff has shown that he actually holds a sufficient amount of copyrighted materials.

The question here is not, whether Christoph has sufficient copyrightable contributions on Linux as a whole, but for the matter of this legal case it is relevant which of his copyrighted works end up in the disputed product VMware ESXi.

Due to the nature of the development process where lots of developers make intermittent and incremental changes, it is not as straight-forward to demonstrate this, as one would hope. You cannot simply print an entire C file from the source code and mark large portions as being written by Christoph himself. Rather, lines have been edited again and again, were shifted, re-structured, re-factored. For a non-developer like the judges, it is therefore not obvious to decide on this question.

This situation is used by the VMware defense in claiming that overall, they could only find very few functions that could be attributed to Christoph, and that this may altogether be only 1% of the Linux code they use in VMware ESXi.

The court recognized this as difficult, as in German copyright law there is the concept of _fading_. If the original work by one author has been edited to an extent that it is barely recognizable, his original work has _faded_ and so have his rights. The court did not state whether it believed that this has happened. To the contrary, the indicated that it may very well be that only very few lines of code can actually make a significant impact on the work as a whole. However, it is problematic for them to decide, as they don't understand source code and software development.

So if (after further briefs from both sides and deliberation of the court) this is still an open question, it might very well be the case that the court would request a techncial expert report to clarify this to the court.

## Are vmklinux + vmkernel one program/work or multiple programs/works?

Finally, there was some deliberation about the very key question of whether or not _vmkernel_ and _vmklinux_ were separate programs / works or one program / work in the sense of copyright law. Unfortunately only the very surface of this topic could be touched in the hearing, and the actual technical and legal arguments of both sides could not be heard.

The court clarified that _if_ vmkernel and vmklinux would be considered as one program, then indeed their use outside of the terms of the GPL would be an intrusion into the rights of the plaintiff.

The difficulty is how to actually venture into the legal implications of certain technical software architecture, when the people involved have no technical knowledge on operating system theory, system-level software development and compilers/linkers/toolchains.

A lot is thus left to how good and 'believable' the parties can present their case. It was very clear from the VMware side that they wanted to down-play the role and proportion of vmkernel and its Linux heritage. At times their lawyers made statements like _linux is this small yellow box in the left bottom corner_ (of our diagram). So of course already the diagrams are drawn in a way to twist the facts according to their view on reality.
