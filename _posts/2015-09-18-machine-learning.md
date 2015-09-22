---
layout: post
title: Machine Learning
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Types of Machine Learning Problems](#typesml)
    -  [Definitions](#typesmldefinitions)
    -  [Problems Summary](#problemssummary)
    -  [Supervised Continuous](#supcontinuous)
    -  [Supervised Categorical](#supcategorical)
    -  [Unsupervised Continuous](#unsupcontinuous)
    -  [Unsupervised Categorical](#unsupcategorical)
*  [Workflow](#workflow)
    -  [Identify Problem](#identifyproblem)
    -  [Collect data](#collectdata)
    -  [Clean and reformat data](#cleanreformatdata)
    -  [Build model](#buildmodel)
    -  [Evaluate model](#evaluatemodel)
    -  [Communicate Results](#communicateresults)

##<a id="summary">Summary</a>

Machine Learning is basically using data to create algorithms that a machine can learn from (usually by finding patterns).  We normally need to put data in a usable format, and then apply different algorithms to see what has better predictive power.

##<a id="typesml">Types of Machine Learning Problems</a>

Machine Learning can fall under a few different categories.  Problems can be __Supervised__ or __Unsupervised__, __Continuous__ or __Categorical__.  We'll go over the definitions of each first, then combine the categories to see what kind of problems we can solve.

####<a id="typesmldefinitions">Definitions</a>

So what are these categories?

*  __Supervised__ learning means we know a pair of data (usually an input and an associated output).  The data is labeled, meaning we know what the input is and what a correct output should be.  For example, we can compare a handwriting image to what the typed text is.  We have an input of a person's age and an output of the person's height.  
*  __Unsupervised__ learning means we try to find some type of hidden structure in a dataset.  The data is unlabeled, meaning we don't know what the dataset stands for.  For example, we might get images of some handwriting, but we don't know what the typed text is.  Instead, our algorithms would try to find patterns (like here's an e shape) and group those together.
*  __Continuous__ (aka Quantitative) means numerical data.  For example, 100 cookies.
*  __Categorical__ (aka Qualitative) means data is in categories.  For example, pregnant or not pregnant, human/cat/dog, first/second/third.

####<a id="problemssummary">Problems Summary</a>

*  __Supervised Continuous__ - make quantitative predictions (e.g. regression)
*  __Supervised Categorical__ - make qualitative predictions (e.g. classification)
*  __Unsupervised Continuous__ - extract quantitative structure (e.g. dimension reduction)
*  __Unsupervised Categorical__ - extract qualitative structure (e.g. clustering)

####<a id="supcontinuous">Supervised Continuous</a>

With _supervised_ learning on one or more _continuous_ variables, we call this __regression__.  Let's say we try to predict the length of a salmon (a continuous variable) based on its age and weight.

####<a id="supcategorical">Supervised Categorical</a>

With _supervised_ learning on one or more _categorical_ variables, we call this __classification__.  Let's say we try to predict the gender of a person (a categorical variable) based on their age and height.

####<a id="unsupcontinuous">Unsupervised Continuous</a>

With _unsupervised_ learning on one or more _continuous_ variables, we call this __dimension reduction__.  We're basically trying to take a lot of numbers/dimensions and squeeze it into a smaller set of numbers/dimensions (usually just two or three).  This helps with data visualization.

####<a id="unsupcategorical">Unsupervised Categorical</a>

With _unsupervised_ learning on one or more _categorical_ variables, we call this __clustering__.  Let's say we look at a lot of attributes of a flower (e.g. color, petal length), we can find what are the natural groupings.

##<a id="workflow">Data Science Workflow</a>

So what's the basic workflow?

1. Identify the problem
2. Collect data
3. Clean and reformat data
4. Build model
5. Evaluate Model
6. Communicate Results

The process is iterative so during Step 5 (evaluating your model) you may realize that the data you collected may not be enough so you move back to Step 2 (collecting more data).




