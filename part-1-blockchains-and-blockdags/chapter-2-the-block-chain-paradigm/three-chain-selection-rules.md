# Three Chain Selection Rules

We are about to discuss several security notions that are defined for block chains in the abstract. To understand these security notions, we would like to apply them to some concrete examples. Hence, it is instructive at this point to introduce some chain selection rules. I chose to describe three of them. The two "big ones" are of course Bitcoin's heaviest chain rule, and GHOST. As a third example, we will use the more exotic "proof of entropy minima", more known as PoEM. The latter one is interesting in the sense that ties are extremely rare, so it can somewhat circumvent selfish mining, a property that is both to its advantage and to its detriment.

## Heaviest Chain Rule (HCR)

The _longest_ chain rule is as simple as the name suggest. Given a tree, choose the tip that is furthest from the genesis block:

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

We can define the _weight_ of each tree like we did in HCR, as the sum of reciprocals of difficulty targets of the blocks in the tree.

To find the selected tip of a given tree, we start from the Genesis block. We then look at all of its children and for each child calculate the size of the tree rooted at this child. We repeat the process for the child with the most weight above it again and again until we find a tip, which will be returned as the selected tip.

Let us follow an example. Assume that all blocks weigh the same, and consider this tree:

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

The chain selection rule's job is to output one of the tips $$T_1,\ldots,T_5$$. You are welcome to check that $$T_5$$ has the longest/heaviest chain below it, so this is the tip that HCR will return. What about GHOST?
