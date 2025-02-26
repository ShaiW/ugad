# The Binomial Formula

The binomial formula is extremely useful for a variety of calculations. It follows a nice observation which follows closely from computing what results when we expend the expresssion $$(a+b)^n$$ for various values of $$n$$.

We have that:

$$
\begin{aligned}\left(a+b\right)^{0} & =1\\
\left(a+b\right)^{1} & =a+b\\
\left(a+b\right)^{2} & =a^{2}+2ab+b^{2}\\
\left(a+b\right)^{3} & =a^{3}+3a^{2}b+3ab^{2}+b^{3}
\end{aligned}
$$

We can rewrite this in a way that might seem a bit odd but will serve us well:

$$
\begin{aligned}\left(a+b\right)^{0} & =1\cdot a^{0}b^{0}\\
\left(a+b\right)^{1} & =1\cdot a^{1}b^{0}+1\cdot a^{0}b^{1}\\
\left(a+b\right)^{2} & =1\cdot a^{2}+2\cdot a^{1}b^{1}+1\cdot b^{2}\\
\left(a+b\right)^{3} & =1\cdot a^{3}+3\cdot a^{2}b^{1}+3\cdot a^{1}b^{2}+1\cdot b^{3}
\end{aligned}
$$

Is this starting to look familiar? What about if we remove all the annoying $$a$$'s and $$b$$'s and plus signs?

$$
\begin{aligned} & 1\\
 & 1\ 1\\
 & 1\ 2\ 1\\
 & 1\ 3\ 3\ 1
\end{aligned}
$$

Of course! These are the first few lines of [Pascal's triangle](binomial-coefficients.md#pascals-triangle)!

In other words, we can rewrite the last line of the expression above as:

$$
\left(a+b\right)^{3}={3 \choose 0}\cdot a^{3}+{3 \choose 1}\cdot a^{2}b^{1}+{3 \choose 2}\cdot a^{1}b^{2}+{3 \choose 3}\cdot b^{3}
$$

How does this happen? Well, remember that when we expend $$(a+b)^3$$ we actually go over each "copy" of $$(a+b)$$ and _choose_ either $$a$$ or $$b$$. "Choose", here's that word again. Say we chose $$b$$ exactly two out of the three times, then we got the monomial $$a\cdot b^2$$. How many ways are there to choose $$b$$ two out of the three times? Obviously $${3 \choose 2}$$.

Similarly, if we ask ourselves what is the coefficient of $$a^{n-k}\cdot b^k$$ in $$(a+b)^n$$. That is, how many different ways are there to choose $$b$$ in exactly $$k$$ of the $$n$$ copies of $$(a+b)$$? We know very well that the answer is $${n \choose k}$$.

Putting this all together, we get the remarkable formula known as _Newton's binomial formula_:

$$
\begin{aligned}\left(a+b\right)^{n} & ={n \choose 0}a^{n}+{n \choose 1}a^{n-1}\cdot b+\ldots+{n \choose k}a^{n-k}b^{k}+\ldots+{n \choose n}b^{n}\\
 & =\sum_{k=0}^{n}{n \choose k}a^{n-k}b^{k}
\end{aligned}
$$

We will revisit this formula when we discuss probabilities in the next part of the appendix, but lets see a nice example of how it can be used to prove fun stuff.

For example, what do you think should be the alternating sum of the binomial coefficients:

$$
{n \choose 0}-{n \choose 1}+{n \choose 2}-{n \choose 3}+\ldots+\left(-1\right)^{n}{n \choose n}
$$

Well, if $$n$$ is _odd_ we can use the fact that $${n \choose k} = {n \choose n-k}$$ and "pair them up" to get

$$
\begin{aligned} & {n \choose 0}-{n \choose 1}+{n \choose 2}-{n \choose 3}+\ldots-{n \choose n}\\
 & =\left({n \choose 0}-{n \choose n}\right)+\left({n \choose 1}-{n \choose n-1}\right)+\ldots+\left({n \choose \left\lfloor n/2\right\rfloor }-{n \choose \left\lceil n/2\right\rceil }\right)\\
 & =0
\end{aligned}
$$

{% hint style="info" %}
For those who do not know these notations, for any $$x$$, we use $$\left\lfloor x\right\rfloor$$ and $$\left\lceil x\right\rceil$$ to denote _rounding it_ down or up respectively to the nearest _integer_.
{% endhint %}

But that won't work when $$n$$ is even. So what can we do? In fact, we can prove it for all cases ($$n$$ even or odd) in one fell swoop without this ugly "pairing up". We just need to rewrite the sum a little differently:

$$
\begin{aligned} & {n \choose 0}-{n \choose 1}+{n \choose 2}-{n \choose 3}+\ldots-{n \choose n}\\
 & =\sum_{k=0}^{n}\left(-1\right)^{k}{n \choose k}\\
 & =\sum_{k=0}^{n}1^{n-k}\left(-1\right)^{k}{n \choose k}
\end{aligned}
$$

which is exactly the binomial formula with $$a=1$$ and $$b=-1$$!

We thus get:

$$
\sum_{k=0}^{n}1^{n-k}\left(-1\right)^{k}{n \choose k}=\left(1+\left(-1\right)\right)^{n}=0
$$

**Exercise**: Prove that the sum of $$n$$th binomial coefficients $${n \choose 0} + {n \choose 1} + \ldots + {n \choose n}$$ is exactly $$2^n$$. Why is this answer expected?

