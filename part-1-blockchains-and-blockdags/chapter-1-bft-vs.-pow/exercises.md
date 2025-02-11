# Exercises

1. Devise a BFT algorithm that is secure against one faulty node out of seven, but can be disrupted by two faulty nodes
2. Why did I choose the number seven in the previous question?
3. Fill in the details in the correctness proof of PSL's algoritm for the case $$n=4$$.
4. \*Prove the general case by induction, using the previous problem as the base of induction
5. A sybil resistance method is called _linear_ if the amount of voting power you get is proportional to the amount of resources you provide (in the regime where it is considered secure). For example, Bitcoin is linear, because if you have a fraction $$\alpha$$ of the global network, then you can create a fraction of at most $$3\alpha/2$$ of the blocks. A system is _better than linear_ if buying a larger fraction of the blocks has diminishing returns. For example, one could argue that a voter registry is better than linear because voting twice is considerably more than twice harder than voting once. Can a permissionless Sybil resistance be better than linear?
6. \*The SHA-256 hash function is not really a random oracle. It can be distinguished from a random oracle because it is susceptible to a "length extension attack". Read about this (for example in Boneh and Shoup’s open access [Graduate Course in Applied Cryptography](https://toc.cryptobook.us/), section 8.7), understand the attack, and explain why it is not a concern for Bitcoin's Sybil resistance.
7. What would be the consequences of trying to impose deterministic finality on a PoW blockchain by prohibiting reorgs of some shallow depth (say, an hour)?
8. Can you solve the rich-get-richer problem by implementing diminishing returns (that is, by making the reward decrease as the amount of coined staked by the same person increases)?

