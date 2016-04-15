---
layout: post
title:  Names scores
subtitle: Problem 22
category: Project Euler
---
> Using [names.txt](https://projecteuler.net/project/resources/p022_names.txt) (right click and 'Save Link/Target As...'), a 46K text file containing over five-thousand first names, begin by sorting it into alphabetical order. Then working out the alphabetical value for each name, multiply this value by its alphabetical position in the list to obtain a name score.
>
> For example, when the list is sorted into alphabetical order, COLIN, which is worth 3 + 15 + 12 + 9 + 14 = 53, is the 938th name in the list. So, COLIN would obtain a score of 938 × 53 = 49714.
>
> What is the total of all the name scores in the file?

Another easy one.
To solve this problem I will simply follow the problem definition.
I will read the file, sort it, multiply the alphabetical value with the line number, and find the sum.

To load the file I used `slurp` and split the file into a vector of names:

~~~clojure
(defn load-names []
  (->
    (slurp "resources/p022_names.txt")
    (s/replace #"\"" "")
    (s/split #",")))
~~~

To get the alphabetical value of each word I summed the character codes, minus the offset of 64 for capital letters:

~~~clojure
(defn alphabetical-value [word]
  (reduce + (map #(- (int %) 64) word)))
~~~

Now to solve I took the line-number and multiplied it with the word value of the name.

~~~clojure
(defn name-score [index name]
  (* index (alphabetical-value name)))

(->> (load-names)
     (sort)
     (map name-score (iterate inc 1))
     (reduce +))
~~~

Done!

