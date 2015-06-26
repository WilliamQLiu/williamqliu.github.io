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
    -  [LaTeX showing code using Sweave](#sweavelatexcode)
    -  [LaTeX showing plots using Sweave](#sweavelatexplot)
    -  [Markdown showing code using Sweave](#sweavemdcode)
    -  [Markdown showing plots using Sweave](#sweavemdplot)
*  [R knitr](#rknitr)
    -  [knitr Setup](#rknitrsetup)
    -  [LaTeX and Markdown showing code using knitr](#rknitrcode)

##<a id="summary">Summary</a>

__R__ normally executes code in a regular R script.  However, sometimes we don't want just the code and instead want to create a report that has some text documentation along with the R code.  By default, we can use the `Sweave` package (included with every R install) and we can add additional dynamic report generation using the `knitr` package.  The idea is to combine program source code (__code chunks__) and the corresponding documentation (__documentation chunks__) into a single file.

Code is normally stored as a __R Script__ (e.g. __.R__ file).  For the documentation portion, we use mainly __Markdown__ (e.g. __.md__, __.rmd__, i.e. anything with __md__) or __LaTeX__ (__.tex__).  We use a library like __Sweave__ or __knitr__ to put together the code chunks in R's noweb format (__.rnw__) along with the documentation (in Markdown and LaTeX) to create an output (e.g. HTML, PDF).

##<a id="setup">Setup</a>

I downloaded R, and RStudio, Sublime Text 3 with the LaTeXTools Package (so I can edit with my text editor of choice).  On a Mac, I downloaded MacTex and on a PC I downloaded MiKTeX.  The MacTex and MiKTeX are package managers for the LaTeX typesetting (i.e. so you can use the `\usepackage{stuff}` command, which imports helpful packages for things like importing images, if-then statements)

##<a id="rsweave">R Sweave</a>

__Sweave__ creates dynamic reports using __Markdown__ or __LaTeX__, a high-quiality typesetting system mainly used in scientific documentation.
*  Sweave file extensions include taking a code chunk (e.g. __.rnw__, __.snw__), combining this with the documentation file (__.tex__) and creating an output of a __pdf__ by clicking the 'Compile PDF' button in RStudio.
*  The function `Sweave()` is used to replace code with its output (e.g. a graph)
*  The function `Stangle()` discards the documentation and keeps only the code chunks

####<a id="sweavelatexcode">LaTeX showing code using Sweave</a>

If you were writing your documentation in LaTeX, Sweave creates code blocks using `<< myoptions >>=` to begin a code block and `@` to end a code block.  The output can be the code itself or the results of the code (e.g. a graph of the output).  The main options are `fig` (which tells us if we should show the plot) and `echo` (which tells us if we should show the R code).  We then click the 'Compile PDF' button.  If you want to run any R code inside, just highlight the code chunks and click 'Run'.

    \documentclass{article}
    
    \begin{document}
    \SweaveOpts{concordance=TRUE}
    
    <<name="Stuff", fig=TRUE, echo=FALSE>>=
    
    cat("This is my R code.\n")
    set.seed(123)
    x <- 1:10
    y <- rnorm(10)
    plot(x, y)
    
    cat("This is some more R code.\n")
    
    @
    
    \end{document}

####<a id="sweavelatexplot">LaTeX showing plots using Sweave</a>

*  One code block can only create one plot.
*  If you want to place the figure yourself, add in `include=FALSE`
*  You can add size options (in inches) with `height=4, width=5`
*  To print with ggplot graphics, put a `print()` around the plotting function
*  If you want to keep your source code format, use `keep.source=TRUE`
*  To change the size of the output graphic (default is 85% of line width), add this to the LaTeX section: 
    -  Set to entire width: `\setkeys{Gin}{width=\textwidth}`
    -  Set to half width: `\setkeys{Gin}{width=.5\linewidth}`


####<a id="sweavemdcode">Markdown showing code using Sweave</a>

If you were writing your documentation in Markdown, you start a code chunk with the below marks.  If you do not want to specify the underlying R code, use `echo=FALSE`.  If you do not want to display the graph, use `eval=FALSE`.

    ```{r, echo=FALSE}
    1+1
    .4-.7+.3  # What?  Why is this not zero?!
    ```
    
    # R code expressions
    Yes I know the value of pi is `r pi` and 2 times pi is `r pi*2`

####<a id="sweavemdplot">Markdown showing plots using Sweave</a>

Again, you can also show plots in Markdown.

     ```{r, echo=FALSE}
    plot(1:10, 1:10)
     ```

##<a id="rknitr">R knitr</a>

####<a id="rknitrsetup">knitr Setup</a>

__knitr__ is a package (like Sweave) that is another way of 'weaving' (i.e. replace code snippets with the generated code output).  It has a few more advanced features like being able to generate the printed R terminal (showing warnings and errors), more flexibility on graphics, etc.
*  knitr takes in the following input documentation text using __LaTeX__ (__tex__) or __Markdown__ (__rmd__) and code chunks using __R noweb__ (__rnw__). 
*  This produces an output of a __pdf__.  To display code chunks, sweave and knitr have the same format.

Using RStudio, install the package 'knitr' on CRAN.  Then change RStudio's settings under 'Tools' > 'Global Options' > 'Sweave' > 'Weave Rnw files using' > switch from 'Sweave' to 'knitr'.

####<a id="rknitrcode">LaTeX and Markdown showing code using knitr</a>

knitr is included as code chunks just like Sweave.  The only thing is to include `library(knitr)` right at the front of the code chunk.

__LaTeX__

    \documentclass{article}
    
    \begin{document}
    \SweaveOpts{concordance=TRUE}
    <<name="Stuff", fig=TRUE, echo=FALSE>>=
        library(knitr)
        cat("This is some more R code.\n")
    @
    \end{document}

__Markdown__

    ```{r, echo=FALSE}
    library(knitr)
    1+1
    .4-.7+.3  # What?  Why is this not zero?!
    ```
