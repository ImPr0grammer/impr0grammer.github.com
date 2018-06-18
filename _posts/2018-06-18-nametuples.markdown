---
layout: post
title:  "Named tuples in Python"
date:   2018-06-18 17:23:19 +0300
tags: [ python, programming, "python-tips" ]
---

Today I want to talk about tuples. 
Code with tuples usually looks like this:
{% highlight python %}
def calculate_invoice():
    days = 14
    cost = 14 * 30
    return (days, cost, "Additional Information")

days, cost, info = calc()
{% endhighlight %}

Tuples are very convenient, epecially in scripts and prototypes.
But it's hard to read large code with tuples.

What should we use instead, Classes?
<!--more-->

There is an elegant solution in Python - <b>namedtuple</b> <a href="https://docs.python.org/3/library/collections.html#collections.namedtuple"> see python 3 docs</a>.
Namedtuple - is a tuple with specified name. Every tuple field also has name.
So it's similar to original tuples but also it's similar to classes. And it makes code much more clear.

Let's rewrite orinial code to see how it works.
{% highlight python %}
from collections import namedtuple

Invoice = namedtuple('Invoice', ['days', 'cost', 'additional_info'])

def calculate_invoice():
    days = 14
    cost = 14 * 30
    return Invoice(days, cost, "Additional Information")

invoice = calculate_invoice()
print(invoice.cost)
{% endhighlight %}

You can see that this code is much easier to read!

Fiew more things to now:
- Namedtuple is readonly you can't change with "normal" code.
- There is a convenient method <a href="https://docs.python.org/3/library/collections.html#collections.somenamedtuple._replace">_replace</a>. Using it you can change one field's value and get a new tuple.
- There is a useful <a href="https://docs.python.org/3/library/collections.html#collections.somenamedtuple._asdict">_asdict</a> that returns dictionary.

Try namedtuple in the next project if it fits you.

Stay tuned, check out [my blog][blog-main] for more useful tips and tricks.

[blog-main]: https://impr0grammer.github.io
[github-repo]: https://github.com/impr0grammer