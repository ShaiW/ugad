# Merkle Trees

Merkle trees are a clever application of hashes and are ubiquitous throughout cryptocurrencies. They have been a cornerstone of the architecture since as early as the Bitcoin whitepaper.

Recall that a _hash_ $$\mathsf{H}$$ is some procedure that takes arbitrary data (for example, a block header), and outputs a binary string of some _fixed_ length $$\ell$$. The fact that the length is fixed is _crucial_. It means that not matter what you put _into_ $$\mathsf{H}$$, you get the _same number of bits_ back.

Other than that, we assume that $$\mathsf{H}$$ has another useful (some would even say _magical_) property: it is _collision-resistant_. A _collision_ is simply two _distinct_ inputs $$x\ne y$$ such that $$\mathsf{H}(x)=\mathsf{H}(y)$$. Defining collision-resistance _precisely_ is as cumbersome as it is unnecessary. For our intents and purposes, it suffices to understand this notion as the proclamation that even if humanity diverted its entire resources to finding a collision for thousands of years, the probability of actually finding one remains smaller than throwing a hundred dice to find them all balanced on one of their corners.

In other words, if we don't know $$x$$ and $$y$$, but we know that $$\mathsf{H}(x) = \mathsf{H}(y)$$, we can safely infer that $$x=y$$.

Let us pave a way to Merkle trees by first working through a simpler problem.

Say I stored a large file $$F$$ on a remote server and erased it from my computer. A year later, I needed the file, so I downloaded it. Let $$F'$$ be the file that I downloaded from the server, how can I be sure it is identical to $$F$$, and that no one tempered with my data?

I can do the following: before erasing $$F$$, I compute and store $$\mathsf{H}(F)$$. That way, if I download a file $$F'$$ that is supposed to be a copy of $$F$$, I can verify its authenticity by computing $$\mathsf{H}(F')$$ and verify that it is identical to the $$\mathsf{H}(F)$$ I stored. I only know $$F', \mathsf{H}(F)$$, and $$\mathsf{H}(F')$$, but the fact that $$\mathsf{H}(F)=\mathsf{H}(F')$$ together with collision-resistance, implies that $$F'=F$$.

How can we extend this idea to accommodate _many_ files $$F_1,\ldots,F_n$$.

One way would be to compute and store $$\mathsf{H}(F_1),\ldots,\mathsf{H}(F_n)$$, but this would require us to store $$n$$ hashes, taking $$\ell\cdot n$$ bits! In other words, the storage we require grows linearly.

Another thing we could do is to hash a single concatenation of all files $$H(F_1\|\ldots\|F_n)$$. This solution indeed only requires $$\ell$$ bits of storage, but it has another problem: I must download _all files_ to verify them. I would _really_ like a way to download only _one_ of the files and verify it.

Is there a way that on the one hand allows us to verify each file independently, and on the other does not require a lot of storage? Yes there is, a Merkle tree.

Merkle trees are simple to understand but a bit hard to explain. I find that the best way is to go through the details of a small example: four files. After explaining it in some detail, it suffices to describe the bold strokes of the general construction, leaving the details to the reader.

So Let us start with four files, $$F_1,F_2,F_3,F_4$$. Why four? We first compute the hashes $$\mathsf{H}(F_1),\ldots,\mathsf{H}(F_4)$$, and place the result in the leaves of a binary tree:

<figure><img src="../../../.gitbook/assets/143.png" alt=""><figcaption></figcaption></figure>

To make things simple, we mark the value of each node with $$v_w$$ where $$w$$ is a binary string describing the path from the root to that node: reading left to right, a $$0$$ denotes descending left whereas a $$1$$ denotes descending right. For example, for the leftmost leaf we have $$H(F_1) = v_{0,0}$$, because starting from the root, we descend left twice to reach it. With this notation, we can define the value of each node to be the hash of its two children.

<figure><img src="../../../.gitbook/assets/144.png" alt=""><figcaption></figcaption></figure>

The value $$v$$ is called the _Merkle root_, and it is _the only value we store_. That is, no matter how many files we backup, we will always need exactly $$\ell$$ bytes.

Now what if we want to download and verify the file $$F_3$$? The server sends us the file, along with the values $$v_{1,1}$$, and $$v_0$$. That is, the values of all nodes that are _exactly once step away_ from the path from the root to the hash of $$F_3$$. The data $$(v_{1,1},v_0)$$ is called the _Merkle proof_.

<figure><img src="../../../.gitbook/assets/145.png" alt=""><figcaption></figcaption></figure>

&#x20;Why is the proof useful? Note that we know $$F_3$$, so we can compute $$v_{1,0} = \mathsf{H}(F_3)$$. We know $$v_{1,1}$$ from the proof, so we can now compute $$v_1 = \mathsf{H}(v_{1,0},v_{1,1})$$. We know $$v_0$$ from the proof, so we can finally compute $$v=\mathsf{H}(v_0,v_1)$$ and compare it to the Merkle root I stored.

A quick inductive argument shows that if there is a fake Merkle proof for another file $$F$$ that somehow passes this validation, then _somewhere_ along the path we will find a collision in $$\mathsf{H}$$, contradiction collision-resistance.

<details>

<summary>Proof for four files</summary>

Let $$F'_3$$ be the file we got. Let $$v_{1,1}'$$ an $$v_0'$$ be the fake Merkle proof. Let $$v_1'$$ and $$v_{1,0}'$$ be the values we compute through the verification process. That is $$v'_{1,0}=\mathsf{H}(F_3')$$ and $$v'_1 = \mathsf{H}(v_{1,0}',v_{1,1}')$$.&#x20;

If the verification passes, we get that $$v = \mathsf{H}(v_0,v_1) =\mathsf{H}(v_0',v_1')$$, so from the collision resistance we get that $$v_1 = v_1'$$ (and $$v_0=v_0'$$, which is not important for the proof, but _is_ important for other reasons). The second equality can be rewritten as $$\mathsf{H}(v_{1,0},v_{1,1}) = \mathsf{H}(v'_{1,0},v'_{1,1})$$ from which we learn that $$v'_{1,0} = v_{1,0}$$, or in other words $$\mathsf{H}(F_3') = \mathsf{H}(F_3)$$. We use collision-resistance for a third time to learn that $$F_3' = F_3$$ as needed.

