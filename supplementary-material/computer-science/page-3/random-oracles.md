# Random Oracles

TODO: this section is poorly written, improve.

In computer science, an _oracle_ is a magic being that performs some task for us. Oracles have _many_ uses, one of them is to assume we can perform some computation without bothering ourselves with the details. Instead, we reason in an alternate universe where a magical oracle does the computation for us. This way we can _divide the work_ with someone working to _implement_ a random oracle. _hash functions_ such as SHA-256, Keccak, or k-HeavyHash are an implementation of a certain kind of oracle called a _random oracle_. The random oracle model has a nice mathematical description that is oblivious to how the hash function is actually implemented.

A random oracle _consistently_ turn an arbitrary string $$s$$ to a _random, fixed length_ string $$\mathsf{H}(s)$$. What does this all mean?&#x20;

_Fixed length_ just means that $$\mathsf{H}(s)$$ is always the same length, regardless of $$s$$ or its length. Under the hood, we assume that $$s$$ is a strings of ones and zeros (but that shouldn't concern you), so we call the length _bits_. Proof-of-work typically uses hash functions with an output length of 256 bits.

&#x43;_&#x6F;nsistency_ and _randomness_ mean that when the oracle responds to a string $$s$$, the oracle should respond to queries like this:

* (consistency) if she was queried about the string $$s$$ before, respond with the same $$\mathsf{H}(s)$$
* (randomness) otherwise, respond with a _uniformly random 256-bit string_

One key property of a random oracle is that it is _hard to invert_. If you have a string $$h$$ of appropriate length, and you an $$s$$ such that $$h=\mathsf{H}(s)$$, how would you go about finding it? This problem is called _inverting_ $$\mathsf{H}$$ (in the point $$h$$).

The only way to get information from the oracle is by making queries. But due to the randomness, if you query on some $$s$$, and get a response different than $$h$$, this tells you next to nothing. Yes, you know that $$s$$ is not the correct answer, but that's it. You haven't eliminated any single other possibility.

The consequences are that the best way to solve the problem is by querying on different strings until you stumble backwards into one that gives the correct answer. Now, how long this should take? If the output length is $$n$$ bits, then there are $$2^n$$ possible outputs. Recall that the oracle always chooses the string _uniformly_. Hence, the probability a query produces the desired result is one in $$2^n$$. That is, it would take about $$2^n$$ attempts.

At a trillion attempts per second, inverting a 256 bits hash function will take over three billion trillion trillion trillion trillion years.





