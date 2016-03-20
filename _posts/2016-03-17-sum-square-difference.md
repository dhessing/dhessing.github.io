---
layout: post
title:  Sum square difference
subtitle: Problem 6
category: Project Euler
---

> The sum of the squares of the first ten natural numbers is,
>  
> $$ 1^2 + 2^2 + \dotso + 10^2 = 385 $$
>
> The square of the sum of the first ten natural numbers is,
> 
> $$ (1 + 2 + \dotso + 10)^2 = 55^2 = 3025 $$
>
> Hence the difference between the sum of the squares of the first ten natural numbers and the square of the sum is 3025 − 385 = 2640.
> 
> Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.

I wrote this code and it worked right away:

{% highlight clojure %}
(let [square #(* % %)
      numbers (range 1 101)]
  (- (square (apply + numbers))
     (apply + (map square numbers))))
{% endhighlight %}

### Bring out the math

It's fine I guess, but I'm sure there's a better answer.
We can use [Faulhaber's formula](https://en.wikipedia.org/wiki/Faulhaber%27s_formula) to simplify the summations.
Let's go:

$$
x = \left(\displaystyle\sum_{i=1}^{n} i\right)^2 - \displaystyle\sum_{i=1}^{n} i^2  =\\
  
\left(\frac{n(n + 1)}{2}\right)^2 - \frac{n(n + 1)(2n + 1)}{6} =\\

\left(\frac{n^2 + n}{2}\right)^2 - \frac{2n^3 + 3n^2 + n}{6} =\\ 
  
\frac{n^4 + 2n^3 + n^2}{4} - \frac{2n^3 + 3n^2 + n}{6} =\\
  
\frac{3n^4 + 6n^3 + 3n^2}{12} - \frac{4n^3 + 6n^2 + 2n}{12} =\\
  
\frac{3n^4 + 2n^3 - 3n^2 - 2n}{12} \\
$$

That looks workable. Even so, typing formulae into a Clojure REPL is torture, so I punched the above into a calculator.
It works!




 

