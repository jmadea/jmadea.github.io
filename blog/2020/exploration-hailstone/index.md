![Gephi screenshot](graphdetail.png)
# Exploration: The Hailstone Numbers

_"Mathematics may not be ready for such problems." &mdash; Paul Erdős_


The Collatz Conjecture is one of mathematics most famous unsolved problems. 
Its fame is undoubtedly due to its seeming simplicity. Any third grader 
can understand the problem. And it's fun to explore!

Start with any natural number. If it is even, divide it by two. If it is odd
first multiply it by three then add one. If your result is greater than one,
repeat the process again with your result. 

For example, start with the number 1. Since it is odd, we multiply it by 3 
and add 1, getting 4. As 4 is greater than 1, we continue. Since 4 is even,
we divide it by 2 to get 2, which of course is greater than 1 and even, so 
we divide it by 2 again and we get . . . 1. And we are done. 

Let's try again, this time with 3. (We've already seen that 2 eventually results
in a 1.) Okay, 3 is odd so 3 times 3 plus 1 equals 10. And 10 is even so 10
divided by 2 equals 5. And 5 is odd, so 3 times 5 plus 1 equals 16. Since 16 is 
a power of 2, we can see it trivially reduces to 1 after we repeatedly divide by 2. 

The Collatz Conjecture, suggested by German Mathematician Lothar Collatz, is that
every natural number subjected to this iterative process will eventually yield 1.

As you might imagine, this problem has been poked and prodded by the best. So far, 
the conjecture has been confirmed for every natural number up to 2<sup>58</sup>.  
