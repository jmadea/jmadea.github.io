![Gephi screenshot](graphdetail.png)
## Exploration: The Hailstone Numbers

An introductory probe to the most alluring problem in mathematics for people
who are just interested in this kinda stuff.[^altdesc]

(In other words, if you found this page, you'll probably be able to understand it.) 

_"Mathematics may not be ready for such problems." &mdash; Paul Erdős_


The Collatz Conjecture is one of mathematics most famous unsolved problems.  Its
fame is undoubtedly due to its seeming simplicity. Any third grader can
understand the problem. Lots of second graders too. And it's fun to explore!

Start with any natural number. If it is even, divide it by two. If it is odd
first multiply it by three then add one. If your result is greater than one,
repeat the process again with your result.

For example, start with the number 1. Since it is odd, we multiply it by 3 and
add 1, getting 4. As 4 is greater than 1, we continue. Since 4 is even, we
divide it by 2 to get 2, which of course is greater than 1 and even, so we
divide it by 2 again and we get . . . 1. And we are done.

The Collatz Conjecture, suggested by German Mathematician Lothar Collatz, is
that every natural number subjected to this iterative process will eventually
yield 1.

Let's try 3. We get 10, then 5, then 16, then 8, then 4, then 2, then 1.

Here are the sequences for the numbers 1-9:

  * 1: [4, 2, 1]
  * 2: [1]
  * 3: [10, 5, 16, 8, 4, 2, 1]
  * 4: [2, 1]
  * 5: [16, 8, 4, 2, 1]
  * 6: [3, 10, 5, 16, 8, 4, 2, 1]
  * 7: [22, 11, 34, 17, 52, 26, 13, 40, 20, 10, 5, 16, 8, 4, 2, 1]
  * 8: [4, 2, 1]
  * 9: [28, 14, 7, 22, 11, 34, 17, 52, 26, 13, 40, 20, 10, 5, 16, 8, 4, 2, 1]

These sequences of _iterates_ are sometimes called "hailstone sequences" because
of the way they repeatedly rise and then fall before eventually falling to the
ground (i.e. down to 1.)

As you might imagine, this problem has been deeply investigated by the best
meathematicians in the world. So far, the conjecture has been confirmed for
every natural number up to 2<sup>58</sup>.  But there's still a lot of numbers
between that and infinity. And so far, we have no way of knowing, without
checking, whether all of those numbers' sequences eventually reach one or not.

In fact, we may never have an answer at all. It may be that the Collatz
Conjecture is _formally undecidable_, meaning that a mathematical proof for it
may not even exist!

So, no matter how much time we spend on it, it's pretty unlikely that we'll make
any signficant progress on the problem. And that means the pressure is off! We
can feel free to explore and play around with it all we want; our only goal is
to satify our curiosity and maybe discover what makes such a simple problem to
explain so devilishly difficult to solve.

So let's play.

Here's a python function that returns a given number's hailstone sequence. We
assume the sequence should not include the original number, so the sequence
returned for 1 is `[4, 2, 1]` and not `[1, 4, 2, 1]`.

``` python 
def hailstone(n):
    sequence = []
    while True:
        n = 3*n + 1 if n & 1 else n // 2
        sequence.append(n)
        if n == 1: break
    return sequence
```

Easy peasy. Great, so now let's reproduce our results above for the numbers 1 to 9: 

``` python 
for n in range(1,10):
    print(f"{n}: {hailstone(n)}")
```

No problem. But what now? Well, after looking at the hailstone sequences for
various numbers we may start to notice some things. First, our sequences always
seem to reach a power of 2, after which they trivially decrease to 1. That makes
sense when we think about it.  The only way for our sequences to decrease is
when we hit an even number and divide by two. That means we must always get to 1
by dividing 2. Similarly, the only way to get to 2 is through 4. So all of our
sequences for n > 4 must end in `[4, 2, 1]`. The only way to get to 4 is either
by multiplying 1 by 3 and adding 1 or by dividing 8 by 2. Since we stop our
sequences when we get to 1, we only get to 4 through dividing 8 by 2 (except in
the special case when we start with 1.) Furthermore, as 8 cannot be expressed as
3k+1, we must get 8 by dividing 16 by 2. So all of our sequences other than
those starting with 8, 4, 2, 1 must end in `[16, 8, 4, 2, 1]`.

Well, that's interesting. It highlights the fact that only some powers of 2 can
be expressed as 3k+1. If we look closer, it appears that it is powers of 4 that
can be expressed as 3k+1. Is that always the case? Spoiler: yes it is. It's a
straight forward proof by induction.[^proof] We can also prove that the powers
of 2 that are not powers of 4 cannot be expressed as 3k+1. 

So, not all of the powers of 2 are interesting, just the powers of 4. They are
interesting because they can be immediately preceded in a sequence by either a
smaller odd number or by a larger power of 2. Or maybe we should consider the
odd numbers that immediately precede powers of 4 the interesting numbers because
hailstone(_n_) (so long as _n_ is not a power of 2 itself) will always end up at
one of those numbers immediately before hitting a power of 2 and dropping
directly to 1.

Let's take a step back and look at the big picture for a second, to the extent
that we are able. Some structure has started to emerge from the problem. There
may be a sort of inexplicable random feel to the different hailstone sequences
we've seen, but we know they all sorta flow to 1 through a major vein made of
powers of 2, and that the sequences are all connected to this vein through pairs
of numbers (_i_, _j_) where 3\*_i_  + 1 = _j_ and  _j_ is a power of 4.

Okay. So what next? Well, let's think about it from the bottom up now. So far,
we've been starting with some number like it is a leaf on a tree, and following
its sequence from twig to branch, back to the power-of-2 trunk, and then down to
1 at the root. Instead, let's start at a number and look the other direction to
see what other numbers' sequences come through _it_. We know that, in this
direction, our given number can be connected to at most two other numbers, an
even one and possibly an odd one as well. So we do this in two steps. First, we
multiply the number by two. That'll give us the even branch, which is always
present. Then, if our number is even, we subtract one from it and check if it is 
divisible by 3.  If it is, then dividing it by 3 gives us our odd branch. We
skip this check if our number is odd because we only apply the 3n+1 rule to
odd numbers which always yields an even number.

We can expand on the idea a bit by collecting the numbers we generate as we go
and retrieving their branches as well. We can supply a distance (or depth) for
our search so it doesn't keep going straight out to infinity. Here's the code.


```python

def liah(n, depth=1):
    nodes = [n]
    ret = []
    while depth > 0:
        depth -= 1
        nextnodes = []
        for node in nodes:
            ret.append(node*2)
            nextnodes.append(node*2)
            if not node & 1:
                k = node - 1
                if k % 3 == 0:
                    ret.append(k//3)
                    nextnodes.append(k//3)
        nodes = nextnodes
    return ret
```

Let's play with our new tool. 

liah(1, 5) returns [2, 4, 8, 1, 16, 2, 32, 5, 4]
liah(2, 5) returns [4, 8, 1, 16, 2, 32, 5, 4, 64, 10, 8, 1]

We have some duplicate numbers here. Let's think about that. We might
decide we want to skip numbers we already have, either as a matter of
convenience or efficiency. But then again, maybe we want to capture the
number of times numbers repeat. The thing is, once a number repeats, its
iterates repeat as well. Hrm. Well, we'll make a note of it and poke 
around some more. 

liah(3, 5) returns [6, 12, 24, 48, 96]

Oh hey, that's interesting! No odd numbers. It looks like numbers of the form
3(2<sup>_n_</sup>) descend directly to 3 much like powers of 2 descend to 1. But
that just makes sense, right? In fact, it's easy to see that, given any _m_,  
hailstone(2<sup>_n_</sup>_m_) will descend directly to _m_. (Only odd _m_'s are 
interesting, of course.) Powers of two always just divide away step by step. 

So maybe this isn't that special after all? 

Well, the interesting bit is that this sequence returned by liah(5,3) isn't 
returning any odd numbers, just continuously incressing even numbers. In other
words, there are no odd _n_s such that 3*n*+1 equals one of these even numbers.
At least that's how it appears. Can we show it must be true?

Yes. We can. Easily.

As we pointed out, these are all of the form 3(2<sup>_n_</sup>). In other words,
they are all divisible by three. So let's assume one of them -- call 
it 3*m* (m &isin; &integers;) can also be expressed as 3*k*+1 (k &isin; &integers). 
I.e.  3*m* = 3*k* + 1. Dividing both sides by 3 gives *m* = *k* + 1/3. But that's 
a contradiction becaue *k* + 1/3 is not an integer. &there4; no number of the 
form 3*m* can be expressed as 3*k* + 1 (m, k &isin; &integers). This is actually 
stronger than what we were going for. This shows that our result will hold not 
only for 3(2<sup>_n_</sup> but for any number 3*m*(2<sup>_n_</sup>). 

So, for example, we can say for sure that every _i_ such that _i_ =
178092921(2<sup>_n_</sup>)[^divbythree] will descend directly to 178092921 and
that none of the numbers encountered along the way are possible hailstone
iterates of an odd number. (We should note, however, that this tells us nothing
about the hailstone sequence of 178092921.)

These sequences of even numbers which can't be reached by any odd numbers are like 
infinitely long branchless twigs on our tree. We find them by looking at all even 
numbers of the form 3*m*(2<sup>_n_</sup>). That's only one third of the even numbers.
The other two thirds can be expressed as either (3*m*+1)(2<sup>_n_</sup>) or 
as (3*m*+2)(2<sup>_n_</sup>. Can we show that these numbers cannot be reached from 
odds. In other words, can we show that they cannot be expressed as 3*k*+1? 

Unfortunately, no. We cannot. Proof is by counterexample. First, consider that 10
is equal to both 3\*3+1 and ((3\*1)+1)(2<sup>1</sup>). Similarly 14 is equal to 
both (3\*4)+2 and ((3\*2)+1)(2<sup>1</sup>). 

Okay, time to recap what we know so far. 

* 2<sup>_n_</sup> => 1
* if _n_ => 1, then _n_ => 2<sup>_k_</sup> for some _k_.
* (4,1), (16,5), (64,21), (256,85), ...
* 3*m*(2<sup>_n_</sup>) => 3*m*
* 






[^altdesc]: In which we slyly teach students about mathematical proof by contradiction and induction, graph data structures, python generators, and more!

[^divbythree] We know 178092921 is divisible by three because the sum of its digits is divisible by three. This is a nifty trick to know about. 

[^proof]: Here is a proof that any power of four can be expressed as 3*k*+1. First, we show that there exists an _n_ such that 4<sup>_n_</sup> can be expressed as 3*j*+1. This is easy to do. Let _n_ = 0. Then 4<sup>_n_</sup> = 4<sup>0</sup> = 1 = 3(0) + 1. So, when _j_=0 and _k_=0, 4<sup>_n_</sup> = 3*j*+1. Thus, we have our base case. Next, to prove by induction, we must only show that _if_ 4<sup>_n_</sup> can be expressed as 3*j*+1 _then_ 4<sup>_n_+1</sup> can be expressed as 3*k*+1. We proceed by "assuming our antecedent"; in other words, we assume the "if" part of that if-then statement. : 4<sup>_n_</sup> = 3*j* + 1 &#8658; 4<sup>_n_</sup> = 4(3*k* + 1) &#8658; 4<sup>_n_+1</sup> = 12*k* + 4 &#8658; 4<sup>_n_+1</sup> = 12*k* + 3 + 1 &#8658; 4<sup>_n_+1</sup> = 3(4*k* + 1) + 1 &there4; _if_ 4<sup>_n_</sup> = 3*k* + 1 _then_  4<sup>_n_+1</sup> = 3*k* + 1 (where *k* = 4*m* + 1)Q.E.D. (That's what people write when they finish a proof. I think it stands for "Quite Easy! Duh!" or maybe something in Latin.) 


