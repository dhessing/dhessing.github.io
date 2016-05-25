---
layout: post
title:  Number spiral diagonals
subtitle: Problem 28
category: Project Euler
---
> Starting with the number 1 and moving to the right in a clockwise direction a 5 by 5 spiral is formed as follows:
>  
> <p style="text-align:center;font-family:'courier new';"><span style="color:#ff0000;font-family:'courier new';"><b>21</b></span> 22 23 24 <span style="color:#ff0000;font-family:'courier new';"><b>25</b></span><br>
  20 &nbsp;<span style="color:#ff0000;font-family:'courier new';"><b>7</b></span> &nbsp;8 &nbsp;<span style="color:#ff0000;font-family:'courier new';"><b>9</b></span> 10<br>
  19 &nbsp;6 &nbsp;<span style="color:#ff0000;font-family:'courier new';"><b>1</b></span> &nbsp;2 11<br>
  18 &nbsp;<span style="color:#ff0000;font-family:'courier new';"><b>5</b></span> &nbsp;4 &nbsp;<span style="color:#ff0000;font-family:'courier new';"><b>3</b></span> 12<br><span style="color:#ff0000;font-family:'courier new';"><b>17</b></span> 16 15 14 <span style="color:#ff0000;font-family:'courier new';"><b>13</b></span></p>
> 
> It can be verified that the sum of the numbers on the diagonals is 101.
> 
> What is the sum of the numbers on the diagonals in a 1001 by 1001 spiral formed in the same way?

The red numbers on the diagonals follow a specific pattern.

- We start with 1.
- On the first ring, we add the number two a total of four times to get 3, 5, 7, 9.
- For the second ring the number four is added four times to give 13, 17, 21, 25.
- If we would expand the ring even further, we would see that we would add six four times.
- And so on, each ring will add a number that's two larger than the previous ring, four times.

This is logical. 
As you can see in the above spiral each side of the ring is 2 numbers larger than the previous one, and each ring has 4 corners to add.

To make a list of these spiral numbers we can do the following:

~~~clojure
(defn spiral-numbers []
  (reductions + 1 (mapcat #(repeat 4 %) (iterate #(+ 2 %) 2))))
~~~

Let's test this.

~~~clojure
(take 9 (spiral-numbers))
=> (1 3 5 7 9 13 17 21 25)
~~~

Nice, those are exactly the red numbers from above.

## Summing

Now we only need generate the right amount of the above spiral numbers, and sum them.

To find the amount of red numbers realize that a $$ n \times n $$ spiral will have the number 1, plus $$ n/2 $$ rings, of 4 numbers each.
This gives us:

$$
\frac{n - 1}{2} * 4 + 1
$$

We can simplify this to:

$$
2(n - 1 ) + 1 = \\
2n - 2 - 1 = \\
2n - 1
$$

Putting this into code we get the following:

~~~clojure
(defn spiral-sum [size]
  (let [n (dec (* 2 size))]
    (reduce + (take n (spiral-numbers)))))
~~~

Let's check this with the above $$ 5 \times 5 $$ spiral.

~~~clojure
(spiral-sum 5)
=> 101
~~~

Cool. Now to find the answer.

~~~clojure
(spiral-sum 1001)
~~~

Done.