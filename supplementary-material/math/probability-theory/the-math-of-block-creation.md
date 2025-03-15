# The Math of Block Creation

The purpose of this section is two-fold:

* Use block creation to understand the Poisson and exponential distributions
* Use the Poisson and exponential distributions to understand block creation

## Poisson Processes

A _Poisson_ _process_ describes any process during something happens at a know _rate_, but we can't know when exactly it happens, just _how often_. A common example is radioactive decay. Emission of radioactive particles is a memoryless process. Knowing when particles were emitted before tells us nothing about the next particle to emit. Other common examples include people arriving at a store, or typos in a book. We assume the time/place each happens is random and independent, but that there is some fixed rate.

Appropriately, the Poisson distribution has a rate parameter $$k$$ that tells us how often we expect the event to happen within an agreed upon unit of time (say, an hour). We want the random variable $$Poi(k)$$ to distribute like the number of events we see in an hour, given that we have $$k$$ events an hour _on average_.

It turns out that the correct distribution is given by

$$
\mathbb{P}[Poi(k)=m] = \frac{k^m\cdot e^{-k}}{m!}{}
$$

We can arrive this formula by dividing our "hour" into small segment, and treating each like a Bernoulli trial.

Say we have an event that happens on average $$k$$ times an hour. We split the hour into $$n$$ parts, making $$n$$ large enough that we feel comfortable to ignore the possibility that the event happens more than once during an $$n$$th of an hour. We note that each $$n$$th of an hour distributes the same, and for each one there is some probability $$p$$ that the event will happen therein. Hence, the number of times we see the event is approximated by $$\mathcal{Bin}(n,p)$$, so we have that $$\mathbb{E}[\mathcal{Bin}(n,p)] = k$$. But we have computed that $$\mathbb{E}[\mathcal{Bin}(n,p)] = np$$ so we get that $$p=k/n$$.

The upshot is that $$Poi(k)=n$$ is the limit (for an appropriate sense of the word) of $$\mathcal{Bin}(n,k/n)$$ as $$n$$ approaches infinity. One can compute explicitly this leads to the formula above, or you can just take my word for it.

**Exercise\***: Prove that for any $$m$$ we have that\


$$
\lim_{n\to\infty}\mathbb{P}\left[\mathcal{Bin}(n,k/n)=m\right]=\frac{k^{m}}{m!}e^{-k}
$$

<details>

<summary>Solution</summary>



$$\begin{aligned}\lim_{n\to\infty}\mathbb{P}\left[\mathcal{Bin}(n,k/n)=m\right] & =\lim_{n\to\infty}{n \choose m}\left(k/n\right)^{m}\left(1-k/n\right)^{n-m}\\  & =\lim_{n\to\infty}\frac{n!}{m!\left(n-m\right)!}k^{m}\frac{1}{n^{m}}\left(1-k/n\right)^{n-m}\\  & =\frac{k^{m}}{m!}\lim_{n\to\infty}\frac{n!}{\left(n-m\right)!}\frac{1}{n^{m}}\left(1-k/n\right)^{n-m}\\  & =\frac{k^{m}}{m!}\lim_{n\to\infty}\frac{\left(n-m+1\right)}{n}\cdot\ldots\cdot\frac{n}{n}\lim_{n\to\infty}\left(1-k/n\right)^{n-m}\\  & =\frac{k^{m}}{m!}\lim_{n\to\infty}\left(1-\frac{1}{n/k}\right)^{n}\\  & =\frac{k^{m}}{m!}e^{-k} \end{aligned}$$



</details>

## Blocks Creation is a Poisson Process

[The way proof-of-work works](../../../part-1-blockchains-and-blockdags/chapter-1-bft-vs.-pow/how-pow-works.md) dictates that block creation is actually a Poisson process. Say that the global hashrate is $$n$$ hashes per second. That is, in an average $$n$$th of a second, _someone_ makes a _single attempt_ to guess the block. And if we divide the second into much more segments, say $$2^n$$, then it becomes extremely unlikely that two hashes were computed in the same segment.

Disregarding difficulty adjustment and such, we assume that the network is parametrized to create, on average, _one block_ _block delay_, where the block delay $$\lambda$$ is determined by the system designer. For example, in Bitcoin we have that $$\lambda = 10\text{ minutes}$$.

The random variable $$Poi(k)$$ how block creation distributes over $$k$$ block delays. For example, the probability that we see nine Bitcoin blocks within 50 minutes (which are five block delays) is

