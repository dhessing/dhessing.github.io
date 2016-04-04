---
layout: post
title:  "Lattice Paths"
subtitle: "Problem 15"
category: Project Euler
---

> Starting in the top left corner of a 2×2 grid, and only being able to move to the right and down, there are exactly 6 routes to the bottom right corner.
>
> ![example image]({% asset_path "p015.gif" %})
>
> How many such routes are there through a 20×20 grid?

This is a very simple problem. The question boils down to finding all the different combinations of 20 steps
to the right and 20 steps down. So basically it's asking for the following combination:

$$ {40 \choose 20} $$

You can punch this into a calculator, or use some library. I used Parallel Colt:

~~~clojure
(import (cern.jet.math.tdouble DoubleArithmetic))

(long (DoubleArithmetic/binomial 40 20))
~~~

Done!