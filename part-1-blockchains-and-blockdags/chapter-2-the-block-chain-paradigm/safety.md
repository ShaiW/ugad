# Safety

The safety property is a statement about how long the transaction has to be on the selected chain before we have confidence $$\varepsilon$$ that it will not revert, assuming no $$\alpha$$-attackers. That is, with the [approval time](a-security-model-for-blockchain-consensus.md#inclusion-and-approval-times) $$S(\alpha,\varepsilon)$$.

We already agreed that there is hope to defend against a $$\frac{1}{2}$$-attacker, so we assume $$\alpha < \frac{1}{2}$$.

For such an $$\alpha$$, we want the confidence to grow exponentially fast. In other words, if we make $$\varepsilon$$ twice as small, we _don't_ want $$S(\alpha,\varepsilon)$$ to be twice as large. In fact, we want something much stronger: that no matter how many time we halve $$\varepsilon$$, each time would add a _constant_ amount of time. If you have some background (say, from [reading about logarithms](../../supplementary-material/math/stuff-you-should-know/asymptotics-growth-and-decay.md#logarithms) in the appendix), you know that such a relationship between $$S$$ and $$\varepsilon$$ can be expressed by the following equation:

$$S(\alpha,\varepsilon) = O\left(\log(1/\varepsilon)\right)$$

This motivates the following:

**Definition?** The protocol is _safe_ if for any $$\alpha < 1/2$$ it holds that $$S(\alpha,\varepsilon) = O\left(\log(1/\varepsilon)\right)$$

The problem with this definition is that, as it turns out, it is _impossible to satisfy_. No protocol can satisfy it.

{% hint style="info" %}
This definition is impossible to satisfy, yet DAGKnight _does_ satisfy it. Being parameterless, it could _adjust_ to varying network conditions and thus protect against _any_ $$\alpha$$-adversary as long as $$\alpha<\frac{1}{2}$$ (although the confirmation times shoot exponentially fast through the roof as $$\alpha$$ approaches $$\frac{1}{2}$$).

When we say no protocol can satisfy this property, this only holds for protocols that can be analyzed on our model. DAGKnight cannot be, because we assume a fixed latency bound, while DAGKnight, in some sense, _responds_ to varying delays, even if they grow longer than our assumed $$D$$. If we tried to analyze DAGKnight in this model, we would have to cap off its adjustment ability (thus actually analyzing a protocol that is weaker than DAGKnight), and the "unhandled cases" will slightly decrease the size of attackers we can handle.
{% endhint %}

Before we understand how to correct the definition, we will see a magical world where it _can_ be satisfied, and is actually satisfied by Bitcoin.

## Imagine a World With No Orphans

Consider how Bitcoin works under the following unrealistic assumption: the honest network _never_ creates orphan blocks. That is, all honest blocks to ever have and will be created are arranged in a chain.

Hence, even when attacked, the network looks something like this:

<figure><img src="../../.gitbook/assets/10.png" alt=""><figcaption></figcaption></figure>

where $$tx$$ is the transaction the adversary tries to double-spend and $$tx'$$ is a conflicting transaction.

We expect the honest network to create $$1-\alpha$$ blocks for any $$\alpha$$ blocks created by the attacker. In other words, the honest network createes blocks $$\frac{1-\alpha}{\alpha}$$ faster. No matter what $$\alpha$$ is, as long as it is smaller than $$\frac{1}{2}$$, the gap between the honest and adversarial chain will increase. And, as we reason later, the probability that the adversary reverts the chain decreases exponentially with this gap.

## Orphans and the Scaling Problem

In reality, orphans _do_ exist in the honest network. However, recall that we assumed that the adversary can arrange their blocks in a perfect chain. This means that the attacker can revert the chain with less than half the hashing power. Say that the honest network orphans one in three honest blocks, and that $$\alpha = 40\%$$. We find that the honest network creates $$60\%$$ of the blocks, but a third of them goes to waste, so only two thirds of that, namely $$40\%$$ of all the blocks, are _honest network blocks_. This means that although $$\alpha < \frac{1}{2}$$, the adversary and honest network are neck to neck. If we slightly increase the adversary fraction to $$41\%$$, the attack will almost certainly succeed.

More generally, say that a fraction of $$r$$ of the honest blocks is orphaned. For any $$\alpha$$ blocks created by the adversary, the honest network creates $$1-\alpha$$ blocks, but only $$(1-r)(1-\alpha)$$ will be honest _chain_ blocks:

<figure><img src="../../.gitbook/assets/12.png" alt=""><figcaption></figcaption></figure>



Hence, the network has no hope against an $$\alpha$$-adversary unless $$(1-r)(1-\alpha) > \alpha$$. Note that the no-orphans case described above is exactly when $$r=0$$, and we get the usual equation $$1-\alpha > \alpha$$, or $$\alpha < \frac{1}{2}$$.

If we solve the more general equation for $$\alpha$$ we get the following condition

$$\alpha \le \frac{1}{2}\left(1-\frac{r}{2-r}\right)$$$$\alpha \le \frac{1}{2}\left(1-\frac{r}{2-r}\right)$$



