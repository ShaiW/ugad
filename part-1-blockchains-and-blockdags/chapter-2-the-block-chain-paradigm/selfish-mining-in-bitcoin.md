# Selfish Mining in Bitcoin

Recall that when we described the [block chain paradigm](the-paradigm.md) we said that we expect two things of honest miners: to mine over the selected tip, and to immediately rebroadcast any valid block they learn of. This naturally led to a discussion over what makes these assumptions justified. We addressed this by noting that the Bitcoin network places incentives such that a [_rational_ miner](honesty-and-rationality.md), seeking to maximize profit, will always choose to mine over the selected tip. And what about reporting all blocks immediately? Well, not quite...

## The Rational Miner

We defined a _rational_ miner to be a miner seeking to increase profits from mining. Does that necessarily mean they seek to mine as many blocks as possible? Not exactly. A more accurate description is that they seek to mine the _largest fraction_ of _non-orphaned blocks_ possible. The subtlety, noticed by Ittay Eyal and Emin Gün Sirer in their 2013 paper [Majority is not Enough: Bitcoin Mining is Vulnerable](https://arxiv.org/pdf/1311.0243), is that _the above are not the same_, and that by _withholding blocks_ a miner can _increase their fraction_ by _increasing the chance competing blocks are orphaned_.

I repeat the subtlety: the attack might _decrease_ the _number_ of non-orphaned blocks created by the adversary, but it causes _a larger increase_ in orphan rates of honest blocks, making the _fraction_ of the attacker grow.

How is it that the attacker actually wasted work, but increased is profit? Because he caused _more_ of the honest work to be wasted. Which brings us to why selfish mining is a concern: it is not only about miners getting more than their fair share, its that this strategy, that turns out to be rational, is _degrading the security by increasing orphan rates_.

So the consequences of selfish mining are not only that the rational and honest strategies are not actually aligned, but that the actual rational strategy is detrimental. So how come Bitcoin is still running securely? Well, the good news are that a selfish miner needs a very large fraction of the global hash rate to be profitable, and that selfish mining attacks are detectable.

## Selfish Mining Strategies

They key to a selfish mining attack is _withholding blocks_. We already considered another block withholding attack, a _double-spend attack_. In a double-spend attack, the adversary attempts to _reorg_ the chain. She withholds an _entire competing chain_ and only posts it once it is heavier than the honest chain. Selfish mining is a much more subtle version. You can think of it as constantly attempting to reorg the latest block, and trying again if you fail. Since you don't care _what_ block you want to reorg out, only that you reorg enough honest blocks to increase your fraction, you can always try again.

Say that I am a rational miner, and that I just mined a block. Before broadcasting it to the world, this is my point of view:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

An honest miner will report the private block right away, but since I am _rational_, I'd rather wait a little bit and see where the wind is going. Say after a little bit, the honest network created a parallel block, then we are in this situation:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

If I keep mining, then there is _some_ chance that I create a block before the honest network, leading to this situation:

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

At this point I can finally publish the withheld blocks, and they will orphan the honest block:

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

The point is this: if I had published my first block the second I found it, the honest network would have considered it the selected tip and switched to mining above it. This would have deprived me of the ability to use my temporary advantage to orphan their work, getting a larger share.

However, I don't _have to_. I can try my luck at maybe creating a longer private chain, orphaning out more honest network blocks and further increasing my gain.

What if the honest network mined a block first, bringing us to this situation:

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Well, in that case it is also my choice. I can keep going, hoping to take the lead again, or I can release my private chain, which is the same length of the public chain, hoping that the network prefers my side. If I decide to keep mining, most chances that I will _not_ gain the next lead, and find myself in this situation:

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

So what do I do now? Do I give up on my private chain and restart the attack, or do I keep pushing?

All of the questions above are what defines a selfish mining strategy.

## The Eyal-Sirer Strategy\*

The explicit strategy given by Eyal and Sirer goes like this:

* As long as you have an advantage, keep going
* If your advantage ever _decreases_ from two to one, _publish your chain_
* If your advantage increased to one for a while, and then decreased back to zero, publish your chain
* If you find yourself at a disadvantage, give up on your chain

Let us try to understand the success rates of this strategy. For this, let $$\alpha$$ be the hash rate fraction of the selfish miner, and let $$\gamma$$ represent the miner's _connectivity_, that is, the _probability that the selfish-miner's chain wins_ in the case that _it is the same length of the honest network_.



## Selfish Mining and Tie Breaking