$$
\mathbb{P}\left[Poi\left(5\right)=9\right]=\frac{5^{9}}{9!}e^{-k}\approx3.6\%
$$

What is the probability that the network produces exactly six blocks in an hour? We expect it to be formidable, but if we compute it we find that&#x20;

$$
\mathbb{P}\left[Poi\left(6\right)=6\right]=\frac{6^{6}}{6!}e^{-6}\approx16\%
$$

which is actually quite low!

What if we take margins? Say, ask ourselves what is the probability that between 5 and 7 blocks are created in an hour?

Using the formula we get:

$$
\mathbb{P}\left[Poi\left(6\right)=5\right]=\frac{6^{5}}{5!}e^{-6}\approx16\%
$$

Wait, why did we get the same result? Of course! By replacing $$6^6$$ with $$6^5$$ we made the six times smaller, but by replacing $$6!$$ with $$5!$$  in the _denominator_ we made it six times _bigger_. Don't be bothered by this too much, it's jus ta coincicidence.

We can also compute that

$$
\mathbb{P}\left[Poi\left(6\right)=7\right]=\frac{6^{7}}{7!}e^{-6}\approx15\%
$$

so we see that even with an _entire block delay_ as a margin of error, the probability we fall within the margin remains below half! That's a testimony to how _noisy_ block production is.

## Block Delays and Exponential Distribution

Instead of asking ourselves _how many blocks we expect to see in a given time period,_ we can ask ourselves _how long will we wait for the next block_.

To discuss this, it is convenient to shift our perspective a bit, and replace _rates_ with _delays._ The statement "we expect $$k$$ blocks per hour" we can say "we expect a block once every $$1/k$$" hours. The quantity $$1/k$$ is just the block delay $$\lambda$$ we described earlier.&#x20;

Up to now, when we described a distribution we just stated, for each value, what is the probability that we see that value. E.g. what is the probability that we see five blocks within one block delay.

If we try that approach here we will run into an annoying obstruction: for any $$\alpha\ge 0$$ the probability we waited _exactly_ $$\alpha$$ seconds is _zero_. Why? Because _time is continuous_. The probability that a block was created at some _specific_ moment is as likely as hitting an infinitesimally small point with an infinitely thin arrow.

Without diving into the formalities, the correct way to reason about continuous is to consider _ranges_. Instead of asking futile questions about the _exact_ value of a block delay (that will inevitably lead to a heated philosophic argument about whether an exact instance in time is even well defined in our physical reality), we can more reasonably ask something like "what is the probability we wait between 599 and 601 seconds?"

It turns out that if the block rate $$1/\lambda$$ blocks per minute (so the number of blocks we see in a minute distribuets like $$Poi(1/\lambda)$$), then the amount of time we wait distributes according to what we call an _exponential distribution_. We denote by $$Exp(\lambda)$$ the continuous random variable with the property that for every $$a,b$$ satisfying $$0\le a < b \le \infty$$ we have that

$$
\mathbb{P}\left[a\lambda\le Exp\left(\lambda\right)\le b\lambda\right]=e^{-a}-e^{-b}
$$

Note that we are allowing $$b=\infty$$ with the understanding that $$e^{-\infty}=0$$.

It turns out that this random variable perfectly represents the waiting time for a block, given that the average block delay is $$\lambda$$. For example, if we want to know the probability that we wait between $$599$$ and $$601$$ seconds for the next Bitcoin block, we choose $$a = 599/600$$ and $$b=601/600$$ (because we assume $$\lambda = 600\text{ sec}$$) and find that the answer is

$$
e^{-599/600}-e^{-601/600}\approx0.1\%
$$

which is a meaningful number we can work with.

**Exercise\***: Assuming that the block rate of mining distributes like $$Poi(1/\lambda)$$, prove the block _delay_ distributes like $$Exp(\lambda)$$.

<details>

<summary><strong>Solution</strong></summary>

We first note that

$$\begin{aligned}\mathbb{P}\left[a\lambda\le Exp\left(\lambda\right)\le b\lambda\right] & =1-\mathbb{P}\left[Exp\left(\lambda\right)\le a\lambda\text{ or }b\lambda\le Exp\left(\lambda\right)\right]\\  & =1-\left(\mathbb{P}\left[Exp\left(\lambda\right)\le a\lambda\right]+\mathbb{P}\left[b\lambda\le Exp\left(\lambda\right)\right]\right)\\  & =1-\left(\mathbb{P}\left[Exp\left(\lambda\right)\le a\lambda\right]+1-\mathbb{P}\left[Exp\left(\lambda\right)\le b\lambda\right]\right)\\  & =\mathbb{P}\left[Exp\left(\lambda\right)\le a\lambda\right]-\mathbb{P}\left[Exp\left(\lambda\right)\le b\lambda\right] \end{aligned}$$

