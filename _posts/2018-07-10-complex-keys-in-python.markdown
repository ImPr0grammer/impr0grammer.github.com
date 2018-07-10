---
layout: post
title:  "Complex keys in dictionaries in Python"
date:   2018-07-10 16:03:21 +0300
tags: [ python, programming, "python-tips" ]
---

Let's imagine you want to store list or dictionary as a key in python dict.
And you are going to write the code like this:

{% highlight python %}
dict_key = {'a': 3, 'b': 2}
stat = {}
stat[dict_key] = 1
{% endhighlight %}

It's a bad code because your are trying to use mutable object as a key in dictionary.
And Python will check it and this code will throw an error:
{% highlight python %}
TypeError: unhashable type: 'dict'
{% endhighlight %}

Let's consider how to use complex objects and data structures as dict keys in Python.

<!--more-->

Let's read the <a href="https://docs.python.org/3/glossary.html#term-hashable">documentation</a> and we see that Python doesn't allow to store <b>dict</b> and <b>list</b> as dictionary or set key.

The reason is that <b>dict</b> and <b>list</b> are <b>mutable</b> and Python protect you from errors like this:
{% highlight python %}
dict_key = {'a': 3, 'b': 2}
stat = {}
stat[dict_key] = 1

# Change the key and you'll not find it on dictionary
dict_key['c'] = 3
print(stat[dict_key])
{% endhighlight %}

### Convert Dict to frozenset before use as dictionary key
Use frozenset for dictionary key-value list.

Let's have a look at the example:
{% highlight python %}
dict = {'a': 3, 'b': 2}
dict_key = frozenset(dict.items())
stat = {}
stat[dict_key] = 1
{% endhighlight %}

If you want to search you should also use frozenset
{% highlight python %}
print(stat[dict_key])
{% endhighlight %}

### Convert List to tuple before using as dictionary key

Let's have a look how to do it:
{% highlight python %}
list = [1, 2, 3]
list_key = tuple(list)
stat = {}
stat[list_key] = 1
{% endhighlight %}

If you want to search you should also use tuples:
{% highlight python %}
print(stat[list_key])
# prints 1
{% endhighlight %}

Note: if you change order of elemenets in list you'll not find it in dictionary. 
Use frozen set for it

### For Objects you have to implement hashable methods
What happens if you to use your Class objects as key? By default Python uses method id() as a key. So every object will be treated as a new key. That's not what you want to have. 

Let's have a look:
{% highlight python %}
class Point:
	def __init__(self, x, y):
		self.x = x
		self.y = y

stat = {}
stat[Point(1, 2)] = 1
print(stat.get(Point(1, 2)))
# prints None
{% endhighlight %}

For proper work it should implement methods <a href="https://docs.python.org/3/reference/datamodel.html#object.__hash__">__hash__</a> and <a href="https://docs.python.org/3/reference/datamodel.html#object.__eq__">__eq__</a>

Let's do it and see the result:
{% highlight python %}
class Point:
	def __init__(self, x, y):
		self.x = x
		self.y = y
    
	def __hash__(self):
		return self.x ^ self.y
    
	def __eq__(self, other):
		return other and self.x == other.x and self.y == other.y

stat = {}
stat[Point(1, 2)] = 1
print(stat.get(Point(1, 2)))
# prints 1
{% endhighlight %}


Recap:
* Use <b>frozendict(dict.items())</b> if you want to store dict as a key in other dictionary or set;
* Use <b>tuple(list)</b> if you want to store list as a key in dictionary;
* Implement <b>__hash__</b> and <b>__eq__</b> if you want to use your Class objects as dictionary keys. Also don't forget that it should be immutable.


Stay tuned, check out [my blog][blog-main] for more useful tips and tricks.

[blog-main]: https://impr0grammer.github.io
[github-repo]: https://github.com/impr0grammer