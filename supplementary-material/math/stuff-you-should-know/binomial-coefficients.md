# Binomial Coefficients

How many ways are there to pick five out of seventeen different toppings for your pizza? But it has to be exactly five, because you haven't eaten all day.

Well, you have seventeen ways to choose the first topping, right? And then you have sixteen ways to choose the second topping and so on, until the last topping you choose, for which you have thirteen choices, so it seems that the answer should be $$17\cdot 16\cdot\ldots\cdot 13$$, but that's not quite the case.

Why isn't it? Because we _overcounted_. Say instead of five toppings we didn't choose five, but just two toppings. The first is pepperoni because all pizzas deserve meat, and the second is pineapples because no one is going to tell you what to do! However, what if you ask for the pineapple first and the pepperoni second? Huh? In this case you would get _the same pizza_, which means we counted pepperoni-pineapple pizza _twice_, on account of our failure to realize that a pepperoni-pineapple pizza and a pineapple-pepperoni pizza is _the same thing_.

Fortunately, we counted each kind of pizza _the same amount of times_. And how much is that? The number of different ways we can any _given_ list of five toppings and shuffle it around.

So how many ways are there to arrange a list of five toppings? Well, we have five ways to choose the first one, four ways to choose the second, you see where this is going. There are $$5\cdot 4 \cdot 3\cdot 2\cdot 1$$ ways to shuffle around the list of topping, which means that _this_ is the number of times we counted each possible pizza. The upshot is that there is a total of $$\frac{17\cdot 16\cdot\ldots\cdot 13}{5\cdot 4 \cdot 3\cdot 2\cdot 1}=6188$$ different pizzas you can put together.

Going abstract we can now consider choosing $$k$$ out of $$n$$ toppings for some $$0\le k\le n$$. To do so, we will introduce a nice notation: given a number $$k$$, the number $$k$$ _factorial_ is denoted and defined as

$$
k! = k\cdot\ldots\cdot 1
$$



So the number of ways to shuffle $$k$$ toppings would be $$k!$$, but what about the number of ways to choose them? Well, as before there are $$n$$ possible ways to choose the first, $$n-1$$ possible ways to choose the second, and so on. When we choose the $$k$$th and final topping, we have already picked $$k-1$$ toppings, so there are $$(n-(k-1))$$ choices remaining. How can we write this nicely? We do _a trick_, and write the total number of combinations _with_ overcounting as:

$$
\begin{aligned} & n\cdot\left(n-1\right)\cdot\ldots\cdot\left(n-\left(k-1\right)\right)\\
= & n\cdot\left(n-1\right)\cdot\ldots\cdot\left(n-\left(k-1\right)\right)\frac{\left(n-k\right)\cdot\left(n-\left(k+1\right)\right)\cdot\ldots\cdot1}{\left(n-k\right)\cdot\left(n-\left(k+1\right)\right)\cdot\ldots\cdot1}\\
= & \frac{n\cdot\left(n-1\right)\cdot\ldots\cdot\left(n-\left(k-1\right)\right)\left(n-k\right)\cdot\left(n-\left(k+1\right)\right)\cdot\ldots\cdot1}{\left(n-k\right)\cdot\left(n-\left(k+1\right)\right)\cdot\ldots\cdot1}\\
= & \frac{n!}{(n-k)!}
\end{aligned}
$$

and now we just divide out the overcounting to get that the number of ways to choose $$k$$ out of $$n$$ toppings is $$\frac{n!}{k!\left(n-k\right)!}$$.

This expression is so useful that it has a name and a notation, it is denoted $${n \choose k}$$ which is pronounced "$$n$$ choose $$k$$". These numbers, are also called the _binomial coefficients_.

Note that this expression has a nice symmetry to it between $$k$$ and $$n-k$$, and that makes sense because _choosing_ $$k$$ _toppings to put on your pizza is exactly the same as choosing_ $$n-k$$ _topping **not** to put on your pizza_. An observation easily stated and verified as the equation $${n \choose k} = {n \choose n-k}$$.

**Exercise**: Show that if $$0\le k \le r \le n$$ then $${n \choose r} \cdot {r \choose k} = {n \choose k} \cdot {n - k \choose r - k}$$. **Hint**: you don't really need math to solve this, you just need to tell yourself the right story.

Binomial coefficients come up _everywhere_, and have very nice properties to them.

## Pascal's Triangle

There is actually a really cool way to represent the binomial coefficients called _Pascal's_ triangle. To draw the triangle, follow these rules. First, write the number $$1$$ at the middle of the top line of the paper, and then write it again twice, like so:

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

In the next line we write _three_ numbers. The first and last would be $$1$$, and the middle number will be the _sum of two numbers above it_:

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

We can keep going, following this pattern, the next few lines will look like this:

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

What does this have to do with everything? Well, remarkably, if we number the lines, starting with zero, then the $$k$$th number if the $$n$$th line is exactly $${n \choose k}$$.

**Exercise**: Assuming that Pascal's triangle satisfy this property, prove that for any $$n,k$$ it holds that $${n \choose k} = {n-1 \choose k} + {n-1 \choose k-1}$$.

