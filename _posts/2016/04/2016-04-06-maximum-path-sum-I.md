---
layout: post
title:  "Maximum path sum I"
subtitle: "Problem 18"
category: Project Euler
---

> By starting at the top of the triangle below and moving to adjacent numbers on the row below, the maximum total from top to bottom is 23.
> 
> <p style="text-align:center;font-family:'courier new';font-size:12pt;"><span style="color:#ff0000;"><b>3</b></span><br><span style="color:#ff0000;"><b>7</b></span> 4<br>
  2 <span style="color:#ff0000;"><b>4</b></span> 6<br>
  8 5 <span style="color:#ff0000;"><b>9</b></span> 3</p>
> 
> That is, 3 + 7 + 4 + 9 = 23.
>
> Find the maximum total from top to bottom of the triangle below:
> 
> <p style="text-align:center;font-family:'courier new';font-size:12pt;">75<br>
  95 64<br>
  17 47 82<br>
  18 35 87 10<br>
  20 04 82 47 65<br>
  19 01 23 75 03 34<br>
  88 02 77 73 07 63 67<br>
  99 65 04 28 06 16 70 92<br>
  41 41 26 56 83 40 80 70 33<br>
  41 48 72 33 47 32 37 16 94 29<br>
  53 71 44 65 25 43 91 52 97 51 14<br>
  70 11 33 28 77 73 17 78 39 68 17 57<br>
  91 71 52 38 17 14 91 43 58 50 27 29 48<br>
  63 66 04 68 89 53 67 30 73 16 69 87 40 31<br>
  04 62 98 27 23 09 70 98 73 93 38 53 60 04 23</p>
> 
> **NOTE**: As there are only 16384 routes, it is possible to solve this problem by trying every route. However, Problem 67, is the same challenge with a triangle containing one-hundred rows; it cannot be solved by brute force, and requires a clever method! ;o)

For this problem, I structured the above triangle as a vector of rows. So the example triangle would look like:
`[[3] [7 4] [2 4 6] [8 5 9 3]]`

Instead of trying every route, I tried to only keep the most promising routes.
The problem is, that when going from the top, you can't know which path is going to be optimal.
For example, when finding an unpromising path starting with `1 → 1 → 1`, 
the rest of the path could be `99 → 99 → 99`, making the sum the highest still.
Basically, we can never be sure that a path in not the highest until we reach the bottom of the triangle.

The solution is to start from the bottom instead.
Let's consider the example triangle:

<p style="text-align:center;font-family:'courier new';font-size:12pt;">
 3<br>
 7 4<br>
 2 4 6<br>
 8 5 9 3</p>

If we start from the bottom left, we find that both 8 and 5 have the same parent: 2.
Giving us two options, to recursively find the max sum of `8 + max-sum-path(2)` or `5 + max-sum-path(2)`.
Where `max-sum-path(p)` is the max sum of the rest of the path, starting from the parent.

Note that the value of `max-sum-path` will be the same for both paths, since we're asking the max sum upward from the same parent.

This gives us the option to discard paths right away, instead of waiting until we reach the bottom:
With `max-sum-path` the same for both options, we know for sure that the path `8 → 2` will have the highest sum.
So we only save this path and discard `5 → 2`. 

We can now do this for all the values of the bottom row, and then work our way upwards to find the max sum of the paths
of the parents. To put this into code:

~~~clojure
(defn add-max-child [[l r] parent]
  (max (+ l parent) (+ r parent)))

(defn add-max-children [child-row parent-row]
  (map add-max-child (partition 2 1 child-row) parent-row))
~~~

And to solve:

~~~clojure
(defn maximum-path-sum [triangle]
  (->> triangle
       rseq
       (reduce add-max-children)
       first))
~~~