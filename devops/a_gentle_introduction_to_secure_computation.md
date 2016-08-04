# A Gentle Introduction to Secure Computation

[Original URL](http://www.alexirpan.com/2016/02/11/secure-computation.html)

> In Fall 2015, I took CS 276, a graduate level introduction to theoretical cryptography. For the final project, I gave a short presentation on secure computation. This blog post is adapted and...

This blog post is adapted and expanded from my presentation notes. The formal proofs of security rely on a lot of background knowledge, but the core ideas are very clean, and I believe anyone interested in theoretical computer science can understand them.

## What is Secure Computation?

In secure computation, each party has some private data. They would like to learn the answer to a question based off everyone's data. In voting, this is the majority opinion. In an auction, this is the winning bid. However, they also want to maintain as much privacy as possible. Voters want to keep their votes secret, and bidders don't want to publicize how much they're willing to pay.

More formally, there are parties, the th party has input , and everyone wants to know for some function . The guiding question of secure computation is this: **when can we securely compute functions on private inputs without leaking information about those inputs?**

This post will focus on the two party case, showing that every function is securely computable with the right computational assumptions.

## Problem Setting

Following crypto tradition, Alice and Bob are two parties. Alice has private information , and Bob has private information . Function is a public function that both Alice and Bob want to know, but neither Alice nor Bob wants the other party to learn their input.

![problem setup](http://www.alexirpan.com/public/secure-comp/setup.png)

The classical example is Yao's Millionaire Problem. Alice and Bob want to know who has more money. It's socially unacceptable to brag about your wealth, so neither wants to say how much money they have. Let be Alice's wealth, and be Bob's wealth. Securely computing

lets Alice and Bob learn who is richer.

Before going any further, I should explain what security guarantees we're aiming for. We're going to make something secure against _semi-honest_ adversaries, also known as _honest-but-curious_. In the semi-honest model, Alice and Bob never lie, following the specified protocol exactly. However, after the protocol, Alice and Bob will analyze the messages received to try to extract information about the other person's input.

Assuming neither party lies is naive and doesn't give a lot of security in real life. However, it's still difficult to build a protocol that leaks no unnecessary information, even in this easier setting. Often, semi-honest protocols form a stepping stone towards the malicious case, where parties can lie. Explaining protocols that work for malicious adversaries is outside the scope of this post.

To prove a protocol is secure, we first need to define what security means. What makes secure computation hard is that Alice and Bob cannot trust each other. Every message can be analyzed for information later, so they have to be very careful in what they choose to send to one another.

Let's make the problem easier. Suppose there was a trusted third party named Faith. With Faith, Alice and Bob could compute without directly communicating.

- Alice sends to Faith
- Bob sends to Faith
- Faith computes and sends the result back to Alice and Bob

![ideal world](http://www.alexirpan.com/public/secure-comp/ideal.png)

This is called the _ideal world_, because it represents the best case scenario. All communication goes through Faith, who never reveals the input she received. She only replies with what the parties want to know, .

If this is the best case scenario, we should define security by how close we get to that best case. This gives the following.

**A protocol between Alice and Bob is secure if it is as secure as the ideal world protocol between Alice, Bob, and Faith.**

To make this more precise, we need to analyze the ideal world's security.

Suppose we securely computed in the ideal world. At the end of communication, Alice knows and , while Bob knows and . If Alice computes

she learns Bob's input . Similarly, if Bob computes

he learns Alice's input . **Even the ideal world can leak information.** In an extreme case like this, possibly even the entire input! If Alice and Bob want to know , they have to give up any hope of privacy.

This is an extreme example, but most functions leak something about each party. Going back to the millionaire's problem, suppose Alice has dollars and Bob has dollars. They securely compute who has the most money, and learn Alice is richer. At this point,

- Alice knows Bob has less than dollars because she's richer.
- Bob knows Alice has more than dollars because he's poorer.

However, there is still some privacy: neither knows the exact wealth of the other.

Since must be given to Alice and Bob, the best we can do is learn nothing more than what was learnable from . This gives the final definition.

<sup>
  <a href="http://www.alexirpan.com/2016/02/11/secure-computation.html#fn:security-defn" class="footnote">1</a>
</sup>

**A protocol between Alice and Bob is secure if Alice learns only information computable from and , and Bob learns only information computable from and**

(Side note: if you've seen zero-knowledge proofs, you can alternatively think of a protocol as secure if it is simulatable from for Alice and for Bob. In fact, this is how you would formally prove security. If you haven't seen zero-knowledge proofs, don't worry, they won't show up in later sections, and we'll argue security informally.)

Armed with a security definition, we can start constructing the protocol. Before doing so, we'll need a few cryptographic assumptions.

## Cryptographic Primitives

All of theoretical cryptography rests on assuming some problems are harder than other problems. These problems form the cryptographic primitives, which can be used for constructing protocols.

For example, one cryptographic primitive is the one-way function. Abbreviated OWF, these functions are easy to compute, but hard to invert, where easy means "doable in polynomial time" and hard means "not doable in polynomial time." Assuming OWFs exist, we can create secure encryption, pseudorandom number generators, and many other useful things.

No one has proved one-way functions exist, because proving that would prove , and that's, well, a really hard problem, to put it mildly. However, there are several functions that people believe to be one-way, which can be used in practice.

For secure computation, we need two primitives: oblivious transfer, and symmetric encryption.

## Oblivious Transfer

_Oblivious transfer_ (abbreviated OT) is a special case of secure computation. Alice has two messages . Bob has a bit . We treat oblivious transfer as a black box method where

- Alice gives
- Bob gives bit , or
- If , Bob gets . Otherwise, he gets . In both cases, Bob does not learn the other message.
- Alice does not learn which message Bob received. She only knows Bob got one of them.

![Oblivious transfer](http://www.alexirpan.com/public/secure-comp/ot.png)

You can think of the OT box as a trusted third party, like Faith. Letting Alice send messages without knowing which message was received will be key to the secure computation protocol. I don't want to get into the details of implementing OT with only Alice and Bob, since they aren't that interesting. If you're curious, [Wikipedia has a good example based on RSA](https://en.wikipedia.org/wiki/Oblivious_transfer#1-2_oblivious_transfer).

## Symmetric Encryption

_Symmetric encryption_ allows two people to send encrypted messages that cannot be decrypted without the secret key. Formally, a symmetric encryption scheme is a pair of functions, encrypter and decrypter , such that

- is the encryption of message with key .
- is the decryption of ciphertext with key .
- Decrypting with the same secret key gives the original message, or ,
- Given just ciphertext , it is hard to find a key and message such that . (Where again, hard means not solvable in polynomial time.)

The above is part of the standard definition for symmetric encryption. For the proof, we'll need one more property that's less conventional.

- Decrypting with a different key results in an error.

With all that out of the way, we can finally start talking about the protocol itself.

## Yao's Garbled Circuits

Secure computation was first formally studied by Andrew Yao in the early 1980s. His introductory paper demonstrated protocols for a few examples, but did not prove every function was securely computable. That didn't happen until 1986, with the construction of Yao's garbled circuits.

To prove every function was securely computable, Yao proved every circuit was securely computable. Every function can be converted to an equivalent circuit, and we'll see that working in the circuit model of computation makes the construction simpler.

## A Quick Circuit Primer

For any function , there is some circuit such that gives the same output. That circuit is made up of logic gates and wires connecting them. Each logic gate has two input wires and one output wire, and computes a simple Boolean function, like AND or OR.

The wires in a circuit can be divided into three categories: inputs for the circuit, outputs for the circuit, and intermediate wires between gates.

![example circuit](http://www.alexirpan.com/public/secure-comp/circuit.png)

## Garbling Circuits

Here is a very obviously insecure protocol for computing

- Alice sends circuit to Bob.
- Alice sends her input to Bob.
- Bob evaluates the circuit to get
- Bob sends back to Alice.

![First protocol](http://www.alexirpan.com/public/secure-comp/protocol1.png)

This works, but Alice has to send to Bob.

This is where garbling comes in. Instead of sending , Alice will send a garbled circuit . Instead of sending , she'll send encoded in a certain way. It will be done such that Bob can evaluate with Alice's encoded input, without learning what Alice's original input was, and without exposing any wire values except for the final output.

<sup>
  <a href="http://www.alexirpan.com/2016/02/11/secure-computation.html#fn:length" class="footnote">2</a>
</sup>

Number the wires of the circuit as . For each wire , generate two random encryption keys and . These will represent and .

A garbled circuit is made of _garbled logic gates_. Garbled gates act the same as regular logic gates, except they operate on the sampled encryption keys instead of bits.

Suppose we want to garble an OR gate. It has input wires , and output wire .

![OR gate](http://www.alexirpan.com/public/secure-comp/or.png)

Internally, an OR gate is implemented with transistors. However, at our level of abstraction we treat the OR gate as a black box. The only thing we care about is that it follows this behavior.

- Given , it sets .
- Given , it sets .
- Given , it sets .
- Given , it sets .

This is summarized in the function table below.

A garbled OR gate goes one step further.

![Garbled OR gate](http://www.alexirpan.com/public/secure-comp/garbled_or.png)

It instead takes for , and for . Treating these keys as bits or , the output is or , depending on which bit the gate is supposed to return. You can think of it as replacing the OR gate with a black box that acts as follows.

- Given , it sets .
- Given , it sets .
- Given , it sets .
- Given , it sets .

By replacing every logic gate with a garbled gate, we can evaluate the circuit using random encryption keys instead of bits.

We implement this using symmetric encryption. For every possible pair of input keys, the correct output is encrypted using both those keys. Doing this for all possible inputs gives the _garbled table_,

With one secret key for each wire, Bob can get the correct output by attempting to decrypt all 4 values. Suppose Bob has and . Bob first decrypts with . He'll decrypt exactly two values, and will get an error on the other two.

Decrypting the two remaining messages with gives

getting the key corresponding to output bit for wire .

(To be fully correct, we also shuffle the garbled table, to make sure the position of the decrypted message doesn't leak anything.)

Creating the garbled table for every logic gate in the circuit gives the garbled circuit. Informally, here's why the garbled circuit can be evaluated securely.

- For a given garbled gate, Bob cannot read any value in the garbled table unless he has a secret key for each input wire, because the values are encrypted.
- Starting with the keys for the circuit input, Bob can evaluate the garbled gates in turn. Each garbled gate returns exactly one key, and that key represents exactly the correct output bit. When Bob gets to the output wires, he must get the same output as the original circuit.
- Both and are generated randomly. Given just one of them, Bob has no way to tell whether the key he has represents or . (Alice knows, but Alice isn't the one evaluating the circuit.)

Thus, from Bob's perspective, he's evaluating the circuit by passing gibberish as input, propagating gibberish through the garbled gates, and getting gibberish out. He's still doing the computation - he just has no idea what bits he's actually looking at.

(The one minor detail left is key generation for the output wires of the circuit. Instead of generating random keys, Alice uses the raw bit or , since Alice needs Bob to learn the circuit output.)

## The Last Step

Here's the new protocol, with garbled circuits

- Alice garbles circuit to get garbled circuit
- Alice sends to Bob.
- Alice sends the keys for her input to Bob.
- Bob combines them with the input keys for , and evaluates to get
- Bob sends back to Alice.

![Second protocol](http://www.alexirpan.com/public/secure-comp/protocol2.png)

There's still a problem. How does Bob get the input keys for ? Only Alice knows the keys created for each wire. Bob could give Alice his input to get the right keys, but then Alice would learn .

One solution is to have Alice send both keys for each of 's input wires to Bob. For each wire, Bob can then pick the key corresponding to the correct bit of . That way, Bob doesn't have to give Alice , but can still run the circuit.

However, this has a subtle bug: by giving Bob both keys for his input wires, **Alice lets Bob run the circuit with an arbitrary .** Letting Bob evaluate many times gives Bob more information. Going back to the millionaire's problem, let and . At the end, Bob learns Alice is richer, meaning . But, if he later evaluates , he'll learn , which is more than he should know.

Thus, to be secure, Bob should only get to run the circuit on exactly . To do so, he needs to get the keys for , and only the keys for , without Alice learning which keys he wants. If only there was a way for Alice to obliviously transfer those keys...

Alright, yes, that's why we need oblivious transfer. Using oblivious transfer to fill in the gap, we get the final protocol.

- Alice garbles circuit to get garbled circuit
- Alice sends to Bob.
- Alice sends the keys for her input to Bob.
- Using oblivious transfer, for each of Bob's input wires, Alice sends to Bob.
- With all input keys, Bob can evaluate the circuit to get
- Bob sends back to Alice.

![Final protocol](http://www.alexirpan.com/public/secure-comp/protocol3.png)

This lets Alice and Bob compute without leaking their information, and without trusted third parties.

## Conclusion

This protocol is mostly a theoretical exercise. However, there has been [recent work](https://www.cs.uic.edu/pub/Bits/PeterSnyder/Peter_Snyder_-_Garbled_Circuits_WCP_2_column.pdf) to make it fast enough for practical use. It's still tricky to use, because the desired function needs to be converted into an equivalent circuit.

Despite this difficulty, Yao's garbled circuits are still a very important foundational result. In a post-Snowden world, interest in cryptography is very high. There's lots of use in designing protocols with decentralized trust, from Bitcoin to [secure cloud storage](https://css.csail.mit.edu/cryptdb/). It's all very interesting, and I'm sure something cool is just around the corner.

_Thanks to the many who gave comments on drafts of this post._
