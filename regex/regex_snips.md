# Regex snippets

## 1. Split data on digits

```python
import re

def numeric_split(str):
    return re.split('(\d+)', str)

amt = '250gm'    
amt_unit = numeric_split(amt)

print(amt_unit)

# Output

['', '250', 'gm']

```
## 2. Delete the digits

```python
import re

def remove_numbers(str):
    return re.sub(r'\d+', '', str)

prod_code = '1ProductName'
product_name = remove_numbers(prod_code)

print(product_name)

# Output

ProductName

```