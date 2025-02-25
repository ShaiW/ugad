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

Binomial coefficients come up _everywhere_, and have very nice properties to them.

One of them is _Pascal's_ triangle. To draw the triangle, follow these rules. First, write the number $$1$$ at the middle of the top line of the paper, and then write it again twice, like so:

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

In the next line we write _three_ numbers. The first and last would be $$1$$, and the middle number will be the _sum of two numbers above it_:

<figure><img src="../../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

We can keep going, following this pattern, the next few lines will look like this:

<figure><img src="../../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

What does this have to do with everything? Well, remarkably, if we number the lines, starting with zero, then the $$k$$th number if the $$n$$th line is exactly $${n \choose k}$$.

**Exercise**: Assuming that Pascal's triangle satisfy this property, prove that for any $$n,k$$ it holds that $${n \choose k} = {n-1 \choose k} + {n-1 \choose k-1}$$.

**Exercise**: Show that if $$0\le k \le r \le n$$ then $${n \choose r} \cdot {r \choose k} = {n \choose k} \cdot {n - k \choose r - k}$$. **Hint**: you don't really need math to solve this, you just need to tell yourself the right story.
