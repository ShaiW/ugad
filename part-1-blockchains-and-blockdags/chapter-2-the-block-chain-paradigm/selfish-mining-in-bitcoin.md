# Selfish Mining in Bitcoin

tie-breakingRecall that in [block chain paradigm](the-paradigm.md) we have two expectations of honest miners: mine over the selected tip, and immediately rebroadcast any valid block they learn of. We naturally asked ourselves what justifies these assumptions, and partially solved it by appealing to _rationality_ (in the game theoretic, non-judgmental meaning of the word). We defined a [_rational_ miner](honesty-and-rationality.md) as one seeking to maximize their mining revenue (and do not care about other incentives such as bribes or vindictiveness), and noted that such a miner is incentivized by Bitcoin's block rewards to always mine over the selected tip. The current section deals with the second expectation, that of not withholding blocks. It turns out that there are subtle and fascinating gaps between the honest and rational strategy.

Those gaps are described as a strategy called _selfish mining_, that allows large enough miners to _increase_ their profit by _withholding_ blocks. Selfish mining was first reported by Ittay Eyal and Emin Gün Sirer in their 2013 paper [Majority is not Enough: Bitcoin Mining is Vulnerable](https://arxiv.org/pdf/1311.0243).

{% hint style="info" %}
Applying selfish mining is traditionally called _an attack_, as it very much resembles one: participants deviating from the protocol to obtain unwanted results. However, from a more modern mechanism design point of view, I find "attack" to be a bit of a misnomer, as it depicts participants maximizing the profit according to the incentives set for them by the protocol designer as nefarious. The term "selfish mining attack" is very commo in the literature, which is fine, but for didactive reasons I will just refer to it as a rational strategy.
{% endhint %}

Consider a scenario where there are only two miners: Alice the honest, and Sally the selfish. Alice acts as expected while wants to maximize her profit. This simplification does not really harm generality (beyond the assumption of a _single_ selfish miner), since one large honest miner behaves exactly the same as many small honest miner (or any other division of hashing power among honest miner).

It turns out that by _withholding blocks_, Sally can increase the probability that blocks made by _Alice_ are orphaned. Some of Sally's blocks will also be orphaned in the process, but it turns out that if Sally is large and well connected enough, she can increase the _relative amount_ of Alice's blocks that get orphaned. In other words, Sally can increase her _own share_ of _non_-orphaned blocks.

Now, why is that a problem? If that's the rational strategy, why not just let all miners follow it? For many reasons, let us list two:

1. It would create a disproportionate advantage for larger miners, making the fraction of a small miner even smaller.
2. It _increases the orphan rates, degrading the security of the network_.

Fortunately for Bitcoin, it seems that selfish mining is only feasible for very large miners, and selfish mining attacks (that are detectable by e.g. following orphan rate from the point of view of a non-selfish sufficiently large miner) have not been witnessed in the wild.

## Selfish Mining Strategies

They key to a selfish mining attack is _withholding blocks_. I assume the reader is familiar with double-spending attacks (if not, fret not. We will cover them very soon). If you think about it, a double-spend boils down to _orphaning_ a _particular_ block. The attacker attempts to accomplish this by mining and withholding a competing chain _starting below that blocks_, in the hopes that it will eventually be heavier than the honest chain. At this point she could reveal the withheld chain and orphan the targeted block. (Technically, she, as well as Sally the selfish, are allowed to reveal some of their blocks while the attack is still happening, but we will ignore this).

You can think of selfish mining as a refined version of this idea, exploiting the fact that Sally _doesn't care_ about _which_ of Alice's blocks are orphaned, just that sufficiently many of them are. This allows Sally to constantly restart a "shallow double-spend attack", targeting a different block each time. Lets see how this go.

Sally begins by normally mining over the current selected tip. As long as she did not mine a block. She will always update the tip as Alice produces more blocks (remember, Sally is _rational_, and we already know that rational miners mine above the latest tip). At some point, she will hit a block:

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

Alice, in Sally's place, would have _honestly_ reported the block right away. But Sally thinks it is more _rational_ to wait a little and see where the wind blows. Say that the wind blew Alice's way, and she created the next block:

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Sally is now at an impasse. She could either release her block, hoping that it would orphan Alice's block, or keep mining over it, hoping to make the next block, knowing that if she fails to beat Alice, there is a good chance that her blocks would be orphaned and Alice wouldn't.

Say that Sally, in a gambling mood, decides to keep mining, and _does_ create the next block:

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

She can now keep mining her (now, heaviest) chain in secret, or she could publish both blocks, orphaning Alice's single block. The second option will lead us to this situation:

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

The point is this: if Sally immediately published the first block, the honest network would have considered it the selected tip and switched to mining above it. Sally took the risk of orphaning her block, but the risk paid off, as she orphaned Alice's block instead, _increasing her own fraction of non-orphaned blocks_.

But what if Sally hasn't published her chain and kept pushing? If Alice creates the next block, we are here:

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

What if the honest network mined a block first, bringing us to this situation:

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Sally again has a choice of publishing her blocks and hoping for the best, or pushing forward trying to beat Alice.

Whatever choices Sally makes, if she is smaller than Alice, she will eventually publish her blocks, either because she garnered an advantage too large to risk, or because she is at a disadvantage too large to recover from. She then restarts the entire thing from the _current_ selected tip, ad infinitum.

So the gaping question is: _what choices_ should have Sally made along the way to _maximize_ her profit? Is it possible that this strategy is more profitable than honest mining?

## The Eyal-Sirer Strategy\*

In their paper, Eyal and Sirer analyzed a particular strategy. This strategy is _not_ optimal, but it is profitable enough to be very interesting. It goes like this:

* Mine over the selected tip:\
  \
  ![](<../../.gitbook/assets/image (23).png>)
* If the honest network found a block before you, restart the attack from the new tip:\
  ![](<../../.gitbook/assets/image (22).png>)
* Otherwise, you have a lead of one block over the honest network, good job! _Keep this block to yourself_ and _keep mining_!\
  ![](<../../.gitbook/assets/image (24).png>)
* If the honest network mined the next block, you are at an impasse:\
  ![](<../../.gitbook/assets/image (25).png>)\
  Release your block to the wild, and hope for the best. For future reference, let $$\gamma$$ be the probability you win in this situation (in a sense, $$\gamma$$ encodes how _well connected_ you are)
*   Otherwise, you are already leading by two blocks, good job! Keep going!\


    <figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>
*   Assuming that you have _less than half_ of the total hashing power, at some point the honest network will start to catch up, and your advantage will have shrunk to one block!\


    <figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

    This is too close for comfort! Publish your chain. All the honest block created since we started will be orphaned, congratulations! Now restart everything from the top.

Computing the profitability of this strategy is a fun exercise, but one a bit too involved for our intents. The result of the computation is depicted in this graph:

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption><p>(Fig 2. from Eyal-Sirer) Effectiveness of the Eyal-Sirer strategy, for a single selfish miner/collusion working against an otherwise honest network. The <span class="math">x</span> axis represents the selfish hashrate, and the <span class="math">y</span> axis represents the fraction of blocks that they mine<span class="math">x</span></p></figcaption></figure>

Here we see an interesting phenomenon: Sally does not need a majority of the hash rate to create a majority of the blocks! For $$\gamma = 0$$ (poorly Connected sally) we see that sally needs to have _one third of the hash power_ (that is, she needs to be at least _half as large as Alice_) for _some_ increase to her profit. For $$\gamma = 1$$ (extremely well connected Sally), this strategy will benefit Sally _regardless_ of her fraction, and if she has one third of the fraction, she can create a _majority_ of the blocks.

These results are obviously striking, but there is a common misconception that they imply something much more sinister: that a majority of one third can double-spend. The argument goes like "a majority of one third creates more than half of the blocks, so they can create a competing chain and revert any transaction". This mistake follows from not understanding the strategy. By watching closely one notes that Sally's block must _piggyback_ on Alice's, if she ever hopes to reorg some of them. This _interleaves_ Alice's and Sally's block along the chain.

A successful double spend attack looks something like this:

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

whereas successful selfish mining might look like this:

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Note that in the second image , Sally created $$9$$ out of $$17$$ _non-orphaned_ blocks, which is a majority. However, Alice created a total of $$13$$ blocks in this time, which are more than the Sally's $$9$$ blocks. If the Sally had arranged her block in a chain, she would not have been able to orphan _any_ of Alice's blocks, and her chain would have been shorter.

## Security Definitions

In our vantage, the motivation for presenting selfish mining was twofold: to better understand rationality and the mechanism design of Bitcoin, and to give a concrete example to our [discussion about security notions](selfish-mining-in-bitcoin.md#security-definitions), where we stressed that a security notion is only defined with respect to some goal. When discussing the security of Bitcoin, many become tunnel visioned on security against double-spends. We already stressed that this property is _not quite enough_. The inability to double spend is a security property called _safety._ If we combine it with _another_ security property called _liveness_, that guarantees an attacker cannot _arbitrarily delay_ consensus, we get a security property that is, not confusingly at all, called _security_.

{% hint style="info" %}
Do not read my last statement in a sardonic tone. It is very common and very useful to have, for the variety of cryptographic schemes, a _default_ security notion that is implicitly assumed to underwrite the statement that the primitive is secure. When someone talks about "the security of digital signatures", I automatically assume that they actually means a particular property that is actually called "universal unforgeability under a posteriory chosen message attack", unless the context dictates otherwise. Having these conventions follows naturally from having to navigate the jungle of security definitions, and is not a _bad_ habit. It's downside, though, is that it can be confusing to non-professionals, and impart inaccurate impressions such that "a blockchain (or any other primitive) that is _secure_ according to the standard formal definition is also _secure_ in any other sense that can be relevant to us". The antidote to falling for these too broad generalizations is to always make sure you understand what is hanging on the word "security".
{% endhint %}

However, I made a hopefully fruitful effort to convince you that there is no such thing as "the security", as there could always be other malicious goals that furnish other security properties.

Selfish mining is just that. A selfish miner _is not prohibited_ by the standard Bitcoin security definition. If we want to discuss the security implications of such a miner, we need _a suitable definition_.

In their 2014 paper [The Bitcoin Backbone Protocol: Analysis and Applications](https://eprint.iacr.org/2014/765.pdf), Juan A. Garay, Aggelos Kiayias, and Nikos Leonardos proposed a model that called "the backbone protocol" that more accurately captures the Bitcoin protocol and provides a unified framework that can express many more of the subtleties of a blockchain. The proceeded to define two properties, the _common-prefix_ property, that is equivalent to what we just called "security", and the _chain quality_ property, that measures the fraction of blocks a miner with a fraction of $$\alpha$$ of the hash rate.

We can recast Eyal and Sirer's work in the terminology later introduced by Garay et al.: Eyal and Sirer proved that Bitcoin is an example of a protocol that (assuming a honest majority) satisfies the common prefix property, but fails to satisfy the chain quality property.

{% hint style="info" %}
The statement above is a bit harsh, and that follows from my choice to present a security property as _binary_. Any protocol is either secure or insecure. In practice, security notions are often _parameterized_, where the parameter indicates _how secure_ the protocol is. We've actually already seen an example: recall that when we talked about [fault tolerance](../chapter-1-bft-vs.-pow/byzantine-fault-tolerance.md) we said that BFT can only provide $$1/3$$ fault tolerance, while Bitcoin can provide $$1/2$$ fault tolerance. We implicitly used the term "fault tolerance" as a parametrized security property.

Garay et al. showed that Bitcoin does not have _perfect_ chain quality, but they also derived bounds on the profitability of a selfish miner. They showied that Bitcoin _does_ admit chain quality to an extent that is arguably sufficient (in particular, it is implied by they work that the Eyal-Sirer strategy is pretty close to optimal)
{% endhint %}

This is a striking example of how attackers (or worse, cryptographers!) can surprise you.

## Selfish Mining and Tie Breaking

Recall the discussion about tie-breaking in the blockchain paradigm. We said in passing that selfish mining and tie-breaking is related, and we can see why if we consider the strategy above. In particular the number $$\gamma$$.

We said that the number $$\gamma$$ measures how _well-connected_ you are. But that's because we were following the Bitcoin "seen first" tie breaking rule. What would have happened if we chose a _deterministic_ tie breaking rule like lowest hash or [PoEM](three-chain-selection-rules.md#proof-of-entropy-minima-poem)?

Consider again this situation:

&#x20;

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

To discuss the subtleties of tie breaking, we have to jettison the simplification that the entire honest network is a single entity called Alice, and think of them as many independent miners. Recall that $$\gamma$$ is the probability that if Sally releases her block, she would orphan the honest block. For the "first seen" tie breaking rule, $$\gamma$$ is directly connected to how well connected Sally is. It quantifies her ability to make sure that _most miners_ see her block first, despite the honest block already being in circulation.

But if the tie breaking rule is _deterministic_, she knows that if her block is losing, then $$\gamma = 0$$ and if her block is winning, then $$\gamma$$ is very close to $$1$$. (It is not exactly $$1$$ because there are _some_ scenarios where she still loses, e.g. if by the time she releases her block, the honest network mines _another_ block, or if suddenly a _third_ parallel block joins the party that wins the tie break rule over _both_ other blocks). This can affect her decision in a way that can increase the profitability of a selfish mining attack.

The impact of a deterministic selfish mining attack was analyzed by Ren Zhang and Bart Preneel in their 2019 paper [Lay Down the Common Metrics: Evaluating Proof-of-Work Consensus Protocols' Security](https://ieeexplore.ieee.org/abstract/document/8835227), concluding that a deterministic tie-breaking law _must_ degrade chain quality.

In PoEM this seems to even be slightly exacerbated: if the competing block has considerably less weight than Sally's, she knows that her advantage will _carry over_ for the remainder of the attack. Say that Sally's block is _significantly heavier_ than the competing honest block:

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

Then she knows that even if the honest network creates the next block, there's some chains that _both blocks together_ will be lighter than her block:

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

The question is, _how probable_ these scenarios are, and _what is the expected profit increase_ when they occur. I haven't done the math, buy my educated guess is that it will reveal that PoEM allows a $$1/3$$ selfish miner to create a majority of the blocks _regardless_ of how well connected they are.&#x20;

## Further Work

Since Eyal and Sirer's initial observation, selfish mining has become a central theme in PoW research. Bitcoin is thankfully quite resilient to selfish mining, but this observation means that other protocols should be on their toes, providing at least some evidence that they are not susceptible to cheap selfish mining attacks. Unfortunately, some chains tend to ignore this issue, despite concerning evidence. One such example is the Kadena network, whose developers have yet to respond to an analysis by Wang et al. in their 2022 paper [An Analytical Study of Selfish Mining Attacks on Chainweb Blockchain](https://ieeexplore.ieee.org/abstract/document/9851985?casa_token=M0cuWNrr3AIAAAAA:RawM0LcFIQzkd7yF9RpDJB4YdWX6mq73Z2u-oqCxNYHS0aU9UwhhTzh_JfIUUcen2DM6E14spCbU), which provides evidence that selfish mining becomes easier as more chains are added.

In 2017, Ayelet Sapirshtein, Yonatan Sompolinsky, and Aviv Zohar published the paper [Optimal Selfish Mining Strategies in Bitcoin](https://link.springer.com/chapter/10.1007/978-3-662-54970-4_30), where they noted that Eyal and Sirer’s attack is not optimal and provided an optimal strategy. Their improvement is particularly interesting as it relies on optimization techniques: the authors noted that for any set of parameters α,γα,γ the optimal strategy is slightly different, and provided an efficient algorithm that computes the optimal policy from these parameters. They noted that the original selfish mining strategy coincides with theirs for γ=1γ=1 and small values of αα.

In the 2018 paper [On Profitability of Selfish Mining](https://arxiv.org/abs/1805.08281), Cyril Grunspa,n and Ricardo Pérez-Marco provide a subtler analysis of selfish mining. They note that the assumption that γγ is constant is unrealistic (as γγ depends on how long it took to create each block), and show that when incorporating the time variance of γγ into the computation, the honest mining strategy becomes optimal. They conclude that selfish mining attacks are actually attacks on the difficulty adjustment algorithm. In particular, they conclude that a successful selfish mining attack on Bitcoin must persist over several difficulty windows, making it impractical.

However, in the 2020 paper [Selfish Mining Re-Examined](https://link.springer.com/chapter/10.1007/978-3-030-51280-4_5), Kevin Negy, Peter Rizun, and Emin Gün Sirer pointed out a _mistake_ in the computation by Grunspan and Pérez-Marco. Fixing it, they found that selfish mining can be profitable in the constant difficulty setting. They carefully analyze selfish mining in scenarios of varying difficulty and find it is _even more_ confusing. A surprising property is that the attack is affected by how the difficulty adjustment works, and different approaches to adjusting difficulty furnish different efficacies for selfish mining attacks.

A year before that, Guy Goren and Alexander Spiegelman published the paper [Mind the Mining](https://arxiv.org/pdf/1902.03899), where they revisit mining in the face of variable difficulty. The attacks in this paper “complement” selfish mining, as they are based on the miner deliberately shutting off some of their hardware. This kind of attack has a very different flavor than selfish mining, which assumes attackers have a fixed hash rate. In other words, the analysis of selfish mining implicitly ignores the expenses of the miner, and treats income as pure profit. Taking expenses into account, Goren and Spiegelman find that Bitcoin’s long difficulty epochs enable “win-win” situations: a miner can increase the profits of _all_ miners by reducing their mining efforts (where the reducing miner is “rewarded” in lower energy bills, while the rest are rewarded in higher mining income due to increased difficulty). The loser of this “win-win” is obviously the network itself, whose hash rate, and whereby security, is reduced.

(My deepest gratitude to Ittay Elay for reviewing a much earlier version of this section and making invaluable comments and suggestions)
