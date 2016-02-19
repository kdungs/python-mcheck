# mcheck: check monad for Python

_In Haskell terms: `Either String a`_

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


## Definition

In terms of (pseudo-)Haskell, we could define `Check` as follows:

```haskell
data Check a = Pass a | Fail String 

instance Monad Check where
    return x = Pass x  -- is mcheck.return_

    Fail msg >>= f = Fail msg  -- is mcheck.bind
    Pass x >>= f = f x
    
    fail x = Fail x  -- is not implemented
```


we also have Kleisli composition `>=>` which is defined as

```haskell
(>=>) :: Monad m => (a -> m b) -> (b -> m c) -> a -> m c
```

and implemented through `mcheck.compose` and the convenience function
`mcheck.compose_many` which takes an arbitrary number of functions (`a -> m b`)
and folds that list using Kleisli composition.

And then there is `mcheck.lift` which is technically not `liftM` (or `fmap`)
which is defined as

```haskell
liftM :: Monad m => (a -> b) -> m a -> m b
```

while `mcheck.lift` is defined as

```haskell
lift :: (a -> bool) -> String -> (a -> Check a)
```

so maybe `lift` is not such a good name after all…
