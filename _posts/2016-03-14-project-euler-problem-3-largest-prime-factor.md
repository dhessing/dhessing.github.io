---
layout: post
title:  "Largest prime factor"
subtitle: "Problem 3"
category: "project-euler"
---

> The prime factors of 13195 are 5, 7, 13 and 29.
> 
> What is the largest prime factor of the number 600851475143 ?

Wikipedia writes that the prime factors of a number are the primes that divide a number equally.
It also shows the following image:

![An example of prime decomposition using 864 as the starting number.](https://upload.wikimedia.org/wikipedia/commons/b/bf/PrimeDecompositionExample.svg)

Inspired by this, I decided I could just keep dividing 600851475143 recursively into smaller and smaller integers that
divide the previous number. Also intuitively, I would have to end up with prime numbers. So let's try that:

{% highlight clojure %}
(defn lowest-divisor [n]
  (->> (range 2 (inc n))
       (filter #(zero? (mod n %)))
       (first)))

(defn prime-factors [n]
  (when-let [prime-factor (lowest-divisor n)]
    (->> (/ n prime-factor)
         (prime-factors)
         (cons prime-factor)
         (lazy-seq))))
{% endhighlight %}

Compared to the above image, this function would give a lopsided tree because it keeps 
taking the lowest divisor:

    864
     /\
    2 432
       /\
      2 216
         /\
        etc...

It doesn't really matter though, the answers still check out:

    (prime-factors 864)
    => (2 2 2 2 2 3 3 3)
    (prime-factors 13195)
    => (5 7 13 29)

And to get the solution:

{% highlight clojure %}
(->> 600851475143
     (prime-factors)
     (apply max))
{% endhighlight %}