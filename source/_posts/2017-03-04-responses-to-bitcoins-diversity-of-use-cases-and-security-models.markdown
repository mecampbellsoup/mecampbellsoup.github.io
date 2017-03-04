---
layout: post
title: "Responses to Bitcoin's Diversity of Use-Cases and Security Models"
date: 2017-03-04 15:01:41 -0500
comments: true
categories: bitcoin
---
Original post: http://bluematt.bitcoin.ninja/2017/02/28/bitcoin-trustlessness/

> While there is arguably some trust in miners required to ensure the entirety of the blockchain isn’t reorganized

Why are you singling out miners? The 51% attack vector is neither exclusive to nor more likely to be pursued by miners. Anyone can incur the PoW cost to mine an alternative version of history (alternative blockchain) and propagate those blocks to nodes. In fact, I would argue that miners are less likely than non-miners to pursue a 51% attack since they are in an extremely competitive business.

> Of course to ensure you aren’t trusting miners and pools to secure their operations perfectly

What does this mean? If your point is that one should wait for `n` confirmations before assuming your transaction is valid, that is incorrect. Miners have no role in validating transactions (full nodes do that); they just choose which already-validated transactions should be put in the next block. If they try to put invalid transactions in a block, then that block will be rejected and their mining reward foregone.

> Clearly a payment system which requires a week or more for payment to clear would not be able to compete with much faster alternatives.

This is not an apples to apples comparison. Many people seem to want to compare Bitcoin to Visa, but that is a trap. After SegWit is activated, Bitcoin will *enable* a trustless, instantaneous and secure peer-to-peer payment network like Lightning, but Bitcoin itself will never be that payment network by design. Acheiving decentralized consensus via PoW and fully-validating nodes requires a massive amount of redundant messaging to propagate throughout the network, which is not a design that lends itself to high throughput for the network at large.

> Many investors who care strongly about Bitcoin’s scarcity properties are happy to trust centralized third-parties in the form of Bitcoin exchanges and “Bitcoin banks”.

That trust is misplaced. They should not keep coins at an exchange (see: all the exchange hacks) and should certainly not get anywhere near something called a "Bitcoin bank" since in Bitcoin users *are* their own bank. Bitcoin is all about financial disintermediation. 

> Many Bitcoin users who want fast payments for medium- to small-value transactions are happy to trust miners, in sufficient measure.

In what way are they trusting miners? Again, miners do not validate transactions, they just decide which transactions to put in the next block. 

> Such trust relationships, as long as users aren’t forced into them (either by explicit requirement or sufficiently strong financial incentive), can provide significantly better user experience through faster, cheaper, and more user-friendly transactions.

Aren't you saying that users *choose* such a trusted relationship? One would think they do this because of a strong financial incentive, e.g. 0 confirmations at one time was considering a value-add feature for payment processors to attract users, but the payment processor would have to trust the user to not double spend. Can you give an example of what you're describing in this paragraph?

>  (eg trusting miners or developers to keep the 21 million Bitcoin limit)

Neither of these make sense to me. Developers do not own the monopology on total number of Bitcoins in circulation, clearly. What does 'trusting miners' mean?

> Bitcoin must only change by consensus of its ever-growing userbase.

Bitcoin *is* consensus, and that consensus is determined by the software being run primarily by fully-validating nodes, and additionaly to some extent by miners & wallets/clients which relay new transactions to the network. Bitcoin does not care about whether or not people agree or not.

> Putting all of this together we see a picture of where Bitcoin must evolve if it wants to retain its trustless properties while providing a usable system for its many, vastly divergent, use-cases.

That step in the evolutino is already here and it's called SegWit. So, run a SegWit full node and help us move towards that vision. 

> or even a trusted “Bitcoin bank”.

This is not one of the divergent use cases you mention that are relevant or specific to Bitcoin. If you want to trust a 3rd party, you don't need (and shouldn't use) Bitcoin. 

> Users which do not even want to trust miners should be free to do so, placing their transactions on the blockchain and waiting weeks to ensure even future hashpower attacks will not reverse them

What? I'm lost. By "placing their transactions on the blockchain" are you talking about running one's own mining equipment? And, again, you aren't trusting miners. Miners expend energy to perform proof-of-work; an attacker trying to double spend needs to *redo* all of the work beteween (a) the block that includes the double spend transaction(s), and (b) the most recent block in the chain.

> the community of Bitcoin users must continue to enforce that changes happen only through consensus among the ever-broadening group

What community? Whom are you addressing? Bitcoin is about *software running on machines*, not people's opinions about what is good or bad for Bitcoin.

> Critically, this means that all changes which do not harm the utility of Bitcoin for any of its many use-cases, while helping others, should be made, wherever possible.

This is the exact thing people are disagreeing about currently vis-a-vis SegWit. And since SegWit is an opt-in backwards compatible soft fork that preserves the trustless, decentralized model, it should be activated.