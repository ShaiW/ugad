# Difficulty Adjustment

In the previous section, we defined the quantity $$t$$ as the expected number of seconds between two consecutive hashes. In this section, we complete the picture by introducing the notion of a difficulty adjustment algorithm (DAA) — the mechanism responsible for estimating $$t$$. As the name "adjustment" implies, $$t$$ is not a constant value, but rather changes over time as mining hardware is activated or deactivated. The DAA has to balance being sensitive enough to respond to organic changes in hashrate while remaining robust enough to perform as expected in unexpected scenarios, including malicious interference.

The difficulty affects which blocks are valid, and thus must be determined in consensus and agreed upon by all. The only consensus data we have that connects blocks to real-world time is their _timestamps_. Unfortunately, timestamps cannot be authenticated. There is nothing stopping miners from reporting false values. One aspect of designing a DAA is providing sufficient protection against timestamp tampering.

In this section, we first provide a _naive_ version of Bitcoin's DAA, that works perfectly _assuming all clocks are synchronized and all timestamps are authentic_. After that, we will explain how Bitcoin handles timestamp tampering threats.

The quantity $$t$$ is a bit conceptually awkward. We used it in the previous section because it was more comfortable for the math. In the current context, it makes more sense to consider the global hashrate $$R$$, which is simply thhe reciprocal: $$R=1/t$$. As $$t$$ is measured in seconds per hash, $$R$$ is measured in hashes per second.

## Fixed Vs. Sliding Windows\*

The core computation of a DAA is estimating $$R$$ from pervious blocks. This calculation is called a _difficulty aggregation heuristic_. There are many ways to do difficulty aggregation, but before we can do that, we need to consider how we choose what blocks to aggregate and when. There are two competing approaches for that.

The original, conceptually simpler approach is _fixed windows_. In fixed windows, the chain is divided into fixed-length successions called _difficulty epochs_. When a new epoch starts, a new difficulty target is computed by aggregating the difficulty of the blocks in the previous epochs. The _window_ is the set of blocks used for aggregating difficulty. Bitcoin uses a fixed-window DAA with an epoch length of 2016 blocks, or approximately 2 weeks, and the window is simply the epoch that just ended.  There are a few variations of fixed windows — such as using different heuristics, epoch lengths, and window lengths — but they all follow the same principles. We will learn these principles shortly when case-studying Bitcoin's DAA.

Bitcoin, and other fixed window DAAs, use _extremal difference_ to aggregate difficulty. This is arguably the simplest imaginable mode of aggregation: compare the earliest and latest timestamps in the window to the expected window length.

As the landscape of cryptocurrency evolved, the limitations of fixed windows became apparent. The relatively long windows make responses to sharp difficulty shifts unreasonably slow. For example, imagine the global hashrate of Bitcoin instantly dropping by 90% just after an epoch started. How long will it take the difficulty to readjust? If your answer is "two weeks," you have not been paying attention. The length of the epoch is 2016, which is _supposed to approximate_ two weeks, and the quality of this approximation is directly proportional to the quality of approximation of $$t$$. Since the global hashrate $$R$$ dropped by 90%, it is now _ten times smaller_ than our approximation, making $$t$$ ten times too big. Consequently, until the difficulty target is adjusted, it is ten times smaller than needed, making the average block time _one hundred minutes_, and the entire epoch length _20 weeks_.

Such scenarios are far-fetched for an established coin like Bitcoin, but pose a genuine concern to younger, less mined proof-of-work chains. There are recorded instances of a large mining pool leaving a small project, causing its block times to skyrocket. When happening in reality, the situation naturally spirals as other miners also turn off their rigs until difficulty increases, grinding the chain to a halt as block times stretch to weeks and difficulty epochs to years. At this point, the only remaining solution is to manually readjust the difficulty via a hard-fork.

To increase reactivity, shorter difficulty windows were considered. But there were good reasons to have long difficulty windows to begin with. With shorter windows, it becomes easier to exploit the DAA to manipulate the system. There is a sharp reactiveness-robustness tradeoff that does not seem to have a sweet spot appropriate to upcoming chains with a low global hashrate.

