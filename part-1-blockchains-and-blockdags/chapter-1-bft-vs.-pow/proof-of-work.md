# Proof-of-Work (PoW)

Before we get going, do me a solid and forget _anything_ you _ever_ heard about PoW. Forgive my insolence, but most likely most of it is wrong anyway.&#x20;

Oh, you find this objectionable? Fine, then let me test you. Answer this one question: what are the security properties of the PoW protocol?&#x20;

If you think you know the answer, then you have already lost. It was a trick question. _PoW is not a consensus protocol at all_. It is a _necessary building block_ for PoW protocols such as the longest-chain rule and GHOST. But in itself, it is something else, more fundamental, the stuff protocols are made of.

So what is it? I'm getting there. Clear your mind and let us take this from the top.

***

<figure><img src="../../.gitbook/assets/image (54).png" alt="" width="212"><figcaption><p>My gratitude to <a href="https://qu.ai/">Quai network</a> for sponsoring this segment of the book</p></figcaption></figure>

***

## Distribution Vs. Decentralization

BFT protocols precede the ambitions of a cryptographic decentralized economy. They were initially motivated by the need to distribute large computations to a cluster of many computers in a way that would remain robust even if some of the computers were faulty.

Such protocols are called _distributed_: the final state is determined and computed by a collection of parties, where each party operates independently of all the others, up to an exchange of messages. In particular, in many protocols, no one party (or small collusion of parties) can coerce the outcome.

Does this make these protocols _decentralized_? Not necessarily. In the original BFT systems, there is an authority responsible for maintaining and enforcing an access list of participating computers. Such systems are called _permissioned_, and while they _are_ often distributed, they cannot be reasonably called decentralized.

_Decentralization requires_ _permissionlessness_. But we cannot just allow _anyone_ to vote without any regulation of voter eligibility, as this can easily create attack vectors. For example, if we use X polls to make a decision, nothing is protecting us from bot armies swaying the result for practically free. We need to find a way to prevent these attacks without resorting to centralized white lists. Before that, we might want to name this central aspect.

## Sybil Resistance

The 1973 novel Sybil by American journalist Flora Rheta Schreiber documents the story of the pseudonymous Syb Dorsett. Dorsett suffered from a remarkably severe manifestation of dissociative identity disorder: she coherently exhibited _sixteen_ independent personalities, all oblivious of each other. The novel is a fascinating and heart-wrenching account of Dorsett's treatment.

Quite tactlessly, her story inspired the term _Sybil attack,_ describing a single attacker taking on many identities to interfere with online protocols. Consequently, _Sybil resistance_ is any countermeasure to such an attack.

We have already seen one form of Sybil resistance: centralization. If there is one entity that regulates the voting procedure, then there is no problem. This is actually the best possible Sybil resistance.

But we want to create _decentralized_ systems. Is there any hope?

The first limitation one must realize is that perfect Sybil resistance, assuring one vote per voter, is impossible. In fact, the _linear_ Sybil resistance is the best a non-permissioned countermeasure can provably provide. (The word "provable" is there to exclude administrative solutions such as "execute via Guillotine anyone caught committing a Sybil attack"). This means that while faking identities is possible, the cost is directly proportional to the number of votes you need to fake.

This is arguably very different than the Sybil resistance you get for, say, online polls. In most cases, a 100,000 votes attack doesn't cost much more than a 10,000 votes attack. The actual costs are in obtaining and maintaining a network of bots. Activating them is not the costly part.

"But can't we just charge 0.1 cents per vote?" one might ask, "This will definitely make costs rise linearly". But then we realize that to actually do that, we need some authority that will process charges, maintain a list of paying voters, and so on. In the words of a celebrated scientist:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

But just because that idea doesn't work, it doesn't mean that it is not in the right direction! Increasing the "per-vote" cost is exactly what we are looking to do. We just have to be a bit clever about it. Using money means adhering to an administrative system. Validating and processing payments requires centralized resources. Generally speaking, if you have to get a permit from any entity for your vote, then you've already lost. So we cannot use money. We need to find some _other way_ to make voting costly. A way that can be verified without requiring access to any centralized records.

## Pricing Via Processing

