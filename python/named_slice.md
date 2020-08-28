# Named Slices

While using slices is well known - naming a slice is lesser known feature.

The slice function takes 3 parameters - start, stop and step. `[start:stop:step]`.

- `start` (optional) - Starting integer where the slicing of the object starts. Default to `None` if missing.

- `stop` - Integer until which the slicing takes place. The slicing stops at index stop -1 (last element).
Defaults to this if no other parameters are given.

- `step` (optional) - Integer value which determines the increment between each index for slicing. Defaults to `None` if missing.

After defining the slice it object it can then be passed within the square brackets in place of `[start:stop:step]` to access the desired slice.

```python
arr = [0, 25, 50, 75, 100]

# slice(-2, None, None)

last_two = slice(-2, None)
print(arr[last_two])

# slice(None, -2, None)
before_last_two = slice(-2)
print(arr[before_last_two])

# Output

[75, 100]
[0, 25, 50]

```