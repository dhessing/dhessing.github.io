---
layout: post
title:  Multiples of 3 and 5
subtitle: Problem 1
category: Project Euler
---

> If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.
>
> Find the sum of all the multiples of 3 or 5 below 1000.

This is the first of the [Project Euler](https://projecteuler.net/) problems.
It is a variation of the classic FizzBuzz question.
 
This one can easiest be solved by first filtering and then summing the list.

{% highlight clojure %}
(->> (range 1000) 
     (filter #(or (zero? (mod % 5))
                  (zero? (mod % 3)))) 
     (apply +))
{% endhighlight %}

### Alternative solution

After solving it in the above way, I also tried calculating all the multiples directly.
This works but you will have to filter out the numbers that are both a multiple of 3 and 5 to prevent them from being summed twice.
To do this, I took the `set` of both ranges.

{% highlight clojure %}
(->> (concat (range 3 1000 3)
             (range 5 1000 5))
     (set)
     (apply +))
{% endhighlight %}