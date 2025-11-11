# Difficulty Adjustment\*

## Difficulty Adjustment

So remember we assumed there is this number $$t$$ that measures how long a single hash takes? This is not a realistic assumption. The number $$t$$ represents, in a sense, how _hard_ it is to compute $$\mathsf{H}$$, and the _global amount of effort_ dedicated to solving the puzzle. The value of $$t$$ is not fixed, but time dependent. If the miner replaces their machine with a faster one, $$t$$ has decreased. If another miner joins the party, $$t$$ has decreased. If a miner went out of business or had a long-term technical failure, $$t$$ has _increased_. This value of $$t$$ that we so arrogantly took for obvious, not only does it depend on information that is not available to us, but it isn't even _fixed_.

In practice, instead of pretending we know $$t$$, we try to _estimate_ it from the information we _do_ have: the blocks themselves. If we see that blocks are created too fast or too slow, we try to adjust $$t$$ appropriately.

As the person who designed Kaspa's difficulty adjustment, I can give you a first-hand testimony that designing difficulty adjustment is very intricate. There are many approaches to choosing how to _sample_ the blocks, and then a plethora of methods to evaluating the difficulty from the data. Later in the book I will describe Kaspa's difficulty adjustment algorithm, and maybe also provide a general discussion of the more common difficulty adjustment approaches (material that is unfortunately not covered in _any_ textbook or other unified source). For now, the task of understanding how difficulty adjustment works in Bitcoin is daunting enough.

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
