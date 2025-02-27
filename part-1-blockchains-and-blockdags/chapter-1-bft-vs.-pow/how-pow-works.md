# How PoW Works\*

At the basis of PoW we have a _hash function_. You know, that proverbial "complex mathematical puzzle" that miners are trying to solve? Well, first of all, it is _not_ a "complex mathematical puzzle" in any way, it is actually the _dumbest_ possible puzzle, a puzzle that can only be solved by _trying all possible solutions one by one until we hit one that works_. In other words, puzzles that can only be solved with _brute-force_. Crucially, it is _very complex_ to _create_ such a dumb puzzle. Such a puzzle is expected to outsmart all attempts to cleverly solve it faster than a brute-force approach would. But the puzzle itself, the task given to miner, is _very dumb by design_, a magic 8-ball you keep shaking until you get the desired output. And that's a _good thing_. You _do not want_ miners to get clever and find all sorts of shortcuts that will undermine the security. The entire point of proof-of-_**work**_ is that _it proves_ that _you did the work_. If there are clever ways to shirk the work and still obtain the proof, then your [Sybil resistance](proof-of-work.md#sybil-resistance) is broken.

## Hashes as Random Oracles

As I said, it takes seriously smart people to design a sufficiently dumb "puzzle", so I will treat the problem like a cryptographer and assume someone already solved the problem for me. More formally, I will assume the existence of a [random oracle](../../supplementary-material/computer-science/page-3/random-oracles.md): a magic _random function_ that floats in the sky, accessible to anyone.

I survey random oracles to some extent in [the appendix](../../supplementary-material/computer-science/page-3/random-oracles.md), but all you should know about it is that it transforms arbitrary strings (such as block headers) into strings of 256 bits, such that if you input a _new_ string, you get a _completely random_ output, but if you input the same string again, you will get the _same_ output. Crucially, the output is _completely different_ no matter how similar the inputs are. Changing the input by as much as a _single bit_ will provide outputs that seem _completely uncorrelated_.

Random oracles obviously do not exist in reality, but we can create things that are pretty darn close. _Hash functions_ attempt to do just that. We compartmentalize the details of _designing_ a hash function by _modeling it as a random oracle_ and ignoring the details of how this is actually achieved (or rather, delegating them to hash designers).

To simplify our notations, we call the has function $$\mathsf{H}$$ and let $$\mathsf{H}(s)$$ denote the string it outputs on input $$s$$. Further, we will _not_ think of $$\mathsf{H}(s)$$ as a bit string, but as an integer $$0\le \mathsf{H}(s) < N$$, where $$N$$ is usually some power of two, most commonly (at least in the context of proof-of-work mining), $$N = 2^{256} \approx 1.158 \times 10^{77}$$. If you want to get some bearing on how hyper-astronomically huge this number is, check out this cool video:

{% embed url="https://www.youtube.com/watch?v=S9JGmA5_unY" %}

To use a hash function for PoW we need one additional assumption beyond the random oracle model: that making a query _takes time_. That there is some $$t$$ that could be very small such that the random oracle answers _exactly one query per_ $$t$$ _seconds_. Why can we assume such a $$t$$ exist? Because computation is not free. Why can we assume that _we know it_ or that it _remains fixed_? That's a more difficult question (pun not-intended), so read through if you want to know.

## The "Dumb" Puzzle

As a warmup, consider the following problem: I choose an arbitrary number $$0\le n < 2^{256}$$ and ask you to find _any_ string $$s$$ such that $$\mathsf{H}(s) = n$$. This problem is called _finding a preimage_ or _reverting the hash._ How would you go about trying? One way would be to try feed $$\mathsf{H}$$ different string until you hit one that suits the bill. Is there a better way? Apparently (if we regard $$\mathsf{H}$$ as a random oracle) the answer is _no_. Proving this requires a bit of finesse but the intuition is clear: say I choose some string $$s'$$ and note that $$\mathsf{H}(s') \ne n$$, what can I learn from this? Well, I can learn that $$s'$$ is not a solution, but can I learn anything else? Not really. Since for any $$s'' \ne s'$$ we have that the outputs $$\mathsf{H}(s')$$ and $$\mathsf{H}(s'')$$ are _random_ and _uncorrelated_, it follows that $$\mathsf{H}(s')$$ tells us _nothing_ about $$\mathsf{H}(s'')$$. In other words, the output $$\mathsf{H}(s')$$ is the _only_ thing we learn, and that information is almost useless for finding a solution.

{% hint style="info" %}
There are stronger security properties that are actually crucial. One of them is _collision resistance_: the inability to find two inputs $$s\ne s'$$ such that $$\mathsf{H}(s) = \mathsf{H}(s')$$. This is the property commonly required of hash functions (though for some applications even _that_ is not enough). Collision resistance is actually _stronger_ than _reverting resistance_. That is, we can conceive a hash that is preimage resistant but not collision resistant, but not the other way around.

