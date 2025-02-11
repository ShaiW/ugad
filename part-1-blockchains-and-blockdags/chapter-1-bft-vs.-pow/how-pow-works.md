# How PoW Works\*

It the basis of PoW we have a _hash function_. You know, that proverbial "complex mathematical puzzle" that miners are trying to solve? Well, first of all, it is _not_ a "complex mathematical puzzle" in any way, it is actually the _dumbest_ possible puzzle, a puzzle that can only be solved by _trying all possible solutions one by one until we hit one that works_. Grated, it is very complex to _create_ such a dumb puzzle, but the puzzle is nonetheless _very dumb_, a magic 8-ball you keep shaking until you get the desired output. And that's a _good thing_. You _do not want_ miners to get clever and find all sorts of shortcuts that will undermine your security. The entire point of proof of _work_ is that there are no ways to "clever away" the hardness of the problem.

## Hashes as Random Oracles

As I said, it takes seriously smart people to design a sufficiently dumb "puzzle", so I will treat the problem like a cryptographer and assume someone already solved the problem for me. More formally, I will assume the existence of a [random oracle](../../supplementary-material/computer-science/page-3/random-oracles.md): a magic _random function_ that floats in the sky, accessible to anyone.

I survey random oracles to some extent in [the appendix](../../supplementary-material/computer-science/page-3/random-oracles.md), but all you should know about it is that it transforms arbitrary strings (such as block headers) into strings of 256 bits, such that if you input a _new_ string, you get a _completely random_ output, but if you input the same string again, you will get the _same_ output.

Random oracles obviously do not exist in reality, but we can create things that are pretty darn close. For example, _hash functions_ try to do just that. We just _model_ the hash function as a random oracle, and ignore the implementation details.

To simplify our notations, we call the has function $$\mathsf{H}$$ and let $$\mathsf{H}(s)$$ denote the string it outputs on input $$s$$. Further, we will _not_ think of $$\mathsf{H}(s)$$ as a bit string, but just as an integer $$0\le \mathsf{H}(s) < 2^{256}$$.

To make this work, we need to make an additional assumption on our hash function: that it makes _exactly one query_ per $$T$$ seconds. However, I stress that this is an _unrealistic_ assumption, and PoW cryptocurrencies have to compensate for that by employing _difficulty adjustment_. I explain how difficulty adjustment works in Bitcoin in the next section.$$T$$ But for now, we assume that $$T$$ is fixed and known.

## The Dumb Puzzle

As a warmup, consider the following problem: I choose an arbitrary number $$0\le n < 2^{256}$$ and ask you to find _any_ string $$s$$ such that $$\mathsf{H}(s) = n$$. How would you go about trying? The only way you have to probe $$\mathsf{H}$$ on different strings until you find one that works.

