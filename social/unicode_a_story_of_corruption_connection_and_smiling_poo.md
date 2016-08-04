# Unicode: A story of corruption, connection, and smiling poo

[Original URL](https://medium.com/@maggieshafer/unicode-a-story-of-corruption-connection-and-smiling-poo-598295e4af9d#.493mbkrfs)

> The Unicode Staff and Editorial Committee Members celebrating the publishing of The Unicode StandardVersion 5.0 in Mountain View, CA in December 2006\. Clearly image resolution was the least of their...

![](https://cdn-images-1.medium.com/max/800/1*XD5IkSJinuaQfpLBh-_BTA.jpeg)

The Unicode Staff and Editorial Committee Members celebrating the publishing of The Unicode Standard<br>
Version 5.0 in Mountain View, CA in December 2006\. Clearly image resolution was the least of their concerns at the time. **THE DANCING RED DRESS LADY'S SECRET REVEALED**

Where do emojis come from?

Most of the world knows they took off in Japan, but who designed them, and how did they get on my iPhone, and what are the broader implications of their ubiquity in text conversation? And what is that triangular-shaped thing with the black square loitering in the foods category? These are the questions I set out to answer when I began working on this article. Then I checked the internet and realized a lot of smarter people had already covered it.

Like [this article](http://www.vox.com/2014/6/17/5814454/where-emoji-come-from) from Vox about where they came from.

Or [this essay](https://youtu.be/VtWabXhR0YU) from from New York Magazine, which explores their evolution and cultural implications.

Or [this one](http://www.wired.com/2015/05/using-emoji-wrong/) from Wired, where you can find out what they mean and how to use them.

These articles answered a lot of my questions, but they also raised a new one. Every one of them mentions something called Unicode. Some of them in passing, as if to say, "We all know about Unicode, right? Let's move on." Some of them explained it briefly, just not in a way that makes sense to my 5th grade understanding of computers. A few of them included a link, which I clicked because that's what you do when you're looking something up real quick and then it's a day. All these links took me to what appeared to be a [derelict website](http://www.unicode.org/) relic of the 90s -- here on out referred to as [the Hidden Temple](https://www.youtube.com/watch?v=KkJgHBp-kMU) -- complete with a left-hand nav bar and absolutely no margins that led me into an endless labyrinth of articles and papers and explanations bleeding to the very edges of my screen and more and more underlined links which I kept clicking until eventually the Home icon disappeared and thank God for the back arrow or I may not be here today to tell you the story of Unicode, and how a small group of linguists and programmers decided to fix the internet before most of us had ever even created an AIM account.

**UNICODE: THE SOLUTION TO CONFLICT AND CORRUPTION**

![](https://cdn-images-1.medium.com/max/800/1*bWGjwxDRQo1_B8MUhJXSTA.png)

One of the many good things about working at bulb is that I am never ever the smartest person in the room. Which is why the first thing I did after coming up for air from the depths of the Hidden Temple was make my way to our software architect [David Anderson's](https://www.linkedin.com/pub/david-anderson/0/744/a17) office that is actually a tiny hallway to ask him to explain a few things.

"What is Unicode?"

He slowly backed away from his 2.4 million open tabs, and with eyes lit up, turned to the glass board behind him and starting drawing what appeared to be a series of rectangles.

Unicode, he explained, is the universal coding standard for script.

It's the reason you can email someone in China and they can read it, or I can text my Dad's Android from iOS and he can yell text me back in all caps because he doesn't know how not to (not Unicode's fault), or that I can build a web page and Google can find that page for someone else, no matter where they are, even someone in space using rocket software. It's a universal character language that all computers speak. The digital tower of Babel.

The Hidden Temple puts it like this: "Fundamentally, computers just deal with numbers. They store letters and other characters by assigning a number for each one. Before Unicode was invented, there were hundreds of different encoding systems for assigning these numbers."

No single one of these systems could contain enough characters to cover all written languages, and some of them needed multiple encoding systems just for a single language with all its letters, punctuation and symbols.

Not only were these systems inadequate on their own, they also conflicted with one another. For instance, two encodings could use different numbers for the letter M, or worse, they could use the same number for two different letters.

Back to The Hidden Temple: "Any given computer (especially servers) needs to support many different encodings; yet whenever data is passed between different encodings or platforms, that data always runs the risk of corruption."

So if you emailed "I love you" to your person but they were using a program with a conflicting encoding system, they might get a message that looks more like "◻◻◻◻◻." Relationship over. Or at very least in the grey area.

Unicode saved your relationship by providing a unique number for every character, no matter what the platform, no matter what the program, no matter what the language. So "I love you" shows up in the exact script in which it was sent.

David has been working as a programmer since he was 14 (more than 25 years), and remembers the transition to Unicode like some of us remember getting our wisdom teeth pulled.

Like most American programmers, he had been primarily relying on ASCII -- American Standard Code for Information Exchange -- a 7-bit (a bit being a 0 or a 1 in binary code) character encoding system in his job at JD Edwards. This worked really well for English, but as soon as you needed to translate something into a non-Latin based language (which was happening more and more frequently as more and more of the world was coming online), you'd get the coding equivalent of all your teeth ending up crooked and your entire face would shapeshift until you were a monster of a person, a shell of your former self that no one would ever take to prom.

Joe Becker, a former Xerox corporation programmer and Unicode founder, explained why full extraction was necessary in [Unicode 88](http://unicode.org/history/unicode88.pdf), a paper he wrote to summarize the work he'd been doing with Lee Collins (a colleague at Xerox), and Mark Davis (then of Apple Corporation) in (wait for it) 1988\. This would become the framework for what we know today as the Unicode Standard.

The problem with ASCII, according to the document, is that the people of the world need to be able to communicate in their own native languages, not just English. Modern text processing systems should be able to accommodate latin-based alphabets for all languages; latin and non-latin based alphabets, "exotic" scripts like Hindi and Thai, not to mention the thousands of ideographic characters used in writing many Asian languages.

![](https://cdn-images-1.medium.com/max/800/1*IWKp-0mr7hGkzR28gtZ0hg.gif)

ASCII vs. Unicode. Unicode wins every time. (Image from Sitepoint.com) What was needed was a new international/multilingual text encoding standard that would be as workable and reliable as ASCII, but that covers all the scripts of the world.

The paper goes on to explain how Unicode would be that standard, by creating a 16-bit number (or room for 65,536 unique codes) and assigning it to a distinct character. And then doing that for every character in every language in the world. It was later that they added for every time period.

Andy Updegrove, a lawyer who represents standard setting organizations like Unicode and maintains an entire site devoted to resources for and about these organizations, is perhaps the most vocally passionate Unicode fan there is. This is the way he put it in [an interview turned blog post](http://www.consortiuminfo.org/standardsblog/article.php?story=20150705201104158): "It lies at the very bedrock of equal access to the Internet, and equal opportunity to benefit from it by being able to influence others. It gives voices to those who would otherwise be voiceless, except in the language of another culture. With it, the world is a level playing field. Without it, we would be stuck in an upgraded example of a colonial world, where historically first world nations continue to force their cultures and rules on emerging nations and their peoples."

The transition to Unicode was thus noble as well as deeply practical, though no one was looking forward to the procedure. Once it was done though, it was well worth it. Most notably, the copious time that developers like David could save from having to deal with translation issues could be repurposed for other things, like building new, better software and emailing friends without worrying about those mysterious white squares. Multiply this time saved by every programmer in every country in the world and we owe the creators of Unicode a big pat on the back. And now that there is a nonprofit Unicode Consortium, you can give them that pat in the form of a [financial donation](http://unicode.org/consortium/donations.html).

But before you do that, you might want to know a little bit more about them.

**THE PEOPLE WHO SPEND THEIR FREE TIME SAVING LANGUAGES AND GETTING CREDIT FOR TACOS**

Ken Whistler is a smart guy. He received a Ph.D. in linguistics from UC Berkeley in 1980 and in his work as a linguist, he learned Japanese and Chinese and studied Native American languages. This all involved a lot of primary data entry, and it was good timing for data entry because the PC was fresh on the scene. The only problem was that he worked with languages that didn't rely on latin characters, and his computer was like WTF?! So he did what all smart guys do and created his own ad hoc system for encoding the foreign characters. It took a lot of time, but it worked. And as long as you were using the same software on the same computer, it'd work for you, too.

And then the internet happened. I mean it'd been around since the late 60s, sure, but it began to look like something that would be useful to more than the military, the government and diehard Thunderbirds fans. Ken realized it would become increasingly more important that there was a universal system for linguists doing work like he was -- at least if we wanted that work to survive in the digital age.

Turns out, Ken wasn't the only smart guy thinking about this. He was hired in 1989 by a company called Metaphor, who also wanted him to work on developing an encoding system for characters. His boss at Metaphor, Mike Kernaghan, introduced him to the man we quoted earlier, Joe Becker, who had already been at work on Unicode with Davis and Collins.

Ken had found a home with these fellow language-loving programmers. And they did what smart guys do when they care about something important. They founded a consortium. Which was another thing I had to look up. This is what the people came up with who collectively wrote the "Consortium" Wikipedia page:

_A consortium is an_ [_association_](https://en.wikipedia.org/wiki/Voluntary_association) _of two or more_ [_individuals_](https://en.wikipedia.org/wiki/Individuals)_,_ [_companies_](https://en.wikipedia.org/wiki/Companies)_,_ [_organizations_](https://en.wikipedia.org/wiki/Organizations) _or_ [_governments_](https://en.wikipedia.org/wiki/Government) _(or any combination of these entities) with the objective of participating in a common activity or pooling their resources for achieving a common goal._

Today, the Unicode Consortium is a non-profit corporation managed by a group of directors ([Mark Davis](https://twitter.com/mark_e_davis) is still president. He also works at Google.) and funded by its various members and donors including major computer corporations, software producers, database vendors, government ministries, research institutions, international agencies, various user groups, and interested individuals. Apple and Microsoft are both voting members, along with the Indian Government and UC Berkeley. Their common goal is to "develop, extend and promote use of the Unicode Standard, which specifies the representation of text in modern software products and standards."

The entire organization functions out of a one-room office at the Microsoft campus in Mountain View, and only the office manager works there and even then, only part time. Most of the technical work is volunteer, distributed and done online, with most big decisions being made at the [Unicode Technical Committee's](http://unicode.org/consortium/utc.html) quarterly in-person meetings. Fun fact: There are people in the world who choose to attend these with their free time.

All but three positions within Unicode are volunteer, which is why we can't fault them for the Hidden Temple. They've been busy doing more important things than designing coherent and navigable websites.

In the last 25ish years, the Unicode consortium had encoded more than 120,000 characters and 129 scripts (which support a lot more than 129 writing systems), including languages that have long been dead, like Hittite hieroglyphs from thousands of years ago. Not only does it ensure that these languages will survive in a digital future, they've basically digitally archived the entire history of writing in an interchangeable way.

"It's one of those jobs -- and a very large one at that -- that is utterly essential and invariably taken for granted," said Updegrove.

Add to that "misunderstood." Case in point: the taco emoji is what finally got them in the [news](http://time.com/3924710/taco-emoji-android-iphone/).

To celebrate the 20 year anniversary of Joe Becker's publishing of Unicode 88, the consortium had a poetry contest at one of its conferences in September 2008\. This is Craig W. Cornelius, a internationalization software engineer for Google, performing his original Unicode Was Made for You and Me. Thank you, Craig, thank you. **THE SECTION YOU SKIP TO BECAUSE YOU WANT TO KNOW WHEN THE "CALL ME" HAND WILL BE RELEASED**

Now that all the major written scripts are encoded, The Unicode Consortium chooses which new projects to tackle from submitted proposals by interested parties.

The interested parties, in the case of emoji, were major software companies including Apple, Google and Microsoft, who basically wanted to sell more phones in Japan. Japanese phone carriers had long been offering emojis, and they had become wildly popular, though they didn't work between carriers because they were all uniquely encoded. So you might send girl flipping hair to your friend on a different carrier and she'd receive girl crossing hands (or more likely a white box) and the result in either case was communication breakdown. Apple and Microsoft and Google wanted emojis because they wanted more Japanese Yen, but they didn't want the breakdown part.

They needed Unicode. And as paying, voting members of the consortium, they got it.

The original Unicode-encoded emojis came out in the Unicode 6.0 release in 2010, but most of the public didn't realize this until the time Unicode 7.0 came out in 2014\. That's when iOS and Android were coming online with the the 6.0 emojis, making the dancing lady in the red dress available to all and bringing on the press attention.

When Unicode releases new emojis, it includes the code point, a name for the emoji and a [sample image or glyph](http://www.unicode.org/emoji/charts/full-emoji-list.html)of what it should look like. The Consortium does not design any of these images itself, but receives them for use from vendors or interested parties. Unicode emoji designs are noticeably lower in design quality than those on your phone, but unlike The Hidden Temple, they are that way on purpose.

Unicode should not be confused with font and/or emoji designers. According to Ken (who prefers emoticons to emojis any day), the example emojis Unicode includes in its releases are purely for identification, and they aim to "encourage all the software developers to go off and make good looking fonts that they can sell to people." So you can thank Apple for that knowing smirk on the pile of poo.

![](https://cdn-images-1.medium.com/max/800/1*wCqCj4z0yuFP56N-5Vvhyg.png)

A screenshot of the emoji data chart from the Hidden Temple. Andr: Why are your emojis so freaky looking? There is something that the inclusion of emoji has forced onto The Unicode Consortium that they're not necessarily cut out for. Encoding written language was always part of their plan. Creating one wasn't.

Let me explain. Unlike most all of the scripts Unicode has encoded, the emoji characters are being constantly updated and added to, as it continues to evolve and grow in usage. This leaves the decision of what proposed characters to encode and when to the Consortium, a decision I sense they are somewhat uncomfortable with.

To help them sift through the emoji character proposals they receive, they've come up with a set of [selection factors](http://unicode.org/reports/tr51/#Selection_Factors) that includes compatibility, usage levels and distinctiveness. In this process, some characters are automatically ruled out: brands, specific people or deities, and ideas that can be represented by existing characters (i.e. man turned sideways + gust of air + monkey face with hands over ears = no need for a fart emoji).

Research has been done in recent years into how emojis are used and to what effect, but the truth is, it's all relatively new, like emojis themselves. We don't know exactly the impact they are having on our lives or communications, though many have speculated: they improve communication, they help us have more sex, they allow us to not have the words but still have the thought. But if it's true that language can itself precede the concept it aims to explain, then the creators of letters, the building blocks of words, or pictures, have a lot more power than perhaps we are giving them credit for (consider the criticism they received over the lack of racially diverse or the same-sex couple emojis). In this sense, the Unicode Consortium is acting as both the preserver _and creator_ of culture and society. Which is not something spelled out in the membership agreement.

Next year there is another large set of emojis coming -- you can get a tentative list [here.](http://www.unicode.org/alloc/Pipeline.html) It includes the "call me" hand, so ya'll can rest easy.

But the biggest addition to Unicode will be Tangut, a historical writing system of China and another large collection of Chinese characters. They'll also be adding a few minority script additions, including Adlam from West Africa, Osage -- a U.S. Native American language -- and Newa of Nepal, the encoding of which has helped to legitimize a population's native tongue and revealing a still fraught relationship between a government and one of its minority groups.

"But the emoji will get all the press ;)" Ken wrote.

**THE SECTION THAT PROVES EMOJI WILL NOT GET ALL THE PRESS**

Newar is a language spoken by more than a million people, most of whom live in Nepal, in the Kathmandu Valley and surrounding regions. It was the country's administrative language from the 14th through the 18th centuries, used for royal decrees, chronicles, religious manuscripts, legal papers, official records and creative writing.

![](https://cdn-images-1.medium.com/max/800/1*KYGalIBE2W_IDUv05FFS3Q.png)

A sample of the Newa script from Lipi Pau magazine. However, beginning in the late 18th century with the Gorkha conquest of Nepal, Newar has suffered from suppression, both officially and unofficially. From 1846–1951, the Rana Dynasty did everything it could to wipe out Newar entirely, declaring legal documents written in Newar unenforceable, forbidding literature in the language and jailing its writers, even going so far as to expel monks who were caught using it.

The history is, like most things, complicated, and like most complicated things, I am not qualified to expound on it with any sort of authority. But what I can say with surety is that Newar has a troubled past, and if it wasn't for Unicode, it's future wouldn't look much better.

The endangered language was first proposed for encoding in 1999 by a group in Nepal, _**_ but did not progress in the encoding process until 2011\. Making the decision to encode it turned out to be the easiest part.

There are people invested in this language, both proponents and suppressors, and where people are invested, there is usually conflict. Especially when one of those people is a government.

As the technical director and managing editor for the consortium, Ken must navigate a technical compromise for the encoding of the writing system while stepping on as few toes as possible. This has been no easy task. Even deciding on what to call the script was an issue, as Newari is a Nepali word, not a Newari word. Newa was what they landed on as a compromise. You can read Ken's technical summary of the emotional issue [here.](http://www.unicode.org/L2/L2014/14285r3-newa.pdf)

Hidden within the technical language are compromises and appeals aimed at keeping all parties on board with the encoding and the consortium apolitical.

The tenuous nature of this encoding project is not limited to Newa. Significant barriers stand in the way of the encoding of nearly all minority writing systems, not least of which is financial. But for Deborah Anderson, a Unicode technical director, member of the editorial committee and president of the Script Encoding Initiative, it's a difficult but crucial task.

"Basically, I see my role as representing the 'little guy.' Since many experts in historic scripts and modern lesser-used scripts can't come to meetings or study up on how to write a successful proposal, I offer to help them write a proposal (or can sometimes help find a veteran proposal author to write a proposal)," Debbie said in an email interview.

She then guides eligible proposals through the encoding process, which can take more than two years.

![](https://cdn-images-1.medium.com/max/800/1*5mTSmEuEvP5M6_2nqLXshQ.jpeg)

A Unicode Technical Committee meeting in 2009\. That's Debbie on the right, and Ken is leaning forward in white. Mark Davis is in the back with the killer sweater. Can't get enough of these photos? I get that. The good news is there are plenty more. Search the Unicode website for the photo album and eat your heart out. The [SEI website](http://linguistics.berkeley.edu/~dwanders/) (which may have been put together by the same person as The Hidden Temple) explains that the encoding of a minority language in Unicode promotes native-language education, universal literacy, cultural preservation, and removes the linguistic barriers to participation in the most powerful tool the world has ever known.

"Unicode it is what allows the richness of human history, culture, identity and knowledge to remain accessible as the fixed media of the past crumble into dust or (worse) are shredded into pulp as we race, pell mell into the digital future," said Updegrove.

But it's not all for the native speakers.

"On a more global scale, I think that by not having access to these scripts we (=humankind) are missing out on the literary, cultural, and historical contributions of such communities, which helps to define our world," said Debbie.

SEI has identified [more than 100 scripts](http://www.linguistics.berkeley.edu/sei/list.html) that have yet to be encoded.

**THIS IS THE SECTION I WOULD TITLE WITH THE HAND WAVING GOODBYE IF MEDIUM SUPPORTED EMOJIS IN TITLES**

The contributors to Unicode number in the thousands, from font designers to programmers to linguists to historians -- a collective effort moved forward by the collectiveness of the problem.

"What drove unicode was that everyone was in agreement that the the current system was unsustainable, broken and expensive. If it wasn't Unicode, it would have been a different solution. A change was inevitable," Ken said.

Which got me thinking about other problems that affect us all, the ones that everyone will deal with but [aren't any single person's or people's responsibility](http://www.investopedia.com/terms/t/tragedy-of-the-commons.asp). We're in the midst of [a pretty major one right now](http://waitbutwhy.com/2015/06/how-tesla-will-change-your-life.html). I think we have a lot to learn from Unicode and the Consortium when it comes to solving those collective problems.

Ken, the linguist turned programmer turned technical writer who was encoding ancient languages before Unicode even existed, is one of those initiative-takers, even when he won't benefit directly from the solutions. For instance, the forthcoming selfie emoji.

"I never really use emojis. I'm pretty old fashioned in that way," he said.

_To learn more about the Consortium, submit a proposal, become a member or donate, you'll have to visit_ [The Hidden Temple](http://www.unicode.org/) _yourself. Godspeed._
