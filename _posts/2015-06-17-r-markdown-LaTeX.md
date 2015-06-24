---
layout: post
title: Report Generation with R, Markdown, LaTeX
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Setup](#setup)
*  [R Sweave](#rsweave)
*  [R knitr](#rknitr)

##<a id="summary">Summary</a>

__R__ normally executes code in a regular R script.  However, sometimes we don't want just the code and instead want to create a report that has some text documentation along with the R code.  By default, we can use the `Sweave` package (included with every R install) and we can add additional dynamic report generation using the `knitr` package.  The idea is to combine program source code (__code chunks__) and the corresponding documentation (__documentation chunks__) into a single file.

Code is normally stored as a __R Script__ (e.g. __.R__ file).  For the documentation portion, we use mainly __Markdown__ (e.g. __.md__, __.rmd__, i.e. anything with __md__) and __LaTeX__ (__.tex__).  We use a library like __Sweave__ or __knitr__ to put together the code chunks (in R) along with the documentation (in Markdown and LaTeX) to create an output (e.g. HTML, PDF).

##<a id="setup">Setup</a>

I downloaded R, and RStudio, Sublime Text 3 with the LaTeXTools Package (so I can edit with my text editor of choice).  On a Mac, I downloaded MacTex and on a PC I downloaded MiKTeX.  The MacTex and MiKTeX are package managers for the LaTeX typesetting (i.e. so you can use the `\usepackage{stuff}` command, which imports helpful packages for things like importing images, if-then statements)

##<a id="rsweave">R Sweave</a>

__Sweave__ creates dynamic reports using __LaTeX__, a high-quiality typesetting system mainly used in scientific documentation.
*  Sweave file extensions include taking an input file (__.rnw__, __.snw__) and converting this to an output file (__.tex__).  This can then be turned into a pdf file using the Compile PDF button.
*  Text is replaced using a __key-value__ system (e.g. __key__ of `\documentclass` takes a __value__ of `{article}`) with optional arguments separated by commas.
*  The function `Sweave()` is used to replace code with its output (e.g. a graph)
*  The function `Stangle()` discards the documentation and keeps only the code chunks

####Sweave Code

Sweave creates code blocks using `<< myoptions >>=` to begin a code block and `@` to end a code block.  The output can be the code itself or the results of the code (e.g. a graph of the output).  The main options are `echo` (which tells us if we should show the commands) and `fig` (which tells us if we should show the plot).

    \documentclass{article}
    
    \begin{document}
    \SweaveOpts{concordance=TRUE}
    
    <<fig=TRUE, echo=FALSE>>=
    boxplot(Ozone ~ Month, data = airquality)
    @
    
    \end{document}

##<a id="rknitr">R knitr</a>

####knitr Setup

__knitr__ is a package (like the default Sweave) that is another way of 'weaving' (i.e. replace code snippets with the generated code output).  It has a few more advanced features like being able to generate the printed R terminal (showing warnings and errors), more flexibility on graphics, .
*  knitr takes in the following input files like __LaTeX__ (__rnw__) and __Markdown__ (__rmd__)

Using RStudio, install the package 'knitr' on CRAN.  Then change RStudio's settings under 'Tools' > 'Global Options' > 'Sweave' > 'Weave Rnw files using' > switch from 'Sweave' to 'knitr'.

####R code in knitr

knitr can embed R code and R expressions.  Press `Ctrl` and `Enter` to run the code chunks (i.e. sends the code to the R console)

    # R code chunks
    ```{r}
    1+1
    .4-.7+.3  # What?  Why is this not zero?!
    ```
    
    # R code expressions
    Yes I know the value of pi is `r pi` and 2 times pi is `r pi*2`



