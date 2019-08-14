---
layout: post
title: "Infinite Stepping Stones"
date: "2019-08-14"
categories: [math, programming]
---

# The Difference of Squares

What is the difference between two perfect square numbers? If I gave you any specific case, all it'd take is a calculator or even some paper to figure it out. For example, the difference between \\( 4^2 \\) and \\( 5^2 \\) is \\( 25 - 16 \\), or \\( 9 \\). A better question to ask is, "is there a pattern?" Could we figure out what the difference is between squares, in general? Of course we can!

Squaring a number can be written algebraically as \\( n^2 \\). Likewise, squaring "the next number" can be written as \\( (n+1)^2 \\). To find the difference between these, we just need to solve for \\( (n+1)^2 - n^2 \\).

\\[
(n+1)^2 - n^2 = 2n + 1
\\]

This tells us that the difference between any two neighboring perfect squares is going to be equal to \\( 2n + 1 \\). Looking back at how \\( 5^2 - 4^2 = 9 \\), if \\( n = 4 \\), then \\( 2n + 1 \\) is indeed \\( 9 \\).

# The Finite Difference

What we just did has a name, the *finite difference.* It's usually notated like this:

\\[
\Delta_h f(x) = f(x + h) - f(x)
\\]

In our case, we solved for \\( \Delta_1 (x^2) \\), which expands to \\( (x+1)^2 - x^2 \\). We could have solved for what the difference was between a square and the square after the next, \\( \Delta_2 (x^2) \\), which would expand to \\( (x+2)^2 - x^2 \\). You can use finite differences to step along a function, assuming you know your current location and the difference to add to arrive at the next location.

For example, you may know that \\( 5^3 \\) is \\( 125 \\), but how about \\( 6^3 \\)? Instead of calculating it directly, we could determine it indirectly by first calculating \\( \Delta_1 (x^3) \\), which is \\( 3x^2 + 3x + 1 \\). Thus, \\( 6^3 = 125 + 3(5)^2 + 3(5) + 1 = 215 \\). It isn't the most efficient way to calculate \\( 6^3 \\), but it does work.

As a side note, as our step size becomes smaller and smaller, we start to take smoother and smoother steps along some curve. In fact, as the \\( h \\) in \\( \Delta_h \\) approaches zero, it stops being a finite difference and becomes a *differential*. This is a big part of the definition of the derivative!

\\[
\lim_{h \to 0} \frac{\Delta_h f(x)}{h} = \frac{\text{d}y}{\text{d}x} f(x)
\\]

# Stepping Forwards Forever

We just calculated \\( 6^3 \\) from \\( 5^3 \\). That idea applies to essentially any function. Given a starting point and a difference, we can calculate the next number, forever. For example, given the fact that \\( 0^2 = 0 \\) and that \\( \Delta_1(x^2) = 2x + 1 \\), we can calculate all perfect squares, step-by-step:


\\[
0^2 = 0 \newline
1^2 = 0^2 + 2(0) + 1 = 1 \newline
2^2 = 1^2 + 2(1) + 1 = 4 \newline
3^2 = 2^2 + 2(2) + 1 = 9 \newline
4^2 = 3^2 + 2(3) + 1 = 16 \newline
\vdots
\\]

This means that we can move forward through the infinite sequence of squares, and all we need to know is:

- The last square
- The difference function
- The current number

When it comes to programming, it turns out that this can be very useful. It allows us to store infinite data in finite space.

# Generators

Consider a list of square numbers, `[0, 1, 4, 9, 16, 25, ...]`. If you needed to store a very large amount of squares, memory constraints can become a problem. Instead, we can create an object which starts at \\( 0 \\), then calculates the next element as-needed. In this way, it can step forwards through a list which may contain essentially infinite elements, and the only space it would take up is what's required to state its current position in this list. Such an object is known as a *generator*.

Python has support for generators built-in. This square generator could be written as:

