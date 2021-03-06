---
title: "Local Estimation"
author: "Jon Lefcheck"
date: "March 15, 2019"
output: html_document
---

# Local Estimation

## Global vs. local estimation

In the previous chapter, we explored the use of structural equation modeling to estimate relationships among a network of variables based on attempts to reproduce a single variance-covariance matrix. We refer to this approach as *global estimation* because the variance-covariance matrix captures relationships among *all* variables in the model at once.  

This approach comes with a number of assumptions about the data, notably that they are multivariate normal and  sufficiently replicated to generate unbiased parameter estimates. However, most data--particularly ecological data--violate these assumptions, and given the difficulty with which they are collected and the complexity of the proposed relationships, often lead to issues with power and identifiability. 

While variance-covariance based methods have been extended to consider special cases such as non-normality, an alternate estimation procedure was proposed in 2000 by Shipley based on concepts from graph theory. In this method, relationships for each endogenous (response) variable are estimated separately, which is why we call it *local estimation* or  *piecewise SEM* due to the nature by which the model is pieced together. 

Recall that global estimation assumes linear relationships, and indeed we have seen in the previous chapter that fitting an SEM and comparing the output with that from a linear model can yield the same results. Local estimation takes the latter approach: fitting a linear model for each response and then stringing together the inferences, rather than trying to estimate all relationships at once. Thus, piecewise SEM is more like putting together a puzzle than admiring the photo on the box.

This approach imparts great flexibility because the assumptions pertaining to each response can be evaluated and addressed individually, rather than treating every variable as arising from the same data-generating process. For example, generalized linear models can be fit for data that are non-Gaussian such as count (e.g., abundance), proportion (e.g., survival), or binary outcomes (e.g., presence-absence). Mixed-effects or hierarchical models can be fit for data that are nested or adhere to some predefined structure. Similarly, non-independence (such as spatial, temporal, or phylogenetic) can be incorporated into the model structure to provide more robust parameter estimates. Moreover, only enough data is needed to be able to fit and estimate each individual regression. In doing so, Shipley's method relaxes many of the assumptions associated with global estimation and better reflects the types and quantity of data collected by modern ecologists.

A key point to be made is that piecewise approach does not absolve the user of all assumptions. The data must still meet the assumption of the individual tests: for example, most linear regression requires constant variance and independence of errors. Such assumptions still hold, but can be easily evaluated using the suite of tools already available for said models (e.g., histograms of residuals plots, Q-Q plots, etc.).

However, recall that the goodness-of-fit measures for variance-covariance based structural equation models largely derive from comparison of the observed vs. estimated variance-covariance matrix. Because local estimation produces a separate variance-covariance matrix for each modeled response, there is no clear extension from global methods. Instead, Shipley proposed a new test based on *directed acyclic graphs* (or DAGs). 

DAGs are the pictorial representation of the hypothesized causal relationships: in other words, the path diagram. It's important to point out quickly that DAGs assume *recursive* relationships, or the absence of feedback loops or bidirectional relationships. Thus, local estimation is unsuitable for such approaches and one must resort to a global approach (with some additional conditions for such model structures).

There is a rich literature pertaining to DAGs, principally in their estimation and evaluation, and Shipley has drawn on this to propose a new index of model fit.

## Tests of directed separation

In global estimation, comparison of the observed vs. estimated variance-covariance matrices through the $\chi^2$ statistic asks whether the model-implied relationships deviate substantially from the relationships present in the data. If not, then the model is assumed to fit well, and we can go on to use it for inference.

Another way of thinking about model fit is to ask: are we missing anything? Recall that structural equation modeling requires careful specification of a hypothesized structure. In the case of underidentified models (those where there are more pieces of known information than parameters to be estimated), this means there are missing relationships that could be present but were not included. Paths might be excluded because there is no *a priori* reason or mechanism to suspect a causal relationship. 

The *tests of directed separation* evaluate this hypothesis: that we are justified in excluding relationships. This question is actually implicit in the $\chi^2$ statistic: a substantial deviation from the observed correlations suggests that we're missing information in our model that could bring in our estimates more in line with our observations. The tests of directed separation take this one step further by explicitly identifying and testing whether each piece of missing information (i.e., each missing path) could indeed change our interpretation of the overall model.

