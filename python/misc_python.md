# Miscellaneous pythonisms

### 1. A tuple can be created even with no brackets

```python
my_tuple = 15,
```

### 2. A list no brackets gets initialized into a tuple 

```python
a = 2, 3, 4, 5
```

### 3. try, except and else

```python
try:
  # statements that can raise exceptions
except:
  # statements that will be executed to handle exceptions
else:
  # statements that will be executed if there is no exception
```

### 4. Assign array items without looping

```python
arr = [0, 25, 50, 75, 100]
zero, quarter, half, three_fourth, full_percent = arr
print(zero, quarter, half, three_fourth, full_percent)

# Output

0 25 50 75 100
```

### 5. Intelligent Unpacking

```python
a, *b, c = [1, 2, 3, 4, 5]
print(a, b, c)

# Output

1 [2, 3, 4] 5

```

### 6. Enumerate

To get the index and value of an array / list.

```python
arr = [0, 25, 50, 75, 100]

for index, value in enumerate(arr):
    print(index, value)

# Output

0 0
1 25
2 50
3 75
4 100

```

### 7. Group Adjacent Lists

Using zip and lambda functions

```python
a = [1, 2, 3, 4, 5, 6]  
group_adjacent = lambda a, k: zip(*([iter(a)] * k)) 
print(list(group_adjacent(a, 3)))
print(list(group_adjacent(a, 2)))
print(list(group_adjacent(a, 1)))

# Output

[(1, 2, 3), (4, 5, 6)]
[(1, 2), (3, 4), (5, 6)]
[(1,), (2,), (3,), (4,), (5,), (6,)]

```