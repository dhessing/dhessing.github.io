---
layout: post
title:  Amicable numbers
subtitle: Problem 21
category: Project Euler
---

> Let d(n) be defined as the sum of proper divisors of n (numbers less than n which divide evenly into n).
> If d(a) = b and d(b) = a, where a ≠ b, then a and b are an amicable pair and each of a and b are called amicable numbers.
> 
> For example, the proper divisors of 220 are 1, 2, 4, 5, 10, 11, 20, 22, 44, 55 and 110; therefore d(220) = 284. The proper divisors of 284 are 1, 2, 4, 71 and 142; so d(284) = 220.
> 
> Evaluate the sum of all the amicable numbers under 10000.

For this problem I am going to generate a list of all amicable numbers and sum them.
To generate these, we need to find the proper divisors of a number.

## Proper divisors

Proper divisors are defined as the positive factors of a number, excluding the number itself.
So I'm first going to write a function to generate the factors of $$ n $$:

~~~clojure
(defn factors [n]
  (->> (for [x (range 1 (inc (Math/sqrt n)))
             :when (zero? (rem n x))]
         [x (/ n x)])
       (reduce concat)
       (sorted-set)))
~~~

As an optimisation, the `for` loop will generate a tuple of the found factor and its co-factor.
After that, `reduce concat` will make it a single list again. 

To get the proper divisors I'm just going to remove $$ n $$ from the resulting list: 

~~~ clojure
(defn proper-divisors [n]
  (disj (factors n) n))
~~~

Let's test this:

~~~ clojure
(sort (proper-divisors 220))
=> (1 2 4 5 10 11 20 22 44 55 110)
~~~

That's the same list as in the instructions, so that's promising!

## Amicable numbers

To find the amicable numbers I used the definition from the instructions:

> Let d(n) be defined as the sum of proper divisors of n (numbers less than n which divide evenly into n). If d(a) = b and d(b) = a, where a ≠ b, then a and b are an amicable pair and each of a and b are called amicable numbers.

In code I define the function $$ d(n) $$ and perform the required checks:

~~~clojure
(defn amicable-number? [a]
  (let [d (fn [n] (apply + (proper-divisors n)))
        b (d a)]
    (and (= a (d b)) (not= a b))))
~~~

Let's see if the code makes sense:

~~~clojure
(amicable-number? 220)
=> true
~~~

Cool. Now to find the answer:

~~~clojure
(apply + (filter amicable-number? (range 1 10001)))
~~~