---
layout: post
title:  Digit fifth powers
subtitle: Problem 30
category: Project Euler
---

> <p>Surprisingly there are only three numbers that can be written as the sum of fourth powers of their digits:</p>
> <blockquote>1634 = 1<sup>4</sup> + 6<sup>4</sup> + 3<sup>4</sup> + 4<sup>4</sup><br>
> 8208 = 8<sup>4</sup> + 2<sup>4</sup> + 0<sup>4</sup> + 8<sup>4</sup><br>
> 9474 = 9<sup>4</sup> + 4<sup>4</sup> + 7<sup>4</sup> + 4<sup>4</sup></blockquote>
> <p class="info">As 1 = 1<sup>4</sup> is not a sum it is not included.</p>
> <p>The sum of these numbers is 1634 + 8208 + 9474 = 19316.</p>
> <p>Find the sum of all the numbers that can be written as the sum of fifth powers of their digits.</p>

This one can easily be brute forced.

I start by generating all the possible sums.
For the 4-digits in the example this would be all numbers between 10 and $$ 4 * 9^4 $$.

Then I break them down into their digits, calculate the sum of the powers, and compare them to our original number. 


~~~clojure
(defn n-power-digits [n]
  (let [numbers (range 10 (inc (* n (math/expt 9 n))))]
    (for [number numbers
          :let [digits (map #(Integer. (str %)) (str number))
                sum (reduce + (map #(math/expt % n) digits))]
          :when (= sum number)]
      number)))
~~~

Now to test this with the example:

~~~clojure
(n-power-digits 4)
=> (1634 8208 9474)
~~~

Great, it works. Now to solve the question:

~~~clojure
(defn solve []
  (reduce + (n-power-digits 5)))
~~~

Done!