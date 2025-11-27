# Merged Mining and Multi-Mining

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

This segment is in a **pre-alpha** phase. None of what you read below is guaranteed to be correct in any way or be a part of the final product. Read with extreme caution or come back later.

***

_Merged mining_ is a technique that allows a _primary_ PoW chain (typically a small chain) to piggyback on the mining efforts of an _auxiliary_ chain (typically a large chain, most commonly Bitcoin). The idea is to accept attempts at mining the auxiliary chain as valid headers to the main chain. Since auxiliary miners are already doing this work anyway, they get to participate in the main chain "for free". This benefits both chains, as mining the main chain becomes more profitable.

On paper, this seems like a great idea, but in practice, it has limitations and even risks.

<div align="right"><figure><img src="../../.gitbook/assets/image (54).png" alt="" width="106"><figcaption><p>My gratitude to <a href="https://qu.ai/">Quai network</a> for<br>sponsoring this segment of the book</p></figcaption></figure></div>

Merged mining was first introduced by [Namecoin](https://www.namecoin.org/), a project that launched in 2011 to create a decentralized database of maintainable, consistent cryptographic identities. While the utility of Namecoin is worthy of a discussion, it seems that the most impactful observation they made is that _the same work can secure two chains_. It is very simple, on a technical level, to use byproducts of Bitcoin mining as valid Namecoin work. However, this decision arguably played a significant role in Namecoin's decline, as the network's security is overly reliant on an external source, which also had consequences for decentralization.

Future incarnations of merged mining proposed a more holistic approach called _multi-mining_, allowing the same chain to be mined in _several different ways_, limiting the consequences of merged mining.

In this section, after explaining in more detail how merged mining works, we consider its consequences for the main chain.

## Bitcoin AuxPoW in Namecoin

We start with the simplest form of merged mining: Namecoin's original approach. We need a way for the main chain to accept work from auxiliary chain miners without interfering with their auxiliary chain mining. Such a form of mining is often called _auxiliary-proof-of-work_ (AuxPoW).

Following the reasoning for how [regular PoW works](how-pow-works.md), we find some issues that need to be considered:

* The difficulty target in the header comes from the aux chain (so we call it $$T_\text{aux}$$), but we need to maintain a separate difficulty target for the primary chain $$T_\text{prim}$$.
* The Merkle transaction root couples the header to the _auxiliary_ block data. We also need to couple it to the main chain data somehow.

That's not hard to accomplish, but it requires some attention to detail. Working through this is a good exercise in applying the principles we learned so far. I will explain specifically how Namecoin's AuxPoW is implemented. Most forms of AuxPoW are slight variations, adapted to the fine structure of the primary and auxiliary chain as needed.

### Merkle Tree Huggers

Recall that to couple the block data to the block header, Bitcoin headers contain a [Merkle transaction root](../../supplementary-material/computer-science/page-3/merkle-trees.md) (MTR). This Merkle root seems to be a solution to all our problems: you can couple any arbitrary data $$d$$ to the block header by adding a new leaf with the value $$\mathsf{H}(d)$$, and attach the corresponding Merkle proof $$P(d)$$ to the data. When validating the data $$(d,P(d))$$, add a step for checking that the Merkle proof goes through with respect to MTR.

This all sounds like simple stuff, until we realize that _we can't_ add leaves to the transaction Merkle tree.  If we try to, we make the block invalid! Recall that one step of validating a Bitcoin block is  _recomputing_ the MTR from the transaction and checking that it matches the MTR written in the header. But if we added additional leaves, then we modified the MTR!

Fortunately, there's a standard solution for that: embed the information you need _inside_ a transaction. That way, the standard MTR computation automatically couples the header to your data. But what transaction should we use? In Bitcoin, the first transaction in each block is a special transaction called the _coinbase transaction_, which miners use to claim the block rewards and fees. Since miners must create a coinbase transaction (or relinquish their reward) anyway, it makes sense to embed the data in it.

### The Bitcoin AuxPoW Recipe

The previous section gives us enough to put together a recipe for converting any PoW chain into a Bitcoin AuxPoW chain. There are other recipes, but they are all very similar to this one.

{% hint style="info" %}
This recipe is a simplified version of what's used in practice. The reason for that is another issue that merged mining needs to address, which I did not discuss: ambiguity. In essence, it means that if _several_ prime chains use the same auxiliary chain, we need to be able to easily and unambiguously tell which of the coins is merge mined. I will discuss this some more after I describe the recipe.
{% endhint %}

Consider a standard PoW chain. A new incoming block $$B$$ is comprised of two parts: $$B_\text{header}$$ and $$B_\text{data}$$. We keep the standard structural assumptions: validating the validity of the chain only requires examining the headers, and given $$B_\text{data}$$, we can validate that it indeed contains the data expected by $$B_\text{header}$$. In particular, we expect $$B_\text{header}$$ to contain a difficulty target $$T_\text{prim}$$. We don't particularly care how the chain works. We only presume that it follows this general structure.

To convert it into a Bitcoin AuxPoW, we redefine the header to be an appropriately cooked-up _Bitcoin_ header, and attach the original _header_ to the block _data_, along with some useful auxiliary data. Let's flesh it out.

The AuxPoW header is a _Bitcoin_ header $$B_\text{aux}$$, with a payload $$P$$ in its coinbase transaction. The AuxPoW block _data_ contains the following information:

* The original data $$B_\text{data}$$ _and_ the original header $$B_\text{header}$$
* The coinbase transaction (which has $$P$$ embedded into it)
* A Merkle proof for the Bitcoin coinbase transaction

When validating a block, we check:

* $$B_\text{aux}$$ has the _semantics_ of a valid Bitcoin header. By "semantics," I mean that we check it has the expected structure, but skip any contextual texts. For example, we check that the header has a pointer to a parent block, but we don't check if that block actually exists. We check that the header has an MTR, but we don't retrieve the Bitcoin block.
* $$\textsf{H}\left(B_\text{aux}\right) < T_\text{prim}$$. Note that we use the _primary chain_ difficulty target on the _auxiliary_ chain header
* The validity of $$P$$, using the coinbase Merkle proof
* Any remaining validity test from the original chain

{% hint style="info" %}
The approach embodied in this recipe is actually a bit outdated. There is a well-hidden trade-off between _Bitcoin semantics_ and _header-only validation_.

Bitcoin semantics means that the auxiliary chain header has identical semantics to a Bitcoin header, and any additional information required for verification is appended to the block _data_. In the early days of Namecoin, this was considered a virtue, as most blockchain-related code was Bitcoin-specialized, so maintaining the semantics made reusability much easier.

Header-only validation unsurprisingly means that the block headers are sufficient on their own to validate the consensus, and you only need the data to access transactions.

As the landscape progressed, and more major projects and ideas started to saturate the market, Bitcoin semantics became less and less relevant. On the other hand, SPV proofs, NiPoPoWs, and other technologies made header-only validation increasingly useful.

I maintain the Namecoin version for ease of presentation, but modern chains largely prefer header-based consensus over Bitcoin semantics. Modifying the recipe to reverse the trade-off (give up Bitcoin semantics for header-only verification) is straightforward and remains as an exercise for the reader.
{% endhint %}

{% hint style="info" %}
This solution assumes there is only one primary chain. If several primary chains use this recipe, each block can merge mine into only _one_ of them. It is possible to extend this approach to allow _each_ block to merge mine into _many_ primary chains, but some subtle tricks are required to keep everything coordinated. The rough idea is to use a _huge_ but _mostly empty_ Merkle tree of commitments (the professional term is a sparse Merkle tree), and use the header data to choose a leaf for each merged chain in an unpredictable manner. That way, we naturally avoid different chains using the same leaf, without requiring any coordination.

I guide the reader through this construction in the exercises. \[No I don't lol, but I will get to this when I rewrite the exercises section]
{% endhint %}



