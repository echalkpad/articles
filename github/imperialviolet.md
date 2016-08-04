# ImperialViolet

[Original URL](https://www.imperialviolet.org/2015/12/24/rlwe.html)

> If large quantum computers can be built (and not of the D-Wave variety) then they'll make quite a mess of public-key cryptography. RSA and systems based on discrete logarithms (i.e. finite-field and...

If large quantum computers can be built ([and not of the D-Wave variety](http://www.scottaaronson.com/blog/?p=2555)) then they'll make quite a mess of public-key cryptography. RSA and systems based on discrete logarithms (i.e. finite-field and elliptic-curve Diffie-Hellman) are all broken. I've written about [hash-based signatures](https://www.imperialviolet.org/2013/07/18/hashsig.html) (which resist quantum attacks) before and the recent [PQCRYPTO recommendations](http://pqcrypto.eu.org/docs/initial-recommendations.pdf) suggest those for signatures and McEliece for public-key encryption. Those are both sound, conservative recommendations but both have some size problems: McEliece public keys can be on the order of a megabyte of data and conservative hash-based signatures are about 40KB.

In some situations that's not a problem, and things like firmware signatures, where the key is embedded in hard-to-change silicon, should consider using hash-based signatures today. But those costs motivate the search for post-quantum schemes that are closer to the small, fast primitives that we have today.

One candidate is called Ring Learning-With-Errors (RLWE) and I'll try to give a taste of how it works in this post. This is strongly based on the [A New Hope](https://eprint.iacr.org/2015/1092) paper by Alkim, Ducas, Pöppelmann & Schwabe, and, in turn, on papers by [Bos, Costello, Naehrig & Stebila](http://eprint.iacr.org/2014/599) and [Peikert](http://web.eecs.umich.edu/~cpeikert/pubs/suite.pdf).

Firstly, the basic stats (because I always hate having to dig around for these values in papers). I've included the numbers for a current, elliptic-curve based Diffie-Hellman scheme (X25519) for comparision:

                          | A New Hope                   | X25519
------------------------- | ---------------------------- | ----------------------------
Alice's transmission size | 1,824 bytes

<sup>a</sup>    | 32 bytes
Alice's computation       | 129,638 cycles

<sup>b</sup> | 331,568 cycles

<sup>c</sup>
Bob's transmission size   | 1,824 bytes                  | 32 bytes
Bob's computation         | 126,236 cycles               | 331,568 cycles

(

<sup>a</sup>

 This is using a more compact scheme than in the paper. 

<sup>b</sup>

 These are Haswell cycle counts from the paper. 

<sup>c</sup>

 These are values from the [SUPERCOP](http://bench.cr.yp.to/results-dh.html) benchmark on titan0.)

Something to keep in mind when looking at the numbers above: sending 1,824 bytes on a 10MBit link takes 5.1 million cycles, assuming that your CPU is running at 3.5GHz.

## RLWE key agreement

Our fundamental setting is ℤ

<sub>12289</sub>

[X]/(X

<sup>1024</sup>

+1). That's the set of polynomial equations where the largest power of _x_ is 1023 and the coefficients are values between zero and 12288 (inclusive). For example, 66 + 4532_x_ + 10000_x_

<sup>2</sup>

 + ... + 872_x_

<sup>1023</sup>

.

Addition and multiplication can be defined for polynomials in this set. Addition is done by matching up powers of _x_ and adding the corresponding coefficients. If the result is out of range then it's reduced modulo 12289.

Multiplication is high-school polynomial multiplication where the polynomial on the right is multiplied by every term of the polynomial on the left and the result is the sum of those terms. Coefficients are reduced modulo 12289 to keep them in range, but it's likely that the resulting powers of _x_ will be too large--multiplying two _x_

<sup>1023</sup>

 terms gives a result in terms of _x_

<sup>2046</sup>

.

Polynomials with too large a degree can be reduced modulo _x_

<sup>1024</sup>

+1 until they're in range again. So, if we ended up with a term of _a_×_x_

<sup>2046</sup>

 then we could subtract a×_x_

<sup>1022</sup>

(_x_

<sup>1024</sup>

+1) to eliminate it. By repeated application of that trick, all the terms with powers of _x_ greater than 1023 can be eliminated and then the result is back in the set.

Now that we can add and multiply within this set of polynomials we need to define a _noise_ polynomial: this is simply a polynomial where each coefficient is sampled from a random distribution. In this case, the distribution will be a centered binomial distribution that ranges from -12 to 12\. The probability density looks like this:

Produced by GNUPLOT 5.0 patchlevel 1 0 -15 -10 -5 0 5 10 15 An important feature of noise polynomials is that the magnitude of each coefficient is small. That will be critical later on.

A _random_ polynomial is one where each coefficient is sampled from a uniform distribution over the full range of zero to 12288.

To build a Diffie-Hellman protocol from this, Alice generates a random polynomial, **a**, and two noise polynomials, **s** and **e**. Alice calculates **b** = **as**+**e** and sends **a** and **b** to Bob. Bob generates his own **s′** and **e′**, uses Alice's **a** to calculate **u** = **as′**+**e′**, and sends **u** back to Alice. Now Alice can calculate **us** = (**as′**+**e′**)**s** = **as′s**+**e′s** and Bob can calculate **bs′** = (**as**+**e**)**s′** = **ass′**+**es′**. But the keen-eyed will notice that those are different values!

The added noise is necessary for security but it means that the two sides to this protocol calculate different values. But, while the values _are_ different, they're very similar because the magnitude of the noise polynomials is small. So a _reconciliation_ mechanism is needed to find a shared value given two, similar polynomials.

## Reconciliation

So far I've been following the [A New Hope](https://eprint.iacr.org/2015/1092.pdf) paper and it does include a reconciliation mechanism. But, to be honest, I'm not sure that I understand it, so I'm going to be describing a mechanism by Peikert here:

The reconciliation will treat each coefficient in the similar polynomials separately and will extract a single, shared bit from each. Since we're dealing with polynomials that have 1024 terms, we'll get a 1024-bit shared secret in total but I'm just going to discuss the process of processing a single coefficient to get a single bit.

Consider the coefficient space as a circle: zero and 12289 are the same value modulo 12289 and we put that at the top of the circle. At the bottom of the circle will be 12289/2 = 6144 (rounding down). We know that, for each coefficient, Alice and Bob will have similar values--meaning that the values will be close by on the circle.

0 6144 One option is to split the circle into left and right halves and say that if the point is in the left half then it's a zero, otherwise it's a one.

0 6144 0 1 But while that will work most of the time, there's obviously a problem when the points are near the top or the bottom. In these cases, a small difference in location can result in a point being in a different half, and thus Alice and Bob will get a different result.

In this case we want to split the circle into top (zero) and bottom (one), so that both points are clearly in the bottom half.

0 6144 0 1 But that just moves the problem around to the left and right edges of the circle. So how about we vary the basis in which we measure the points depending where they are? If the point is near the bottom or the top we'll use the top–bottom (blue) basis and, if not, we'll use the left–right (red) basis.

0 6144 But there's still a problem! Consider the two points in the diagram just above. One party will think that it's in a red area, measure it left–right and conclude that the shared bit is a zero. The other will think it's in a blue area, measure it top–bottom and conclude that the shared bit is a one.

There isn't a solution to this in which the parties operate independently so, instead, one of the parties chooses the basis in which each coefficient will be measured. This information is a single bit of information (i.e. red or blue) that we have Bob send to Alice along with his value **u**. With this _reconciliation information_ in hand, both parties can measure their points in the same, optimal basis and calculate the same, shared value.

## Optimisations

There's lots in the paper that I've skipped over here. Most importantly (for performance), a variant of the Fourier transform can be used to convert the polynomials into the frequency domain where multiplication is much faster. Some of the values transmitted can be transmitted in the frequency domain too to save conversions overall. Also, random polynomials can be sampled directly in the frequency domain.

The parameters here have also been carefully selected so that the reduction stage of multiplication happens magically, just by point-wise multiplication of a some constants before and after the transform.

The **a** value that Alice generates could be a global constant, but in order to save worrying about how it was generated, and to eliminate the possibility of all-for-the-price-of-one attacks (like [LogJam](https://weakdh.org/)), it's generated fresh for each instance. Rather than transmit it in full, Alice need only send a seed value for it to Bob.

## Contrasts with Diffie-Hellman

The most important change from Diffie-Hellman is that this scheme requires that all values be ephemeral. Both QUIC and TLS 1.3 assume that a Diffie-Hellman public-value can be reused but, in this scheme, that breaks the security of the system. More traditional uses of Diffie-Hellman, e.g. TLS 1.2 and SSH, are fine though.

Another important change is that this scheme takes a full round-trip for Alice. With Diffie-Hellman, both parties can transmit a message at time zero and as soon as the other party has received the message, they can calculate the shared key and start transmitting. But even if the **a** value is a global constant in this scheme, the reconciliation process means that Bob can't send a message until he's received Alice's message, and Alice can't calculate the shared key until she has received Bob's message. So Alice has a wait a full round-trip.

Often that limitation isn't important because other parts of the protocol already require the round-trip (for example, in TLS). But for some uses it's a critical difference.

Also, since this protocol involves random noise it has a failure probability: it's possible that the reconciliation mechanism produces different answers for each side. Thankfully this probability can be made negligible (i.e. less than one in 2

<sup>64</sup>

).

I should also note that the RLWE problem is hypothesised to be resistant to quantum attack, but we don't know that. We also don't know that it's resistant to attacks by classical computers! It's possible that someone will develop a classical algorithm tomorrow that breaks the scheme above. Thus it should be used concurrently with a standard Diffie-Hellman (e.g. [X25519](https://tools.ietf.org/html/draft-irtf-cfrg-curves-11)) and the outputs of each should concatenated as the input keying material for a KDF.
