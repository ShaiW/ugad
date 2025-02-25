# Asymptotics, Growth, and Decay

When considering stuff like the security and efficiency of algorithms and procedures, and in particular cryptographic schemes and block chains, we need a way to relate them to the various parameters. Consider for example a procedure that _sorts_ an array: it is given a list of numbers, and returns them in increasing order. How can we describe "how fast" that procedure is? We can ask how fast it takes to sort ten numbers, or twenty, but that won't tell us much if in the future we want to use it to sort 1357 or 20545 numbers. We need some way to talk about how the length of the computation _grows_.

For this we use a _function_ $$f$$ that is given the _input length_ $$n$$ and outputs "how long" it would take to sort an array of this size.

Now, the term "how long" is also ill conceived. How do we count? In seconds? CPU cycles? Don't all such metrics depend on the computer running it? Can't they be different for two different lists with the same length?

The answer to all of that is yes. The whole point of discussing _growth_ is to put that aside, and just look at the _trajectory_. This is often called the _asymptotics_ of the function.

It is true that the asymptotics doesn't have _all_ the information, but it tells us what happens in the long run. That "other part", that doesn't tell us about the _rate_ of growth, but gives us _concrete numbers_ (which is often just referred to as "the constants"), is also very important. But from some vantages, like the one permeating most of this book, the asymptotic point of view is the appropriate one.

## Asymptotic Notation

To streamline the notations, computer scientists came up with a bit confusing yet very powerful notational system that captures the "type of growth" of the function while ignoring everything else.

Say that $$f$$ is some function that interests us, like the runtime of a sorting algorithm given the length $$n$$ of the list it needs to sort. As we said, $$f$$ is quite complicated and not even completely defined. However, we can say something like $$f$$ grows "at most linearly". What do we mean by that? That while $$f$$ might be a bit erratic and unexpected, we know that there is a function that _looks like a line_ such that $$f$$ is _almost always_ below it. This would look something like this:

<figure><img src="../../../.gitbook/assets/image (20).png" alt=""><figcaption><p>Illustration of <span class="math">f=O(n)</span>, the red line is the function <span class="math">2n+6</span></p></figcaption></figure>

We use the notation $$f=O(n)$$ to say that there is _some line_ such that $$f$$ is below it _from some point onward_. In other words, we say that $$f=O(n)$$ if there exist two numbers $$m$$ and $$b$$ such that for any _sufficiently large_ $$n$$ we have that $$f(n) < mn + b$$. Note that the notation $$f=O(n)$$ _hides_ the actual numbers $$m,b$$ and only tells us they exist. _This_ is the power of asymptotic notation, it _hides the constants_ and allows us to _qualitatively_ talk about the growth _trend_. So yeah, the constants may _change_ based on the exact architecture of the system that will eventually run the code, but for any system there will be _some_ constants fitting the bill, and _that_ is what captured by this notation.

This is often called the _big oh_ notation.

**Exercise**: Prove that $$n^2 \ne O(n)$$

<details>

<summary>Solution</summary>

Say that $$n^2 = O(n)$$, then there are some $$m$$ and $$b$$ and $$N$$ such that for any $$n>N$$ we have that $$n^2 < mn+b$$. Dividing both sides by $$n$$ we get that $$n < m + \frac{b}{n}$$. However, for $$n>b$$ we have that $$m+\frac{b}{n} < m+1$$, but $$m+1$$ is a constant so we can't have that $$n<m+1$$ for _all_ sufficiently large $$n$$.

</details>

More generally, given two functions $$f$$ and $$g$$ we can denote $$f=O(g)$$ to mean that there are some constants $$m$$ and $$b$$ such that, for sufficiently large $$n$$, it holds that $$f(n)\le m\cdot g(n) + b$$. This allows us to write stuff like $$f = O(n^2)$$ to say "look, I can't tell you _exactly_ how $$f$$ grows, but the rate at which it grows is at most quadratic, if you put in an input three times longer, the growth in runtime is at most by a factor of three squared, that is nine".

**Exercise**: Prove that $$n^2 = O(n^3)$$. More generally, prove that if $$a$$ and $$b$$ are natural numbers then $$n^a = O(n^b)$$ if and only if $$a\le b$$.

