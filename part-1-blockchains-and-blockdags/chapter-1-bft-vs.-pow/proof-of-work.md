# Proof-of-Work (PoW)

Before we get going, do me a solid and forget _anything_ you ever heard about PoW, most of it is wring anyway. If you disagree then answer this one question: what are the security properties of the PoW protocol? If you think you know the answer then you already lost. It was a trick question. _PoW is not a consensus protocol at all_. So please, just clear your mind and let us take it from the top.

## Sybil Resistance

The 1973 novel Sybil by Americal journalist Flora Rheta Schreiber documents the story of the pseudonymous Syb Dorsett, and her treatment of dissociative identity disorder. Sybil coherently displayed sixteen independent personalities oblivious of each other.

Quite tactlessly, this inspired the term _Sybil attack_, describing a single attacker taking on many identities to interfere with online protocols such as creating millions of accounts to sway a Twitter poll.

_Sybil resistance_ is any mean to counter the possibility of such attacks. The most common form of Sybil resistance is maintaining a list of people allowed to participate, and track their participation. For example, in an election there is one ballot per voter, that can be used _once_ for voting. Any system applying this approach is called _permissioned_, and it is not easy to get convinced that a permissioned system has no hope of being decentralized, because _someone_ has to handle the permissions.

This means that any decentralized system must find some other way to resist Sybil attacks.

Before the era of decentralized protocols, BFT was used for distributed yet permissioned protocols. Even though many nodes could participate, there was still an authority that handles _who the nodes are_. The motivation BFT at this point was more modest than a trustless economy: to make distributed computations on large clusters of machines, in an era where faulty machines and transmission lines were much more common.

Without maintaining an access list, the next natural idea is to somehow make creating each identity _costly_, not costly enough to deter anyone from legitimately participating, but costly enough to make amassing a formidable quantity of identities prohibitive.

In 1992, Cynthia Dwork (pronounced Dvork) and Moni Naor published a paper called [Pricing via Processing (or Combatting Junk Mail)](https://www.wisdom.weizmann.ac.il/~naor/PAPERS/pvp.pdf), where they introduced a new idea that would later evolve to what we know as proof-of-work: requiring that each identity provides a proof that requires a lot of time to produce. Dwork and Naor, did not use it for the purpose of voting, but provided a different motivation: combatting spam. They figured that if they could require each e-mail to produce a proof that requires one tenth of a cent of electricity, then it should not bother the average user too much. However, a spammer trying to send a hundred million e-mails will find themselves $100k short.

Proof of work is a fascinating form of Sybilness, but it took a while before it caught the eye of the emerging movement of cypherpunks seeking to create a decentralized economy, and even when it did, it took about 16 years since Dwork and Naor's paper and until the pseudonymous Satoshi figured out how it could be used for a brave new form of consensus, a _permissionless one_.

Since then, many other forms of Sybil resistance emerged, most famously proof-of-stake. But the crypto landscape is not short of all sorts of mechanisms for proof-of-X. Some of them rely on physical resources, like proof-of-time-and-space that relies on provably sacrificing storage space for a given amount of time. Others, like proof-of-stake, proof-of-coverage, proof-of-reputation and the rest rely on some intrinsic value such as coin holding, accrued reputation, and so on.

At this point, a harsh statement is unfortunately in order: Sybilness is serious business, and new forms of Sybil resistance should be analyzed with great care before being rolled into production. Anyone with some experience in decentralized consensus design knows that a careless use of a Sybil resistance mechanism could make it coercible or gameable. Even layering two forms of proof-of-work can be completely broken if done incorrectly, and that risk grows by orders of magnitude when using a home-brewed new form of Sybilness. Yet, the industry landscape is packed with projects that rely on new forms of Sybilness for which no analysis or documentation is available. Personally, I steer clear of any proof-of-X form that does not have sufficient resources to convince me that it was thoroughly scrutinized. So far, this has not happened.

## PoW as a Global Timer





