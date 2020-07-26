# Generate primes using Sieve of Erastosthenes

```python
multiples = set()

for i in range(2, limit + 1):
    
    # If i has not been eliminated already
    
    if i not in multiples:
        yield i
        multiples.update(range(i * i, limit + 1, i))
```
