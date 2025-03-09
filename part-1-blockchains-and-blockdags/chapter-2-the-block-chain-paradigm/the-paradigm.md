# The Paradigm

The blockchain paradigm maintains that many interesting block chains differ only by the way they resolve conflict. That is, the _chain selection rule_ they follow.

So to describe the paradigm, I owe you two things: an _abstract_ _definition_ of a chain selection rule, and a _concrete_ _description_ of what miners do with this chain selection rule. This is the purpose of thee current section. Once it is concluded, owe you a _third_ explanation: _why_ should miners follow the expected behavior? This will be addressed in the following two sections.

## Block Validity

One issue we need to get out of the way is _block validity_. We all know that blocks can be invalid. They can have corrupt headers, illegal transactions, bad nonce, etc. Obviously a part of the miner's work is to verify the validity of each block before even considering it.

Verifying block validity is not trivial, and we will have much to say about it, especially in the context of GHOSTDAG. But for now, we treat it as somebody else's problem and assume all blocks that arrive at our networks are validated for us, and discarded if they fail.

## Chain Selection Rules

A _chain selection rule_ has a very simple task. It is given a [tree](a-graph-theory-primer.md#trees) and outputs a tip. Why do we call it a _chain selection rule_ if it outputs a tip? Well, recall that each tip $$B$$ of the tree defines a _unique_ chain $$B.Chain$$ to the root/genesis.

The tree we feed into this chain selection rule is the tree of blocks created by the network (or at least those available to us).

Given a selected tip $$B$$ we call $$B.chain$$ _the selected chain_ or just _the chain_. Blocks on this chain are called _included_, and the remaining blocks are called _orphans_.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
"But wait," you ask, "shouldn't a chain selection rule be able to choose a chain that is _not_ starting from a tip? Aren't you harming generality this way?"\


Well yes, yes I am. When defining a chain selection rule this way ("return any chain you see fit as long as it ends in genesis") the full generality allows for weird and unsavory chain rules, like only allowing chains of even length. Or what about the "chain selection rule" that always returns the genesis block? Surely, our definition must prohibit these somehow.

What saves us is that we assume a chain rule is _monotone_: it would always prefer $$B$$ over $$B.Parent$$. This requirement makes sense, since the entire point is to be _inclusive_. Why should we exclude a block that we can include?&#x20;

Monotonicity is equivalent to requiring that the chain selection rule returns a tip and not just any arbitrary block.
{% endhint %}



## Breaking Ties

Chain selection rules sometimes run into _ties_. Situations where they have to decide between "equal" options. For example, consider a situation like this:

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



Which tip should the chain rule prefer, $$A$$ or $$B$$? For most of the chain rules we will see, there is no _natural_ way to choose between them. For example, we have not introduced the longest chain rule yet, but it does not require a wild stretch of the imagination to get convinced that the selected chains of $$A$$ and $$B$$ have the same length. So how should this tie be broken?

Like pretty much everything that has anything to do with blockchains, choosing a tie-breaking rule might seem innocuous, but in practice, a bad tie-breaking rule could have adverse consequences. For example, if the rule is "gameable" (that is, there are ways for large miners to increase their probability of winning the tie break), it would exacerbate an already existing problem called [selfish mining](selfish-mining-in-bitcoin.md), that we will describe later in this chapter.

A crucial observation is that the tie breaking rule itself _doesn't have to be in consensus_. The situation where miners "split" to mine over different blocks is unavoidable. For example, in the situation depicted in the illustration above, there will plausibly be some nodes that heard of $$A$$ first and other nodes that heard of $$B$$ first. **It is impossible to avoid these situations**. The chain rule provides us with a consensus of the winner _after the tie has been broken_. But who shall this winner be? That's anybody's game.

Note that this also means that tie-breaking rules _cannot be enforced_. There is no way to force a miner to mine over block $$A$$ when they "should have" mined over block $$B$$, as they could always claim the block $$B$$ never reached them. The best we can do is suggest a tie-breaking rule that is aligned with the miners' incentives.&#x20;

In Bitcoin, miners mine over the block they _observed first_. This makes sense from the miner's point of view: if $$A$$ arrived at some miner earlier than $$B$$, then it is plausible that, in general, $$A$$ was created earlier or by a more well connected miner. That's good grounds to assume most of the network mines over $$A$$, increasing the probability that $$B$$ is forked away, making mining over it a wasted effort.

{% hint style="info" %}
Tie breaking rules make appearances in other surprising contexts. For example, in his 2018 paper [On the insecurity of quantum Bitcoin mining](https://arxiv.org/abs/1804.08118), Or Sattath (full disclosure: my Ph.D co-adviser) noted that the presence of quantum miners gives rise to an optimal strategy called _aggressive mining_, that increases the fork rates. They propose a modification of the tie-breaking rule as a countermeasure.
{% endhint %}

## The Block Chain Paradigm (at last)

The block chain paradigm translates a chain selection rule to the following protocol:

* Miners mine **above the selected tip**
* Whenever a miner discovers a new block (by mining it or from a peer), they send it to their peers and **recompute the selected tip**

Simpler than you expected? That's a good sign. It means we did a good job encapsulating away the tedious details.
