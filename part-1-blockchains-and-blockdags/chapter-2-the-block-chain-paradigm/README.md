# Chapter 2: the Block Chain Paradigm

Now that we understand the [Sybil resistance provided by PoW](../chapter-1-bft-vs.-pow/proof-of-work.md#sybil-resistance), we turn to the next pressing question: how to apply it to create decentralized ledgers.

It is at this point that my exposition diverges from more traditional ones. Instead of jumping right into Bitcoin and how it works, I want to present an _abstract framework_ called the _Block Chain paradigm_ that captures the general structure of a block chain. The abstract approach has many advantages:

* It draws a line between general properties of block chains, and properties unique to a specific block chain,
* It allows us to define notions of security without appealing to a particular protocol or construction, we could then use these definitions to reason about _any_ block chain, and
* It is readily generalizable, setting the ground for exploring the bloc&#x6B;_&#x44;AG_ paradigm in the next chapter.

The idea of the block chain paradigm is that the differences between block chain protocols all reduce to a _chain selection rule_. The two most ubiquitous chain selection rules are Bitcoin's _heaviest chain_ rule and the GHOST rule, though there are others.

Once we understand what a chain selection rule _is_, we provide definitions and framework to reason about their security. In particular, we will talk about safety, liveness, and transaction finality (a.k.a. confirmation times). But before that, we will spend a bit of time pontification on what security even _means_.

Having built our understanding of the security of block chains in the abstract, we finally introduce the heaviest chain and GHOST rules, and discuss their security informally.

At this point the reader might garner some confidence in their understanding of security. This is definitely a problem. We fix that problem by discussing _selfish mining_, a (then) unexpected attack vector on Bitcoin that shows that no matter how solid you think your understanding of security is, there could always be attack vectors that are not prohibited by the definition, and still have undesirable consequences. Hopefully, this should instill some humility into the reader, that they will pass along to anyone who claims this or that idea is "secure" without having done their due diligence.

We conclude the chapter with a somewhat detailed sketch of the security proof of Bitcoin. This section is a bit more heavy on the math, but also completely skippable.