In 1992, Cynthia Dwork (pronounced Dvork) and Moni Naor realized that the answer is, quite literally, at their fingertips. In their paper [Pricing via Processing (or Combatting Junk Mail)](https://www.wisdom.weizmann.ac.il/~naor/PAPERS/pvp.pdf), they came up with a brilliantly simple idea: use _computation_ as a resource.

Say that to send an email, we must append to it the result of some computation. Without this computation, servers will disregard the email as spam and discard it. For such a system to work, the requested computation must satisfy some conditions. Dwork and Naor noted this and proposed a particular approach that is very similar to how Bitcoin works under the hood. We explore the details in the next section.

But the key property is that the computation requires a controllable amount of computational effort. Then you could set it up such that the energetic cost of sending an email is around, say 0.1 cents. For the everyday user, this will seldom accumulate to more than $1/month. Even an office sending tens of thousands of emails per month will only pay several dozen bucks. However, a spammer trying to send out one million messages will find themselves $100k short.

Junk mail is a friendly example, but this approach has a multitude of applications. The core idea is that by imposing processing requirements, we can mitigate spam. This spam could be advertisements for performance-enhancing pills, but it could be many other things. Say, votes?

It took a while for the concept of Sybilness through processing to catch the eye of the emerging cypherpunk movement as they attempted to design a decentralized currency. And even when it did, it took a few more years to find the correct way to piece everything together. But in 2009, 16 years after Dwork and Naor's paper, the pseudonymous Satoshi Nakamoto publicly posted the Bitcoin whitepaper, and thus, proof-of-work was born.

## PoW as a Global Timer

No one knows who Satoshi is or what went through her mind when she invented Bitcoin. But if I had to guess what realization was the _eureka!_ that started this ball rolling, I would go with the insight that PoW can _prove the passage of time._

Satoshi and the cypherpunks who preceded her imagined a global ledger that anyone could append to. This is all fun and well until two messages are appended at the same time, causing disagreement. (Don't fret if you are not clear on that. We will dive deep into how Bitcoin works in a moment.)

Satoshi's thought was presumably along the lines of "what if we can somehow _force_ the messages to be so far apart that this could never happen? Too bad there is no way to enforce such a policy... _or is there_?" The key realization here is that by requiring enough work, we can guarantee that enough time passed...

...to an extent. There are several issues with this idea:

First, it cannot _actually_ _guarantee_ how much time it took to create the block. The guarantee is quite weaker: that we can control the _average_ time between blocks. In fact, [the math shows](../../supplementary-material/math/probability-theory/the-math-of-block-creation.md) that the block-creation process is very noisy (which is what led Satoshi to choose such a long block delay). This is not a technicality that can be resolved. This noise is ingrained into any possible proof-of-work.

Second, it cannot even guarantee _that_! To use PoW even as an approximate timer, one must know _exactly_ how much effort is _globally exerted_ to create the next message. This quantity is not only difficult to measure but also _constantly changing_. The global hashrate is subject to many external influences, from energy prices to legislation. PoW ledgers handle this via _difficulty adjustment_: a heuristic that approximates the change in global hashrate from observed block creation rates. Difficulty adjustment is a tricky art with its own separate theory. We will overview how difficulty adjustment works in Bitcoin in the next chapter, hopefully demonstrating how nuanced a task it is.

Third, this is a new form of consensus that does not fit nicely into preexisting theoretical frameworks. This is manifest in two ways. First, the BFT discussion we have seen assumed _deterministic finality_. That is, convergence is guaranteed. Proof-of-work cannot provide this kind of finality; it relaxes it to a probabilistic finality, where the probability of reversion is "just" lower than the chance of being run over by a bus while struck by lightning before getting out of bad. But that's actually not new. BFT protocols with probabilistic finality have been researched since the 1980s, and there are several central examples. The real issue is the _subjectivity_. In distributed computers, it was always the norm that there was some globally agreed-upon criterion for when a transaction is "final" that everyone applies. This is not true for proof-of-work, where every party can set its own acceptance policy. This might seem like picking nits, but this is actually a fundamental difference that required developing entirely new approaches to distributed protocol security to even reason about what a PoW consensus can and cannot achieve.

For me, that third observation is a source of great joy. It initiated a brand new theory of _probabilistic consensus_ that is still in its formative stage. It caused decade-old models to be revised and refined, making the theory much more interdisciplinary. I would say that the main reason I found myself in the field, to begin with, is that _I love probability theory_. If consensus were to remain deterministic and boring, this book might have never been written.
