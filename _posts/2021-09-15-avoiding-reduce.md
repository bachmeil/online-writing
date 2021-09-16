---
layout: post
title: "Avoiding Reduce in R Programs"
---

I'm a big advocate of functional programming in R. One reason is that my experience suggests it's *much* easier to teach students without a programming background how to write programs built on functional programming concepts than otherwise.

In spite of this, R's `Reduce` function can be difficult for students to comprehend. One solution is to write functions that cover important special cases where `Reduce` is appropriate.

One example: Suppose you want to compute the vector of errors in an MA(1) model. That's relatively simple as calls to `Reduce` go, but we can make it possible to do something like this:

```
errors <- errorCalc(y, f, 0, par)
```

`y` is the data, `f` is a function that calculates an error given an observation of `y`, a guess of the parameters of the model, and the vector of errors calculated up to that point. `errorCalc` would know what to do from there and would return the full error vector.

In the case of an MA(1) model, we have

$$y_{t} = \alpha + \rho \varepsilon_{t-1} + \varepsilon_{t}$$

The function `f` supplied by the user would look like this:

```
f <- function(theta, y, e) {
    return(y - theta[1] - theta[2]*last(e))
}
```

Since it's an MA(1) model, the user would only supply one initial value for the error vector. The inner workings of the `errorCalc` function would look something like this when y is a single ts:

```
errorCalc <- function(y, f, init, par) {
    result <- rep(NA, length(y)+length(init))
    result[1:length(init)] <- init
    for (ii in 1:length(y)) {
        result[ii] <- f(par, y[ii], result[1:(ii-1+length(init))])
    }
    return(result)
}
```

This is a simpler approach because `errorCalc` takes care of all of the iteration. The user needs only specify the data, choice of parameters, and calculation of one value of the error term. Typically, this is easy for non-programmers to do. It is the addition of iteration that is hard for them to do correctly.