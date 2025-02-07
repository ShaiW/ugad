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

This protocol can be extended to&#x20;







