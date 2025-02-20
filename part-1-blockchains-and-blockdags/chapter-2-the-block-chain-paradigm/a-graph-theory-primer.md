# A Graph Theory Primer

You have probably noticed that most of the mathematical exposition has been deferred to an [appendix](../../supplementary-material/math/). However, basic graph theory shall be the exception to the rule.

Graphs are important to understand even for people who only seek cursory, informal understanding. They aren't just a theoretical instrument, but they underwrite all of the visual language used to illustrate and demonstrate pretty much everything throughout this book. Besides, they are easy and fun, so why not?

## Graphs

A (directed) _graph_ is nothing but a bunch of circles called _nodes_ or _vertices,_ and a bunch of arrows between them called _edges_. For example:

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption><p>A whacky graph</p></figcaption></figure>

We are usually interested in less whacky graphs, so we assume there are no _multiple arrows_ between _the two same vertices_, and no _loopy arrows_ going from a vertex to itself, for example:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>a simple graph</p></figcaption></figure>

This is called a _simple_ (directed) graph.

In most textbooks, directed graphs are considered an intermediate topic, and the discussion start with _u&#x6E;_&#x64;irected graphs. These are graphs where for any two vertices $$A$$, $$B$$ there is an arrow from $$A$$ to $$B$$ if and only if there is an arrow from $$B$$ to $$A$$. In this case, the two arrows are compressed into a single, arrowless line. For example:\


<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption><p>an undirected graph</p></figcaption></figure>

Unlike most textbooks, we will be concerned almost exclusively with directed graphs, so we will employ a backward convention: a _graph_ is assumed to be _directed_ unless explicitly stated otherwise.

Usually undirected graphs are used to describe _reciprocal_ affinities: for example, the graph whose nodes are Facebook users, and there is an edge between any two people if and only if they are friends on Facebook, is undirected. That's because being friends on Facebook is subject to _mutual agreement_ and is thus reciprocal. In contrast, Twitter allows users to follow other users without being followed back, so a similar graph will be directed.

## DAGs

We want to use graphs to describe _causality_ or _chronology_. It makes sense that such a graph would be directed, because it does not make sense that an event $$A$$ caused or preceded event $$B$$ _while_ event $$B$$ caused or preceded event $$A$$.

However, how will you understand the chronology described by this graph:

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption><p>A closed timelike curve</p></figcaption></figure>

Does it say that event $$A$$ happened before event $$B$$ that happened before event $$C$$, that happened _before_ event $$A$$? Well, if we are being _completely_ honest, the existence of [closed timelike curves](https://en.wikipedia.org/wiki/Closed_timelike_curve) is still very much an open question. Nevertheless, we will make the assumption that our blockchain will never traverse such a loop. Let us carefully define what this means, the terms we introduce for that purpose will accompany us for the rest of the book.

Given a simple directed graph, we say that the vertex $$B$$ is _reachable_ from the vertex $$A$$ if there is some way to get from $$A$$ to $$B$$ by only following arrows. For example:

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption><p><span class="math">A</span> and <span class="math">A'</span> are both reachable from <span class="math">B</span>. However, <span class="math">B</span> is reachable from <span class="math">A</span> but not from <span class="math">A'</span>.</p></figcaption></figure>

Each way to reach $$B$$ from $$A$$ is called a _path_. A path is just a list of arrows such that the first arrow leaves $$A$$, the last arrow enters $$B$$, and any arrow in between leaves the vertex entered by the previous arrow.

For example, in the graph above:

<figure><img src="../../.gitbook/assets/image (8) (1).png" alt=""><figcaption><p>There is a single path from <span class="math">A</span> to <span class="math">B</span> (red), but three different paths from <span class="math">B</span> to <span class="math">A'</span> (orange, blue, and green)</p></figcaption></figure>

Note that in our convention, a path must have _at least one arrow_. Staying in place _doesn't count_. Under this convention, a _cycle_ is a path from any vertex $$A$$ to itself. Given the name, the following definition should not come as a surprise.

**Definition**: A _directed acyclic graph (DAG)_ is a simple directed graph with no cycles.

The DAGs that will interest us have one more useful property, they are _rooted_. That is, there is a vertex that we call $$G$$ (alluding to the genesis block) with the property that it is reachable from _all_ other vertices. We call this vertex the _root_ of the DAG.

Note that I assumed above that if the DAG is rooted then it only has one root. It is easy to prove that this is indeed the case, and there could not be a rooted DAG with two roots.

## Trees

In the context of block _chains_, we are interested in a particular type of rooted DAG called a _tree_:

**Definition**: A rooted DAG is called a _tree_ if all vertices have at most one arrow leaving them

One can see that the definition is less flexible than seems. The properties of a rooted DAG actually imply that there are zero arrows leaving the root vertex, and one arrow leaving any other vertex. A node that has no arrow going _into_ it is called a _tip_ or a _leaf_.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Given any node beside the root, we call the nodes it is pointing at its _parent._ Given any node that is not a tip, we call the nodes pointing at it its children. Note that a vertex can have many children but only one parent. Hence, for any non-root vertex $$B$$ we can define without ambiguity that $$B.Parent$$ is the _unique_ vertex that $$B$$ points to:

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
In any other graph theory book you open, you will find an opposite convention. Trees are either undirected, or directed with the arrows pointing _away from the root_, making the parents further from the root than the children. This is not because computer scientists think that trees grow from foliage to root, but because trees are often used to describe many possible routes that all converge to the same conclusion, and from this vantage the root is the "end of time" and not the "beginning of time". However, in this book we assume that the root is the oldest block, encouraging an inverse convention.
{% endhint %}

Note that in a rooted tree there is only _one_ path from any (non-root) vertex to the root (in the exercises you will show the converse, that a rooted DAG where there's only one path from any non-root vertex to the root is a tree). We call this path the _chain_ of $$B$$, and denote it $$B.Chain$$.