Two variables are *d-separated* if they are statistically independent conditional on their joint influences. Let's unpack this statement. First, 'two variables.' The two variables are *unrelated* in the hypothesized causal model: in other words, there is not a directed path already connecting them. Second, 'statistically independent.' We test for statistical dependence in our model all the time: the *P*-values associated with the path coefficients, for example, test whether the effect is significantly different than zero. Statistical *independence* then asks whether the two variables are significantly *unrelated*, or that that their relationship is in fact no diffeent than zero. Finally, 'conditional on their joint influences' means that the test for statistical independence must account for contributions from any other influences. In other words, the test must consider the *partial* effect of one variable on the other if either or both are already influenced by other variables in the model.

Procedurally, this evaluation is quite easy: identify missing paths, test whether the effect is not significantly different from zero (*P* > 0.05), and combine those inferences to gauge the overall trustworthiness of the model. But there is some background to cover first.

Let's consider a simple path diagram:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/global_estimation_model1.png)

In this case, we have specified two sets of directed relationships: $x1 -> y1$ and $y1 -> y2$.

If we apply the t-rule from the chapter on global estimation, we have $3(3+1)/2$ or 6 pieces of known information (the variances on the 3 variables + the 3 sets of covariances). We want to estimate the 2 parameters $\gamma_{x1y1}$ and $\beta_{y1y2}$ and the variances on the 3 variables (we can get their covariances from that). Thus we have 6 known values to estimate 5 unknown values, and the model is *underidentified*. We noted in the chapter on global estimation that the number of leftover known values can be used as degrees of freedom in the $\chi^2$ goodness-of-fit test. In this case, there is 1 degree of freedom, so likewise, we can go on to test model fit.

This 1 degree of freedom actually corresponds to the missing relationship between $x1 -> y2$. This is the *independence claim* we wish to test: that there is indeed no relationship between $x1$ and $y2$. However, the effect of $x1$ on $y2$ must be independent (or the partial effect) or the known influence of $y1$. Thus, we are testing the partial effect of $x1$ on $y2$ given $y1$. You may see this claim written in the following notation: $x1 | y2 (y1)$ where the bar separates the two variables in the claim, and any conditioning variables follow in parantheses. (Shipley actually puts the two variables in parentheses followed by the conditioning variables in brackets: $(x1, y2) | {y1}$, for the record.)

In this simple example, there is one conditioning variable for the single independence claim. This one independence claim constitutes what is called the *basis set* which is the minimum number of independence claims derived from a path diagram. The key word is *minimum*. 

Yet, we could have just as easily tested the claim $y2 | x1 (y1)$, which is the same relationship but in the opposite direction. However, the statistical test associated with this relationship is the same regardless of the direction. In other words, the partial effect of $x1$ on $y2$ is the same as $y2$ on $x1$ (although there is a caveat to this claim, which we will address later). In such a case, we would include only the one claim, rather than both claims that provide the same information. _Our first rule of directed separation is: the sum number of independence claims in the basis set cannot be derived from some combination of the others within it._

As an aside, if we add this claim back into the model, we would have no missing paths and thus there would be no independence claims or tests of directed separation possible. As is the case with $\chi^2$, we would not have any leftover information with which to test model fit.

As path diagrams become more complex, the natural question is: how far back do you go in terms of conditioning? Take the following example:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/local_estimation_model1.png)

There are several missing paths: $x1 -> y2$, $x1 -> y3$ and $y1 -> y3$.

Let's consider the independence claim $x1 -> y3$. Based on our last example, $y2$ must be included as a conditioning variable due to its direct influence on $y3$, but what about $y1$? It has an indirect influence on $y3$ through $y2$. However, by having included $y2$ in the independence claim, we have already (theoretically) incorporated the indirect influence of $y1$ through it. In other words, any effect of $y1$ would change $y2$ before $y3$, and the variance in $y2$ is already considered in the independence claim. So the full claim would be: $x1 | y3 (y2)$. 

_Our second rule is:  conditioning variables consist of only those variables *immediately ancestral* to the two variables whose independence is being evaluated._ In other words, we assume that the effects of any other downstream variables are captured in the variance contributed by the immediate ancestors, and we can therefore ignore them. Upstream variables (those occuring later in the path diagram) are never considered as conditioning variables, for the obvious reason that they have no effect on the preceding variables.

For the claim $y1 -> y3$ above, there are now two conditioning variables: $y2$ (on $y3$) and also $x1$ (on $y1$). So the final independence claim would be: $y1 | y2 (x1, y1)$.

The full basis set for this diagram would then be:

* $x1 | y3 (y2)$
* $y1 | y3 (y2, x1)$
* $x1 | y2 (y1)$