Random oracles _are_ collision resistant, and hashes used for proof-of-work are also believed to be collision resistant. However, there are _even stronger_ properties that hold for random oracles, but we _know_ do not hold for some of the known hash functions. For that reason, extreme caution should be exercised when analyzing a hash as a random oracle.

See the exercises for more details.
{% endhint %}

So if we accept that spamming inputs is the only way to solve the problem, how long should it take Each such attempt has a probability of one in $$N$$ to succeed, so it will take (on average) $$t\cdot N$$ seconds to find a solution. For $$N=2^{256}$$, If $$t$$ is a trillionth of a second, this would only take just over three billion trillion trillion trillion trillion years. Grab a coffee while you wait.

That's a bit too long to wait, so let us make the problem easier: I choose _two_ arbitrary numbers $$n_1$$ and $$n_2$$ and ask you to find $$s$$ such that _either_ $$\mathsf{H}(s)=n_1$$ or $$\mathsf{H}(s) = n_2$$? The logic above still convinces us that a brute-force approach is the only approach. But now, each attempt has _twice_ the probability to be successful, cutting our total running time in half, to $$t\cdot N / 2$$.

Now, since the random oracle is, well, random, it doesn't really matter what $$n$$ was in the first problem, we can just replace it with $$0$$. And in the second problem, we could have similarly replaced $$n_1$$ and $$n_2$$ by $$0$$ and $$1$$. More generally, we can choose some number $$T$$, called the _difficulty target_, and ask you to find an input $$s$$ such that $$\mathsf{H}(s) < T$$. The two examples we've seen are the special cases where the target is set to $$T=1$$ or $$T=2$$ respectively. The number $$T$$ is exactly the number of outputs that are considered _small enough_.

For a general $$T$$, how long will we wait? Well, whenever we input a fresh string $$s$$, there are $$N$$ possible, equally likely values for $$\mathsf{H}(s)$$, and exactly $$T$$ of them are sufficiently small. So an attempt will succeed $$T$$ out of $$N$$ times, meaning that we will need around $$T/N$$ attempts, taking a total of $$t\cdot N / T$$ seconds. So if we want that, on average, it would take $$\lambda$$ seconds to solve the puzzle, we want to choose $$T$$ such that $$t\cdot N/T = \lambda$$, or after rearranging:

$$
T = t\cdot N/ \lambda
$$

For example, say that $$t$$ is one trillionth of a second, $$t=10^{-12}$$, and that we want to have a block delay of ten minutes, so $$\lambda = 600$$ (because we are working in units of seconds), then we get that the appropriate difficulty target is $$T=10^{-12}\cdot2^{256}/600\approx1.876\times2^{206}$$.

{% hint style="info" %}
You might be concerned that the solution is generally (and actually quite rarely) not an integer. This concern is much more valid than most people expect. Yes, rounding is obviously the solutions, but how do you prevent assure implementations — or even the same implementation running on different architectures — all handle rounding errors _exactly_ the same way? Even a slight incompatibility, including a round-off bug in a future popular CPU, can split the network and cause vicious complications. For that reason, cryptocurrencies apply measures that assure that roundoff error handling remains uniform and platform-independent.
{% endhint %}

