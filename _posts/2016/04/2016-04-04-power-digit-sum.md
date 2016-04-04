---
layout: post
title:  "Power digit sum"
subtitle: "Problem 16"
category: Project Euler
---

> $$ 2^{15} = 32768 $$ and the sum of its digits is $$ 3 + 2 + 7 + 6 + 8 = 26 $$.
>  
> What is the sum of the digits of the number $$ 2^{1000} $$?

Another easy one. We can directly calculate the power, and use the `to-digits` function from 
[problem 7]({% post_url 2016-03-19-largest-product-in-a-series %}).

~~~clojure
(apply + (problem-008/to-digits (math/expt 2 1000)))
~~~

That's it!