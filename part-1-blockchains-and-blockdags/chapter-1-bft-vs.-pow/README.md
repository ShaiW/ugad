# Chapter 1: From BFT to PoW

We start our story at the "birthplace" of distributed consensus theory, the 1980 seminal paper [Reaching Agreement in the Presence of Faults](https://lamport.azurewebsites.net/pubs/reaching.pdf) by Pease, Shostak and Lamport (PSL).

In this paper, PSL introduce the _Byzantine generals problem_ (BGT), which was the first to formulate many _identical_ participants trying to reach an _egalitarian_ decision without any _central authority_.

Even without describing what this means exactly, it is quite clear that if it is _guaranteed_ that all participants follow the protocol, then the problem becomes trivial: each participant tells all other participants their opinion, and the most favorable possibility wins. The problem starts when we consider participants who _deviate_ from the protocol. For example, we will see that in the simple majority voting scenario, a _single deviator_ can cause the entire consensus to collapse!

Hence, the _real_ question is how many _faulty_ players can a protocol endure _while guaranteeing a consensus will be reached_. The property of being able to handle the presence of faulty players has since been named _Byzantine fault tolerance_ (BFT), in honor of the original problem.

Lamport et al. provided a protocol that can overcome faulty nodes, given that there are at least $$3f+1$$ nodes in total (they also showed this could be slightly improved to $$3f$$ nodes assuming the existence of digital signatures). Equally importantly, they proved a theorem establishing that _no_ BFT protocol could handle any more faults. This result is since known as the $$3f+1$$ _theorem_, or simply as the one-third Byzantine security limit.

Imagine everyone's surprise then, when in 2009 the pseudonymous Satoshi Nakamoto published the [Bitcoin Whitepaper](https://lamport.azurewebsites.net/pubs/reaching.pdf), exhibiting a brand new form of consensus that maintains impressive security properties as long as at least _half_ of the nodes are following the protocol. What is going on here? Did Satoshi prove PSL wrong? Is the $$3f+1$$ theorem false?

Not at all.

Two factors allowed Satoshi to break the one-third consensus barrier. First, he assumed that we can use cryptography to _prove the passage of time_, and showed how proof-of-work can implement such a capability. Second, he _relaxed_ the security requirements: instead of requiring that there is a fixed number of rounds after which a transaction will _certainly_ never revert, he "compromised" for "just" having the probability of revert decrease very very fast as rounds (which can now be converted to time, thanks to the first assumption) pass.

In this first chapter, we do not yet consider proof-of-work based _protocols_. We do consider proof-of-work itself as a form of sybilness, and discuss what it achieves that BFT protocols arguably cannot.