Deriving the basis set can be difficult but mercifully is automated in the `piecewiseSEM` package. This package makes some choices about the basis set that deviate from the recommendations of Shipley. For example, consider the following path diagram:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/local_estimation_model2.png)

The basis set includes the unspecified paths from $x1 | y2 (y1)$ and $x2 | y2 (y1)$. But what about $x1 | x2$?

Shipley would include this claim in the basis set. However, several argument could be made against it along several fronts.

First, unlike $y2$ which very clearly is an effect (i.e., has a directed path flowing into it), there is no expectation of a cause-effect relationship between the two exogenous variables $x1$ and $x2$. In fact, such a relationship may yield nonsensical claims (e.g., between butterfly color and number of train stations) or where directionality is confounded in one direction (e.g., latitude and species richness). If the purpose of the test is to evaluate linkages that were originally deemed irrelevant, is it really that useful to test non-mechanistic or impossible links? If we did indeed recover a significant correlation between butterfly color and train stations, is that mechanistically interesting or (more likely) totally spurious? And should we therefore reject a model due to a totally spurious relationship? These are tough questions with no clear answer. From a personal perspective, I believe the tests of directed separation should be diagnostic: should I have included this path? Did it provide useful information? Including non-informative claims because they can be evaluated simply inflates the test statistic with no real benefit to the identifying underlying causal processes.

Second, and more practically, there is no easy way for the user to specify the distributional and other assumptions associated with exogenous variables in the same way they can for endogenous variables. By virtue of modeling $y2$ in a directed path (from $y1$), it is clear how that response should be treateed by the way the model is oded. However, no where in the regression models is there information on how $x1$ should be treated: is it binomial? Hierarchical? Polynomial? Asking the user to code this information would vastly inflate the amount of code necessary to run tests, and combined with the above, would yield little insight for a potentially very large investment.

Nevertheless, independence claims could be added back into the basis set if the user decides they disagree with this perspective.

Now that we are comfortable identifying missing paths and constructing the basis set, the next step is to test them for statistical independence. This can be done by taking the response as it is treated in the original model, and swapping the predictors with those in the independence claim. The way, the assumptions of the endogenous variable are preserved in any further evaluations. So, for example, if $y3$ in the previous path model is binomally-distributed as a function of $y2$, then any independence claims involving $y2$ would also treat is as binomial. 

Once the model is fit, statistical independence is assessed with a t-, F-, or other test. If the resulting *P*-value is >0.05, then we fail to reject the null hypothesis that the two variables are conditionally independent. In this case, a high *P*-value is a *good* thing: it indicates that we were justified in excluding that relationship from our path diagram in the first place, because the data don't support a strong linkage between those variables within some tolerable threshold of error.

Shipley's most important contribution was to shose *P*-values can be used to construct a fit index analogous to the $\chi^2$ statistic from global estimation: Fisher's *C* statistic.

Fisher's *C* is calculated as:

$$C = -2{\sum_{i=1}^k ln(p_i)}$$

where $k$ is the number of independence claims in the basis set, *i* is the *i*th claim, and *p* is the *P*-value from the corresponding significance test.

Furthermore, Shipley showed that *C* is $\chi^2$ distributed with 2$k$ degrees of freedom. Thus, a model-wide *P*-value can be obtained by comparing the value to a $\chi^2$ table with the appropriate degrees of freedom.

As with the $\chi^2$ test in global estimation, a model-wide *P* > 0.05 is desirable because it implies that a the hypothesized structure is supported by the data. In other words, no potentially significant missing paths were excluded.

Like the $\chi^2$ difference test, the *C* statistic can be used to compare nested models. Shipley later showed that the the *C* statistic can also be used to compute an AIC score for the SEM:

  $$AIC = C + 2K$$
  
where $K$ is the likelihood degrees of freedom (not $k$, the number of claims in the basis set). A further variant for small sample sizes, $AIC_c$, can be obtained by adding an additional penalty:

  $$AIC_c = C + 2K\frac{n}{(n - K - 1)}$$

It's important to point out that, like the $\chi^2$ statistic for global estimation, the *C* statistic can be affected by sample size, but not in as a direct way. As sample size increases, the probability of recovering a "significant" *P*-vaue increases, reducing the potential for a good-fitting model. Similarly, more complex models may lead to a kind of "overfitting" where significant d-sep tests are obscured by many more non-significant values leading to strong support for the (potentially incorrect) model structure. Paradoxically, poor sample size can also lead to a good-fitting model because the tests lack the power to detect an actual effect (high Type II error). Such biases should be considered when reporting the results of the test.

