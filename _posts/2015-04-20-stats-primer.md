---
layout: post
title: Statistical Analysis Primer
---

## {{ page.title }}

- - - -

# Table of Contents
  *  [Overview](#overview)
  *  [What kind of data are we measuring?](#whatdata)
      -  [Independent and Dependent Variables](#variables)
      -  [Categorical and Continuous Variables](#categoricalcontinuous)
      -  [Reliability](#reliability)
      -  [Validity](#validity)
  *  [How are we collecting the data?](#howdata)
      -  [Correlation and Causation](#correlationcausation)
      -  [Independent Subjects and Between Subjects](#correlationcausation)
  *  [Descriptive Statistics](#descriptivestatistics)
      -  [Frequency Distribution (Histogram)](#histogram)
      -  [Mode, Median, Mean](#modemedianmean)
      -  [Quantile, Quartile, Percentages](#quantilequartilepercentages)
      -  [Dispersion](#dispersion)
      -  [Probability](#probability)
      -  [Populations and Samples](#populationssamples)
  *  [Inferential Statistics](#inferentialstatistics)
      -  [Statistical Models](#models)
      -  [Null Hypothesis Significance Testing](#nhst)
      -  [Type I and Type II Errors](#typeerrors)
      -  [Parametric Statistics](#parametric)
      -  [Homogeneity and Heterogeneity of Variance](#geneityvariance)
  *  [Linear Regression](#linreg)
      -  [Data Transformation](#linregdatatransformation)
      -  [Covariance and Correlation](#linregcovariancecorrelation)
      -  [Regression Analysis](#linreganalysis)
      -  [Selecting Predictors](#linregpredictors)
      -  [Evaluating Model](#linregevaluatemodel)
      -  [Assumptions](#linregassumptions)
      -  [Violating Assumptions](#linregviolations)
  *  [Logistic Regression](#logreg)
      -  [Types of Logistic Regressions](#logregtypes)
      -  [Comparing Linear to Logistic Regression](#logregcompare)
      -  [Selecting Predictors](#logregpredictors)
      -  [Assumptions](#logregassumptions)
      -  [Issues](#logregissues)
  *  [T-test](#ttest)
      -  [Independent and Dependent Means t-test](#ttestinddep)
      -  [Calculations](#ttestcalcs)
  *  [One-way ANOVA](#onewayanova)
      -  [Concept](#onewayanovaconcept)
      -  [Assumptions](#onewayanovaassumptions)
      -  [Comparing Different Groups](#onewayanovacomparegroups)
      -  [Planned Contrasts](#onewayanovaplannedcontrasts)
      -  [Post Hoc Comparison](#onewayanovaposthoccomparison)
  *  [ANCOVA](#ancova)
      -  [Assumptions](#ancovaassumptions)
      -  [Calculations](#ancovacalculations)
  *  [Factorial ANOVA](#factorialanova)
      -  [ANOVA Naming Convention](#factorialanovanaming)
      -  [Independent Factorial Design ANOVA](#factorialanovaindependent)
      -  [Repeated-Measures Factorial Design ANOVA](#factorialanovarepeatedmeasures)
      -  [Mixed Designs ANOVA](#factorialanovamixeddesigns)
  *  [Non-parametric Tests](#nonparametric)
      -  [Wilcoxon Rank Sum Test](#wilcoxonranksum) 
      -  [Wilcoxon Signed-Rank Test](#wilcoxonsignedrank)
      -  [Kruskal-Wallis Test](#kruskalwallis)
      -  [Friedman's ANOVA](#friedmansanova)
  *  [MANOVA](#manova)
      -  [Matrices](#matrices)
      -  [When to use MANOVA](#manovauses)
      -  [Variances and Covariances of MANOVA](#manovavariancecovariance)
      -  [Predicting](#manovapredicting)
      -  [Eigenvalues](#manovaeigenvalues)
      -  [Assumptions](#manovaassumptions)
      -  [Which group caused effect](#manovawhichgroupeffect)
      -  [Robust Methods](#manovarobust)
  *  [Factor Analysis](#factoranalysis)
      -  [Calculating Factor Scores](#factoranalysisfactorscores)
      -  [What to do with Factor Scores](#factoranalysiswhattodo)
      -  [Factor Extraction](#factoranalysisfactorextraction)
      -  [Factor Analysis Interpretation](#factoranalysisinterpretation)
  *  [Categorical Variables](#comparecategorical)
      -  [Pearson's Chi-Square Statistic](#comparecategoricalpearsonchisquare)
  *  [Loglinear Analysis](#loglinearanalysis)
  *  [Multilevel Linear Models](#multilevellinear)


## Overview <a id="overview"></a>
Welcome!  This is a short onboarding course for new data analysts.  I'll cover the basics of statistics.  No other knowledge is necessary.

## What data are we measuring? <a id="whatdata"></a>

#### Independent and Dependent Variables <a id="variables"></a>
Data is made up of variables.  Variables can have any value (like a category or number).  `y = 10` says that the variable '_y_' has the value of '_10_'.  There are two types of variables:

* __Independent Variable (aka cause, predictor)__ - the variable(s) does not depend on any other variable (e.g. _Smoked Cigarettes Per Day_, _Time Spent Studying_, _How much you eat_).  This variable is thought to be the cause of some effect.  In experimental research, this variable(s) is manipulated.
* __Dependent Variable (aka effect, outcome)__ - the variable(s) that we think are an effect because the value depends on the independent variables (e.g. _Has Lung Cancer_, _Test Score_).  This variable(s) is thought to be affected by the independent variable.
* __Example__: '_Time Spent Studying_' causes a change in '_Test Score_'.  '_Test Score_' can't cause a change in '_Time Spent Studying_'.  '_Test Score_' (_independent variable_) depends on '_Time Spent Studying_' (_dependent variable_)
* Summary: What you're trying to do is see if the _independent variable(s)_ causes some kind of change in the _dependent variable(s)_; we're checking if there's any relationship, which later we'll understand it to be as part of the overall: `outcome = model + error`.

#### Categorical and Continuous Variables <a id="categoricalcontinuous"></a>
There are different levels of measurement used to categorize and quantify variables.  Variables can be categorized '_categorical_' or '_continuous_' as well as quantified at different levels.  As we go down the list, the measurements become more detailed and useful for statistical analysis.

* __Categorical (aka Qualitative)__ - Deals with unmeasurables/can't do arithmetic with it (e.g. '_species_' could have values of '_human_', '_cat_', or '_dog_').  Your choices are discrete (as in you can be a human or a cat, but not both).  Categoricals are further categorized as:
    - __Dichotomous (aka Binary)__ - Two distinct possibilities (e.g. pregnant or not pregnant)
    - __Nominal__ - Two or more possibilities (e.g. human, cat, dog) 
    - __Ordinal__ - Two or more possibilities and there is a logical order (e.g. first, second).  You know which is bigger or smaller, but not by how much (e.g. you know who won race, but not how close race was)
    - _Programming_ - In R, this is 'factor()' and Python's Pandas, this is 'categorical' with an optional order
* __Continuous (aka Quantitative)__ - Deals with numbers (e.g. '_length_', '_age_').  Continuous are further categorized as:
    - __Interval__ - Two or more possibilities, there is a logical order that you can measure (i.e. numbers that you can do arithmetic with), and there are equal intervals (e.g. Fahrenheit measurement of 40 to 50 degrees is the same difference as 50 to 60 degrees)
    - __Ratio__ - Two or more possibilities, there is a logical order that you can measure, there are equal intervals, and there is a true zero point (e.g. the weight of an object cannot weigh less than 0) 
* _Note: Parametric and Non-parametric_: The types of data determines the types of tests: _parametric_ or _non-parametric_, and they affect the type of statistical procedures you can use.  The main differences are that parametric tests have either 'interval' or 'ratio' scales and uses information about the mean and deviation from the mean (meaning it has a relatively normal distribution), which gives it more statistical power.  Nonparametric statistics uses 'nominal' or 'ordinal' data, (i.e. less information) in their calculation and are thus less powerful.

#### Reliability <a id="reliability"></a>

* __Measurement Error__ - the discrepancy between the numbers we use to represent the thing we're measuring and the actual value of measuring it directly (e.g. I measure my height with a ruler, but might be a little inaccurate)
* __Reliability__ - whether an instrument can be interpreted consistently across different situations.  The easiest method is through _test-retest reliability_, which tests the same group of people twice (e.g. if I weighed myself within minutes of each other, would I get the same result?)
* __Counterbalancing__ - order in which a person participates in an experiment may affect their behavior.  Counterbalancing fixes this; say there are two possible conditions (A and B), subjects are divided into two groups with Group 1 (A then B), Group 2 (B then A)
* __Randomization__ - randomly allocates experimental units across groups; reduces _confounding_ by dispersing at chance levels (hopefully roughly evenly)

#### Validity <a id="validity"></a>
Validity is whether an instrument actually measures what it sets out to measure (e.g. does a scale actually measure my weight?).  Validity is usually divided into three forms including _Criterion_, _Content_, and _Construct_.

* __Criterion Validity__ - looks at the correlation between one set of variable(s) predicts an outcome based on information from another set of criterion variable(s).  e.g. IQ tests are often validated against measures of academic performance (the criterion).  Criterion Validity can be either _concurrent_ or _predictive_:
    - __Concurrent Validity__ - assess the ability to distinguish between groups (e.g. check if an AP exam can substitute taking a college course; all students take AP exam and college course, check if there is a strong correlation between exam scores and college course grade)
    - __Predictive Validity__ - assess the ability to predict observations at a later point in time (e.g. how well does SAT test predict academic success?  Determine usefulness by correlating SAT scores to first-year student's GPA)
    - _Note_: Difficult because objective criteria that can be measured easily may not exist
* __Content Validity (aka Logical Validity)__ - a non-statistical type of validity that assesses the degree to which individual items represent the construct being measured (e.g. test of the ability to add two numbers should include adding a combination of digits including odd, even and not multiplying)
    - __Face Validity__ - an estimate of whether a test appears to measure a certain criterion
    - __Representation Validity (aka Translation Validity)__ - the extent to which an abstract theoretical construct can be turned into a specific practical test
* __Construct Validity__ - a construct is an abstraction (attribute, ability or skill) created to conceptualize a _latent variable_.  (e.g. someone's English proficency is a construct).  Construct Validity is whether your test measures the construct adequately  (e.g. how well does your test measure someone's English proficiency).  In order to have _construct validity_, you need both _convergent_ and _discriminant_ validity.
    -  __Convergent Validity__ - the degree to which two measures of constructs that should be related, are in fact related (e.g. if a construct of general happiness had convergent validity, then it should correlate to a similar construct, say of marital satisfaction)
    -  __Discriminant Validity__ - the degree to which two measures of constructs that should be unrelated, are in fact unrelated (e.g. if a construct of general happiness had discriminant validity, then it should correlate to an unrelated construct, say of depression)

## How are we measuring data?  <a id="howdata"></a>

#### Types of Research Methods  <a id="correlationcausation"></a>
We're interested in _correlation_ as well as _causality_ (cause and effect).  To test a hypothesis, we can do the following types of research.

* __Observational/Correlational Research__ - observe what naturally goes on without directly inferring.  Correlation suggests a relationship between two variables, but cannot prove that one caused another.  Correlation does not equal causation.
    - __Cross-sectional Study__ - a snapshot of many different variables at a single point in time (e.g. measure cholesterol levels of daily walkers across two age groups, but can't consider past or future cholestral levels of snapshot)
    - __Longitudinal Study__ - measure variables repeatedly at different time points (e.g. we might measure workers' job satisfaction under different managers)
    - __Limitations of Correlational Research__
        + __Continuity__ - we view the co-occurrence of variables so we have no timeline (e.g. we have people with low self-esteem and dating anxiety, can't tell which came first)
        + __Confounding Variables (aka Teritum Quid)__ - extraneous factors (e.g. a correlation between breast implants and suicide; breast implants don't cause suicide, but a third factor like low self-esteem might cause both)
* __Experimental Research__ - manipulate one variable to see its effect on another.  We do a comparison of situations (usually called _treatments_ or _conditions_) in which cause is present or absent (e.g. we split students into two groups: one with motivation and the other as control, no motivation)
    - __Between-Subjects Design (aka independent measures, between-groups)__ - Participants can be part of the treatment group or the control group, but not both.  Every participant is only subjected to a single treatment (e.g. motivational group gets motivation, control gets no motivation through entire experiment)
    - __Within-Subjects Design (aka repeated measures)__ - Participants are subjected to every single treatment, including the control (e.g. give positive motivation for a few weeks, then no motivation)

## Descriptive Statistics  <a id="descriptivestatistics"></a>

#### Frequency Distribution (aka Histogram)  <a id="histogram"></a>
A count of how many times different values occur.  The two main ways a distribution can deviate from normal is by _skew_ and _kurtosis_

* __N__ - _N_ is the size of the sample and _n_ represents a subsample (e.g. number of cases within a particular group)
* __skew__ - the symmetry (tall bars are clustered at one end of the scale); _positively skewed_ means clustered at the lower end / left side) while _negatively skewed_ means clustered at the higher end / right side)
* __kurtosis__ - the pointyness (degree that scores cluster at the end of the distributions/ the _tails_); _positive kurtosis_ has many scores in the tails (aka _leptokurtic distribution_, _has heavy-tailed distribution_) while _negative_kurtosis_ is relatively thin (aka _platykurtic distribution_, _has light tails_)
* __Normal Distribution__ - a bell-shaped curve (majority of bars lie around the center of the distribution); has values of `skew=0` and `kurtosis=0`

#### Mode, Median, Mean  <a id="modemedianmean"></a>
Used in frequency distribution to describe central tendancy

* __Mode__ - the value that occurs most frequently.  Modes can have many 'highest points'
    - __bimodal__ - two bars that are the highest
    - __multimodal__ - more than two bars that are the highest
* __Median__ - the middle value when ranked in order of magnitude
* __Mean__ - the average, represented as:


#### Quantiles, Quartiles, Percentages  <a id="quantilequartilepercentages"></a>
* __Range__ - largest value minus the smallest value
* __Quantiles__ - quantiles split data into equal portions.  Quantiles include _quartiles_ (split into four equal parts), _percentages_ (split into 100 equal parts), _noniles_ (points that split the data into nine equal parts)
* __Quartiles__ - the three values that split sorted data into four equal parts.  _Quartiles_ are special cases of _quantiles_
    - __Lower Quartile (aka First Quartile, Q1)__ - the median of the lower half of the data
    - __Second Quartile (aka Q2)__ - the median, which splits our data into two equal parts
    - __Upper Quartile (aka Third Quartile, Q3)__ - the median of the upper half of the data
    - _Note_: For discrete distributions, there is no universal agreement on selecting quartile values
    * __Interquartile Range (aka IQR)__ - exclude values at the extremes of the distribution (e.g. cut off the top and bottom 25%) and calculate the range of the middle 50% of scores.  `IQR = Q3 - Q1`

#### Dispersion <a id="dispersion"></a>

* __Variation (aka spread, dispersion)__ - a change or difference in condition, amount, level
* __Variance__ - a particular measure of _variation_; how far a set of numbers are spread out around the mean or expected value; low variance means values are clumped together (zero means all values are identical), high variance means values are spread out from the mean and each other
    - __Unsystematic Variation (aka random variance)__ - random variability within individuals and/or groups (e.g. I feel better today than yesterday)  `Unsystematic Variance = Measurement Error + Individual Differences`
    - __Systematic Variation (aka between-groups variance)__ - variance between-groups created by a specific experimental manipulation (e.g. give bananas to reward monkeys for successfully completing tasks); doing something in one condition but not in the other condition
* __Deviance__ - quality of fit for a model, the difference between each score and the fit.  Used in the _sum of squares of residuals in ordinary least squares_ where model-fitting is achieved by maximum likelihood.  Can be calculated as: `deviance = <insert deviance equation>`
* __Sum of Squared Errors (aka SS, sum of squares)__ - we can't just add up all the deviance (or else the total spread is just zero, which is meaningless) so we square all the values in order to get the total dispersion / total deviance of scores (i.e. gets rid of negatives)
* __Variance__ - _SS_ works nicely until the number of observations (_n_) changes, then we'll need to recalculate.  Instead of using total dispersion, we use the average dispersion, which is the _variance_
* __Standard Deviation__ - Since the _variance_ is still squared, we need to do the square root of the variance, as calculated here: `standard deviation = <insert standard deviation formula>`
* __Test Statistic__ - the ratio of systematic to unsystematic variance or effect to error (i.e. the signal-to-noise).  Depending on the model, we have a different name for the test statistic.  Examples include:
    - _F-test_
    - _Leven's test_
    - _Bartlett's test_
    - _Brown-Forsythe test_

#### From Frequency to Probability  <a id="probability"></a>
So what does the frequency tell us?  Instead of thinking of it as as the frequency of values occuring, think of it as how likely a value is to occur (i.e. probability).  For any distribution of scores, we can calculate the probability of obtaining a given value.

* __Probability Density Functions (aka probability distribution function, probability function)__ -  just like a histogram, except that lumps are smoothed out so that we have a smooth curve.  The area under curve tells us the probability of a value occurring.
    -  Common distributions include _z-distribution_, _t-distribution_, _chi-square distribution_, _F-distribution_
    -  We use a normal distribution with a mean of 0 and a standard deviation of 1 (this lets us use tabulated probabilities instead of calculating ourselves)
    -  To ensure a standard deviation of 1, we calculate the _z-scores_ using: `z = <insert z-score equation>`
    -  Check tabulated values and you'll get the probability _P_ of a value occurring

#### Populations and Samples  <a id="populationssamples"></a>
We want to find results that apply to an entire population.

* __Population__ - summation of the same group or species; can be very general or very specific (e.g. humans, cats named Will)
* __Sample__ - small subset of the population
* __Degrees of Freedom__ - number of values in the final calculation that are free to vary (e.g. mean is 5 for the values 4, 6, and <blank>.  <blank> must then be 5)
* __Sampling Variation__ - samples will vary because they contain different members of the population (e.g. grab random samples of people off the street, some samples you'll get a sample/group of people that is smarter, some samples not so much) 
* __Sampling Distribution (aka finite-sample distribution)__ - the distribution of a sample statistic (based on a random sample); this tells us about the behavior of samples from the population
* __Standard Error (aka SE)__ - a measure of how representative a sample is likely to be of the population.  A large standard error means there is a lot of variability so samples might not be representative of the population.  A small standard error means that most samples are likely to be an accurate reflection of the population
* __Central Limit Theorem__ - as samples get large (greater than 30), the sampling distribution has a normal distribution and a standard deviation of: `insert standard deviation formula`
* __Confidence Intervals (for large samples)__ - along with standard error, we can calculate boundaries that we believe the population will fall.  In general, we could calculate confidence intervals using the _central limit theorem_:
    - `lower boundary of confidence interval = formula`
    - `upper boundary of confidence interval = formula`
    - Note: Different for 95% confidence interval (most common) or 99% or 90%
* __Confidence Intervals (for small samples)__ - for smaller samples, we can't calculate boundaries using the _central limit theorem_ because the _sampling distribution_ isn't a _normal distribution_.  Instead, smaller samples have a _t-distribution_ and would be calculated with:
    - `lower boundary of confidence interval = formula`
    - `upper boundary of confidence interval = formula`
* __p-value__ - the probability of obtaining the observed sample results when the null hypothesis is true.  If the _p-value_ is very small (threshold based on the previously chosen _significance level_, traditionally 5% or 1%) then the hypothesis must be rejected.
    - p <= 0.01 means very strong presumption against null hypothesis
    - 0.01 < p <= 0.05 means strong presumption against null hypothesis
    - 0.05 < p <= 0.1 means low presumption against null hypothesis
    - p > 0.1 means no presumption against the null hypothesis
    - _Note_: _NHST_ only works if you generate your hypothesis and decide on threshold before collecting the data (otherwise chances of publishing a bad result will increase; as is with 95% confidence level you'll only report 1 bad in 20)


## Inferential Statistics  <a id="inferentialstatistics"></a>

#### Statistical Model  <a id="models"></a>

We can predict values of an outcome variable based on some kind of model.  All models follow the simple equation of `outcome = model + error`.

* __Model__ - Models are made up of _variables_ and _parameters_
    - __Variables__ - are measured constructs that vary across entities in the sample
    - __Parameters__ - are estimated from the data (rather than being measured) and are (usually) constants.  E.g. the mean and median (which estimate the center of the distribution) and the correlation and regression coefficients (which estimate the relationship between two variables).  We say 'estimate the parameter' or 'parameter estimates' because we're working with a sample, not the entire population.
* __Outcome__ - `outcome = model + error`
* __Error (aka deviance)__ - Error for an entity is the score predicted by the model subtracted from the observed score for that entity.  `error = outcome - model`

#### Null Hypothesis Significance Testing (aka NHST) <a id="nhst"></a>

__Null Hypothesis Significance Testing (aka NHST)__ is a method of statistical inference used for testing a hypothesis.  A test result is _statistically significant_ if it has been predicted as unlikely to have occurred by chance alone, according to a threshold probability (the significance level)

* __Alternative Hypothesis (aka H1, Ha, experimental hypothesis)__ - the hypothesis or prediction that sample observations are influenced by some non-random cause
* __Null Hypothesis (H0)__ - Opposite of the _alternative hypothesis_, refers to the default position that there is no relationship between two measured phenomena
* __Directional and Nondirectional Hypothesis__ - hypothesis can be either _directional_ if it predicts whether an effect is larger or smaller (e.g. if I buy cookies, I'll eat more) or _non-directional_ if it does not predict whether effect is larger or smaller (e.g. if I buy cookies, I'll eat more or less).
  - __One-tailed test__ -  a statistical model that tests a directional hypothesis (Note: Basically, do not ever do a one-tailed test unless you are absolutely sure of the direction)
  - __Two-tailed test__ -  a statistical model that tests a non-directional hypothesis
* __Sample Size and Statistical Significance__ - there is a close relationship between _sample size_ and statistical significance (the _p-value_).  The same effect will have different _p-values_ in different sized samples (i.e. small differences can be 'significant' in large samples and large effects might be deemed 'non-significant' in small samples)
* __Effect Size and Cohen's d__ - statistical significance does not tell us about the importance of an effect.  The solution is to measure the size of the effect, which is a quantitative measure of the strength of a phenomenon.  In order to compare the mean of one sample to another, we calculate _Cohen's d_.
* __Pearson's correlation coefficient (aka r)__ - measure of strength of relationship between two variables (thus it's an _effect size_)
    - r ranges between 0 (no effect) and 1 (a perfect effect)
    - r=.10 means a small effect
    - r=.30 means a medium effect
    - r=.50 means a large effect
    - _Note_: r=.6 does not mean it has twice the effect of r=.3
* __Meta-analysis__ - statistical methods for contrasting and combining results from different studies in the hope of identifying patterns among study results (i.e. conducting research about previous research).  Meta-analysis allows to achieve a higher _statistical power_.

#### Type I and Type II Errors <a id="typeerrors"></a>

There are two types of errors that we can make when testing a hypothesis.  These two errors are linked; if we correct for one, the other is affected.  You can visualize this with a __confusion matrix (aka contingency table, error matrix)__

* __Type I error (aka false positive)__ - occurs when we believe there is an effect in our population, when in fact there isn't (e.g. a doctor thinking a man is pregnant).  Using conventional 95% confidence level, the probability is 5% of seeing this error.  This means if we repeated our experiment, we would get this error 5 out of 100 times.
    * __Familywise (FWER) or Experimentwise Error Rate (EER)__ - the error rate across statistical tests conducted on the same data for _Type I errors_.  This can be calculated using the following equation (assuming .05 level of significance): `familywise error=1=(0.95)^n` where _n_ is the number of tests carried out.
    * __Bonferroni correction__ - the easiest and most popular way to correct _FWER_, which fixes familywise error rate, but at the loss of _statistical power_.
* __Type II error (aka false negative, lack of Statistical Power)__ - opposite of _Type I error_, occurs when we believe there is no effect in the population when in fact there is an effect (e.g. a doctor thinks a woman is not pregnant, but she is).  The maximum acceptable probability of a _Type_II error_ is 20%.  This means we miss 1 in 5 genuine effects.
    * __Statistical Power__ - statistical power is the ability of a test to find an effect; the power of the test is the probability that a given test will find an affect assuming that one exists in the population.  Basically, _statistical power_ is linked with the _sample size_.  We aim to achieve a power of 80% chance of determining an effect if one exists.  
    * _Note_: For _R_, use the package 'pwr' to use power to calculate the necessary sample size.  You can also calculate the power of a test after the experiment, but if you find a non-significant effect, it might be that you didn't have enough power.  If you find a significant effect, then you had enough power.


#### Parametric Statistics   <a id="parametric"></a>
A branch of statistics that assumes the data comes from a type of probability distribution and makes inferences about the parameters of the distribution.  The assumptions are:

* __normal distribution__ - different depending on the test you're using (remember sample size affects how we test for _normal distribution_).  What we want is a `skew = 0` and `kurtosis = 0`.  We can eye-ball this in R with 'stat_function()' and 'Q-Q plot'.  To get more accurate (instead of just eye-balling), we can use 'describe()'.
    - __stat_function()__ - draws a function over the current plot layer; for example, using the argument _fun = dnorm()_, which returns the probability (i.e. the density) for a given value.
    - __Q-Q plot (quantile-quantile plot)__ - a _quantile_ is the proportion of cases we find below a certain value; using the `stat = "qq"` argument in a _qplot()_, we can plot the cumulative values we have in our data against the cumulative probability of a normal distribution (i.e. data is ranked and sorted).  If data is normally distributed, then the actual scores will have the same distribution as the score so we'll get a straight diagonal line.
    - __describe() or stat.desc()__ - We can get descriptive summaries of our data with the _describe()_ function of the 'psych' package or _stat.desc()_ function of the 'pasetcs' package.  Again, we're interested in `skew = 0` and `kurtosis = 0`, which we can then calculate the _z-score_ (so that we can compare to different samples that used different measures and so that we can see how likely our values of _skew_ and _kurtosis_ are likely to occur).
    - __z-score skewness calculation__ - we get a z-score for skewness
    - __z-score kurtosis calculation__ - we get a z-score for kurtosis
    - _Note:_ interpretting z-scores depends on the sample size.  Absolute value greater than 1.96 is significant at 'p < .05', greater than 2.58 at 'p < .01', greater than 3.29 is significant at 'p < .001'
        + _small samples (<30)_ - it's okay to look for values above 1.96
        + _large samples(>30 and <200)_ - it's okay to look for values above 2.58
        + _very large samples (_200+)_ - look at the shape's distribution visually and value of the skew and kurtosis statistics instead of calculating their significance (because they are likely to be significant even when skew and kurtosis are not too different than normal)
    - __skew.2SE and kurt.2SE__ - _stat.desc()_ gives us _skew.2SE._ and _kurt.2SE_, which stands for the _skew_ and _kurtosis_ value divided by 2 standard errors (i.e. instead of values above, we can say if the absolute value greater than 1 is significant at 'p < 0.5', greater than 1.29 at 'p < .01', greater than 1.65 is significant at 'p < .001')
    - __Shapiro-Wilk Test of Normality__ - _Shapiro-Wilk_ is a way of looking for normal distribution by checking whether the distribution as a whole deviates from a comparable normal distribution.  This is represented as _normtest.W_ (W) and _normtest.p_ (p-value) through either the _stat.desc()_ or _shapiro.test()_ functions
    - __interval data__ - data should be measured at least in the interval level (tested with common sense)
    - __independence__ - different depending on the test you're using

#### Homogeneity and Heterogeneity of Variance   <a id="geneityvariance"></a>
Variances should be the same throughout the data (i.e. data is tightly packed around the mean).  For example, say we measured the number of hours a person's ear rang after a concert across multiple concerts.

* __Homogeneity of Variance__ - After each concert, the ringing lasts about 5 hours (even if this is sometimes 10-15 hours, 20-25 hours, 40-45 hours)
* __Heterogeneity of Variance__ - After each concert, the ringing lasts from 5 to 30 hours (say first concert is 5-10 hours, last concert is 20-50 hours)
* __Levene's test (F)__ - _Levene's test_ tests that the variances in different groups are equal (i.e. the difference between variances is equal to zero).
    - Test is significant at p <= .05, which we can then conclue that the variances are significantly different (meaning it is not _homogeneity of variance_)
    - Test is non-significant at p > .05, then the variances are roughly equal and it is _homogeneity of variance_
    - _Note:_ in large samples, Leven's test can be significant even when group variances are not very different; for this reason, it should be interpreted with the _variance ratio_
* __Hartley's Fmax (aka variance ratio)__ - the ratio of the variances between the group with the biggest variance the group with the smallest variance.  This value should be smaller than the critical values.

## Linear Regression  <a id="linreg"></a>

#### Data Transformations  <a id="linregdatatransformation"></a>
If you cannot make data fit into a normal distribution and you checked that the data was entered in correctly, you can do the following:

* __Remove the outlier__ - delete the case only if you believe this is not representative (e.g. for Age someone puts 200, pregnant male)
* __Data transformation__ - do trial and error data transformations.  If you're looking at differences between variables you must apply the same transformation to all variables.  Types of transformations include:
    - __Log transformation log(Xi)__ - taking the logarithm of a set of numbers squashes the right tail of the distribution.  Advantages is this corrects for _positive skew_ and _unequal variances_.  Disadvantage is that you can't take the log of zero or negative numbers (though you can do log(x +1) to make it positive where 1 is the smallest number to make the value positive.
    - __Square root transformation__ - Taking the square root centers your data (square root affects larger values more than smaller values).  Advantage is it corrects _positive skew_ and _unequal variances_.  Disadvantage is same as log, no square root of negative numbers
    - __Reciprocal transformation (1/Xi)__ - divide 1 by each score reduces the impact of large scores.  This reverses the score (e.g. a low score of 1 would create 1/1 = 1; a high score of 10 would create 1/10 = 0.1).  The small score becomes the bigger score.  You can avoid score reversal by reversing the scores before the transformation 1/(X highest - Xi).Advantages is this corrects _positive skew_ and _unequal variances_
    - __Reverse Score transformation__ - The above transformations can correct for _negative skew_ by reversing the scores.  To do this, subtract each score from the highest score obtained or the highest score + 1 (depending if you want lowest score to be 0 or 1).  Big scores have become small and small scores have become big.  Make sure to reverse this or interpret the variable as reversed. 
* __Change the score__ - if transformation fails, consider replacing the score (it's basically the lesser of two evils) with one of the following:
    - Change the score to be one unit above the next highest score in the data set
    - The mean plus three standard deviations (i.e. this converts back from a z-score)
    - The mean plus two standard deviations (instead of three times above)
*  __Dummy coding__ - is a way of representing groups of people using only zeros and ones.  To do this, we create several variables (one less than the number of groups we're recoding) by doing:
    -  Count the number of groups you want to recode and subtract 1
    -  Create as many new (dummy) variables as step above
    -  Choose one of your groups as a baseline; this should be your control group or the group that represents the majority of your people (because it might be interesting to compare other groups against the majority)
    -  Assign the baseline group value of 0 for all of your dummy variables
    -  For the first dummy variable, assign value of 1 to the first group that you want to compare against the baseline and 0 for all other groups
    -  Repeat above step for all dummy variables
    -  Place all your dummy variables into analysis

* __Robust test (robust statistics)__ - statistics that are not unduly affected by outliers or other small departures from model assumptions (i.e. if the distribution is not normal, then consider using a _robust test_ instead of a _data tranformation_).  These tests work using these two concepts _trimmed mean_ and _bootstrap_:
    - __trimmed mean__ - a mean based on the distribution of scores after you decide that some percentage of scores will be removed from each extreme (i.e. remove say 5%, 10%, or 20% of top and bottom scores before the mean is calculated)
        + __M-estimator__ - slightly different than _trimmed mean_ in that the _M-estimator_ determines the amount of trimming empiraccly.  Advantage is that we never over or under trim the data.  Disadvantage is sometimes _M-estimators_ don't give an answer.
    - __bootstrap__ - _bootstrap_ estimates the properties of the sampling distribution from the sample data.  It treats the sample data as a population from which smaller samples (_bootstrap samples_) are taken with replacement (i.e. puts the data back before a new sample is taken again).
    - _Note:_ _R_ has a package called _WRS_ that has these _robust tests_, including _boot()_
        + `my_object <- boot(data, function, replications)` where _data_ is the dataframe, _function_ is what you want to bootstrap, _replications_ is the number of bootstrap samples (usually 2,000)
        + _boot.ci(my_object)_ returns an estimate of bias, empirically derived standard error, and confidence intervals

#### Linear Regression - Covariance and Correlation  <a id="linregcovariancecorrelation"></a>
We can see the relationship between variables with _covariance_ and the _correlation coefficient_.

* __variance__ - remember that _variance_ of a single variable represents the average amount that the data vary from the mean
* __covariance__ - _covariance_ is a measure of how one variable changes in relation to another variable.  Positive covariance means if one variable moves in a certain direction (e.g. increases), the other variable also moves in the same direction (e.g. increases).  Negative covariance means if one variable moves in a certain direction (e.g. increases), the other variable moves in the opposite direction (e.g. decreases).
    - __deviance__ - remember that _deviance_ is the difference in value vertically (i.e. the quality of fit)
    - __cross-product deviations__ - when we multiple the _deviation_ of one variable by the corresponding _deviation_ of another variable
    - __covariance__ - the average sum of combined deviations (i.e. it's the cross-product deviation, but also divided by the number of observations, N-1)
    - _Note:_ _covariance_ is NOT a good standardized measure of the relationship between two variables because it depends on the scales of measurement (i.e. we can't say whether a covariance is particularly large relative to another data set unless both data sets used the same units).
    - __standardization__ - is the process of being able to compare one study to another using the same unit of measurement (e.g. we can't compare attitude in metres)
    - __standard deviation__ - the unit of measurement that we use for _standardization_; it is a measure of the average _deviation_ from the mean
* __correlation coefficient__ - this is the standardized covariance; we basically get the standard deviation of the first variable, multiply by the standard deviation of the second variable, and divide by the product of the multiplication.  There's two types of correlations including _bivariate_ and _partial_:
    - __bivariate correlation__ - correlation between two variables.  Can be:
        + __Pearson product-moment correlation (aka Pearson correlation coefficient)__ - for parametric data that requires interval data for both variables
        + __Spearman's rho__ - for non-parametric data (so can be used when data is non-normally distributed data) and requires only ordinal data for both variables.  This works by first ranking the data, then applying _Pearson's equestion_ to the ranks.
        + __Kendall's tau__ - for non-parametric data, similar to Spearman's, but use this when there's a small number of samples and there's a lot of tied ranks (e.g. lots of 'liked' in ordinal ranks of: dislike, neutral, like)
    - __partial correlation__ - correlation between two variables while 'controlling' the effect of one or more additional variables
    - _Note:_ values range from -1 (negatively correlated) to +1 (positively correlated)
        + +1 means the two variables are perfectly positively correlated (as one increases, the other increases by a proportional amount)
        + 0 means no linear relationship (if one variable changes, the other stays the same)
        + -1 means the two variables are perfectly negatively correlated (as one increases, the other decreases in a proportional amount)
        + __correlation matrix__ - if you want to get correlation coefficients for more than two variables (a gird of correlation coefficients)
        + __correlation test__ - if you only want a single correlation coefficient
    - _R_ to calculate correlation, we can the _Hmisc_ package, specifically with _cor()_, _cor.test()_, and _rcorr()_
    - __causaulity__ - _correlation_ does not give the direction of causality (i.e. correlation does not imply causation)
    - __third-variable problem (aka tertium quid)__ - _causality_ between two variables cannot be assumed because there may be other measured or unmeasured variables affecting the results
* __coefficient of determination (R^2)__ - a measure of the amount of variability in one variable that is shared by the other (i.e. indicates how well data fit a statistical model); this is simply the _correlation coefficient_ squared.  If we multiply this value by 100, we can say that variable A CAN (not necessarily does) account up to X% of variable B.
* __biserial and point-biserial correlation coefficient__ - these correlational coefficients are used when one of the two variables is _dichotomous (aka binary)_.  _point-biserial correlation_ is used when one variable is a _discrete_ dichotomy (i.e. dead or alive, can't be half-dead).  _biserial correlation_ is used when that one variable is a _continuous_ dichotomy (i.e. your grade is pass or fail, but it can have multiple levels including A+, C-, F).
* __partial correlation and semi-partial correlation (aka part correlation)__ - a _partial correlation_ is the relationship between two variables while controlling for the effects of a third variable on both variables in the original correlation.  _semi-partial correlation_ is the relationship between two variables while controlling for the effects of a third variable on only one of the variables in the original correlation.

#### Linear Regression Analysis  <a id="linreganalysis"></a>
_Regression Analysis_ is a way of predicting an outcome variable from one predictor variable (_simple regression_) or from several predictor variables (_multiple regression_).  We fit a model to our data and use it to predict values of the _dependent variable_ from one or more _independent variables_.

*  __method of least squares__ - method to find the line of best fit, which finds the smallest _residuals_ (aka the difference, _variance_) between the actual data and our model
*  __regression model (aka regression line)__ is the line of best fit resulting from the _method of least squares_.  Remember that even though this is the best fitting line, the model could still be a bad fit to the data
*  __residual sum of squares (aka sum of squared residuals)__ - represents the degree of inaccuracy when the best model is fitted to the data
*  __model sum of squares__ - the improvement going from the worst fit line (which just uses the mean for every value) to the best fit line (our _regression model_).  If this value is large, then the _regression model_ has made a big improvement on how well the outcome variable can be predicted.  If this value is small, then the model isn't much of an improvement.
*  __t-test (aka Student's t-test)__ - is a method to test the hypothesis about the mean of a small sample from a _normally distributed_ population when the population _standard deviation_ is unknown.  This is when you can't compute a _z-score_ because samples are too small or we don't know the population variance of the population.
*  __t-statistic (aka t-score)__ - usually used in _t-tests_, helps us assess predictor variables in whether they improve our model.
*  __t-distribution (aka Student's t-distribution)__ is the distribution of the _t-statistic_.  This is a probability distribution that is used to estimate population parameters when the sample size is small and/or when the population variance is unknown.
*  __Akaike Information Criterion (aka AIC)__ - measure of fit (like R^2), except that it penalizes the model for having more variables (whereas R^2 only fits the data better with more predictors).  The bigger the value means a worse fit, smaller the value means better fit.  Only compare the AIC to models of the same data (there's no reference; can't say 10 is small or big).
*  __Bayesian Information Criterion (aka BIC)__, a measure of fit like _AIC_, but has a larger penalty for more variables than _AIC_.


#### Linear Regression - Selecting Predictors  <a id="linregpredictors"></a>
If you're making a complex model, the selection of predictors and its order can have a huge impact.  Put predictors methodically, not just add a bunch and see what results happen.  Here's a few methods:

*  __Hierarchical Regression__ is where predictors are selected based on past work and the experimenter decides what order to enter the predictors into the model.  Known predictors should be entered in first in order of importance in predicting the outcome.
*  __Forced entry__ is where all predictors are carefully chosen, then forced into the model simultaneously in random order.
*  __Stepwise methods__ is where all predictors and their order are based off a mathematical criterion and has a direction of _forward, backward, both_.
    -  __Forward Direction (aka Forward Selection)__ - An initial model is defined that contains only the constant, then the computer searches for the predictor (out of the ones available) that best predicts the outcome variable.  If the model improves the ability to predict the outcome, the predictor is retained.  The next predictor selected is based on the largest semi-partial correlation (i.e. the 'new variance'; if predictor A explains 40% of the variation in the outcome variable, then there's 60% variation left unexplained.  If predictor B is measured only on the remaining 60% variation).  We stop selecting predictors when the _AIC_ goes up on the remaining variables (remember lower _AIC_ means better model)
    -  __Backward Direction (aka Backward Elimination)__ - An initial model that starts with all predictor variables, removes one at a time, stops if remaining variables makes _AIC_ go up (remember lower _AIC_ means better model).  This is the preferred method because of _suppressor effects_, which occurs when a predictor has an effect but only when another variable is held constant.  _Forward Direction_ is more likely than _Backward Direction_ to exclude predictors involved in suppressor effects (and thus make a _Type II error_)
    -  __Both Direction (aka stepwise)__ - Starts the same as _Forward Direction_, but whenever you add a predictor, a removal test of the least useful predictor is done to see if there's any redundant predictors
    - _Note:_ If you used one of the above stepwise methods to get dressed on a cold day, you might put on pants first instead of underwear.  It'll see that underwear doesn't fit now that you have pants on so it'll skip.  If you don't mind your computer doing lots of calculations, try the _all-subsets_ method.
    -  __All-subsets methods__ is where we try every combination of predictors to see which is the best fit (using a statistic called _Mallow's Cp_).  You can increase accuraccy, but the possibilities increase exponentially so calculations take much longer.
    -  _Another Note:_ There's a huge danger of over-fitting with too many variables so it's important to __cross-validate__ the model by splitting into train/test sets.  Remember, the fewer predictors the better.


#### Linear Regression - How's my model doing?  <a id="linregevaluatemodel"></a>
When making a model, we should check for two things 1.) how well the model fits the observed data through _outliers, residuals, influence cases_ and 2.) for _generalization_, which is how the model generalizes to other cases outside your sample.

*  __Outliers and Residuals__ - We want to look at outliers to see if a small number of cases heavily influence the entire model.  What we do is look for _residuals_, which is the error pressent in the model (smaller the value, the better the fit.  Large values mean outliers).
    - __Unstandardized residuals (normal residuals)__ are measured in the same units as the outcome variable and are difficult to interpret across different models.  We cannot define a universal cut-off point of what is an _outlier_.  Instead, we need _standardized residuals_.
    - __Standardized residuals__ are _residuals_ divided by an estimate of their _standard deviation_, which gives us the ability to compare residuals from different models using the properties of _z-scores_ to determine universal guidelines on acceptable and unacceptable values.  E.g. Normally distributed sample, 99% of z-scores should lie between -3.29 and 3.29.  Anything above or below these values are cause for concern and thus the model is a bad fit.
*  __Influential Cases__ - We should also check to see that any influential cases aren't greatly biasing the model.  We can assess the influence of a particular case using multiple methods:
    -  __Adjusted Predicted Value__ - So basically, two models are created; one without a particular case and with the case.  The models are then compared to see if the predicted value is the same regardless of whether the value is included.  If the predicted value is the same, the model is good.  If the predicted value is not the same, the model is a bad fit.  The _adjusted predicted value_ is the predicted value for the model without the case.  
        + __DFFit__ is the difference between the _adjusted predicted value_ (when the model doesn't include a case) from the original predicted value (when the model includes the case).
        + __DFBeta__ is the difference between a parameter estimated using all cases and estimated when one case is excluded
    -  __Studentized Residual__ is when the residual is divded by the standard error so it gives us a standardized value; this can be compared across different regression analyses because it is measured in standard units.  It's called the studentized residual because it follows a _Student's t-distribution_.  This is useful to assess the influence of a case on the ability of the model to predict the case, but doesn't provide info on how a case influences the model as a whole (i.e. the ability to predict all cases)
    -  __Cook's distance__ is a statistic that considers the effect of a single case on the model as a whole (i.e. the overall influence of a case on the model); any values greater than 1 may be cause for concern
    -  __hat values (aka leverage)__ is another way to check the influence of the observed value of the outcome variable over the predicted values (0 means the case has no influence up to 1 meaning the case has complete influence).  If no cases exert undue influence over the model, then all leverage values should be close to the average value.  Some recommend investigating cases greater than twice to three times the average.


#### Linear Regression - Assumptions  <a id="linregassumptions"></a>
*  __Generalization__ asks the question of whether our model can generalize to other cases outside of this sample (i.e. apply to a wider population)?  We need to check for these assumptions in regression analysis.  Once these below assumptions are met, the coefficients and parameters of the regression equation are said to be _unbiased_.  An _unbiased model_ tells us that on average, the regression model from the sample is the same as the population model (but not 100%)
    -  __Variable types__ - All predictor variables must be quantitative or categorical (with two categories).  The outcome variable must be quantitative (measured at the interval level), continuous, and unbounded (no constraints on the variability of the outcome; e.g. if the outcome is a measure ranging from 1 to 10 yet the data collected vary between 3 and 7, then its constrained)
    -  __Non-zero variance__  - Predictors should have some variation in value (i.e. do not have variances of 0)
    -  __No perfect multicollinearity__ - Should be no perfect linear relationship between two or more predictors (and don't correlate too highly).
    -  __Predictors are uncorrelated with 'external variables'__ - external variables that haven't been included in the regression model which influence the outcome variable.  If external variables do correlate with the predictors, then the conclusions we draw from the model are unreliable (because other variables exist that can predict the outcome)
    -  __Homoscedasticity__ - At each level of the predictor variable(s), the variance of the residual terms should be constant.  The residuals at each level of the predictor(s) should have the same variance (_homoscedasticity_); when the variances are very unequal there is _heteroscedasticity_
    -  __Independent Errors__ - For any two observations the residual terms should be uncorrelated (i.e. independent, sometimes called lack of _autocorrelation_).
        +  __Durbin-Watson test__ is a test that checks for serial correlations between errors (i.e. whether adjacent residuals are correlated).  The test statistic can vary between 0 and 4:
            *  2 means that the residuals are uncorrelated
            *  <2 means a positive correlation between adjacent residuals
            *  >2 means a negative correlation between adjacent residuals
            *  <1 or >3 means definite cause for concern
            *  Even if value is close to 2, can still be cause for concern (this is just a quick test, still depends on sample size and model)
            *  _Note:_ this test depends on the order; if you reorder the data, you'll get a different value
    -  __Normally Distributed Errors__ - assumed that the residuals in the model are random, normally distributed variables with a mean of 0, which means the differences between the model and the observed data are most frequently zero or close to zero and that differences greater than zero are rare.  _Note:_ does not mean that predictors have to be normally distributed.
    -  __Independence__ - All the values of the outcome variable are independent
    -  __Linearity__ - the mean values of the outcome variable for each increment of the predictor(s) lie along a straight line (i.e. this is a regression, we should be modeling a linear relationship)
*  __Cross-validation__ - A way to assess the accuracy of our model/ how well our model can predict the outcome in a different sample.  If a model is generalized, it can predict another sample well.  If the model is not generalized, it can't predict another sample well. 
    -  __Adjusted R^2__ - this adjusted value indicates the loss of predictive power (aka _shrinkage_).  The equation is __Stein's Formula__.  Note this is different than R^2, which uses _Wherry's equation_.
    -  __Data splitting__ - Usually split data randomly to 80% train, 20% test
    -  __Sample size__ - Depends on the size of effect (i.e. how well our predictors predict the outcome), how much statistical power we want and the number of predictors.  As a general rough guide, check out Figure 7.10 in the book (Page 275)
    -  __Multicollinearity__ - exists when there is a strong relationship between two or more predictors in a regression model.  _Perfect collinearity_ exists when at least one predictor is a perfect linear combination of the others (e.g. two predictors have a correlation coefficient of 1).  As _collinearity_ increases, these three problems arise:
        +  _Untrustworthy b_ - b coefficients increase as collinearity increase; big standard errors for b coefficients mean that bs are more variable across samples, thus b is less likely to represent the population, thus predictor equations will be unstable across samples
        +  _limits size of R_ - Having uncorrelated predictors gives you better _unique variance_
        +  _importance of predictors_ - multicollinearity makes it difficult to assess the individual importance of a predictor.  If the predictors are highly correlated then we can't tell which of say two variables is important
    -  __Testing collinearity__ - To test for collinearity, you can do the following:
        +  _correlation matrix_ shows relationships between variables to variables with anything above say above .8 as an indicator of really highly correlated and might be an issue, however it misses on detecting _multicollinearity_ since it only looks at one variable at a time
        +  __variance inflation factor (VIF)__ is a collinearity diagnostic that indicates whether a predictor has a strong linear relationship with another predictor(s) and is good for spotting relationships between multiple variables.  There's no hard and fast rules, but a 10 is a good value to start worrying or if the average _VIF_ is close to 1, then _multicollinearity_ might be biasing the model
        +  __tolerance statistic__ is the reciprocal of _variance inflation factor (i.e. 1/VIF)_ Any values below .1 are serious concerns
    -  __Plotting__ is a good way to check assumptions of regression to make sure the model generalizes beyond your sample.
* __Plotting__ - You can check assumptions quickly with graphs
    -  Graph the _standardized residuals_ against the _fitted (predicted) values_.  
        +  If the plot looks like a random array of dots, then it's good.
        +  If the dots seem to get more or less spread out over the graph (like a funnel shape) then is probably a violation of the assumption of _homogeneity of variance_.
        +  If the dots have a pattern to them (like a curved shape) then this is probably a violation of the assumption of _linearity_
        +  If the dots have a pattern and are more spread out at some points on the plot than others then this probably reflects violations of both _homogeneity of variance_ and _linearity_
    -  Graph the histogram of the residuals
        +  If the histogram looks like a normal distribution (and the _Q-Q plot_ looks like a diagonal line) then its good
        +  If the histogram looks non-normal, then things might not be good


#### Violating Linear Regression Assumptions  <a id="linregviolations"></a>
If assumptions are violated, then you cannot generalize your findings beyond your sample.  You can try correcting the samples using:

*  If residuals show problems with _heteroscedasticity_ or _non-normality_, you could try transforming the raw data (though might not affect the residuals)
*  If there's a violation of the _linearity_ assumption, then you could do a _logistic regression_ instead
*  You can also try a _robust regression (aka bootstrapping, robust statistics)_, which is an alternative to the _least squares regression_ when there's too many outliers or influential cases

## Logistic Regression  <a id="logreg"></a>

#### Logistic Regression - Types of Logistic Regressions  <a id="logregtypes"></a>
Logistic Regression is multiple regression, but with an outcome variable that is a categorical and predictor variables that are continuous or categorical.  There's two types of logistic regression:

*  __binary logistic regression__ is used to predict an outcome variable with a binary response (e.g. tumor cancerous or benign).
*  __multinomial (or polychotomous) logistic regression__ predicts an outcome variable that has more than two categories (e.g. favorite color).
*  There's no additional formulas between these two types of _logistic regression_; the reason is that _multinomial logistic regression_ just breaks the outcome variable down into a series of comparisons between two categories.  Say we have three outcome categories (A, B, C) then the analysis will consist of a series of two comparisons (e.g. A vs B and A vs C) or (A vs C and B vs C) or (B vs A and B vs C); basically you have to select a baseline category.


#### Comparing the Linear and Logistic Regression Models <a id="logregcompare"></a>
*  What are we measuring?  (A comparison between linear and logistic regressions)
*  __logit__ - In _linear/simple regression_, you predict the value of Y given X.  In _logistic regression_, you predict the probability of Y occuring given X.  You can't use _linear regression_ equations on a _logistic regression_ problem (i.e. outcome is a categorical instead of continuous) unless you do some data transformations (like _logit_, which logs the data)
*  __log-likelihood__ - In _linear/simple regression_, we used R^2 (the _Pearson correlation_) to check between observed values of the outcome and the values predicted by the regression model.  For _logistic regression_, we use the _log-likelihood_ given by `insert equation`, which is based on summing the probabilities associated with the predicted and actual outcomes (i.e. how much unexplained information there is after the model has been fitted).  A larger _log-likelihood_ means poor fitting model because there's more unexplained observations.  This is good for overall fit.  For partial fit, see _R-statistic_.
    -  __maximum-likelihood estimation (MLE)__ is a method to estimate the parameters of a statistical model (i.e. given a sample population, it estimates what most likely would have occured).  _MLE_ gives you the coefficients most likely to have occurred.
    -  __deviance (aka -2LL)__ is related to the _log-likelihood_ and its equation is `deviance =-2*log-likelihood` and sometimes used instead of the _log-likelihood_ because it has a _chi-square distribution_, which makes it easy to calculate the significance of the value.

#### Logistic Regression - Selecting Predictors <a id="logregpredictors"></a>
Using R, we can see how the model fits the data

*  __R-statistic__ is the partial correlation between the outcome variable and each of the predictor variables (opposed to _log-likelihood_ which is for the overall correlation instead of partial); can be between -1 (meaning as the the predictor value increases, likelihood of the outcome occurring decreases) to 1 (meaning that as the predictor variable increases, so does the likelihood of the event occurring).
*  __Hosmer and Lemeshow's RL^2 measure__ is the proportional reduction in the absolute value of the log-likelihood measure and thus is a measure of how much the badness of fit improves as a result of the inclusion of the predictor values.  Values range from 0 (predictors are useless at predicting the outcome variable) to 1 (model predicts the outcome variable perfectly)
*  __Cox and Snell's RCS^2__ and __Nagelkerke's RN^2__ are another way of getting a equivalent of R^2 in linear regression.
*  Information Criteria - Penalize a model for having more predictors
    -  __Akaike Information Criterion (AIC)__ for logistic regression is `AIC = -2LL +2k` where `-2LL` is the _deviance_ and `k` is the number of predictors
    -  __Bayes Information Criterion (BIC)__ for logistic regression is `BIC = -2LL +2k * log(n)` where `n` is the number of cases
*  How much are each predictor(s) contributing?
    -  __t-statistic__ - in linear regression, the regression coefficient _b_ and their standard errors created the _t-statistic_, which tells us how much a preditor was contributing
    -  __z-statistic (aka the Wald statistic)__ - in logistic regression, the _z-statistic_ tells us if the _b_ coefficient for that predictor is significantly different than zero.  If a coefficient _b_ is much greater than zero, then that predictor is making a significant contribution to the prediction of the outcome.  `z = b / (SEb)`.  Be warned, when the z-statistic value is large, the _standard error_ tends to be inflated, resulting in _z-statistic_ being underestimated.  An inflated _standard error_ increases the probability of rejecting a predictor as being significant when in reality it is making a significant contribution (i.e. _Type II error_)
*  So the coefficient _b_ in a _logistic regression_ is an exponent instead of multiplying, how does that work out / what does it mean?
    -  __odds__ as we normally know it is the probability of something happening / something not happening (e.g. probability becoming pregnant divided by / probability of not becoming pregnant).  This isn't the same as the _logistic regression_'s _odds ratio_ mentioned below.
    -  __odds ratio__ is the exponential of B (i.e., e^B), which is the change in odds resulting from a unit changes in the predictor.  E.g. calculate the odds of becoming pregnant when a condom is not used, calculate the odds of becoming pregnant when a condom is used, then calculate the proportionate change in odds between the two.  
        *  Formula is: `change in odds = odds after a unit change in the predictor / original odds`
        *  if the value is greater than 1, then it means as the predictor increases, the odds of the outcome occuring also increases
        *  if the value is less than 1, then it means as the predictor increases, the odds of the outcome occuring decrease
*  How do we know what order to put in / take out predictors in _logistic regression_?
    +  __forced entry method__ - the default method for conducting regression; place in one block and estimate parameters for each predictor
    +  __stepwise method__ - select a forward, backward, or both stepwise method (remember the issues with it though).

#### Logistic Regression - Assumptions  <a id="logregassumptions"></a>
What are Logistic Regression assumptions?

*  __Linearity__ - In linear regression we assume the outcome had a linear relationship with the predictors.  However, logistic regression's outcome is categorical so our linear regression assumptions don't apply.  Instead, with logistic regression we check if there are any linear relationships between any continuous predictors and the _logit_ of the outcome variable.
*  __Independence of errors__ - Same as linear regression where the cases of data are not related
*  __Multicollinearity__ - Same as linear regression where predictors should not be too highly correlated; can check with _tolerance statistic_ and _VIF statistics_, the eigenvalues of the scaled, uncentred cross-roducts matrix, the condition indices and the variance proportions.

#### Logistic Regression - Issues  <a id="logregissues"></a>
What are situations where Logistic Regression can cause trouble and return invalid results?

*  Not enough samples for specific categories (e.g. checking if people are happy, but only _n_ of 1 for people that are 80-year old, Buddhist, left-handed lesbian).  To spot, create a crosstabulations and look for really high _standard errors_.  To fix, collect more data.
*  __complete separation__ is where the outcome variable can be perfectly predicted by one variable or a combination of variables.  This causes issues in that there's no data in the middle probabilities (where we're not very sure of the probability) which can cause a wide range of curves/probabilities.  E.g. tell between cats and burglars, there's no cats that weigh more than 15kg and no burgulars that weigh less than 30kg.  The issue is usually caused when there's too many variables fitted to too few cases.  To fix, collect more data or use a simpler model.

## Comparing two means (i.e. t-test)  <a id="ttest"></a>

We've looked at relationships between variables, but sometimes we're interested in differences between groups of people.  This is useful in making causal inferences (e.g. two groups of people, one gets a sugar pill and other gets actual pill).  There's two different ways to expose people to experiments: _independent-means t-test_ and _dependent-means t-test_.  _t-tests_ are basically _regressions_ so it has much of the same assumptions (_parametric tests_ based on a _normal distribution_).

#### t-test - Independent and Dependent Means t-test <a id="ttestinddep"></a>

*  __independent-means t-test (aka between groups, independent-measures, independent-samples t-test)__ is used when there are two experimental conditions and different participants were assigned to each condition (e.g. Group 1 gets Treatment A, Group 2 gets Treatment B).  _independent t-tests_ also assume that scores in different treatment conditions are _independent_ (because they come from different people) and that there is _homogeneity of variance_ (but this only really matters if you have unequal group sizes.  Also if this is violated, you can use _Welch's t-test_ to adjust your data, which you should always do)
*  __dependent-means t-test (aka within subjects, repeated measures design, matched-pairs, pair-samples t-test, paired t-test)__ - is used when there are two experimental conditions and the same participants took part in both conditions of the experiment (e.g. Group 1 gets Treatment A, Group 2 gets Treatment B, then swap with Group 1 gets Treatment B, Group 2 gets Treatment A).  The sampling distribution of the differences between scores should be a _normal distribution_ (not the scores themselves)

#### t-test - Calculations <a id="ttestcalcs"></a>

Calculations for _t-tests_ can be viewed as if the t-test were a _linear regression (GLM)_.  The _independent t-test_ compares the means between two unrelated groups on the same continuous dependent variable  (e.g. blood pressure of patients who were given a drug vs control group of a placebo).  The _dependent t-test_ can be seen as a _within-subjects_ (aka _repeated-measures_) test (e.g. blood pressure of patients 'before' vs 'after' they received a drug)  Note: Don't really worry about the below, _R_ will do the calculations:

*  __Generalized Linear Model (GLM), (i.e. t-test as a linear regression)__ the _t-test_ can be thought of as a form of _linear regression_.  It'll allow you to test differences between two means.  
-  E.g. say you have two groups (one looks at picture of spider versus other that looks at a real spider and you measure their anxiety as heartrate).  The _t-test_ as a _GLM_ would setup each group (picture vs real spider) with the equation `outcome = (model) + error`.  We _dummy code_ the group variable (say Group 1 has value of 0 and Group 2 has value of 1).  We calculate the anxiety across both groups and then test whether the difference between group means is equal to 0.  The _t-statistic_ tests if the difference between group means is significantly different than zero.
*  __Independent t-test__ This means that when different groups participate in different conditions, pairs of scores will differ not just because of the experiment's manipulation, but also because of other sources of variance (e.g. IQ).  Therefore, we make comparisons on a 'per condition' basis'.  By looking at the 'per condition basis', we assess whether the difference between two sample means is statistically meaningful or by chance (through numerous sampling and looking at the sampling means distribution).
*  __Dependent t-test__ Since we put the same group through multiple experiments, we need to see the score in condition A compared to condition B and add up the differences (could be large or small) for all participants.  We divide by the number of participants in the group and get the average difference (on average, how much each person's score changed in condition A to condition B).  We then compare (divide) this by the standard error (which represents if we just randomly sampled from the population and not done any experiments).  This gives us the _test statistic_ that represents the model/error. 
  __t-statistic (aka test statistic)__ - _t-tests_ produce the _t-statistic_, which tells us how extreme a statistical estimate is.  If the experiment had any kind of effect, we expect the systematic variation to be much greater than the unsystematic variation (i.e. if _t_ is much greater than 1, there's an effect; if _t_ is less than 1, there's no effect.  If _t_ exceeds the critical value for an effect, we're confident that this reflects an effect of our independent variable)
  - __effect-size and t-tests__ - even though a _t-statistic_ might not be statistically significant, it doesn't mean that our effect is unimportant.  To check if an _effect-size_ is substantive, we use the following equation: `insert formula`
  - Reporting _t-tests_ should involve stating the finding to which the test relates, report the _test statistic_, _degrees of freedom_, an estimate of the _effect-size_, and the _probability_ of that test statistic.  E.g. On average, participants experienced greater anxiety from real spiders (M = 47.00, SE = 3.18), than from pictures of spiders (M=40.00, SE = 2.68).  This difference was not significant t(21.39) = -1.68, p>.05; however, it did represent a medium-sized effect r=.34

##One-way ANOVA  <a id="onewayanova"></a>

#### Comparing Several Means with ANOVA (Analysis of Variance GLM 1, aka One-way ANOVA)  <a id="onewayanovaconcept"></a>
If we want to compare more than two conditions, we use _one-way ANOVA_.  _t-tests_ checked whether two samples have the same mean while _ANOVA_ checks if three or more groups have the same means.  _ANOVA_ is an _omnibus_ test, which means it tests for an overall effect between all groups and does not say what group has a different mean.

*  __familywise (aka experimentwise error rate)__ is the error rate across statistical tests conducted on the same experimental data.  For example, we use _ANOVA_ instead of using multiple _t-tests_ on each pair of groups because the probability of _Type I_ errors would quickly stack (e.g. say .05 level of significance with 3 pairs would be .95 * .95 * .95 = .857 probability of no _Type I error_).  The more groups the more chance of an error.
*  __F-statistic (aka F-ratio, F-test)__ is a _test statistic_ that is the systematic variance divided by the unsystematic variance (i.e. a measure of how much the model has improved the prediction of the outcome compared to the level of inaccuracy of the model).  A large value (greater than at least 1) means a good model.  Basically, its the ratio of the model to its error (much like the _t-statistic_).  Say we have Groups A, B, C.  The _F-ratio_ tells us if the means of these three groups (as a whole) are different.  It can't tell what groups are different (e.g. if groups A and B are the same and C is different, if groups A, B, C are all different).  _F-ratio_ says that the experimental manipulation has some effect, but doesn't say what causes the effect.  The _F-ratio_ can be used to fit a multiple regression model and test the differences between the means (again, much like the _t-statistic_ with a linear regression).  The _F-ratio_ is based on the ratio of the improvement due to the _model sum of squares_ and the difference between the model and the observed data _residual sum of squares_.
*  When creating _dummy variables_ for _ANOVA_ groups (e.g. Group A, B, C) where we have one less dummy variable than the number of groups in the experiment.  We choose a _control group (aka baseline group)_ that acts as a baseline for other categories.  This baseline group is usually the one with no experiments (e.g. baseline is no viagra and others are low dose and high dose viagra).  When group sizes are unequal, the baseline group should have a large number of samples.
*  __Assessing variation (deviance)__
At every stage of the _ANOVA_ we're assessing _variation_ (or _deviance_) from a particular model using the formula `deviation = change in (observed - model)^2`.  We calculate the fit of the most basic model, then the fit of the best model; if the model is any good then it should fit the data significantly better than the basic model.
    - __Total Sum of Squares (aka TSS, SST)__ gives us the total amount of variation.  We calculate the difference between each observed data point and the grand mean.  We then square these differences and add them together to get us the _total sum of squares_.  
        + For _TSS_, the _degrees of freedom_ is one less than the total sample size (N-1); the mean is the constant being held.  The equation is `N-1`  E.g. we have 15 participants, the degrees of freedom is 14.
    - __grand variance__ is the variance between all scores, regardless of the experimental condition, and can be used to calculate the _total sum of squares_ with the equation `<insert equation>`
    - __Model Sum of Squares (SSm)__ tells us how much of the total variation can be explained by the fact that different data points come from different groups.  We calculate the difference between the mean of each group and the grand mean, square the differences, multiply each result by the number of participants within that group, then add the values for each group together.  
        + For _SSm_, the _degrees of freedom_ is one less than the number of parameters estimated.  The equation is: `k-1`  E.g. with 3 groups of participants, we have 2 degrees of freedom.
    - __Residual Sum of Squares (SSr)__ tells us how much of the variation cannot be explained by the model (e.g. individual differences in weight, testosterone, etc in particpants).  _SSr_ can be seen by looking at the difference between the score obtained by a person and the mean of the group that the person belongs.
        + For _SSr_, the _degrees of freedom_ are the total degrees of freedom (i.e. the total sample size `N`) minus the degrees of freedom for the model (i.e. the number of groups `k`).  The equation is: `N-k`.  E.g. with 15 participants and 3 groups, we have (14 - 2 = 12) degrees of freedom. 
    - __Mean Squares (MS)__ eliminates the bias of the number of scores (because _SSm_ and _SSr_ tells us the total, not the average).  _MS_ gives us the the _sum of squares_ divided by the _degrees of freedom_.
        + _MSm_ is the average amount of variation explained by the model (the systematic variation)
        + _MSr_ is the average amount of variation explained by extraneous variables (the unsystematic variation)
        + _F-ratio_ is the measure of the ratio of the variation explained by the model and the variation explained by unsystematic factors (i.e. how good the model is against how much error there is).  The equation is: `(_MSm_)/(_MSr_)`  

#### One-way ANOVA - Assumptions  <a id="onewayanovaassumptions"></a>

- _Homogeneity of variance_ - the variances of the groups are equal.  You can check this using _Levene's test_, which is an _ANOVA test_ done on the absolute differences between the observed data and the mean or median.  If _Levene's test_ is significant (i.e. p-value is less than .05) then the variances are significantly different and we shouldn't use _ANOVA_.
- Note that _ANOVA_ is a _robust test_, which means it doesn't matter if we break some assumptions (the _F-ratio is still accurate_).  When group sizes are equal, then _F-ratio_ is quite robust.  However, when group sizes are not equal, the accuracy of _F-ratio_ is affected by _skew_.
- If _homogeneity of variance_ has been violated, you can try different data transformations or you can try a different version of the _F-ratio_, like _Welch's F_.
- If there's distributional problems, there are other methods like _bootstrapping_ or _trimmed means_ and _M-estimators_ that can correct for it.

#### Comparing different groups (Planned Contrasts and Post Hoc Comparisons)  <a id="onewayanovacomparegroups"></a>

__Planned contrasts__ and __Post Hoc Comparisons__ are two methods that tells us which groups differ in an _ANOVA_.  This is important because _F-ratio_ tells if there's an effect, but not what group causes it.  _Planned contrasts_ and _Post hoc comparisons_ are a way of comparing different groups without causing _familywise error rate_.

__<a id="onewayanovaplannedcontrasts"></a>Planned Contrasts (aka Planned Comparisons)__ - break down the _variance_ accounted for by the model into component parts.  This is done when you have a specific hypotheses to test.  Some rules:

  -  If we have a control group, we compare this against the other groups
  -  Each comparison must compare only two 'chunks' of variation (e.g. low dose, high dose)
  -  Once a group has been singled out in a comparison, it can't be used in another comparison (we're slicing up the data like a cake, the same part of a cake can't be on multiple slices)
  -  When we're comparing two groups, we're just comparing the mean of the group to the mean of the other group
  -  E.g. We compare the average of the placebo group to the average of the low dose and high dose groups; if the standard errors are the same, the experimental group with the highest mean (high dose) will be significantly different from the mean of the placebo group.

Planned contrasts can be either: _Orthogonal comparisons_ or _Non-orthogonal comparisons_

  *  __Orthogonal comparisons__
    -  Now we want to answer, what groups do we compare?  Instead of creating dummy variables of 0 and 1 as we would the main _ANOVA_, we instead assign a _weight_.  Here's a few basic rules for that:
        -  Choose sensible comparisons
        -  Groups coded with positive weights will be compared against groups coded with negative weights (so assign one chunk a positive, the other a negative; it's arbitrary which one is positive or negative)
        -  The sum of weights for a comparison should be zero
        -  If a group is not involved in a comparison, it's automatically assigned a weight of 0 (which eliminates it from all calculations)
        -  For a given comparison, the weights assigned to the group in one chunk of variation should be equal to the number of groups in the opposite chunk of variation
    -  Let's go through an example of how to apply these:
        1.)  Example (Step 1): Chunk1 (Low Dose vs High Dose) vs Chunk2 (Placebo)
        2.)  Example (Step 2): Chunk1 (Positive weight) vs Chunk2 (Negative Weight)
        3.)  Example (Step 3): Chunk1 has two chunks ('Low Dose' and 'High Dose'), they each have a magnitude of 1 (for a sum of 2), weight of +1 each (for a sum of +2).  Chunk2 only has one chunk ('Placebo') so it has a magnitude of 2, weight of -2.
        4.)  The sum of the weights should add up on both sides so this means Chunk1 has weight of +1 (for Low Dose) and +1 (for High Dose) -2 (for Placebo) = `1+1-2=0`
        5.)  To compare just two groups (Low Dose and High Dose), we set the Placebo with a weight of 0 (which eliminates it from calculations).  We now have a comparison of Chunk1 (High Dose, 1 Magnitude, +1 Weight) vs Chunk2 (Low Dose, 1 Magnitude, -1 Weight).
        6.)  We now have the following dummy variables:

                  Dum_var1 (comp1), Dum_var2 (comp2), Product (comp1* comp2)
       Grp Placebo              -2,                0,                     0
       Grp Low Dose              1,               -1,                    -1
       Grp High Dose             1,                1,                     1
       Total                     0                 0                      0

        7.)  We want to do an __orthogonal__ comparison (basically make sure that _Total_ row is 0) and this means our comparisons are independent (so we can use _t-tests_).  The _p-values_ won't be correlated so we won't get _familywise errors_
        8.)  From the significance values of the _t-tests_ we can see if the experimental groups (Low Dose, High Dose) were significantly different from the control (Placebo).

  *  __Non-orthogonal comparisons__
    -  Similar to _orthogonal comparisons_ except the comparisons don't have to sum to zero.
    -  Example, you can compare across different levels, say Chunk1 is 'High Dose' only and Chunk2 is 'Placebo'

                  Dum_var1 (comp1), Dum_var2 (comp2), Product (comp1* comp2)
       Grp Placebo              -2,               -1,                     2
       Grp Low Dose              1,                0,                     0
       Grp High Dose             1,                1,                     1
       Total                     0                 0                      3
    
    -  The _p-values_ here are correlated so you'll need to be careful how to interpret the results.  Instead of .05 probability, you might want to have a more conservative level before accepting that a given comparison is statistically meaningful.

  *  __Polynomial Contrast (trend analysis)__ is a more complex way of looking at comparisons.  The different groups usually represent a different amount of a single common variable (e.g. amount of dosage for drug) and its effect
      -  __Linear Trend__ is where the group means increase proportionately (e.g. a positive linear trend is where the more of the drug we give, the more effect, looks like a linear line)
      -  __Quadratic Trend__ is where the the group means increase at the beginning, peak in the middle, then goes down (e.g. drug enhances performance if given a certain amount, then after a certain amount, the more you give the worse the performance); basically there's one direction change.  This requires at least 3 groups.
      -  __Cubic Trend__ is where the group means goes up, down, up (or vice versa); basically there's two direction changes.  This requires at least 4 groups.
      -  __Quartic Trend__ is where the group goes up, down, up, down (or vice versa); basically there's at least four direction changes. 

__<a id="onewayanovaposthoccomparison"></a>Post hoc comparisons (aka post hoc test, data mining, exploring data)__
  Compare every group (like you're doing multiple _t-tests_) but using a stricter acceptance criterion so that the familywise error rate doesn't rise above the acceptable .05.  This is done when you have no specific hypothesis to test.

  *  _post hoc comparisons_ is kinda cheating since this consits of __pairwise comparisons__ that compare all different combinations of the treatment groups.  However, _pairwise comparisons_ control _familywise errors_ by correcting the level of significance for each test so that the overall _Type I error rate_ across all comparisons remains at .05.  This is accomplished in multiple ways:
      +  __Bonferroni correction__ is a trade-off for controlling the _familywise error rate_ with a loss of _statistical power_; this means that the probability of rejecting an effect that does actually exist is increased (_Type II error_).  For example, we do 10 tests, we use .005 as our criterion for significant (instead of .05).  Formula is `<insert formula>`
      +  __Holm's method__ computes the _p-values_ for all of the pairs of groups in your data, then order them from smallest to largest.  We start similar with the normal _Bonferroni correction_ for the first comparison, but then each subsequent comparison the _p-value_ gets bigger (i.e. less conservative).  This method is _stepped_, which means we continue as long as comparisons are significant.  If there's a non-significant comparison we stop and assume all remaining comparisons are also non-significant.
      +  __Benjamini-Hochberg__'s method doesn't focus on making _Type I error rate_ like the above methods (basically, if we make a _Type I_ error, it's not that bad) and instead focuses on __False Discovery Rate (FDR)__, which is the proportion of falsely rejected null hypothesis to the total number of rejected null hypothesis.  The _Benjamini-Hochberg_ tries to keep the _FDR_ under control instead of the _familywise error rate_.  Like _Holm's method_, you comput the _p-value_ for all pairs of groups in your data and order them similarly (smallest to largest).  The difference is that this method is _step-up_, which means instead of working down the talbe we work up.  We say the bottom value is non-significant and continue moving up the table until we see a significant comparison, then assume that all other comparisons are also significant.
  *  So which _post hoc procedure_ should you use?  There's these and numerous others that R doesn't do out of the box.  When deciding, consider:
      1.  Whether the test controls the _Type I error rate_
      2.  Whether the test controls the _Type II error rate_ (i.e. has good statistical power)
      3.  Whether the test is reliable when the test assumptions of _ANOVA_ have been violated
  *  __Bonferroni__ and __Tukey's Honest Significant Difference (HSD) tests (aka Tukey's range test, Tukey method__ both control the _Type I error rate_, but are conservative so they lack _statistical power_.  _Bonferroni_ has more power when the number of comparisons is small.  _Tukey_ is more powerful when testing large numbers of means.
  *  _Benjamini-Hochberg_ has more power than _Holm's_; _Holm's_ has more power than _Bonferroni_.  Just remember _Benjamini-Hochberg_ doesn't attempt to control _Type I errors_.
  *  __Warning__: The above _post-hoc comparisons_ perform badly when group sizes are unequal and when population variances are different.  If this is the case, look up _bootstrapping_ or _trimmed means_ and _M-estimators_ (both of which include a bootstrap).  Use _bootstrap_ to control _Type I errors_ and _M-estimators_ if you want more _statistical power_.
  *  Note: for some reason, the effect size _r^2_ specifically for _ANOVAs_ are called __eta squared__ and looks like `n^2`


## Comparing Several Means with ANCOVA (Analysis of Covariance GLM 2) <a id="ancova"></a>
_ANCOVA_ is like _ANOVA_, but also includes __covariates__, which are one or more continuous variables that are not part of the main experimental manipulation, but have an influence on the outcome (aka dependent variable).  We include _covariates_ because of two reaons:

  1. To reduce within-group error variance: In _ANOVA_, if we can explain some of the 'unexplained' variance _(SSr)__ in terms of _covariates_, then we reduce the error variance, allowing us to more accurately assess the effect of the independent variable _(SSm)_ 
  2. Elimination of confounds: this means that an experiment has unmeasured variables that confound the results (i.e. variables other than the experimental manipulation that affect the outcome variable).  _ANCOVA_ removes the bias from the variables that influence the independent variable.
  *  E.g. Say we look at the example with the effects of Viagra on the libido; the _covariates_ would be other things like medication (e.g. antidepressants).  _ANCOVA_ attempts to measure these continuous variables and include them in the regression model.

#### ANCOVA Assumptions <a id="ancovaassumptions"></a>

ANCOVA has the same assumptions as any linear model with these two additional considerations:
  1. independence of the covariate and treatment effect - Unexplained variance _(SSr)_ should only overlap with the _Variance explained by Covariate_ (and not with _Variance explained by the independent variable_ or else the effect is obscured).
  2. __homogeneity of regression slopes__ - the slopes of the different regression lines should all be equivalent (i.e. parallel among groups)  For example, if there's a positive relationship between the covariate and the outcome in one group, then we assume there's a positive relationship for all other groups

#### ANCOVA Calculations <a id="ancovacalculations"></a>

*  __Sum of Squares (Type I, II, III, IV)__
  -  Remember that order matters when evaluating
  -  __Type I Sum of Squares__ We put one predictor into the model first (it gets evaluated), then the second (then it gets evaluated), etc.  Use if the variables are completely independent of each other (unlikely), then _Type I SS_ can evaluate the true effect of each variable.  
  -  __Type II Sum of Squares__ Use if you're interested in main effects; it gives an accurate picture of a main effect because they're evaluated ignoring the effect of any interactions involving the main effect under consideration.  However, if an interaction is present, then _Type II_ can't evaluate main effects (because variance from the interaction term is attributed to them)
  -  __Type III Sum of Squares__ is usually the default; use this when sample sizes are unequal, however they work only when predictors are encoded with _orthogonal contrasts_ instead of a _non-orthogonal contrast_.
  -  __Type IV Sum of Squares__ is the same as _Type III_, but is designed for situations in which there's missing data.
*  __Interpreting the covariate__ - Draw a scatterplot of the covariate against the outcome (e.g. participant's libido as y, partner's libido as x).  If covariate is positive, then there's a positive relationship where the covariate increases, so does the outcome.  If covariate is negative, then there's a negative relationship where the covariate increases, the outcome decreases.
*  __Partial eta squared (aka partial n^2)__ is an effect size measure for _ANCOVA_ (kinda similar to _eta squared (n^2)_ in _ANOVA_ or _r^2_).  This differs from _eta squared_ in that it looks not at the proportion of total variance that a variable explains, but at the proportion of variance that a variable explains that is not explained by other variables in the analysis.


## Factorial ANOVA <a id="factorialanova"></a>
_ANOVA_ and _ANCOVA_ looks at differences between groups with only a single independent variable (i.e. just one variable being manipulated).  _Factorial ANOVA_ looks at differences between groups with two or more independent variables.  When there are two or more independent variables, it is __factorial design__ (because sometimes variables are known as _factors_).  There are multiple types of this design including:

  -  __independent factorial design__ - there are several independent variables and each has been measured using different entities 
  -  __repeated-measures (related) factorial design__ - there are several independent variables measured, but the same entities have been used in all conditions
  -  __mixed design__ - several independent variables have been measured, some have been measured with different entities and some used the same entities

#### ANOVA Naming Convention  <a id="factorialanovanaming"></a>
The names of _ANOVA_s can seem confusing, but are easy to break down.  We're simply saying how many independent variables and how they were measured.  This means:

1.  The quantity of independent variables (e.g. one independent variable translates to 'one-way independent')
2.  Are the people being measured the same or different participants?
    - If the same participants, we say __repeated measures__
    - If different participants, we say __independent__
    - If there are two or more independent variables, then it's possible some variables use the same participants while others use different participants so we say __mixed__

#### Independent Factorial Design ANOVA (aka Between Groups, Between-Subjects ANOVA, GLM 3)  <a id="factorialanovaindependent"></a>
An example of _Factorial ANOVA_ using two independent variables is looking at the effects of alcohol on mate selection at nightclubs.  The hypothesis was that after alcohol has been consumed (the first independent variable), subjective perceptions of physical attractiveness would become more inaccurate.  Say we're also interested if this effect is different for men and women (this is the second independent variable).  We break groups into gender (male, female), drinks (none, 2 pints, 4 pints), and measured based off an independent assessment of attractiveness (say 1 to 100).

*  Calculations for a _Two-way ANOVA_ is very similar to a _One-way ANOVA_ with the exception that in a _Two-way ANOVA_ the variance that is explained by the experiment (_SSm_) is broken down into the following:
    -  _SSa (aka main effect of variable A)_ is the variance explained by Variable A
    -  _SSb (aka main effect of variable B)_ - is the variance explained by Variable B
    -  _SSa*b (aka the interaction effect)_ - is the variance explained by the interaction of Variable A and Variable B
*  For the _F-ratio_, each one of the above effects (_SSa_, _SSb_, and _SSa*b_) all have their own _F-ratio_ so that we can tell if each effect happened by chance or reflects an effect of our experimental manipulations
*  __interaction graphs__ can help you interpret and visualize significant interaction effects
*  Once again, we run _Levene's test_ to see if there are any significant differences between group variances (_homogeneity of variance_); for example, check if the variance in attractiveness differs across all different gender and alcohol group combinations.
*  For choosing _contrasts_, we need to define contrasts for all of the independent variables.  If we use _Type II sums of squares_, then we have to use an _orthogonal contrast_.  If we want to look at the _interaction effect_ (i.e. the effect of one independent variable at individual levels of the other independent variable), we use a technique called __simple effects analysis__
*  Like _ANOVA_, do a _post hoc test_ on the _main effects_(e.g. _SSa_, _SSb_) in order to see where the differences between groups are.  If you want to see the _interaction effect_ (e.g. _SSa*b_) then look at _contrasts_.


#### Repeated-Measures Factorial Designs ANOVA (aka Within Groups, Within-Subjects ANOVA, ANOVA for correlated samples, GLM 4)  <a id="factorialanovarepeatedmeasures"></a>
_Repeated measures_ is when the same entities participate in all conditions of the experiment.  A _Repeated-Measures ANOVA_ is like a regular _ANOVA_, but it violates the assumption that scores in different conditions are independent (scores are likely to be related because they're from the same people), which will cause the _F-test_ to lack accuracy.

*  Since the _F-test_ lacks accuracy, we have to make a different assumption called the __assumption of sphericity (aka circularity)__, which is an assumption about the structure of the covariance matrix; we assume the relationship between pairs of experimental conditions is similar (More precisely, the variances of the differences between treatment levels is about the same).  That means we calculate the differences between pairs of scores for all combinations of the treatment level.  We then calculate the variance of these differences.  As such, _sphericity_ is only an issue with three or more variables.
*  __compound symmetry__ is a stricter requirement than sphericity (if this is met, so is _sphericity_; if this is not met, you still need to check for _sphericity_).  This requirement is true when both the variances across conditions are equal (same as the _homogeneity of variance assumption_ in _between-group designs_) and the covariances between pairs of conditions are equal.
*  __Mauchly's test__ is a way to assess _sphericity_ and let you know if the variances of the differences between conditions are equal.  If _Mauchly's test_ is significant, then we have to be wary of the _F-ratio_, otherwise you're okay.  However, remember that this is also dependent on sample size; large samples can have small deviations from sphericity to be significant while in small samples large violations can be non-significant.
*  Violating _sphericity_ means a loss of statistical power and it also impacts _post hoc tests_.  If _sphericity_ is definitely not violated, use _Tukey's test_.  If _sphericity_ is violated, then using the _Bonferroni method_ is usually the most robust of the univariate techniques in terms of power and control of _Type I error rate_.
*  __epsilon__ is a descriptive statistic that indicates the degree to which _sphericity_ has been violated.  The formula is: `epsilon = 1/(k-1)` where `k` is the number of repeated-measures conditions.  If epsilon is closer to 1, the more homogeneous the variances of differences (and closer to the data being spherical).
*  If data violates the _assumption of sphericity_, then we can apply a correction factor that is applied to the degrees of freedom used to assess the _F-ratio_ (or we can not use the _F-ratio_).
    -  __Greenhouse-Geisser correction__ - Use if epsilon is less than .75 or if nothing is known about sphericity (otherwise the correction is too conservative and there's too many false null hypothesis that fail to be rejected).  Go look up how to do this.
    -  __Huynh-Feldt correction__ - Use if estimate is more than .75
    -  Use a different test other than the _F-ratio_ like using multivariate test statistics (_multivariate analysis_, _MANOVA_) because they don't depend on the _assumption of sphericity_.  There's a trade-off in power between _univariate_ and _multivariate tests_.  Go look up how to do this.
    -  Analyze the data as a _multilevel model_ so we can interpret the model coefficients without worrying about sphericity because dummy-coding our group variables ensures that we only compare two things at once (and sphericity is only an issue when comparing three or more means).  Not for the faint of heart (think about doing a _multivariate test_ first)
*  The calculations for _repeated-measures ANOVA_ can be seen as `SSt = SSb + SSw` where _SSt_ is the Total Variability, _SSb_ is the Between-Participant variability and _SSw_ is the Within-Participant Variability.  The main difference is that the _SSw_ ('Within-Participant Sum of Squares') is now broken down into:
    -  _SSw_ looks at the variation with an actual person, not within a group of people; some of this variation is explained by the effect of the experiment and some of it is just random fluctuation
    -  _SSm_ (Model Sum of Squares) is the effect of the experiment
    -  _SSr_ (Residual Sum of Squares) represents the error; the variation not explained by the experiment
*  Example of this is if we tested a group of participants (Person1, Person2, Person3, Person4, Person5, Person6) each eating multiple gross foods (NastyFoodA, NastyFoodB, NastyFoodC, NastyFoodD) and measuring how long it takes to retch.  The independent variable is the type of food eaten (e.g. NastyFoodA) since that's what we're manipulating, the dependent variable is the time to retch (since it depends on the food).  _SSw_ represents each participant's own tolerance level for nasty food.  _SSm_ is how much our manipulation (change in food) calculates the mean for each level of the independent variable (mean time to retch for say NastyFoodA, NastyFoodB, etc) and compares this to the overall mean of all foods.  _SSr_ is just the error.
*  To check _effect size_ for _repeated-measures designs_, we calculate __omega squared (w^2)__.  This formula is slightly different than in _one-way independent ANOVAs_ and it looks pretty scary so google it up.
*  __Factorial repeated-measures designs__ is just extending the _repeated-measures designs_ to include a second or more independent variable.  If there's two independent measures, then it's a _two-way repeated-measures ANOVA_.  If there's three independent measures, then it's a _three-way repeated-measures ANOVA_, etc.  Again we'll need to be careful about interaction effects of multiple independent variables.

#### Mixed Designs ANOVA (aka split-plot ANOVA, GLM 5)  <a id="factorialanovamixeddesigns"></a>
__Mixed Designs ANOVA__ is where you compare several means when there are two or more independent variables and at least one independent variable has been measured using the same participants (_repeated-measures_) and at least another independent variable has been measured using different participants (_independent measures design_).

*  You can explore the data similar to a _repeated-measures design ANOVA_ or as a _multilevel model_.  If you're using the _ANOVA_ approach, check for the _assumption of sphericity_, then choose what you want to _contrast_, compute the main model (might need to run a robust version of the test), then follow up with your _post hoc tests_.
*  At this point you'll realize why you want to limit the number of independent variables that you include.  The interpretation gets increasingly difficult with the more variables you have.

## Non-parametric Tests  <a id="nonparametric"></a>
_Non-parametric tests_ are statistical procedures that make fewer assumptions about the type of data, mostly on the principle of _ranking_ the data.  For example, find the lowest score (rank of 1), then next highest score (rank of 2) and so forth.  We then carry out the analysis on the rank instead of the actual data.  These tests include:

-  _Wilcoxon rank-sum test (aka Mann-Whitney test)_
-  _Wilcoxon signed-rank test_
-  _Kruskal-Wallis test_
-  _Friedman's test_
-  _McNemar's test (aka McNemar's Z-test)_

#### Wilcoxon's rank-sum test  <a id="wilcoxonranksum"></a>
The __Wilcoxon's rank-sum test (aka WRS, Mann-Whitney test, Mann-Whitney-Wilcoxon test, Wilcoxon-Mann-Whitney test)__ is used for the non-parametric equivalent of a _independent t-test_ (i.e. if you want to test the differences between two conditions and different participants have been used in each condition).  The theory is that you rank the data and ignore the group to which a person belonged (say we're looking at depression levels between ecstasy and alcohol users).

+  A _tied rank_ is where multiple values are given the same rank.  Say we had two scores of 6 and they would've both ranked 3 and 4, we then take the average of the two potential ranks (3+4/2=3.5)
+  We need to correct for the number of people in the group (or else larger groups would have larger ranks) so we calculate the _mean rank_ by taking the mean of the numbers with this formula: `mean rank = (N(N+1))/2` so say you have
+  Now for each group, calculate `W = sum of ranks - mean rank`
+  __Monte Carlo method__ is an _exact approach_ to obtain the significance level (_p-value_).  This means creating lots of data sets that match the sample, but instead of putting people into the correct groups, they're put into a random group MANY times.  It then compares the difference that appears in the data when the null hypothesis is true is as large as the difference in your data.  This is good for small samples.  Keep in mind:
    -  This process takes a while because it's done MANY times; with an increase in sample size, the length of time takes more and more
    -  If you have _tied ranks_ in the data, you can't use this method because this is an exact approach.
+  If your sample size is large (say larger than 40) or if you have _tied ranks_, try a _normal approximation approach_ to calculate the significance level (_p-value_).  This doesn't need a normal distribution; it just assumes that the sampling distribution of the _W_ statistic is normal, which means that the standard error can be computed that can be used to calculate a _z_ and then a _p-value_.
    -  With a _normal approximation_, you have an option to do a __continuity correction__, which tries to smooth out the distribution (since there's _tied ranks_), but comes at the expensve of maybe making your _p-value_ a little too high.

####Wilcoxon Signed-Rank Test  <a id="wilcoxonsignedrank"></a>
__Wilcoxon signed-rank test__ is used in situations where there are two sets of scores to compare, but these scores come from the same participants.  This is the _non-parametric_ equivalent of a _dependent t-test_.  The theory is that we're looking at the differences between scores in the two conditions you're comparing (e.g. see the effects of two drugs, one measured on Saturday and again on Wednesday for the same participants).  The main difference is that there's a sign (positive or negative) assigned to the rank.

+  For each group, if the difference in scores are the same (from Sat to Wed) then we exclude this data from the ranking
+  We make a note of the sign (positve or negative) and then rank the differences (starting with the smallest) while ignoring the sign
+  We deal with _tied ranks_ in the same way as before, we average them (e.g. say we had two scores of 6 and they would've both ranked 3 and 4, we then take the average of the two potential ranks (3+4/2=3.5))
+  __T+__ is where we collect the ranks that came from a positive difference and add them up
+  __T-__ is where we collect the ranks that came from a negative difference and add them up
+  We do _T+_ and _T-_ calculations for both groups (say alcohol and ecstasy)
+  To calculate the significance of the test statistic _T_, we need:
    -  The mean __T__, which is given by `T = (n(n+1)/4)`
    -  The standard error __SEt__, which is given by `SEt = sqrt( (n(n+1)(2n+1))/24 )` 
    -  With the above values, we can convert the test statistic into a _z-score_, which then tells us if the test is significant based on the _p-value_.  This will tell us if there is a significant difference between depression scores on Wed and Sat for both ecstasy and alcohol.

####Kruskal-Wallis Test  <a id="kruskalwallis"></a>
__Kruskal-Wallis test__ looks at the differences between several independent groups.  This is the _non-parametric_ counterpart to the _one-way independent ANOVA_.  The theory is also based on ranked data (ordering scores from lowest to highest ignoring the group that the score belongs, lowest score of 1 and going up).

+  Once you rank the data you then collect the scores back into their groups and add up the ranks for each group.  The sum of ranks for each group is denoted by __Ri__ where _i_ is used to denote the particular group
+  Once the sum of ranks (_Ri_) has been calculated for each group, the test statistic __H__ is calculated `insert formula`.  _H_ has a _chi-square distribution_ and for this distribution, there is one value for the _degrees of freedom_, which is one less than the number of groups `(k-1)`
+  You can visualize the comparisons by group really well by using a _boxplot_.  For example, sperm count as y, number of soya meals as groups (no soya meals, 1 soya meal, 4 soya meals, 7 soya meals a week) on x 
+  To do a _non-parametric_ _post hoc procedure_ is to do a _Wilcoxon rank-sum test_ on all possible comparisons; this involves taking the difference between the mean ranks of the different groups and comparing this to a value based on the value of _z_ (corrected for the number of comparisons being done) and a constant based on the total sample size and the sample size in the two groups being compared.  The inequality is given in formula `insert formula`
+  __Jonckheere-Terpstra test (aka Jonckheere test)__ is used when you think that groups are different and you want to hypothesize that there's a trend (i.e. an ordered pattern to the medians of the groups you're comparing).

####Friedman's ANOVA  <a id="friedmansanova"></a>
__Friedman's ANOVA__ looks at differences between several related groups.  This is used for testing differences between conditions where there are more than two conditions and the same participants have been used in all conditions (each case contributes several scores to the data).  The theory is also based on _ranked data_.

*  In your columns (say you're measuring weight at different times), you put down the weight at different times (start, after 1 month, after 2 months).  Each person is a row.
*  Now go through each row and give rank per column (e.g. if someone lost weight each month, we would have a rank of '3 for start', '2 for the first month', '1 for the last month'
*  Now add up the ranks across each column (e.g. sum rank for the 'start' column, sum rank for the '1 month' column, etc.), this is the __Ri__
*  Then calculate the test statistic __Fr__ given by the equation `insert equation`.  When the number of people tested is large (bigger than 10), this test statistic has a _chi-square distribution_ and there is one value for the degrees of freedom, which is one less than the number of groups `(k-1)`
*  Again, you can follow up on the main analysis with _post hoc tests_ and checking if the _differences_ is significant.
  
## Multivariate Analysis of Variance (MANOVA)  <a id="manova"></a>

#### Matrices  <a id="matrices"></a>

You should know about __Matrices__ before learning about _MANOVA_.

-  __matrix__ is simply a collection of numbers arranged in columns and rows.  For example, a 2*3 matrix is 2 rows, 3 columns of data.
-  __components (aka elements)__ are the values within a _matrix_
-  __square matrix__ is a matrix where there are an equal number of columns and rows.
    *  In a _square matrix_, the __diagonal component__ are the values that lie on the diagonal line from the top left element to the bottom right element.  The __off-diagonal component__ are the other elements.
    *  In a _square matrix_, the __identity matrix__ is the _diagonal component_ elements are equal to 1 and the _off-diagonal component_ are 0
    *  A __row vector__ is a special case of a matrix where there are data for only one entity (e.g. just a row of data, like a single person's score on four differnt variables for a 1*4 vector)
    *  A __column vector__ is a special case of a matrix where there are data for only one column (e.g. just a column of data, like five participants' scores on one variable for a 5*1 vector)

#### MANOVA - When to use MANOVA <a id="manovauses"></a>

__MANOVA (multivariate analysis of variance)__ is used when you want to compare groups on several dependent variables (outcomes); when you can compare against several dependent variables, this is known as a __multivariate test__ (as opposed to _ANOVA_ that can only be used on one dependent varialbe, which is known as a __univariate test__).  We can use _MANOVA_ when there is one independent variable or when there are several.

*  Using _MANOVA_ instead of _ANOVA_: The reason why we don't do a separate _ANOVA_ for each dependent variable is similar to why we don't do multiple _t-tests_ (if we conduct more tests on the same data, the more we inflate the _familywise error rate_, thus the increased chance of _Type I error_).  Also, if we do a bunch of _ANOVA_s, the relationship between dependent variables is ignored, whereas _MANOVA_ includes all dependent variables in the same analysis and can account for the relationship between dependent variables.  _ANOVA_ can only tell us if groups differ on a single dimension whereas _MANOVA_ has the power to detect whether groups differ along a combination of dimensions.  For example, we can't tell happiness by individual factors of: work, social, sexual, and self-esteem, but you can tell by the combination of factors (i.e. _MANOVA_ has greater power to detect an effect)
*  When to use _MANOVA_ - Since _MANOVA_ is used to compare several dependent variables, it makes sense that it depends a lot on the correlation between dependent variables.
    +  Use _MANOVA_ with highly negatively correlated dependent variables (best scenario); _MANOVA_ works acceptably well with moderately correlated dependent variables in either direction  
    +  Don't use _MANOVA_ if there is no correlation because it is useless.  Also, as the correlation between dependent variables increases, the power of _MANOVA_ decreases.
    +  Note: The power of _MANOVA_ depends on a combination of the correlation between dependent variables, the effect size to be detected, and the pattern of group differences (e.g. if measures are somewhat different, if group differences are in the same direction for each measure)

#### MANOVA - Variances and Covariances  <a id="manovavariancecovariance"></a>

We're interested in the ratio of the effect of the _systematic variance_ to the _unsystematic variance_.  In _ANOVA_, these variances were single values.  In _MANOVA_, each of these is a matrix containing many variances and covariances.

*  __hypothesis SSCP (aka H, hypothesis sum of squares and cross-products matrix, model SSCP Matrix)__ is the matrix that represents the systematic variance (similar to the _model sum of squares for all variables (SSm)_)
*  __error SSCP (aka E, error sum of squares and cross-products matrix, residual SSCP Matrix)__ is the matrix that represents the unsystematic variance (the _residual sums of squares for all variables (SSr)_)
*  __total SSCP (aka T, total sum of sqaures and cross-products matrix)__ is the matrix that represents the total amount of variance present for each dependent variable (the _total sums of squares for each dependent variable (SSt)_)
*  Note: The above are all _sum of squares and cross-products (SSCP) matrices_.  The _sum of squares_ of a variable is the total squared difference between the observed values and the mean value.  The _cross-product_ is the total combined error between two variables
*  _MANOVA's test statistic_ isn't as easy to calculate as _ANOVA_'s _F-ratio_.  For _ANOVA_'s _test statistic_, we just divide the _systematic variance (SSm)_ by the _unsystematic variance (SSr)_.  You can't just divide matrices so we can't do this for _MANOVA_.  Instead, the _test statistic_, which for _MANOVA_ is called __HE^-1__, is calculated from multiplying the _model SSCP_ with the inverse of the _residual SSCP_.  Unlike _ANOVA_'s _test statistic_, we don't get a single value and instead get a matrix with several values (with _p^2_ values, where _p_ is the number of dependent variables)

#### MANOVA - Predicting  <a id="manovapredicting"></a>

In _ANOVA_, we try to predict an outcome (dependent variable) using a combination of predictor variables (independent variables).  In essence, what we're doing with _MANOVA_ is predicting an independent variable from a set of dependent variables).

*  __variates (aka latent variables, factors)__ are linear combinations of the dependent variables
*  __discriminant function variates (aka discriminant functions)__ is when we use linear variates to predict which group a person belongs to (i.e. we're using them to discriminate groups of people).  For example, which group of treatment did they receive (CBT, BT, no treatment)
*  __eigenvector (aka characteristic vector)__ are the _vectors_ associated with a given matrix that are unchanged by transformation of that matrix to a diagonal matrix
    -  A __vector__ is just a set of numbers that tells us the location of a line in geometric space; it has both _magnitude_ (how long it is) and _direction_.  For example, vector a=(8, 13).
    -  A __diagonal matrix__ is simply a matrix in which the the _off-diagonal elements_ are zero and by changing _HE^-1_ to a _diagonal matrix_ we eliminate all of the _off-diagonal elements_ (thus reducing the number of values that we must consider for significance testing)
    -  Example, say that supermodels make more salary (y value) when their attractiveness (x value) increases.  Let's say the two values are correlated and when scatterplotted, forms an ellipse.  If we draw two linse to measure the length and height of the ellipse, then they are the _eigenvectors_ of the original correlation matrix for these two variables.  The two lines (one for height, one for width of the oval) are perpendicular (90 degrees, which means they're independent of one another).  We can add additional variables (e.g. experience of the supermodel) then the scatterplot gets additional dimension(s).
    -  Interpreting eigenvectors - If the shape is a circle (or sphere if 3D, etc) then there is no correlation (eigenvector value near 1).  If the shape is an ellipse scatterplot that looks like a diagonal straight line (height of eigenvector is almost 0), then there is high correlation.
*  __eigenvalues (aka characteristic roots)__ is the length (distance from one end of the _eigenvector_ to the other).  By looking at all the _eigenvalues_, we can tell the dimensions of the data (which tells us how evenly or non-evenly the variances of the matrix are distributed).

#### MANOVA - Eigenvalues  <a id="manovaeigenvalues"></a>

_Eigenvalues_ are the equivalent to the _F-ratio_ in an _ANOVA_.  Now we need to compare how large these values are compared to what we would expect by chance alone.  We can do that a few ways.
    -  __Pillai-Bartlett trace (aka V, Pillai's trace)__ is the sum of the proportion of _explained variance_ on the discriminant funtions; it's similar to the ratio of _SSm/SSt_, which is known as __R^2__.  Formula is `insert formula`  This should usually be your default _test statistic_.
    -  __Hotelling-Lawley trace (aka Hotelling's T^2)__ is the sum of the _eigenvalues_ for each variate.  This _test statistic_ is the sum of _SSm/SSt_ for each of the variates and so it compares directly to the _F-ratio_ in _ANOVA_.
    -  __Wilk's lambda__ is the product of the _unexplained variance_ on each of the variates.  This _test statistic_ represents the ratio of error variance to total variance (_SSr/SSt)_ for each variate.  Note that large eigenvalues (which represent a large experimental effect) lead to small values of _Wilk's lambda_, which means statistical significance is found when _Wilk's lambda_ is small.
    -  __Roy's largest root__ is the _eigenvalue_ of the first variate.  It's basically the same the _Hotelling-Lawley trace_, but only for the first variate.  This _test statistic_ represents the proportion of _explained variance_ to _unexplained variance_ (_SSm/SSr)_ for the first discriminant function.  _Roy's root_ represents the maximum possible _between-group_ difference given nad is in many cases the most powerful.
    -  Choosing a method
        *  If the sample sizes are small or moderate, the four approaches differ little in terms of power.
        *  If group differences are concentrated on the first variate, then _Roy's statistic_ should be the most powerful (since it takes account of only the first variate), followed by _Hotelling's trace_, _Wilk's lambda_, and _Pillai's trace_.  If groups differ along more than one variate, the power order is reversed.
        *  It's recommended to use fewer than 10 dependent variables unless sample sizes are large.
        *  When sample sizes are unequal, use the _Pillai-Bartlett trace_ since it's the most robust to violations of assumptions.  Make sure to check the _homogeneity of covariance matrices_; if they seem _homogeneous_ and if the _assumption of multivariate normality_ is tenable, then _Pillai-Bartlett trace_ is assumed to be accurate.  

#### MANOVA - Assumptions  <a id="manovaassumptions"></a>

_MANOVA_ Assumptions are similar to _ANOVA_ assumptions, but extends them to _multivariate_ cases instead of _univariate_ cases:

*  _Independence_: observations are statistically independent.  This can be checked the same as with univariate cases (like _ANOVA_)
*  _Random Sampling_: data should be randomly sampled from the population of interest and measured at an interval level.  This can be checked the same as with univariate cases (like _ANOVA_)
*  _Multivariate normality_: In _ANOVA_ we assumed that our dependent variable is normally distributed within each group.  In _MANOVA_ we assume that the dependent variables (collectively) have multivariate normality within groups.  This can be tested with the _Shapiro test_ that we used similar to the univariate cases
*  _Homogeneity of covariance matrices_: In _ANOVA_ we assume that the variances in each group are roughly equal (i.e. _homogeneity of variance_).  In _MANOVA_, we must assume that this is true for each dependent variable, but also that the correlation between any two dependent variables is the same in all groups.  We can check this assumption by testing whether the population __variance-covariance matrices__ of the different groups in the analysis are equal.  We can test the _assumption of equality of covariance matrices_ using _Box's test_
*  __Box's test__ is used to test the _assumption of equality of covariance matrices_; it'll be non-significant if the matrices are the same.  If sample sizes are equal, ignore _Box's test_ because it's unstable.  If group sizes are different, then the robustness of the _MANOVA_ cannot be assumed.  The more dependent variables you have measured, the greater the differences in sample sizes, the more distorted the probability values become.

#### MANOVA - Which group caused the effect  <a id="manovawhichgroupeffect"></a>

You can figure out which group caused the effect a couple different ways.  After a _MANOVA_, you can either do a _discriminant function analysis_ or do a different univariate _ANOVA_ for each dependent variable.

*  __ANOVA__ One way to figure out which group caused the effect (not recommended) is to do a univariate _ANOVA_ for each dependent variable, which are then followed up with _contrasts_.  The idea behind this follow-up _ANOVA_ approach is that the _ANOVA_ hides behind the _MANOVA_ (so it doesn't run into the same errors as multiple _ANOVAs_ by itself), but is dangerous because a significant _MANOVA_ reflects a significant difference for one, but not all of the dependent variables.  If you do this approach, then _Bonferroni corrections_ should be applied to the level at which you accept significance.  
*  __discriminant function analysis (aka DFA, discriminant analysis)__ finds the linear combination(s) of the dependent variable that best separates (or discriminates) the groups.  The main advantage for this is that it reduces and explains the dependent variables in terms of a set of underlying dimensions thought to reflect substantive theoretical dimensions.
    -  It might be useful to look at the __discriminant scores__, which are scores for each participant on each variate.  These scores are useful because the variates that the analysis identifies may represent underlying constructs, which if they're identified is useful for interpretation to know what a participcant scores on each dimension.
    -  To find out which groups are discriminated by a variate, plot the _discriminant scores_.  Split the vertical and horizontal axes at the midpoint and look at which groups tend to fall on either side of the line.  The variate plotted on a given axis is discriminating between groups that fall on different sides of the line (i.e. the midpoint)

####MANOVA - Robust Methods  <a id="manovarobust"></a>

Two robust methods for _MANOVA_ are __Munzel and Brunner's method (aka Munzel-Brunner rank order test)__ and __Choi and Marden's robust test (aka Choi-Marden Multivariate Rank Test__ (which is an extension of the _Kruskal-Wallis one-way analysis of variance by ranks test_)

## Factor Analysis   <a id="factoranalysis"></a>
So the idea is that _factor analysis_ identifies clusters of variables that relate to each other.  We check to make sure our variables aren't related to each other too much or too little using a _correlation matrix_.  We then check if there's any issues (e.g. enough sample size), then decide how many factors we want to keep (_factor extraction_), then finally decide which variables go to which factors (_factor loading_).  We finally consider whether items you have are reliable measures of what you're trying to measure.

*  __factors (aka latent variables)__ are things that cannot directly be measured.  For example, we can't directly measure 'burnout' (someone who has been working very hard on a project for a prolonged time suddenly finds themselves out of motivation).  However, we can measure different aspects of burnout (get an idea of motivation, stress levels, whether the person has any new ideas)
*  __factor analysis__ is a technique for for identifying groups or clusters of variables.  It has three main uses:
    1. to understand the structure of a set of variables (e.g. try to find the _latent variable_  for 'intelligence' or 'burnout')
    2. to construct a questionnaire to measure an underlying variable (e.g. create questionnaire to measure 'burnout')
    3. to reduce a dataset to a more manageable size while retaining as much of the original information as possible.  We look for variables that correlate highly within a group of other variables, but do not correlate with variables outside of that group.
    Examples: Common example of _factor analysis_ is trying to assess personality traits (say extroversion-interoversion).  Another example might be looking at the traits of profit, productivity, workforce and seeing if this can be reduced down to an underlying dimension of company growth.
    Sample Size: This technique requires a large sample size with a rule of thumb of a bare minimum of 10 observations per variable
        * 50 = Very Poor
        * 100 = Poor
        * 200 = Fair
        * 300 = Good
        * 500 = Very Good
        * 1000+ = Excellent
        * You can also calculate out the sample size needed using __Kaiser-Meyer-Olkin (KMO) measure of sampling adequacy__, which calculates for individual and multiple variables and represents the ratio of the squared correlation between variables to the squared partial correlation between variables.  Basically, you get back a statistic that is between 0 (_factor analysis_ is unlikley to be beneficial) and 1 (_factor analysis_ should be reliable).  This value should be greater than .5 as a bare minimum (or else collect more data)

*  __correlation-matrix (aka R-matrix)__ is a table of correlation coefficients between each pair of variables (with the _off-diagonal elements_ as the correlation coefficients).  Clusters of large correlation coefficients between subsets of variables suggest that these are _factors_
    -  If correlation between variables is about .3 or lower, then they're probably not the same underlying dimension so we can exclude them
    -  __Bartlett's test__ examines whether the population correlation matrix resembles an _identity matrix_ (i.e. the _off-diagonal components_ are zero).  If _Bartlett's test_ is significant then it means that the correlations between variables are overall significantly different from zero (which means good news).  A non-significant _Barlett's test_ is cause for concern because we're looking for clusters of variables that measure similar things and if no variables correlate then there's no clusters to find.
    -  __singularity__ is when variables are perfectly correlated (say above .8 or .9); this causes _multicollinearity_ and causes a problem because it becomes impossible to determine the unique contribution to a factor of the variables that are highly correlated.  Considering eliminating one of these high correlated variables
*  Remember that _total variance_ for a particular variable is made up of _common variance_ (variance shared with other variables) and _unique variance_ (which itself is made up of variance specific to that measure and _error (aka random variance)_.  When we're looking at _variance_ within a _correlation matrix_, we're interested in finding the common underlying dimensions within the data so we're primarily interested in only the __common variance (aka communality)__.  If there is no _random variance_, then the _communality_ is 1.  A variable that shares none of its variance with other variables has a _communality_ of 0.  The issue is that in order to do the _Factor analysis_, we need to know the _common variance_, but in order to do the _factor analysis_ we need to know the _common variance_.  We can solve this issue by:
    1. Assuming _common variance_ of every variable is 1 and do _principal components analysis_
    2. Estimate the _common variance_ by estimating _communality_ values for each variable using 

*  __principal components analysis (aka PCA)__ is also another technique for identifying groups or clusters of variables and is similar to _factor analysis_; the difference is that _factor analysis_ typically incorporates more domain specific assumptions about the underlying structure (_communality=1_) and solves _eigenvectors_ of a slightly different matrix.  Usually _PCA_ and _factor analysis_ differs very little.
    -  If doing _PCA_, a _factor matrix_ is known as _component matrix_
    -  _PCA_ works in a similar way to _MANOVA_.  We use a matrix to represent the relationships between variables.  The linear components (aka variates, factors) of that matrix are then calculated by determining the _eigenvalues_ of the matrix.  The _eigenvalues_ are used to calculate _eigenvectors_, the elements of which provide the loading of a particular variable on a particular factor (i.e. the _b_ values).  _Eigenvalues_ are also used to measure the importance of the _eigenvector._

*  __factor loading__ is the relative contribution that a variable makes to a factor.  When graphically represented, if you were to plot the variables (where the factors are the axis of a graph) and we plot variables along these axis (with the axis representing correlation between -1 to 1), the _factor loading_ would be the coordinate of a variable along the classification axis.  For example, say a list of traits are reduced to two factors: 'Consideration' is the y axis, 'Sociability' as the x axis.  We look at a variable (say 'Social Skills') and see how it correlates (Pearson correlation) between these two factors ('Sociability', 'Consideration'); we get two correlations and plot this point (which this point is the _factor loading_).
    *  To get the importance of a particular factor (represented by _b_), just square it.

*  __factor matrix (denoted as A)__ is the matrix that results from converting the _factor loadings_ into a matrix and is usually denoted _A_.  The columns represent each factor (say 'Sociability', 'Consideration') and the rows represent the loadings of each variable (e.g. Social Skills, Interest, Selfish, Liar, etc)

#### Factor Analysis - Calculating Factor Scores  <a id="factoranalysisfactorscores"></a>

Calculating __factor scores__ is estimating a score on a factor, based on their scores for the constituent variables.  There's a few different methods including:

*  __weighted averages__ - We assign a weight (say 1 to 10) for all our traits and then multiply the weights with the correlation and add up for each trait.  This method is overly simplistic and not really used because it requires all the variables to use the same measurement scale.
*  __regression method__ - We calculate _factor score coefficients_ as weights rather than using _factor loadings_.  The _factor loadings_ are  adjusted to take account of the initial correlations between variables and so the differences in units of measurement and variable variances are stabalized.  To get the matrix of _factor score coefficients (B)_, we multiply the matrix of factor loadings by the inverse (_R^-1_) (because we can't divide a matrix) and multiply by the _factor matrix (A)_.  This resulting matrix is a measure of the unique relationship between variables and factors.  Equation is: `B = R^-1*A`

#### What to do with Factor Scores  <a id="factoranalysiswhattodo"></a>

-  If the goal is to reduce the size of the dataset, we can now use the _factor scores_ as the smaller subset of measurement variables (e.g. here's an individual's scores on this subset of measures).  Any further analysis can be done on the _factor scores_.
-  We can even use _factor scores_ to remove the issue of _collinearity (aka multicollinerarity)_.  For example, say we did a multiple regression analysis and we identified sources of multicollinerarity.  To fix this, we do a _PCA_ on the predictor variables to reduce them to a subset of uncorrelated factors.  We then combine the correlated factors into a single factor.  Now we can rerun the analysis with the new factor score as a predictor variable.

####Factor Analysis - Factor Extraction  <a id="factoranalysisfactorextraction"></a>

Now that you have your _factor score_, you need to choose a technique to explore the factors in your data; choosing the technique depends on what you want to do:

*  __confirmatory factory analysis__ - used if you have a specific hypothesis to test
*  If you want to explore your data, then you can apply our findings using either the _descriptive method_ or the _inferential method_.  
    +  __descriptive method__ is to apply our findings to just the sample collected instead of extrapolating it beyond the sample.  Recommended techniques for the _descriptive method_ are _Principal Components Analysis_ and _Principal Factors Analysis (Principal Axis Factoring)_
    +  __inferential methods__ generalizes our findings to a population. Recommended techniques for the _iniferential methods_ include the _maximum-likelihood method_ and __Kaiser's alpha factoring__
*  __factor extraction__ is deciding what factors to keep; we don't want to keep all our factors and are interseted in keeping only the ones with a relatively large _eigenvalue_.
*  __scree plot__ is a plot of each _eigenvalue_ (y axis) against the factor that it's associated with (x axis).  The reason it's called this is because it usually looks like a rock face with a pile of debris/scree at the bottom.  By plotting the _eigenvalues_, the relative importance of each is apparent (usually there's a cut-off point called the __point of inflexion__ where there's a very steep slope).  For example, if the _point of inflexion_ happens on the third point, only select the first two points.  Use when sample size is greater than 200.
*  __Kaiser's criterion__ is usually the worst way to determine how many factors to keep; it says to keep any _eigenvalue_ that is 1 or more, which usually overestimates the number of factors to retain. 
*  __Jolliffe's criterion__ is a way to determine how many factors to keep; it says to keep any _eigenvalue_ that is .7 or more.
*  __parallel analysis__ is usually the best way to determine how many factors to retain (over _Kaiser's criterion_); _parallel analysis_ takes each _eigenvalue_ (which represents the size of the factor) and compares against an _eigenvalue_ for the corresponding factor in many randomly generated data sets that have the same characteristics as the data being analysed.

#### Factor Analysis - Interpretation  <a id="factoranalysisinterpretation"></a>

__factor rotation__ is a technique to interpret factor analysis by discriminating between factors; this is used after factors have been extracted and comes in two forms:

+ __orthogonal rotation__ is used when any underlying factors are assumed to be independent and the factor loading is the correlation between the factor and the variable, but is also the regression coefficient (i.e. values of the correlation coefficient = values of the regression coefficient)
+ __oblique rotation__ is used when the underlying factors are assumed to be related or correlated to each other.  The resulting correlations between variables and factors will differ from the corresponding regression coefficients.  This results in two different sets of _factor loadings_: the factor __structure matrix__, which is the correlation coefficients between each variable and factor and the factor __pattern matrix__, which is the regression and coefficients for each variable on each factor.  The math behind this is difficult and requires a __factor transformation matrix__, a square matrix whose size depends on how many factors were extracted (e.g. 2 factors = 2*2 matrix)

### Comparing Categorical Variables  <a id="comparecategorical"></a>
For continuous variables we measure using the means, but this is useless for categorical variables (since we'd assign numbers to categories and it would just depend on how many categories there were).  Instead, for categorical variables, we count the _frequency_ of the category to get a _contingency table_, which is a tabulation of the frequencies.  We use different algorithms depending on how many categorical variables there are (2 or more than 2).

*  An example of two categoricals is say training (if the cat was trained using either food or affect, but not both) and dance (if the cat ended up being able to dance)
*  <a id="comparecategoricalpearsonchisquare"></a>__Pearson's chi-square statistic (aka chi-square test)__ is used to compare the relationship between two categorical variables.  This is given by the equation ` `, which is a variation on `deviation = change in (observed -model)^2`.  Note that this is only an approximation (which works really well for large samples), but if the sample size is too small, use _Fisher's exact test_, _likelihood ratio_, or _Yate's continuity correction_ to avoid making _Type I errors_.
    -  __Fisher's exact test__ is a way to compute the exact probability of the _chi-square statistic_, useful for when the sample sizes are too small.  Usually this is used on 2 * 2 contingency tables (two categorical variables each with two categorical options) and small sample size, but can be used on larger at the cost of really intensive calculations
    -  __likelihood ratio statistic__ is an alternative to the _Pearson's chi-square_ and is based on the _maximum-likelihood theory_.  The idea is that you collect some data and create a model for which the probability of obtaining the observed set of data is maximized, then you compare this model to the probability of obtaining those data under the null hypothesis.  You then compare observed frequencies with those predicted by the model.  This is roughly the same as _Pearson's chi-square_, but preferred when samples are small.  The equation is: ` `
    -  __Yate's continuity correction__ is used specifically for 2 * 2 contingency tables because for these cases _Pearson's chi-square_ tends to make _Type I_ errors.  Be careful though, this sometimes overcorrects and produces a _chi-square_ value that is too small .  The equation is: ` `
*  Assumptions of _chi-square test_ are:
    -  Independence of data - this means that each person, item, entity contributes to only one cell of the contingency table; you can't use this on a _repeated measures design_ (e.g. train cats with food to dance, then trained the same cats with affection to see if they would dance)
    -  Expected fequencies should be _greater than 5_; although in larger contingency tables, it's okay to have up to 20% of expected frequencies below 5, the result is loss of statistical power (which means the test might fail to detect a genuine effect).  If there isn't more than 5, collect more data of that particular category.
*  Interpreting the _chi-squared test_; if the _p-value_ is less than .05, then there is a significant relationship between your two variables
*  __odds ratio__ is a way to calculate _effect size_ for categorical data.  _odds ratio_ are good for 2 * 2 contingency tables and probably not useful for anything larger.  Example is odds of cat dancing after food = (number that had food and danced / number that had food but didn't dance), say 28/10 = 2.8.  Repeat for odds of dancing after affection (let's say that's .0421).  Then to calculate the _odds ratio_ = (odds dancing after food / odds dancing after affection); odds ratio = (2.8/.0421) =6.65  This means an annimal trained with food has 6.65 times higher chance to dance than being trained with affection.  
    -  If we're interested in finding out which particular factor contributed significantly in larger contingency tables, we can use the _standardized residuals_.


## Comparing Multiple Categorical Variables using Loglinear Analysis  <a id="loglinearanalysis"></a>
__loglinear analysis__ is used when we want to look at more than two categorical variables.  Think of this as the _ANOVA_ for _categorical variables_ (where for every variable we have, we get a main effect but we also get interactions between variables).  For example, say we want three variables: Animal (dog or cat), Training (food as reward or affection as reward) and Dance (did they dance or not?)  Again, this can also be seen as a regression model.  Let's not get into the math, but basically categorical data can be expressed in the form of a linear model provided we use log values (thus loglinear analysis).  The idea is that we try to fit a simpler model without any substantial loss of predictive power through backward elimination (remove one at a time hierarchically).

*  For example, say we have the following interactions for these three variables: Animal (dog or cat), Training (food or reward), Dance (can they dance or not).  We take the one interaction involving all three variables (i.e. _highest-order interaction_) and remove it.  We look at whether the new model without this interaction and if the new model significantly changes the likelihood ratio statistic, then we stop here and say that we have a significant three way interaction.  If there is no change in the likelihood ratio statistic, then we move to a lower-order interaction. 
    -  Three main effects (Animal, Training, Dance)
    -  Three interactions involving two variables each (Animal * Training, Animal * Dance, Training * Dance)
    -  One interaction involving all three variables (Animal * Training * Dance)
*  Assumptions in loglinear analysis include:
    -  _Loglinear analysis_ is an extension of the _chi-square test_ so it has similar assumptions
    -  Each cell must be independent
    -  Expected frequencies should be large enough for a reliable analysis.  With more than two variables, it's okay to have up to 20% of cells with expected frequencies less than 5; but all cells must have expected frequencies greater than 1.  If this assumption is broken, try to collapse the data across one or the variables
*  Reporting results of a loglinear analysis - Example: The three-way loglinear analysis produced a final model that retained all effects.  The likelihood ratio of this model was this and p was that.  This indicated that the highest order interaction (Animal * Training * Dance) was significant.  To break down this effect, separate chi-square tests on the Training and Dance variables were performed separately for dogs and cats.


## Multilevel Linear Models  <a id="multilevellinear"></a>
Sometimes data is hierarchical instead of at a single level.  This means that some variables are clustered or nested within other variables (i.e. some of our other analysis may be oversimplification).  Let's use this example data set:

  -  __Level 1 variable__ - Say we have a lot of children (this is the lowest level of the hierarchy)
  -  __Level 2 variable__ - Say these students are organized by classrooms (this means children are _nested_ within classes)
    +  Note: We can have additional levels (e.g. level 3 variable is the school of that classroom, level 4 is the school in that school district)
*  The idea is that children at different layers (say within the same classroom or the same school) are more similar to each other.  This means that each case is not entirely independent.  To solve for this, we use __intraclass correlation (ICC)__, which represents the proportion of the total variability in the outcome that is attributable to the child's classroom.  This means that if the classroom had a huge effect on the children, then the _ICC_ will be small.  Howerver, if the classroom had little effect on the children, then the _ICC_ will be large.  Simply, _ICC_ says if this hierarchical grouping had an effect on the outcome.
*  So why use a _multilevel linear model_?  The benefits include:
    -  We don't have to assume that the relationship between our covariate and our outcome is the same across the different groups that make up our predictor variable.
    -  We don't need to make the assumption of independence.
    -  It's okay to have missing data.  You don't have to correct and impute for missing data.