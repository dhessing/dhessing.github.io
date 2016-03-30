---
layout: post
title:  "Summation of primes"
subtitle: "Problem 10"
category: Project Euler
---

> The sum of the primes below 10 is 2 + 3 + 5 + 7 = 17.
> 
> Find the sum of all the primes below two million.

For this one I re-used the prime-generator from [problem 7](http://127.0.0.1:4000/2016/03/18/10001st-prime/).

{% highlight clojure %}
(->> (project-euler.problem-007/prime-seq)
     (take-while (partial > 2e6))
     (apply +))
{% endhighlight %}

It works, but takes about 4 seconds to solve on my machine.
I guess this is mainly because I used an incremental sieve for problem 7, because we needed the nth-prime.
To make this problem faster we can write ourselves a normal sieve, or cheat and use Java-interop.

Let's do that:

{% highlight clojure %}
(defn prime? [n]
  (.isProbablePrime (BigInteger/valueOf n) 10))

(->> (range 2e6) 
     (filter prime?) 
     (apply +))
{% endhighlight %}

Done!

