# Byzantine Fault Tolerance

Like many good stories, ours also begins with war. In the fifth century, the titular city-state of Byzantium was expending. In one particular excusion, several Byzantine legions all surrounded the same fortress. Knowing their only chance to topple its impregnable walls is if _all_ generals work together, they need to somehow agree among themselves whether to attack or retreat. Most crucially, they must all reach _the same_ decision. But here is the crux: due to their importance, it is never allowed that more than two generals confer. They cannot all convene in one tent and hash it out. The entire decision _must_ only rely on one-on-one communication. This is not just quite a problem, it is _the_ problem.

## The Byzantine Generals Problem (BGT)

While the historical novel style has its charm, we will shed it for a more colloquial jargon. (After all, it would be weird if three chapters from now we would refer to miners as generals).

The BGT problem essentially desccribes a network of _nodes_ communicating with each other, having to _agree_ on one of given list of _options_. (e.g. a bunch of generals having to decide whether to attack or retreat). The nodes communicate with each other in _rounds_, where in each round, any node can send a message to any other nodes. However, nodes only start to _receive_ messages after _all_ nodes are done sending their messages. Their goal is to devise a _protocol_ that guarantees that, if _all_ nodes follow the protocol they will all reach _the same_ decision after a _known_ number of rounds.

We've actually already mentioned one such protocol: majority voting. In this protocol, each node tells all other nodes its preference, and nodes agree to accept the popular choice. This protocol only takes one round.

