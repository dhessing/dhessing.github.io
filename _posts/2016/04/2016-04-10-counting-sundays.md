---
layout: post
title:  Counting Sundays
subtitle: Problem 19
category: Project Euler
---
> You are given the following information, but you may prefer to do some research for yourself.
> 
> - 1 Jan 1900 was a Monday.
> - Thirty days has September,\\
>   April, June and November.\\
>   All the rest have thirty-one,\\
>   Saving February alone,\\
>   Which has twenty-eight, rain or shine.\\
>   And on leap years, twenty-nine.
> - A leap year occurs on any year evenly divisible by 4, but not on a century unless it is divisible by 400.
>
> How many Sundays fell on the first of the month during the twentieth century (1 Jan 1901 to 31 Dec 2000)?

We can probably just guess this answer by assuming a binomial distribution and calculating the mean. 
The number of trials will be  12 months times a a hundred years, giving us a mean $$ \mu = np = 1200 * \frac{1}{7} = 171 $$. 

But let's know for sure.
The plan is to generate a list of all sundays of the twentieth century, then filter only those that fall on the first,
and then to find the length of this list.

For the dates I will first try to find the first sunday of 1901.
I used the excellent `clj-time` library to put this into code:

~~~clojure
(defn first-sunday-of-year [year]
  (first (filter pr/sunday? (p/periodic-seq (t/date-midnight year) (t/days 1)))))
~~~

    (first-sunday-of-year 1901)
    => #<DateMidnight 1901-01-06T00:00:00.000Z>
    
Ok, so the first sunday is the 6th of January.
Now to solve I will keep adding whole weeks until I reach 2001, filter by first day and count:

~~~clojure
(->> (p/periodic-seq (first-sunday-of-year 1901) (t/weeks 1)) 
     (take-while #(< (t/year %) 2001)) 
     (filter pr/first-day-of-month?) 
     (count))
~~~~

The answer may surprise you!