To obtain better trade-offs, protocol designers naturally turned to _sliding windows_. In this approach, there are no difficulty epochs. The difficulty is recalculated for each block based on a window of blocks preceding it. This allowed for maintaining a large window while responding quickly.

Initially, sliding windows used the same simple aggregation method used in Bitcoin. We will describe it in full soon, but the idea is to compare the expected length of the epoch with the difference between the earliest and latest timestamp therein to approximate how off the difficulty is.

This approach works well for chains with high block rates, like Kaspa, where it is adopted. But this is because such networks have the privilege of accumulating thousands of samples within a few hours. For more typical proof-of-work chains, where block delays are minutes long, this approach is inappropriate.&#x20;

The reasons for that are subtle, and I cannot fully explain them here. But I can shed some light with a nice analogy. This is a variation of an analogy I heard from the pseudonymous Zawy12, a thought leader in the DAA world, whose notes (given as a [list of GitHub issues](https://github.com/zawy12/difficulty-algorithms/issues), in a true cypherpunk fashion) are practically the only technical source on sliding windows. Imagine the difficulty in sliding windows to a long and thin rod, made of a slightly elastic metal. The rod is fixed to the floor at the bottom and has a target at its top. Changing the difficulty is like hitting the target. It causes the top of the rod to oscillate back and forth. The oscillations are dampened by the bottom of the rod being fixed to the floor, but typically, this force is too minimal compared to natural fluctuations in hashrate.

This analogy demonstrates that a sliding window DAA has a sort of natural _inner frequency_. And this is actually what we observe in reality. For example, consider the Kaspa difficulty curve:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>Screenshot from <a href="https://2miners.com/kas-network-hashrate">2miners.com</a></p></figcaption></figure>

We can see fluctuations of around 6-8% in the global hashrate with a consistent frequency of about once oscillation per 40 minutes, which is (by no pure chance) about the window length of Kaspa's difficulty adjustment.

Zawy further noticed that by properly timing a "switch attack" where a relatively small rig is turned on during increases and off during decreases, the oscillations can constructively interfere, causing higher spikes, deeper troughs, and much less stability. Worse yet, these switching attacks emulate what rational coin-hoppers — miners that constantly redirect their rigs to coins based on value and difficulty — would naturally do in many cases.

Unfortunately, this analogy doesn't only hint at a problem, but also at a solution. If you want to have a better-controlled oscillator, you _dampen it_. That is, you add something that _resists_ the movement, but the strength of the resistance is _proportional_ to the strength of the movement. Mechanically, this could be accomplished by using a spring that connects the target to its stationary location, like so:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

So the idea is to implement a sort of "digital spring" that will dampen the oscillations around the displacement. The problem here is that to implement such a spring directly, we need to _know_ the actual hashrate, but figuring that out is exactly why we need DAA to begin with!

Fortunately, there are ways to approximate such a spring. I will not go into the math, but the idea is that if you use the entire history of difficulty targets, but give higher weight to more recent blocks, you get a dampening effect that is somewhat similar to the simple spring dampening above.

There are two common ways to do this:

* LWMA (Linear Weighted Moving Average). The weight of each block is decreased by a _fixed amount_ compared to the previous block. The graph of influence as a function of depth becomes a linear function, hence the name. This approach is usually accredited to Zawy.\
  There are several versions of LWMA, and they are used by Litecoin, Monero, Zcash, and more.
* ASERT (Adjusted Service Rate). There are two key differences. One is that the weighting decreases _exponentially_, and the other is that the weight is not determined by the depth (in terms of block count) but the distance between timestamps.\
  ASERT is developed and used mainly by Bitcoin Cash.

There are various trade offs between the two. For example, ASERT is considered more responsive, while LWMA is considered more robust to timestamp manipulation.\




### Difficulty Adjustment in Bitcoin

Bitcoin was naturally the first crypto to suggest _any_ form of difficulty adjustment. Up to a few tweaks, it was lifted almost verbatim from the original Bitcoin whitepaper, which was published before cryptocurrencies even existed. Many argue that Bitcoin's approach to difficulty adjustment is outdated, has many drawbacks, and can even be dangerous, while others argue that it is time-tested and its simplicity protects us from unexpected attacks. So basically, a carbon copy of _any_ _other argument_ about Bitcoin.

Bitcoin uses a _fixed difficulty window_. A difficulty _epoch_ (or window) lasts $$N=2016$$ blocks (or approximately two weeks), and at the end of each epoch, the difficulty is adjusted according to the blocks within that epoch.

{% hint style="info" %}
The alternative is a _sliding window_ approach, where the difficulty is recalculated for _each block_ according to the $$N$$ blocks that preceded it.

The advantage of a sliding window approach is that it is _more responsive_, making the network quickly react to changes in the global hashing power instead of waiting for the end of the epoch. Note that in cataclysmic circumstances, waiting for the _end of the epoch_ could be a very long time, as less mining means longer block delays, delaying the end of the epoch, and possibly causing more miners to jettison, making the end of the epoch even more distant. I know of exactly one example of a small chain that had more than 80% of its mining on a single private pool. One day the pool decided to switch to another coin, making the hashrate instantly drop to 20%, increasing the block delays five times over. Displeased miners hopped as well, dropping the hashrate to about 1% of what it was, effectively halting the chain. For a two weeks long difficulty epoch, the network would have to wait _literal years_ for such a drop to be properly adjusted. The stall was resolved by a hard fork, manually increasing the target, while implementing a sliding window difficulty adjustment.

The disadvantage of a sliding window approach is that it is, well, _more responsive_. Responsiveness is a double-edged sword and a difficulty adjustment mechanism that is too sensitive can be abused to manipulate the network in ways that the robust difficulty epoch would not allow. Moreover, sliding windows are naturally more complex, and have complicated and not completely understood dynamics, such as _difficulty fluctuations_ that create new vectors for opportunistic mining, further disrupting block creation rate consistency.

TODO: find references, I remember a series of posts explaining this problem in BCH or BSV's attempts to use ASERT and in [Zawy's posts](https://github.com/zawy12/difficulty-algorithms/issues), I need to dig for it
{% endhint %}

So given a window of $$N$$ blocks, and the times they were created, how do we adjust $$T$$? Quite simply, compare how long it was _supposed_ to take with how long it _actually_ took, and adjust $$T$$ accordingly.

If the block delay is $$\lambda$$, then the expected length of an epoch is $$\lambda \cdot N$$. For example, in Bitcoin we have $$\lambda = 10\text{ min}$$ and $$N=2016$$ so the length of a difficulty epoch is expected to be $$20160$$ minutes which are exactly two weeks.

A comfortable way to state what we _observed_ is by using the ratio $$\alpha$$ satisfying that the epoch lasted $$\alpha \cdot \lambda \cdot N$$. For example, $$\alpha = 1/2$$ means the epoch was twice shorter than expected, while $$\alpha = 2$$ means it was twice longer. For reasonably large values of $$N$$ ($$2016$$ is more than enough) we can deduce from this that the _average_ block time is extremely close to $$\alpha\cdot \lambda$$ (to understand why using a small $$N$$ is too noisy, you can review the discussion on [the math of block creation](../../supplementary-material/math/probability-theory/the-math-of-block-creation.md)). We want to adjust this to the desired $$\lambda$$, which means that we want to make mining $$1/\alpha$$ times harder: if $$\alpha = 1/2$$ we want to make it twice harder, if $$\alpha=2$$ we want to make it twice _easier_.

Recall that a _larger target_ means _easier blocks_, so to make mining $$1/\alpha$$ times harder, we choose $$\alpha\cdot T$$ as our new difficulty.

### Handling Timestamps

This is all good and well, but there is one problem: how do we _know_ how long it took to create these $$N$$ blocks?

Each block includes a timestamp that allegedly reports when it was discovered, but it cannot be _authenticated_. It could be that the miner's clock is out of sync, or worse, that she is deliberately trying to interfere with the network by messing with the difficulty adjustment.

As a first line of defense, Bitcoin bounds the difficulty change to a factor of four. No matter what the timestamps look like, if the old and new targets are $$T$$ and $$T'$$, then we will always have that $$\frac{1}{4} \le \frac{T}{T'} \le 4$$. But that's obviously not enough.

So how can we avoid timestamp manipulations? If we just take the first and last timestamp of the epoch and check their difference, and the malfeasant miner happens to create the first or last block, she essentially gets a carte blanche to set the difficulty to whatever she wants. The adjustment cannot depend just on two blocks.&#x20;

One might be tempted to tackle this by analyzing the timestamps within the epoch better, and trying to isolate the "true" ones. Indeed, one can _improve_ the robustness using such approaches, but _any_ such mechanism would be exploitable if it can be fed arbitrary timestamps.

Instead, the solution is to impose smart limitations on the block's timestamp when it is _processed_.

The idea is not to allow a timestamp to be more than two hours off the mark. If we can guarantee that, then the worse an attacker can do is to compress/stretch the difficulty window by four hours, which are about $$1\%$$ of two weeks. But how can we guarantee it?

The first observation is that we can verify that a timestamp is not too deep into the _past_ from the _consensus data_. Since a block delay is ten minutes, we expect $$12$$ blocks to be created in two hours. So what should we do? Should we go 12 blocks back from the candidate block, and check that it has an earlier timestamp? That happens to be exploitable. If the miner happens to have created that block too, and the block 12 blocks before that one, and the blocks 12 blocks before that one, and so on. Each block in this chain allows _two more hours_ of manipulation. This attack might not seem practical, but if we let an adversary try it consistently, it _will_ succeed at some point, and it only requires less than $$10\%$$of the hashing power, a _far cry_ from thee honest majority security we were promised. Bitcoin overcomes this by taking the timestamps of the last $$23$$ blocks, and picking the _median_. This is a great idea because highly irregular timestamps will not be the median, but in the fringes.

So say we want to enforce a timestamp deviation of at most $$N$$ block delays. Say that the block $$B$$ has a timestamp $$S$$, and let $$M$$ be the _median_ timestamp of the $$2N-1$$ blocks preceding it, the we have the following rule

**Rule I**: if $$S<M$$ then the block is invalid.

OK, but what about blocks whose timestamp deviates into the future? Now we don't have any blocks to rely on. We can't have the validity of a block depend on the blocks succeeding it!

The idea is to use the _actual system clock_. Let $$C$$ be the system clock while validating the block. The length of time $$S-M$$ is an approximation of $$N$$ _actual_ block delays (that is, according to the current, possibly inaccurate difficulty target). We would like to invalidate blocks whose timestamps are more than $$S-M$$ later than $$C$$, and it is easy to check this just means that $$M>C$$. The problem is that $$C$$, unlike $$M$$, is not in consensus, and can vary from miner to miner. If we tell miners to _invalidate_ such blocks we are practically begging for a net split. The correct solution is to _delay_ the block:

**Rule II**: if $$M>C$$, _wait_ until $$C=M$$ before including the block, if while waiting a new valid block arrived, prefer it over the delayed block.

Note that miners are incentivized to follow this validation rule, as it gives them more time to create a competing block.

This does not _prohibit_ blocks with timestamps set to the far future, but it strongly _discourages_ them by _degrading their probability to be in the chain_. The later the timestamp is, the more miners will delay mining over it, increasing the probability that the block will be orphaned.

With these two rules in place, we can finally enforce the very simple policy: at the end of an epoch, find the earliest and latest time stamps $$S, S'$$ in the epoch, set $$\alpha = \frac{S'-S}{\lambda}$$, and adjust the difficulty as explained in the previous section. The timestamp deviation rules we outlined strongly limit the ability to abuse this mechanism through timestamp manipulation.

{% hint style="info" %}
One might be curious why we look for the earliest and latest timestamps and not just take the first and last one. The answer is that timestamps in Bitcoin are not always monotone. There are known examples of later blocks with earlier timestamps. Amusingly enough, I found that out from a 2014 paper that quotes a paper from 2015.
{% endhint %}

