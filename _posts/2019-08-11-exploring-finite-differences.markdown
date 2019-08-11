---
layout: post
title: "Infinite Stepping Stones"
date: "2019-08-11"
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

As a side note, as our step size becomes smaller and smaller, we start to take smoother and smoother steps along some curve. In fact, as the \\( h \\) in \\( \Delta_h \\) approaches zero, it stops being a finite difference and becomes a *differential*. This is the definition of a derivative!

\\[
\lim_{h \to 0} \Delta_h f(x) = f^\prime (x)
\\]

# Stepping Forwards Forever

We just calculated \\( 6^3 \\) from \\( 5^3 \\). That idea applies to essentially any function. Given a starting point and a difference, we can calculate the next number, forever.