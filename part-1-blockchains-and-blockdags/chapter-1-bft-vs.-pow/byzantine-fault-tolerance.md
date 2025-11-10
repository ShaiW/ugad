# Byzantine Fault Tolerance

Like many good stories, ours also begins with war. In the fifth century, the titular city-state of Byzantium was expanding. In one particular excursion, several Byzantine legions all surrounded the same fortress. Knowing their only chance to topple its impregnable walls is if _all_ generals work together, they need to somehow agree among themselves whether to attack or retreat. Most crucially, they must all reach _the same_ decision. But here is the crux: generals are _far too important_ to allow more than two of them to be in the same place. Arranging for all generals to convene and reach a decision together is far too dangerous. The decision _must_ rely on one-on-one conversations between two generals. Of course, this is simple if all generals are honest. But what if some generals are compromised and are trying to manipulate our protocol to get some, but not all, generals to attack?

_This_ is called the Byzantine fault tolerance problem.

## The Byzantine Generals Problem (BGP)

While the historical theme has its charm, we abandon it in favor of a more colloquial jargon. (After all, it would be weird if, three chapters from now, we referred to miners as generals.)

The BGP problem describes a network of _nodes._ These nodes can send _messages_ to each other. A consensus protocol is a procedure for a network to _agree_ on one option from a given list. (e.g., a bunch of generals having to decide whether to attack or retreat). The nodes communicate in _rounds._ In each round, any node can send a message to any other node. An important restriction that might be a little less intuitive is that all messages are sent first, and only then are they received. This is a mathematical convenience that ensures the messages in each round depend only on those received in previous rounds.

We've already mentioned one such protocol: majority voting. In this protocol, each node tells all other nodes its preference, and nodes agree to accept the popular choice. This protocol only takes one round.

