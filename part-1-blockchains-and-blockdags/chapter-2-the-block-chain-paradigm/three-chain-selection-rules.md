# Three Chain Selection Rules

We are about to discuss several security notions that are defined for block chains in the abstract. To understand these security notions, we would like to apply them to some concrete examples. Hence, it is instructive at this point to introduce some chain selection rules. I chose to describe three of them. The two "big ones" are of course Bitcoin's heaviest chain rule, and GHOST. As a third example, we will use the more exotic "proof of entropy minima", more known as PoEM. The latter one is interesting in the sense that ties are extremely rare, so it can somewhat circumvent selfish mining, a property that is both to its advantage and to its detriment.

## Heaviest Chain Rule (HCR)

The _longest_ chain rule is as simple as the name suggest. Given a tree, choose the tip that is furthest from the genesis block:

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

However, the rule is called _heaviest_ chain, not _longest_ chain, which begs two questions: what makes a chain heavy, and why is it important.&#x20;

To answer the first question, recall that in PoW there is a [difficulty target](../chapter-1-bft-vs.-pow/how-pow-works.md#difficulty-adjustment-in-bitcoin) that is constantly changed to regulate the flow of blocks. The difficulty target sets how many leading zeros the hash of a block header must have. Without diving into the details, just recall that for each block we assign a _target_ $$T$$, where a _lower_ target means that it is _harder_ to solve the block. Hence, it makes sense to define the _weight_ of a block as $$1/T$$.

When the difficulty is fixed, it follows that all blocks have the same weight, and so the heaviest chain rule and longets chain rules become synonymous.

As for _why_ we need the heaviest chain rule? The short answer is that a small miner can cheaply create very long chains, much longer than the honest network, by manipulating the difficulty adjustment to make block production very easy. This attack will create a very long chain, but a very light one too. The accumulated weights do not measure how many blocks were created, but how much _work_ was put into them. It doesn't matter how cleverly the small miner manipulates the difficulty adjustment, they cannot spend more work than the rest of the network. We will revisit this attack in more detail at the end of the section.

Interestingly, in the original Bitcoin white paper, Satoshi missed this attack and proposed the longest chain rule (despite discussing difficulty adjustment). Fortunately, in the two years between the white paper and the launch of the actual network, this issue was noticed and fixed.

## Greedy Heaviest Observed Subtree (GHOST)

The GHOST rule was first introduced by Yonatan Sompolinsky and Aviv Zohar in 2013, in their aptly named paper [Fast Money Grows on Trees, Not Chains](https://eprint.iacr.org/2013/881).

Many people, including yours truly, see GHOST as the first meaningful innovation withing the block chain paradigm since Bitcoin was launched. While other attempts to improve on Bitcoin included superficial parameter changes (like playing with block sizes and delays), GHOST quite literally turns HCR on its head.

While in HCR you find the selected tip by starting from the tips and looking _back in time_ to see who is standing on the most weight. In GHOST, you start _from the genesis_ and _climb your way up_, choosing each step the block that has most work _above_ it.

More concretely, for any block $$B$$, we can define the _subtree rooted at_ $$B$$ to contain $$B$$ and all blocks from which $$B$$ is reachable (when we switch to DAG parlance, we will simply call this $$B.\overline{Future}$$):

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can define the _weight_ of each tree like we did in HCR, as the sum of reciprocals of difficulty targets of the blocks in the tree.

To find the selected tip of a given tree, we start from the Genesis block. We then look at all of its children and for each child calculate the size of the tree rooted at this child. We repeat the process for the child with the most weight above it again and again until we find a tip, which will be returned as the selected tip.

Let us follow an example. Assume that all blocks weigh the same, and consider this tree:

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

The chain selection rule's job is to output one of the tips $$T_1,\ldots,T_5$$. You are welcome to check that $$T_5$$ has the longest/heaviest chain below it, so this is the tip that HCR will return. What about GHOST?

Well, lets work it out. GHOST starts at genesis, and computes the weight of its children:

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

It then chooses the child with the most weight above it, and repeats the process:

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

GHOST repeats the process until it reaches a tip, and outputs it as the selected tip:

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

While I have your attention, I would like to address a few common misconceptions about GHOST that need to go away:

* The purpose of GHOST is _not_ to pay block rewards to so called "uncle blocks" (which are orphaned blocks, but in a close proximity to the blocks that were not (like $$T_2$$ above but not like $$T_5$$). Ethereum indeed intended  to use GHOST for such a feature in _their_ implementation, but it is not the motivation for the original design and in fact, the GHOST paper explicitly recommends _against_ it.
* In fact, Ethereum 1.0 never used GHOST in production. They used some (home brewed) variation of the [Inclusive protocol](https://allquantor.at/blockchainbib/pdf/lewenberg2015inclusive.pdf) (Lewenberg, Sompolinsky, Zohar). Post-merge Ethereum uses an adaptation of GHOST for PoS called LMD-GHOST.
* Most importantly, and I can't say this emphatically enough, **GHOSTDAG is not a DAG version of GHOST**. I am going to repeat this statement several times throughout this book so be prepared. The worst thing about GHOSTDAG is how misleading its name is. In the next part of the book, when we survey the jungle of attempted blockDAG protocols, we will see that GHOSTDAG is pretty much the _only_ one that is _not_ a DAGified version of GHOST, but rather a DAGified version of HCR.

{% hint style="info" %}
One might wonder at this point, if GHOSTDAG is not a DAGified version of GHOST, then **why is it called GHOSTDAG?!?** Well, the answer is, obviously, that it is named after the movie "Ghost Dog: The Way of the Samurai".
{% endhint %}

## Proof of Entropy Minima (PoEM)\*

This last chain rule is an interesting one. In particular, unlike GHOST and HCR, PoEM _couples_ the [difficulty mechanism](../chapter-1-bft-vs.-pow/how-pow-works.md#the-dumb-puzzle) to the chain selection rule.

PoEM was introduced in [a note](https://arxiv.org/abs/2303.04305) by Karl Kreder (a.k.a. "Dr. K") and Shreekara Shastry, and more thoroughly analyzed by Zindros, Kreder et al. in a [2025 preprint](https://eprint.iacr.org/2024/200.pdf).

Instead of just looking at the topology of the block, it uses the difficulty hit by the blocks to weigh the chain. Recall that in order for a block $$B$$ to be considered valid it should contain a nonce $$n$$ such that $$\mathsf{H}(B[n])\le T$$ where $$T$$ is the difficulty target.

In the context of HCR and GHOST, we don't really care about the _actual value_ $$\mathsf{H}(B[n])$$. Even when we use difficulty to weigh the chain, we used _targets_ and not actual values. PoEM uses the actual values. That is, if a block happened to get $$\mathsf{H}(B[N])$$ which is _twice as small_ as the target, then in some sense, it will be considered "twice as heavy".

{% hint style="info" %}
In practice, the PoEM weighting works a little different than described, they don't take the sum of reciprocals but their _logarithms_. So in the example above if $$\mathsf{H}(B[N])$$ is twice smaller than $$T$$ then the block $$B$$ is "one more" heavier,$$\log(1/\mathsf{H}(B[N])) = \log(2/T)=1+\log(1/T)$$

They have good reasons to do this which I will not get into.


{% endhint %}

One interesting thing about PoEM is that [tie breaking](the-paradigm.md#breaking-ties) is extremely rare. If two blocks point at the same block, they are only tied if _they happen to have the same hash_ which is extremely unlikely (how unlikely? Despite years of usage and scrutiny, including all Bitcoin mining, not a single collision in SHA-256 is known).

Recall this situation:

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

In HCR and GHOST miners are advised to mine over the blocks they heard about first. In particular, the only way to determine whether $$A$$ or $$B$$ is the ultimate successor is to wait for sufficiently long and see where the wind blows.

In contrast, in PoEM, you can read off the blocks themselves which will be preferred by a miner, which can give more certainty that, as their analysis shows, translates to faster transaction confirmation.

Note that just because block $$B$$ has more weight than block $$A$$ does not _guarantee_ that it will be the chosen block. Indeed, it could be that even though $$B$$ has more weight, the network managed to mine an _additional block_ over block $$B$$, and their _combined_ weight is higher:

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption><p>Here <span class="math">B</span> is heavier than <span class="math">A</span> but lighter than <span class="math">A'</span>. From the point of view of a node that does not know <span class="math">A</span>, <span class="math">B</span> is the tip selected by PoEM, but from the point of view of a node that knows <span class="math">A'</span>, PoEM selects <span class="math">A'</span>, and <span class="math">B</span> is orphaned.</p></figcaption></figure>

{% hint style="info" %}
Note that it would be unfair to call this property a _criticism_ of PoEM. It only establishes that PoEM can't guarantee something we already know HCR and GHOST can't guarantee either. In fact, _no_ chain selection rule can _guarantee_ which side of the fork is going to win, because that's a form of deterministic finality. In other words, the existence of a chain rule with such a strong guarantee will contradict the $$3f+1$$ theorem.
{% endhint %}

However, these benefits are not without costs, as we will see in the sequel.

## Why Heaviest and Not Longest?\*

We stated above that the longest chain rule is not secure due to the [difficulty adjustment mechanism](../chapter-1-bft-vs.-pow/how-pow-works.md#difficulty-adjustment-in-bitcoin), that allows creating long (though not heavy) chains for cheap.

Lets work out the math. Consider a blockchain that uses difficulty epochs like Bitcoin, say that the block delay is $$\lambda$$ and that there are $$N$$ blocks per epoch. Consider an attacker with $$\alpha$$ of the global hash rate. Also, let $$q$$ be the maximal _decrease_ in difficulty across difficulty windows. (recall that in Bitcoin we have $$\lambda = 10\text{ min}$$, $$N = 2016$$ (so $$\lambda\cdot N = 20160\text{ minutes} = 2\text{ weeks}$$), and $$q=\frac{1}{4}$$).

Since the adversary only has a fraction $$\alpha$$ of the hash rate, before the difficulty has changed, it will take them $$\frac{1}{\alpha} \lambda$$ to create each block. So the first entire difficulty epoch will require $$\frac{1}{\alpha} \lambda\cdot N$$. After which, the adversary can reduce the difficulty by $$q$$ making the second difficulty epoch take $$\frac{1}{\alpha} \lambda\cdot N\cdot q$$, and similarly the third will take $$\frac{1}{\alpha} \lambda\cdot N\cdot q^2$$ and so on.

Obviously, at some point the difficulty will be so low that other overhead will dominate the computation, but if we ignore this, we get that an adversary can create "infinitely many blocks" in a finite time, which we can compute using the [geometric series formula](../../supplementary-material/math/stuff-you-should-know/geometric-series.md) to be

$$
N\frac{\lambda}{\alpha}\sum_{n=0}^{\infty}q^{n}=\frac{1}{\alpha}\frac{\lambda\cdot N}{1-q}
$$

In particular, if we plug in the values for Bitcoin we get that $$\frac{\lambda\cdot N}{1-q} = 26880\text{ min}$$ which is less than $$20$$ days. So we get, for example, that a $$1\%$$ attacker would be able to create a longer chain in less than $$2000$$ days, or $$5.5$$ years, and a $$10\%$$ attacker could revert the entire Bitcoin chain in just six months.

This becomes even worse if you consider adversaries that start their competing chain in the genesis block, where difficulty is already low.
