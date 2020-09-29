---
layout: post
title: "R learning cheat sheet"
published: true
---


# R for data science
## felm
felm is used to fit linear models with multiple group fixed effects, similarly to lm. It uses the Method of Alternating projections to sweep out multiple group effects from the normal equations before estimating the remaining coefficients with OLS.
```R
felm(
  formula,
  data,
  exactDOF = FALSE,
  subset,
  na.action,
  contrasts = NULL,
  weights = NULL,
  ...
)
```
The formula specification is a response variable followed by a ***four part*** formula. 

<ol>
<li>The first part consists of ordinary covariates
<li>The second part consists of factors to be projected out. 
<li>The third part is an IV-specification. 
<li>The fourth part is a cluster specification for the standard errors. 
</ol>
*i.e. something like y ~ x1 + x2 | f1 + f2 | (Q|W ~ x3+x4) | clu1 + clu2 where y is the response, x1,x2 are ordinary covariates, f1,f2 are factors to be projected out, Q and W are covariates which are instrumented by x3 and x4, and clu1,clu2 are factors to be used for computing cluster robust standard errors. *
Parts that are not used should be specified as **0**, except if it's at the end of the formula, where they can be omitted. The parentheses are needed in the third part since | has higher precedence than ~. Multiple left hand sides like y|w|x ~ x1 + x2 |f1+f2|... are allowed.
