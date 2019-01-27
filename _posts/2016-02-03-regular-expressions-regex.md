---
layout: post
title: Regular Expressions
---


# {{ page.title }}


__Regular Expressions__ (aka __regexes__) represent patterns for matching text.
Each character in a regular expression is either a __metacharacter__ (having special meaning) or
a __regular character__ that has a literal meaning.

## Metacharacter vs Regular Character

The regex `a.` is broken down into:

* `a` is a literal character, which matches the character `a`
* `.` is a meta character that matches every character except a newline

The above regex will match text like: `a`, `ax`, `a0`.

## General Patterns

* `.` is a general pattern that matches everything
* `[a-z]` matches all lower case letters from `a` to `z`
* `|` to do a Boolean 'or' (e.g. `gray|grey` matches either `gray` or `grey`)
* `?` indicates __zero or one__ occurrences of the preceding element (e.g. `colou?r` matches `color` and `colour`)
* `*` indicates __zero or more__ occurrences of the preceding element (e.g. `ab*c` matches `ac`, `abc`, `abbc`, `abbbc`)
* `+` indicates __one or more__ occurrences of the preceding element (e.g. `ab+c` matches `abc`, `abbc`, `abbbc`, but not `ac`)
* `{n}` indicates the preceding element is matched __exactly n__ times
* `{min,}` indicates the preceding element is matched __min or more__ times
* `{min,max}` indicates the preceding element is matched at least __min or more__ times, but __not more than max times__.
* 

## How is regex used?

Regex is used programs like `grep` (e.g. grep through some logs)

## Vim

For example, with vim, you can start a search with `/`
If you want to search for just the character `a` by itself, you can use `\<` and `\>` to run: `/\<a\>`

