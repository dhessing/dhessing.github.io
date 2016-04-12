---
layout: post
title:  Factorial digit sum
subtitle: Problem 20
category: Project Euler
---

> n! means n × (n − 1) × ... × 3 × 2 × 1
>  
> For example, 10! = 10 × 9 × ... × 3 × 2 × 1 = 3628800,
> and the sum of the digits in the number 10! is 3 + 6 + 2 + 8 + 8 + 0 + 0 = 27.
>  
> Find the sum of the digits in the number 100!

Easily solved. I'll write a factorial function and re-use the to-digit from [problem 8]({% post_url 2016-03-19-largest-product-in-a-series %}).

The factorial looks as follows:

~~~clojure
(defn factorial [n]
  (reduce * (range 1 (inc n))))
~~~

Let's check it:

~~~clojure
(factorial 101)
ArithmeticException integer overflow  clojure.lang.Numbers.throwIntOverflow (Numbers.java:1424)
~~~

Oops. I need to use `*'` instead of `*`.
It will promote to Clojure's BigInt automatically.
Let's try that again:

~~~clojure
(reduce *' (range 1 101))
=>
93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000N
~~~

Yeah, that looks about right...

Now for the solution:

~~~clojure
(apply + (map int (problem-008/to-digits (factorial 100))))
~~~

Done!