In this way, the tests of directed separation may be usefully diagnostic by drawing attention to the specific relationships that could be re-inserted into the model, which would have the added benefit of improving model fit (by removing those significant *P*-values from the basis set). Whether this is advisable depends on the goal of the exercise: in an "exploratory" mode, for example, adding paths might be useful *if* they are theoretically justifiable. Blindly selecting all the significant tests and re-inserting those paths, however, is irresponsible and in fact antithetical to the philsophy of SEM (where paths are carefully specified by the user).

Like $\chi^2$ and other fit indices from global estimation, just identified models have no degrees of freedom (missing paths) left over with which to test model fit. As in those cases, we can turn to other diagnostic tests, such as individual model $R^2$ to give us a sense of confidence in the model structure. If a high proportion of variance is explained in all endogenous variable and there are significant path coefficients, it follows that residual error is low, and it's safe to assume that there are no other variables out there that can further clarify the model structure.

## Model fitting using *piecewiseSEM*

Fitting a piecewise structural equation model is as simple as fitting each regression separately: if you can fit an `lm` in R, you can fit an SEM!

The package of course is *piecewiseSEM*:


```r
library(piecewiseSEM)
```

And let's return to the data from Grace & Keeley (2006)


```r
data(keeley)
```

As a reminder, Grace & Keeley wanted to understand patterns in plant diversity following disturbance, in this case wildfires in California.

In the end of the global estimation chapter, we tested for full mediation using the following model:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/global_estimation_keeley_sem2.png)

As in *lavaan*, it's first necessary to break down the component models. Unlike *lavaan* these are not coded as character strings, but instead as full-fledged linear models. Moreover, they are put together using the function `psem` which is the primary workhouse of the *piecewiseSEM* package.


```r
keeley_psem <- psem(
  lm(firesev ~ age, data = keeley),
  lm(cover ~ firesev, data = keeley),
  data = keeley)
```

It's not necessary to pass a `data` argument to `psem` but it can help alleviate errors in certain cases.

Before we get to the model fitting, let's just examine the `psem` object:

```r
keeley_psem
```

```
## Structural Equations of x :
## lm: firesev ~ age
## lm: cover ~ firesev
## 
## Data:
##   distance elev  abiotic age   hetero firesev     cover rich
## 1 53.40900 1225 60.67103  40 0.757065    3.50 1.0387974   51
## 2 37.03745   60 40.94291  25 0.491340    4.05 0.4775924   31
## 3 53.69565  200 50.98805  15 0.844485    2.60 0.9489357   71
## 4 53.69565  200 61.15633  15 0.690847    2.90 1.1949002   64
## 5 51.95985  970 46.66807  23 0.545628    4.30 1.2981890   68
## 6 51.95985  970 39.82357  24 0.652895    4.00 1.1734866   34
## ...with  84  more rows
## 
## [1] "class(psem)"
```

It returns the component models, their model classes, and a snippet of the data.

The first step is to derive the basis set using the function `basisSet`:


```r
basisSet(keeley_psem)
```

```
## $`1`
## [1] "age _||_ cover | firesev"
```

Here, there is a single independence claim representing the missing path from $age -> cover$ conditional on the influence of $firesev$ on $cover$.

Now to evaluate the tests of directed separation using the function `dSep`:


```r
dSep(keeley_psem, .progressBar = FALSE)
```

```
##      Independ.Claim Test.Type DF Crit.Value    P.Value 
## 1 cover ~ age + ...      coef 87   -1.80184 0.07503437
```

Note that the output is the same as if we evaluated the independence claim ourselves:


```r
summary(lm(cover ~ firesev + age, data = keeley))$coefficients
```

```
##                 Estimate  Std. Error   t value     Pr(>|t|)
## (Intercept)  1.121762237 0.092029731 12.189129 1.620652e-20
## firesev     -0.067243722 0.020398729 -3.296466 1.418463e-03
## age         -0.004832969 0.002682241 -1.801840 7.503437e-02
```

Now, we could compute the Fisher's *C* statistic from the d-sep test with 2 degrees of freedom and compare it to a $\chi^2$ distribution to get a model-wide *P*-value:


```r
(C <- -2 * log(summary(lm(cover ~ firesev + age, data = keeley))$coefficients[3, 4]))
```

```
## [1] 5.179618
```

```r
1-pchisq(C, 2)
```

```
## [1] 0.07503437
```

Or, we could just use the function `fisherC`:


```r
fisherC(keeley_psem)
```

