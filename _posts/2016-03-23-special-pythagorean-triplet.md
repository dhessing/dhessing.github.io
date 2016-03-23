---
layout: post
title:  "Special Pythagorean triplet"
subtitle: "Problem 9"
category: Project Euler
---

> A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,
>  
> $$ a^2 + b^2 = c^2 $$
>
> For example, $$ 3^2 + 4^2 = 9 + 16 = 25 = 5^2 $$. 
>  
> There exists exactly one Pythagorean triplet for which $$ a + b + c = 1000 $$.
> Find the product $$ abc $$.

For the solution I first generate an endless list of triplets,
and then to find the one we need and multiply it.

Let's start with the triplets.
Euclid's formula tells that given an arbitrary pair of positive integers m and n with m > n, the integers

$$ a = m^2 - n^2 ,\ \, b = 2mn ,\ \, c = m^2 + n^2 $$

form a Pythagorean triple. To put that into code:

{% highlight clojure %}
(defn pythagorian-triplets []
  (for [m (iterate inc 1)
        n (iterate inc 1)
        :while (> m n)
        :when (odd? (- m n))
        :let [a (- (* m m) (* n n))
              b (* 2 m n)
              c (+ (* m m) (* n n))]]
    [a b c]))
{% endhighlight %}

But that will only generate us the primitive triplets.
Presumably, we need them all.
To get these we can multiply with $$ k $$:

$$ a = k\cdot(m^2 - n^2)   ,\ \, b = k\cdot(2mn) ,\ \, c = k\cdot(m^2 + n^2) $$

Now in the code I could have just added a loop over $$ k $$, but I can also directly calculate it's value.
For $$ s $$ the sum of the triplets, we get:

$$
\begin{align*}
s &= a + b + c \\
&= k\cdot(m^2 - n^2) + k\cdot(2mn) + k\cdot(m^2 + n^2) \\
&= km^2 - kn^2 + 2kmn + km^2 + kn^2 \\
&= 2km^2 + 2kmn\\
&= 2km(m + n) \\
k &= \frac{s}{2m(m+n)}
\end{align*}
$$

Using that, we get the following updated code:

{% highlight clojure %}
(defn pythagorian-triplets [s]
  (for [m (drop 1 (range))
        n (drop 1 (range))
        :while (> m n)
        :when (odd? (- m n))
        :when (= 1 (math/gcd m n))
        :let [k (/ s (* 2 m (+ m n)))                       ;new
              a (* k (- (* m m) (* n n)))                   ;added k
              b (* k (* 2 n m))                             ;added k
              c (* k (+ (* m m) (* n n)))]                  ;added k
        :when (integer? k)]                                 ;new
    [a b c]))
{% endhighlight %}

And to answer it:

{% highlight clojure %}
(apply * (first (pythagorian-triplets 1000)))
{% endhighlight %}