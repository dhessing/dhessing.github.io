---
layout: post
title:  Non-abundant sums
subtitle: Problem 23
category: Project Euler
---
> A perfect number is a number for which the sum of its proper divisors is exactly equal to the number. For example, the sum of the proper divisors of 28 would be 1 + 2 + 4 + 7 + 14 = 28, which means that 28 is a perfect number.
> 
> A number n is called deficient if the sum of its proper divisors is less than n and it is called abundant if this sum exceeds n.
> 
> As 12 is the smallest abundant number, 1 + 2 + 3 + 4 + 6 = 16, the smallest number that can be written as the sum of two abundant numbers is 24. By mathematical analysis, it can be shown that all integers greater than 28123 can be written as the sum of two abundant numbers. However, this upper limit cannot be reduced any further by analysis even though it is known that the greatest number that cannot be expressed as the sum of two abundant numbers is less than this limit.
> 
> Find the sum of all the positive integers which cannot be written as the sum of two abundant numbers.

This problem can be split up into 3 problems. First I need to find abundant numbers, 
then I can start finding numbers that are the sums of 2 abundant numbers. 
Finally I can use this list to find the sum all numbers that are *not* sums of 2 abundant numbers.

## Abundant numbers

Finding abundant numbers is easy because I can reuse the `proper-divisors` function from 
[problem 021]({% post_url 2016-04-13-amicable-numbers %}):

~~~clojure
(defn abundant-number? [n]
  (< n (reduce + (p21/proper-divisors n))))
~~~

## Find sums

Now I need to check if a number $$ n $$ is two sum of two abundant numbers. 
We know from the problem definition that we only need to check for numbers below 28124,
because it is mathematically proven that all other numbers can be written as the sum of two abundant numbers.

So to do this I take the number $$ n $$ that we'd like to check and see if for any 
$$ a $$ in the list of abundant numbers we can calculate $$ b = n - a $$
to that $$ b $$ is in the set of abundant numbers.

~~~clojure
(defn sum-of-two-abundant-numbers-checker []
  (let [abundant-numbers (into [] (filter abundant-number? (range 1 28124)))
        abundant-number-set (set abundant-numbers)]
    (fn [n]
      (let [half (/ n 2)]
        (some true?
              (for [a abundant-numbers
                    :while (<= a half)
                    :let [b (- n a)]
                    :when (contains? abundant-number-set b)]
                true))))))
~~~

What's interesting about the above function it that it returns another function. 
This is called a closure and I use it because if I want to call this function multiple times,
the returned function can re-use the variables `abundant-numbers` and `abundant-number-set` without
having to recalculate these.

Also note that it only checks half of all possible combinations because $$ a + b $$ is the same as
$$ b + a $$ and we only need to find one combination.

## Solve

To solve the problem I bind the above function and use it to remove all the numbers 
that it return true for, up to the number 28123.

~~~clojure
(let [sum-of-two-abundant-numbers? (sum-of-two-abundant-numbers-checker)]
    (reduce + (remove sum-of-two-abundant-numbers? (range 1 28124))))
~~~

Sum it, and done!