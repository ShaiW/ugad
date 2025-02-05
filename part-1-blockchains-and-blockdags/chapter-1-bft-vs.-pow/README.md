# Chapter 1: BFT Vs. PoW

To set the stage and get warmed up, we start where most tours of consensus theory start: the Byzantine generals problem (BGT), which is considered by many as the birthplace of consensus theory. Coming up with solutions to BGT led to the development of the first distributed protocols for reaching consensus and, not less crucially, proving impossibility results.

BGT was inaugurated by Pease, Shostak and Lamport in their seminal 1980 paper [Reaching Agreement in the Presence of Faults](https://lamport.azurewebsites.net/pubs/reaching.pdf). In this paper, the provide the first protocol that can handle \$$ f \$$ faulty generals, given there are at least $3f+1$ generals (which can be slightly improved to $3f$, assuming the existence of digital signatures). They also prove that it is _impossible_ to construct a protocol that can handle more faulty nodes. This result has since become known as the&#x20;
