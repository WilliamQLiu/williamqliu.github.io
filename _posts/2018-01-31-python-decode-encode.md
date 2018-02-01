---
layout: post
title: Python (Decode and Encode)
---


# {{ page.title }}

For Python 2 developers dealing with data, you'll probably run into an encoding or decoding issue. If you're in Python 3, consider yourself lucky that text is unicode and that a few of these problems go away.

Here's my (probably oversimplified) notes from working with encoding and decoding data for Python 2. I highly recommend reading [The Absolute Minimum Every Software Developer Absolutely Must Know About Unicode and Character Sets](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)

There's __Unicode__, where letters map to a __code point__ (a theoretical concept). These code points are mapped so that the character `A` is also mapped to the lower case `a` or an italicied or bold version. What's cool is that other languages like the Arabic letter `Ain` has its own mapping. You can see exactly what is mapped where at the [Unicode web site](http://www.unicode.org/)

The idea is that there are character encodings with the default encoding of __ascii__, which has the bare minimum English characters. We also have other encodings like __utf-8__, utf-16__, __latin-1__, etc. Some take 8 bits, others 16, etc. You hopefully know what this encoding is (if not, I use the below libraries to help guess, but again this is only a guess).

## Encode and Decode

Encoding and Decoding means to change formats.

* __Encoding__ is to change your data into a new format.

An example would be:

	my_str.encode('ascii', 'ignore')  # encode your string into ascii format, ignore any errors

* __Decoding__ is to read the data from a specific format.

An example would be:

	my_str.decode('ascii')

## Python 2 types

In Python 2, we're dealing with instances of either __strings__ (bytes, not really built to support different encodings) or __unicode__.  In Python 3 its only unicode. If you know the encoding (e.g. latin-1, ascii, utf-8), then explicitly state it when you decode it. Otherwise, you can try these libraries for a guess:

### unicode w/ unidecode

	from unidecode import unidecode

	my_str = u'idzie wąż wąską dróżką'
	unidecode(my_str)

### str w/ chardet

	import chardet

	my_str = 'hello world'
	chardet_result = chardet.detect(my_str)
	my_str.decode(chardet_result['encoding'])

## Databases

Besides just making sure your code works, make sure that your database character sets are correct.

mysql> use some_database_name;
	 > show variables like 'character_set_database'
	 +------------------------+---------+
	 | Variable_name          | Value   |
	 |------------------------+---------|
	 | character_set_database | latin1  |
	 +------------------------+---------+

