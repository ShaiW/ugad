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
\mathbb{P}\left[Poi\left(5\right)=9\right]=\frac{5^{9}}{9!}e^{-m}\approx3.6\%
$$

What is the probability that the network produces exactly six blocks in an hour? We expect it to be formidable, but if we compute it we find that&#x20;

$$
\mathbb{P}\left[Poi\left(6\right)=6\right]=\frac{6^{6}}{6!}e^{-6}\approx16\%
$$

which is actually quite low!

What if we take margins? Say, ask ourselves what is the probability that between 5 and 7 blocks are created in an hour?

Well, the probability that exactly five blocks are created is

$$
\mathbb{P}\left[Poi\left(6\right)=5\right]=\frac{6^{5}}{5!}e^{-6}\approx16\%
$$

Wait, why did we get the same result? Of course! By replacing $$6^6$$ with $$6^5$$ we made the answer smaller by $$6$$, but by replacing $$6!$$ with $$5!$$ we made it _large_ by a factor of 6 (because we _divide_ by a number six times smaller). It's just a curiousity of Poisson distribution that should not bother us too much.

We can also compute that

$$
\mathbb{P}\left[Poi\left(6\right)=7\right]=\frac{6^{7}}{7!}e^{-6}\approx15\%
$$

so we see that even with an _entire block delay_ as a margin of error, the probability we fall within the margin remains below half! That's a testimony to how _noisy_ block production is.

## Block Delays and Exponential Distribution

Instead of saying "we expect $$k$$ blocks per hour" we can say "we expect a block once every $$1/k$$" hours. The quantity $$1/k$$ is just the block delay $$\lambda$$ we described earlier. Instead of asking ourselves _how many blocks we expect to see in a given time_ we can ask ourselves _how long will we wait for the next block_.

This is described by what we call an _exponential distribution_ $$Exp(\lambda)$$. We would like to say something like "$$\mathbb{P}[Exp(\lambda) = \alpha\cdot \lambda]$$ is the probability that we have waited exactly $$\alpha$$ block delays", which is technically correct but also extremely useless. You see, it turns out that for any $$\alpha$$ we have that $$\mathbb{P}[Exp(\lambda) = \alpha\cdot \lambda]=0$$. Why is this expected? Because the probability we wait _exactly_ alpha block rewards is $$0$$. The number $$\alpha$$ eventually expresses time, and time is _continuous_. Without getting into the theory of continuous variables, I will just say that the useful representation of such variables is to ask what is the probability that they fall _within some range_. The question "what is the probability that we wait _exactly_ ten minutes for a block?" is not interesting, but the question "what is the probability that we wait between nine and a half and ten and a half minutes?" _is_.

It turns out that if blocks are created according to the distribution $$Poi(1/\lambda)$$, then we have that the probability we have to wait between $$a$$ and $$b$$ block delays is given by

$$
\mathbb{P}\left[a\lambda\le Exp\left(\lambda\right)\le b\lambda\right]=e^{-a}-e^{-b}
$$

Note that we are allowed to put $$a=0$$ and $$b=\infty$$ with the understanding that $$e^{-\infty}=0$$.

**Exercise\***: Prove this formula

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

So what does this tell us about block distribution? Let's start with an easy one: what is the probability we wait more than an hour to the next Bitcoin block?

To compute this, we plug in $$a=6$$ (as there are six Bitcoin block delay in one hour) and $$b=\infty$$ (as we want to know the probability we waited _any_ time longer than an hour) to get the result $$e^{-6}\approx 0.2\%$$. This means that we should see such delays about once per 500 blocks, or twice a week.

A good next question is what is the probability that the next block is created _within_ a block delay. Let us compute:

$$
\mathbb{P}\left[0\le Exp\left(\lambda\right)\le\lambda\right]=e^{-0}-e^{-1}=1-\frac{1}{e}\approx63\%
$$

We see that almost _two thirds_ of the time, the delay between blocks will be _shorter than expected_.

A good next question is how fast do the majority of blocks arrive. In other words, how long do we have to wait so that the probability we see a block is exactly half. We already know this should be _less_ than a block delay. To get an exact answer we need to solve for $$a$$ the equation $$\frac{1}{2}=\mathbb{P}\left[0\le Exp\left(\lambda\right)\le a\lambda\right]$$, and I'll leave it to you to verify that the answer is $$a=\log\left(2\right)\approx0.69$$. Nice! We get that _half_ of the blocks arrive within 6.9 seconds. That is, a majority of blocks are _significantly_ earlier than expected. This sheds a bit of light on why Bitcoin's block delay has to be so long!

So how bad is the problem? How short is short enough that, say, $$99\%$$ of the time, the block delay will be longer? To work this out we solve for $$b$$ the equation $$\mathbb{P}\left[Exp\left(\lambda\right)\le b\lambda\right]=\frac{1}{100}$$ to find that the answer is just above $$0.01$$ block delays, or six seconds! That's right, one in 100 Bitcoin blocks will be discovered within _six seconds_, despite the block delay being 10 minutes.

There are many more things that can be worked out using these simple yet powerful formula, and it is always good to have them in your toolbox. I hope that I at least convinced you that there is a concrete reason why many people are concerned with reducing block times in Bitcoin.

{% hint style="info" %}
In this [lovely post](https://blog.lopp.net/bitcoin-block-time-variance/) by Jameson Lopp, you can see how well this theory holds in practice.
{% endhint %}