```
##   Fisher.C df P.Value
## 1     5.18  2   0.075
```

It seems in this case, as with the example for global estimation, we would fail to reject the partial mediation model with the directed path from $age -> cover$ and instead refer to the full mediation model. Also as previously, we can perform a Chi-square difference test to validate this conclusion empirically.

Let's create the partial mediation model and compare the two:


```r
keeley_psem2 <- psem(
  lm(cover ~ firesev + age, data = keeley),
  lm(firesev ~ age, data = keeley),
  data = keeley
)

anova(keeley_psem, keeley_psem2)
```

```
## Chi-square Difference Test
## 
##        AIC    BIC Fisher.C Fisher.C.Diff DF.diff P.value 
## 1    17.18 32.179     5.18                               
## vs 2 14.00 31.499     0.00          5.18       2   0.075
```

It seems that, based on the output of the Chi-square difference test, the two models are not significantly different and thus parsimony would tend towards the model with fewer estimatde parameters (the full mediation model).

Note that the output also computes AIC and BIC scores: these can be obtained with the `AIC` and `BIC` functions as well:


```r
AIC(keeley_psem)
```

```
## [1] 17.18
```

```r
BIC(keeley_psem)
```

```
## [1] 32.179
```

A note of caution: as for the $\chi^2$ statistic in global estimation, a fully saturated or just identified model will yield a *C* statistic of 0. Based on Shipley's equation above, the AIC score reduces to $2K$, or twice the likelihood degrees of freedom. This is in contrast to global estimation, which apparently employs a different calculation of AIC because the same model fit in *lavaan* produces a different (and lower) AIC score. Until the differences are reconciled, we advise caution in applying AIC to saturated models with 0 degrees of freedom.

This exercise was a long workaround to reveal that all the above can be executed simultaneously using the `summary` function:


```r
summary(keeley_psem, .progressBar = FALSE)
```

```
## 
## Structural Equation Model of keeley_psem 
## 
## Call:
##   firesev ~ age
##   cover ~ firesev
## 
##     AIC      BIC
##  17.180   32.179
## 
## ---
## Tests of directed separation:
## 
##      Independ.Claim Test.Type DF Crit.Value P.Value 
##   cover ~ age + ...      coef 87    -1.8018   0.075 
## 
## Global goodness-of-fit:
## 
##   Fisher's C = 5.18 with P-value = 0.075 and on 2 degrees of freedom
## 
## ---
## Coefficients:
## 
##   Response Predictor Estimate Std.Error DF Crit.Value P.Value Std.Estimate
##    firesev       age   0.0597    0.0125 88     4.7781       0       0.4539
##      cover   firesev  -0.0839    0.0184 88    -4.5594       0      -0.4371
##      
##   ***
##   ***
## 
##   Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05
## 
## Individual R-squared:
## 
##   Response method R.squared
##    firesev   none      0.21
##      cover   none      0.19
```

The output should look very familar to the output from other summary calls, like `summary.lm`. The d-sep test, Fisher's C and information criteria are all reported.

Additionally, model coefficients are returned. Unlike *lavaan*, the standardized estimates are provided by default. Also unlike *lavaan*, the individual model $R^2$ values are also returned by default. Both sets of statistics are key for inference, and thus we have decided to make them available with any further arguments passed to `summary`.

We can compare the *piecewiseSEM* output to the *lavaan* output:


```r
library(lavaan)

sem1 <- '
firesev ~ age
cover ~ firesev
'

keeley_sem1 <- sem(sem1, keeley)

summary(keeley_sem1, standardize = T, rsq = T)
```

```
## lavaan 0.6-3 ended normally after 19 iterations
## 
##   Optimization method                           NLMINB
##   Number of free parameters                          4
## 
##   Number of observations                            90
## 
##   Estimator                                         ML
##   Model Fit Test Statistic                       3.297
##   Degrees of freedom                                 1
##   P-value (Chi-square)                           0.069
## 
## Parameter Estimates:
## 
##   Information                                 Expected
##   Information saturated (h1) model          Structured
##   Standard Errors                             Standard
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
##   firesev ~                                                             
##     age               0.060    0.012    4.832    0.000    0.060    0.454
##   cover ~                                                               
##     firesev          -0.084    0.018   -4.611    0.000   -0.084   -0.437
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
##    .firesev           2.144    0.320    6.708    0.000    2.144    0.794
##    .cover             0.081    0.012    6.708    0.000    0.081    0.809
## 
## R-Square:
##                    Estimate
##     firesev           0.206
##     cover             0.191
```

