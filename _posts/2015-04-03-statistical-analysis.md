---
layout: post
title: Statistical Analysis
---

## {{ page.title }}

**Summary**

When I was younger, I wish someone told me to learn calculus if I wanted to be a rocket scientist, otherwise go learn statistics.  Even with that advice, I found it difficult to learn statistics because there seems to be a lot of different naming conventions.  Here is my take on the first principles of statistics where we try to break down statistical analysis into its smallest pieces; the idea is that we want to find useful patterns for learning statistics.  Statistics is simply `outcome = model + error`

**Factors**

I think the main factors in determining an analysis are:

*  Number of __independent variables__ and __dependent variables__
*  Data type(s) of __independent variables__ and __dependent variables__
    -  E.g. Are the variables categorical (dichotomous, nominal, ordinal) or continuous?  If the variables are continuous, we can use __parametric__ tests and if the variables are categorical, we use __non-parametric__ tests that make less assumptions
*  What are we interested in checking, __correlation__ or __causality__ (cause and effect)?  Correlation does not directly infer whereas Causality says a variable effects another.
*  How are we measuring?  Are we looking at __independent measures__ (a single point in time snapshot) or __repeated measures__ (measure variables at different points in time)
*  What is the Sample Size and what should we consider statistically significant (p-value)?
*  Are we reliably checking what we actually want to measure __reliability__ and __validity__?

**Descriptive Statistics**

This describes the dataset and is pretty straightforward.  For example, what is the mode, median, mean?  For more details, look up the Stats Primer post.

**Inferential Statistics**

We predict values of an outcome variable based on some kind of model.  This usually means we are looking to see if the independent variable(s) cause some kind of change in the dependent variable(s).  All inferential statistics can be described with:

    outcome = model + error

**Model CheatSheet**

We simply switch out the model, which is made up of __variables__ and __parameters__.

| __test_name_aka | ___about_model_description | _____example_use_of_the_model |dist|#_dep|type_dep|#_ind|type_ind|pop| ____the_notes_section|
|:--------:|:-------------------|:----------------------|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|__one sample t-test__ and __one sample z-test__|compare one group to a 'known truth' looking at their __means__; <30 sample is t-test, >30 sample is z-test| Does the average NY student spend more time studying than the national average of 2hrs/day |P|1|continuous|0|N/A|1 ind|Calculations based on Central Limit Theorem|
|__one sample sign__ (aka one sample median) and __one sample Wilcoxon__|compare one group to a 'known truth' looking at their __medians__; use one sample sign for unknown or asymmetric distribution, use one sample Wilcoxon for symmetric distribution|We want to know the median time it takes for a new user to add a friend to their buddy list.  We want to know if the median time for a newly developed drug to relieve pain is 12 minutes.|NP|1|categorical (ordinal) or continuous|0|N/A|1 ind|
|__one sample test of proportions__ (__binomial sign test__ and __Chi-square goodness-of-fit__)|compare one group to a 'known truth' looking at their __proportions__;use binomial sign test if categories are dichotomous, use Chi-square for more than 2 categories|A company prints out baseball cards claiming 30% are rookies, 60% are veterans, 10% all-stars.  We gather a random sample and use Chi-square goodness of fit to see if our sample distribution is statistically greater than what the company claimed|NP|1|categorical|0|N/A|1 ind|Population should be at least 10 times as large as the samples; this method is good for really large samples, but not suited for small samples (go get more samples if necessary)|

