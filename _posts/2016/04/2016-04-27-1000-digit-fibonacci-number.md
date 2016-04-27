---
layout: post
title:  1000-digit Fibonacci number
subtitle: Problem 25
category: Project Euler
---
> The Fibonacci sequence is defined by the recurrence relation:
> 
> \$$ \qquad F_n = F_{n−1} + F_{n−2}\text{, where } F_1 = 1 \text{ and } F_2 = 1. $$ 
>
> Hence the first 12 terms will be:
> 
> \$$ 
> \qquad  F_1 = 1 \\
> \qquad  F_2 = 1 \\
> \qquad  F_3 = 2 \\
> \qquad  F_4 = 3 \\
> \qquad  F_5 = 5 \\
> \qquad  F_6 = 8 \\
> \qquad  F_7 = 13 \\
> \qquad  F_8 = 21 \\
> \qquad  F_9 = 34 \\
> \qquad  F_{10} = 55 \\
> \qquad  F_{11} = 89 \\
> \qquad  F_{12} = 144 
> $$
>
> The 12th term, $$ F_{12} $$, is the first term to contain three digits.
> 
> What is the index of the first term in the Fibonacci sequence to contain 1000 digits?

First I wrote a Fibonacci sequence generator. 
It's basically the same one as in [problem 2]({% post_url 2016-03-13-even-fibonacci-numbers %}),
with the difference that I used Clojure's bigint so we can use this function to generate large numbers.

~~~clojure
(defn fib []
  ((fn step [a b]
     (lazy-seq (cons a (step b (+ a b)))))
    1N 1N))
~~~

The problem can now be solved by searching trough this sequence
to find the first number that's larger than the first 1000-digit-number: $$ 10^{999} $$.

~~~clojure
(let [limit (math/expt 10 (dec 1000))]
    (->> (fib)
         (keep-indexed (fn [idx n] (when (<= limit n) (inc idx))))
         (first)))
~~~

Note that I've increased the index by one to make it 1-indexed.

Done!
