---
layout: post
title:  Reciprocal cycles
subtitle: Problem 26
category: Project Euler
---
> A unit fraction contains 1 in the numerator. The decimal representation of the unit fractions with denominators 2 to 10 are given:
> 
> \$$
> \qquad ^1/_2	= 	0.5 \\
> \qquad ^1/_3	= 	0.(3) \\
> \qquad ^1/_4	= 	0.25 \\
> \qquad ^1/_5	= 	0.2 \\
> \qquad ^1/_6	= 	0.1(6) \\
> \qquad ^1/_7	= 	0.(142857) \\
> \qquad ^1/_8	= 	0.125 \\
> \qquad ^1/_9	= 	0.(1) \\
> \qquad ^1/_{10}	= 	0.1 \\
> $$
>
> Where 0.1(6) means 0.166666..., and has a 1-digit recurring cycle. It can be seen that 1/7 has a 6-digit recurring cycle.
> 
> Find the value of d < 1000 for which 1/d contains the longest recurring cycle in its decimal fraction part.

To find the number of recurring decimals I used the [Multiplicative order](https://en.wikipedia.org/wiki/Multiplicative_order).
We can use this to find the number of decimals in a cycle by taking $$ a = 10 $$.

From Wikipedia: 

> In number theory, given an integer a and a positive integer n with gcd(a,n) = 1, the multiplicative order of a modulo n is the smallest positive integer k with
>
> a^k ≡ 1 (mod n).

So to find the number of recurring decimals we search for the lowest $$ k $$ so that $$ 10^k \equiv 1 \pmod n $$.

~~~clojure
(defn multiplicative-order [a n]
  (letfn [(f [k] (= (mod (math/expt a k) n) 1))]
    (when (= 1 (math/gcd a n))
      (first (filter f (iterate inc 1))))))
~~~

So lets try this with the number 7 and calculate $$ O_7(10) $$:

~~~clojure
(multiplicative-order 10 7)
=> 6
~~~

Hooray, 1/7 indeed has 6 recurring digits.

## Relative prime

What's interesting about this function if that it's only defined for $$ n $$ that are relative prime to 10.

~~~clojure
(multiplicative-order 10 6)
=> nil
~~~

If we would ignore this we could get into an infinite loop, like when calculating $$ O_6(10) $$:

$$ 
10^1 \bmod 6 ≡ 4 \\
10^2 \bmod 6 ≡ 4 \\
10^3 \bmod 6 ≡ 4 \\
\text{etc...}
$$

Luckily we do not need to calculate these numbers.

These numbers are not relative prime to 10 and will therefore share a factor with another number.
It can be proven that if the denominators of two fractions are multiples of each other, they will have the same number
of repeating decimals (for more info see: <http://mathworld.wolfram.com/RepeatingDecimal.html>).
So 1/6 will have the same number of recurring digits as 1/3 because they differ by a factor of 2.

Since we are looking for the longest cycle, we are not interested in these multiples at all and just finding the first one will be fine.

## Answer

So now to find the answer we calculate the multiplicative order for numbers $$ < 1000 $$
and used zero as a filler value for `nil`.

~~~clojure
(apply max-key #(or (multiplicative-order 10 %) 0) (range 2 1000))
~~~

Done!