Now to figure out $$\mathbb{P}\left[a\lambda\le Exp\left(\lambda\right)\right]$$ we note that it is the same of asking what is the probability of seeing _zero_ blocks during a period of $$a\lambda$$ block delays, a period through which we _expect_ to see $$a$$ blocks. In other words, we get that

$$\mathcal{P}\left[a\lambda\le Exp\left(\lambda\right)\right]=\mathcal{P}\left[Poi\left(a\right)=0\right]=\frac{\left(ak\right)^{0}e^{-a}}{0!}=e^{-a}$$

and we can repeat the computation for $$b$$, and get the desired expression.

</details>

{% hint style="info" %}
We derived the Poisson formula from first principles by assuming that the process is _memoryless_. The assumption that each interval is independent of the others is what allows us to reduce the Poisson process into a limit of binomial distributions. Can we similarly derive the exponential distribution formula from first principles? Yes, we can. The idea is that the amount of time we have to wait _does not depend_ on how long we've been waiting already. Expressing this requires conditional probabilities, a topic we haven't touched. But the idea is that the probability we wait $$a$$ block delays is _exactly_ the probability that we wait _a total_ of $$a+b$$ block delays _given that we already waited_ $$b$$ block delays. In conditional probability notation, the exponential distribution $$Exp(\lambda)$$ must satisfy the equation

$$
\mathbb{P}\left[a\lambda\le Exp\left(\lambda\right)\right]=\mathbb{P}\left[\left(a+b\right)\lambda\le Exp\left(\lambda\right)\mid b\lambda\le Exp\left(\lambda\right)\right]
$$

With a bit of work, one can show that the exponential distribution we described above is the _only_ one that has this property.
{% endhint %}

So what can we compute with this formula?&#x20;

Let's start with an easy one: what is the probability we wait more than an hour for the next Bitcoin block?

To compute this, we plug in $$a=6$$ (as there are six Bitcoin block delays in one hour) and $$b=\infty$$ (as we want to know the probability we waited _any_ time longer than an hour) to get the result $$e^{-6}\approx 0.2\%$$. This means we should see such delays about once per 500 blocks, or twice a week.

A good next question is: what is the probability that the next block is created _within_ a block delay> Let us compute:

$$
\mathbb{P}\left[0\le Exp\left(\lambda\right)\le\lambda\right]=e^{-0}-e^{-1}=1-\frac{1}{e}\approx63\%
$$

We see that almost _two-thirds_ of the time, the delay between blocks will be _shorter than expected_.

A good next question is how fast do the majority of blocks arrive. In other words, how long do we have to wait so that the probability we see a block is exactly half. We already know this should be _less_ than a block delay. To get an exact answer we need to solve for $$a$$ the equation $$\frac{1}{2}=\mathbb{P}\left[0\le Exp\left(\lambda\right)\le a\lambda\right]$$, and I'll leave it to you to verify that the answer is $$a=\log\left(2\right)\approx0.69$$. Nice! We get that _half_ of the blocks arrive within 6.9 seconds. That is, a majority of blocks are _significantly_ earlier than expected. This sheds a bit of light on why Bitcoin's block delay has to be so long!

So how bad is the problem? How short is short enough that, say, $$99\%$$ of the time, the block delay will be longer? To work this out we solve for $$b$$ the equation $$\mathbb{P}\left[Exp\left(\lambda\right)\le b\lambda\right]=\frac{1}{100}$$ to find that the answer is just above $$0.01$$ block delays, or six seconds! That's right, one in 100 Bitcoin blocks will be discovered within _six seconds_, despite the block delay being 10 minutes.

We can work out _many_ things with these simple yet power formulae, which are always good to have in your toolbox. I hope that I at least convinced you that there is a concrete reason why many people are concerned with reducing block times in Bitcoin.

{% hint style="info" %}
In this [lovely post](https://blog.lopp.net/bitcoin-block-time-variance/) by Jameson Lopp, you can see how well this theory holds in practice.
{% endhint %}

