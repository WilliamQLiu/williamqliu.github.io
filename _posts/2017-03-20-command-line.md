---
layout: post
title: Command Line
---

## {{ page.title }}


- - - -

#Summary

Command line interfaces are powerful, but have a steep learning curve. A GUI is friendlier, but usually doesn't have as many options. You'll often hear RTFM (Read the Fucking Manual), but in reality the manual is even hard to read for a beginner and can get pretty complex even for advanced use cases. Here's some notes:

##Man Pages

Linux manual pages are docs on how to use a command. Run with `man` (e.g. `man man` to see what man does). If there's a number after a command, that is the Man page section. Sections break down into:

* (1) User Commands
* (2) System Calls
* (3) Library functions
* (4) Devices
* (5) File Formats
* (6) Games and Amusements
* (7) Conventions and Miscellany
* (8) System Administration and Priveledged Commands
* (L) Local - some programs install their man pages into this section instead of (1)
* (N) TCL commands

For example, you might type in the man command, the section, and the command you're looking up (`man 1 grep`).

##Synopsis

This will tell you how to use the command and what options are available. Here's how to read them given this format:

    utlity_name[-a][-b][-c option_argument][-d][-e][-foption_argument][operand...]

###Synopsis Summary

* `utility_name` - The utility in this example (e.g. `man`, `grep`, `cat`)
* After the utility name, we have `options`, `option-arguments`, and `operands`
* `options` are the arguments that have a hyphen and single letters or digits (e.g. `-a`). Aka `flags`
* `option-argument` is when an option is followed by an argument, (e.g. `[-c option_argument]`)
* `operands` are the arguments following the last options and option-arguments, (e.g. `[operand...])

####Synopsis Details

* Options are usually listed in alphabetical order
* When a utility has only a few permissible options, they are sometimes shown individually. Utilities with many flags generally show all of the individual flags (that do not take option-arguments) grouped. Here's the two examples:

Example utility with only a few options:

    utility_name [-abcDxyz][-p arg][operand]

Example utility with very complex arguments:

    utility_name [options][operands]

####To space or not to space in option_argument

If the synopsis of a utility shows a **space** between an option and an option-argument (e.g. `[-c option_argument]`), a conforming application should use separate arguments for that option and its option-argument.

If a space is not shown (e.g. `[-foption_argument]`), then a conforming application won't add a space (and will just place an option and its option-argument directly adjacent in the same argument string).

###[]

Square parameters mean that the stuff inside is optional. 

* `[optional]`
* `[-abcgln]`

###{}

Braces with a list of comma separated values mean a limited list of choices.

* `{one,two,three}`
* `{yes,no}`

###<>

Less-than and greater-than symbols mean a mandatory parameter. Usually these values have embedded underscores (e.g. `file_name`)

* `<mandatory>`
* `<file_name>`

###bolded text or 'typewriter' font

If there is bolded text or the typewriter font on text, this means a literal command line input (e.g. `man`, `grep`). Type it in as is.

###italics

If there is italics, this means its a variable that you can replace.

###|

Vertical bars `|` mean these are separate choices (mutually exclusive, cannot both be true). If there are mutually-exclusive options and operands, use multiple synopsis lines.

Example of a mutually exclusive utility_name appearing on multiple lines of the synopsis:

    utility_name -d[-a][-c option_argument][operand...]
    utility_name [-a][-b][operand...]

###...

Ellipses (`...`) mean that one or more occurences of an option or operand ar allowed.

    utility_name -f option_argument...[operand...]
    utility_name [-g option_argument]...[operand...]

Uhh what? So in the first example, each `option_argument` needs a `-f` in front and you can have multiple option_arguments.

##Commands

###apropros

`apropos` searches the descriptions for instances of a keyword.

    `apropos search`

###whatis

`whatis` to display a short description from the manual page, if available.

    `whatis grep`
    # git-grep(1)              - Print lines matching a pattern
    # grep(1), egrep(1), fgrep(1), zgrep(1), zegrep(1), zfgrep(1) - file pattern searcher

