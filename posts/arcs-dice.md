---
title: 'Arcs Dice Blog'
---

I've recently gotten very into Leder Games/Cole Wherle's newest board game, Arcs. It's a scrappy, clever game about clashing space empires, and it's chock full of deliciously thorny mechanics that lead to interesting decisionmaking.

One of these mechanics is its system of building dice pools. Whenever you send your ships into combat in Arcs, you assemble a dice pool of one die per ship, and resolve the combat in a single roll. What makes this such an interesting bit of decisionmaking is the different types of dice you can pull from for your pool:

![Arcs' Assault, Raid, and Skirmish dice](images/arcs/dice-stolen.jpg)

Each die has a different utility and risk profile: blue Skirmish dice are basically a coin flip for whether you'll do damage, but carry no risk of harming your own ships. The red Assault dice do considerably more damage to the enemy, but with a higher chance of blowing up your fleet. And the orange Raid dice carry the most risk of self-damage, as well as chances of blowing up enemy buildings (which can be good or bad), but importantly are the only dice which let you roll keys, the game's way of stealing resources and other goodies.

Building your dice pool is an exercise in risk management: how many of your own ships can you afford to blow up to get what you want? With with the exception of blue Skirmish dice, the symbols are unevenly allocated over the faces of each die, making for some dramatic dice rolls.

Now to be clear, I don't think Cole Wherle intended players to perform exacting calculations of the odds for every dice pool. The gist of the mechanic is that one die is safe, one is risky, and one is _real risky_, so plan accordingly.

But as a fan of recreational math, I got to wondering: **given any assortment of Skirmish, Assault, and Raid dice, what are the odds of rolling any given outcome?** And as it turns out, what I thought would be a fairly dreadful brute-force statistics problem actually led to some elegant math in unexpected places.

## A simpler case

To start with, let's analyze the case of rolling a pool of $n$ standard six-sided dice and adding up the values. We can consider each die to be a **discrete random variable**, which means something whose value is randomly determined from a discrete set of values. In this case, those values are ${1, 2, 3, 4, 5, 6}$, i.e. the number of pips on each die face. Let's call that random variable $D$ for die.

Next, we'd try to model a single roll of this dice pool, which is just the sum of all the random die variables. Since we have $n$ dice, let's call the roll $R_n$, and thus $R_n = D_1 + D_2 + ... + D_n$.

Now, we can finally ask a question like "when rolling 4 dice, what are the odds of rolling a 7?" Or, in a more mathy parlance, what is the value of $R_n$'s [**probability mass function** (PMF)](https://en.wikipedia.org/wiki/Probability_mass_function) $P(R_n = 7)$? <aside>Probability mass functions are (in my opinion) terribly named owing to a tortured metaphor of probability "densities", but conceptually they're actually really simple: they're just functions which return the probability of getting a value for a given discrete random variable. For example, for a single die, the function is just $\dfrac{1}{6}$ for all values.</aside> The problem, of course, is we don't know what the PMF is yet, so we can't evaluate it at anything!

There's a few ways we can go about calculating the PMF. Grant Sanderson (aka 3Blue1Brown) has a fantastic [video on convolutions](https://www.youtube.com/watch?v=KuXjwB4LzSA) which visualizes some of them, but the approach we'll be taking here is considerably _quirkier_.

## Generating functions: a prelude

To spoil the ending a bit, we'll be using things called [**generating functions**](https://en.wikipedia.org/wiki/Generating_function) to analyze these dice pool distributions. But I have to admit, when I first learned this approach, it felt _extremely bizarre_. Generating functions can often seem like total non-sequiters in their applications, and if you've never dealt with them before, my goal in this post is to motivate their usage from first principles, and explain why they're a great fit for this kind of problem. But to quote [another Grant Sanderson video](https://www.youtube.com/watch?v=bOXCLR3Wric), "there's a time in your life before you understand generating functions, and a time after, and I can't think of anything that connects them other than a leap of faith."

Back to our roll of $n$ dice and its result, $R_n$. To make things a bit more simple, let's consider the case of two dice ($R_2$), and write down all of 36 possible sums of the two rolls:

|       | **1** | **2** | **3** | **4** | **5** | **6** |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|**1**  | 2     | 3     | 4     | 5     | 6     | 7     |
|**2**  | 3     | 4     | 5     | 6     | 7     | 8     |
|**3**  | 4     | 5     | 6     | 7     | 8     | 9     |
|**4**  | 5     | 6     | 7     | 8     | 9     | 10    |
|**5**  | 6     | 7     | 8     | 9     | 10    | 11    |
|**6**  | 7     | 8     | 9     | 10    | 11    | 12    |

As expected, $2$ and $12$ occur just once to account for the unique rolls (1, 1) and (6, 6) that produce them, and $7$ is the most frequent outcome. Since each individual cell of this chart has a $\dfrac{1}{6} * \dfrac{1}{6} = \dfrac{1}{36}$ chance of occurring, we can simply multiply the number of occurrence of each value by $\dfrac{1}{36}$ to get the probability of that outcome:

| Outcome | # Occurrences | Probability |
|:-------:|:-------------:|:-----------:|
| 2       | 1             | $\dfrac{1}{36}$      |
| 3       | 2             | $\dfrac{2}{36}$      |
| 4       | 3             | $\dfrac{3}{36}$      |
| 5       | 4             | $\dfrac{4}{36}$      |
| 6       | 5             | $\dfrac{5}{36}$      |
| 7       | 6             | $\dfrac{6}{36}$      |
| 8       | 5             | $\dfrac{5}{36}$      |
| 9       | 4             | $\dfrac{4}{36}$      |
| 10      | 3             | $\dfrac{3}{36}$      |
| 11      | 2             | $\dfrac{2}{36}$      |
| 12      | 1             | $\dfrac{1}{36}$      |

And hey, just like that we've just built a PMF! To calculate the probability of some roll, just look up the outcome and read out its corresponding probability.

Before we proceed, let's take a step back and generalize the steps we took here. For ease of reference, I'm also going to give each step a name:

1. **Enumeration**: Given the faces on each die, we enumerated the collection of all possible rolls
2. **Combination**: For each possible combination of rolls $D_1 = a$ and $D_2 = b$:
    a. We calculated the outcome's value: $a + b$
    b. We calculated the outcome's probability: $P(D_1 = a) * P(D_2 = b)$ (which in our example was just $\dfrac{1}{6} * \dfrac{1}{6}$, but worth writing out for later)
3. **Consolidation**: We added together the probabilities of all rolls with equivalent outcomes

Now for the leap of faith. Suppose we wanted a nice, compact, closed-form equation that performs each of these three steps. Luckily, that's exactly what generating functions allow us to do, though it's not so clear from the outset _how_ they manage to do it.

## Discovering our generating function

Instead of diving into generating functions head-first, let's approach them a bit more cautiously, and see if we can stumble across one just by trying to design a process that reproduces the 3 above steps: **Enumeration**, **Combination**, and **Consolidation**. For the rest of this section, we'll be considering the rolls of two standard six-sided dice, $D_1$ and $D_2$.

First off, **Enumeration**. What we're basically looking for is something called a [Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) of both die's set of face values. This would give us all possible pairs of faces from the two dice. One idea that might occur to us is that regular old multiplication can do something like this, which many of us learned as FOIL (First, Out, Inner, Last):

$$(A + B) * (C + D) = AC + AD + BC + BD$$

So if we could somehow encode each die as a sum of _somethings_ representing each face, then multiplying those two sums together would give us a giant FOIL'd sum representing every combination of dice faces. For example if we say $D_1$'s _somethings_ are $D_1 = a_1 + a_2 + a_3 + a_4 + a_5 + a_6$, and $D_2$'s are $D_2 = b_1 + b_2 + b_3 + b_4 + b_5 + b_6$, then:

$$ D_1 * D_2 = (a_1 + ... + a_6) * (b_1 + ... + b_6) = a_1 * b_1 + a_1 * b_2 + ...$$

would give us all combinations of $a$'s and $b$'s. But simply setting those _somethings_ to be the face values (e.g. $D_1 = 1 + 2 + 3 + ...$) clearly wouldn't work, since we'd just up with the product of two numbers. What else might work?

Well, whatever those _somethings_ are, we know we're going to be multiplying them together, so let's recall what we want from our **Combination** step: the product of two faces should somehow a) add the face values, and b) multiply their respective probabilities. At first, this might seem like something that could only be done in two separate operations. But suppose we encode two outcomes, say $D_1 = 2$ and $D_2 = 6$, like this:

