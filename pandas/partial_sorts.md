# Partial Sorts Partitioning

To find the k smallest values in an array - can use the `np.partition` function.
`np.partition` takes an array and a number K; the result is a new array with the smallest K values to the left of the partition, 
and the remaining values to the right, in arbitrary order.

```python

x = np.array([7, 2, 3, 1, 6, 5, 4])
np.partition(x, 3)

# Output

array([2, 1, 3, 4, 6, 5, 7])

```
The first three values in the resulting array are the three smallest in the array, and the remaining array positions contain the remaining values.

Ref : [Partial Sorts: Partitioning - Jake Vanderplas](https://github.com/jakevdp/PythonDataScienceHandbook/blob/master/notebooks/02.08-Sorting.ipynb)