Majority voting turns out to be a _very bad_ protocol. Recall the story of the generals, and assume there are only three generals: [Nicephorus](https://en.wikipedia.org/wiki/Nikephoros_II_Phokas), who wants to fight; [Martin](https://en.wikipedia.org/wiki/Siege_of_Phasis), who wants to retreat; and [Euphemius](https://en.wikipedia.org/wiki/Euphemius_\(Sicily\)), who is secretly a traitor to Byzantium.

Nicephorus and Martin are loyal to Byzantium and its protocols, so they broadcast their intentions. However, Euphemius the treacherous had other plans: he told Martin that he wants to fight, and Nicephorus that he wants to retreat! Consequentially, Nicephorus the fierce was coerced to retreat, while Martin the calm-minded was coerced to fight alone, leading to a great defeat!

That example shows where majority voting lacks as a protocol: in its _fault tolerance_. We call a node _faulty_ if it deviates from the protocol for any reason. Adversarial generals and hackers are a reason for concern, but in real world scenarios nodes that are simply malfunctioning are also a serious concern.

A _byzantine fault tolerance_ (BFT) protocol is a protocol that can guarantee that the nodes will reach a decision in the prescribed amount of time even if _some_ of the nodes are faulty. The level of security such a protocol provides is usually described as a number $$\alpha$$ between $$0$$ and $$1$$ that describes the _fraction_ of faulty nodes a protocol can tolerate.

In this terms, we can say that PSL created a protocol with $$1/3$$ fault tolerance, and proved that this is the maximal fault tolerance possible.

**Remark**: The BFT property has _two_ requirements. One is that the less than \alpha minority cannot _coerce_ a decision, and the other is that it cannot _delay_ it. The first form of security is called _safety_ while the other is called _liveness_, and  security requires _both_. PSLs protocol requires a fixed number of rounds that can not be changed as a result of faulty nodes (though it does depend on the number of nodes), but more modern types of BFT adapt the number of rounds according to how the protocol is progressing, making liveness a concern.

## PSL's Protocol

We describe the PSL protocol for four nodes called <mark style="color:blue;">John</mark>, <mark style="color:purple;">Paul</mark>, <mark style="color:orange;">George</mark> and <mark style="color:red;">Ringo</mark> who are trying to decide whether they should call their new album <mark style="color:yellow;">Revolver</mark> or <mark style="color:green;">Rubber Soul</mark>.

In the first round, each Beetle tells just tells all other Beetles how _they_ want to call the album. That's a good start, but we also know that's not quite enough yet. In the second round, each Beetle shares with each other Beetles what _the other Beetles told them_.

After the first round, John should have three pieces of information, for example:

> <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:orange;">George</mark> wants to call the album <mark style="color:yellow;">Revolver</mark>&#x20;
>
> <mark style="color:red;">Ringo</mark> wants to call the album <mark style="color:green;">Rubber Soul</mark>

After the round is over, John should have _six_ more pieces of information. If anyone were honest, they should look like this:

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

From which John can clearly understand that (discounting his vote) it is a two-to-one for Revolver. If John also want to call the album Revolver, then he would agree with this name. If all other Beetles followed the protocol, they will also agree on the name Revolver, _including Ringo_ who preferred Rubber Soul!

What if John wants to call the album Revolver too? That's an interesting question I am going to ignore.

This works nicely if everyone is playing nicely, but what if someone tries to cheat? Say that Ringo _really_ wants to call the album Rubber soul, and he knows John is on his side. What if he lies to John about what George and Paul had to say? Then John would see the following messages:

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
> <mark style="color:red;">Ringo</mark> said that <mark style="color:purple;">Paul</mark> wants to call the album <mark style="color:green;">**Rubber Soul**</mark>
>
> <mark style="color:red;">Ringo</mark> said that <mark style="color:orange;">George</mark> wants to call the album <mark style="color:green;">**Rubber Soul**</mark>

Here John will note a contradiction: Paul told him they want to call the album revolver, George also told him that Paul wants to call the album revolver, only Ringo for some reason claims that George wants to call the album Rubber Soul.

What John essentially does is _second order majority voting_. He sees how _Paul, George and Ringo_ voted on _how Paul wants to call the album._ Note that even though Paul is the subject of discussion, his vote counts exactly as much as George and Paul.

It is not hard to complete this line of thought, and convince ourselves that _no matter how Ringo tries to cheat_, as long the rest of the Beetles follow the protocol there is no way for him to coerce a decision that is not backed by a strict majority.

But what if Ringo was innocent, and it was _Paul and George_ who collude to enact the name Revolver, despite half of the Beetles being against it? Now John will see the following messages:

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

Upon the contradictions regarding Ringo's position, Paul will again carry out the second order voting, and see that _two_ Beetles claim Ringo is pro Revolver, but only _one_ Beetle claims Ringo wants to call the album Rubber Soul.

But wait, you might ask, the Beetle who thinks Ringo prefers Rubber soul _is Ringo_, shouldn't that account for something? Maybe, but the ability to override Ringo is exactly what protects us from Ringo playing a double game, telling each Beetle they have a different preference in an attempt to split the consensus. It is the _crux_ of the protocol and how it works.

This protocol can be extended to $$n=3f+1$$ Beetles (or any other coleopterans) by adding more rounds. So by the end of the say fourth round John will hold messages such as

> <mark style="color:orange;">George</mark> said that <mark style="color:red;">Ringo</mark> said that said that <mark style="color:purple;">Paul</mark> said that <mark style="color:purple;background-color:green;">Yoko</mark> wants to call the album <mark style="background-color:purple;">Let it Be</mark>

One can prove by way of induction that if at most $$f$$ of the nodes are faulty, then the protocol is _guaranteed_ to provide consensus within $$f$$ rounds.

## Practical BFT\*

A BFT protocol is not only measured by its security. Fault tolerance is an important feature, but your fault tolerant protocol isn't going to help anyone if it requires a trillion rounds or terabytes of RAM. The _communication complexity_ is the number of rounds the protocol requires, the _space complexity_ is how much data it has to keep track of, and the _time_ complexity is the amount of processing required to compute the result from the received messages. One might wonder whether the length of the messages should play a part, and it does: it manifests itself in the time complexity, as we consider reading the messages a part of the work.

In the PSL protocol, the number of rounds is a third of the nodes. That's already less than ideal. For example, the Ethereum network has more than 750,000 validators, so using the PSL protocol on this network would require 250,000 rounds per block.

However, it is the runtime complexity where the real problem shows. To see that, it suffices just to look at how many times a Beetle is named in each message.&#x20;

Let us assume there are $$n=3f+1$$ Beetles and consider the messages Paul sends John. In the first round, Paul just sends his messages, so he names no Beetles. In the second round, Paul _lists all Beetles besides Paul and John (and their alleged preferences_), making him name $$n-2$$ Beetles.

What happens in the third round? Well, consider Ringo. For any Beetle X, Paul's message will include a line starting with "Ringo says that X wants", where there are $$n-3$$ options for X, as it could be any Beetle beside John, Paul, or Ringo. This means that we have $$n-3$$ lines starting with "Ringo said that". However, we don't really have to name Ringo in each and every line, as we can only write "Ringo says" once, and then list everything he told us. The total number of Beetle names is $$n-2$$. But wait, that's just Ringo! We have a similar list for _all_ $$n-2$$ Beetles besides Paul and John, making us name a total of $$(n-2)^2$$ Beetles.&#x20;

By the time we reach round $$f=\frac{n-1}{3}$$ we find that each message contains $$(n-2)^{(n-1)/3}$$ Beetle names, which is _a lot_. Even if we assume storing the name of Beetle only requires _one bit_, we get that if there are $$20$$ Beetles, _each message_ in the last round is more than 10 megabytes. Doesn't sound like a lot? Well, for $$30$$ Beetles the message size would surpass 10 &#x74;_&#x65;ra&#x62;_&#x79;tes, for $$40$$ we succeed 344 _ex&#x61;_&#x62;yte, and $$50$$ Beetles will topple two _bront&#x6F;_&#x62;yte, a unit of storage so large I never heard of it before writing this paragraph, and even my spell checker does not recognize. And what about 750,000 Ethereum validators? Well, the number is so large you would need about 170 kilobytes just to write down _how many digits it has_.

Fortunately, BFT protocols only started with PSL's work. In 1999, Miguel Castro and Barbara Liskov introduced the[ Parctical Byzantine Fault Tolerance](https://pmg.csail.mit.edu/papers/osdi99.pdf) (PBFT) protocol. PBFT most significant contribution is actually in terms we have not defined here: synchronicity model. Roughly, a synchronicity model models message latency. In the PSL analysis, there is an implicit assumption of _full synchronicity_. That is, all participants know when each round starts and when it ends. Full synchronicity is _not_ how networks such as the internet naturally behave, and imposing it incurs large overheads. The internet is best modeled as admitting _partial synchronicity_, where we _don't know_ how long it takes messages to transmit, but we do have an _upper bound_. Castro and Liskov's protocol is the first to provide $$1/3$$ fault-tolerance in the partially synchronous model. But more than that, it reduces the complexity to $$O(n^2)$$. Several improvements of various sorts succeeded PBFT, such as Zyzzyva and ABsTRACTs who provide improved performence, Aaardvark who provides improved robustness, and Adapt that switches between protocols to respond to changing conditions. More recent protocols such as HotStuff and Marlin further reduce the complexity to linear, and we should probably also mention BFT protocols crafted specifically for proof-of-stake such as Alphabet and Tendermint.









