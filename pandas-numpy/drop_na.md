# Drop rows with no values


1. Pandas method to drop rows with no values in multiple columns

```python
# order is the pandas dataframe
order.dropna(subset=order.columns[1:], how='all', inplace=True)

```


2. Pandas method to drop rows with no values in a specific column 
```python
# order is the pandas dataframe
order.dropna(subset=['columnname'], inplace=True)

```
Ref : [Python Data Cleaning Cookbook by Michael Walker](https://github.com/PacktPublishing/Python-Data-Cleaning-Cookbook)
