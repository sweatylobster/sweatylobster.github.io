# Intro
The business is a flat-rate ride service.
The cost is the driver's wage.
Our data shockingly reveals low wages are accepted less often.
So the challenge is serving the rider at a profitable cost.
(We never make money when the rider is not served.)
Our large supply pool is an asset to be taken advantage of.
We can ameliorate the low acceptance rate of an ideal wage by offering to a large number of drivers.
The principle is this.
It's implausible you flip a coin 10 times and see only the same face.
Snake eyes *will* come up in a long enough game of craps, by the same.
Therefore *somebody* will give this guy a ride if we ask enough people.
Since we can't answer: "How many drivers do I need to offer this wage to before one of them accepts?"
We answer instead: "We offered $13 dollars to 40 drivers and none accepted. How likely is that?"
We do a binomial distribution to simulate an experiment of this kind.

## Lowballing drivers strategically with the binomial distribution
The binomial distribution eponymously asks for two numbers, probability and number of trials.
It effectively rolls "the dice" however many times you ask.
We can intuit each wage as a special die.
We roll the acceptance ratio of each wage over a large number of trials.
We estimate the chances of our supply *not* meeting demand at this wage by counting the failures in the trial.
With this heuristic, we can set our wages based on the number of drivers we have.
As we try it out in practice, the feedback will reveal truer probabilities of acceptance.
In other words, we'll set our wages in proportion to our supply pool more skillfully as time goes on, market conditions remaining the same.


<!-- INSERT IMAGE -->
![]('images/binomial_wages.png')
<!-- In our case, the acceptance rates for profitable wages are the dice we're rolling. -->

## Problem
We want to determine how profitable the business is.
Profit is cost plus revenue. 
Our total acquisition cost is 300k, at 30 dollars for each of the 10k customers.
We can add 1k riders at most each month, so the max monthly acquisition cost is 30k.
Because we start at a loss, we want to ensure we maintain customers. 
Doing so gives the greatest room for growth.
Habituation of the app's convenience is proportional to app usage. 
So if we can afford it, we acquire customers at the maximum rate, and satisfy them for the longest time.

The revenue, in any period, is profit-per-ride times rides given in the period.
We projected a 10 dollar profit on average with the Binomial distribution assuming ~20 drivers are working at all times.
I take this to follow when we say our supply pool is "very deep."
Now all we need is to rationally project the number of rides we can deliver.
The Poisson distribution projects this figure in keeping with the following assumptions.

## Justification of the Poisson distribution for our problem

1. The customer will probably use the app once in the first month. (Why else would they sign up?)
2. In any subsequent month, a customer will use the app about as many times $k$ as last month $\lambda$.
3. Therefore, if we maintain the size of our supply pool, they'll *get* about as many rides as last month.
4. We're agnostic about why a customer uses or leaves the app.
5. We're dealing with rare events. We can take 'rare' to mean that, at most, someone will use the app about 6 times a month.

If these are fair for our problem, the Poisson distribution is fair for our problem.
It projects the likelihood that an event happens $k$ times when it tends to happen $\lambda$ times.
To get an estimate of the total rides in the year, we use last month's value to project next month's.
We'll call this projection resampling, or $k$ given $\lambda$.

Now we can translate two reasonable propositions into their arithmetical representation.
1. "Assume new riders will get at least one ride." Or why else did they join the app?
Each NEW group, then, is a Poisson distribution where $n$ is 1000 and $\lambda$ is 1.
For our information, the sum of a Poisson distribution is roughly $n * \lambda$, so we expect ~1000 rides from the first month's users.
Then for the second proposition:
2. "Users will get about as many rides as last month."
The permissibility of this assumption is what grounds the legitimacy of resampling.
Resampling is merely the arithmetical form of this statement.
The only room for error, then, is how the Poisson distribution evaluates next month's $k$ given last month's $\lambda$.
But the procedure lies on the grounds of common sense.

## Projecting the yearly rides
We repeat this process for each customer which isn't a part of the new group (which is always $\lambda$ = 1, $n$ = 1000).
10k riders can join at a maximum of 1k a month. 
The projection of the number of rides for each group is given by the following table.
NEW means we have generated a poisson distribution with lambda equal to 1, and n equal to 1k.
R1, R2, R3... etc. signify the 1st, 2nd, 3rd... resample, respectively.
We'll notice that there are only two months for growth with the final group.

