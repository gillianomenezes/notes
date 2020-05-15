# First-Class Functions
Functions in Python are first-class objects, because they can be:
- Created at runtime
- Assigned to a variable or element in a data structure
- Passed as an argument to a function
- Returned as the result of a function

## Treating a Function Like an Object
Python functions are objects. Here we create a function, call it, read its `__doc__` atribute, and check that the function object itself is an instance of the function class.  We can assign it a variable fact and call it through that name. We can also pass factorial as an argument to map. The map function returns an iterable where each item is the result of the application of the first argument (a function) to succesive elements of the second argument (an iterable):
```python
>>> def factorial(n): #This is a console session, so we’re creating a function in "runtime".
... '''returns n!'''
... return 1 if n < 2 else n * factorial(n-1)
...
>>> factorial(42)
1405006117752879898543142606244511569936384000000000
>>> factorial.__doc__ #__doc__ is one of several attributes of function objects
'return n!'
>>> type(factorial) #factorial is an instance of the function class
<class 'function'>
>>> fact = factorial
>>> fact
<function factorial at 0x7fa8d7c42840>
>>> fact(5)
120
>>> map(factorial, range(11))
<map object at 0x7fa8d82d83c8>
>>> list(map(fact, range(11)))
[1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880, 3628800]
```
## Higher-Order Functions
A function that takes a function as argument or returns a function as the result is a _higher-order function_. One example is `map`, as shown in previous example. Another is the built-in function `sorted`: an optional `key` argument lets you provide a function to be applied to each item for sorting.
```python
>>> fruits = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
>>> sorted(fruits,key=len)
['fig', 'apple', 'cherry', 'banana', 'raspberry', 'strawberry']
```
### Modern Replacements for map, filter, and reduce
Functional languages commonly offer the map , filter , and reduce higher-order func tions (sometimes with different names). The map and filter functions are still builtins in Python 3, but since the introduction of list comprehensions and generator expressions, they are not as important. A listcomp or a genexp does the job of map and filter combined, but is more readable.
```python
>>> list(map(fact, range(6))) # Build a list of factorials from 0! to 5!.
[1, 1, 2, 6, 24, 120]
>>> [fact(n) for n in range(6)] # Same operation, with a list comprehension.
[1, 1, 2, 6, 24, 120]
>>> list(map(factorial, filter(lambda n: n % 2, range(6)))) # List of factorials of odd numbers up to 5!, using both map and filter .
[1, 6, 120]
>>> [factorial(n) for n in range(6) if n % 2] # List comprehension does the same job, replacing map and filter , and making lambda unnecessary.
[1, 6, 120]
```
The reduce function was demoted from a built-in in Python 2 to the functools module in Python 3. Its most common use case, summation, is better served by the sum built in available since Python 2.3 was released in 2003. This is a big win in terms of readability and performance
```python
>>> from functools import reduce # Starting with Python 3.0, reduce is not a built-in
>>> from functools import reduce # Import add to avoid creating a function just to add two numbers.
>>> reduce(add, range(100)) # Sum integers up to 99.
4950
>>> sum(range(100)) # Same task using sum ; import or adding function not needed.
4950
```
The common idea of sum and reduce is to apply some operation to successive items in a sequence, accumulating previous results, thus reducing a sequence of values to a single value.
## Anonymous Functions
The lambda keyword creates an anonymous function within a Python expression. However, the simple syntax of Python limits the body of lambda functions to be pure expressions. In other words, the body of a lambda cannot make assignments or use any other Python statement such as while , try , etc.
```python
>>> fruits = ['strawberry', 'fig', 'apple', 'cherry', 'raspberry', 'banana']
>>> sorted(fruits, key=lambda word: word[::-1])
['banana', 'apple', 'fig', 'raspberry', 'strawberry', 'cherry']
```
## User-Defined Callable Types
Not only are Python functions real objects, but arbitrary Python objects may also be
made to behave like functions. Implementing a __call__ instance method is all it takes.
```python
import random

class BingCage:

    def __init__(self, items): # __init__ accepts any iterable; building a local copy prevents unexpected side effects on any list passed as an argument
        self._items = list(items)
        random.shuffle(self._items) # shuffle is guaranteed to work because self._items is a list.

    def pick(self): # The main method.
        try:
            return self._items.pop()
        except IndexError:
        raise LookupError('pick from empty BingoCage') # Raise exception with custom message if self._items is empty.

    def __call__(self): # Shortcut to bingo.pick() : bingo()
        return self.pick()
```
Here is a simple demo of example above. Note how a bingo instance can be invoked as a function, and the callable(...) built-in recognizes it as a callable object:
```
>>> bingo = BingoCage(range(3))
>>> bingo.pick()
1
>>> bingo()
0
>>> callable(bingo)
True
```
## Function Introspection
Function objects have many attributes. The following table describes it.
|Name|Type|Description|
|---|---|---|---|---|
|`__anotations__`|dict|Parameter and return annotations|
|`__call__`|method-wrapper|Implementation of the () operator; a.k.a the callable object protocol|
|`__closure__`|tuple|The function closure, i.e., bidings for free variables (often is None)|
|`__code__`|code|Function metadada and function body
|`__defaults__`|tuple|Default values for the formal parameters|
|`__get__`|method-wrapper|Implementation of the read-only descriptor protocol|
|`__globals__`|dict|Global variables of the module where the function os defined|
|`__kwdefaults`|dict|Degault values for the keyword-only formal parameters|
|`__name__`|str|The funtion name|
|`__qualname`|str|The qualified function name, e.g., `Random.choice`|
## From Positional to Keyword-Only Parameters
One of the best features of Python functions is the extremely flexible parameter handling mechanism, enhanced with keyword-only arguments in Python 3. Closely related are the use of * and ** to “explode” iterables and mappings into separate arguments when we call a function.
```python
def tag(name, *content, cls=None, **attrs):
    """Generate one or more HTML tags"""
    if cls is not None:
        attrs['class'] = cls

    if attrs:
        attr_str = ''.join(' %s="%s"' % (attr, value)
                            for attr, value
                            in sorted(attrs.items()))
    else:
        attr_str = ''

    if content:
        return '\n'.join('<%s%s>%s</%s>' %
                        (name, attr_str, c, name) for c in content)
    else:
        return '<%s%s />' % (name, attr_str)
```
Some of the many ways of calling the `tag` function:
```python
>>> tag('br') #A single positional argument produces an empty tag with that name.
'<br />'
>>> tag('p', 'hello') #Any number of arguments after the first are captured by *content as a tuple .
'<p>hello</p>'
>>> print(tag('p', 'hello', 'world'))
<p>hello</p>
<p>world</p>
>>> tag('p', 'hello', id=33) #Keyword arguments not explicitly named in the tag signature are captured by **attrs as a dict .
'<p id="33">hello</p>'
>>> print(tag('p', 'hello', 'world', cls='sidebar')) #The cls parameter can only be passed as a keyword argument.
<p class="sidebar">hello</p>
<p class="sidebar">world</p>
>>> tag(content='testing', name="img") #Even the first positional argument can be passed as a keyword when tag is called.
'<img content="testing" />'
>>> my_tag = {'name': 'img', 'title': 'Sunset Boulevard', 'src': 'sunset.jpg', 'cls': 'framed'}
>>> tag(**my_tag) #Prefixing the my_tag dict with ** passes all its items as separate arguments, which are then bound to the named parameters, with the remaining caught by **attrs
'<img class="framed" src="sunset.jpg" title="Sunset Boulevard" />'
```