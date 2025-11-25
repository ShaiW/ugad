# What About Proof-of-Stake?

Proof-of-work is not the only form of sybil resistance proposed for blockchains. In fact, there are more proof-of-X variants than reasonable to count. But the most popular contender to rival PoW is _proof-of-stake_ (PoS).

PoS and PoW roughly divide the world of decentralized Sybilness into two categories. PoW _extrinsic_: the influence of participants relies on their ability to obtain _external resources_ such as hardware and energy.  PoS is _intrinsic_: the influence of a participant is directly proportional to a resource _generated_ _by the protocol,_ such as coins. Practically any other form of decentralized Sybilness falls into one of these two categories. For example, proof-of-time-and-space is extrinsic, while the many forms of proof of reputation/observation/participation are all intrinsic.

The benefit of PoS is straightforward: it does not waste resources to secure itself. It is therefore the obligation of any good PoW book to explain why PoS does not make PoW obsolete. So I want to take the  time to present some unsavory properties of PoS (and intrinsic anti-Sybil in general).

The intention is not a condemnation of PoS, just a list of problems that have to be addressed by any PoS  protocol. These problems are unique to PoS, so each of them is an advantage of PoW, showing that  there is no unambiguously better form of Sybilness.

The million-dollar question is whether it is possible to create a protocol that successfully overrides these risks. From my familiarity with the landscape, there is no such protocol today. My personal conviction is that these problems pose an insurmountable conceptual barrier to decentralized PoS protocols.

## Long Live the King

Imagine a PoW miner that controls $$90\%$$ of the global hash rate. Obviously, they have the power to censor the network completely. But how costly is it to maintain this control?

If it is a popular network, the answer is _a lot_. Maintaining the mining operation requires huge utility costs in terms of electricity, network, hosting, and so on. And that's just first-order expenses. To keep their advantage from eroding, they must procure a majority of all newly manufactured hardware. This effect becomes heavier as new hardware becomes more efficient, making the ruler's influence shrink even faster.

In contrast, a PoS entity with $$51\%$$ of the coin can maintain an advantage practically for free. All they have to do is keep staking. One might argue that this is not free, as they are paying lost opportunity costs as long as they keep staking. That's not true at all. If they can make more coin some other way, they will. The key observation is that the staking fees ensure that the _minimal_ increase in their fraction is always positive. It makes even the _worst-case scenario_ more profitable for large holders.

In PoW networks, maintaining control is ever-costly, since it requires holding the majority of a physical, external resource.  This resource is costly to maintain, and the protocol does not regulate its supply. In contrast, in PoS, there is no way to force a majority holder to relinquish control.

## The Rich Get Richer

By isn't this scenario unrealistic? If the coin is sufficiently spread, wouldn't accruing this much be unreasonably expensive? Isn't this exactly like trying to buy so much mining hardware that you jack the price through the roof?

The problem is that in PoS, _staking more means earning more_. Excluding a completely unutilized network where 100% of the coin is perpetually staked, the freshly minted coins always go to stakers. This means that the fraction each staker holds of the total supply gradually increases.

To understand this subtle effect, let's work through the math.

Say that the staking fee provides growth by $$\iota$$. That is, if you stake a fraction of $$f$$ of the coin, then after the round, the amount of coin you have is $$\iota\cdot f$$ of the _old_ supply. How much is this of the _new_ supply?

If you hold $$f$$ of the coin, then the rest of the network holds $$1-f$$ of the coin. Now assume the honest network does not stake all of its coin, but some proportion of it, say $$\alpha$$, and say that you also stake only a fraction of your coin, say $$\beta$$. Then only a fraction $$\beta$$ of your fraction was increased, and in total, you hold $$(\iota\cdot\beta+ (1-\beta))\cdot f$$ of the old supply. Similarly, the rest of the network now holds $$(\iota\cdot\alpha + (1-\alpha))\cdot (1-f)$$. What is now your fraction of the _new_ supply? Well, we divide our supply by the total supply to obtain

$$
\frac{\left(\iota\cdot\beta+\left(1-\beta\right)\right)\cdot f}{\left(\iota\cdot\beta+\left(1-\beta\right)\right)\cdot f+\left(\iota\cdot\alpha+\left(1-\alpha\right)\right)\cdot\left(1-f\right)}=\frac{1}{1+\frac{\left(\iota-1\right)\left(\alpha-\beta\right)}{\left(\iota-1\right)\beta+1}\cdot\left(1-f\right)}f
$$

The left side is simply your coin (in old supply units) over _all_ coin (in the same units), the right side was arranged so that it will be clear under what conditions your fraction _increases_. For this, we need the ugly expression before $$f$$ to be larger than $$1$$, so we want its denominator to be smaller than $$1$$, and it is quite easy to see that this happens if and only if $$\beta > \alpha$$ (recall that since the minting fee is _positive_ we have that $$\iota > 1$$). In other words, if your staked fraction is _larger_ than the fraction staked by the rest of the network (combined), then your total fraction of the supply _grows._

By how much?

Say that the staker stakes all of their coin, while the network consistently stakes a fraction of $$\alpha$$. Then if at the start of the round you held a fraction of $$f$$, by the end of the round, your fraction will have increased by a factor of $$1+\frac{\left(\iota-1\right)\left(1-\alpha\right)\left(1-f\right)}{\iota-\left(\iota-1\right)\left(1-\alpha\right)\left(1-f\right)}$$.

Let us apply this simple model to ethereum. The staking fee is around 5% annually. Ethereum has $$206$$ rounds a day, so we set $$\iota = 1.05^{1/{(365\cdot 206)}}\approx 1.00000065$$.

Currently around $$1/4$$ of Ethereum is constantly staked, but if we assume this includes our rich trying to get richer, we get that the remaining fraction is $$1/4-f$$. So we set $$a = 1/4 -f$$ where $$f$$ is the _current_ fraction of the rich.

Assuming these conditions remain constant (not a very realistic assumption, but we are just trying to feel out the growth rate here), we can reiterate the formula above to see how the fraction of a collusion that currently holds a fraction $$f$$ of the coin for some $$f<1/4$$. I simulated $$50$$ years of accumulation for initial values of $$f=0.1,0.13,0.16,0.19,0.22$$, and these are the results:

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption><p>Growth of a stake-holder staking their entire bag for 50 years assuming the parameters above</p></figcaption></figure>

Now, 50 years is a _long_ time to stake _all_ your money. Nevertheless, this growth is still an alarming business. After all, we _are_ talking about systems with presumptions to replace at least a part of the backbone of the global economy. Who can tell how much money it will be worth to coerce such a system in the future?
