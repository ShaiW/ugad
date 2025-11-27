# Difficulty Adjustment

In the previous section, we defined the quantity $$t$$ as the expected number of seconds between two consecutive hashes. In this section, we complete the picture by introducing the notion of a difficulty adjustment algorithm (DAA) — the mechanism responsible for estimating $$t$$. As the name "adjustment" implies, $$t$$ is not a constant value, but rather changes over time as mining hardware is activated or deactivated. The DAA has to balance being sensitive enough to respond to organic changes in hashrate while remaining robust enough to perform as expected in unexpected scenarios, including malicious interference.

<div align="right"><figure><img src="../../.gitbook/assets/image (54).png" alt="" width="106"><figcaption><p>My gratitude to <a href="https://qu.ai/">Quai network</a> for<br>sponsoring this segment of the book</p></figcaption></figure></div>

## Difficulty Adjustment in Bitcoin

Difficulty adjustment has roughly two aspects: using timestamps to adjust the difficulty, and ensuring timestamps are sufficiently reliable.

In Bitcoin, the former is very simple. But things will become somewhat more complicated when we consider the latter.

To streamline the discussion, we introduce some notations. Let $$\lambda$$ be the desired block delay, and $$N$$ be the number of blocks per window. Let $$T^-$$ denote the difficulty target of the ending epoch. We want a formula for the difficulty of the new epoch, which we denote $$T$$.

### Difficulty Adjustment Formula

Since we expect an average block delay of $$\lambda$$, we expect that creating the entire window should take $$\lambda\cdot N$$. For example, in Bitcoin we have $$\lambda = 10 \mbox{ min}$$ and $$N = 2016$$, so we get $$\lambda\cdot N = 20160\text { min} = 2\text{ weeks}$$.

To find the difficulty adjustment factor, we compare the _expected_ epoch length with the _observed_ length: If the epoch lasted twice as long as expected, we make would the next epoch half as difficult. If it were three times shorter than expected, we would make block creation three times harder, and so on.&#x20;

Generally speaking, we defined the _observed length_ $$\Delta$$ to be the difference between the earliest and latest timestamps in the epoch, and then set:

$$
T = \left(\frac{\Delta}{\lambda\cdot N}\right)T^-\text{.}
$$

The directionality of the formula is that larger $$\Delta$$ makes larger $$T$$. This makes sense: large $$\Delta$$ means that the difficulty is too high, so we expect $$T$$ to be large as well. (Recall that a larger difficulty target $$T$$ means it is _easier_ to create blocks.)

<details>

<summary>How do we choose <span class="math">N</span> and why is it so large?*</summary>

Recall that $$\lambda\cdot N$$ is the _expected_ value. This is what we will see _on average_. In reality, we only get one sample, so we need to understand how much it could deviate from the actual value.

For $$N=1$$, the length of the entire epoch is a single block delay $$\lambda$$. Those who understand the [math of block creation](../../supplementary-material/math/probability-theory/the-math-of-block-creation.md) know that the block delay follows an _exponential distribution_ with parameter $$\lambda$$. This distribution is notoriously noisy, with a standard deviation of $$\lambda$$. This means that more than 10% of the blocks will have a delay of more than $$2\lambda$$ minutes, that is, _twice as large_ as expected. Similarly, we can show that more than 10% will take less than $$\lambda/2$$. The bottom line is that if we use $$N=1$$ as our window, more than 20% of the time our estimation will be incorrect by a factor of at least two!

To better control the tails of the distribution of the observed length $$\Delta$$, we increase $$N$$. Why? To see that, we need to recall that standard deviation represents how "fat" the tail is. The tail starting one standard deviation right of the mean is approximately $$14\%$$ of the entire probability. As we take more standard deviation, the tail becomes exponentially lighter. For example, ignoring events above _five_ standard deviations is considered the gold standard in particle physics, the most accurate experimental science in human history. The probability of observing a deviation of more than five standard deviations is approximately one in 3.5 million.

