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
|__unpaired t-test__ (aka two independent samples t-test) and __unpaired z-test__ (aka two independent samples z-test)|Compare two continuous variables by looking at their __means__ using unpaired t-test (n<30 or unknown standard deviation) and unpaired z-test (n>30)|We want to know if two categorical groups (say women and men) have different average heights|P|1|continuous|1|categorical (only dichotomous)|2 ind|Do not do multiple t-tests because of __familywise__ (aka experimentwise error rate)|
|__Wilcoxon Rank-Sum__ (aka Wilcoxon-Mann-Whitney, Mann-Whitney, Wilcoxon two sample)|Compares two categoricals (looking at their medians) using the __independent measures__ design.  Note: For repeated-measures design, use Wilcoxon signed rank test.|We want to know the differences in depression levels between two groups of people (one takes alcohol, other ecstasy).  We want to decide at a .05 significance level if the gas mileage data of manual and automatic cars have identical distributions.|NP|1|categorical (ordinal) or continuous|1|categorical (only dichotomous)|2 ind||
|__Chi square test of homogeneity__ (aka Chi-square test) and __Fisher's exact test__|Compare two categoricals (looking at their __proportions__).  Create crosstab and if each cell has a minimum frequency of 5 use Chi-square test of homogeneity.  If a cell does not have the minimum frequency of 5, use Fisher's exact test.|We want to know if there is a relationship between a person's gender (Male, Female) and the type of school (Public, Private).  Is there any statistically significant relationship between school attended and gender?|NP|1|categorical|1|categorical (only dichotomous)|2 ind||
|__One-way Independent ANOVA__ (Analysis of Variance)|Compares several groups (three or more) by looking at their __means__ using __independent measures__.  ANOVA is an __omnibus test__, meaning it tests for an overall effect between all groups.  Check __homogeneity of variance__ (i.e. variances of groups should be equal) with __Levene's test__.|We want to see if costumes of flying superheroes (Superman, Spiderman) injure themselves (scale 0 to 100) more than non-flying superheroes (Hulk, Ninja Turtles).  Does the mean score differ significantly among the levels of each group/superhero?|P|1|continuous|1|categorical|3+ ind|Test statistic is the __F-ratio__ (aka F-statistic, F-test) and tells us if the means of the 3+ groups are different (as a whole).  Do __planned contrasts__ or __post-hoc comparisons__ to determine which specific group is different|
|__Kruskal Wallis One-way ANOVA__ (aka Kruskal Wallis)|Compares several groups (three or more) by looking at their medians. Idea is to rank all data from all groups together (ordering scores from lowest to highest), assign tied ranks the average of the ranks had they not been tied.  Once ranked, collect the scores back into their groups and add up ranks for each group.|We want to see if coca cola affects sperm count.  We have four groups (no soda, 1 soda, 4 sodas, 7 sodas a day) and check sperm count every day.|NP|1|categorical (ordinal) or continuous|1|categorical|3+ ind|Visualize with __boxplot__.  Use __Jonckheere-Terpstra test__ if you think there is some type of trend between the group's medians.|
|__Chi square test of homogeneity__ (aka Chi-square test) and __Fisher's exact test__|Compare two categoricals (looking at their __proportions__).  Create crosstab and if each cell has a minimum frequency of 5 use Chi-square test of homogeneity.  If a cell does not have the minimum frequency of 5, use Fisher's exact test.|Compare relationship between a person's gender (Male, Female) and the tv shows they prefer (Simpsons, Breaking Bad, Game of Thrones).  Do men have a significantly different preference for tv shows than women? |NP|1|categorical|1|categorical|3+ ind|__Contingency Table__ (aka Cross Tab) shows __Populations__ * __Category Levels__|
|__paired t-test__ (aka two dependent samples t-test, paired two sample t-test, dependent t-test for paired samples, paired two samples t-test, repeated-measures t-test)|Checks for statistically significant difference by looking at the __means__ 