Note that we get almost all the same statistics, except for the model-wide *P*-value, which comes down to differences in the ML estimation procedure for *lavaan*. Inferentially, however, the two models are identical.

Of course, we might expect greater divergence between the two methods as *piecewiseSEM* begins to incorporate more about the distributional and other assumptions about the endogenous variables.

## Shipley (2009): A Worked Example

Let's turn to the example from Shipley (2009) on tree survival. In this (hypothetical) study, individual trees are followed for 36 years at 20 sites and measured for date of bud burst (Date), cumulative degree days until first bud burst (DD), growth, and survival.

It's important to note that these data have multiple levels of hierarchical structure: between sites, between individuals within sites, between years within individuals within sites. They also have non-normal responses: survival is measured as a binary outcome (alive or dead).

Shipley hypothesized these variables are related in the following way:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/local_estimation_shipley_sem.png)

Let's first treat the data as normal and independent using *lavaan*:


```r
shipley_model <- '
DD ~ lat
Date ~ DD
Growth ~ Date
Live ~ Growth
'

shipley_sem <- sem(shipley_model, shipley)

summary(shipley_sem, standardize = T, rsq = T)
```

```
## lavaan 0.6-3 ended normally after 27 iterations
## 
##   Optimization method                           NLMINB
##   Number of free parameters                          8
## 
##                                                   Used       Total
##   Number of observations                          1431        1900
## 
##   Estimator                                         ML
##   Model Fit Test Statistic                      38.433
##   Degrees of freedom                                 6
##   P-value (Chi-square)                           0.000
## 
## Parameter Estimates:
## 
##   Information                                 Expected
##   Information saturated (h1) model          Structured
##   Standard Errors                             Standard
## 
## Regressions:
##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
##   DD ~                                                                  
##     lat              -0.860    0.023  -37.923    0.000   -0.860   -0.708
##   Date ~                                                                
##     DD               -0.517    0.016  -32.525    0.000   -0.517   -0.652
##   Growth ~                                                              
##     Date              0.173    0.020    8.508    0.000    0.173    0.219
##   Live ~                                                                
##     Growth            0.006    0.001    9.854    0.000    0.006    0.252
## 
## Variances:
##                    Estimate  Std.Err  z-value  P(>|z|)   Std.lv  Std.all
##    .DD               52.628    1.967   26.749    0.000   52.628    0.499
##    .Date             38.080    1.424   26.749    0.000   38.080    0.575
##    .Growth           38.981    1.457   26.749    0.000   38.981    0.952
##    .Live              0.025    0.001   26.749    0.000    0.025    0.936
## 
## R-Square:
##                    Estimate
##     DD                0.501
##     Date              0.425
##     Growth            0.048
##     Live              0.064
```

Firstly, we notice the goodness-of-fit can be estimated, but the model is a poor fit (*P* < 0.001). The paths are all significant but this doesn't do us much good considering the model is not suitable for inference. 

Instead of fiddling with modification indices and trying to rejigger the model strcuture, let's analyze the same path diagram using a piecewise approach and recognizing both the hierarchical structure AND non-normality of the data:

```r
library(nlme)
library(lme4)

shipley_psem <- psem(

  lme(DD ~ lat, random = ~ 1 | site / tree, na.action = na.omit,
  data = shipley),

  lme(Date ~ DD, random = ~ 1 | site / tree, na.action = na.omit,
  data = shipley),

  lme(Growth ~ Date, random = ~ 1 | site / tree, na.action = na.omit,
  data = shipley),

  glmer(Live ~ Growth + (1 | site) + (1 | tree),
  family = binomial(link = "logit"), data = shipley)

  )

summary(shipley_psem, .progressBar = FALSE)
```

