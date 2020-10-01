---
title: "R learning cheat sheet"
published: true
---


# R for data science
## Some useful learning sourses:
**Statistical Methods for Data Science** by ***Elizabeth Purdom***
https://epurdom.github.io/Stat131A/book/

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


1.The first part consists of ordinary covariates

2.The second part consists of factors to be projected out.
 
3.The third part is an IV-specification. 

4.The fourth part is a cluster specification for the standard errors. 


i.e. something like ```felm(“Dependent variable”~”Independent variable for Statistical Inference” | “Variables just for Control")```
```y ~ x1 + x2 | f1 + f2 | (Q|W ~ x3+x4) | clu1 + clu2 ``` where y is the ***response/dependent variable***, x1,x2 are ordinary ***covariates/independent variable*** (seperated by ```+``` signs), f1,f2 are factors to be projected out, Q and W are covariates which are instrumented by x3 and x4, and clu1,clu2 are factors to be used for computing cluster robust standard errors.

Parts that are not used should be specified as ***0***, except if it's at the end of the formula, where they can be omitted. The parentheses are needed in the third part since | has higher precedence than ~. Multiple left hand sides like ```y|w|x ~ x1 + x2 |f1+f2|...``` are allowed.

### Problems

#### 1.memory error

```R
Error: cannot allocate vector of size 7.6 Gb
```

**Attempt 1:**

```R
memory.limit(FALSE/TRUE/number for MB) # check memory limit or increase memory limit
memory.size() # check current memory size
object.size() # check object size
gc() # clean workspace
ls() # check variables
```

***Note:*** Using memory.limit() to increase memory size cannot fully solve memory limitation problem even if we set a very large number.
The usable memory space is determined by the RAM space size (which is 8 GB for me).

Another way to increase memroy size, open Rstudio via terminal and use the following command:

```R
rstudio.exe -max-mem-size 40Gb
```

Since using felm needs to create the factor matrix and load it into RAM, we should be careful when dealing with large datasets. Also the matrix will take up very large space especially for the case when we have too many independent variables which have a high factor level(many categories). However the memory usage will be much lower if I take high
factor level variables as control variables.

#### 2.Error when using "clustered" standard errors

```R
Error in is_nested(z$fe[[fe_k]], cluster[[cl_g]]) : length(f1) == length(f2) is not TRUE
```

***Attempt 1***

1.Run a regression using either lm or felm “Dependent variable”~“Variables just for Control”

2.Obtain residuals of the regression above

3.Run a regression with cse that “residual of above regression” ~ ”Independent variable for Statistical Inference”

One problem is when I set the previous control variables to independent variables, the matrix created by felm() implicitly expands size greatly (fron 6.8 Gb to 96 Gb).

***Attempt 2***

mfatt.rnd<-felm(dt$attention~factor(dt$position) | dt$participant+dt$stimulus2)

From the model above, can get “residuals” + Intercept estimate, which can be the dependent variable for the following model:
	```[residuals (vector) + intercept (scalar) from mfatt.rnd above] ~ dt$pre_stimulus2```, with cse

***residual:*** vertical distance between a data point and the regression line.

One problem: felm does not provide explicit intercept, "it's implicitly captured in the factor structure" (according to https://journal.r-project.org/archive/2013-2/gaure.pdf)

Here I just take the residuals without adding the intercept