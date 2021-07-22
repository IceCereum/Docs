# The IceCereum Network - A Beginner's Guide

This document will explain two things:

- What a blockchain / cryptocurrency is and what the IceCereum Network is
- What the [wallet](https://github.com/IceCereum/Wallet) does under the hood

### What is a blockchain / cryptocurrency

If you gave me $100 a few months ago and you asked for it back, I can say you
didn't give me any and there would be no way for anyone to prove anything. The
next best thing is to involve a trusted individual that we both know and make
them the witness. I can bribe this individual and then they can say that you
never gave me money. Now, we go to an established institution like a bank to
officially record a transfer of $100 from you to me. The issue now is that the
bank is a central authority that can have its own issues (like getting hacked,
for example) - essentially almost like a single point of failure.

The idea of the blockchain is to go a step further, a step larger and bigger in
every aspect. Imagine that we have a thousand people who we don't know in front
of us. Imagine that we tell all of them that you gave me $100 and they all take
a note of it in their accounting book or ledger. For me to say that you _didn't_
pay me I have to convince 50.1% of those thousand people to say that you, in
fact, did not. This just became a much harder task for me. You may wonder why
these people may even agree to do this; they do it because they're given a small
fee for doing so.

That's a vague gist of what a blockchain really is - a distributed ledger. There
are more cryptographic intricacies involved but this doesn't really get into
that. There are people who verify transactions (i.e. the sender has enough
money) and add these transactions to a block in the big ledger. These people are
called miners. They also try to do some "complex math" that help add the block
of transactions into the ledger, now a chain of blocks. The more number of
independent people who keep track of this ledger, the harder it becomes for a
person like me to try to convince a majority that you did not give me $100.

Cryptocurrencies are coins that use this blockchain idea, or special currencies
that these ledgers understand. Of course, this is a large oversimplification;
however, the primary points still remain true - they use blockchains and they
are special currencies. A real currency (fiat) is brought into circulation by a
country's mint. A country's mint is centralised and controlled by the
government. Coins in a cryptocurrency, on the other hand, are introduced into
the blockchain at regular intervals to increase the supply via the miners.
Whenever a miner does the "complex math" that helps add the block of
transactions correctly, they get the reward of the transactions as well as
fabricate a pre-determined amount of coins and reward themselves and notify the
other miners.

This way, coins are brought into the system periodically to linerally increase
the supply. The demand, however, is dependent on how many people want to own
this coin and how much they're willing to pay for it. This creates some very
interesting economics and is often called as a hyper-volatile market.

#### IceCereum

I wanted to make a coin for the meme sake. It was funny when I started talking
about it with friends, now I'm just sick of working on it for over a week and
want to get it over with. IceCereum, first of all, is a **private**
cryptocurrency. I created it with the intention of the coins to be never listed
on a marketplace where people can sell them and buy them for real money. I
created with the intention of trading coins between friends in case they ever
wanted to even if they never wanted to. So because of this, I took **a lot** of
liberties when writing the code:

1. There is only one miner. Writing code that could be deployed on many
computers that sync with each other sounds really exciting, but also very
exhausting. So, for now, I have only one miner that keeps the ledger.
  > "Wait a second. That sounds hella scuffed. _You're_ the only miner? Can't
  you take over the network if you want to?", You ask.

  - Yes. The answer to this is yes. I can take over the network if I wanted to.
  The thing is I won't because a) I created the network, and b) there isn't any
  money attached to this. In the future I do plan to work on syncing miners
  that can collectively work together, but that's not immediately planned.

2. The core blockchain doesn't implement merkle trees. This is a major
foundation on which most, if not all, blockchains work on. I don't think this
coin will ever have more than 1,000 or 2,000 transactions overall. Implementing
merkle trees would be a waste of time. Instead there's a lot of manual lookup.
If this _does_ cross 1,000 or 2,000 transactions, a) I will be very surprised,
and b) I will consider implementing merkle trees.

3. <s>I don't think I really want to open source the Network because whatever I
wrote is a pile of garbage I never want anyone to see. I'm frankly embarassed
that I even wrote this. For all you know, I could just be keeping a big
dictionary of addresses and balances instead of implementing a proper
blockchain. You can rest assured I didn't. When (and if) I refactor my code, I
will open source it, but just not today.</s> Whodathunk it... I refactored my
code and you can now find it at [Core](https://github.com/IceCereum/Core).

### What the wallet does under the hood

The [wallet](https://github.com/IceCereum/Wallet) is a command line interface
that interfaces with the IceCereum Network. You don't need an email, a credit
card or anything to create a wallet. You just need to run the program and issue
the command that creates a wallet. Right before a wallet is created, a 12 word
seed phrase is generated under the hood. In the program, there are 4096 words
(2 letters to 6 letters long) in the dictionary. 12 random words are selected
and those words in that sequence are converted to numbers which help generate
your wallet. Entering these 12 words in the same order will always result in the
generation of the original wallet as it will generate the same set of keys 
(explained below).

The wallet, simply put, is a set of two keys - a public and a private. To
understand the math of this takes quite a long time but
[here](https://youtu.be/YEBfamv-_do) is a nice and simple video explaining the
workings of it. The idea is your public key is shown to everyone but your
private key is only known to you. These two keys do voodoo magic that help
authenticate you and ergo your transactions at anytime. When you use the
program, you don't really have to worry about handling the public and private
key. The program does that for you. All you need to tell people is your
"address" which is derived from the same keys.

When you send a transaction, your wallet encodes a message with your private
key saying that that you are transferring some amount to some address. Anyone
can then decrypt the encrypted message **only** with your public key. Because
the message is decrypted with your public key, it must be you who decided to
transfer the amount. There are plenty of subtleties that are not explained, but
this is the general idea. As long as you have your public and private key, you
have your account. But since you aren't expected to know / remember those two,
you can write down your 12 word seed that can generate these for you.