```
## 
## Structural Equation Model of shipley_psem 
## 
## Call:
##   DD ~ lat
##   Date ~ DD
##   Growth ~ Date
##   Live ~ Growth
## 
##     AIC      BIC
##  49.536   149.592
## 
## ---
## Tests of directed separation:
## 
##       Independ.Claim Test.Type   DF Crit.Value P.Value 
##     Date ~ lat + ...      coef   18    -0.0798  0.9373 
##   Growth ~ lat + ...      coef   18    -0.8929  0.3837 
##     Live ~ lat + ...      coef 1431     1.0280  0.3039 
##    Growth ~ DD + ...      coef 1329    -0.2967  0.7667 
##      Live ~ DD + ...      coef 1431     1.0046  0.3151 
##    Live ~ Date + ...      coef 1431    -1.5617  0.1184 
## 
## Global goodness-of-fit:
## 
##   Fisher's C = 11.536 with P-value = 0.484 and on 12 degrees of freedom
## 
## ---
## Coefficients:
## 
##   Response Predictor Estimate Std.Error   DF Crit.Value P.Value
##         DD       lat  -0.8355    0.1194   18    -6.9960       0
##       Date        DD  -0.4976    0.0049 1330  -100.8757       0
##     Growth      Date   0.3007    0.0266 1330    11.2917       0
##       Live    Growth   0.3479    0.0584 1431     5.9552       0
##   Std.Estimate    
##        -0.6877 ***
##        -0.6281 ***
##         0.3824 ***
##              - ***
## 
##   Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05
## 
## Individual R-squared:
## 
##   Response method Marginal Conditional
##         DD   none     0.49        0.70
##       Date   none     0.41        0.98
##     Growth   none     0.11        0.84
##       Live  delta     0.16        0.18
```

(In the new version of *lme4* we get some warnings in the d-sep tests, but the model still return an output.)

The immediately obvious difference is that the model is no longer a poor fit: we have 12 degrees of freedom corresponding to 6 indepdence claims, all of which have *P* > 0.05. Therefore, the model-wide *P* = 0.484, and we would therefore reject the null that the data do not support the hypothesized model structure.

Moreover, while the direction of the parameter estimates remain the same, they vary considerably in their magnitudes (e.g., $\beta_{date, DD} = -0.652$ for *lavaan* and $\beta_{date, DD} = -0.497$ in *piecewiseSEM*).

The model $R^2$s are all higher as well, for fixed-effects only (marginal) and especially for fixed- and random-effects together (conditional).

Thus, by addressing the non-independence of the data, we have converged on support for the hypothesized model structure, more accurate parameter estimates, and a higher proportion of explained variance than was possible using *lavaan*.

## A Special Case: Where Graph Theory Fails

In the majority of cases, as we have established, the direction of the independence claim doesn't matter because, while the coefficients will differ, their *P*-values will be identical. Thus it doesn't matter if you test $y | x$ or $x | y$ because the claim will yield the same significance test. EXCEPT when intermediate endogenous variables are non-normally distributed. 

Consider the following SEM:

