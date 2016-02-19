# Check monad for Python

Allows you to build pipelines of checks on values. For example

```python
import mcheck as c

pipeline = c.compose_many(
  c.lift(lambda x: x > 0, 'Value has to be positive.'),
  c.lift(lambda x: x < 100, 'Value has to be smaller than 100.'),
  c.lift(lambda x: not x & 1, 'Value has to be even.'),
  c.lift(lambda x: not x & (x - 1), 'Value has to be a power of two.')
)

result = pipeline(34)
print(result.message)
```

will result in the output `Value has to be a power of two.`.
