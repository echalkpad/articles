# A Tribute to VMware Workstation, Fusion, and Hosted UI

[Original URL](http://blog.chipx86.com/2016/01/26/a-tribute-to-vmware-workstation-fusion-and-hosted-ui/)

> Yesterday morning, the Hosted UI team, responsible for VMware's Workstation and Fusion products, woke up to find themselves out of a job. These products, despite being award-winning and...

Yesterday morning, the Hosted UI team, responsible for VMware's Workstation and Fusion products, woke up to find themselves out of a job. These products, despite being award-winning and profitable, are probably not long for this world.

I was not directly affected, in this way at least, as I had already [left VMware in 2013](http://blog.chipx86.com/2013/07/30/a-new-adventure-begins/) to work on [Review Board](https://www.reviewboard.org) full-time. However, many of my closest friends were, and a product I spent 9 years of my life on may have seen its last feature.

I could talk all day about how I think we got here, losing this amazing team and these fantastic products. I could point fingers and lash out at those I blame. I could talk about how _furious_ this all makes me.

Instead, I'm going to talk about the team and what we built -- and I don't just mean our products.

## Let me tell you about our team

[![Hosted UI](https://chipx86.files.wordpress.com/2016/01/hosted-ui-banana.jpg?w=300&h=384)](https://chipx86.files.wordpress.com/2016/01/hosted-ui-banana.jpg)

I began working in Hosted UI on August 23rd, 2004, as a bright-eyed 20 year old freshly dropped out of college. Back then, it was a small team full of amazingly bright and passionate people, working days and nights to build a product they believed in.

The culture at that time within VMware was just so fun and energizing. People _wanted_ to be there, and were proud of their work. Features were brainstormed over games of foosball or DDR, designs discussed over free lunches and beer bashes. In the evenings, we'd order dinner in and watch Simpsons, or whatever was on.

Company culture changed over the years, becoming more corporate and stiff, but not Hosted UI. We'd work all day, with the occasional interruption for YouTube videos or some laughs, and at night we went out and had some more. Poker nights, movie nights, video game nights. Dinners out together, sometimes several times a week.

Many people came and went over those years. The team changed, though, for a software company, a surprising number remained until the very end. Even those that left kept in touch, joining for poker nights or dinners here or there, coming to the dunkings (if you were getting married, you were going in the pond), birthday celebrations, and reunions. We formed alumni lists and kept in touch. We hung out on IRC outside of work.

[![Poker Night](https://chipx86.files.wordpress.com/2016/01/poker.jpg?w=630&h=413)](https://chipx86.files.wordpress.com/2016/01/poker.jpg)

Through deadlines and downtimes, stresses and celebrations, our team worked and played hard. We were dedicated, passionate, and if you'll allow me, we were _damn good_ at what we did.

I left this team two years ago, but it hasn't really felt that way. I still saw them almost every week. Our team didn't have to be in the same building or even the same company to stay a team.

Hosted UI may no longer exist at VMware, but that's really VMware's loss. They lost one of the most dedicated teams they could ever hope for, the kind of team you can't just hire again.

## We built some amazing products

### Workstation

![Workstation](https://chipx86.files.wordpress.com/2016/01/workstation-logo.png?w=630)VMware Workstation was the first VMware product (back then, it was simply known as "VMware."). At a time when dot-coms dominated the Super Bowl and Amazon was all about books, VMware Workstation was letting pioneers in the Linux world virtualize their Windows desktop so they could run Microsoft Office instead of StarOffice.

This product evolved over the years with over 15 major releases, and more features than I can count, running on every flavor of Linux and Windows. It did this without falling prey to the bloat of most long-running products, as we focused not only on making it a more powerful product but also a more usable product.

Workstation made it easy to run complex development and testing scenarios, creating and working with several virtual environments all at once across any number of host computers. It integrated your virtual desktops with your host desktop. It let you take snapshots at different moments in the lifetime of your VM, and jump between them at will. It helped you catch defects in your software through remote debugging and CPU/memory record/replay capabilities, it helped you test complex network setups with virtual LAN devices, and it worked as a powerful front-end for VMware's Server, ESXi, and vSphere products. And, in the end, it also helped you simply run your Windows programs on Linux, your Linux programs on Windows, or whatever you wanted.

[![Workstation](https://chipx86.files.wordpress.com/2016/01/workstation.png?w=630&h=465)](https://chipx86.files.wordpress.com/2016/01/workstation.png)

Internally at VMware, Workstation was also seen as an indispensable product, helping other teams test features and devices that would eventually become selling points on the more high-end vSphere product releases. With Workstation's ease-of-install and ease-of-use, people could get set up in minutes and get right to work.

We _loved_ our product. This was our baby. We took input from marketing, management, sales, customers, and so on, but in the end, we were given a lot of creative liberty over the features and design. We were also given time to address technical debt, helping to get our codebase in shape for future challenges.

[![Workstation with Unity](https://chipx86.files.wordpress.com/2016/01/workstation-unity-2.png?w=630&h=394)](https://chipx86.files.wordpress.com/2016/01/workstation-unity-2.png)

I don't know how many awards we received, but I think it was a lot. I do know that we had so many users who loved the product we poured our souls into. That meant a lot, and kept us motivated.

It was, let's say, a challenge getting some parts of the company to really care about the product. Workstation made a lot of money, but not the hundreds of millions the company would have preferred. This, I believe, ultimately led to yesterday's sad outcome... Still, I'm very proud of what we built.

### Fusion

![Fusion](https://chipx86.files.wordpress.com/2016/01/vmware_fusion_logo.png?w=630)

Workstation was a power user product built for Linux and Windows. In 2007, its sister product, Fusion for Mac, was released. This focused more on consumer usage, helping people run Office and other Windows apps on their Mac.

At the time, Apple had just moved to Intel processors, and were touting the ability to dual-boot between Windows and MacOS X, using a feature called Bootcamp. Fusion offered a better way by letting you run Windows and MacOS X at the same time. It was popular amongst students who needed to run Windows software for class on their shiny new MacBooks. It was popular amongst developers who needed to run or test Windows or Linux environments while on the go.

[![Fusion](https://chipx86.files.wordpress.com/2016/01/fusion.png?w=630&h=473)](https://chipx86.files.wordpress.com/2016/01/fusion.png)

Fusion was a very different product in some ways than Workstation, but it was also very closely related. While it didn't focus on many of the power user features that Workstation offered, it did take many of those features and reimagine them for more casual users. It also shared much of the core code that Workstation used, meaning that features could more easily be ported across and bugs fixed just once.

Fusion was a reimagining of what Workstation could have been, built for a different time and a different audience. Like Workstation, it was also built by a group of very loyal, dedicated, brilliant people, the Fusion segment of Hosted UI.

While I never worked directly on Fusion, I did get to see features I built for Workstation make their way there, and watched as our users got to try them for the first time on the Mac. It wasn't the product I devoted my time to, but it was one I loved, and one I still use today.

## And all the others

Our small team has built quite a lot over the years. Along with Workstation and Fusion, we've also built:

- [Player](https://www.vmware.com/products/player): A slimmed-down product for simply running and interacting with VMs, without all the UI of Workstation
- [VMRC](https://www.vmware.com/support/developer/vmrc/): A browser plugin and SDK for embedding virtual machines in your browser or other applications
- [Server](https://en.wikipedia.org/wiki/VMware_Server): A free product built from Workstation that offered remote VM hosting and management)
- [WSX](http://blog.chipx86.com/2012/03/13/wsx-virtual-machines-in-your-browser/): A web-based service for running VMs natively in your browser from anywhere
- [AppCatalyst](https://blogs.vmware.com/cloudnative/vmware-appcatalyst/): A developer-focused, API-driven development and testing service that works with Docker

I'm pretty sure there's more, but those are the highlights.

These, along with Workstation and Fusion, were built by a team typically no larger than about 20 people (at any given point in time).

We did good.

## Time for the next adventure

VMware lost a lot of amazing people, and will be feeling that for some time to come, once they realize what they've done. It's a shame. As for our team, well, I think everyone will do just fine. Some of the best companies in the Silicon Valley are full of ex-VMware members, many former Hosted UI, who would probably welcome the chance to work with their teammates again.

Workstation, Fusion, and our other products may survive in maintenance mode, or they may disappear. It's hard to say at this point what will happen. What I can say is that no matter what happens to them, they had an amazing run, and are something every one of us can be proud of the rest of our careers.

And we can be proud of the team, the friendships, and the strong bonds we built, now and through our next adventures.

### Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

### _Related_