Like the big oh notation helps us bound things from above, we have the _big omega_ notation to bound things from _below_. By writing that $$f(n) = \Omega(n)$$ we say that $$f$$ grows _at least linearly_, but it could actually grow faster, just like a _su&#x62;_&#x6C;inear function is still $$O(n)$$.

**Exercise**: Write a formal definition for $$f=\Omega(g)$$

<details>

<summary>Solution</summary>

There are some constants $$m$$ and $$b$$ such that, for sufficiently large $$n$$, it holds that $$f(n)\ge m\cdot g(n) + b$$.&#x20;

</details>

Finally, we have the _theta_ notation, which we use to say that we know _exactly_ what the asymptotics are. In brief, we write that $$f = \theta(g)$$ if it holds that _both_ $$f = O(g)$$ _and_ $$f =\Omega(g)$$. The notation $$f=\theta(g)$$ essentially means "we might not know what $$f$$ is, but as long as we talk about growth rate, we can just pretend that it is $$g$$". Typically, $$g$$ will be much simpler than $$f$$. For example, $$f$$ could be the running time of some complex algorithm, or the probability to revert a transaction in some concrete protocol, and instead of computing $$f$$ explicitly we could just prove that, say, $$f = \theta(n^2)$$.

**Exercise**: A function $$f$$ is called _bounded_ if there is some $$B$$ such that $$f(n) < B$$ for all $$n$$. Let $$f$$ be bounded and let $$g$$ be any function, prove that $$f\cdot g = \theta(g)$$.

**Exercise**: Find a function $$f$$ that is _not linear_, but such that $$f=\theta(n)$$.

Sometimes we don't care about _growth_ but about _decay_. That is, we want to know how _small_ $$f$$ gets as $$n$$ increases. For example, $$f$$ could be the probability that an attacker manages to break your encryption as a function of the length of your secret-key. We can do it using the same notation. For example, $$f(n) = O(1/n)$$ is what we mean when we say that a function _decays linearly_.

## Exponentials

Exponentials and logarithms are how we typically describe things that are _la&#x72;_&#x67;e or _small_. Note that I did not end the latest sentence with "respectively", and this is no coincidence. When we talk about _growth_ (that is, how fast things go _to infinity_), exponential growth is large, while logarithmic growth is _very slow_ (and in between we have e.g. the _polynomial_ growth: linear, quadratic, quartic, etc...). When we talk about _decay_ (that is, how fast things go to _zero_), then exponential decay is _small_.

As an example, consider generating a secret-key. In any encryption scheme, a secret-key can be represented as a _uniformly random_ string of bits, where the length is chosen by the user. Say we use a key of length $$n$$, then how many possible keys are there? The first bit is either $$0$$ or $$1$$, so two options. The second bit is either $$0$$ or $$1$$ too, so two more options _on top_ of each of these two options, leading to four options, and so on. It is easy to get convinced that the number of possible keys is $$2^n$$. Hence, the number of keys _grows exponentially with_ $$n$$.

Now say I am trying to _guess_ your secret key. Since all the bits are uniformly random, we get that they are all _equally likely_, so that the probability I guess correctly (no matter how I guess) is one in $$2^n$$, that is, $$1/2^n$$ which is also commonly written as $$2^{-n}$$. So since the possible number of keys _grows exponentially_ (and the keys are uniform), then the probability to guess a key _decays exponentially_.

To be more concrete, we say that $$f$$ is _exponential_ if there is some constant $$c>0$$ such that $$f=\theta(e^{cn})$$ where $$e$$ is the infamous [Euler's number](https://en.wikipedia.org/wiki/E_\(mathematical_constant\)). Why $$e$$ and not any other number? Because it is comfortable. The fact is that for any number $$b$$ there is a number $$c$$ such that $$b^n = e^{cn}$$. (This number $$c$$ is called the _natural logarithm_ of $$b$$, denoted $$\ln b$$, and we will discuss it shortly.) So you can replace $$e$$ with any positive number larger than $$1$$ and obtain a completely equivalent definition.

**Exercise**: Let $$b>1$$, let $$f$$ satisfy that there is some $$c>0$$ such that $$f=\theta\left(b^{cn}\right)$$, prove that $$f$$ is exponential.

