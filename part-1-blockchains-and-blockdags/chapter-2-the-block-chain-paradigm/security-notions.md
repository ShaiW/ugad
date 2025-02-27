# Security Notions

I encourage you to do the following thought exercise before reading forward: put the book away, lie down on a comfy sofa or go for a short walk, and try to think how would _you_ define what makes a block chain secure. Try to challenge yourself, play devil's advocate, search for what makes a definition bad and how it can go wrong. Then come here and read my takes on the matter _after_ attempting for yourself.

Before we dive into the various security notions one can define for a block chain, it might be instructive for the non-cryptographer to take a step back to ponder what a security notion even _is_.

Many people "know" that a block chain protocol such as Bitcoin is "secure" if "transactions cannot be reverted". More introspection raises two questions: what do you mean by "cannot", and who is to say that's the correct way to define security anyway?

The purpose of this section is to convince you of a harsh truth: there is no _perfect_ notion of security. A security notion can only cover _limited_ attackers. If you assume an omnipotent adversary, then you do have little to do against them other than roll-up and cry. In particular, you should not count on any encryption scheme to protect you from a [$5 wrench attack](https://xkcd.com/538/).

But when you _do_ impose limitations on the adversary, you take the risk that a future adversary might rise above your limitations. Defining a security notion is the art of walking this fine line: it must be _strong enough_ to prohibit as many attack vectors as possible, but _weak enough_ to actually be achievable.

{% hint style="info" %}
Those with some background might think that when I say an "omnipotent adversary" I mean "computationally unbounded" (a.k.a. "unconditional") adversary. That's not completely. An unconditional adversary is an adversary that can make arbitrary complicated computations, even computations that we know are impossible in reality. But they are _not omniscient:_ we can _keep secrets_ from them. Sometimes, the ability to have a shared secret with someone is enough to handle attackers even if they have god tier computational abilities. The textbook simple example of an encryption scheme that is secure against unconditional attackers but not against all-knowing attackers is the [one-time pad](../../supplementary-material/computer-science/page-3/one-time-pads.md).
{% endhint %}

## The Pizza Scandal (No, Not _That_ One)

The Pentagon, obviously, works in extreme confidentiality, enforcing strict protocols to prevent data leakage. Yet, during the cold war, the soviets allegedly managed to obtain vital clues about the timing of future major events. It took a while, but it was eventually revealed they used an OPSEC called PizzaInt (for _pizza intelligence_): by tracking the amount of pizzas delivered to the Pentagon, they could reliably detect periods of increased activity, which signaled that something is afoot.

This method of gauging levels of international crisis is since known as the "pizza meter". Famously, it was used by news desks to report about the Iraqi invasion of Kuweit the night before it happened.

The Pentagon tried to plug the leak with updated security protocols that require personnel to go pick-up orders rather than having them delivered. But this is only a partial solution, as tracking the activity of fast-food eateries in the proximity of the Pentagon and the White House still provides information. International crisis begets increased activity, and that is a very hard thing to hide, as we [have seen](https://x.com/RealBenGeller/status/1819555623068963097) last April, prior to the Iranian attack on Israel.

This purpose of this story is not to tear down the CIA, but to illustrate just how difficult it is to take _everything_ into consideration.&#x20;

## Falcon Down

As a quantum cryptographer who is constantly asked about post-quantum blockchains, there are few cryptography related stories I like more than that of the [_Falcon_ signature scheme](https://falcon-sign.info/).

With the rising concerns of quantum computers breaking contemporary cryptographic schemes, the National Institute of Standards and Technology (NIST) invited applied cryptographers to participate in a _competition_ to create the best encryption and signature schemes that are arguably secure against quantum attackers.

The Falcon team did an amazing job, and indeed became one of the leading NIST candidates.

However, soon after it was published, an [exploit was found](https://eprint.iacr.org/2021/772). Does this mean Falcon is broken? That it's analysis is wrong? Was it removed from the NIST competition? No.

The vulnerability is in a category called _side channel attacks_. This means that the attack does not appeal to the protocol itself, but to information that _might_ leak by an _implementation_. The exploit notices that in a careless implementation of Falcon, a little bit of information can be recovered _making electromagnetic measurements of the signing device_, and noticing _how long_ some of the operations took (in a way that kind of resembles the [Heartbleed exploit](https://heartbleed.com/) found in OpenSSL in 2014). It turns out that this little bit of information is enough to forge Falcon signatures. They proceeded to show experimentally that there are implementations of Falcon that they are able to break this way.

We hit the recurring theme again: it's not that the Falcon security analysis is sloppy, it is that it has to make _assumptions_ about the information available to the adversary, and circumventing these assumptions allows to circumvent the security guaranteed by the protocol. As far as we know, Falcon remains completely secure when implemented on appropriate hardware (in particular, hardware with fixed time floating point arithmetic).

The block chain Algorand prides itself on being quantum secure by merit of having [implemented Falcon signatures](https://algorand.co/blog/pioneering-falcon-post-quantum-technology-on-blockchain). Fortunately, as far as I can tell, there is currently no hardware wallet that supports these Falcon signatures, and that's a _good thing_. Appropriate hardware is more expensive and not typical for hardware wallets, who strive to be as lean as possible, and if the exploit was only discovered say, in 10 years, it could have compromised all Algorand holders.

## Unconditional Security and String Theory

In the world of unconditional security we deal with computationally unrestricted adversaries. If it can be computed: they can compute it. In some cases, we even assume they can compute stuff that can't be computed in reality. The only assumption we make is that there is some _secret information_ not known to the adversary. Is this a realistic assumption?

In his 2018 paper [Is the Security of Quantum Cryptography Guaranteed by the Laws of Physics?](https://sidechannels.cr.yp.to/qkd/holographic-20160326.pdf) Daniel Berenstein challenged this assumption in a rather amusing way: by appealing to _string theory_!

Now hear me out, I know that a lot of people consider string theory as "controversial" or "not even wrong" or whatever, but one principle that dropped out of it that is largely uncontested is the so called _holographic principle_. This principle states that _all of the information_ that exists inside a _volume_ of space, can be perfectly recovered from measurements on its _surface._

A _truly_ omnipotent adversary could build a dense spherical array of electromagnetic sensors surrounding the earth, and, according to the holographic principle, by observing the measurements they can recover _any information on earth_, yes, including your secret crushes and keys.

This attack is obviously not _practical_ in any way. It just comes to show that we _always_ have to make assumptions. Even if the assumption is as mild as requiring that the adversary cannot design an experiment that distinguishes string theory (where the holographic principle exists) from the standard model (where it doesn't).

## Defining Security Notions

The _bad_ way to define a security notion is to require that a particular _attack_ does not work. Why is this bad? Because it does not prohibit _other_ attacks that achieve the _same goal_. Unfortunately, this reasoning of the "our protocol is secure because the attacks that we attempted don't work" ilk is quite common, mostly because it is _much easier_ to rule out a specific attack than a generic attack.

A _good_ security definition has three components:

* Explicitly stated _limitations_ on the adversary (such as "computationally bounded" or "only has a minority of the hash power")
* Explicitly stated assumptions about the _environment_ (honest majority, secret information, that kind of stuff)
* Explicitly stated _goal_ for the adversary (revert a transaction, forge a digital signature, and so on)

The security definition then takes the general form of: if the environment satisfies the assumption, then _any_ attacker within our limitations is _sufficiently unlikely_ to achieve the goal.

The reason we say "sufficiently unlikely" and not "impossible" is that in most cryptography "impossible" is just too much to ask. For example, in any situation where the security relies on a secret-key being secret, and there is a reasonable way to check that some guess for the secret-key is correct (e.g. by using it to decrypt messages and see that it works), there is always a chance that the adversary just happens to _guess_ the secret-key by sheer luck. We can't undo this fact, so instead we require that this probability can be easily and efficiently made very very small. Typically, increasing the key size will make routine procedures like generating keys, encrypting, decrypting, signing and so on only slightly more complex, while making the probability of any attacker _much_ more complex.

{% hint style="info" %}
Of course, there is a layer of formalism that I skipped for the sake of exposition, and actually defining a security notion requires most work. But I maintain that the _gist_ is well captured in this description above, and the rest are technicalities. When we actually define concrete security notions, I will make some of the vague statements like "fast enough" more concrete. For those seeking a more formal treatment of security notions, see the cryptography appendix (TODO: write a section about security notions and link it here).
{% endhint %}

## Security Notions for Block Chains

Let us go through the process of refining a security notion for block chains. In this discussion, I will deliberately break down many details that I will actually comfortably ignore for the rest of the book. The purpose is not that you remember each and every nook and cranny of the definition (though that definitely won't be bad for you!), but to demonstrate just how nuanced this process is, hoping that you keep it in mind, perhaps the next time the new project devs tell you that their protocol is secure because they "tested it".

So how can we define when a blockchain is secure? Let us first concentrate on double-spending. We do not want reverting transactions to be possible, so what about this security notion:

> A block chain is secure if it is _impossible_ to revert a transaction

Well, we know that's bad. We already agreed that "impossible" is not going to happen. First of all, there are scenarios where reverting transactions is easy, say if the adversary has 90% of the hashing power. Moreover, even without this advantage, the is _some_ probability to revert by sheer luck.

A more decent attempt would be something like:&#x20;

> Assuming that the rest of the network is [rational](honesty-and-rationality.md), an adversary with less than half of the global hash rate is not likely to revert a transaction

That looks better, but that's still not quite there at all. First, what does "not likely" even mean? But more pressing, is this definition even possible to satisfy? Well, it isn't. We will soon see that a PoW network _can't_ be secure against attackers that can get arbitrarily _close_ to 50%. For example, in Bitcoin, a little of the hash rate to be wasted on orphan blocks, making it so that you "only" need 49.997% of the global hash rate to revert arbitrarily long transaction with accuracy. The reason this number is so close to 50% is because of the way Bitcoin is _parameterized_. The block delay is much larger than the network delay, causing very low orphan rate that only translates to a marginal degradation of security. In particular, while we can never be secure against a 50% attack of the network, for any $$\delta>0$$ we _can_ set Bitcoin's parameters such that it will be secure against attackers with less than $$\frac{1}{2} -\delta$$ of the total hash rate. So let us encode this into the definition:

> For any $$\delta > 0$$, we can parameterize the network such that any adversary with less than $$\frac{1}{2}-\delta$$ of the global hash rate is not likely to revert a transaction

Good! We have better defined the environment _and_ the limitations on the adversary. Are we done? Not really, because the goal seems kind of ill-defined. What does "revert a transaction" mean? It means a few things, but one necessary (yet not sufficient) such thing is to _change the selected chain to one that does not include the transaction_. In other words, what we would really want to prevent is _reorging a block_.

So consider this situation: the block $$B$$ was the latest one to be created, and you want to reorg it. You have 30% of the hashing power. In order to reorg $$B$$ it is sufficient to create _two blocks_ before the honest network creates one block. How probable is that? Well, we will compute this [a bit later](confirmation-times.md), but I can tell you right now that the probability comes out about 49%! I believe that we could all agree that reverting a transaction with probability of almost one half is not in the "not likely" regime.

Here is the thing, the probability to reorg a block decreases as _more blocks are added_. The standard way to talk about fast decay is by requiring that it is what we call a negligible function.

> For any $$\delta > 0$$, we can parameterize the network such that any if at least $$\frac{1}{2}+\delta$$ of the global hash is rational, then the probability a block created $$T$$ seconds ago is a negligible function of $$T$$

OK, that is starting to take shape, but consider this: what if the attacker finds a vulnerability in the hash function, allowing them to create blocks very fast? If our protocol is secure according to the definition above, this would imply that such a vulnerability does not exist. In other words, proving that a protocol satisfies the security _requires_ proving somehow along the way that the underlying hash is secure.

We _really_ want to avoid this. First of all, it just doesn't make much sense to amalgamate the security of the hash function into our definition, we would like to discuss them _separately_. Second, designing a block chain protocol and designing a hash function are two _very different tasks_ usually done by different people with different backgrounds. We want to find an approach that compartmentalizes away the problem of creating a good hash function. It's not that this problem is not important, it is in fact crucial, it's that we want our analysis to prove that the protocol is secure if you use _any_ "secure" hash function. We do so by assuming the underlying hash function is some _ideal and unrealistic_ machine called a [_random oracle_](../../supplementary-material/computer-science/page-3/random-oracles.md), and then leave it for the designer of a hash function to provide us with evidence that the hash indeed sufficiently resembles this mythical random oracle. This brings us to the following definition:

> (Assuming the underlying hash is modeled as a random oracle,) for any $$\delta > 0$$, we can parameterize the network such that any if at least $$\frac{1}{2}+\delta$$ of the global hash is rational, then the probability a block created $$T$$ seconds ago is a negligible function of $$T$$

There are obviously _many_ other details that actually need addressing, for example, how inefficient this things behaves as $$\delta$$ gets close to $$0$$, How does $$\delta$$ affect the function, and so on. But overlooking this detail, are we done? Is this a satisfactory security definition?

Well, kind of... This definition only captures one way to disturb the consensus process. Another concern is attackers that _delay_ the consensus. This is the issue of [safety](safety.md) vs. [liveness](liveness.md), an interesting story that will occupy much of this chapter. The bottom line, though, is that the definition above only captures the safety property, and the equally important liveness property _can_ be violated in networks that provide safety, as we will see in explicit examples.

So OK, this safety property we kind of defined and the liveness property we did not define combine to what is usually called "security". Is a secure blockchain everything we could hope for? In the next section we will see that it is not quite the case. We will find that attackers can have _other goals_ besides harming the consensus, and that these goals could be achieved in secure blockchains. Actually, we will find something much more disturbing, that _rational_ miners will follow this attack.

