# Chapter 2: the Block Chain Paradigm

Your mental image of Bitcoin is probably along these lines: miners discover blocks, either by hearing about them from their peers or by creating them. Whenever a miner discovers a block, they broadcast it to their peers and start mining over it. If two competing blocks are created, the miner keeps mining above the block they heard about first, unless the chain above the other block becomes longer, in which case they switch.

If you don't have a mental image of Bitcoin, and don't feel like unpacking this condensed description, I recommend this video:

{% embed url="https://www.youtube.com/watch?v=bBC-nXj3Ng4" %}

This description is not unreasonable, but it is rather _clunky_. It requires telling this story about entities, and take into consideration complex ideas like _time_. It also has a gaping hole: _why_ should miners follow the rules at all? What justifies this assumption?

If you try to write a similar description for how a different protocol, say GHOST, works, you will find that, annoyingly enough, the description will be _exactly the same_ except this fragment of a sentence: "...unless the chain above the other block becomes longer...".

The _only_ difference between the Bitcoin protocol, the GHOST protocol, or most other block _chain_ protocol, is the way they handle conflicts. The idea of the block chain paradigm is to make the tie breaking rule _abstract_, but the rest concrete. The block chain paradigm capitalizes on this observation. It provides a chassis to fit different chain selection rules into, and tools to quantify how well these chain selection rules achieve certain properties.

The abstract approach has many advantages:

* It draws a line between general properties of block chains, and properties unique to a specific block chain,
* It allows us to define notions of security without appealing to a particular protocol or construction, we could then use these definitions to reason about _any_ block chain, and
* It is readily generalizable, setting the ground for exploring the bloc&#x6B;_&#x44;AG_ paradigm in the next chapter.

Once we understand what a chain selection rule _is_, we provide definitions and framework to reason about their security. In particular, we will talk about safety, liveness, and transaction finality (a.k.a. confirmation times). But before that, we will spend a bit of time pontification on what security even _means_.

Having built our understanding of the security of block chains in the abstract, we finally introduce the heaviest chain and GHOST rules, and discuss their security informally.

At this point the reader might garner some confidence in their understanding of security. This is definitely a problem. We fix that problem by discussing _selfish mining_, a (then) unexpected dynamic in Bitcoin that shows that no matter how solid you think your understanding of security is, there could always be attack vectors that are not prohibited by the definition, and still have undesirable consequences. Hopefully, this should instill some humility into the reader, that they will pass along to anyone who claims this or that idea or technology is "secure" without having done their due diligence.

We conclude the chapter with a somewhat detailed sketch of the security proof of Bitcoin. This section is a bit more heavy on the math, but also completely skippable.
