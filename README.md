Inspired by a forum post by Valentin Albillo and an additional idea by Albert Chan.

The original post, from 2020, on the HP Museum forum:
* [[VA] SRC#006- Pi Day 2020 Special: A New Fast Way to Compute Pi](https://www.hpmuseum.org/forum/thread-14636.html)

There's more discussion and exploration on Stardot:
* [Two more Pi programs from Valentin Albillo](https://stardot.org.uk/forums/viewtopic.php?t=18892)

We will need to compute sin(x) several times: each time we get 3x more correct digits of pi.

The simplest demonstration in Basic uses a SIN function
```text
   10 P=3
   20 FOR I=1 TO 2
   30 P=P+SIN(P)
   40 PRINT P
   50 NEXT I
```
Even in extended precision we only need three iterations to converge:
```
>RUN
3.1411200080598672222
3.1415926535721955587
3.1415926535897932385
```

Of course we need to implement our own SIN function, and we need eventually to move to multiprecision arithmetic.

But only the final round needs full precision: we only need to compute to the number of digits which will be correct, more or less.

So the total cost is no more than 1.5x times the cost of the final sin(x) calculation.

This should compare favourably with arctan methods: sin(x) as a power series converges much faster than arctan(x) because the denominators are factorials.

Even sin(3) converges rather nicely, but Albert Chan introduces an angle reduction formula, so we can instead compute sin(3/125) or sin(3/625) and we'll need fewer terms.   When computing at greater precision, there may be savings for even larger powers of 5, offsetting the cost of un-reducing the angle.  With the angle reduction, sin(x) might be even more efficient than arctan(x).

For multiprecision calculations in Basic, I've collected a couple of examples (by Katie Wasserman and by Gerson Barbosa) in another post on Stardot:
* [Pi Day - many digits of Pi (again, twice)](https://stardot.org.uk/forums/viewtopic.php?t=24451)

To help migrate to a multiprecision approach, we need to simplify the code progressively to individual additions, multiplications, divisions.  We need to see how many large numbers we'll need, which will limit the maximum number of digits we can produce.

Eventually, with an assembly language approach computing in binary, if we go there, we'll also need to do a final conversion to a decimal form.

Here's a somewhat simplified version of the code, with several parameters:
```text
  100 @%=&1414
  110 N=3 : REM number of iterations of X+SIN(X)
  120 P=3 : REM initial estimate for PI
  130 U=9 : REM odd, max term in SIN(X)
  140 PRINT ;P
  150 FOR I=1 TO N
  160   REM compute SIN of reduced angle with a series
  170   T=P/125:S=T:Q=T*T : REM S, T, Q, P all longs
  180   FOR K=3 TO U STEP 2
  190     D=K*(K-1) : REM can make this an addition
  200     T=-T*Q/D
  210     S=S+T
  220   NEXT K
  230   REM apply the SIN(5X) transformation
  240   FOR J=1 TO 3
  252     Q=S*S
  254     T=16*Q-20
  256     T=Q*T+5
  258     S=S*T
  260   NEXT J
  270   P=P+S
  280   PRINT ;P" "P-PI
  290 NEXT I
```
with result
```text
>RUN
3
3.1411200080598672224 -0.00047264552992601609888
3.1415926535721955587 -1.7597679823760925544E-11
3.1415926535897932385 0
```