One peculiarity with the difficulty target is that _lower targets make for harder blocks_. This makes sense, as hitting a number below one million is harder than hitting a number below one trillion. But we have to be mindful of this if we ever want to use $$T$$ as a measure of _how hard_ solving the puzzle was. For that reason, it is common to define _the difficulty_ of the puzzle as $$1/T$$, so we would have that _the higher the difficulty the harder the puzzle_. The meaning of the terms _difficulty_ and _difficulty target_ is actually inverse, and that these terms are commonly used interchangeably does not help resolve the confusion. The best thing I can give you is that whenever anyone uses the terms _difficulty_ or _target_ in a context where these details are important, be sure to ask them explicitly what they mean.

OK, so now we have a grasp of how to construct "dumb puzzles", does this solve all our problems? Just require a miner to find some $$s$$ such that $$\mathsf{H}(s)$$ is sufficiently small knowing this would delay them? Well, obviously not. If the puzzle is just to find some $$s$$ such that $$\mathsf{H}(s) < T$$ then a miner could just find this number _once_, and use it as "proof" for all their future blocks.

The solution is to change the puzzle such that the string $$s$$ _depends on the block_. That's why we say that the miner is mining _a block_. The idea is this: we can separate a block into two parts, header and data. The header of the block contains a few fields of information that depend on the data therein, and an important field called _nonce_ that can be _any number_. Why is the nonce field there? Because that's exactly what the miner changes in order to get a sufficiently low hash. The nonce is where there is _room to brute-force_.

Let $$B$$ be a block header without the nonce set, and let $$B[n]$$ be that same block with the nonce set to $$n$$, When a miner mines for the block $$B$$, what they actually do is compute $$\mathsf{H}(B[n])$$ for different values of $$n$$ until they find $$n$$ for which $$\mathsf{H}(B[n]) < T$$.

So are we done? Not quite. The remaining question is: if we only hash the _header_, what does this say about the data? Can't we just change the data, and use the header as proof? The "obvious" solution is to hash the entire block instead of just the header, but that will make mining much harder and more centralized. Instead, Satoshi had the foresight to use a cool construction called [Merkle trees](../../supplementary-material/computer-science/page-3/merkle-trees.md). For our current purposes you don't have to understand exactly what a Merkle tree is (though you should, because they are _very useful_ and _very cool_), only that it allows us to take an _arbitrary amount of information_ and extract from it a 256 bit string called the _Merkle root_ such that it is impossible to manipulate the data without changing the Merkle root. By including the Merkle root in the block header, we get that the string $$B[n]$$ affirms the veracity of the block contents as well.

## Difficulty Adjustment

So recall we said there is this number $$t$$ that measures how long a single hash takes? This is clearly not a realistic assumption. The number $$t$$ represents, in a sense, how _hard_ it is to compute $$\mathsf{H}$$, and the _global amount of effort_ dedicated for solving the puzzle. The value of $$t$$ is not fixed, but time dependent. If the miner replaces their machine by a faster one, $$t$$ has decreased. If another miner joins the party, $$t$$ has increased. If a miner went out of business or had a long-term technical failure, $$t$$ has _decreased_. This value of $$t$$ that we so arrogantly took for obvious, not only does it depend on information that is not available to us, but it isn't even _fixed_.

In practice, instead of pretending we know $$t$$, we try to estimate it from the information we _do_ have: the blocks themselves. If we see that blocks are created too fast or too slow, we try to adjust $$t$$ appropriately.

As the person who designed Kaspa's difficulty adjustment, I can give you a first-hand testimony that designing difficulty adjustment is very intricate. There are many approaches to choosing how to _sample_ the blocks, and then a plethora of methods to evaluating the difficulty from the data. Later in the book I will describe Kaspa's difficulty adjustment algorithm, and maybe also provide a general discussion of the more common difficulty adjustment approaches (material that is unfortunately not covered in _any_ textbook or other unified source). For now, the task of understanding how difficulty adjustment works in Bitcoin is daunting enough.

## Difficulty Adjustment in Bitcoin

Bitcoin was naturally the first crypto to suggest _any_ form of difficulty adjustment. Up to a few tweaks, it was lifted almost verbatim from thee original Bitcoin whitepaper, that was published before cryptocurrencies even existed. Many argue that Bitcoin's approach to difficulty adjustment is outdated, has many drawbacks, and can even be dangerous, while others argue that it is time tested and its simplicity protects us from unexpected attacks. So basically, a carbon copy of _any_ _other argument_ about Bitcoin.

