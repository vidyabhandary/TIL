# Basic MapReduce Understanding

## SQL 
The sample query below allows to aggregate over groups.
It has a nested query and uses the output of an inner query in an outer one.

Query to find the percentage of executions from each county.

```SQL
SELECT
  county,
  100.0 * COUNT(*) / (SELECT COUNT(*) FROM executions)
    AS percentage
FROM executions
GROUP BY county
ORDER BY percentage DESC
```

This is MapReduce in SQL. 
MapReduce is a famous programming paradigm which views computations as occuring in a "map" and "reduce" step.

Ref : [Nested Query - SelectStar](https://selectstarsql.com/longtail.html#nested)

## Python

### map()
The map() function executes a specified function for each item in a iterable. The item is sent to the function as a parameter.

```python
data = [1, 2, 3, 4, 5, 6]
mapped_result = map(lambda x: x*2, data)
```

Output
```
[2, 4, 6, 8, 10, 12]
```

Ref : [Map Function - W3Schools](https://www.w3schools.com/python/ref_func_map.asp)

### reduce()

A reduce repeatedly applies a given operation to the elements of an array until **only a single result remains**.

```python
import numpy as np

x = np.arange(1, 6)
np.add.reduce(x)
```

Output
```
15
```

Ref : [Reduce - DataScienceHandbook](https://github.com/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/02.03-Computation-on-arrays-ufuncs.ipynb)

## Combining map and reduce

```python
data = [1, 2, 3, 4, 5, 6]
mapped_result = map(lambda x: x*2, data)

final_result = reduce(lambda x, y: x+y, mapped_result)
```

Output
```
42
```
Ref : [Simple explanation of MapReduce - Stackoverflow](https://stackoverflow.com/questions/28982/simple-explanation-of-mapreduce)


More References

- [Filter, Map, Reduce explained in less than 2 minutes](https://www.youtube.com/watch?v=PZvHZJVeYdw)

- [Halloween chocolate distribution](https://webofdata.wordpress.com/2012/11/05/mapreduce-for-kids/)