Now say you have two different strings $$s$$ and $$s'$$. The numbers $$\mathsf{H}(s)$$ and $$\mathsf{H}(s')$$ were sampled _uniformly_, which means that knowing $$\mathsf{H}(s)$$ tells you _nothing_ about $$\mathsf{H}(s')$$. Consequentially, if while trying to solve the problem you query the oracle on $$s$$ and find that $$\mathsf{H}(s)\ne n$$ this actually tells you _very little_. It tells you that $$s$$ doesn't solve the problem, and _that's it_.

Each such attempt has a probability of one in $$2^{256}$$ to succeed, so it will take (on average) $$T\cdot 2^{256}$$ seconds to find a solution. If $$T$$ is a trillionth of a second, this would take just over three billion trillion trillion trillion trillion years.

Now, what if we modify the puzzle like this: I choose _two_ $$T$$arbitrary numbers $$n_1$$ and $$n_2$$ and ask you to find $$s$$ such that _either_ $$\mathsf{H}(s)=n_1$$ or $$\mathsf{H}(s) = n_2$$? Well, now the success probability is _twice as large_, so the time it takes to solve will be _half has long_, coming up at $$T\cdot 2^{256}/2 = T\cdot 2^{255}$$.

Instead of choosing some arbitrary $$n_1$$ and $$n_2$$ we could choose $$0$$ and $$1$$. So our puzzle becomes: find $$s$$ such that $$\mathsf{H}(s) < 2$$ (similarly, in the previous puzzle we could choose $$n=0$$, so the puzzle becomes to find $$s$$ such that $$\mathsf{H}(s)<2^0$$.

We can generalize this by choosing some $$t$$ and setting the puzzle to find some $$s$$ such that $$\mathsf{H}(s) < 2^{256 -t}$$. The time expected time it would take to solve now becomes $$T\cdot 2^{t}$$, and we can set $$t$$ so that this time is the interval we are looking for. For this reason $$t$$ is called the _difficulty target_.

So if $$\lambda$$ is the desired block delay, we need it to satisfy $$T\cdot 2^{t} = \lambda$$, and you are welcome to verify that $$t = \log_{2}\left(\frac{\lambda}{T}\right)$$. For example, if we assume that $$T = 10^{-12}$$ (that is, that the global hashrate is one petahash), and want a ten minutes block delay, you should set $$t = 49$$.

This expression only makes sense if $$\lambda \gg T$$, but that is usually the case: there are no networks for which $$\lambda < 1/100$$, whereas if the global hash rate is more than _one tera hash per second_ we have that $$T < 10^{-9}$$.

{% hint style="info" %}
You often hear that the difficulty target is "the expected number of leading zeros". That's because saying that $$\mathsf{H}(s) < 2^{256 -t}$$ is equivalent to saying that if we thing of $$\mathsf{H}(s)$$ as a binary string, then the $$t$$ leftmost digits are all $$0$$.

This also explains why we require  $$\mathsf{H}(s) < 2^{256 -t}$$ and not  $$\mathsf{H}(s) < 2^{t}$$: because we want a _larger_ $$t$$ to correspond to a _harder_ difficulty.
{% endhint %}

So does this solve all our problems? Not really. If the puzzle is just to find some $$s$$ such that $$\mathsf{H}(s) < 2^{256-t}$$ then a miner could just find this number _once_, and use it as "proof" for all eternity.

The solution is to change the puzzle such that the string $$s$$ _depends on the block_. That's why we say that the miner is mining _a block_. The idea is this: we can separate a block into two parts, header and data. The header of the block contains a few fields of information that depend on the data therein, and an important field called _nonce_ that can be _any number_. Why is this field there? Because that's exactly what the miner changes in order to get a sufficiently low hash.

Let $$B$$ be a block header without the nonce set, and let $$B[n]$$ be that same block with the nonce set to $$n$$, When a miner mines for the block $$B$$, what they actually do is compute $$\mathsf{H}(B[n])$$ for different values of $$n$$ until they find a value for which $$\mathsf{H}(B[n]) < 2^{256-t}$$.

So are we done? Not quite. The remaining question is, if we only hash the _header_, what does this say about the data? Can't we just change the data, and use the header as proof? The "obvious" solution is to hash the entire block instead of just the header, but that will make mining much harder and more centralized. Instead, Satoshi had the foresight to use a cool construction called [Merkle trees](../../supplementary-material/computer-science/page-3/merkle-trees.md). For our current purposes you don't have to understand exactly what a Merkle tree is, only that it allows us to take an _arbitrary amount of information_ and extract from it a 256 bit string called the _Merkle root_ such that it is impossible to manipulate the data without changing the Merkle root. By including the Merkle root in the block header, we get that the string $$B[n]$$ affirms the veracity of the block contents as well.

## Difficulty Adjustment in Bitcoin

To understand difficulty adjustment, it is instructive to understand how it s done in Bitcoin. Many argue that Bitcoin's approach to difficulty adjustment is outdated, has many drawbacks, and can even be dangerous, while others argue that it is time tested and its simplicity protects us from unexpected attacks. So essentially, a carbon copy of _any_ _other argument_ about Bitcoin.

Bitcoin's approach is to use a _fixed window_. That is, the difficulty is readjusted at fixed intervals. In Bitcoin, there is a difficulty readjustment once every $$2016$$ blocks, which are supposed to represent two weeks.

The idea is to check how long it took to create these $$2016$$ blocks, and compare it to the expected two weeks.

For simplicity, let us use notation instead. Say that we set the length of an epoch to $$N$$ blocks, then we expect the length of the window to be $$N\cdot\lambda$$ seconds, where $$\lambda$$ is the block delay. At the end of the epoch, we check how long it _actually_ took the network to create these $$N$$ blocks. Say it took $$\alpha$$ block delays, that is, $$\alpha\cdot \lambda$$ seconds. Then we get that the ratio between the desired and actual block creation speed is $$\alpha/N$$. So to adjust the difficulty, we need to change the time it takes to mine a block by a factor of $$\alpha/N$$. That is, we change the difficulty from $$T\cdot 2^{256-t}$$ to

$$
\frac{\alpha}{N}T\cdot2^{256-t}=T\cdot2^{256-t+\log_{2}\left(\alpha/N\right)}
$$

In other words, we change the difficulty target to $$t' = t+\log_2(N/\alpha)$$. Note that if $$N>\alpha$$ then blocks are created _too fast_, so we want to _increase_ the difficulty, and indeed in this case $$\log_2(N/\alpha)>0$$ and so $$t' > t$$.

{% hint style="info" %}
You might be concerned about the fact that $$\log_2(N/\alpha)$$ is not necessarily an integer. This is resolved by, instead of just "fixing' the current $$t$$, recomputing the difficulty target altogether, and rounding the result to the nearest integer. The computation concludes with some $$t$$ such that the actual block delay differs from the actual block delay by a fraction of at most $$2^{-t}$$. For example, at the time of writing, the difficulty target of Bitcoin was $$t=20$$. Consequentially, the block delay might not be _exactly_ 10 minutes, but it is within 5 _mill&#x69;_&#x73;_econds_ of 10 minutes, which is not something worth making a fuss of.
{% endhint %}

## Handling Timestamps

This is all good and well, but there is one problem: how do we _know_ how long it took to create the $$N$$ blocks?

Well, each block includes a timestamp that allegedly reports when it was discovered, but it cannot be _authenticated_. It could be that the miner's clock is out of sync, or worse, that she is deliberately trying to interfere with the network by messing with the difficulty adjustment.

As a first line of defense, Bitcoin bounds the difficulty change to a factor of four. No matter what the timestamps look like, we will always have $$|t-t'|\le 2$$. But that's obviously not enough.

How can we avoid this? If we just take the first and last timestamp of the epoch and check their difference, and the malfeasant miner happens to create the first or last block, she essentially gets a carte blanche to set the difficulty to whatever she wants. The adjustment cannot depend just on two blocks.&#x20;

One might be tempted to tackle this by analyzing the timestamps within the epoch better, and trying to isolate the "true" ones. Indeed, one can _improve_ the robustness using such approaches, but _any_ such mechanism would be exploitable if it can be fed arbitrary timestamps.

Instead, the solution is to impose smart limitations on the block's timestamp when it is _processed_.

The idea is not to allow a timestamp to be more than two hours off the mark. If we can guarantee that, then the worse an attacker can do is to compress/stretch the difficulty window by four hours, which are about $$1\%$$ of two weeks. But how can we guarantee it?

The first observation is that we can verify that a timestamp is not too deep into the _past_ from the consensus data. Since a block delay is ten minutes, we expect $$12$$ blocks to be created in two hours. So what should we do? Should we go 12 blocks back from the candidate block, and check that it has an earlier timestamp? That happens to be exploitable. If the miner happens to have created that block too, and the block 12 blocks before that one, and the blocks 12 blocks before that one, and so on. Each block in this chain allows _two more hours_ of manipulation. Bitcoin overcomes this by taking the timestamps of the last $$23$$ blocks, and picking the _median_. Highly irregular timestamps will not be the median, but in the fringes.

So say we want to enforce a timestamp deviation of at most $$N$$ block delays. Say that the block $$B$$ has a timestamp $$T$$, and let $$M$$ be the _median_ timestamp of the $$2N-1$$ blocks preceding it, the we have the following rule

**Rule I**: if $$T<M$$ then the block is invalid.

OK, but what about blocks whose timestamp deviates into the future? Now we don't have any blocks to rely on. We can't have the validity of a block depend on the blocks succeeding it!

The idea is to use the _actual system clock_. Let $$C$$ be the system clock while validating the block. The length of time $$T-M$$ is an approximation of $$N$$ _actual_ block delays (that is, according to the current, possibly inaccurate difficulty target). We would like to invalidate blocks whose timestamps are more than $$T-M$$ later than $$C$$, and it is easy to check this just means that $$M>C$$. The problem is that $$C$$, unlike $$M$$, is not in consensus, and can vary from miner to miner. The solution is to _delay_ the block:

**Rule II**: if $$M>C$$, _wait_ until $$C=M$$ before including the block, if while waiting a new valid block arrived, prefer it over the delayed block.

Note that miners are incentivized to follow this validation rule, as it gives them more time to create a competing block.

This does not _prohibit_ blocks with timestamps set to the far future, but it strongly _discourages_ them. The later the timestamp is, the more miners will delay mining over it, increasing the probability that the block will be orphaned.

With these two rules in place, we can finally enforce the very simple policy: at the end of an epoch, find the earliest and latest time stamps $$T, T'$$ in the epoch, set $$\alpha = \frac{T'-T}{\lambda}$$, and adjust the difficulty as explained in the previous section. The timestamp deviation rules we outlined strongly limit the ability to abuse this mechanism through timestamp manipulation.

{% hint style="info" %}
One might be curious why we "look for the earliest and latest timestamps" and not just take the first and last one. The answer is that timestamps in Bitcoin are not always monotone. In fact, there are know examples of later blocks with earlier timestamps. Amusingly enough, I found that out from a 2014 paper that quotes a paper from 2015.
{% endhint %}