$$ D_1 = 2 \qquad \implies \qquad (\dfrac{1}{6}) * x^2$$
$$ D_2 = 6 \qquad \implies \qquad (\dfrac{1}{6}) * x^6$$

What we've done here is take the probability of rolling each outcome ($\dfrac{1}{6}$), and set it as the coefficient of a single-term polynomial whose degree is equal to the face's value. Why would we possibly do that? Let's see what happens when we multiply them together:

$$(\dfrac{1}{6}x^2) * (\dfrac{1}{6}x^6) = (\dfrac{1}{6}) * (\dfrac{1}{6}) * x^(2 + 6) = (\dfrac{1}{36}) * x^8$$

Sure enough, we arrive at a new single-term polynomial whose coefficient is the probability of the two-dice outcome, and whose degree is equal to the sum of the two rolls. We've taken advantage of the unique property that exponents are added when their bases are multiplied to arrive at **Combination**'s second property, while also multiplying the probability coefficients to get the first property. Pretty convenient!

For the final step, **Consolidation**, we'd like to add up the probabilities of all outcomes with equivalent values. Luckily enough, our polynomial-based encoding scheme handles this for us automatically, since any outcomes with matching degrees will add together naturally:

$$p_1x^a + p_2x^a = (p_1 + p_2)x^a$$

