---
layout: post
title:  "Longest Collatz sequence"
subtitle: "Problem 14"
category: Project Euler
---

>  The following iterative sequence is defined for the set of positive integers:
>  
>  $$ n → n / 2 \: ( n \text{ is even} ) $$
>
>  $$ n → 3n + 1 \: ( n \text{ is odd} ) $$
>  
>  Using the rule above and starting with 13, we generate the following sequence:
>  
>  $$ 13 → 40 → 20 → 10 → 5 → 16 → 8 → 4 → 2 → 1 $$
>
>  It can be seen that this sequence (starting at 13 and finishing at 1) contains 10 terms. Although it has not been proved yet (Collatz Problem), it is thought that all starting numbers finish at 1.
>  
>  Which starting number, under one million, produces the longest chain?
>  
>  **NOTE**: Once the chain starts the terms are allowed to go above one million.

This is easily my favorite problem so far.

The first thing to do is write a function to calculate the length of a single sequence, starting from $$ n $$.
To calculate this, we don't actually need all the individual terms. 
We can just recursively add one to the length of the Collatz sequence of the next number in the chain:

~~~ clojure
(defn collatz-step [n]
  (if (even? n) (/ n 2) (inc (* n 3))))

(defn collatz-chain-length [n]
  (if (= n 1)
    1
    (inc (collatz-chain-length (collatz-step n)))))
~~~

Let's check this with the example from above:

    (collatz-chain-length 13)
    => 10
    
This basically works, but if we try to solve the problem using:

~~~ clojure
(apply max-key collatz-chain-length (range 1 1e6))
~~~

It will take a long, *long* time.

## Memoization

The above solution is slow because we are generating some sequences over and over again.
Take for example the Collatz sequences of 5 and 32:

$$ 5 → 16 → 8 → 4 → 2 → 1 $$

$$ 32 → 16 → 8 → 4 → 2 → 1 $$

The sequence 16 → 8 → 4 → 2 → 1 is the same.
In fact, we will generate this sequence almost every time!

If we were to cache the intermediate results of `collatz-chain-length` 
we would not have to calculate the length of this sequence every time.
Let's add memoization:

~~~ clojure
(def collatz-chain-length
  (memoize
    (fn [n]
      (if (= n 1)
        1
        (inc (collatz-chain-length (collatz-step n)))))))
~~~ 

And now it will compute in a reasonable time!

## Bonus continuation passing version:

The above code could potentially stack overflow because it does not `recur`.

While not strictly needed for the above problem, 
we could rewrite it using continuation passing and a custom memoization strategy:

~~~ clojure
(let [cache (atom {1 1})]
  (defn collatz-chain-length-alt [n]
    (loop [n n, k identity]
      (letfn [(cache-add [length] 
                (swap! cache assoc n length) length)]
        (if-let [length (@cache n)]
          (k length)
          (recur (collatz-step n)
                 (comp k inc cache-add)))))))
~~~

The big trick is in the new argument $$ k $$, which is a function. 
We keep growing this function on the recur call using `comp`.
We add `inc` and `add-cache` functions until it finds a hit in the cache.

Then this function will unwind, returning the length and filling the cache in the process. Neat.

The optimisation is that we're building the calls in the heap instead of the stack, which is larger.

We can't use `memoize` like before because it would also memoize the $$ k $$ argument.
This would be pointless, because that value of $$ k $$, as a stack of functions, will be unique for every value of $$ n $$.
So it will never hit our cache.

But, other than only using $$ n $$ to cache, the caching solution here is basically a direct copy from the `memoization` source code.

While certainly not clearer, it's faster for larger problems.