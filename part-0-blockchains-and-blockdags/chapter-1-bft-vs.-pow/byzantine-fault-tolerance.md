# Byzantine Fault Tolerance

Like many good stories, ours also begins with war. In the fifth century, the titular city-state of Byzantium was expending. In one particular excusion, several Byzantine legions all surrounded the same fortress. Knowing their only chance to topple its impregnable walls is if _all_ generals work together, they need to somehow agree among themselves whether to attack or retreat. Most crucially, they must all reach _the same_ decision. But here is the crux: due to their importance, it is never allowed that more than two generals confer. They cannot all convene in one tent and hash it out. The entire decision _must_ only rely on one-on-one communication. This is not just quite a problem, it is _the_ problem.

## The Byzantine Generals Problem (BGT)

While the historical novel style has its charm, we will shed it for a more colloquial jargon. (After all, it would be weird if three chapters from now we would refer to miners as generals).

The BGT problem essentially desccribes a network of _nodes_ communicating with each other, having to _agree_ on one of given list of _options_. (e.g. a bunch of generals having to decide whether to attack or retreat). The nodes communicate with each other in _rounds_, where in each round, any node can send a message to any _one_ other node. Their goal is to devise a _protocol_ that guarantees that, if _all_ nodes follow the protocol they will all reach _the same_ decision after a _known_ number of rounds.

We've actually already mentioned one such protocol: majority voting. In this protocol, each node tells all other nodes its preference, and nodes agree to accept the popular choice. If there are $n$ nodes participating, then this protocol requires $n-1$ rounds.

Majority voting turns out to be a _very bad_ protocol. Recall the story of the generals, and assume there are only three generals. 

Martin
Euphemius
Nicephorus 
