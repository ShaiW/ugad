# Geometric Sums and Series

A _geometric sum_ is what happens when we _sum_ many numbers such that any two consecutive numbers have the same ratio. In other words, we start with some number $$a$$ and some ratio $$q$$, and then we add $$a\cdot q$$ and then we add $$a\cdot q^2$$ and so on.  If we apply the ration $$q$$ for $$n$$ times, we get the sum

$$
a+a\cdot q+a\cdot q^{2}+\ldots+a\cdot q^{n}=a\cdot\sum_{k=0}^{n}q^{k}
$$

When would you possibly encounter such a sum? Well, consider the Bitcoin total supply. Initially, the block reward was $$50$$, then it was _halved_ to $$25$$ and then to $$12.5$$ and so on. So it is clear that $$q=1/2$$ in this example, but what is $$a$$. Well, that depends on how many blocks there are between any two halvings. We will get back to this soon.

We note that $$a$$ just multiplies the entire expression, so to understand geometric sums better, it suffices to understand them for $$a=1$$ where we get the simpler expression:

$$
1+q+q^{2}+\ldots+q^{n}
$$

Is there a way to simplify this thing into an expression without any ellipses in the middle (indicating a sum of unknown length)? Well, there is, but it is a bit tricky. The idea is to start with the expression $$1-q^{n+1}$$. Those of you who know about polynomial factorization will recognize that this expression vanishes when substitution $$q=1$$ and can therefor be divided by $$(q-1)$$, but for those who do not understand anything of what I just said, there is a common trick: we are going to add to $$1-q^{n+1}$$ a bunch of stuff, but also subtract that exact same stuff, so that we know that we didn't _change_ the value, but the new expression could be rearranged in a way that relates $$1-q^{n+1}$$ to the geometric sum. It goes like this:

$$
\begin{aligned}1-q^{n+1} & =1+\overset{=0}{\overbrace{\left(q-q\right)}}+\overset{=0}{\overbrace{\left(q^{2}-q^{2}\right)}}+\ldots+\overset{=0}{\overbrace{\left(q^{n}-q^{n}\right)}}-q^{n+1}\\
 & =\left(1+q+q^{2}+\ldots+q^{n}\right)-\left(q+q^{2}+q^{3}+\ldots+q^{n+1}\right)\\
 & =\left(1+q+q^{2}+\ldots+q^{n}\right)-q\left(1+q+q^{3}+\ldots+q^{n}\right)\\
 & =\left(1-q\right)\left(1+q+q^{2}+\ldots+q^{n}\right)
\end{aligned}
$$

and as long as we assume that $$q \ne 1$$, we can divide both sides by $$1-q$$ and get the famous formula:

$$
1+q+q^{2}+\ldots+q^{n}=\frac{1-q^{n+1}}{1-q}
$$

So say that we mine a Bitcoin block exactly once every four years. The first block yielded a reward of $$50$$ bitcoin, the second of $$25$$ and so on. Lets say that we mined exactly $$11$$ blocks (that is, we went through $$n=10$$ halvings). All we need to do is to set $$n=10$$ and $$q=1/2$$ in the formula above, and multiply the result by $$50$$ (to scale for the fact that this was the initial block reward) to get

$$
50\cdot\frac{1-\left(\frac{1}{2}\right)^{11}}{1-\frac{1}{2}}=100\cdot\frac{2047}{2048}\approx99.95
$$

It is easy to complete the case for $$q=1$$, as then we have that $$q^k = 1$$ for all $$k$$ so the sum just becomes $$a(n+1)$$.

If $$q>1$$ then clearly as we add more and more elements (that is, look at larger values of $$n$$), this sum will go to infinity, as _each element_ is larger than the previous. However, if $$0<q<1$$ it seems that as $$n$$ increases, $$q^{n+1}$$ vanishes exponentially fast. In fact, this also happens when $$-1<q<0$$ because, while the powers of $$q$$ change sign, their absolute value still vanishes the same way. A bit of calculus can transform this intuition to a formula for the _infinite geometric sum_, a.k.a. geometric _series_:

$$
\begin{aligned}1+q+q^{2}+\ldots=\sum_{n=0}^{\infty}q^{n}=\frac{1}{1-q} & , & \left|q\right|<1\end{aligned}
$$

