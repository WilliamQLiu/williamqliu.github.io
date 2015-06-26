---
layout: post
title: Markdown Basics
---

## {{ page.title }}

- - - -

##Table of Contents

*  [Summary](#summary)
*  [Headers](#headers)
*  [Text (paragraphs)](#text)
*  [Text Styling (bold, italics)](#textstyling)
*  [Lists (ordered, unordered)](#lists)
*  [Links](#links)
*  [Code](#code)

##<a id="summary">Summary</a>

Markdown is a simple to read and write text-to-HTML document.  I'm just going to include the markdown examples.  If you want to see the text, just add `.text` to the file or URL and you'll see the actual markdown code.


##<a id="headers">Headers</a>

First Level header
====================

Second Level Header
-------------------

## Also Second Level Header

### Third Level Header

##<a id="text">Text</a>

This is just a regular paragraph.  I used the guide from here

This is another paragraph.  You can view the markdown text for a file 'Markdown_Basics.md' by going to 'Markdown_Basics.md.text'

> This is a blockquote.
> 
> ### This is a header in a blockquote


##<a id="textstyling">Text Styling (bold, italics)</a>

For simple emphasis, do this *to emphasize*.

For simple emphasis, you can also do this _to emphasize_.

For stronger emphasis, do this **to strong emphasize**

For stronger emphasis, you can also do this __to strong emphasize__

##<a id="lists">Lists (unordered, ordered)</a>

Unordered (bulleted) lists use asterisks, pluses, and hyphes (*,+, and -) as list markers.  This produces the same output

* candy
* gum
* booze

+ candy
+ gum
+ booze

- candy
- gum
- booze

***

* stuff
    
  With multiple paragraphs.
  
  More paragraphs
* more stuff


##<a id="links">Links</a>

This is an [inline link](http://jobwaffle.com)

This is an [inline link with a title](http://jobwaffle.com "With a Title").

Reference-style links allow you to refer to your links by names like [Google][1] or [Markdown Basics][2].

[1]: http://google.com    "Google"
[2]: https://daringfireball.net/projects/markdown/basics  "Markdown Basics"

Link names may contain letters, numbers, and spaces, but are not case sensitive.

I start my morning with a cup of coffee and [The New York Times][NY Times]

[ny times]: http://www.nytimes.com

Image syntax is much like link syntax

Inline is like this ![alt text](http://commons.wikimedia.org/wiki/File:Google.png "Title")

Reference-style is like this ![alt text][myid]

[myid]: http://commons.wikimedia.org/wiki/File:Google.png "Title"

##<a id="code">Code</a>

Here's how to put code inline `print "Hello World"`

Here's how to put a code block

    import pandas
    
    print "Hello World"

<!-- This is a comment; the below might not render -->
