# Selecting columns

Different ways to select columns in a Pandas dataframe

1. Pass a string matching a column name to the bracket operator to return a pandas series

```python
# order is a pandas dataframe
orderdemo = order['ordertype']

type(orderdemo)

>>> pandas.core.series.Series

```


2. Pass a list of one (or more) element with a column name and a DataFrame is returned

```python
# order is a pandas dataframe
orderdemo = order[['ordertype']]

type(orderdemo)

>>> pandas.core.frame.DataFrame

```

3. Filter with `like` for columns

```python
# nls97 is a pandas dataframe
analysiswork = nls97.filter(like='weeksworked')

analysiswork.info()

>>> <class 'pandas.core.frame.DataFrame'>
Int64Index: 8984 entries, 100061 to 999963
Data columns (total 18 columns):
 #   Column         Non-Null Count  Dtype  
---  ------         --------------  -----  
 0   weeksworked00  8603 non-null   float64
 1   weeksworked01  8564 non-null   float64
 2   weeksworked02  8556 non-null   float64
 3   weeksworked03  8490 non-null   float64
 4   weeksworked04  8458 non-null   float64
 5   weeksworked05  8403 non-null   float64
 ...
 17  weeksworked17  6670 non-null   float64
dtypes: float64(18)
memory usage: 1.3 MB
```

4. Select all columns with the category data type

``` python
# nls97 is a pandas dataframe
analysiscats = nls97.select_dtypes(include=['category'])

analysiscats.info()

>>> <class 'pandas.core.frame.DataFrame'>
Int64Index: 8984 entries, 100061 to 999963
Data columns (total 57 columns):
 #   Column                 Non-Null Count  Dtype   
---  ------                 --------------  -----  
 0   gender                 8984 non-null   category
 1   maritalstatus          6672 non-null   category
 2   weeklyhrscomputer      6710 non-null   category
 3   weeklyhrstv            6711 non-null   category
 ...
 55  colenrfeb17            6733 non-null   category
 56  colenroct17            6734 non-null   category
dtypes: category(57)
memory usage: 580.8 KB

```

5. Select all columns with numeric data types

``` python
# nls97 is a pandas dataframe
analysisnums = nls97.select_dtypes(include=['number'])

analysisnums.info()

>>> <class 'pandas.core.frame.DataFrame'>
Int64Index: 8984 entries, 100061 to 999963
Data columns (total 31 columns):
 #   Column                 Non-Null Count  Dtype  
---  ------                 --------------  -----  
 0   birthmonth             8984 non-null   int64  
 1   birthyear              8984 non-null   int64  
 2   highestgradecompleted  6663 non-null   float64
 3   childathome            4791 non-null   float64
 4   childnotathome         4791 non-null   float64
 ...
 30  weeksworked17          6670 non-null   float64
dtypes: float64(29), int64(2)
memory usage: 2.2 MB

```

6. Regex for column names

```python
# nls97 is a pandas dataframe
nls97.filter(regex='income')

>>> 	
personid wageincome	govincomediff		
100061	12500.0	    NaN
100139	120000.0	NaN
100284	58000.0	    NaN
...	...	...
999963	50000.0	    NaN

8984 rows × 2 columns

```

Ref : [Chapter 3:  Python Data Cleaning Cookbook by Michael Walker](https://github.com/PacktPublishing/Python-Data-Cleaning-Cookbook)
