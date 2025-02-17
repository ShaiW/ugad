# The Paradigm

The blockchain paradigm maintains that many interesting blocks differ only by the way they resolve conflict. That is, by following a _chain selection rule_.

So to describe the paradigm, I owe you two things: an _abstract_ _definition_ of a chain selection rule, and a _concrete_ _description_ of what miners do with this chain selection rule. Then I also owe you a third thing: _why_ should miners follow the expected behavior?

## Chain Selection Rules

A _chain selection rule_ has a very simple task. It is given a [tree](a-graph-theory-primer.md#trees), and outputs a tip. Why do we call it a _chain selection rule_ if it outputs a tip? Well, recall that each tip of the tree defines a _unique_ chain to the root/genesis. So there is no difference.

The tree we feed into this chain selection rule is the tree of blocks created by the Bitcoin network (or at least those available to us).

Given a selected tip $$B$$ we call $$B.chain$$ _the selected chain_ or just _the chain_. Blocks on this chain are called _included_, and the remaining blocks are called _orphans_.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

## Breaking Ties

When we describe chain selection rules, we will omit one part: handling ties. Consider a situation like this:

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>



Which tip should the chain rule prefer, $$A$$ or $$B$$? For most of the chain rules we will see, there is no _natural_ way to choose between them. For example, we did not introduce the longest chain rule yet, but it does not require a wild stretch of the imagination to get convinced that the selected chains of $$A$$ and $$B$$ have the same length. So how should this tie be broken?

Like pretty much everything that has anything to do with block chains, choosing a tie-breaking rule might seem innocuous, but in practice, a bad tie-breaking rule could have adverse consequences. For example, if the rule is "gameable" (that is, there are ways for large miners to increase their probability of winning the tie break), it would exacerbate an already existing problem called [selfish mining](selfish-mining.md), that we will describe later in this chapter.

A crucial observation is that the tie breaking rule itself _doesn't have to be in consensus_. The situation where miners "split" to mine over different blocks is unavoidable. For example, in the situation depicted in the illustration above, there will plausibly be some nodes that heard of $$A$$ first and other nodes that heard of $$B$$ first. **It is impossible to avoid these situations**. The chain rule provides us with a consensus of the winner _after the tie has been broken_. But who shall this winner be? That's anybody's game.

Note that this also means that tie-breaking rules _cannot be enforced_. There is no way to force a miner to mine over block $$A$$ when they "should have" mined over block $$B$$. The best we can do is suggest a tie-breaking rule that is aligned with the miners' incentives.&#x20;

In Bitcoin, miners mine over the block they _observed first_. This makes sense from the miner's point of view: if $$A$$ arrived at some miner earlier than $$B$$, then it is plausible that, in general, $$A$$ was created earlier or by a more well connected miner. That's good ground to assume most of the network mines over $$A$$, increasing the probability that $$B$$ is forked away, making mining over it a wasted effort.

{% hint style="info" %}
Tie breaking rules make appearances in other surprising contexts. For example, in his 2018 paper [On the insecurity of quantum Bitcoin mining](https://arxiv.org/abs/1804.08118), Or Sattath (full disclosure: one my Ph.D co-adviser) noted that the presence of quantum miners gives rise to an optimal strategy called _aggressive mining_, that increases the fork rates. They propose a modification of the tie-breaking rule as a countermeasure.
{% endhint %}

## The Block Chain Paradigm (at last)

The block chain paradigm translate a given chain selection rule to the following protocol:

* Miners mine **above the selected tip**
* Whenever a miner discovers a new block (by mining it or from a peer), they send it to their peers and **recompute the selected tip**

## Block Validity

One thing that might seem missing is the issue of block _validity_. Some blocks could break the protocol rules and should be ignored. The block chain paradigm assumes the validity of each block _has been verified_. Actually verifying the validity of a block is more involved than it might seem. However, that is less of a consensus problem and more of a data layer problem, given that the validity rule for a block typically also depends on the data therein (e.g. not including invalid transactions).

When we explain GHOSTDAG protocol, we will discuss how block validity is handled therein. But for now, presenting a general theory of validation will stray too far from the purposes of this book.