Now we can start putting the pieces together. Since all faces $1$ through $6$ of a standard six-sided die have an equal chance of being rolled, it can be described by the polynomial:

$$f(x) = \dfrac{1}{6}x^1 + \dfrac{1}{6}x^2 + \dfrac{1}{6}x^3 + \dfrac{1}{6}x^4 + \dfrac{1}{6}x^5 + \dfrac{1}{6}x^6$$

This is called a **generating function**, and although it seems odd to bring this seemingly unrelated polynomial into our problem, remember that we're just using it to associate probabilities to outcomes. And since, as we've seen, multiplying polynomials reproduces our 3 steps, if we multiply $f(x)$ by itself we should arrive at a generating function that encodes the probability distribution of rolling two dice:

$$f(x) * f(x) = f^2(x) = \dfrac{1}{36}x^2 + \dfrac{2}{36}x^3 + \dfrac{3}{36}x^4 + \dfrac{4}{36}x^5 + \dfrac{5}{36}x^6 + \dfrac{6}{36}x^7 + \dfrac{5}{36}x^8 + \dfrac{4}{36}x^9 + \dfrac{3}{36}x^10 + \dfrac{2}{36}x^11 + \dfrac{1}{36}x^12$$

If we match each exponent-value with the outcome/probability table above, we'll find that all of the coefficients indeed match the expected probabilities. Moreover, by multiplying our generating function together repeatedly, we can arrive at a compact, closed-form description for an arbitrarily sized dice pool. And so, at last, we can derive the PMF for $R_n$ simply by calculating $f^n(x)$.

## An interlude on $x$

