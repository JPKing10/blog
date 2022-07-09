+++
title = "HackerRank The Chase Writeup"
date = 2019-03-20T00:00:00Z
tags = ["HackerRank", "ProjectEuler+", "Probability"]
aliases = ["/posts/hackerrank-projecteuler-227-writeup/", "/posts/hackerrank-projecteuler+-227-writeup/"]
+++
A long long time ago I solved [The Chase (Project Euler 227) on HackerRank](https://www.hackerrank.com/contests/projecteuler/challenges/euler227). It took me a long time to figure it out, so I wrote up these notes. 

<!-- more -->

[Bayleef](https://www.hackerrank.com/profile/bayleef) amended the problem from the original and it is a bit more complicated. Namely, it requires an exact answer. And the inputs can be rather large, so we need to make sure our algorithm is efficient.

## The problem

An even number of players are playing The Chase. At the start, two players on opposite sides of a table are each given m-sided dice. On each turn, both players roll the dice. If it lands on 1, they pass it to the player on their left. If it lands on m, they pass it to the player on their right. The game ends when one player has both dice at the end of a turn.

We need to answer: for n players and m-sided dice, how many turns will the game last?

## Modulo 10<sup>9</sup> + 9

The numbers we could be dealing with in our solution could get quite large so the result is returned modulo 10^9 + 9. In this case, you can think of modulo as a checksum. The result is that the biggest numbers used in the solution will fit into a `long long` and most variables will fit into an `int`.

You can learn more about why modulo is needed and how to approach questions involving it in [A. Sharma's article](https://www.hackerearth.com/practice/notes/abhinav92003/why-output-the-answer-modulo-109-7/). However, I'll outline the basics here.

The first important thing to note is that the question requires an **exact answer**. If you have a solution to the original Project Euler problem, you probably took an approach which gave an approximate answer (to the precision specified in the original problem). That's not going to work here. To start figuring out an approach, you might want to look at [Markov chains](http://www.dartmouth.edu/\~chance/teaching_aids/books_articles/probability_book/amsbook.mac.pdf).

The modulo operation has the following distributive properties:

    (a + b) % c == ((a % c) + (b % c)) % c
    (a - b) % c == ((a % c) - (b % c)) % c
    (a * b) % c == ((a % c) * (b % c)) % c

However, modulo is _not_ distributive over division:

    (a / b) % c != ((a % c) / (b % c)) % c

To approach division under modulo, we instead multiply by the inverse of the divisor:

    a / b == a * b^-1

We call b<sup>-1</sup> the multiplicative inverse, and there are [several algorithms available](https://www.geeksforgeeks.org/multiplicative-inverse-under-modulo-m/) to help us calculate it (I'll leave you to decide which to use). Rather than use division we can now multiply by the multiplicative inverse.

## Optimisations

To enable your code to successfully process larger inputs within the available time and memory limits you'll need to optimise.

My first piece of advice is to write your solution in a fast language. I originally wrote mine in Python 3 but it timed out for test case #36 and onward. I rewrote the algorithm in C++ and successfully completed all the test cases.

However, this might say more about the inefficiency of my algorithm (rather than Python). [oleg_b](https://www.hackerrank.com/oleg_b?hr_r=1) commented in the [discussion](https://www.hackerrank.com/contests/projecteuler/challenges/euler227/forum) that there is a Python2 solution that has a worst runtime of 2.6 secs. 

The next is to write out the matrix you are trying to solve (using a placeholder for the values). By doing this you should be able to notice a number of optimisations in terms of memory usage and the number times your code loops.

## Further reading

I hope that this gives you some extra insight into how to solve the problem without taking away all the fun. If you'd like to learn more about the techniques I used then I suggest reviewing the following:

1. [Introduction to Probability by C. Grinstead and J. Snell - Chapter 11 Markov Chains](http://www.dartmouth.edu/\~chance/teaching_aids/books_articles/probability_book/amsbook.mac.pdf)
2. [Why “OUTPUT THE ANSWER MODULO 10^9 + 7"? by A. Sharma](https://www.hackerearth.com/practice/notes/abhinav92003/why-output-the-answer-modulo-109-7/)
3. Linear Algebra and Its Applications by G. Strang (4th ed) - 1.6 Inverses and Transposes
4. [Modular multiplicative inverse on GeeksforGeeks](https://www.geeksforgeeks.org/multiplicative-inverse-under-modulo-m/)

