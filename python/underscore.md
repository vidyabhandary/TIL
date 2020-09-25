# Python Single Underscore

### 1. Leading Underscore: _variable

This is a hint that indicates to other developers - this variable is not 
meant to be used outside the class / method. Be warned - this is not enforced by 
the Python interpreter. 

```
class gene:
    def __init__(self):
        self._dna = 'TCGAAGGGGTTTCCC'

```

### 2. Trailing Underscore: variable_

To use a reserved name as a variable name - append it with '_'. Avoids naming 
conflicts with Python keywords.

```
raise # Will raise an error
    
raise_ # No issues
```

### 3. Single underscore in for loop

Using a single _ in a for loop is to indicate the variable is temporary or not of importance.

```
for _ in words:
    do_something()

```

### 4. Single underscore to unpack

To ignore values that one does not care about

```
medicals = ('height', 'eye_color', 'race', 'weight')
175, _, _, 125 = medicals

```