## Example: High Dimensional Cubes

A three dimensional cube has eight vertices (corners), twelve edges, and six faces. In mathematese, we call these all _faces_. The vertices are just points, so they are the _zero dimensional_ faces, the edges are one-dimensional lines, so we call them the _one-dimensional_ faces, and what we used to call _faces_ we will now call _two-dimensional_ faces. Following this logic, a cube has a single _three-_&#x64;imensional face: the cube itself.

In this terminology, a two-dimensional cube is just a _square_. It has four zero-dimensional faces, four one-dimensional faces, and one two-dimentsonal face. Cool? Cool.

So how many eight-dimensional faces does a seventeen-dimensional cube have? Turns out we can compute this using binomial coefficients! We will actually find a formula for the number of $$k$$-dimensional edges in an $$n$$-dimensional cube for all $$k\le n$$!

To do this, we will need a better way to encode what "a cube" and "a face" is, one that can easily encode the information we need without forcing us to imagine shapes in more than three dimensions.

Consider a three dimensional cube. Each corner can be represented by three numbers, each being either 0 or 1. The first number represents if we are "left or right", the second if we are "back or front", and the third if we are "up or down".

<figure><img src="../../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

This allows us to comfortably encode the corners. Note that two corners are connected if and only iff they differ in exactly _one coordinate_. So this gives as an indication of what a one-dimensional face, and edge, looks like: two coordinates are locked, and we are only allowed to move in the third. The "free coordinate" tells us where we can move along the edge. For example, a "back to front" edge will have the second coordinate free, since this coordinate represents "back to front":

<figure><img src="../../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

Considering the corners of the marked edge we see that in both of them the first coordinate is $$1$$ to indicate that the edge is on the right, and the third coordinate is $$0$$ to indicate that the edge is on the bottom, but the second coordinate varies between them, to indicate that the edge goes "from back to front", namely, one of it's vertices, $$(1,0,0)$$, is in the front while the other $$(1,1,0)$$ is in the back.

We can do the same for edges:

<figure><img src="../../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

Here we see that only _one_ coordinate is fixed. The third coordinate is set to $$1$$ in all edges, indicating that the edge is on _top_. Other than that, all coordinates can be set either way. It makes sense that a _two dimensional_ shape, like the face of a cube, will have _two degrees of freedom_. Even without knowing exactly what _two-dimensional_ means formally, it makes sense that a two dimensional shape would have exactly two directions to move around.

Note that the three dimensional cube drawn above has exactly eight corners, corresponding to the eight different strings of three bits. This is the property we extend to higher dimension. When thinking of an $$n$$-dimensional cube, we just think of all sequences of bits (zeros and ones) of length $$n$$. We think of each coordinate as "a direction" (though unfortunately, in more than three dimensions these directions don't have common names like "width", "depth" and "height", so we just call them "the first coordinate", "the second coordinate", etc.), and of its value as either being "close" or "far" in that direction from the point $$(0,\ldots,0)$$.

So how would we define a $$k$$-dimensional face of this $$n$$-dimensional hypercube? Well, we said that a $$k$$-dimensional face must have $$k$$ free dimensions. The way to form such a face is hence to _choose_ the $$k$$ free directions and _fix_ the remaining coordinates. For example, when we consider all the possible one-dimensional faces (edges) of a two-dimensional cube (square), we choose one free dimension, and fix the other dimension. Say we choose the free dimension to be the first one, and fix the second dimension to $$0$$, then we get the two corners $$(0,0)$$ and $$(1,0)$$ corresponding to the left edge:

<figure><img src="../../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

There are two ways to _choose_ the free direction (either first coordinate or second coordinate), and then two possible values to _fix_ in the remaining direction (either $$0$$ or $$1$$), so in total we have $$2\cdot 2 = 4$$ options, which checks out as a square indeed has four edges!

Extending this logic, when considering a $$k$$-dimensional face of an $$n$$-dimensional cube, we first _choose_ the $$k$$ free coordinates. There are of course $${n \choose k}$$ ways to do so. We then _fix_ the remaining $$n-k$$ coordinates. We set each of them to either $$0$$ or $$1$$, so there is a total of $$2^{n-k}$$ ways to fix the coordinates. We have proved:

**Theorem**: an $$n$$-dimensional cube has exactly $$2^{n-k}\cdot{n \choose k}$$ $$k$$-dimensional edges.

So for example, for the cube we have $$n=3$$, and indeed the number of $$k=2$$-dimensional faces is exactly $$2^{3-2}\cdot {3 \choose 2} = 2\cdot 3 = 6$$, and the number of $$k=1$$-dimensional faces is exactly $$2^{3-1}\cdot {3 \choose 1} = 2^2 \cdot 3 = 12$$. And as for the question above? In a seventeen-dimensional cube, the number of eight dimensional edges is $$2^{17-8}\cdot {17 \choose 8} = 12,446,720$$.

**Exercise**: Prove that the number of $$k$$-dimensional faces of an $$n$$ dimensional cube is $$\theta\left(2^{n-k}\cdot n^k\right)$$

