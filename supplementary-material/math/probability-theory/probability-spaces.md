# Probability Spaces

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

<details>

<summary>Solution</summary>

Intuitively, we have that $$A\cap B$$ is all the events that are in both $$A$$ and $$B$$, so we count them once in $$\mathbb{P}[A]$$ and a second time in $$\mathbb{P}[B]$$, so we have to subtract them once to balance out the double counting.

Formally, we can write $$A' = A \setminus B$$  (that is $$A'$$ is the event that $$A$$ happened and $$B$$ _didn't_), So $$A\cup B = A' \cup B$$ but $$A'$$ and $$B$$ are _disjoint_, so we have:

$$\mathbb{P}[A\cup B] = \mathbb{P}[A' \cup B] = \mathbb{P}[A'] + \mathbb{P}[B]$$

However, we also have that $$A = A' \cup (A\cap B)$$, where the union is also disjoint, so we have that $$\mathbb{P}[A] = \mathbb{P}[A'] + \mathbb{P}[A\cap B]$$, or equivalently that $$\mathbb{P}[A'] = \mathbb{P}[A] - \mathbb{P}[A\cap B]$$.

Substituting this into the equation above we get

$$\mathbb{P}[A\cup B] = \mathbb{P}[A' ]  + \mathbb{P}[B] = \mathbb{P}[A]  + \mathbb{P}[B]-\mathbb{P}[A\cap B]$$

as needed.

</details>



## Atomic Events

We say that an event $$A$$ _contains_ another event $$B$$, and denote $$B\subseteq A$$, if $$A$$ is _guaranteed_ to happen _given_ that $$B$$ happens. For example, the event $$X=4$$ is contained in the event that $$X$$ is even. An event is called _atomic_ if it has positive probability, but it does not contain any other event that has positive probability. For example, a fair die has six atomic events, for each $$n=1,\ldots,6$$ we have the atomic event $$X=n$$.

It is conventional to call the set of atomic events $$\Omega$$. For example $$\Omega = \{\omega_1,\ldots,\omega_6\}$$ describes the atomic events of _any_ six-sided die. The die doesn't have to be _fair_, but it does have to satisfy that each of its six sides has _some_ probability to be the result.

**Exercise**: prove that any two different atomic events are disjoint

The set $$\Omega$$ is also commonly called the "sample space", as it describes all possible results of an experiment.

## Finite and Discrete Probability Spaces

Given a set of atomic events $$\Omega$$, we can endow them with a probability function a _probability function_ $$\mathbb{P}$$ that assigns to each atomic event $$\omega\in\Omega$$ a number $$0<\mathbb{P}[\omega]\le1$$. The pair $$(\Omega,\mathbb{P})$$ is called a _discrete probability space_ if the probabilities of all atomic events sum to one. In other words, if _all events_ can be described as a union of atomic events.

Note that since atomic events are disjoint, the definition of $$\mathbb{P}$$ naturally extends to all events, simply as the sum of atomic events that comprise them. For example, if we $$A$$ be the event that the result is at most three, then we have

$$
\mathbb{P}[A]=\mathbb{P}[\omega_{1}\cup\omega_{2}\cup\omega_{3}]=\mathbb{P}[\omega_{1}]+\mathbb{P}[\omega_{2}]+\mathbb{P}[\omega_{3}]
$$

If we want to describe a _fair die_, we will define $$\Omega = \{1,\ldots,6\}$$ and $$\mathbb{P}[n] = \frac{1}{6}$$ for any $$n=1,\ldots,6$$, and we will indeed get that the probability that the die is less than three is

$$
\mathbb{P}[A]=\mathbb{P}[\omega_{1}\cup\omega_{2}\cup\omega_{3}]=\mathbb{P}[\omega_{1}]+\mathbb{P}[\omega_{2}]+\mathbb{P}[\omega_{3}]=\frac{1}{6}+\frac{1}{6}+\frac{1}{6}=\frac{1}{2}
$$

With a bit of thought, one can convince herself that if $$\Omega$$ is finite then any probability space over $$\Omega$$ is discrete. However, a space can be discrete also if $$\Omega$$ is infinite. For example, lets say I flip a fair coin util it lands on head, how many flips would it take? Well, for any positive integer $$n$$ there is _some_ probability that it will take $$n$$ attempts. So our sample space is has an event for _any positive natural_ numbe $$\Omega = \{\omega_1,\omega_2,\ldots\}$$.

The probability that a single flip of a fair coin will land on head is $$1/2$$. For it to take _exactly_ two attempts means that the first flip landed on tails and the second landed on heads, which has a probability $$1/4$$ (as it is one of four equally likely results of flipping two coins). It would take _exactly three_ attempts with probability $$1/8$$ and so on. The pattern is quite obvious: the probability it would take exactly $$n$$ flips is $$\left(\frac{1}{2}\right)^n$$, which we can more comfortable write as $$2^{-n}$$.

We said that the probabilities sum to one, and indeed if we let $$A_n$$ be the event that it took $$n$$ flips, we get that

$$
\mathbb{P}[\omega_1] + \mathbb{P}[\omega_2] + \mathbb{P}[\omega_3] + \ldots = \frac{1}{2} +\frac{1}{4} + \frac{1}{8} + \ldots = 1
$$

I will not prove the last equality, but I can provide a beautiful visual intuition:

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can now ask ourselves, say, what is the probability this would take _at most ten tries_, and compute that the result is

$$
\sum_{i=1}^{10}2^{-n} = 1-2^{-10} = \frac{1023}{1024} \approx 99.8\%
$$

**Exercise**: prove a more generalized version of the equality above, namely that the probability it would take at most $$n$$ coin flips is $$1-2^{-(n-1)}$$. Hint, recall the _finite geometric sum formula_: for any $$q$$ we have that

$$
1+q+q^2 + \ldots +  q^n = \sum_{k=0}^n q^k = \frac{1-q^{n+1}}{1-q}
$$

<details>

<summary>Solution</summary>

Setting $$q = \frac{1}{2}$$ we have that

$$\begin{aligned}\sum_{k=1}^{n}\frac{1}{2^{k}} & =\sum_{k=0}^{n}\frac{1}{2^{k}}-1\\  & =\frac{1-\frac{1}{2^{n+1}}}{1-\frac{1}{2}}-1\\  & =2\left(1-\frac{1}{2^{n+1}}\right)-1\\  & =2-2\cdot\frac{1}{2^{n+1}}-1\\  & =1-\frac{1}{2^{n}} \end{aligned}$$

</details>

We can even look at _infinite_ unions. For example, we can ask ourselves what is the probability that it took us an _even_ number of attempts? One can compute that this comes out to

$$
\sum_{n=1}^{\infty}\omega_{2n}=\sum_{n=1}^{\infty}\frac{1}{2^{2n}}=\sum_{n=1}^{\infty}\frac{1}{4^{n}}=\frac{1}{3}
$$

**Exercise**: prove the above inequality. Hint, recall the _infinite_ geometric sum formula: if $$-1<q<1$$ then

$$
1+q+q^2 + \ldots  = \sum_{k=0}^\infty q^k = \frac{1}{1-q}
$$

**Remark**: When is a probability space not discrete? We won't talk about them much, and _definitely_ not introduce them formally, but there could be _continuous probability spaces_. For example, we can uniformly sample _real number_ between $$0$$ and $$1$$. One can easily see that the probability to sample any particular number is $$0$$. Worse yet, for _any_ $$0\le a<b \le 1$$ the event "we sampled a number between $$a$$ and $$b$$" is not atomic, as it strictly contains the event "we sampled a number between $$a'$$ and $$b'$$" whenever $$a<a'<b'<b$$. So such events are never atomic, and a similar approach shows that, in fact, there are _no atomic events at all_ in this space. This means that our definitions are too narrow to expend to the continuous case, and while that definitely can be done, we have no reason to do so.
