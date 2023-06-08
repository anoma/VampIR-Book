# Boolean Logic

One can easily check that a value is a boolean by checking if it's either `0` or `1`.

```haskell
def isBool x = (x * (1 - x) = 0);
```

If one already knows a value is a boolean, they can perform all the standard boolean operations;

```haskell
def negb x = 1 - x;
def andb x y = x * y;
def orb x y = negb (andb (negb x) (negb y));
```

Rather than defining boolean functions, we may also define full constraints which may be more efficient for some operations. For example, the constraint \\(x \oplus y = z\\), where \\(\oplus\\) represents the xor function, can be implemented as;

```haskell
def xor x y z = 2 * x * y = x + y - z;
```

We can define a selection constraint, `b ? x : y = z`, via;

```haskell
def select b x y z = b * (y - x) = (y - z);
```

Possibly the most basic predicate over numbers is equality. While this can be checked with `=`, one may need to represent the result with a boolean value instead for later complication. One can test if a number is `0` with the following modification of the program appearing in the section on [gated witnesses](section_3_4.md).

```haskell
def isZero x = {
  def xi = fresh (1 | x);
  x * (1 - xi * x) = 0;
  1 - xi * x
};
```

One can then define an equality predicate as

```haskell
def equal x y = isZero (x - y);
```

Through a slight modification, inequality can also be defined.

