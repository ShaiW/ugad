# PoS Vs. PoW

I would like to conclude this section with some _opinions_ about how PoW compares to other, _intrinsic_ anti Sybil countermeasures. I chose to focus on the most ubiquitous one, proof-of-stake (PoS), but the criticism generalizes to any form of BFT that relies on intrinsic scarcity. In PoS, the scarcity is of the coin itself. There are many constellations for this kind of Sybilness, but the common ground is that the influence of any participant, and their compensation, is proportional to the amount of coin staked.

I will now list some unsavory properties of PoS. For me, these are more than enough to conclude that PoS is _unsuitable_ for a decentralized network. However, I stress that while the phenomenon I describe are a matter of objective fact, the latter _interpretation_ is _not_. Many people see things differently and have interesting arguments to back their interpretations (though none that I personally found convincing).

## Security Thresholds

The first observation is that PoS just provides a _weaker level of security_.

As we will see in the following chapters, PoW can provide _security_ assuming a rational majority. If $$51\%$$ of the miners work to maximize their profits, then we are guaranteed two properties:

* Safety: the probability that a transaction reverts decreases exponentially with the number of blocks mined above it
* Liveness: the number of blocks increases with time, no adversary can _stall_ the network

BFT does not have probabilistic finality, but deterministic finality. This means that on one hand it can provide a slightly better safety: if a transaction is accepted, it is guaranteed never to revert. But the slight improvement in safety incurs a meaningful concession in liveness. It follows directly from the [$$3f+1$$ theorem](./) that any collusion of $$34\%$$ can stall the network.

PoS networks typically deal with this using a technique called _slashing_: if a collusion of more than $$34\%$$ and less than $$50\%$$ attempts to disrupt the network, it could be detected, and a fine could be taken from the staked fund. This _decentivizes_ such an attack, but does not _prevent_ it.&#x20;

To be honest, I consider this a completely reasonable security model. Yeah, it is in a sense weaker than that of PoW, but _that_ is not where the bones I want to pick are buried.

## Long Live the King

Imagine a PoW miner that has $$90\%$$ of the global hash rate. Obviously, they have complete control over what happens on the network, but how costly it is to maintain this control?

If it is a popular network, the answer is _a lot_. Maintaining the mining operation requires huge utility costs in terms of electricity, network, hosting, and so on. And that's only where it starts. Because to maintain the advantage, the miner doesn't need just to keep mining, but to keep up with the hardware availability. The have to procure a majority of the newly manufactured hardware, or their advantage will eventually erode. This becomes even more pressing as new hardware becomes more performant, making the miner's current proportion shrink even faster.

In contrast, a PoS entity with $$51\%$$ of the coin could maintain their advantage practically for free. All they have to do is to keep staking. The only loss here is lost-opportunity, but even that's arguable since first, they are still earning staking fees, second, if only a fraction of the coin is staked (as _should_ happen in networks that are actually used for things other than staking), then they only have to stake a similar fraction of their own share and third, they could use the opportunity to earn more and then using this money to buy more coin.

The bottom line here is this: in PoW networks, maintaining control is ever-costly, since it requires holding the majority of a physical, external resource, that could increase in supply. In contrast, in PoS there is no way to force a majority holder to relinquish control.

## The Rich Get Richer

But why does someone accruing $$51\%$$ of the coin is even a concern? If the coin is sufficiently spread around, wouldn't this make it unreasonably expensive to purchase such a large portion, just like trying to buy all the mining machines for a PoW network will spike the price through the roof?

The problem in PoS is that staking more means earning more. Let's see how the math works out.

What makes the analysis a bit confusing is that the total supply changes. We will call the supply at the start of the round the _old_ supply, and the supply at the end of the round the _new_ supply.

Say that the staking fee provides growth by $$\iota$$. That is, if you stake a fraction of $$f$$ of the coin, then after the round, the amount of coin you have is $$\iota\cdot f$$ of the _old_ supply. How much is this of the _new_ supply?

If you hold $$f$$ of the coin, then the rest of the network holds $$1-f$$ of the coin. Now assume the honest network does not stake all of its coin, but some proportion of it, say $$\alpha$$, and say that you also stake only a fraction of your coin, say $$\beta$$. Then only a fraction $$\beta$$ of your fraction was increased, and in total, you hold $$(\iota\cdot\beta+ (1-\beta))\cdot f$$ of the old supply. Similarly, the rest of the network now holds $$(\iota\cdot\alpha + (1-\alpha))\cdot (1-f)$$. What is now your fraction of the _new_ supply? Well, we divide our supply by the total supply to obtain

$$
\frac{\left(\iota\cdot\beta+\left(1-\beta\right)\right)\cdot f}{\left(\iota\cdot\beta+\left(1-\beta\right)\right)\cdot f+\left(\iota\cdot\alpha+\left(1-\alpha\right)\right)\cdot\left(1-f\right)}=\frac{1}{1+\frac{\left(\iota-1\right)\left(\alpha-\beta\right)}{\left(\iota-1\right)\beta+1}\cdot\left(1-f\right)}f
$$

The left side is simply your coin (in old supply units) over _all_ coin (in the same units), the right side was arranged so that it will be clear under what conditions your fraction _increases_. For this, we need the ugly expression before $$f$$ to be larger than $$1$$, so we want its denominator to be smaller than $$1$$, and it is quite easy to see that this happens if and only if $$\beta > \alpha$$ (recall that since the minting fee is _positive_ we have that $$\iota > 1$$). On other words, if you stake a larger fraction than the rest of the network stakes, then your total fraction grows.

By how much?

Say that the staker stakes all of their coin, while the network consistently stakes a fraction of $$\alpha$$. Then if at the start of the round you held a fraction of $$f$$, by the end of the round, your fraction will incrase by a factor of $$1+\frac{\left(\iota-1\right)\left(1-\alpha\right)\left(1-f\right)}{\iota-\left(\iota-1\right)\left(1-\alpha\right)\left(1-f\right)}$$.

We take Ethereum as a model. The staking fee is around 5% annually. Ethereum has $$206$$ rounds a day, so we set $$\iota = 1.05^{1/{(365\cdot 206)}}\approx 1.00000065$$.

Currently around $$1/4$$ of Ethereum is constantly staked, but if we assume this includes our rich trying to get richer, we get that the remaining fraction is $$1/4-f$$. So we set $$a = 1/4 -f$$ where $$f$$ is the _current_ fraction of the rich.

Assuming these conditions remain constant (not a very realistic assumption, but we are just trying to feel out the growth rate here), we can reiterate the formula above to see how the fraction of a collusion that currently holds a fraction $$f$$ of the coin for some $$f<1/4$$. I simulated $$50$$ years of accumulation for initial values of $$f=0.1,0.13,0.16,0.19,0.22$$, and these are the results

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Growth of a stake-holder staking their entire bag for 50 years assuming the parameters above</p></figcaption></figure>

Now, 50 years is a _long_ time, even longer if you literally stake _all_ your money. But this growth is still alarming business. After all, we _are_ talking about systems with presumptions to replace at least a part of the backbone of global economy. Who can tell how much money it will be worth to coerce this system in the future.
