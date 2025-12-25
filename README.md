Inspired by a forum post by Valentin Albillo and an additional idea by Albert Chan.

[VA] SRC#006- Pi Day 2020 Special: A New Fast Way to Compute Pi
https://www.hpmuseum.org/forum/thread-14636.html

We will need to compute sin(x) several times: each time we get 3x more correct digits of pi.

But only the final round needs full precision: we only need to compute to the number of digits which will be correct, more or less.

So the total cost is no more than 1.5x times the cost of the final sin(x) calculation.

This should compare favourably with arctan methods, because sin(x) as a power series converges much faster than arctan(x) because the denominators are factorials.

