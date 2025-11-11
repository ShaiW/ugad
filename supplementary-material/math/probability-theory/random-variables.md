# Random Variables

You would notice that the discussion about [probability spaces](probability-spaces.md) was a little too verbose for comfort. Having to say stuff like "the probability of the event that the die is either odd or below three" is quite tiring, and will definitely become increasingly cumbersome as we deal with more complex probabilities.

To streamline the discussion, we introduce the notation of _random variables_. A random variable is like a number, except we don't know exactly what it is, only how it _distributes_.

So if I start my discussion with "let $$X$$ be a fair die", anyone who studied a bit of probability will interpret this as "$$X$$ is a random variable that can have an integer value between $$1$$ and $$6$$, all of which are equally probable". Then, instead of the cumbersome "the probability that result of the tie toss less than three", we can just write $$\mathbb{P}[X\le 3]$$.

We can also use random variables to describe events, for example, the event "the result of throwing the die was even", we can write $$A=\left\{ X=2\text{ or }X=4\text{ or }X=6\right\}$$ or more conventionally $$A=\left\{ X\in\left\{ 2,4,6\right\} \right\}$$ or even simply $$A=\left\{ X\text{ is even}\right\}$$. Whatever is most comfortable for you.

This might seem like a cosmetic convenience, but defining random variables will actually go a long way in helping us understand complex situation. With all their simplicity, they turn out to be a very powerful abstraction, that allows us to focus on _how_ things distribute while disregarding what they _are_.

## Discrete Random Variables

