---
layout: post
title:  Largest palindrome product
subtitle: Problem 4
category: Project Euler
---

> A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99.
>
> Find the largest palindrome made from the product of two 3-digit numbers.

To solve this I needed to generate a list of all the products of two 3-digit numbers,
and a way to check for palindromes.

First the products:

{% highlight clojure %}
(defn products []
  (for [a (range 100 1000)
        b (range 100 1000)
        :while (>= a b)]
    (* a b)))
{% endhighlight %}

The `:while` breaks the inner loop on the given condition.
It is a small optimization so whe don't check the same products twice. 

For the palindromes we check if the reverse string is the same as the original string:

{% highlight clojure %}
(defn palindrome? [n]
  (let [s (str n)]
    (->> s
         (reverse)
         (apply str)
         (= s))))
{% endhighlight %}

And the answer can be gotten the following way:

{% highlight clojure %}
(->> (products)
     (filter palindrome?)
     (apply max))
{% endhighlight %}

But for a more compact solution, the filter can also be included in the for loop itself:

{% highlight clojure %}
(->> (for [a (range 100 1000)
           b (range 100 1000)
           :while (>= a b)
           :let [product (* a b)]
           :when (palindrome? product)]
       product)
     (apply max))
{% endhighlight %}

I think that's beautiful.