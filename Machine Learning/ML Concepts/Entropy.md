- To quantify similarities and differences
- There is a type of inverse relationship between probability and surprise (when probability is high, surprise is low)
- $Surprise = log_2(\frac1{Probability}) = log(1) - log(Probability)$
- When the probability is close to 0, it is more surprise

- Entropy is the expected value of the surprise

> Entropy of a coin - the expected surprise every time we flip the coin


$$
\sum{x}P(X=x)
$$

- x is the value for surprise
- P(X=x) is the probability of observing that specific value for Surprise

$$
Entropy 
= \sum p(x)log(\frac1{p(x)})
= \sum p(x)[log(1) - log(p(x))]
= \sum p(x)[0 - log(p(x))]
= \sum - p(x)log(p(x))
= -\sum p(x)log(p(x))
$$