### Table
|Group| Jan | Feb | Mar | Apr | May | Jun | Jul | Aug | Sep | Oct | Nov | Dec |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|  1  | NEW | R1  | R2  | R3  | R4  | R5  | R6  | R7  | R8  | R9  | R10 | R11 |
|  2  |     | NEW | R1  | R2  | R3  | R4  | R5  | R6  | R7  | R8  | R9  | R10 |
|  3  |     |     | NEW | R1  | R2  | R3  | R4  | R5  | R6  | R7  | R8  | R9  |
|  4  |     |     |     | NEW | R1  | R2  | R3  | R4  | R5  | R6  | R7  | R8  |
|  5  |     |     |     |     | NEW | R1  | R2  | R3  | R4  | R5  | R6  | R7  |
|  6  |     |     |     |     |     | NEW | R1  | R2  | R3  | R4  | R5  | R6  |
|  7  |     |     |     |     |     |     | NEW | R1  | R2  | R3  | R4  | R5  |
|  8  |     |     |     |     |     |     |     | NEW | R1  | R2  | R3  | R4  |
|  9  |     |     |     |     |     |     |     |     | NEW | R1  | R2  | R3  |
|  10 |     |     |     |     |     |     |     |     |     | NEW | R1  | R2  |

### Nitty gritty of the Poisson distribution, whether we ought to fine-tune caveats
This is the logic of my projection.
The exact figures, however, depend on the mechanics of the Poisson distribution.
Let's familiarize ourselves with the mechanics of the distribution by visualizing a few different values for $\lambda$.
<!-- sns.histplot(np.random.poisson(1, 100), stat='probability', discrete=True) -->
We notice each of the outcomes $k$, which are farther from $\lambda$, are drawn with diminishing frequency.
If $\lambda$ is 1, $k$=0 is about as likely as $k$=1
If $\lambda$ is 2, $k$=0 has a probability of 15%.
These probabilities won't work for modeling only if the empirical results aren't consistent with our assumptions above.
But we should ask if the Poisson distribution is false, or if we're just charging too much.
If we're charging exorbitant prices, *clearly* they won't use the app about as many times as last month.
In fact they'd only use it if they have no other option.
So we'll maintain the reasonability of the Poisson distribution's probability mass function.
The only caveat with using the Poisson distribution for our problem is what it does when $\lambda$ is 0. 
$\lambda$ is zero when, in a month's time, a customer did not get a ride with us.
We'll notice that the Poisson distribution for $\lambda$ = 0 gives a 100% probability of 0.
In a mathematical sense, this is a completely natural judgment:
"There is no chance that something will happen when *nothing* tends to happen."
But when applied to our problem, this effectively says:
"The user that didn't get a ride this month will never get a ride."
This is simply untrue.
However, for reasons we'll see down the line, this might be a serviceable mechanic of the distribution when $\lambda$=1.

#### Much ado about nothing
When we draw a Poisson distribution with $\lambda$ equal to 1, 37% of the values end up taking a $k$=0.
To be consistent with this mechanic, we would have to claim: "~37% of people who sign up for the app never use it."
This ultimately might not be unreasonable after collecting data.
The sum of a Poisson distribution approaches $\lambda$ * $n$.
So then the sum of the yearly rides is given by the number of people in the pool each month:
(1+9, 2+8, 3+7, 4+6) + 5 + (10*3) for all 12 months in the year.
The sum of yearly rides is 75k if we do not alter the Poisson distribution.
Each month's lambda value will not shift from one.
This means we can expect one ride from each customer in the pool.
The annual sums of our cost and rides are 300k and 75k respectively.
This means that we will break even at 4 dollars per ride as far as revenue is concerned.
But as far as profit is concerned, we can only break even at the end of the year if we get the driver to accept a 25 dollar wage on average.
This means that we can dig ourselves out of the hole after May if we get drivers to accept a 20 dollar wage on average.
The first 5 months, however, are taken at a loss.
Using the binomial distribution, it doesn't seem implausible for us to profit 10 dollars on average, if we do indeed have a deep supply pool.
Therefore, we can at worst expect to make upwards of 400k with the application, over the course of the first year.
We will continue making 10k a month at no acquisition cost in subsequent months by these projections.
We will be hemorrhaging money for the first half of the year, however.

