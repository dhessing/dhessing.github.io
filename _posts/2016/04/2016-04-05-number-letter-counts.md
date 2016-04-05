---
layout: post
title:  "Number letter counts"
subtitle: "Problem 17"
category: Project Euler
---
> If the numbers 1 to 5 are written out in words: one, two, three, four, five, then there are 3 + 3 + 5 + 4 + 4 = 19 letters used in total.
>  
> If all the numbers from 1 to 1000 (one thousand) inclusive were written out in words, how many letters would be used?
>  
> 
> **NOTE:** Do not count spaces or hyphens. For example, 342 (three hundred and forty-two) contains 23 letters and 115 (one hundred and fifteen) contains 20 letters. The use of "and" when writing out numbers is in compliance with British usage.

To solve this, the plan is to generate the english representations of the words, count and sum.

To get the english representation I used the ~R directive of `clojure.pprint/cl-format`,
see: [Common Lisp HyperSpec](http://www.lispworks.com/documentation/HyperSpec/Body/22_cba.htm).

The problem is that it does not use the British "and" that we need here.
So I will add it myself by adding it between the second and third words:

~~~clojure
(defn number->english [n]
  (let [english (clojure.pprint/cl-format nil "~R" n)
        words (clojure.string/split english #"\s")]
    (if (< 2 (count words))
      (let [[w1 w2 w3] words]
        (clojure.string/join " " [w1 w2 "and" w3]))
      english)))
~~~

Let's try this:

    (number->english 342)
    => "three hundred and forty-two"
    
Cool!

Now we count, using a regex to only find the letters:

~~~clojure
(defn number-letter-counts [n]
  (count (re-seq #"\w" (number->english n))))
~~~

And to find the answer:

~~~clojure
(apply + (map number-letter-counts (range 1 1001)))
~~~