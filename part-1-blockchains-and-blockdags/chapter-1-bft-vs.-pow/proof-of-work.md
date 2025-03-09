# Proof-of-Work (PoW)

Before we get going, do me a solid and forget _anything_ you _ever_ heard about PoW. Most of it is wrong anyway. Before protesting, answer this one question: what are the security properties of the PoW protocol? If you think you know the answer then you already lost. It was a trick question. _PoW is not a consensus protocol at all_. So please, just clear your mind and let us take this from the top.

## Sybil Resistance

The 1973 novel Sybil by Americal journalist Flora Rheta Schreiber documents the story of the pseudonymous Syb Dorsett, and her experience being treated for dissociative identity disorder. Sybil was a remarkable case: coherently displayed _sixteen_ independent personalities oblivious of each other.

Quite tactlessly, this inspired the term _Sybil attack_, describing a single attacker taking on many identities to interfere with online protocols (such as creating millions of accounts to sway a Twitter poll).

The term _Sybil resistance_ describes any means to counter the possibility of a Sybil attack, either by making them impossible, or, more commonly, by making them extremely expensive.

The most common form of Sybil resistance is simply maintaining an _access list_ of allowed participants and tracking their participation. For example, consider a presidential election. If we allow ourselves to simplify the process a bit, there is one ballot per voter, that can be used _once_ for voting. This is implemented by an _authority_ that issues these ballots and assures they are not reused.

This kind of System is called _permissioned_, and it is easy to realize that a permissioned system can never be decentralized. After all, there is a central authority that is in charge of granting permission, decide when it is abused, and sanction the abuser.

But we want to build _decentralzied_ systems, so we must come up with better forms of Sybil resistance.

One thing to keep in mind is that BFT was designed to be _distributed_, but not necessarily _decentralized_. In particular, the BFT process happens _after_ the list of participants has been determined, whereas Sybilness is used to form the list of participants.

BFTs precede the ambitions of a cryptographic decentralized economy. They were originally motivated by the need to distribute large computations to a cluster of many computers in a way that would remain robust even if some of the computers were faulty. In such architectures, there is an authority that has an access list of computers that participate in the computation and is responsible for piecing together the solution.

To advance from distribution to decentralization, we need a new paradigm. A way to allow users to _attempt_ as many identities as they want, but somehow make it difficult to produce these identities. A system that inherently limits the ability of creating new identities without keeping any list of known or allowed identities.

The next natural thought is that perhaps we should make it so that creating an identity requires some _effort_? This thought naturally leads to the consideration of an interesting recent (at that time) work with completely different motivations.

In 1992, Cynthia Dwork (pronounced Dvork) and Moni Naor published a paper called [Pricing via Processing (or Combatting Junk Mail)](https://www.wisdom.weizmann.ac.il/~naor/PAPERS/pvp.pdf), where they introduced a new idea that would later evolve into what we know as proof-of-work: requiring that each identity provides a proof that requires _some effort_ to produce. Dwork and Naor's motivation was combatting junk mail. They figured that if they could require each e-mail to produce proof that requires say one-tenth of a cent of electricity, then it should not bother the average user too much. However, a spammer trying to send a hundred million e-mails will find themselves $100k short. But of course junk mail is just an application, the core idea was that by imposing processing requirements, we could mitigate spam, whether of voter identities or advertisements for enhancement performing pills. Thus proof-of-work was born.&#x20;

Proof-of-work is a _fascinating_ form of Sybilness, but it took a while before it caught the eye of the emerging movement of cypherpunks seeking to create a decentralized economy, and even when it did, it took about 16 years since Dwork and Naor's paper and until the pseudonymous Satoshi figured out how it could be used for a brave new form of consensus, a _permissionless one_.

Since then, many other forms of Sybil resistance emerged, most famously proof-of-stake. But the crypto landscape is not short of all sorts of mechanisms for proof-of-X. Some of them rely on physical resources, like proof-of-time-and-space that relies on provably sacrificing storage space for a given amount of time. Others, like proof-of-stake, proof-of-coverage, proof-of-reputation, and the rest rely on some intrinsic value such as coin holding, accrued reputation, and so on.

{% hint style="info" %}
At this point, a harsh statement is unfortunately due: Sybilness is _serious business_. New forms of Sybil resistance should be analyzed with great care before being rolled into production. Anyone with some experience in decentralized consensus design knows that careless use of a Sybil resistance mechanism could make it coercible or gameable. Even layering two forms of proof-of-work can be completely broken if done incorrectly, and that risk grows by orders of magnitude when using a home-brewed new form of Sybilness. Yet, the industry landscape is packed with projects that rely on new forms of Sybilness for which no analysis or documentation is available. Personally, I steer clear of any proof-of-X form that does not have sufficient resources to convince me that it was thoroughly scrutinized. Unfortunately, this is a _very_ rare occurrence.
{% endhint %}

## PoW as a Global Timer

I surely don't know what went through Satoshi's pseudonymous mind at the time, but if I had to guess what was his _eureka!_ moment, I would guess that it was the realization that PoW can use to prove the _passage of time_.

Satoshi, and the cypherpunks that preceded him, imagined a global ledger that _anyone_ can append to. The problem is, of course, that two different messages could be appended at the same time, causing inconsistencies and disagreements. (Don't fret if you are not clear on that, we will dive deep into how Bitcoin works in a moment.)

Satoshi's thought was presumably along the lines of "what if we can somehow _force_ the message to be so far apart that this could never happen? Too bad there is no way to enforce such a policy... _or is there_?" The key realization here is that by requiring enough work, we can guarantee that enough time passed...

...to an extent. There are several issues with this idea:

First, it cannot _actually_ _guarantee_ how much time it took to create the block. The guarantee is quite weaker: that the _average_ time between blocks is some predefined interval. In fact, [the math shows](../../supplementary-material/math/probability-theory/the-math-of-block-creation.md) that the block creation process is very noisy, which is likely what caused Satoshi to choose such a long block delay.

Second, it actually cannot guarantee even _that_. To use PoW as an accurate timer, one must know _exactly_ how many hashes are computed per unit of time, a quantity that _constantly changes_ and is _impossible to measure_. PoW chains are forced to handle this by using a _difficulty adjustment algorithm_: a way to approximate the changes in global hashrates through changes in block creation rates. These algorithms are hard to implement (in part, because we can't really _know_ when a block was created since it is impossible to authenticate timestamps), and can enable new attack vectors.

Third, this kind of consensus doesn't fit nicely into the theoretical framework that existed at the time. Until Bitcoin, consensus algorithms were expected to provide _deterministic finality_, some line in the sand after which it is _guaranteed_ that a transaction will not revert. Bitcoin can "only" provide _probabilistic_ finality, the guarantee that the probability a transaction reverts becomes negligible very fast. This sounds a bit scary to muggles, but a cryptographer knows that essentially _all modern cryptography_ relies on "up to a negligible security." (I mean, it doesn't matter how strong your password is, there is always _some chance_ a hacker would guess it through sheer luck.)

For me, that third observation is a source of great joy. It initiated a brand new theory of _probabilistic consensus_ that is still in its formative stage. It caused decade-old models to be revised and refined and made the theory much more interdisciplinary. I would say that the main reason I found myself in the field, to begin with, is that _I love probability theory_. If consensus was to remain deterministic and boring, this book might have never been written. Accordingly, the rest of the chapters in Part 1 are dedicated to guiding the reader through this brave, new world. But before that, we finish the current chapter, and our discussion of PoW vs. BFT.