~~~ python
def squares():
  n = 0
  nSquared = 0

  while True:
    yield nSquared

    nSquared += 2*n + 1
    n += 1
~~~

And could be used as such:

~~~ python
s = squares()

print(next(s)) # 0
print(next(s)) # 1
print(next(s)) # 4
~~~

Generators aren't limited to numeric functions. *Any* list where it's possible to determine the difference between elements can be represented with a generator. Consider a function \\( f \\) where \\( f(1) = \text{"s"} \\), \\(f(2) = \text{"ss"} \\), \\(f(3) = \text{"sss"} \dots\\). It should be clear that \\( \Delta_1 f(n) = \text{'s'} \\). Therefore, one potential generator is:

~~~ python
def sGen():
  s = ""

  while True:
    yield s
    s += "s"
~~~

Generators like that are very good when you only need to iterate over the sequence a single time, without ever needing to go backwards or repeat yourself. They're the programming equivalent of the *forward difference*. However, there's also the *backwards difference*:

\\[
	\nabla_h f(x) = f(x) - f(x - h)
\\]

The backwards difference tells you how much to subtract from the current value in order to arrive at the previous one. For example, \\( \nabla_1 (x^2) = x^2 - 1 \\). This means that since \\( 4^2 = 16 \\), we can calculate \\( 3^2 \\) as \\( 4^2 - 2(4) + 1 \\).

# A Generalized Generator

With the ability to move forwards and backwards, we could create a generator which more closely behaves like an array. Let's assume that our generator is at the current position \\(n\\). If we try to access `g[m]`, where \\(m > n\\), we can just use the forward difference until we arrive at \\(m\\). If \\( m < n \\), then we can use the backwards difference instead.

Here is some example Python code which implements the idea, although this would absolutely be possible in a wide variety of languages:

~~~ python
class Generator:
  def __init__(self, start, forward, backward):
    self.forward = forward
    self.backward = backward
    self.val = start
    self.index = 0

    return
  
  def get(self, i):
    if i > self.index:
      while i > self.index:
        self.val += self.forward(self.index)
        self.index += 1
    
    elif i < self.index:
      while i < self.index:
        self.val -= self.backward(self.index)
        self.index -= 1
    
    return self.val

~~~

We can recreate our list of squares with:

~~~ python
squares = Generator(0, lambda n: 2*n + 1, lambda n: 2*n - 1)
~~~

Now `squares.get(n)` returns \\( n^2 \\). This is even more general than a traditional array, because `squares.get(-10)` is perfectly acceptable, and will correctly return \\( -10^2 \\). Like the other generators, this also stores infinitely many values in finite space. It only needs space to store: the current index, the current value, a pointer to the forward difference function, and a pointer to the backwards difference function.

# Some Function Tomfoolery

Our generalized generator has a few interesting properties. When created from the differences of \\(f(n)\\), it is *equal* to \\(f(n)\\) itself, so long as \\(n\\) is an integer. This means that our generator recreates arbitrary functions from their differences. If the finite difference of a function is analagous to its finite derivative, then the generator is like a finite integral.

The Fundamental Theorem of Calculus states that the integral of the derivative of \\(f(x)\\) is just equal to \\(f(x)\\):

\\[
	\int f^\prime (x) \ \text{d}x = f(x)
\\]

But for our generator, we have that:

\\[
	\forall n \in \Z,\ \text{Generator}(f(0),\ \Delta_1f(n),\ \nabla_1f(n)).\text{get}(n) = f(n)
\\]

It may not come up often, but it could still be useful. When building these arrays, it may in some cases be easier to think of what the differences should be than the actual function. Our generator lets you build the function from its differences!

I think generators are very useful in general for representing streams of information. Sometimes you need the idea of something, rather than an example. For example, maybe you need ten values from a list which has been filtered through a chain of tests. You don't want to worry about how large the initial list should be in order to find ten acceptable values in it, so setting it to infinitely large is a good ease on your cognitive burden.

Plus hey, it's cool.