Majority voting is a very simple protocol. Unfortunately, it also turns out to be a _very bad_ protocol. Recall the story of the generals, and assume there are only three of them: [Nicephorus](https://en.wikipedia.org/wiki/Nikephoros_II_Phokas), who wants to fight; [Martin](https://en.wikipedia.org/wiki/Siege_of_Phasis), who wants to retreat; and [Euphemius](https://en.wikipedia.org/wiki/Euphemius_\(Sicily\)), who is secretly a traitor to Byzantium.

Nicephorus and Martin are loyal to Byzantium and its protocols, so they broadcast their true intentions. However, Euphemius the treacherous had other plans: he told Martin that he wanted to fight, and Nicephorus that he wanted to retreat! Consequently, Nicephorus the fierce was coerced to retreat, while Martin the calm-minded was tricked into fighting alone, leading to a great defeat!

This shows that majority voting lacks _fault tolerance_. A _faulty_ (or Byzantine) node is a general term for any node that deviates from the protocol, whether due to malice or an actual fault. When analyzing the protocol's security, we don't care what caused a deviation. We care about conditions assuring that no deviation can break the protocol.

More formally, a _Byzantine Fault Tolerance_ (BFT) protocol guarantees that _all non-faulty nodes_ reach a _mutual agreement_ within some known number of rounds. We usually quantify the fault tolerance with a number $$0\le\alpha\le 1$$, indicating how many faulty nodes the protocol can handle. We call a consensus protocol $$\alpha$$-BFT if it remains correct as long as the fraction of faulty nodes is at most $$\alpha$$.

With this terminology, we can state the first major result of the field.

> **The** $$3f+1$$ **Theorem** (Pease, Shostak, Lamport, 1980):
>
> * There is no $$\alpha$$-BFT protocol for $$\alpha > 1/3$$.
> * Under certian conditions, there exists a $$1/3$$-BFT protocol.

{% hint style="info" %}
I'm actually hiding a subtlety here. There are two variations of the impossibility part of PSL's theorem. One for $$\alpha > 1/3$$, assuming the existence of digital signatures, and one for $$\alpha \ge 1/3$$ without assuming the existence of digital signatures. In a world without cryptography, we need _strictly fewer_ than a third of nodes to be fault&#x79;_._ But if we presume digital signatures exist, we can handle a scenario in which _exactly_ a third of the nodes are faulty.

This is a book about permissionless ledgers, so everything we do is deeply rooted in the assumption that cryptography exists. Hence, I chose to focus on the latter variant.
{% endhint %}

The name $$3f+1$$ comes from denoting the number of faulty nodes by $$f$$, and noting that assuming $$\alpha < 1/3$$ is the same as assuming that there are at least $$3f+1$$ nodes in total.

We will provide a more accurate version shortly. To specify what the "certain conditions" are, we must first introduce _synchronicity models_.

## Safety and Liveness

Note that the $$\alpha$$-BFT definition packs in _two_ requirements: that an $$\alpha$$ minority cannot cause a split decision, and that it cannot delay the decision indefinitely.

These two properties are best treated separately and are often referred to as _safety_ and _liveness_. In consensus protocols, security is usually broken into these two properties. It is a common mistake, and one that production-level networks unfortunately fall into, to consider safety and ignore liveness.

Majority voting is an example of how safety can fail. We should also see an example of a liveness failure.

Consider a toy protocol called the _indecisive dictator_. In this setting, one all-powerful dictator decides everything. The problem is that the dictator sometimes acts on an impulse and changes their mind soon after. To provide the dictator with some leeway, the consensus protocol accepts the dictator's decree only if it is consistent across two consecutive rounds. In this situation, if the _dictator herself_ is Byzantine, she can delay the decision indefinitely by constantly changing her mind.

This example is not as convoluted as it seems. It is a purified example of a subtle phenomenon called a balancing attack, in which a small collusion of Byzantine nodes can convince all the remaining nodes to change their minds. This does not compromise safety, as the nodes constantly change their minds together, so at any point, they all agree on the same thing. But it is a problem because the agreement remains unstable and never converges on a single option.

As we will see, liveness is _trivially true_ for the PSL protocol: the number of rounds is known in advance, and the proof shows that it is sufficient. Nothing that participants do can trigger additional rounds. This is the _exception_ to the rule. More modern BFT protocols improve performance by _adapting_ the number of rounds based on the protocol's progress. For such protocols, it is plausible that an attacker could exploit this mechanism to perform a liveness attack.

We will revisit safety and liveness more thoroughly in the next chapter, in the context of probabilistic consensus.

## Synchronicity Models

So far, we have not really discussed the properties and limitations of "a network". We talked in terms of entities transmitting messages. We didn't stop to think about the assumptions we had to make about the ability to send messages.

In fact, we made a very strong implicit assumption, can you see it? It hides in the presumption that we can divide our protocol into _rounds_. This means that the network can somehow tell when a round has ended and the next round has started.

This assumption is called the _synchronous model_, and in many cases, it is considered too good to be true. To see what other options are out there, we need a clearer way to state our assumption.

A common way to do it is by message delay. Say that you **know** that any message is guaranteed to arrive within two seconds. You can decree that a round is four seconds long, and that messages must be transmitted within the first two seconds, and arrive before the round ends. That way, you are guaranteed that no messages are lost. Or are you? We are forgetting something...

**Exercise**: What are we forgetting, and how can you fix it?

<details>

<summary><strong>Solution</strong></summary>

The remaining unaccounted for assumption is that everyone knows when the round starts. But in practice, this will usually be announced to the network by messages from other peers, and these _new round_ messages can themselves be delayed!

So a better solution is to make each round _six_ seconds long. Two seconds for receiving the round end message, two seconds for responding, and two seconds for receiving the next round end message.

Note that an end message contains the results of the round, so a participant cannot send messages for the new round without seeing the end message of the previous round.&#x20;

There are many other fixes, but this is one of the simplest.

</details>

This motivates the following:

> In the **synchronous model**, there is a known network delay bound $$\Delta$$ such that any message that sent at time $$t$$ arrives before $$t+\Delta$$.

The synchronous model is reasonable in sufficiently controlled networks, but it is not suitable at all for a global, permissionless network like a distributed ledger.

With this vocabulary, we can provide a more accurate statement of the PSL theorem:

> **The** $$3f+1$$ **Theorem** (Pease, Shostak, Lamport, 1980):
>
> * There exists a $$1/3$$-BFT protocol **in the synchronous model**
> * There is no $$\alpha$$-BFT protocol for $$\alpha > 1/3$$

Note that only the first statement needed adjustment. Since the synchronous model is the most presumptuous one, showing that something is _impossible_ in this model implies it is impossible in _all models_.

In the other corner, we have a model with no guarantees at all:

> In the **asynchronous model**, it is guaranteed that messages arrive _eventually_, but there is no guarantee (not even a probabilistic one) on how long each message will take, the order they arrive in, and so on.

It would definitely be terrific to have Byzantine agreement under such Spartan assumptions. Unfortunately, there can't be:

> **Theorem** ([Michael Fischer, Nancy Lynch, and Michael Paterson, 1985](https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf)):
>
> For any $$\alpha>0$$, no consensus protocol is $$\alpha$$-BFT in the asynchhronous model.

{% hint style="info" %}
FLP actually prove a subtler theorem that _implies_ the statement above. To fully state their original statement, we must foray into different types of faults and failures. This digression is unnecessary for our purposes, as most of the book pertains to proof-of-work, where such distinctions are unhelpful. I refer the curious to a series of [online lectures by Tim Roughgarden](https://www.youtube.com/watch?v=vJhm9uhd34E).
{% endhint %}

The proof is subtle, but the key insight is simple: we can always find a scenario where a single message changes the outcome. By delaying this one message sufficiently long, we can change the consensus. Hence, liveness is only guaranteed after such a message could not have been transmitted. But since we have no bound on the message delay, this means no wait is long enough to provide liveness.

We need a model that is not unreasonably accommodating like the synchronous model, but not as harsh as the asynchronous model. A good compromise is the **partially synchronous model**, [proposed by Cynthia Dwork, Nancy Lynch, and Larry Stockmeyer in 1988](https://groups.csail.mit.edu/tds/papers/Lynch/jacm88.pdf).

> In the **partly synchronous model** there is a delay $$\Delta$$ on the time it takes messages to arrive, but it is **not known**

{% hint style="info" %}
This is one of several equivalent formulations of this model. This variation is the simplest to understand, but not the simplest to work with. The _Global Stabilization Time_ (GST) formulation is arguably most commonly used in the literature. I again refer the studious reader to [Tim Roughgarden's lectures](https://www.youtube.com/watch?v=vJhm9uhd34E).
{% endhint %}

The idea of this model is to capture network conditions. In reality, delay bounds may change, but we assume such changes are not constant. For periods of minutes or even hours, $$\Delta$$ is usually constant. So instead of talking about an oxymoronic "constant that changes," we can talk about a "constant we don't know", which is exactly how this model works.

This model seems to be the best playground for BFT protocols. In the synchronous model, there are already "perfect" protocols that satisfy most applications. No protocol survives the asynchronous model. But the partially synchronous model seems to hit the sweet spot where useful protocols are possible, but it seems that "perfect" protocols are not, and there is room for plenty of interesting trade-offs and approaches.

## PSL's Protocol

We fully flesh out the protocol for the special case of four nodes making a binary choice. That's enough nodes to understand the internal structure without bogging ourselves down with details.

Let us call our nodes <mark style="color:blue;">John</mark>, <mark style="color:purple;">Paul</mark>, <mark style="color:orange;">George,</mark> and <mark style="color:red;">Ringo</mark>. Instead of nodes, we will refer to them as Beatles, stressing that this is not the standard terminology. The Beatles are trying to decide whether they should call their new album <mark style="color:yellow;">Revolver</mark> or <mark style="color:green;">Rubber Soul</mark>.

The protocol's rules are the same for all participants, so we assume John's point of view.

The first round of the protocol is very simple: each Beatle tells the others how _they_ would like to call the album. After the first round, John should have _three pieces_ of information, which could look, for example, like this:

> <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>

This is a good start, but we already know it is not quite enough. What could Paul do with this information? In particular, how could he know that the remaining Beatles were _consistent_, and that, say, <mark style="color:purple;">Paul</mark> didn't tell <mark style="color:orange;">George</mark> they wanted to call the album <mark style="color:green;">Rubber Soul</mark>?

To verify that, all Beatles now tell all other Beatles all they have learned in the first round. An example will make this less confusing. After the round is over, John should have _six_ more pieces of information. If anyone were honest, they should look like this:

> <mark style="color:purple;">Paul</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:purple;">Paul</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:orange;">George</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:red;">Ringo</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>
>
> <mark style="color:red;">Ringo</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;

This is enough information for John to deduce that (discounting his own vote) there is a two-to-one agreement for <mark style="color:yellow;">Revolver</mark>. If John also wants to call the album <mark style="color:yellow;">Revolver</mark>, then we are done here: all Beatles will see <mark style="color:yellow;">Revolver</mark> as preferred by most of the band, and agree on the name.

But what if John wants to call the album <mark style="color:green;">Rubber Soul</mark>? How do we handle the ensuing tie? An interesting question that I will defer until we discuss [tie-breaking rules](../chapter-2-the-block-chain-paradigm/the-paradigm.md#breaking-ties).

So we see that (up to tie-breaking) the protocol works nicely if everyone is playing nicely. But that was already true for simple majority voting! The real meat lies in how this protocol responds to faults.

Say that Ringo _really_ wants to call the album Rubber Soul, and he knows John is on his side. What if he lies to John about what George and Paul had to say? Then John would see the following messages:

> <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:purple;">Paul</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:purple;">Paul</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:orange;">George</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:red;">**Ringo**</mark>**&#x20;said that&#x20;**<mark style="color:purple;">**Paul**</mark>**&#x20;wants to call the album&#x20;**<mark style="color:green;">**Rubber Soul**</mark>
>
> <mark style="color:red;">**Ringo**</mark>**&#x20;said that&#x20;**<mark style="color:orange;">**George**</mark>**&#x20;wants to call the album&#x20;**<mark style="color:green;">**Rubber Soul**</mark>

Note the two emphasized lines in the end. They are inconsistent with previous lines. John notes a contradiction: Paul tells him he wants to call the album Revolver. But Ringo tells him that Paul wants to call the album Rubber Soul! How can he resolve the contradiction?

A naive answer would be that he should trust Paul, since first-hand information is always better than second-hand. But if we always preferred first-hand information, then all rounds but the first are actually meaningless, and we revert to simple majority voting.

The secret is essentially _second-order majority voting._ John goes over all claims of Paul's preference, including those from Paul himself, and takes the majority. In this case, he will note that both Paul and George attest that Paul wants to call the album Revolver, giving them a majority against Ringo's claims.

Note that this has nothing to do with Paul's _actual_ preference. The goal here is to reach a consensus. It doesn't matter what this agreement reflects, as long as everybody agrees.&#x20;

It is not hard to complete the argument and convince ourselves that _no matter how Ringo tries to cheat_, he will fail as long as the remaining Beatles stick to the protocol.&#x20;

But what if Ringo was innocent, and it was _Paul and George_ who colluded to enact the name Revolver, despite half of the Beatles being against it? Now John will see the following messages:

> <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>
>
> <mark style="color:purple;">Paul</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:purple;">Paul</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:yellow;">**Revolver**</mark>&#x20;
>
> <mark style="color:orange;">George</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> said that <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:yellow;">**Revolver**</mark>&#x20;
>
> <mark style="color:red;">Ringo</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>
>
> <mark style="color:red;">Ringo</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;

In this case, the plot is successful. John will note that both Paul and George claim that Ringo wants Revolver, but only Ringo claims that Ringo wants Rubber Soul. By majority decision, Ringo wants to call the album Revolver.

Again, this might seem a bit unfair. Who should Paul and George decide for Ringo? And we have to remember again that this is about reaching a decision, not about what decision is reached. Paul cannot tell whether the contradictions he observes are due to Paul and George trying to force a name on Ringo, or to Ringo professing inconsistent opinions to manipulate the result somehow. Sticking to the PSL protocol ensures that no matter what, a single Beatle cannot coerce the decision.&#x20;

This protocol can be extended to $$n=3f+1$$ Beatles (or any other rhythmic coleopterans) by adding more rounds. So by the end of the fourth round, John will hold messages such as

> <mark style="color:orange;">George</mark> said that <mark style="color:red;">Ringo</mark> said that said that <mark style="color:purple;">Paul</mark> said that <mark style="color:purple;background-color:green;">Yoko</mark> wants to call the album <mark style="background-color:purple;">Let it Be</mark>

One can prove by induction that if at most $$f$$ of the nodes are faulty, then the protocol is _guaranteed_ to provide consensus after $$f+1$$ rounds, provided there are at least $$3f+1$$ nodes.

## PSL Complexity\*

Security is important, but practicality is also important. Fault-tolerance isn't going to help anyone if it requires a trillion rounds or an exabyte of RAM.&#x20;

There are three benchmarks for the efficiency of a consensus protocol. The _communication complexity_ measures the number of rounds the protocol requires. The _space complexity_ measures how much RAM each participant needs. The _time complexity_ measures the amount of processing required to compute the result. The effect of the message length on complexity is baked into the time complexity, as we assume each non-faulty party, at the very least, reads valid messages in their entirety. Note that the space complexity might be smaller than the message length. There are protocols in which the messages can be read as a stream and do not need to be stored in their entirety at any point.

In the PSL protocol, the number of required rounds is proportional to the number of nodes: we need $$f+1$$ rounds to obtain full $$1/3$$-BFT security for $$3f+1$$ nodes. That's already far from ideal. For example, the Ethereum network has more than 750,000 validators. Directly applying PSL would require 250,000 rounds per block.

What about time complexity? We answer this by analyzing message lengths.

Recall the Beatles. In the first round, John only stated his own preference. In the second round, in each message, John had to state the preferences he heard from $$n-1$$ other Beatles. In the third round, he has to state, for each of the $$n-1$$ other Beatles, the preference they claim for the remaining $$n-1$$ Beatles (including John himself). In the $$k$$th round, John has to list at least $$(n-1)^{k-1}$$ preferences. Note that each preference is a binary value (because we assumed only two voting options), so the number of bits John must transmit in the $$k$$th round is also at least $$(n-1)^{k-1}$$.

The final round is $$k=f+1$$, in which John must transmit $$(n-1)^{f}=\Omega\left((n-1)^{n/3}\right)$$ bits, which is exponentially large.

**Exercise**: How does having a different number of options affect this computation?

<details>

<summary><strong>Solution</strong></summary>

If we have $$m$$ options, and we have to state $$p$$ preferences, we will need $$\log_2(m^p)$$ bits. But we note that $$\log_2(m^p) = \log_2(m)\log_m(m^p)=p\log_2(m)$$, so the total number of bits is the same up to a constant $$\log_2(m)$$.

It is generally good advice to remember that $$\log_2(m)$$ is exactly the number of bits required to state one out of $$m$$ possibilities.

</details>

## Practical BFT

Fortunately, PSL was just the first in a royal lineage of BFT protocols.

In 1999, Miguel Castro and Barbara Liskov introduced the[ Parctical Byzantine Fault Tolerance](https://pmg.csail.mit.edu/papers/osdi99.pdf) (PBFT) protocol. PBFT's most significant contribution is in terms of the synchronicity model. It is one of the first protocols that works in the partially synchronous model. But more than that, it reduces the time complexity to $$O(n^2)$$. An extreme improvement over the exponential complexity of PSL.

Several improvements of various sorts succeeded PBFT, such as Zyzzyva and ABsTRACTs, which provide improved performance, Aaardvark, which provides improved robustness, and Adapt, which switches between protocols to respond to changing conditions. More recent protocols, such as HotStuff and Marlin, further reduce the complexity to linear. Finally, we would be remiss not to mention a few protocols specifically crafted for proof-of-stake, such as Alphabet and Tendermint.

***

<figure><img src="../../.gitbook/assets/image (52).png" alt="" width="375"><figcaption><p>My gratitude to <a href="https://qu.ai/">Quai network</a>  for sponsoring this segment</p></figcaption></figure>

