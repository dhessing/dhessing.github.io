---
layout: post
title:  Lexicographic permutations
subtitle: Problem 24
category: Project Euler
---
> A permutation is an ordered arrangement of objects. For example, 3124 is one possible permutation of the digits 1, 2, 3 and 4. If all of the permutations are listed numerically or alphabetically, we call it lexicographic order. The lexicographic permutations of 0, 1 and 2 are:
> 
> $$ 012 \quad 021 \quad 102 \quad 120 \quad 201 \quad 210 $$
>
> What is the millionth lexicographic permutation of the digits 0, 1, 2, 3, 4, 5, 6, 7, 8 and 9?

This challenge is easy because Clojure has the function we need in its library.

~~~clojure
(require '[clojure.math.combinatorics :as c])

(defn solve []
  (c/nth-permutation (range 10) (dec 1000000)))
~~~

And done.

## My permutations

So to add a bit of challenge, I tried writing my own permutation function from scratch.

~~~clojure
(defn permutations [items]
  (if (= 1 (count items))
    (list items)
    (for [item items
          p (permutations (filter #(not= % item) items))]
      (cons item p))))
~~~

The function defines the permutations recursively as all of the items 
plus the permutations of the items minus itself.

So the permutations of `[1 2 3]` are `[1] + permutations([2 3])` and `[2] + permutations([1 3])` etc...

It works, but in a benchmark it gets absolutely crushed compared with the library version.

~~~clojure
;;;; Library function
(dotimes [_ 5]
  (time
    (dotimes [_ 10]
      (nth (c/permutations (range 10)) (dec 1000000)))))
"Elapsed time: 3723.572324 msecs"
"Elapsed time: 3703.400947 msecs"
"Elapsed time: 3677.972303 msecs"
"Elapsed time: 3817.614154 msecs"
"Elapsed time: 3804.335535 msecs"
~~~

~~~clojure
;;;; My version
(dotimes [_ 5]
  (time
    (dotimes [_ 10]
      (c/nth-permutation (range 10) (dec 1000000)))))
"Elapsed time: 1.333919 msecs"
"Elapsed time: 0.333342 msecs"
"Elapsed time: 0.349889 msecs"
"Elapsed time: 0.319568 msecs"
"Elapsed time: 0.243754 msecs"
~~~

So yeah, my code appears to be 10.000 times slower.

## Why so slow

I started looking into the library code to find out why it was so fast. I expected to find a Java routine,
but it turns out it is all in Clojure. The critical routine in the code appears to be this monster:

~~~clojure
(defn- iter-perm [v]
  (let [len (count v),
        j (loop [i (- len 2)]
            (cond (= i -1) nil
                  (< (v i) (v (inc i))) i
                  :else (recur (dec i))))]
    (when j
      (let [vj (v j),
            l (loop [i (dec len)]
                (if (< vj (v i)) i (recur (dec i))))]
        (loop [v (assoc v j (v l) l vj), k (inc j), l (dec len)]
          (if (< k l)
            (recur (assoc v k (v l) l (v k)) (inc k) (dec l))
            v))))))
~~~

It was not immediately clear to me what was happening, so I tried calling it directly:

~~~clojure
(iter-perm [0 1 2])
=> [0 2 1]
(iter-perm [0 2 1])
=> [1 0 2]
(iter-perm [1 0 2])
=> [1 2 0]
(iter-perm [1 2 0])
=> [2 0 1]
(iter-perm [2 0 1])
=> [2 1 0]
(iter-perm [2 1 0])
=> nil
~~~

Aha, it gives us the next lexicographic permutation to iterate over, and returns `nil` on the last value
of the sequence. Now looking back at the code, it's much clearer.

What's cool about this is that compared to my function it has almost no internal looping state.
It can calculate the next iteration solely from its arguments.

My function on the other hand creates a Multi-Legged Beast of Terror™ of internal state because
it calls itself recursively within a for loop. It basically builds lists of permutations
to build a list of even more permutations. All those stack frames make my algorithm really slow. 

So what did I learn from all this? Stack frame allocation is slow.
Not only should you be using loop/recur (or any of the other methods) anyway to avoid blowing the stack,
it's also much faster.