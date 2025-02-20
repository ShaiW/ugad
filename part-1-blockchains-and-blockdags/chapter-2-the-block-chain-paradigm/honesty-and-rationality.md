# Honesty and Rationality

The idiom "honest majority" is extremely common when discussing PoW. The sufficiency of an honest majority (opposed to a privileged majority of two thirds) is considered one of the greatest accomplishments of Nakamoto consensus. Another tenet of the Bitcoin ethos is _trustlessness_: the property that all sides of all interactions do not have to trust the other side to follow suit. That the protocol protects them from malfeasance.

Ostensibly, there is quite a tension between the two: how can a system be _trustless_ if its security hinges on an honest majority we _trust_ to exist?

The answer is that _we don't_. The term _honest_ might be a bit misleading. In the world of protocol design and algorithmic game theory, labeling a player _honest_ is not supposed to be a testimony to the pureness of their intention, it just means that they behave _the way we expect_. We assume honesty because it makes analysis easier. But it _does_ require us to further explain _why_ we should assume miners (or other types of players) would be compelled towards the behavior we branded as "honest".

Miner behavior _cannot_ be enforced by the protocol. If we could _assure_ that miners are "honest" we would not need to even make assumptions. The next best thing is to _encourage_ miners to behave as we need them to through _incentive alignments_. That is, we make it _worthwhile_ for a miner to be "honest".

This, in a nutshell, is the underlying theme of the subset of _algorithmic game theory_ called _mechanism design_, a beautiful field of research concerned with constructing algorithms in a way that encourages participants to behave as the protocol requires.

But what _is_ an incentive? How do we quantify and reason about it? A more in depth discussion will be deferred to far down the line, when we analyze a blockDAG's fee market (and until that is completed, you can enjoy [this post](https://kasmedia.com/article/three-woes) instead). But a cursory explanation is that we assume there is some _utility_ that a so-called _rational_ miner will want to maximize. For example, we can assume that a Bitcoin miner wants to maximize the amount of coin they gain from mining. A miner that conforms to this utility (in a sense that will be made more formal when time is due) is called _rational_.

Now, much like "honest", the name _rational_ is also not a judgement call. It's not that miners that are rational are crazy, or stupid, or irrational in any other way. It just means that _their utility is different_. A miner that stops mining because they went into debt and are better off selling their equipment is not maximizing the utility and is therefore "not rational", despite selling her gear to cover her debts is arguably the more rational approach, and that's hardly the only example of a miner who has something to gain by _deviating_ from the protocol.

However, if the incentive is strong enough, assuming that a majority of miners are rational (in the sense that they work toward obtaining as much coin as possible) is a very reasonable assumption. So it remains to explain why the rational behavior coincides with our idea of honesty.

## Incentive Alignment in Block Chains

When we defined the [block chain paradigm](the-paradigm.md#the-block-chain-paradigm-at-last), we only had two expectations honest of miners:

* Always mine over the **selected tip**
* When discovering a new block (via mining or a peer) **immediately transmit it and recompute the selected tip**

But now we want _rational_ miners to follow suit. How shall we achieve that? Well, Bitcoin's solution is simple: **pay miners for creating blocks,** as long as the block is **within the selected chain**. The said payment has two components: block rewards, and fees.

Block rewards serve functions: increase supply in a _gradual manner_ (which is why the coins created by block rewards are often called _emissions_), and incentivize miners to behave even when fees are negligible.

Most PoW coins are _deflationary_, which means that the _total emission_ _must be finite_, making fees the dominant incentive for good behavior. Hence, making the fees the only security subsidy in the long run. The fee market and the incentives it poses is obviously _crucial_ for the long run of a deflationary proof of work. For that reason, we defer the discussion to an entire part of the book dedicated to fee market dynamics. For now, however, we treat block rewards as something _fixed_.

{% hint style="info" %}
The way deflation is usually implemented is by _geometric emission_. That is, every _fixed_ period of time (measured in number of blocks), the reward is decreased by a fixed _ratio_. If the initial reward is $$R$$, and it is decreased by a factor of $$q$$ once every $$N$$ blocks, then we get that the first $$N$$ blocks provide a reward of $$R$$ each, the next $$N$$ blocks provide $$R\cdot q$$ each, the next $$N$$ blocks provide $$R\cdot q^2$$ each and so on. Using the formula for a [geometric series](../../supplementary-material/math/stuff-you-should-know/geometric-series.md), we get that the total emissions sum to

$$\sum\limits _{n=0}^{\infty}N\cdot R\cdot q^{n}=\frac{N\cdot R}{1-q}$$

In Bitcoin, the initial block reward was $$100$$ bitcoin, and it is reduced by half once every $$210,000$$ blocks providing a total emission of

$$\frac{210,000\cdot50}{1-\frac{1}{2}}=21,000,000$$

Clearly it is not truly the case that emission resume forever, becoming infinitely smaller, as they will eventually become smaller than a single satoshi. In the exercise you will see how cutting off the tail affects the total emission.
{% endhint %}

So, how do block rewards affect rational miners?

One can prove that for Bitcoins heaviest chain rule (that we did not introduce yet), not mining over the selected tip reduces the chance of creating the next block, and thus the expected gain. This is true essentially since by not mining over the currently heaviest known chain, the miner competes against it from a disadvantage.

OK, but what about not withholding blocks? Surely a rational miner would want to avoid that too, right? Well, not quite. We will soon discuss a phenomenon called [selfish mining](selfish-mining-in-bitcoin.md), which shows that there is in fact _some_ divergence between the rational and honest strategies for bitcoin miners.
