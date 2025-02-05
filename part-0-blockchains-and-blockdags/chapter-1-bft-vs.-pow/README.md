# Chapter 1: From BFT to PoW

We start our story at the "birthplace" of distributed consensus theory, the 1980 seminal paper  [Reaching Agreement in the Presence of Faults](https://lamport.azurewebsites.net/pubs/reaching.pdf) by Pease, Shostak and Lamport (PSL).

In this paper, PSL introduce the _Byzantine generals problem_ (BGT), which was the first to formulate many _identical_ participants trying to reach an _egalitarian_ decision without any _central authority_.

Even without describing what this means exactly, it is quite clear that if it is _guaranteed_ that all participants follow the protocol, then the problem becomes trivial: each participant tells all other participants their opinion, and the most favorable possibility wins. The problem starts when we consider participants that _deviate_ from the protocol. For example, in the simple majority voting scenario a _single deviator_ can cause the entire consensus to collapse!

Hence, the _real_ question is how many _faulty_ players can a protocol endure _while guaranteeing a consensus will be reached_. The property of being able to handle the presence of faulty players has since been named _Byzantine fault tolerance_ (BFT), in honor of the original problem.

BGT was inaugurated by  in their seminal 1980 paper. In this paper, the provide the first protocol that can handle $$f$$ faulty generals, given there are at least $$3f+1$$ generals (which can be slightly improved to $$3f$$, assuming the existence of digital signatures). They also prove that it is _impossible_ to construct a protocol that can handle more faulty nodes. This result has since become known as the $$3f+1$$ theorem, or simply the statement that it is impossible to reach a distributed consensus secure against more than a third of the nodes being faulty and/or adversarial.

Imagine everyone's surprise then, when in 2009 the psudonymous Satoshi Nakamoto published the [Bitcoin Whitepaper](https://lamport.azurewebsites.net/pubs/reaching.pdf), exhibiting a brand new form of consensus that maintains impressive security properties as long as at least _half_ of the nodes are following the protocol. What is going on here? Did Satoshi prove PSL wrong? Is the $$3f+1$$ theorem false?

Not at all. There are two factors that allowed Satoshi to break the one-third consensus barrier. First, he assumed that we can use cryptography to _prove the passage of time_, and showed how proof-of-work can implement such a capability. Second, he _relaxed_ the security requirements: instead of requiring that there is a fixed number of rounds after which a transaction will _certainly_ never revert, he "compromised" for "just" having the probability of revert decrease very very fast as rounds (which can now be converted to time, thanks to the first assumption) pass.

In this first chapter, we will start at PSL's algorithm. Studying it will give us a handle on how BFT protocols fff