![](https://raw.githubusercontent.com/jslefche/sem_book/master/img/local_estimation_glm_sem.png)

In this SEM, there are two independence claims:

* $y3 | x1 (y1, y2)$
* $y2 | y1 (x1)$

In the second independence claim, if both variables were normally distributed, the significance value is the same whether the test is conducted as $y2 | y1 (x1)$ or $y1 | y2 (x1)$. This is NOT true, however, when one or both of the responses are fit to a non-normal distribution. This is because the response is now transformed via a *link function* $g(\mu)$ (see chapter on coefficients), and the parameter estimates--and their standard errors--are now expressed on the link scale. This transformation means the *P*-value obtained by regressing $y1 ~ y2$ is NOT the same as the one obtained by regressing $y2 ~ y1$.

To show this is true, let's generate some Poisson-distributed data and model using both LM and GLM with a log-link:


```r
set.seed(87)

glmdat <- data.frame(x1 = runif(50), y1 = rpois(50, 10), y2 = rpois(50, 50), y3 = runif(50))

# LM
summary(lm(y1 ~ y2 + x1, glmdat))$coefficients[2, 4]
```

```
## [1] 0.03377718
```

```r
summary(lm(y2 ~ y1 + x1, glmdat))$coefficients[2, 4]
```

```
## [1] 0.03377718
```

```r
# GLM
summary(glm(y1 ~ y2 + x1, "poisson", glmdat))$coefficients[2, 4] 
```

```
## [1] 0.03479666
```

```r
summary(glm(y2 ~ y1 + x1, "poisson", glmdat))$coefficients[2, 4]
```

```
## [1] 0.08586767
```
In the case of `lm` the *P*-value is identical regardless of the direction, and moreover is < 0.05, thus--depending on the outcome of the other claim--we might reject the model.

In contrast, when $y1$ and $y2$ are modeled as Poisson-distributed, the *P*-value is alternatingly < and >= 0.05. Thus, depending on how the claim is specified, we might or might not reject the model. A big difference!

`piecewiseSEM` solves this by providing three options to the user. 

(1) We can specify the directionality of the test if, for instance, it makes greater biological sense to test $y1$ against $y2$ instead of the reverse (for example abundance drives species richness, not vice versa); or

(2) We can remove that path from the basis set and instead specify it as a correlated error using `%~~%`. This circumvents the issue altogether but it may not make sense to assume both variables are generated by some underlying process; or

(3) We can conduct *both* tests and choose the most conservative (i.e., lowest) P**-value.

These options are returned by `summary` in the event the above scenario is identified in the SEM:

```r
glmsem <- psem(
  glm(y1 ~ x1, "poisson", glmdat),
  glm(y2 ~ x1, "poisson", glmdat),
  lm(y3 ~ y1 + y2, glmdat)
)

summary(glmsem)
```

```
## Error: 
## Non-linearities detected in the basis set where P-values are not symmetrical. 
## This can bias the outcome of the tests of directed separation.
##  
## Offending independence claims: 
##  y2 <- y1 *OR* y2 -> y1 
##  
## Option 1: Specify directionality using argument 'direction = c()' in 'summary'.
##  
## Option 2: Remove path from the basis set by specifying as a correlated error using '%~~%' in 'psem'.
##  
## Option 3 (recommended): Use argument 'conserve = TRUE' in 'summary' to compute both tests, and return the most conservative P-value.
```
In option 1, the directionality can be specified using `direction = c()` as an additional argument to `summary`.


```r
summary(glmsem, direction = c("y1 <- y2"), .progressBar = F)$dTable
```

```
##   Independ.Claim Test.Type DF Crit.Value P.Value 
## 1  y3 ~ x1 + ...      coef 46    -0.7187  0.4760 
## 2  y2 ~ y1 + ...      coef 47     1.7176  0.0859
```

In option 2, the SEM can be updated to remove that test by specifying it as a correlated error.


```r
summary(update(glmsem, y1 %~~% y2), .progressBar = F)
```

```
## 
## Structural Equation Model of update(glmsem, y1 %~~% y2) 
## 
## Call:
##   y1 ~ x1
##   y2 ~ x1
##   y3 ~ y1 + y2
##   y1 ~~ y2
## 
##     AIC      BIC
##  17.485   32.781
## 
## ---
## Tests of directed separation:
## 
##   Independ.Claim Test.Type DF Crit.Value P.Value 
##    y3 ~ x1 + ...      coef 46    -0.7187   0.476 
## 
## Global goodness-of-fit:
## 
##   Fisher's C = 1.485 with P-value = 0.476 and on 2 degrees of freedom
## 
## ---
## Coefficients:
## 
##   Response Predictor Estimate Std.Error DF Crit.Value P.Value Std.Estimate
##         y1        x1  -0.1007    0.1573 48    -0.6402  0.5221            -
##         y2        x1   0.0252    0.0737 48     0.3423  0.7322            -
##         y3        y1  -0.0160    0.0128 47    -1.2511  0.2171       -0.183
##         y3        y2   0.0144    0.0074 47     1.9416  0.0582       0.2839
##       ~~y1      ~~y2   0.3155         - 50     2.2792  0.0136       0.3155
##    
##    
##    
##    
##    
##   *
## 
##   Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05
## 
## Individual R-squared:
## 
##   Response     method R.squared
##         y1 nagelkerke      0.01
##         y2 nagelkerke      0.00
##         y3       none      0.08
```

Note that the claim no longer appears in the section for the tests of directed separation.

Finally, option 3 can be invoked by specifying `conserve = T` as an additional argument


```r
summary(glmsem, conserve = T, .progressBar = F)$dTable
```

```
##   Independ.Claim Test.Type DF Crit.Value P.Value  
## 1  y3 ~ x1 + ...      coef 46    -0.7187  0.4760  
## 3  y1 ~ y2 + ...      coef 47     2.1107  0.0348 *
```

The user should be vigilant for these kinds of situations and ensure that both the specified paths AND the independence claims all make biological sense. In the case where the underlying assumptions of the d-sep tests can bias the goodness-of-fit statistic, `piecewiseSEM` should automatically alert the user and suggest solutions.

## References

Shipley, Bill. "A new inferential test for path models based on directed acyclic graphs." Structural Equation Modeling 7.2 (2000): 206-218.

Shipley, Bill. "Confirmatory path analysis in a generalized multilevel context." Ecology 90.2 (2009): 363-368.

Shipley, Bill. "The AIC model selection method applied to path analytic models compared using ad‐separation test." Ecology 94.3 (2013): 560-564.

Lefcheck, Jonathan S. "piecewiseSEM: Piecewise structural equation modelling in r for ecology, evolution, and systematics." Methods in Ecology and Evolution 7.5 (2016): 573-579.
