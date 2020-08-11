# Python string.punctuation

Python's `string.punctuation` contains list of special characters that can be used
to strip punctuation from a sentence.

Chars in `string.punctuation` are

> ### !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~

```
import re
import string

sentence = 'Algorithms, Euler"s problems and Random Code generators !!! '

# Regex 
regex = re.compile('[%s]' % re.escape(string.punctuation))

sentence = regex.sub('', sentence)

print(sentence)
```

Output

```
Algorithms Eulers problems and Random Code generators
```