</details>



Note that the size of the Merkle proof is one less than _the height of the tree_. For four files, the size of the Merkle proof is $$2\cdot \ell$$. For more files, the process looks like this.

<figure><img src="../../../.gitbook/assets/146.png" alt=""><figcaption></figcaption></figure>

For $$n$$ files, the size of the only thing we need to store — the Merkle root — remains _one_ hash. The size of the proof increases by _one hash_ every time the number of files _doubles_. In other words, the Merkle proof contains $$\log n$$ hashes. Given the file $$F$$, and the Merkle proof, I can easily compute all the hashes along the Merkle path, all the way up to the root. So if I have, say, a thousand files. The Merkle root will be _nine to ten hashes_ long.

This cool trick is used in many ways in cryptography and cryptocurrencies. One example is the block _header_. Recall that when we [described how proof-of-work works](../../../part-1-blockchains-and-blockdags/chapter-1-bft-vs.-pow/how-pow-works.md), we said that the block header must contain information that ties it to the contents of the block, without making the header too large. Well, _this is it_. When creating the block, the miner computes a Merkle root of all transactions therein and places it in the block. When given a list of transactions that were allegedly the contents of this block, one could compute the Merkle tree for themselves to verify this.

At this point some of you might be asking: "wait, wasn't the _entire motivation_ to verify files _separately_? If we just verify the entire block content, why not just hash all of the transactions together?". Ah, nice observation. The motivation for using a Merkle tree is exactly so that a user could verify a transaction was in a given block without having to store and transmit the entire block content. Instead, they only store the Merkle proof of the transaction that interests them. With this proof in hand, the user can verify that the transaction is present in a block with access to nothing but the Merkle root, which is stored in the header. This is the idea of _simple perfect verification_ (SPV) nodes. They only store block headers and discard of block data, but thanks to Merkle proof, users can still verify that their transaction is on the blockchain.
