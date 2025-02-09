# Random Variables

You would notice that the discussion about [probability spaces](probability-spaces.md) was a little too verbose for comfort. Having to say stuff like "the probability of the event that the die is either odd or below three" is quite tiring, and will definitely become increasingly cumbersome as we deal with more complex probabilities.

To streamline the discussion, we introduce the notation of _random variables_. A random variable is like a number, except we don't know exactly what it is, only how it _distributes_.

So if I start my discussion with "let $$X$$ be a fair die", anyone who studied a bit of probability will interpret this as "$$X$$ is a random variable that can have an integer value between $$1$$ and $$6$$, all of which are equally probable". Then, instead of the cumbersome "the probability that result of the tie toss less than three", we can just write $$\mathbb{P}[X\le 3]$$.

We can also use random variables to describe events, for example, the event "the result of throwing the die was even", we can write $$A=\left\{ X=2\text{ or }X=4\text{ or }X=6\right\}$$ or more conventionally $$A=\left\{ X\in\left\{ 2,4,6\right\} \right\}$$ or even simply $$A=\left\{ X\text{ is even}\right\}$$. Whatever is most comfortable for you.

This might seem like a cosmetic convenience, but defining random variables will actually go a long way in helping us understand complex situation. With all their simplicity, they turn out to be a very powerful abstraction, that allows us to focus on _how_ things distribute while disregarding what they _are_.

## Discrete Random Variables

We will introduce the definition of a random variable specifically for [discrete probability spaces](random-variables.md#discrete-random-variables).

Recall that in such a space is defined over a _sample space_ $$\Omega$$ of atomic events. A _random variable_ $$X$$ is simply a function that assigns to each atomic event a _real number_. What does that number represent? Whatever we want!

In the die toss case (where $$\Omega = \{\omega_1,\ldots,\omega_6\}$$)