We will introduce the definition of a random variable specifically for [discrete probability spaces](random-variables.md#discrete-random-variables).

Recall that in such a space is defined over a _sample space_ $$\Omega$$ of atomic events. A _random variable_ $$X$$ is simply a function that assigns to each atomic event a _real number_. What does that number represent? Whatever we want!

In the die toss case (where $$\Omega = \{\omega_1,\ldots,\omega_6\}$$), if we care about the _result_ we can set $$X(\omega_n)=n$$. But we can do a whole lot of other stuff. For example, we can consider $$\Omega$$ to be the set of all possible results of flipping a coin $$100$$ times. Each atomic event $$\omega \in \Omega$$ corresponds to one of the $$2^{100}$$ possible lists of length $$100$$ of "heads" and "tails". If we only care about the _amount_ of heads we can define $$X(\omega)$$ to be the number of heads (regardless of where they appear in the list). Then a complex event such as "the probability that between 27 and 34 flips landed on heads" admits the succinct description $$\{27\le X\le 34\}$$.

We can define variables $$X$$ as complex as the imagination would let us, for example $$X(\omega)$$ could be the length of the longest succession of identical results in $$\omega$$, if that's what we're interested in.

## Expectation

Given a random variable $$X$$, we define its _expectation_ as the average value it could get, _weighted_ by their probability.

For example, say that we flip a coin. The probability space is $$\Omega=\{H,T\}$$ (which stand for _heads_ and _tails_ respectively). Say that we consider $$H$$ the desired result. We can model this by defining a random variable $$X$$ satisfying $$X(H) = 1$$ and $$X(T) = 0$$.

If our coin is fair, the _expectation_ of $$X$$, denoted $$\mathbb{E}[X]$$, is intuitively one half, because there is a one half chance that it is zero and a one half chance that it is one. In math:

$$
\begin{aligned}\mathbb{E}\left[X\right] & =\mathbb{P}\left[H\right]\cdot X\left(H\right)+\mathbb{P}\left[T\right]\cdot X\left(T\right)\\
 & =\frac{1}{2}\cdot1+\frac{1}{2}\cdot0\\
 & =\frac{1}{2}
\end{aligned}
$$

More generally speaking, if $$X$$ is a random variable over some discrete probability space $$(\Omega,\mathbb{P})$$ we have the general formula

$$
\mathbb{E}\left[X\right]=\sum_{\omega\in\Omega}\mathbb{P}\left[\omega\right]\cdot X\left(\omega\right)
$$

So for example, if $$X$$ is the result of tossing a fair die, we have that

$$
\begin{aligned}\mathbb{E}\left[X\right] & =\sum_{n=1}^{6}\mathbb{P}\left[\omega_{n}\right]\cdot X\left(\omega_{n}\right)\\
 & =\sum_{n=1}^{6}\frac{1}{6}\cdot n\\
 & =3\frac{1}{2}
\end{aligned}
$$

Let us work out a more complicated example. Say that we have an unfair day with the following properties: all odd results are equally likely, all even results are equally likely, but an even result is three times as likely as an odd result, What is the expected value of tossing the die?

Let $$X$$ be the random variable describing the die. If we denote $$\mathbb{P}\left[X\text{ is odd}\right]=x$$ we get that $$\mathbb{P}\left[X\text{ is even}\right]=3x$$. However, since a die is _always either_ odd or even _but never both_, we get that

$$
\begin{aligned}1 & =\mathbb{P}\left[X\text{ is odd or even}\right]\\
 & =\mathbb{P}\left[X\text{ is odd}\right]+\mathbb{P}\left[X\text{ is even}\right]\\
 & =x+3x\\
 & =4x
\end{aligned}
$$

From which we get that $$\mathbb{P}\left[X\text{ is odd}\right]=\frac{1}{4}$$.

However, since we assumed that all odd outcomes are equally likely, we get that $$\mathbb{P}[\omega_1] = \mathbb{P}[\omega_3] = \mathbb{P}[\omega_5]$$ and it follows that

$$
\begin{aligned}\frac{1}{4} & =\mathbb{P}\left[X\text{ is odd}\right]\\
 & =\mathbb{P}\left[\omega_{1}\right]+\mathbb{P}\left[\omega_{3}\right]+\mathbb{P}\left[\omega_{5}\right]\\
 & =3\cdot\mathbb{P}\left[\omega_{1}\right]
\end{aligned}
$$

from which it follows that $$\mathbb{P}[\omega_1] = \mathbb{P}[\omega_3] = \mathbb{P}[\omega_5]= \frac{1}{12}$$, and we can similarly prove that $$\mathbb{P}[\omega_2] = \mathbb{P}[\omega_4] = \mathbb{P}[\omega_6]= \frac{1}{4}$$. We then get that

$$
\begin{aligned}\mathbb{E}\left[X\right] & =\sum_{n=1}^{6}\mathbb{P}\left[\omega_{n}\right]\cdot X\left(\omega_{n}\right)\\
 & =\frac{1}{12}\cdot1+\frac{1}{4}\cdot2+\frac{1}{12}\cdot3+\frac{1}{4}\cdot4+\frac{1}{12}\cdot5+\frac{1}{4}\cdot6\\
 & =\frac{1+3+5}{12}+\frac{2+4+6}{4}\\
 & =3\frac{3}{4}
\end{aligned}
$$

For a spicier example, we can ask what is the expected number of times we need to flip a fair coin before we get heads. We already [modeled this](https://shai-deshe.gitbook.io/understanding-blockdags-and-ghostdag/supplementary-material/math/probability-theory/probability-spaces#finite-and-discrete-probability-spaces) with the sample space $$\Omega = \{\omega_1,\omega_2,\ldots\}$$ and the probability function $$\mathbb{P}[\omega_n] = 2^{-n}$$. The random variable $$X(\omega_n) = n$$ maps each atomic event to the number of coin flips it implies, and its expectation is

$$
\begin{aligned}\mathbb{E}\left[X\right] & =\sum_{n=1}^{\infty}\mathbb{P}\left[\omega_{n}\right]\cdot X\left(\omega_{n}\right)\\
 & =\sum_{n=1}^{\infty}\frac{n}{2^{n}}\\
 & =2
\end{aligned}
$$

where the last equality is not hard to show, but requires a bit of calculus.

<details>

<summary>Proving the equality*</summary>

We use the fact that if $$|x|<1$$ then the sequence $$\sum_{n=0}^{\infty}x^{n}$$ absolutely converges around $$x$$. This means we are allowed to change the order of summation and differentiation, getting

$$\begin{aligned}\sum_{x=1}^{\infty}nx^{n} & =x\sum_{x=1}^{\infty}nx^{n-1} &  & =x\sum_{x=0}^{\infty}\left(n+1\right)x^{n}\\  & =x\sum_{x=0}^{\infty}\frac{d}{dx}x^{n+1} &  & =x\frac{d}{dx}\sum_{x=0}^{\infty}x^{n+1}\\  & =x\frac{d}{dx}x\sum_{x=0}^{\infty}x^{n} &  & =x\frac{d}{dx}\frac{x}{1-x}\\  & =\frac{x}{\left(1-x\right)^{2}} \end{aligned}$$\\

and setting $$x=1/2$$ we get the desired result.

</details>

We can generalize this to unfair coins, to show that if a coin lands on head with probability $$p$$, the expected number of times we'd have to flip it before seeing heads is $$1/p$$.

This is a _very important result_! It tells that the amount of attempts before we are successful at something is inversely proportional to how likely we are to succeed each time (at least when talking about stuff like participating in a raffle, that we can't _improve_ at and increase our chances over time).

## The Distribution of a Random Variable

What's nice about a random variable is that we often _don't really care_ about the sample space its defined above. We really only care how it _distributes_. Often times, we can tell very different origin stories for random variables, but find out that they are exactly the same.

For example, consider we flip a fair coin until we get tails, and let $$X(\omega_n)$$ be zero of $$n$$ is even or one if $$n$$ is odd. Consider that we toss a fair die once, and let $$Y(\omega_n)$$ be zero for $$n=1,2$$ or one for $$n=3,\ldots,6$$. We've already seen that $$\mathbb{P}[X = 1]$$ is $$2/3$$ and you should be able to see that $$\mathbb{P}[Y = 1]$$ is $$2/3$$ as well. Since both could only be $$0$$ or $$1$$ we get that $$\mathbb{P}[X = 0]=\mathbb{P}[Y=0]$$.

In other words, $$X$$ and $$Y$$ distribute equally. They might have a very different origin story, but they are _identical_ as random variables. We denote this as $$X\sim Y$$.

This notation is also very powerful, as it allows to consider a few ubiquitous random variables, and exploring them in the abstract would give useful results for any random variable we run into that distributes the same.

The simplest random variable is a _Bernoulli experiment_. The sample space is $$\Omega=\{\omega_0,\omega_1\}$$ and we set the random variable $$X(\omega_b)=b$$. We typically call the event $$\omega_0$$ _failure_ and the event $$\omega_1$$ _success_. Any probability function on $$\Omega$$ is defined by the _success probability_: a single number $$0<p<1$$ satisfying $$\mathbb{P}[\omega_1] = p$$. It immediately follows that $$\mathbb{P}[\omega_0] = 1-p$$.

We call the resulting $$X$$ a _Poisson variable with parameter p_ and denote it $$Poi(p)$$.

With this language in hand, if $$X$$ is the random variable defined on a fair die as $$X(\omega_1)=X(\omega_2) = 0$$ and $$X(\omega_3)=X(\omega_4)=X(\omega_5)=X(\omega_6)=1$$ then we have that $$X\sim Poi(p)$$.

Another important variable is a _Binomial variable_. Say we repeat a Poisson experiment with parameter $$p$$ for $$n$$ times, and let $$X$$ be the random variable that counts how many of the repetitions were successful. What is the probability that $$X=k$$? Clearly, for $$k>n$$ it is zero, because we can't have flipped more heads than we flipped coins. How many sequences there are for which _exactly_ $$k$$ flips are heads? Well, there were a total of $$n$$ flips, and we have to _choose_ $$k$$ of them. The number of ways to make this choice is the [binomial coefficient](../stuff-you-should-know/binomial-coefficients.md) $${n \choose k}$$. And what is the probability of each such sequence? Well, for each coin that landed on heads, it did so with probability $$p$$. Since there are $$k$$ of those, the probability they _all_ landed on heads is $$p^k$$. Similarly, all the remaining $$n-k$$ coins landed on tails, which happens with probability $$(1-p)$$. Multiplying all of this together we get that

$$
\mathbb{P}\left[X=k\right]={n \choose k}p^{k}\left(1-p\right)^{n-k}
$$

This leads us to the following definition: if $$X$$ is a random variable, we say that $$X$$ _distributes binomially with parameters_ $$n,p$$, and denote $$X\sim \mathcal{B}(n,p)$$, if for any $$k=0,\ldots,n$$ we have that

$$
\mathbb{P}\left[X=k\right]={n \choose k}p^{k}\left(1-p\right)^{n-k}
$$

**Exercise**: Verify that $$\sum_{k=0}^n \mathbb{P}[\mathcal{B}(n,p)=k] = 1$$

**Exercise**: Show that $$\mathbb{E}\left[\mathcal{B}\left(n,p\right)\right]$$, namely that if we flip a $p$-coin $n$ times, we expect $pn$ coins to land on heads.

<details>

<summary>Solution</summary>

Using the [binomial formula](../stuff-you-should-know/binomial-coefficients.md):

$$\begin{aligned}\mathbb{E}\left[\mathcal{B}\left(n,p\right)\right] & =\sum_{k=0}^{n}{n \choose k}p^{k}\left(1-p\right)^{n-k}k\\  & =n\sum_{k=1}^{n}{n-1 \choose k-1}p^{k}\left(1-p\right)^{n-k}\\  & =n\sum_{k=0}^{n-1}{n-1 \choose k}p^{k+1}\left(1-p\right)^{n-\left(k+1\right)}\\  & =np\sum_{k=0}^{n-1}{n-1 \choose k}p^{k}\left(1-p\right)^{\left(n-1\right)-k}\\  & =np \end{aligned}$$

where in the second equality we used the fact that

$${n \choose k}k=\frac{n!}{k!\left(n-k\right)!}k=\frac{\left(n-1\right)!n}{k\left(k-1\right)!\left(n-k\right)}={n-1 \choose k-1}n$$

</details>

## Laws of Large Numbers

What is the meaning of expectation? What motivates this definition?

We claimed that expectation is what happens in the "average case", but why is this proclamation justified? It certainly isn't something that's obvious from the definition.

Consider for example a fair coin toss, and say that if it lands on heads I win one dollar, but if it lands on tails I get nothing. That is, my profit is described by the random variable $$X$$ satisfying $$X(H)=1$$ and $$X(T)=0$$. We know how to compute that $$\mathbb{E}[X]=1/2$$, but why is that the "average"?

Well, say I repeat the game $$10$$ times, what is the probability that I got, say, less than three heads? The number of heads distributes like $$\mathcal{Bin}(10,1/2)$$, and we know that

$$
\begin{aligned}\mathbb{P}\left[\mathcal{Bin}\left(10,1/2\right)\le3\right] & =\mathbb{P}\left[\mathcal{Bin}\left(10,1/2\right)=0\right]+\mathbb{P}\left[\mathcal{Bin}\left(10,1/2\right)=1\right]\\
 & +\mathbb{P}\left[\mathcal{Bin}\left(10,1/2\right)=2\right]+\mathbb{P}\left[\mathcal{Bin}\left(10,1/2\right)=3\right]\\
 & =\frac{1}{2^{10}}\left({10 \choose 0}+{10 \choose 1}+{10 \choose 2}+{10 \choose 3}\right)\\
 & =\frac{176}{1024}\approx17.2\%
\end{aligned}
$$

Using the symmetry of the problem (the probability of exactly 3 heads is exactly like the probability of exactly 3 tails, which is the same as the probability of exactly 7 heads), we can conclude that we will flip between four and six heads (that is, will be at distance at most one from the average) with a probability of about $$65.4\%$$. We already see the concentration around the half, with less than a third of the options getting almost two thirds of the probability.

Using a binomial distribution to understand how this progresses as $$n$$ becomes larger is possible, but a bit tiresome. Instead, we appeal to one of computer science's most beloved secret weapon: [the Chernoff bound](https://en.wikipedia.org/wiki/Chernoff_bound).

There is no reason to describe the bound here, I will just tell you that it is an extremely powerful weapon to the "scattering" of repeating many independent (though not necessarily identical!) experiments. Applying Chernoff to coin tossing, one can prove that if we toss a coin $$n$$ times, the probablity to see more than $$n/2+\sqrt{6n\cdot\ln n}$$ flips of the same result is less than $$2/n^4$$. By plugging numbers we can see, for example, that if we flip a thousand independent fair coins, the probability that get the same result more than $$700$$ times is less than one in $$500$$ billion, and if we flip a _million_ such coins, the number of heads will be within $$1000$$ flips from half a million with probability above $$99.99999999999999999998\%$$.

This is a special example of a _law of large numbers_. Such laws give us conditions under which _repeating experiments and averaging_ will _converge to the expectation_ as we average more and more experiments. To demonstrate it, I had my computer flip ten thousand random coins, and averaged the difference between heads and tails. I repeated the experiment five times, and plotted each. This is the result:

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Flipping ten thousand coins and tracking the result. The <span class="math">x</span> axis is the number of repetitions, the <span class="math">y</span> axis is the difference between heads and tails over the number of repetitions. We see that while the paths start very differently, they converge very quickly to <span class="math">0</span>.</p></figcaption></figure>
