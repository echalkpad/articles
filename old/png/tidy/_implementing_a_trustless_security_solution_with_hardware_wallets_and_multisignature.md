# Implementing a trustless security solution with hardware wallets and multisignature

[Original URL](https://medium.com/@Ledger/implementing-a-trustless-security-solution-with-hardware-wallets-and-multisignature-8f50732c6f4c)

> Hardware wallets are bringing a strong layer of security to the Bitcoin industry. Private keys are stored safely inside a dedicated device, and all critical operations such as transaction signing are...

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*IJbcuBXFjzREwkHO0hL_og.jpeg)

Hardware wallets are bringing a strong layer of security to the Bitcoin industry. Private keys are stored safely inside a dedicated device, and all critical operations such as transaction signing are being executed in its secure environment. However, this does not truly amount to a trustless solution: you need to have some faith in its firmware and/or its hardware.

The risk of having an ARM microprocessor running rogue code or microcode is of course extremely low, but the need to trust any kind of third party can be a no-go for some users, especially when considering the storage of large amounts of funds.

Also, the possibility of physical assault is real and must be taken into account. One could use plausible deniability PIN codes when under duress, but would you really play this card if your children were being held at gun point?

This is where multisignature comes handy to solve this problem: what if the signature of two different hardware wallets was required to approve a payment? Not only would it protect you against all possible attack vectors, but you would get a 100% trustless solution.

By using two separate hardware wallets and requiring them both to sign a payment transaction, you do not have to trust any third party. Even if you had rogue hardware running tampered code or compromised multisignature tools, the possibility that they could collaborate to execute a synchronized hack is extremely unlikely. And if you still thought it was not enough, you could opt for more complex m of n schemes.

If you don't have direct control of all the private keys of your wallets, even if they're written on paper wallet backups stashed somewhere safe, and if you need to ask someone else to retrieve one or several keys because the others were compromised, then by definition you are not in a trustless system.

Traditionally, multisignature has always been a complex tool to implement. [GreenAddress](https://greenaddress.it) was one of the first companies to bring the added security level of multignature to mainstream audiences with their excellent solution. But if you wanted to build a custom multisig scheme, you had either to entrust the keys to a third party hosting them, or to juggle with master private seeds kept on insecure devices.

With the integration of the [Ledger API](https://github.com/LedgerHQ/ledger-wallet-api) by [Coinkite](https://coinkite.com), it is now possible in just a few clicks to build a very secure and trustless 2 of 2 multisignature wallet. Each key is secured by a Ledger Nano (or Ledger HW1), and the transaction logic is managed seamlessly by Coinkite's interface.

In case of a disaster (Ledger and/or Coinkite goes out of business), you can recover your bitcoins using BIP39 backups and some scripting tools.

![](https://d262ilb51hltx0.cloudfront.net/max/600/1*FUH2owr7UjL_4UbbVZN-Gg.png)

Creating a multisignature vault at Coinkite is **free**, and [ordering](https://www.ledgerwallet.com) a Duo edition of the Ledger Nano (containing two keys) would cost you **41.50€** (excluding shipping and taxes). For a cheaper option, the Ledger HW1 Duo is sold for **21.50€** (+ taxes for EU countries) including worldwide free shipping.

To demonstrate how easy and convenient it is to create and use a 2 of 2 multisignature wallet with Ledger and Coinkite, we have written a [complete step by step tutorial](https://ledger.groovehq.com/knowledge_base/topics/how-to-create-a-2-of-2-multisignature-account-with-two-ledger-nano-and-coinkite).

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*tMa2poXWJ5nj7IbsAmJV5Q.png)

A co-signing page on Coinkite server where a transaction has been approved with the signature of two different Ledger Nano Key management is a very important element of the equation: use **different computers** for each signature, and keep them in **different locations**.

Also, you might want to compile yourself the [Ledger Chrome app](https://github.com/LedgerHQ/ledger-wallet-chrome), to rule out any possibility of foul play by either Ledger or Coinkite.

> At such a low price and high level of security, there is no excuse not to use Ledger and Coinkite to safeguard your bitcoin savings.
