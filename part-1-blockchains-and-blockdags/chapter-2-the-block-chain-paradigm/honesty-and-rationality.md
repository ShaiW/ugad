# Honesty and Rationality

The idiom "honest majority" is extremely common when discussing PoW. The sufficiency of an honest majority (opposed to a privileged majority of two thirds) is considered one of the greatest accomplishments of Nakamoto consensus. Another tenet of the Bitcoin ethos is _trustlessness_: the property that all sides of all interactions do not have to trust the other side to follow suit. That the protocol protects them from malfeasance.

Ostensibly, there is quite a tension between the two: how can a system be _trustless_ if its security hinges on an honest majority we _trust_ to exist?

The answer is that _we don't_. The term _honest_ might be a bit misleading. In the world of protocol design and algorithmic game theory, labeling a player _honest_ is not supposed to be a testimony to the pureness of their intention, it just means that they behave _the way we expect_. We assume honesty because it makes analysis easier. But it _does_ require us to further explain _why_ we should assume miners (or other types of players) would be compelled to follow behavior we branded as "honest".

Miner behavior _cannot_ be enforced by the protocol. If we could _assure_ that miners are "honest" we would not have needed to make assumptions. The next best thing is to _encourage_ miners to behave as we need them to through _incentive alignments_. That is, we make it _worthwhile_ for a miner to behave.

This, in a nutshell, is the underlying theme of the subset of _algorithmic game theory_ called _mechanism design_, a beautiful field of research concerned with constructing protocols in a way that encourages participants to behave as the protocol requires.

But what _is_ an incentive? How do we quantify and reason about it? A more in depth discussion will be deferred far down the line, when we analyze a blockDAG's fee market (and until that is completed, you can enjoy [this post](https://kasmedia.com/article/three-woes) instead). But a cursory explanation is that we assume there is some _utility_ that a so-called _rational_ miner will want to maximize. For example, we can assume that a Bitcoin miner wants to maximize the amount of coin they gain from mining. A miner that conforms to this utility (in a sense that will be made more formal when time is due) is called _rational_.

Now, much like "honest", the name _rational_ is also not a judgement call. It's not that miners that are not rational are crazy, or stupid, or irrational in any other colloquial interpretation of the term. It just means that _their utility is different_. A miner that stops mining because they went into debt and are better off selling their equipment is not maximizing the utility and is therefore "not rational", despite selling her gear to cover her debts is arguably the more (colloquially) rational approach, and that's hardly the only example of a miner who has something to gain by _deviating_ from the protocol.

However, if the incentive is strong enough, assuming that a majority of miners are rational (in the sense that they work toward obtaining as much coin as possible) is a very reasonable assumption. So it remains to explain why the rational behavior coincides with our idea of honesty.

## Incentive Alignment in Block Chains

When we defined the [block chain paradigm](the-paradigm.md#the-block-chain-paradigm-at-last), we only had two expectations honest of miners:

* Always mine over the **selected tip**
* When discovering a new block (via mining or a peer) **immediately transmit it and recompute the selected tip**

But now we want _rational_ miners to follow suit. How shall we achieve that? Bitcoin offers a simple and powerful: **pay miners for creating blocks,** as long as the block is **within the selected chain**. The said payment has two components: block rewards, and fees.

Block rewards serve two functions: increase supply in a _gradual manner_ (which is why the coins created by block rewards are often called _emissions_), and incentivize miners to behave even when fees are negligible.

Most PoW coins are _deflationary_, which means that the _total emission_ _must be finite_, making fees the dominant, and finally only, incentive for good behavior in the long run. Hence, the fee market and the incentives it poses are obviously _crucial_ for the long run of a deflationary proof of work. For that reason, we defer the discussion to an entire part of the book dedicated to fee market dynamics. For now, however, we treat block rewards as _fixed_.

{% hint style="info" %}
For a review and analysis of Bitcoin's emission schedule, see the [appendix](../../supplementary-material/math/stuff-you-should-know/geometric-series.md)
{% endhint %}

So, how do block rewards affect rational miners?

One can prove that for Bitcoin's heaviest chain rule (that we did not introduce yet), not mining over the selected tip reduces the chance of creating the next block, and thus the expected gain. In essence, this is true since by not mining over the currently heaviest known chain, the miner competes _against it_ from a disadvantage.

OK, but what about withholding blocks? Surely a rational miner would want to avoid that too, right? Well, not quite. We will soon discuss a phenomenon called [selfish mining](selfish-mining-in-bitcoin.md), which shows that there is in fact _some_ divergence between the rational and honest strategies for bitcoin miners. But before that, we need to familiarize ourselves with some chain selection rules.
