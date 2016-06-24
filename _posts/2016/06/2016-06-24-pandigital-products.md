---
layout: post
title:  Pandigital Products
subtitle: Problem 32
category: Project Euler
---
> <p>We shall say that an <var>n</var>-digit number is pandigital if it makes use of all the digits 1 to <var>n</var> exactly once; for example, the 5-digit number, 15234, is 1 through 5 pandigital.</p>
> <p>The product 7254 is unusual, as the identity, 39 × 186 = 7254, containing multiplicand, multiplier, and product is 1 through 9 pandigital.</p>
> <p>Find the sum of all products whose multiplicand/multiplier/product identity can be written as a 1 through 9 pandigital.</p>
> <div class="note">HINT: Some products can be obtained in more than one way so be sure to only include it once in your sum.</div>

To solve this problem I will generate the multiplicand $$ a $$ and the multiplier $$ b $$ and I will check if the product
 $$ c $$ will make the total 1 trough 9 pandigital.

### Generating multipliers
Theoretically $$ a $$ $$ b $$ and $$ c $$ can only be of a certain amount of digits.
If the total needs to be 10 digits we can have:

- 1-digit times 4-digits = 4-5 digits (1 * 1000 = 1000,  9 * 9999 = 89991)
- 2-digits times 3-digits = 4-5 digits (10 * 100 = 1000,  99 * 999 = 98901)

So what we need to do is generate pairs of 1 and 4 digit, and 2 and 3 digit numbers, with each number having unique digits. 

So let's start with writing a function to generate all permutations of a certain length.
`clojure.math/combinatorics` doesn't have a function for this, but it is easily written.

~~~clojure
(require '[clojure.math.combinatorics :as combo])

(defn permutations [items length]
  (mapcat combo/permutations (combo/combinations items length)))
~~~

Generating the multipliers can now be done using the above function.
We need the digits to be unique so we will remove the digits of $$ a $$ from the list of digits for $$ b $$.

~~~clojure
(defn gen-multipliers []
  (let [digits (range 1 10)]
    (for [[length-a length-b] [[1 4] [2 3]]
          digits-a (permutations digits length-a)
          digits-b (permutations (remove (set digits-a) digits) length-b)]
      [digits-a digits-b])))
~~~

### Pandigital products

To find the pandidial products we need to calculate the product of $$ a $$ and $$ b $$ and check if the total is indeed pandigital.
To calculate the product we need a way to switch between digit and integer representation.

~~~clojure
(defn int-to-digits [n]
  (map #(Integer/parseInt (str %)) (str n)))

(defn digits-to-int [coll]
  (Integer/parseInt (clojure.string/join (map str coll))))
~~~

Now we can search for our pandigital products:

~~~clojure
(defn pandigital-products []
  (let [digits (set (range 1 10))]
    (for [[digits-a digits-b] (gen-multipliers)
          :let [a (digits-to-int digits-a)
                b (digits-to-int digits-b)
                c (* a b)
                digits-total (concat digits-a digits-b (int-to-digits c))]
          :when (and
                  (= (set digits-total) digits)
                  (= (count digits-total) 9))]
      c)))
~~~

To solve you can:

~~~clojure
(reduce + (set (pandigital-products)))
~~~