### The complications of coercing 0 to take on another value 
Let's recall the rhetorical translation of a $\lambda$=0 distribution:
"The user that didn't get a ride this month will never get a ride."
To deal with this caveat, we'd have to invent an arbitrary "loss rate".
The loss rate coerces a fraction of zeroes encountered in the last month to be a different value.
The only restriction for this value is that it can't be 100%; otherwise we'd effectively be projecting as above.
A loss rate of say, 40%, has the practical effect of enunciating:
"40% of inactive users won't use the app again."
The remaining 60%, then, would be redrawn with a different value.
Potential values are:
1. A $\lambda$ of 1 for a resample,
2. A coin toss between 0 and 1,
3. Simply 1.

The third option obviously is mindless. It has no rationale.
The first option is so complicated that Ockham's razor leaves nothing of it behind.
(For those who love difficulty for its own sake, I'll explain its mechanics later.)
So options one and three are inadmissible on the grounds of rational hygiene.
Furthermore, the second option is the one most consistent with our argument this far.
Its rationale lies on the same common sense grounds as that of resampling, namely, that a customer will use the app about as many times as last month.
It's implausible that a user who didn't use the app last month, or the month before, suddenly uses the app up to three times, which can happen when $\lambda$ is one.
One could add stipulations to the first option to deal with this, but it gets too complicated to use.
So we go with the second option.
Total rides in the year are projected assuming 40% of inactive users will never use the app.
A $\lambda$=0 distribution expresses their situation perfectly.
Of the other 60%, half use it next month, and half don't.
With this assumption, we project twice the number of rides as before.
Instead of 75k, this resampling procedure projects an annual sum of rides that hovers around 130k.
When we look at the number of zeroes in the final month, we see that they take 25% of the distribution.
Let's reason in reverse from this result.
Let's recall that when $\lambda$ is 1, the outcome of 0 and 1 is equally likely at 37% each.
Then the loss rate affects up to 37% of any month's values.
40% of the zeroes leave, and half of the others use the app next month, so the fraction of zeroes is:
(.4 * .37) + (.37 * .6 * .5), which is 30%.
So our projection that, in the final month, 25% of users did not get a ride betrays that the mean lambda of the pool has increased above 1.
We are not violently coercing values to be greater than one when they were zero last month, which is great.

### Implausibility of resampling the zeroes according to a Poisson distribution where lambda is 1
Because drawing a $k$ of 0 is less likely when $\lambda$ takes on higher values, the loss rate parameter has less of an effect as time goes on.
For example, the probability of drawing 0 is 15% when $\lambda$=2, 5% when $\lambda$=3, and 2% when $\lambda$=4.
We can verify this with `scipy.stats.poisson.pmf(k, lambda)`.
Let's say we want to make life difficult.
We coerce the 0 to be 1 for a new Poisson distribution.
En masse, 37% of such resamples result in $k$ being equal to zero, and 63% result in a $k$ greater than zero.
But this itself came from a Poisson distribution where $\lambda$=1.
So we have to multiply the likelihood of getting $k$=0 from two Poisson distributions where $\lambda$=1, multiplied by the loss rate.
With a 40% loss rate, we leave 60% alone (.37 * .6). This is 22.2% of our initial distribution which has $k$=0.
Then we take the 37% of the 40% we coerced which will be equal to 0 by: (.37 * .6 * .37), which is 5.4%.
Their sum is 28%, which hardly differs from our calculation above.
So this is all needlessly complicated.
In addition to how complicated this is, there's something logically implausible about resampling according to $\lambda$=1.
We're taking for granted that a customer will use the app this month about as many times as last month.
With a $\lambda$ of 1, a $k$ of 3 is possible. 
Is 3 rides next month very likely if they didn't get even one ride last month?
The problem is aggravated three months down the line where this value has been resampled into a 7. 
Yes, we could add an additional stipulation to prevent us from making fanciful projections with this group.
"Inactive users shall not be forecasted to suddenly use the app 3 times or more in the subsequent month."
But then, what's the difference between this and a coin toss?
We practically end up in the same situation as 2 above.
So the perfected form of the first option is the second option.
QED.