Bitcoin uses a _fixed difficulty window_. A difficulty _epoch_ (or window) lasts $$N=2016$$ blocks (or approximately two weeks), and at the end of each epoch, the difficulty is adjusted according to the blocks within that epoch.

{% hint style="info" %}
The contrast is a _sliding window_ approach, where the difficulty is recalculated for _each block_ according to the $$N$$ blocks that preceded it.

The advantage of a sliding window approach is that it is _more responsive_, making the network quickly react to changes in the global hashing power instead of waiting for the end of the epoch. Note that in cataclysmic circumstances, waiting for the _end of the epoch_ could be a very long time, as less mining means longer block delays, delaying the end of the epoch, and possibly causing more miners to jettison, making the end of the epoch even more distant. I know of exactly one example of a small chain that had more than 80% of its mining on a single private pool. One day the pool decided to switch to another coin, making the hashrate instantly drop to 20%, increasing the block delays five times over. Displeased miners hoppeed as well, dropping the hashrate to about 1% of what it was, effectively halting the chain. I do not remember the exact parameters, but for a two weeks long difficulty epoch, the network would have to wait _literal years_ for such a drop to be properly adjusted. The stall was resolved by a hard fork, manually increasing the target, while implementing a sliding window difficulty adjustment.

The disadvantage of a sliding window approach is that it is, well, _more responsive_. Responsiveness is a double-edged sword, and a difficulty adjustment mechanism that is too sensitive can be abused to manipulate the network in ways that the robust difficulty epoch would not allow. Moreover, sliding windows are naturally more complex, and have complicated and not completely understood dynamics, such as _difficulty fluctuations_ that create new vectors for opportunistic mining, further disrupting block creation rate consistency.

TODO: find references, I remember a series of posts explaining this problem in BCH or BSV's attempts to use ASERT and in [Zawy's posts](https://github.com/zawy12/difficulty-algorithms/issues), I need to dig for it
{% endhint %}

So given a window of $$N$$ blocks, and the times they were created, how do we adjust $$T$$? Quite simply, compare how long it was _supposed_ to take, compare it to how long it _actually_ took, and adjust $$T$$ accordingly.

If the block delay is $$\lambda$$, then the expected length of an epoch is $$\lambda \cdot N$$. For example, in Bitcoin we have $$\lambda = 10\text{ min}$$ and $$N=2016$$ so the length of a difficulty epoch is expected to be $$20160$$ minutes which are exactly two weeks.

A comfortable way to state what we _observed_ is by using the ratio $$\alpha$$ satisfying that thee epoch lasted $$\alpha \cdot \lambda \cdot N$$. For example, $$\alpha = 1/2$$ means the epoch was twice shorter than expected, while $$\alpha = 2$$ means it was twice longer. For reasonably large values of $$N$$ ($$2016$$ is more than enough) we can deduce from this that the _average_ block time is extremely close to $$\alpha\cdot \lambda$$ (to understand why using a small $$N$$ is too noisy, you can review the discussion on [the math of block creation](../../supplementary-material/math/probability-theory/the-math-of-block-creation.md)). We want to adjust this back to the desired $$\lambda$$, which means that we want to make mining $$1/\alpha$$ times harder: if $$\alpha = 1/2$$ we want to make it twice harder, if $$\alpha=2$$ we want to make it twice _easier_.

Recall that a _larger target_ means _easier blocks_, so to make mining $$1/\alpha$$ times harder, we choose $$\alpha\cdot T$$ as our new difficulty.

## Handling Timestamps

This is all good and well, but there is one problem: how do we _know_ how long it took to create the $$N$$ blocks?

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
One might be curious why we "look for the earliest and latest timestamps" and not just take the first and last one. The answer is that timestamps in Bitcoin are not always monotone. In fact, there are known examples of later blocks with earlier timestamps. Amusingly enough, I found that out from a 2014 paper that quotes a paper from 2015.
{% endhint %}