When will we ever need such a formula? Well, say we want to compute the _total_ _supply_ of Bitcoin. Say that each _halving epoch_ contains $$N$$ blocks, and in the first epoch, each block emitted a reward of $$R$$. Then we have $$a=N\cdot R$$, so if in every halving the reward is multiplied by $$q$$ we get that the total supply is given by $$\frac{N\cdot R}{1-q}$$. In Bitcoin, we have $$R=50$$, $$N=210,000$$ and $$q=\frac{1}{2}$$, substituting into the formula we get the familiar figure

$$
\frac{210,000\cdot50}{1-1/2}=21,000,000
$$

But wait, isn't Bitcoin emission supposed to end at some point? If so, when?

Say that generally we have some $$\varepsilon > 0$$ such that we set the emission to stop once the reward goes below $$\varepsilon$$. Then what we are looking for is the _smallest_ $$n$$ such that $$R\cdot q^n \le \varepsilon$$, this will tell us how many halvings are expected before the emission stops. If the block delay is $$\lambda$$, we will get that the length of each halving epoch is $$\lambda\cdot N$$ so the total time until emission ends is $$\lambda\cdot N \cdot n$$.

Solving the equation $$R\cdot q^n \le \varepsilon$$ is not hard for anyone who knows [logarithms](asymptotics-growth-and-decay.md#logarithms). By rearranging a bit we get the equation $$\frac{R}{\varepsilon}\le\left(\frac{1}{q}\right)^{n}$$, and taking the logarithm in base $$1/q$$ we get the inequality $$\log_{1/q}\frac{R}{\varepsilon}\le n$$

(We use $$1/q$$ as the basis of our logarithm rather than $$q$$ because logarithms have confusing pathologies and inversions in a base smaller than $$1$$).

Since the $$n$$ we are looking for is the _smallest_ integer satisfying this inequality, we can obtain it by simply _rounding up_ the expression in the left hand side, this is notated like this:

$$
n=\left\lceil \log_{1/q}\frac{R}{\varepsilon}\right\rceil
$$

Putting this all together we have shown

**Proposition**: If a block chain has block delay $$\lambda$$, and an initial block reward $$R$$ which is reduced by a factor of $$q$$ once every $$N$$ blocks until it goes below $$\varepsilon$$, then the time it will take the emission to end is

$$
\lambda\cdot N\cdot\left\lceil \log_{1/q}\frac{R}{\varepsilon}\right\rceil
$$

In Bitcoin we have $$\lambda = 10\text{ min}$$, $$N=210,000$$, $$q=1/2$$, and $$R=50$$. The value $$\varepsilon$$ is the smallest representable Bitcoin value, a _staoshi_, that is worth one hundrendth of one millionth of a bitcoin. In other words, $$\varepsilon = 10^{-8}$$ qubits. We know that $$\lambda$$ and $$N$$ were chosen so that $$\lambda\cdot N = \text{ 4 years}$$. Finally, we can use a calculator to compute that $$\log_{1/q}\frac{R}{\varepsilon}=\log_{2}\left(50\cdot10^{8}\right)\approx32.2$$, so we get that $$n=33$$, so the emission will end after $$33\cdot 4 = 132$$ years. Since Bitcoin launched in 2009, we get that the emissions will end in 2041.

But wait! Why is everyone saying it will end in 2040? Ahhh yes. This computation is only correct as long as $$\lambda$$ is correct. In practice, the [difficulty](../../../part-1-blockchains-and-blockdags/chapter-1-bft-vs.-pow/how-pow-works.md#the-dumb-puzzle) of Bitcoin is constantly increasing, and in the time it takes the [difficulty adjustment](../../../part-1-blockchains-and-blockdags/chapter-1-bft-vs.-pow/how-pow-works.md#difficulty-adjustment-in-bitcoin) to correct it, the block delays become ever so shorter. This difference is not very perceptible in our everyday usage of Bitcoin (it is far smaller than the [typical noisiness of block creation](../probability-theory/the-math-of-block-creation.md)), but it accumulates over time, reducing the estimate by a little bit.