Let $$d$$ be the number of standard deviations we are willing to tolerate. When $$N=1$$, $$d$$ standard deviations is $$d\cdot \lambda$$. We know that even for $$d=1$$ (which is way to small) we get a deviation of up to $$\lambda$$, which is much too large. How can increasing $$N$$ improve the situation? Well, we know that the expected length of the window increases as $$\lambda\cdot N$$. However, the marvelous _central limit theorem_ tells us that the standard deviation only grows like $$\lambda \cdot \sqrt{N}$$!

If we assume that observations are always within $$d$$ standard deviations, we get an error of at most $$d\cdot \lambda\cdot \sqrt{N}$$. If we look at the ratio of this with the expected value $$N\cdot \lambda$$ we get that the noise can only account to a fraction of $$d\cdot \lambda \cdot \sqrt{N}/\lambda\cdot N = d/\sqrt{N}$$ of the correction. For example, for $$d=3$$ and $$N=2016$$ we get an error of about $$7\%$$.  The probability that the sample we see deviates by more than $$3$$ standard deviations is about $$0.135\%$$. All of this combines to the following conclusion:

> In Bitcoin, in $$99.87\%$$ of the epochs the observed window length reflects the true hashrate up to a mistake of $$7\%$$

We can also solve these equations the other way around, if we want to guarantee some confidence within some probability. Say, we want a mistake of at most $$1\%$$ in $$99\%$$ of the time. Note that we increased the accuracy, but decreased the confidence. Referring to a [Z-table](https://math.arizona.edu/~rsims/ma464/standardnormaltable.pdf), we find that the correct $$d$$ to ensure an error of less than $$d$$ deviations $$99\%$$ of the time is $$d=2.33$$. We want $$d/\sqrt{N}\le 1\%$$. Solving for $$N$$ we get $$N\ge (100d)^2\approx 54289$$. So this accuracy requires a window of around $$55$$ thohusand blocks. With Bitcoins $$\lambda = 10\text{ min}$$ this makes each epoch about a year long.

</details>

Let us first assume that all timestamps are _accurate_: the clocks used by all miners are perfectly synchronized, and the timestamps reported in blocks are all truthful. Under this assumption, $$\Delta$$ is always perfectly accurate and non-gameable, making the formula above the optimal difficulty estimator. (This mathematical statement is not quite trivial to prove, but it is true.)

But in the real world, we cannot make these assumptions. Most of the remaining discussion is about how to estimate $$\Delta$$ robustly without having to make this assumption.

### Limited Correction

Bitcoin introduces one more limitation, that is supposed to act as a last line of defense. It limits by how much the difficulty is allowed to change across different epoch to a factor of four. That is: $$\frac{1}{4} \le \frac{T^-}{T} \le 4$$. This provision is hardwired into the code, and _always_ applies, regardless of what was observed.

For the technicalities, see the box below for the corrected formula for $$T$$.

<details>

<summary>Bitcoin DAA Summary</summary>

Let $$\Delta$$ be the difference between the earliest and latest timestamps in the epoch.

Set:

$$\alpha_0 = \frac{\Delta}{\lambda\cdot N}$$

$$\alpha=\begin{cases} 1/4 & \alpha_{0}<1/4\\ 4 & \alpha_{0}>4\\ \alpha_{0} & \text{else} \end{cases}$$

New difficulty: $$T=\alpha\cdot T^{-}$$

</details>

### Handling Timestamps

A truly robust estimation of $$\Delta$$ strongly limit the ability to tamper with timestamps (under mild assumption, e.g. that a majority of miners use truthful timestamps). But we cannot do so too harshly. We have to restrict the ability to manipulate the DAA, while providing enough leeway to accommodate natural variance coming from network lags, clock drifts, and so on. In particular, we must always remember that _there is no_ _one universal clock_. In particular, we cannot treat timestamps recorded on the blockchain as the "true time".

{% hint style="info" %}
You might have expected this to be dealt with by coming up with fancier formulae for $$\Delta$$, that somehow aggregate all the timestamps to yield a non-gameable approximation. Unfortunately, no matter how you estimate $$\Delta$$, as long as you allow arbitrary timestamps, this approach is doomed to fail in one way or another, even assuming an honest majority. It is necessary to impose rules on what timestamps are _allowed_.

Other aggregation modes are used for different motivations, as we will see in the next section.
{% endhint %}

To get a clue how to proceed, first note a tiny detail that I snuck into the description of $$\Delta$$: it is computed from the _earliest and latest_ timestamps, _not_ _the first and last_ timestamps. Under the assumption of authentic timestamps, these two are the same. But in general, they are not.

{% hint style="info" %}
This can and does happen organically. There _are_ Bitcoin blocks that refer to blocks with a _later_ timestamp. Amusingly enough, I learned about this from a 2014 paper that refers to a 2015 paper.
{% endhint %}

For example, imagine an adversary lucky enough to create the first block of the epoch, whose timestamp is deliberately set a week into the future. The consequence: the _entire_ next epoch will be twice as difficult as it should have been. And all it took was tampering with _one_ block!

If we use the extremal timestamps, this attack won't work. The attacker's timestamp is _between_ the earliest and latest timestamps, so it does not affect difficulty. Consequently, a successful attack requires a timestamp that is either considerably earlier than the earliest honest timestamp or considerably later than the latest honest timestamp. So we need to set up a way to prohibit both possibilities. These two cases might sound symmetric, but they are actually handled very differently.

Before we can begin, we must set a tolerance for timestamp deviation. Setting it too large can enable an attack, while setting it too small can result in valid blocks being dropped, and other undesired coupling between consensus and clock synchronization.

Bitcoin set the tolerance to a comfortable 2 hours, far shorter than an epoch yet large enough to generously accommodate all reasonable organic deviations. If we can guarantee that, then the adversary can affect the difficulty by at most four hours, which is less than $$1\%$$ of the two-week-long epoch.

#### Preventing Past Timestamps

It is tempting to make the limitation simply: Do not allow a block whose timestamp is more than two hours earlier than its parent.

To see the problem, recall that we allow blocks to be two hours _too late_ as well. If the latest block happens to be two hours into the future, all blocks that are even _slightly_ into the past will be invalidated by our policy. This is another example of _losing robustness_ by giving too much credence to a _single block_. And the solution, as usual, is to aggregate many blocks.

It becomes clear that we need to consider the timestamps. The first idea might be to consider the timestamp of the block at depth $$12$$. After all, it should take about $$10$$ minutes to create each block, so $$12$$ blocks should approximate two hours. This still doesn't quite cut it, as the $$12$$ blocks could _also_ be an outlier that stretches the allowed leeway, and relying on this block alone propagates its deviation and makes everything difficult.

To improve this idea, we note that we need to look not just at the $$12$$thh block, but also at the few blocks in its near past and present. How many blocks? Since the allowed deviation is two hours, it makes sense to look in an interval of approximately two hours in each direction.

After some reflection, it seems that the most reasonable lower bound (and the one used by Bitcoin) is to consider the last $$23$$ blocks, and take the _median timestamp_. That is, we only look at the block ordering to take a window of approximate length of four hours, and then we discard the order and focus on the timestamp, taking the one that is chronologically smack in the middle.

More generally, if we want a leeway of $$\lambda\cdot D$$ minutes, we set $$L$$ to be the median timestamp of the last $$2D-1$$ blocks (this also ensures that the number of blocks in the window is necessarily odd, making the median well defined). Setting $$TS=B.timestamp$$ be the timestamp of the block $$B$$ under consideration, we get the first rule:

**Rule I**: If $$TS<L$$ then $$B$$ is invalid.

#### Preventing Future Timestamps

Trying to _upper_-bound the allowed timestamp, we realize that we can't repeat the previous trick. To do that, we need to know the timestamps of blocks that do not exist yet! With some more reflection, we see that a strict upper bound _doesn't make sense_! What if, just by chance, no block was created for four ours? Being too strict with an upper bound can easily devolve into not accepting _any_ blocks with correct timestamps.

Indeed, an _entirely different_ idea is needed here. An idea that can tolerate arbitrarily late timestamps, but only if it _has no choice_. The brilliant solution implemented in Bitcoin is to use the _actual system clock_!

Let $$C$$ denote the clock time. We might be tempted to state the policy: if $$TS-C$$ is more than two hours, reject the block. But that doesn't quite work.

First, when lower-bounding the timestamp, we read off what "two hours" looks like from the timestamps. We want to use that same length for upper-bounding as well. (Otherwise, the protocol can become lopsided, which has annoying consequences.) We use $$C-L$$ to approximate two hours. We do not want the timestamp to exceed $$C$$ by more than this approximation. In other words, we reject a block if we see that $$TS-C > C - L$$, or equivalently $$TS>2C-L$$.

More crucially, note that the system clock time $$C$$ is _not in consensus_! Every node sees a different $$C$$. Relying on $$C$$ (or any other subjective value) in validation rules invites disagreements about block validity that will split the network.

To overcome thihs, note that as time passes, $$2C-L$$ increases while $$TS$$ remains the same. If we wait long enough, eventually the inequality $$TS>2C-L$$ will no longer hold. So we _delay_ the block until it becomes valid! However, if, while waiting, we run into a block that can be included _now_, we do so and discard the delayed block.

**Rule II**: Prefer the first block you see that satisfies $$TS\le 2C - L$$, including delayed blocks.

This is a game-theoretic solution. It does not _prohibit_ future timestamps, but strongly discourages them by reducing their probability of being included (whereby, reducing their expected revenue). Moreover, this probability drops exponentially as the timestamp increases.

<details>

<summary>Summary of Timestamp Rules</summary>

Variables:

* $$B$$ — block under consideration
* $$TS=B.timestamp$$
* $$C$$ — System clock
* $$D$$ — Deviation tolerance measured in block delays (in Bitcoin $$D=12$$)

Compute lower bound: Let $$L$$ be the median timestamps of the latest $$2D-1$$ blocks.

Compute upper bound: Let $$U=2C-L$$

**Lower bound rule**: If $$TS<L$$, reject the block.

**Upper bound rule**: If $$TS>U$$, delay the block. The selected tip is the first encountered valid block that needs no (further) delay.



</details>

## Sliding Windows\*

The way we computed $$\Delta$$ above is one method of many. Such methohds are called _difficulty aggregation heuristics_. The "simple aggregation" we've seen so far is very common in _fixed window DAA, which_ is what we call DAAs similar to Bitcoin's.

As the landscape of cryptocurrency evolved, the limitations of fixed windows became apparent. The relatively long windows make responses to sharp difficulty shifts unreasonably slow. For example, imagine the global hash rate of Bitcoin instantly dropping by 90% _just after_ an epoch started. How long will it take the difficulty to readjust? If your answer is "two weeks," you have not been paying attention. The length of the epoch is 2016, which is _supposed to approximate_ two weeks, and the quality of this approximation is directly proportional to the quality of approximation of $$t$$. Since the global hashrate dropped by 90%, it is now _ten times smaller_ than our approximation, making $$t$$ ten times too big, so $$T$$ is ten times too small! Consequently, untill thhe difficulty target is adjuste, the average block time will be _ten times longer_. The average block time will spike to _one handred minutes,_ and the entire epoch length to _20 weeks_.

Such scenarios are far-fetched for an established coin like Bitcoin, but pose a genuine concern to younger, less mined proof-of-work chains. There are recorded instances of a large mining pool leaving a small project, causing its block times to skyrocket. In practice, the situation naturally spirals as other miners also turn off their rigs until difficulty decreases, grinding the chain to a halt as block times stretch to weeks and difficulty epochs to years. At this point, the only remaining solution is to manually readjust the difficulty via a hard-fork.

To increase reactivity, shorter difficulty windows were considered. But there were good reasons to have long difficulty windows to begin with. With shorter windows, it becomes easier to exploit the DAA to manipulate the system. There is a sharp reactiveness-robustness tradeoff that does not seem to have a sweet spot appropriate to upcoming chains with a low global hashrate.

To obtain better trade-offs, protocol designers naturally turned to _sliding windows_. In this approach, there are no difficulty epochs. The difficulty is recalculated for each block based on a window of blocks preceding it. This allowed for maintaining a large window while responding quickly.

Initially, sliding windows used the same simple aggregation method as Bitcoin. This approach works well for chains with high block rates, like Kaspa, where it is adopted. But this is because such networks have the privilege of accumulating thousands of samples within a few hours. For more typical proof-of-work chains, where block delays are minutes long, this approach is inappropriate.&#x20;

The reasons for that are subtle, and I cannot fully explain them here. But I can shed some light with a nice analogy. This is a variation of an analogy I heard from the pseudonymous Zawy12, a thought leader in the DAA world, whose notes (given as a [list of GitHub issues](https://github.com/zawy12/difficulty-algorithms/issues), in a true cypherpunk fashion) are practically the only technical source on sliding windows. Imagine the difficulty in sliding windows to a long and thin rod, made of a slightly elastic metal. The rod is fixed to the floor at the bottom and has a target at its top. Changing the difficulty is like hitting the target. It causes the top of the rod to oscillate back and forth. The oscillations are dampened by the bottom of the rod being fixed to the floor, but typically, this force is too minimal compared to natural fluctuations in hashrate.

This analogy demonstrates that a sliding window DAA has a sort of natural _inner frequency_. And this is actually what we observe in reality. For example, consider the Kaspa difficulty curve:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>Screenshot from <a href="https://2miners.com/kas-network-hashrate">2miners.com</a></p></figcaption></figure>

We can see fluctuations of around 6-8% in the global hashrate with a consistent frequency of about once oscillation per 40 minutes, which is (by no pure chance) about the window length of Kaspa's difficulty adjustment.

Zawy further noticed that by properly timing a "switch attack" where a relatively small rig is turned on during increases and off during decreases, the oscillations can constructively interfere, causing higher spikes, deeper troughs, and much less stability. Worse yet, these switching attacks emulate what rational coin-hoppers — miners that constantly redirect their rigs to coins based on value and difficulty — would naturally do in many cases.

Fortunately, this analogy doesn't only hint at a problem, but also at a solution. If you want to have a better-controlled oscillator, you _dampen it_. That is, you add something that _resists_ the movement, but the strength of the resistance is _proportional_ to the strength of the movement. Mechanically, this could be accomplished by using a spring that connects the target to its stationary location, like so:

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

So the idea is to implement a sort of "digital spring" that will dampen the oscillations around the displacement. The problem here is that to implement such a spring directly, we need to _know_ the actual hashrate, but figuring that out is exactly why we need DAA to begin with!

Fortunately, there are ways to approximate such a spring. I will not go into the math, but the idea is that if you use the entire history of difficulty targets, but give higher weight to more recent blocks, you get a dampening effect that is somewhat similar to the simple spring dampening above.

There are two common ways to do this:

* LWMA (Linear Weighted Moving Average). The weight of each block is decreased by a _fixed amount_ compared to the previous block. The graph of influence as a function of depth becomes a linear function, hence the name. This approach is usually accredited to Zawy.\
  There are several versions of LWMA, and they are used by Litecoin, Monero, Zcash, and more.
* ASERT (Adjusted Service Rate). There are two key differences. One is that the weighting decreases _exponentially_, and the other is that the weight is not determined by the depth (in terms of block count) but the distance between timestamps.\
  ASERT is developed and used mainly by Bitcoin Cash.

There are various trade offs between the two. For example, ASERT is considered more responsive, while LWMA is considered more robust to timestamp manipulation.
