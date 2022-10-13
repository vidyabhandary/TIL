### Python Append vs Extend

**Append vs Extend:**

For some reason - I always seemed to forget the difference between append and extend. And I would look it up again.

But the reference link by freecodecamp makes it very simple.

Ironically after I read that I realized, I could have easily taken a hint from the names of the functions !

1. Append - will only add a single element to the list - Be it a single element, a list or tuple etc.

2. Extend - will add all elements of an iterable to the list - It throws an error if you try to extend using a single element. Do note a str or single char will be considered a list, so that will work.

Below examples show the difference, but the one that really captures the difference to me is the 'Hello World' example !!!

```
lst = [1, 3, 5, 6, 7]

# Append a single element
lst.append(77)
# Output - [1, 3, 5, 6, 7, 77]

# Append a list
lst.append([73.7, 73.5, 73.3])
# Output - [1, 3, 5, 6, 7, 77, [73.7, 73.5, 73.3]]

# Append a tuple
lst.append([17, 25, 96])
# Output - [1, 3, 5, 6, 7, 77, [73.7, 73.5, 73.3], [17, 25, 96]]

lst = [2, 4]
# Extend a single integer element
# lst.extend(57)
# Error - 'int' object is not iterable

lstS = [2, 4]
# Extend a single char/str element
lstS.extend('a')
# Output - [2, 4, 'a']

# Extend a list
lst.extend([19, 29.2, 39.3])
# Output - [2, 4, 19, 29.2, 39.3]

# Extend a tuple
lst.extend((37.3, 47.2, 57.1))
# Output - [2, 4, 19, 29.2, 39.3, 37.3, 47.2, 57.1]

# Append a word
lst = ['Hello']
lst.append('World')
# Output - ['Hello', 'World']

# Extend a word
lst = ['Hello']
lst.extend('World')
# Output - ['Hello', 'W', 'o', 'r', 'l', 'd']
```

---

Ref:

1. [FreeCodeCamp - Python List Append vs Extend](https://www.freecodecamp.org/news/python-list-append-vs-python-list-extend/)
