---
layout: post
title: Statistical Analysis
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Statistics High Level Overview](#statsoverview)
    -  [How are we using the data?  Descirbe or Infer](#describeinfer)
    -  [What are the data types?  Categorical or Continuous](#categoricalcontinuous)
    -  [Number of Independent and Dependent Variables](#numbervariables)
*  [Model Cheatsheet](#modelcs)
*  [SciPy Stats](#scipystats)
    -  [Example: One Sample T-Test](#onesamplettest)

##<a id="summary">Summary</a>

When I was younger, I wish someone told me to learn calculus if I wanted to be a rocket scientist, otherwise go learn statistics.  Even with that advice, I found it difficult to learn statistics because there seems to be a lot of different naming conventions.  Here is my take on the first principles of statistics where we try to break down statistical analysis into its smallest pieces; the idea is that we want to find useful patterns for learning statistics.  Statistics is simply `outcome = model + error`

##<a id="statsoverview">Statistics High Level Overview</a>

I think data analysis can be broken down into three key parts.  This is a very high level overview so it is probably oversimplifying a lot of complex topics, but should give you a sense of what data is.

1.  How are we using the data?  Do we want to __describe__ or make __inferences__ about the dataset?
    -  If we want to just __describe__ the dataset, we look at things like the mean, median, etc.  We want to be careful to make sure that our measurements are good in the sense that they are __reliable__ and __valid__, etc.
    -  If we want to make some type of __inference__, we have additional criteria to consider and can look at it a few different ways.
        -  What are we interested in checking, __correlation__ or __causality__ (cause and effect)?  Correlation does not directly infer whereas Causality says a variable effects another.
        -  How are we measuring?  Are we looking at __independent measures__ (aka __cross-sectional study__, a single point in time snapshot) or __repeated measures__ (aka __longitudinal study__, measure variables at different points in time).
        -  There's additional considerations like if we have an adequate __sample size__, what should we consider __statistically significant (p-value)__, __one-tailed__ or __two-tailed__ test, etc.
2.  What are the data types?  Are the variables __categorical__ (dichotomous, nominal, ordinal) or __continuous__?
    -  If the variables are continuous, we can use __parametric__ tests (that assume a normal distribution) and if the variables are categorical, we use __non-parametric__ tests, which make less assumptions
3.  Number of __independent variables__ and __dependent variables__
      - The number of variables affect the test we can use.  I made a quick guide below with some examples.

####<a id="describeinfer">How are we using the data?</a>

__Descriptive Statistics__ describes the dataset and is pretty straightforward.  For example, what is the mode, median, mean?  For more details, look up the Stats Primer post.

__Inferential Statistics__ predicts values of an outcome variable based on some kind of model.  This usually means we are looking to see if the independent variable(s) cause some kind of change in the dependent variable(s) or if there is a strong relationship between a set of variables.  All inferential statistics can be described with:

    outcome = model + error

Personal Note: A lot of data analyst jobs out there are just making graphs of descriptive statistics.  It's a great intro to data and is useful, but being able to make inferences from the dataset is a natural (and more difficult) next step.

####<a id="categoricalcontinuous">What are the data types?</a>

Data can be broken down into different __levels of measurement__ that help determine what statistical tests we can do.  Ideally we want more specific (continuous) instead of braoder (categorical-binary).  Here they are from weakest to strongest in terms of statistical power (so you can find the right effect).

*  __Categorical__ (aka Qualitative)
    -  __Binary__ (aka Dichotomous) has only two distinct possibilities (e.g. either alive or dead, pregnant or not)
    -  __Nominal__ has two or more possibilities (e.g. human, cat, dog)
    -  __Ordinal__ has two or more possibilities and there is a logical order (e.g. first, second, third place in a race); you know which is larger and smaller, but not by how much
*  __Continuous__ (aka Quantitative)
    -  __Numerical__ data that are numbers (e.g. 1,000 cookies)


####<a id="numbervariables">Number of Independent and Dependent Variables</a>

See chart below on what statistical test to use based on the number (and type) of variables we have.

##<a id="modelcs">Model Cheat Sheet</a>

We simply switch out the model, which is made up of __variables__ and __parameters__.  Like all cheat sheets, this is a large oversimplification of statistics, but could be useful as a high level starting point.

| __test_name_aka__ | __about_model_description__ | __example_use_of_the_model__ |dist|__#dep__|type_dep|__#ind__|type_ind|pop| __the_really_long_notes_section__ |
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
|__paired t-test__ (aka two dependent samples t-test, paired two sample t-test, dependent t-test for paired samples, paired two samples t-test, repeated-measures t-test)|Checks for statistically significant difference by looking at the __means__ of two dependent samples using __repeated-measures__.|We want to know if a test preparation course helps students with their test scores.  We do a before-course exam and an after-course exam, then compare to see if there was any significant difference.|P|1|categorical (ordinal) or continuous|1|continuous|2 dep|Note: There's two types of t-test, unpaired (aka independent samples) that compares between-groups (unrelated groups).  This is paired (aka dependent samples) t-test, compares repeated-measures (i.e. before and after, same group)|
|__Wilcoxon signed Rank Test__|Checks if statistically significant difference by looking at __median__ differences using __repeated-measures__.  We rank all items from lowest to highest score, then do analysis on the rank itself instead of the actual data.  Once ranked, collect the scores back into their groups and add up ranks for each group.|We survey people about an area before gentrification and after.  Responses were on a categorical from (really love it to really hate it).  We add up the positive and negative responses.  We compare to see if there is any significant difference|NP|1|categorical (ordinal) or continuous|1|categorical (ordinal) or continuous|2 dep|Similar to paired t-test but non-parametric version so we assume difference is ordinal.  Main thing is there's a sign (positive or negative) assigned to the rank.  If sample size too small, use Fisher's exact test, likelihood ratio, or Yate's continuity correction to avoid Type I errors.|
|__McNemar's test__ (aka McNemar's Z-test)|Checks if statistically significant difference by looking at the __proportion__ using __repeated-measures__.|We want to see if a drug has an effect on a particular disease.  Counts of individuals are given with the diagnosis between treatment (present or absent) in the rows and diagnosis after treatment (present or absent) in the columns.|NP|1|categorical (dichotomous)|1|categorical (dichotomous)|2 dep|We're testing difference between paired proportions; is there a significant difference before-after? We want the marginal frequencies (i.e. proportion) of two binary outcomes.  To plot, use a 2*2 __contingency table__|
|__One-way Repeated-Measures ANOVA (analysis of variance)__|Check if statistically significant differences between several groups (three or more) by looking at their __means__ with __repeated-measures__.|We want to see if there is a difference in alcohol consumption (our dependent variable) after rehab (e.g. with three measurement points: immediately before, 1 month after, 6 months after; 'time' is our independent variable).|P|1|continuous|1|categorical|3+ dep|Our values are likely related because they're from the same population (due to repeated-measures design) so we can't use __F-ratio__ because it lacks accuracy and instead we use the __assumption of sphericity__ (aka circularity), which is an assumption about the structure of the covariance matrix (i.e. checks if variances of the differences between conditions are about equal)|
|__Friedman's test__ (aka Friedman's two-way ANOVA for ranks)|Check if statistically significant differences between several groups (three or more) looking at their __medians__ with __repeated-measures__.  Theory is we rank all items from lowest to highest score, then do analysis on the rank itself (ordinal) instead of the actual data.  Once ranked, collect the scores back into their groups and add up ranks for each group.  |We survey multiple subjects (say 20 people) about three soft drinks (Pepsi, Coke, 7-up) and they rate on a scale of 1-10 on taste of each drink (given in random order) using the same participants.  Is there a significant difference in taste?|NP|1|categorical (ordinal) or continuous|1|categorical|3+ dep|Visualize with a __boxplot__.  Use the __Jonckheere-Terpstra test__ when you hypothesize that there's some type of trend between the group's medians.  Group sizes should be about equal.|
|__Wald Chi-Square Test__ and __Repeated Measures Logistic Regression__ (aka Wald log-linear chi-square test)|Check if statistically significant difference between two categoricals by looking at their __proportions__ using __repeated measures__ in a two-way table.  Wald chi-square is the difference between observed and expected weight cell frequencies  (e.g. probability of becoming pregnant divided by probability of not becoming pregnant).  Wald log-linear chi-square test is based on the log odds ratios (e.g. odds of becoming pregnant when condom is used, odds of becoming pregnant when condom not used, then calculate the proportionate change in odds between the two)|We do multiple tests (did person have a high pulse) on multiple subjects (say 30 people) that are assigned to different diets (meat, no meat) and different exercises (biking, swimming, tap dancing).  Predict the probability of getting a high pulse with meat diet.|NP|1|categorical|1|categorical|2 dep|We can't have __complete separation__, which is where the outcome variable can be perfectly predicted by one variable or a combination of variables; this causes issues in that there's no data in the middle (where we're not very sure of probability); to fix this, collect more data or use a simpler model (less features).|
|__Factorial ANOVA__ (aka 2+ way independent Factorial ANOVA, independent Factorial ANOVA)|Compares the differences between groups with two or more independent variables using __independent measures__.  Note that when effects of 2+ variables are considered together, this is a __factor__.  The calculation for SSm (variance explained by the experiment) is calculated as SSa (variance of A), SSb (variance of B), SSa*b (__interaction effect__, variance of interaction of A and B).|Hypothesis is that after alcohol is consumed (1st independent variable), subjective perceptions of attractiveness are more inaccurate.  We're also interested if effect is different for men and women (2nd independent variable)|P|1|continuous|2+|categorical|2+ ind|__Levene's test__ to check for significant differences between group variances (homogeneity of variance).  __Simple effects analysis__ to look at __interaction effects__.   __interaction graphs__ help interpret and visualize significant interaction effects.|
|__Ordinal Logistic Regression__ (aka ordered logit, Ordered Logistic Regression, Multinomial Logistic Regression)|Compares several groups (with the dependent variable as an ordinal categorical and at least two independent categoricals) by looking at their __medians__ using __independent measures__.  Predict categorical (ordinal) dependent variable using independent variables with calculations using __log-likelihood__, which sums the probabilities associated with the predicted and actual outcomes (i.e. how much left unexplained after the entire model has been fitted).|Predict if 'tax is too high' (this is your ordinal dependent variable measured on a 4 point Likert item from 'Strong Disagree' to 'Strongly Agree') based on two independent variables (Age, Income).  Can also determine whether a number of independent variables (Age, Gender, Level of Physical Activity) predict the ordinal dependent variable of obesity (values of normal, overweight, obese)|NP|1|categorical (ordinal) or continuous|2+|categorical|2+ ind|For partial fit, use __maximum-likelihood estimation (MLE)__ or __deviance__ (aka -2LL) because it has a chi-square distribution.  Check for __multicollinearity (ind variables are too highly correlated), __tolerance statistic__ and __VIF statistics__.  Also can't have __complete separation__.  Can penalize models for having more variables using __Akaike Information Criterion (AIC)__ or __Bayes Information Criterion (BIC)__|
|__Factorial Logistic Regression__|Compares several groups where there are two or more categorical independent variables with a dichotomous dependent variable using __independent measures__.|We're interested in the relationship between the independent variables of school type (tier1, tier2, tier3), program (art, science, history) and the dependent variable (gender).  Can we determine probabilities of independent variables affecting the dependent?|NP|1|categorical (dichotomous)|2+|categorical|2+ ind|Check for __multicollinearity__ (where independent variables are too highly correlated), __tolerance statistic__, __VIF statistics__.  Also can't have __complete separation__ (outcome is perfectly predicted by independent variables) since we need data in the middle / not 100% of the probability.|
|__Correlation__|Compares the relationship between two or more continuous variables using __Pearson's correlation coefficient__.  __coefficient of correlation__ is __r^2__; the higher the value the better the fit.|We want to see if there's any relationship between the 'number of ice cream getting sold' (continuous variable) and the temperature (continuous variable)|P|1|continuous|1|continuous|1 ind|A __negative correlation__ means there is an __inverse relationship__ (when one decreases, the other increases).  A __positive correlation__ means relationship is __directly related__ (when one increases, the other also increases).  __r__ ranges from 0 (no effect) to 1 (perfect effect).  r=.1 means small effect, r=.3 means medium effect, r=.5 means large effect.  Note that r=.6 does not mean it is twice the effect of r=.3|
|__Simple Linear Regression__|Looks at linear relationship between a continuous dependent variable and a continuous dependent variable using __independent measure__.  Mathmatical concept is __model sum of squares__ where we fit a line that minimizes error; simple linear regression the line is straight, in multiple linear regression the line is more complex.  Measure with __Pearson's correlation coefficient__ and __coefficient of correlation__ is __r^2__.  |How much does advertisting cost (continuous independent variable) affect album sales (continuous dependent variable)?|P|1|continuous|1|continuous|1 ind|If we graph the __standardized residuals__ against the __predicted values__, it should look like a random array of dots.  If we get a 'funnel' type shape, it means violation of __homogeneity of variance__ (which means we cannot generalize findings beyond this sample); can try correcting (with resisuals try transforming data, with violation of linearity assumption try logistic regression, otherwise try robust regression with bootstrapping).  Order of putting in predictor variables matter, recommend using __backward direction__ to prevent __suppressor effects__ or checking __all-subsets__ with __Mallow's Cp__ if you want to increase accuracy at cost of exponentially more calculations.  __Cross-validate__ with train/test at random 80/20 split.|
|Non-Parametric Correlation using __Spearman's rho__ (aka Spearman's Rank-Order correlation) and __Kendall's Tau__ coefficient|Compares the relationship between one or more categorical (ordinal) variables using __correlation__, which means __Spearman's rho__ or __Kendall's Tau__ coefficient.  Idea is that values are converted to ranks and then the ranks are correlated.|We want to see if there's any relationship between when people finish a race (first place, second place, third place - the categorical ordinal) and their time spent training (1 month, 2 months, 3 months - continuous variable)|NP|1|at least one categorical (nominal) in dep or ind|1|at least one categorical (nominal) in dep or ind|1 ind|__r__ can be __Spearman's rho__ (non-parametric data and at least ordinal data for both variables), __Kendall's tau__ (non-parametric data, but use when small number of samples and there's a lot of tied ranks; e.g. lots of 'liked' in ordinal ranks of 'dislike', 'neutral', 'like').  Correlation is not causation.  __Confounding variables__ (aka teritum quid) are extraneous factors (e.g. there's a correlation between breast implants and suicide; breast implants don't cause suicide, but a third factor like low self-esteem might cause both).|
|__Simple Logistic Regression__ (aka Binary Logistic Regression)|Test that assumes the dependent variable is dichotomous using __independent measures__.|Predict the likelihood that a tumor is cancerous or benign (dichotomous dependent variable)|NP|1|categorical (dichotomous)|1|continuous|1 ind|We assess the model with __log-likelihood__, the smaller value the better the fit.  This basically sums the probabilities associated with the predicted and actual outcomes (i.e. how much unexplained there is after the entire model has been fitted); larger values mean poorer fit.  For a partial fit, use __maximum-likelihood estimation (MLE)__ or __deviance (aka -2LL)__ because it has a chi-square distribution.|
|__Multiple Linear Regression__|Looks at the linear relationship between two or more independent continuous variables and its effect on a continuous dependent variable using __independent measures__.|How much does advertising cost (first independent continuous variable) and airtime on radio (second independent continuous variable) affect album sales (continuous dependent variable)?|P|1|continuous|2+|continuous|2+ ind|We calculate __Pearson's correlation__ and __coefficient of correlation (r^2)__.  This is similar to a Simple Linear Regression, except in a Multiple Linear Regression we have more than one independent variable.  The mathmatical concept is still the same, we use model sum of squares where we try to fit a line that minimizes error.  Here the line is more complex than a straight line.|
|__Analysis of Covariance__|Looks at the linear relationship between independent variables (where there's both continuous and categorical) and a dependent continuous variable by comparing several __means__.|We have viagra doses (placebo, low, high dose as categoricals) and we measure a participant's libido (continuous dependent variable) with the partner's libido (independent continuous variable as the __covariate__)|P|1|continuous|2+|at least 1 continuous and/or at least 1 categorical|2+ ind|__Partial eta squared__ (aka __partial n^2__) is an effect size measure for ANCOVA; this is like __eta squared__ (n^2) in ANOVA or r^2.  ANCOVA is like ANOVA except you include __covariates__, which are one or more continuous variables that are not part of the main experimental manipulation, but have an influence on the dependent variable.  You add these to 1.) __reduce within-group error variance__ and 2.) __elimination of confounds__ (those variables other than the experimental manipulation that affect the dependent variable)|
|__Multiple Logistic Regression__|Test that assumes one categorical (nominal) dependent variable and two or more continuous independent variables using __independent measures__.|High school graduates make career choices (nothing, work, college as the dependent variable) and we model this off of their social economic status (low, middle, high income as their first independent variable) and their SAT scores (as their second independent variable).|NP|1|continuous|2+|at least 1 continuous and/or at least 1 categorical|2+ ind|We're interested in the effect that the independent variables have on the probability of obtaining a particular value of the dependent variable|

##<a id="scipystats">SciPy Stats</a>

In Python, there's an open-source library called SciPy that handles a lot of standard mathematics, science, and engineering calculations.  There's many subpackages, including linear algebra (`scipy.linalg`), spatial data structures and algorithms to compute triangulations and Voronoi diagrams (`scipy.spatial`), but for this article we want to look at the statistics side (`scipy.stats`).  FYI there are other libraries like `statsmodel` that do this and more.
   
####<a id="onesamplettest">Example: One Sample T-Test</a>

__Problem__

We want to compare a sample mean to a known population mean of the average American height (177cm).  Does this data represent the population mean?

__Code__

        """
      Definitons:
        Assume statistical significance level (alpha) = .05
        Assume Two tailed
        Degrees of Freedom (dof) = For a simple t-test: number of samples - 1
    
      Example:
        If we look up degrees of freedom, say for 29 samples, assume a statistical
        significance .05, two-tailed test, we look up a 'critical value' table
        and get a critical value of 2.045; this means if we get a t-statistic of
        less than -2.045 or greater than 2.045, we reject the null hypothesis
    """
    
    from scipy import stats
    import numpy as np
    
    data_a = [177, 177, 178, 179, 172, 173, 178, 177, 174, 175, 178, 179,
                 178, 179, 178, 177, 173, 177, 175]
    data_b = [180, 198, 200, 178, 199, 210, 270, 210, 430, 240, 260,
                 180, 190, 188, 300, 210]
    
    print "Running Data A; we accept the null hypothesis" \
          "(i.e. no relationship/difference between measured groups)"
    print "Data A has a mean of:", np.mean(data_a)  # avg height: 176.526
    dof = len(data_a)-1  # for a t-test, degrees of freedom = number samples-1
    print "Data A has a length of:", len(data_a), " and " \
        " degrees of freedom:", len(data_a)-1
    result = stats.ttest_1samp(data_a, 177)
    print "t-statistic is {} and p-value is {}".format(result[0], result[1])
    # t-statistic is -0.940589569294 and p-value is 0.359367763116
    critical_value = stats.t.isf(0.05/2, dof)  # critical value for two sided test
    print "critical value is:", critical_value  # 2.100
    
    print "\nRunning Data B; we reject the null hypothesis" \
          "(i.e. there is a relationship/difference between measured groups)"
    print "Data B has a mean of:", np.mean(data_b)  # avg height: 227.687
    dof = len(data_b)-1  # for a t-test, degrees of freedom = number samples-1
    print "Data B has a length of:", len(data_b), " and " \
        " degrees of freedom:", len(data_b)-1
    result = stats.ttest_1samp(data_b, 177)
    print "t-statistic is {} and p-value is {}".format(result[0], result[1])
    # t-statistic is 3.13929630004 and p-value is 0.00675255864071
    critical_value = 2.13  # 2.13, found through critical value chart
    critical_value = stats.t.isf(0.05/2, dof)  # critical value for two sided test
    print "critical value is:", critical_value  # 2.131


__Explaination of Results__

We did a __t-test__ for the mean of ONE group of scores.  We created two examples, one dataset that accepts the null hypothesis and the other that rejects the null hypothesis.  As a quick overview:

*  __Null hypothesis__ is just a way of saying there is no relationship/difference among these variables.  We either accept or reject the null hypothesis.  If we accept the null hypothesis, this means there is no relationship/difference between variables.  If we reject the null hypothesis, this means there is a relationship/difference between the groups.
*  The __statistical signifiance level__ (aka __alpha__) is a number we pick at the beginning of our experiment (usually `.05` or `.01`) and use this as a threshold to determine if we accept or reject the null hypothesis.
*  The __p-value__ can be seen as the probability of obtaining the observed sample results.  If the _p-value_ is equal or smaller than the _statistical signifiance level_, then the _null hypothesis_ must be rejected.  However, this does not automatically mean that the alternative hypothesis is true.
*  The __t-statistic__ used in this __t-test__ assesses whether the size of the difference is significant.  The greater this value, the greater the evidence against the null hypothesis.
*  The __critical value__ is what we use to compare to the t-statistic to see if we reject the null hypothesis or not.

__Example 1 - Similar Data__

In `data_a` we had an average height of `176.526`.  Just by eyeballing, our `data_a` is near the mean of 177 that we passed in.  For a t-test, we calculate the _degrees of freedom_ as the number of samples-1, which comes out to be `19-1=18`.  We get a _t-statistic_ that is `-0.940` and a _two tailed p-value_ of `0.359`.  We calculate the _critical value_ to be `2.100`.  We compare the _t-statistic_ with the _critical value_ and find that the _t-statistic_ value (`-0.940`) is within the _critical value_ ranges (of `-2.1` to `2.1`) so we have to accept the null hypothesis (i.e. there is no relationship/difference between these two sets of numbers).  With a _p-value_ of `.0359`, this is another indicator that we must reject the null hypothesis since it is greater than our signifiance level of `.05`.

__Example 2 - Different Data__

In `data_b` we had an average height of `227.687`.  Just by eyeballing, our `data_b` is not near the mean of 177 that we passed in.  For a t-test, we calculate the _degrees of freedom_ as the number of samples-1, which comes out to be `16-1=15`.  We get a _t-statistic_ that is `3.139` and a _two tailed p-value_ of `0.006`.  We calculate the _critical value_ to be `2.131`.  We compare the _t-statistic_ with the _critical value_ and find that the _t-statistic_ value (`3.139`) is within the _critical value_ ranges (of `-2.131` to `2.131`) so we reject the null hypothesis (i.e. there is a relationship/difference between these two sets of numbers).  With a _p-value_ of `.006`, this is another indicator that we can accept this alternative hypothesis since it is less than our signifiance level of `.05`.