"Cool functions", I can hear you saying, "but what are they functions _of_?" And you're right to be leery. If the coefficients of our function represent probabilities, and the exponents represent dice faces, what meaning does $x$ have?

Well in short, it's just a symbol. When generating a PMF, we're not actually evaluating our generating functions $f(x)$ for any values of $x$. As Herbert Wilf said in the excellent [_generatingfunctionology_](https://www2.math.upenn.edu/~wilf/DownldGF.html), "a generating function is a clothesline on which we hang up a sequence of numbers for display". The coefficients of these functions are the sequence we're interested in displaying, not the values of the function itself.

That said, you can definitely treat these critters like real functions and arrive at some really neat insights into your subject. One basic example of this would be to evaluate $f(1)$, which for our generating function above would simply equal the sum of the coefficients (a.k.a. probabilities). And since all probabilities in a distribution must add to equal $1$, we know that $f(1) = 1$.

A more interesting example of this would be $f(-1)$. What do you think this might do? Well, $(-1)^n$ for even powers of $n$ is just $1$, and for odd powers it's $-1$, so we end up with:

$$f(-1) = \dfrac{1}{6} * (-1) + \dfrac{1}{6} * 1 + \dfrac{1}{6} * (-1) + \dfrac{1}{6} * 1 + \dfrac{1}{6} * -1 + \dfrac{1}{6} * 1 = 0$$

In other words, it subtracts the probability of rolling an odd face from the probability of rolling an even face. In our case, this obviously cancels out to 0, since a 6 sided die has an equal number of even and odd faces. But it also tells us that rolling _any_ number of standard six-sided dice and adding the result has an equal chance of giving an even or odd number, since $f^n(-1) = f(-1) * f^n-1(-1) = 0$.

For more on this, be sure to check out Grant Sanderson's [video on generating functions for solving a tough puzzle](https://www.youtube.com/watch?v=bOXCLR3Wric) that I referenced above.

## Non-standard dice

So what if we have dice with different numbers of sides, or different allocations of pips? Have no fear, generating functions got your back. Simply follow the rules of generating functions for PMFs:

1. The sum of all coefficients must equal 1
2. No coefficients can be negative

With that, let's try creating the generating function for a 4 sided die with faces ${0, 3, 7, 7}$:

$$f(x) = \dfrac{1}{4} + \dfrac{1}{4}x^3 + \dfrac{1}{4}x^7 + \dfrac{1}{4}x^7 = \dfrac{1}{4} + \dfrac{1}{4}x^3 + \dfrac{1}{2}x^7$$

Since we only have 4 sides, we adjust the coefficients to be $\dfrac{1}{4}$, and since one side has 0 pips, we straightforwardly enough assign its probability to $x^0$, a.k.a. 1. And as you might hope, to obtain the result of rolling dice with different allocations of pips and different sides, you simply multiply their respective generating functions together.

## Arcs!

We just need one more tool before tackling the dice in Arcs: a way of handling multiple _types_ of pips. In Arcs, each dice can any number of 5 different symbols. I'll assign each of them a letter, which will be useful going forward:

| Symbol | Name | Letter |
|:------:|:----:|:------:|
| ![Hit symbol](images/arcs/symbol-hit.png) | Hit | $h$ |
| ![Building hit symbol](images/arcs/symbol-buildinghit-white.png) | Building hit | $b$ |
| ![Self-hit symbol](images/arcs/symbol-selfhit.png) | Self-hit | $s$ |
| ![Intercept symbol](images/arcs/symbol-intercept.png) | Intecept | $i$ |
| ![Key symbol](images/arcs/symbol-key.png) | Key | $k$ |

And for ease of reference, here are the layouts of each die:

![Diagram of Arcs' Assault, Raid, and Skirmish dice](images/arcs/dice.png)

How can we represent these? One possiblity might be to create separate generating functions for each type of symbol appearing on each die. So the Assault die would have a hit function, a self-hit function, and an intercept function. But this approach treats the different pip types as independent from one another, and would fail to account for the fact that the 1 intercept symbol on Assault dice can only ever be paired with the single hit symbol.

Instead, we make our generating functions _multivariate_, i.e. they are functions of more than one variable. In this model, we'll be writing three dice functions on the five pip type variables: $A(h, s, b, i, k)$ for Assault dice, $S(h, s, b, i, k)$ for Skirmish, and $R(h, s, b, i, k)$ for Raid:

$$
\begin{align}
&A(h, s, b, i, k) = \dfrac{1}{6} + \dfrac{1}{6}h^2 + \dfrac{1}{6}h^2s + \dfrac{1}{6}hi + \dfrac{1}{3}hs \\
&S(h, s, b, i, k) = \dfrac{1}{2} + \dfrac{1}{2}h \\
&R(h, s, b, i, k) = \dfrac{1}{6}ik^2 + \dfrac{1}{6}hk + \dfrac{1}{6}bk + \dfrac{1}{3}hb + \dfrac{1}{6}i \\
\end{align}
$$

Note that constant values represent empty faces, and that the probabilities of common terms have been summed together. For brevity's sake, I'll refer to these functions by just their capital letters from here on out.

And now we finally have a way to compute the general PMF of any given roll for an arbitrary Arcs dice pool: for a pool of $x$ Assault dice, $y$ Skirmish dice, and $z$ Raid dice, we compute $A^xS^yR^z$, and then lookup the coefficient of a given roll.

Let's try a simple example. For a pool with two Assault dice and one Skirmish die, we'd multiply and expand $A^2S^1$:

$$
\begin{align}
A^2S &= (\dfrac{1}{6} + \dfrac{1}{6}h^2 + \dfrac{1}{6}h^2s + \dfrac{1}{6}hi + \dfrac{1}{3}hs)^2 * (\dfrac{1}{2} + \dfrac{1}{2}h)  \\
 &= \dfrac{1}{72} + \dfrac{1}{72}(h^5 s^2) + \dfrac{2}{72}(h^5 s) + \dfrac{1}{72}h^5 + \dfrac{2}{72}h^4 i s + \dfrac{2}{72}(h^4 i) \\
 &\quad + \dfrac{5}{72}(h^4 s^2) + \dfrac{6}{72}(h^4 s) + \dfrac{1}{72}h^4 + \dfrac{1}{72}(h^3 i^2) + \dfrac{6}{72}h^3 i s + \dfrac{2}{72}(h^3 i) \\
 &\quad + \dfrac{8}{72}(h^3 s^2) + \dfrac{6}{72}(h^3 s) + \dfrac{2}{72}h^3 + \dfrac{1}{72}(h^2 i^2) + \dfrac{4}{72}h^2 i s + \dfrac{2}{72}(h^2 i) \\
 &\quad + \dfrac{4}{72}(h^2 s^2) + \dfrac{6}{72}(h^2 s) + \dfrac{2}{72}h^2 + \dfrac{2}{72}(h i) + \dfrac{4}{72}(h s) + \dfrac{1}{72}h \\
\end{align}
$$

As you can see, our (_checks notes_) "simple" example has give us a pretty unweildy polynomial. But, if you wanted, you could add up all of the coefficients and verify they equal $1$ (I certainly did). And although it's a bit painful to look at, we can fairly easily notice a few things:

* There's a $\dfrac{1}{72}$ chance of rolling nothing
* All other $\dfrac{71}{72}$ possible outcomes include at least one Hit
* The most likely roll is 3 Hits, 2 Self-hits with probability $\dfrac{9}{72}$

We can also draw more interesting statistics from this by combing through the terms. For example, the question "what are the chances of rolling more than 3 hits and up to 1 self-hit" is just a matter of adding up the probabilities for every term with a factor of $h^ns^m$ where $n \geq 3$ and $m \leq 1$.
