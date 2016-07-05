---
layout: post
title:  Digit cancelling fractions
subtitle: Problem 33
category: Project Euler
---
> <p>The fraction <sup>49</sup>/<sub>98</sub> is a curious fraction, as an inexperienced mathematician in attempting to simplify it may incorrectly believe that <sup>49</sup>/<sub>98</sub> = <sup>4</sup>/<sub>8</sub>, which is correct, is obtained by cancelling the 9s.</p>
> <p>We shall consider fractions like, <sup>30</sup>/<sub>50</sub> = <sup>3</sup>/<sub>5</sub>, to be trivial examples.</p>
> <p>There are exactly four non-trivial examples of this type of fraction, less than one in value, and containing two digits in the numerator and denominator.</p>
> <p>If the product of these four fractions is given in its lowest common terms, find the value of the denominator.</p>

This problem is basically a search with a set of constraints and can be solved using a big for-comprehension. 

First we'll use the `int-to-digits` and `digits-to-int` from previous problems.

~~~clojure
(defn int-to-digits [n]
  (map #(Integer/parseInt (str %)) (str n)))

(defn digits-to-int [coll]
  (Integer/parseInt (clojure.string/join (map str coll))))
~~~

### Building the comprehension

We want to find all fractions of two digits:

~~~clojure
(for [[a b] (combo/combinations (range 10 100) 2)]
  [a b])
~~~

We get the digits of these numbers:

~~~clojure
:let [digits-a (set (int-to-digits a))
      digits-b (set (int-to-digits b))]
~~~

Find the cancelled digits by using set difference:

~~~clojure
:let [digits-a-cancelled (clojure.set/difference digits-a digits-b)
      digits-b-cancelled (clojure.set/difference digits-b digits-a)]
~~~

filter out numbers with double digits that leave us with an empty set:

~~~clojure
:when (not-any? empty? [digits-a-cancelled digits-b-cancelled])
~~~

Make an integer from the cancelled numbers:

~~~clojure
:let [c (digits-to-int digits-a-cancelled)
      d (digits-to-int digits-b-cancelled)]
~~~

And we filter out the trivial values, the values where nothing cancelled, and finally find the ones that have
 the same value if we divide the original and the cancelled numbers. (We also catch for a divide by zero).
              

~~~clojure    
:when (try (and (not= (rem a 10) 0)
                        (not= digits-a digits-a-cancelled)
                        (= (/ a b) (/ c d)))
                   (catch ArithmeticException _ false))
~~~

Putting it all together we get this final form:

~~~clojure
(defn digit-cancelling-fractions []
  (for [[a b] (combo/combinations (range 10 100) 2)
        :let [digits-a (set (int-to-digits a))
              digits-b (set (int-to-digits b))
              digits-a-cancelled (clojure.set/difference digits-a digits-b)
              digits-b-cancelled (clojure.set/difference digits-b digits-a)]
        :when (not-any? empty? [digits-a-cancelled digits-b-cancelled])
        :let [c (digits-to-int digits-a-cancelled)
              d (digits-to-int digits-b-cancelled)]
        :when (try (and (not= (rem a 10) 0)
                        (not= digits-a digits-a-cancelled)
                        (= (/ a b) (/ c d)))
                   (catch ArithmeticException _ false))]
    [a b]))
~~~

Let's see if it works:

~~~clojure
(digit-cancelling-fractions)
=> ([16 64] [19 95] [26 65] [49 98])
~~~

We found four numbers, just like the problem said! That looks good.

### Solving the problem

Assuming the above numbers are correct solving the problem is easy now.
We need to find the denominator of the product of the above fractions in its lowest common terms.

~~~clojure
(denominator (reduce * (digit-cancelling-fractions))))
~~~

Done!