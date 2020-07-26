# Python set_default() method

Python's `set_default` method helps set a value in a dictionary if that key does not exist.

E.g. - Setting an empty list for a key that does not exist in the dictionary.

```python
til_dt = all_tils.setdefault(til_date, [])
```

If value for that date is not present will get an empty list returned.

Ref : [Automate The Boring Stuff](https://automatetheboringstuff.com/chapter5/)
