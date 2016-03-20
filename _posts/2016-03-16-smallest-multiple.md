---
layout: post
title:  Smallest multiple
subtitle: Problem 5
category: Project Euler
---

> 2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder.
> 
> What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?

The first thing that comes to mind is a brute-force solution.
I will first write  a check if a number is divisible by all numbers below a certain upper bound.
Then I'll filter all numbers with this check.

{% highlight clojure %}
(defn divisable-by-all? [upper n]
  (->> (range upper)
       (map inc)
       (map #(mod n %))
       (every? zero?)))
{% endhighlight %}

Let's check that with the example:

{% highlight clojure %}
(divisable-by-all? 2520 10)
=> true
{% endhighlight %}

Good. Now to check the entire example:
{% highlight clojure %}
(->> (range)
     (map inc)
     (filter (partial divisable-by-all? 10))
     (first))
=> 2520
{% endhighlight %}

Even better. Now to get the answer:
{% highlight clojure %}
(->> (range)
     (map inc)
     (filter (partial divisable-by-all? 20))
     (first))
{% endhighlight %}

Oh-oh. That takes a long time. Maybe checking every number isn't the way.

### Finding a faster solution

I tried to get a better feel for the function by observing how the sequence progresses:

{% highlight clojure %}
(map #(->> (range)
           (map inc)
           (filter (partial divisable-by-all? %))
           (first)) (range 1 10))
=> (1 2 6 12 60 60 420 840 2520)
{% endhighlight %}

A-ha! Every number is a multiple of the previous number.
That's logical because the factors of the next multiple must include all the individual factors of the previous multiple,
plus a new factor.

Using the result of the previous iteration is a fold, so let's do that in code:

{% highlight clojure %}
(defn smallest-multiple []
  (letfn [(smallest-multiple' [previous upper]
            (->> (range)
                 (map inc)
                 (map (partial * previous))
                 (filter (partial divisable-by-all? upper))
                 (first)))]
    (reduce smallest-multiple' 1 (range 1 21))))
{% endhighlight %}

It's fast! And even better, it works!




