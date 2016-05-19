---
layout: post
title:  Quadratic primes
subtitle: Problem 27
category: Project Euler
---
> Euler discovered the remarkable quadratic formula:
> 
> $$ n^2 + n + 41 $$
> 
> It turns out that the formula will produce 40 primes for the consecutive values n = 0 to 39. However, when $$ n = 40, 40^2 + 40 + 41 = 40(40 + 1) + 41 $$ is divisible by 41, and certainly when $$ n = 41, 41^2 + 41 + 41 $$ is clearly divisible by 41.
> 
> The incredible formula $$ n^2 − 79n + 1601 $$ was discovered, which produces 80 primes for the consecutive values n = 0 to 79. The product of the coefficients, −79 and 1601, is −126479.
> 
> Considering quadratics of the form:
> 
> $$ n^2 + an + b, \text{where } \lvert a \rvert < 1000 \text{ and } \lvert b\rvert < 1000 $$
> 
> where |n| is the modulus/absolute value of n
> e.g. |11| = 11 and |−4| = 4
> Find the product of the coefficients, a and b, for the quadratic expression that produces the maximum number of primes for consecutive values of n, starting with n = 0.

Looking at this problem, brute forcing looks the way to go as there are only 2000 * 2000 possible combinations of a and b.
To do this I can simply loop over all values of a and b,
generate a function for these values and count the number of consecutive primes this function gives.

Let's start by defining a function that returns a candidate function, given $$ a $$ and $$ b $$.

~~~clojure
(defn make-function [a b]
  (fn [n] (+ (* n n) (* a n) b)))
~~~

That is one great name for a function.
Note that it returns a _function_.

To find the number of consecutive primes, we use Java's BigInteger class for its primality check.

~~~clojure
(defn prime? [n]
  (.isProbablePrime (biginteger n) 10))

(defn count-consecutive-primes [coll]
  (count (take-while prime? coll)))
~~~

Let's test this with the example functions.

~~~clojure
(count-consecutive-primes (map (make-function 1 41) (range)))
=> 40
(count-consecutive-primes (map (make-function -79 1601) (range)))
=> 80
~~~

## Brute force

To brute force this I can simply loop over all values of a and b,
generate a function for these values and count the number of consecutive primes.

But on closer inspection I can cut down the problem space a bit.
Since the first value ($$ n = 0 $$) has to be a prime, we get:

$$
n^2 + an + b = \\
0^2 + a * 0 + b = \\
b
$$

So for the first number to be prime, $$ b $$ has to be a prime number.

Let's write the main loop:

~~~clojure
(defn consecutive-primes []
  (let [numbers (range -1000 1000)
        primes (filter prime? numbers)]
    (for [a numbers
          b primes
          :let [values (map (make-function a b) (range))]]
      [[a b] (count-consecutive-primes values)])))
~~~

This will generate all the combinations of $$ a $$ and $$ b $$ and their corresponding length.

## Answer

To solve this, we just need to find the product of the 2 variables with the longest length.

~~~clojure
(apply * (first (apply max-key second (consecutive-primes))))
~~~

Done!