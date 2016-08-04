# The Father of Online Anonymity Has a Plan to End the Crypto War

[Original URL](http://www.wired.com/2016/01/david-chaum-father-of-online-anonymity-plan-to-end-the-crypto-wars/)

> It's been more than 30 years since David Chaum launched the ideas that would serve as much of the groundwork for anonymity online. In doing so, he also helped spark the debate that's...

It's been more than 30 years since David Chaum launched the ideas that would serve as much of the groundwork for anonymity online. In doing so, he also helped spark the debate that's endured ever since, over the anarchic freedoms that digital secrecy enables--the conflict between privacy advocates and governments known today as the "crypto wars."

Now Chaum has returned with his first online privacy invention in more than a decade. And with it, he wants to bring those crypto wars to an end.

At the Real World Crypto conference at Stanford University today, Chaum plans to present for the first time a new encryption scheme he calls PrivaTegrity. Like other tools Chaum has spent his long career developing, PrivaTegrity is designed to allow fully secret, anonymous communications that no eavesdropper can crack, whether a hacker or an intelligence agency. But PrivaTegrity, which Chaum's been developing as a side project for the last two years along with a team of academic partners at Purdue, Radboud University in the Netherlands, Birmingham University and other schools, is meant to be both more secure than existing online anonymity systems like Tor or I2P and also more efficient; he claims it will be fast enough to work as a smartphone app with no perceptible delay. Chaum wouldn't comment on whether the project, which has yet to be fully coded and tested, would be commercialized or run as a non-profit, but he says an alpha version for Android is in development that functions as an instant-messaging app. In future versions, Chaum and his collaborators plan to add features like larger file sharing for photos and video, the ability to follow Twitter-like feeds, and even financial transactions, all under the cover of strong anonymity with untraceable pseudonyms. "It's a way to create a separate online reality," says Chaum, "One in which all the various things we now know people like to do online can be done in a lightweight manner under a completely different and new and very attractive privacy and security model."

That ambitious privacy toolset aside, Chaum is also building into PrivaTegrity another feature that's sure to be far more controversial: a carefully controlled backdoor that allows anyone doing something "generally recognized as evil" to have their anonymity and privacy stripped altogether.

Anyone using PrivaTegrity for something "generally recognized as evil" can have their anonymity and privacy stripped altogether.

Whoever controls that backdoor within PrivaTegrity would have the power to decide who counts as "evil"--too much power, Chaum recognizes, for any single company or government. So he's given the task to a sort of council system. When PrivaTegrity's setup is complete, nine server administrators in nine different countries would all need to cooperate to trace criminals within the network and decrypt their communications. The result, Chaum argues, is a new approach that "breaks the crypto wars," satisfying both the law enforcement agencies who argue that encryption offers a haven for criminals, and also those who argue that it's necessary to hobble mass spying.

"If you want a way to solve this apparent logjam, here it is," says Chaum. "We don't have to give up on privacy. We don't have to allow terrorists and drug dealers to use it. We can have a civil society electronically without the possibility of covert mass surveillance."

## Inventing Anonymity

Chaum's quest for a shield against Internet surveillance began before most of the world was even aware of the Internet at all. His inventions include the first-ever cryptocurrency, a 1990s venture known as DigiCash, and DC Nets, a scheme he invented in the early '80s to allow theoretically perfect anonymity within a group of computers. But perhaps the most influential of Chaum's privacy ideas was an earlier, simpler scheme he called a "mix network," a term he coined in 1979.

Mix networks anonymize messages by encrypting them in layers and routing them through a series of computers that serve as intermediaries. Each of those middlemen machines collects messages in batches, shuffles them, strips off one layer of their encryption that only that computer can decrypt, and then passes them on to the next computer in the chain. The result is that no one, not even the individual intermediary computers themselves, can trace the messages from origin to destination. Today, anonymity tools inspired by mix networks are used by everyone from the nearly 2 million inhabitants of the Tor anonymity network--whose messages are routed through a sort of mutated mix network of thousands of volunteer machines--to Bitcoin spenders hiding drug transactions on the Dark Web.

With PrivaTegrity, Chaum is introducing a new kind of mix network he calls cMix, designed to be far more efficient than the layered encryption scheme he created decades ago. In his cMix setup, a smartphone communicates with PrivaTegrity's nine servers when the app is installed to establish a series of keys that it shares with each server. When the phone sends a message, it encrypts the message's data by multiplying it by that series of unique keys. Then the message is passed around all nine servers, with each one dividing out its secret key and multiplying the data with a random number. On a second pass through the nine servers, the message is put into a batch with other messages, and each server shuffles the batch's order using a randomized pattern only that server knows, then multiplies the messages with another random number. Finally, the process is reversed, and as the message passes through the servers one last time, all of those random numbers are divided out and replaced with keys unique to the message's intended recipient, who can then decrypt and read it.

You have to perfect the traceability of the evil people and the untraceability of the honest people. 

<span class="attribution">David Chaum</span>

Chaum argues that PrivaTegrity's setup is more secure than Tor, for instance, which passes messages through three volunteer computers which may or may not be trusted. Unlike PrivaTegrity, Tor also doesn't deliver its messages in batches, a decision designed to allow fast Web browsing. But that tradeoff means a spy who watches both ends of Tor's network of intermediary computers might be able to identify the same message going in one at one place and coming out at another, a problem PrivaTegrity batch system is designed to solve.

PrivaTegrity's protocol will be speedier than past attempts at implementing mix networks, Chaum claims. That supposed efficiency comes from the fact that the collections of random numbers it uses, both before and after the messages are shuffled, can be precomputed and passed between the servers during moments when the servers are idle, instead of being created in real-time and slowing down conversations. And because the entire cMix process is a series of simple multiplications and divisions, it's far faster than the public key computations necessary in older mix networks, says Aggelos Kiayas, a computer science professor at the University of Connecticut who's reviewed Chaum's system. "It is well known that mix nets can be better than Tor in terms of privacy...The real question is latency," Kiayas writes in an email, cautioning that he can't fully judge the scheme's efficiency without seeing the final app. "PrivaTegrity appears to be a decisive step forward in this direction."

## A Backdoor Security Council

On top of those security and efficiency tricks, PrivaTegrity's nine-server architecture--with a tenth that works as a kind of "manager" without access to any secret keys--also makes possible its unique backdoor decryption feature. No single server, or even eight of the nine servers working together, can trace or decrypt a message. But when all nine cooperate, they can combine their data to reconstruct a message's entire path and divide out the random numbers they used to encrypt it. "It's like a backdoor with nine different padlocks on it," Chaum says.

For now, Chaum admits the prototype of PrivaTegrity that he plans to distribute to alpha testers will have all its servers running in Amazon's cloud, leaving them open to the usual threats of American government surveillance, from subpoenas to National Security Letters. But in the app's final version, Chaum says he plans to move all but one of those servers abroad, so that they're spread out to nine different countries, and require each server to publish its law enforcement cooperation policy. Chaum won't yet detail his suggested privacy policies for those servers, but suggests that decryption and tracing could be reserved for "serious abuse, something that leads to death and real harm to people or major economic malfeasance." Or perhaps the system could limit the frequency of covert traces to some number, such as 100 decryptions per year. Or, Chaum suggests, the privacy policy could be written to prevent the servers from performing any voluntary tracing or decryption, though they'd still be subject to legal threats from governments. Chaum argues that he doesn't himself advocate any one of those approaches. "It's one thing to advocate an opinion, and another thing to create something and say 'this could be the solution,'" Chaum says.

<sup>3</sup>

Chaum has yet to reveal the full list of the countries where PrivaTegrity would place its servers. But he suggests they'll be in the jurisdiction of democratic governments, and names Switzerland, Canada and Iceland as examples. "It's like the UN," says Chaum. "I don't think a single jurisdiction should be able to covertly surveil the planet...In this system, there's an agreement on the rules, and then we can enforce them."

[![_MG_7805](http://www.wired.com/wp-content/uploads/2016/01/MG_7805-731x1024.jpg)](http://www.wired.com/wp-content/uploads/2016/01/MG_7805.jpg) 

<span class="credit link-underline-sm"> Julian Berman for WIRED</span>

 The mere mention of a "backdoor"--no matter how many padlocks, checks, and balances restrict it--is enough to send shivers down the spines of most of the crypto community. But Chaum's approach represents a bold attempt to end the stalemate between staunch privacy advocates and officials like [FBI director James Comey](https://www.washingtonpost.com/business/technology/2014/09/25/68c4e08e-4344-11e4-9a15-137aa0153527_story.html), [CIA deputy director Michael Morrell](http://www.wired.com/2015/11/paris-attacks-cia-director-john-brennan-what-he-gets-wrong-about-encryption-backdoors/) and [British Prime Minister David Cameron](http://www.wired.co.uk/news/archive/2015-07/15/cameron-ban-encryption-u-turn) who have all opposed tech companies' use of strong, end-to-end encryption. Comey, Cameron, and Morell have lashed out at firms like Apple and Whatsapp, for instance, for using systems in which even the company itself doesn't possess the key to decrypt communications or stored data, and thus can't cooperate with law enforcement. (Those same privacy features have earned the companies praise from privacy groups.) The debate between encryption fans and surveillance hawks has only intensified in the wake of ISIS's attacks in Paris, and in last month's Democratic presidential debate Hillary Clinton [called for a "Manhattan-like Project"](http://arstechnica.com/tech-policy/2015/12/hillary-clinton-wants-manhattan-like-project-to-break-encryption/) to develop a system that "would bring the government and the tech communities together."

<sup>2</sup>

Most encryption experts insist, however, that any backdoor would lead to abuse by hackers, if not by the very law enforcement or national security agencies it was created for. Chaum counters that spreading the keys to decrypt communications among nine servers would solve both of those problems, preventing abusive government surveillance and making his backdoor far harder to hack. He suggests that the servers' administrator will eventually develop their own security protections and even distinct code to implement PrivaTegrity's protocol, avoiding any single bug that could be common to all nine nodes. "These systems would be far more hardened than even corporate systems, and to abuse the backdoor you'd have to break all of them," he says.

Whether PrivaTegrity lives up to its efficiency and security promises will only become clear when the finished app is released, and Chaum himself, despite spending two years perfecting its crypto system, hasn't even tried the final demo of the app's private alpha. He remains cagey about naming a date for releasing the public beta and publishing its code so that it can be scoured for flaws, but he says there's "no technical reason why it couldn't be ready for the first quarter of 2016."

If PrivaTegrity's reality matches Chaum's descriptions of its potential, he hopes it could serve as a model for how other encryption systems can protect innocent people from spying without offering impunity to criminals. "You have to perfect the traceability of the evil people and the untraceability of the honest people," says Chaum. "That's how you break the apparent tradeoff, this standoff called the encryption wars."

For more technical information on the cMix idea that PrivaTegrity will use, here's Chaum's and his co-authors' still-unpublished paper on the system:

<sup>1</sup>

[cMix: Anonymization by High-Performance Scalable Mixing](https://www.scribd.com/doc/294737065/cMix-Anonymization-by-High-Performance-Scalable-Mixing "View cMix: Anonymization by High-Performance Scalable Mixing on Scribd")

<sup>1</sup>

_Updated 1/6/2016 9:30am EST to embed the technical paper describing Privategrity's cMix system._

<sup>2</sup>

_Correction 1/6/2016 3:30pm EST: An earlier version of the story misspelled the name of FBI director James Comey._

<sup>3</sup>

_Updated 1/7/2016 12:30 EST to clarify Chaum's point that the privacy policy for PrivaTegrity's servers could also be written to allow no voluntary decryption or tracing, as well as his claim to not be advocating any particular privacy policy._

[Go Back to Top. Skip To: Start of Article.](http://www.wired.com/2016/01/david-chaum-father-of-online-anonymity-plan-to-end-the-crypto-wars/#start-of-content)
