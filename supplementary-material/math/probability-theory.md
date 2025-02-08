# Probability Theory

_Probability theory_ is how we discuss likelihood of events. But more than that, it is how we reason about how events _affect each other_. "What is the chance that it will rain on my wedding day?" is a question about the likelihood of two events happening _together_. "Are white cars more likely to be in accidents?" is a question about the probability of one event _given the other_, it is an example of _conditional probability_. Probability gives us tools to _compute_ the answer to such question given quantities _we know_. For example, if we know for each color how many cars of that color are, and how many accidents cars of this color had, we can easily check if white cars are involved in more accidents than the average car.

For the purpose of this book, it suffices to do probability like they did in the 1800s. While there _is_ a formal way to define what an "event" is, but I will just denote events as abstract letters such as $$A$$, $$B$$.  For a fun formal treatment accessible to early undergrads, I recommend Sheldon Ross' [First Course in Probability](https://www.cs.utexas.edu/~abdonm/SDS%20321/a_first_course_in_probability.pdf).

## A Probability Function

A probability function $$\mathbb{P}$$ takes an event, and tells us how likely it is as a number from $$0$$ to $$1$$. For example, if I roll a (fair) die, the event $$A$$ that it came out four has probability $$\mathbb{P}[A] = \frac{1}{6}$$.

What is the probability that it will be _either_ three _or_ four? Let $$A$$ be the event that it is three, and $$B$$ the event that it is four, then the event that it is _either_ is denoted $$A\cup B$$. Note that it is impossible that die is _both_ three _and_ four, we call such events _disjoint_. Since $$A$$ and $$B$$ are disjoint, we get that the probability _either_ happens is the _sum of probabilities_ the do happen. There's a one in six chance to get three, and a one in six chance to get four, hence there's a _two_ in six chance to get either. Or in math:

$$
\mathbb{P}[A\cup B] = \mathbb{P}[A]+\mathbb{P}[B] = \frac{1}{6} + \frac{1}{6} = \frac{1}{3}\text{.}
$$

When events are not disjoint, we _cannot_ sum them up like this. For example, the probability that the result is _odd_ is three in six, or one half. The probability that the result is _at most three_ is also three in six, or one half. So the probability that the result is _either odd or at most three_ is half + half = one? Of course not, because four is _neither_. when summing the two we overlook the fact that a result can be _both_ odd _and_ at most three, making us count these events twice. We said that _three in six_ are odd and _three in six_ are at most three, but three and one are _both_. We _overcounted._

**Exercise**: Given two events $$A$$ and $$B$$, let $$A\cap B$$ be the event that they _both_ happen (if they are disjoint, then $$A\cap B$$ is the _empty event_ $$\emptyset$$ that satisfies $$\mathbb{P}[\emptyset]=0$$), convince yourself that

$$
\mathbb{P}[A\cup B] = \mathbb{P}[A] + \mathbb{P}[B] - \mathbb{P}[A\cap B]\text{.}
$$









.









