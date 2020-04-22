#Dictionary and Sets
dict objects keys must be hashable (but the values doesn't need to be). An object is hashable if it has a hash values that never changes along its life cycle (should have __hash__() method) and could be compared to others objects (must have __eq__() method.

###dict comprehension
After python 2.7 the listcomps and genexps sintax has been applied to dict comprehensions.

Example:
```
>> dial_codes = [(1, "EUA"), (7, "Russia"), (55, "Brasil"), (86, "China")]
>> country_codes = {code:country for code, country in dial_codes}
>> country_codes[55]
'Brasil'
```
###Handling missing keys with setdefault
Accordingly to the fail fast philosophy, access to dict with d[k] raises an error when k is a inexistent key. So, if you want to set a value for a dict and handle the inexistent key problem, this could be the first idea that may come to your mind:
```
if key not in my_dict:
   my_dict[key] = []
my_dict[key].append(new_value)
```
But the problem here is executing up to three searches for the key. Luckily, Python supports dict.setdefault to search and update a dict in the same execution:
```
my_dict.setdefault(key,[]).append(new_value)
```
Another solution for the same problem would be the use of collections.defaultdict:
```
>> from collections import defaultdict
>> my_dict = defaultdict(list)
>> my_dict["key"]
[]
```
###Imutable mappings
Since Python 3.3 types module offers a wrapper class called MappingProxyType; given an mapping, this class returns an mappingproxy instance, which is a read only view from the original mapping.
Example:
```
>> from types import MappingProxyType
>> d = {1: 'A'}
>> d_proxy = MappingProxyType(d)
>> d_proxy[1]
'A'
>> d_proxy[2] = 'x'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'mappingproxy' object does not support item assignment
>> d[2]='x'
>> d_proxy[2]
```
###Set Theory
The sets are a collection of unique objects. An basic use case is to remove duplications:
```
>> set(l)
{'spam', 'eggs'}
>> list(set(l))
['spam', 'eggs']
```
The set class also implements essetial operations for sets like infix operators; that is, given two sets a and b, a | b returns the union, a & b return the intersection, and a - b returns the difference between both sets.
###Set comprehensions
Set comprehencsions (_setcomps_) were added toPython 2.7 along with dictcomps. Following an example:
```
>> {letter for letter in list("arara")}
{'a','r'}
```
###Set operations
Python `sets` offers math operations. Example below:
```
>> a = {1,2,3}
>> b = {2,3,4}
>> a & b #intesect (and) operator
{2,3}
>> a | b #union (or) operator
{1,2,3,4}
>> a ^ b #exclusive or operator
{1,4}
>> a >= b #is it 'a' a superset of 'b'?
False
>> a <= b #is it 'a' a subset of 'b'?
False
```