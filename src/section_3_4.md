# Gating Witnesses


By default, any circuit we generate will not know anything about fresh witnesses. They are just numbers, unless we add additional constraints to give them meaning. Consider this function which indicates if a number is not zero.

```haskell
def isntZero x = fresh (1 | x) * x;

isntZero ((-1)) = 1;
isntZero 0 = 0;
isntZero 1 = 1;
isntZero 2 = 1;
```

While this generates a valid proof, what is it proving? Essentially just that we know of inverses for -1, 1, and 2, as well as a number that is 0 when multiplied by 0. Since the correctness of `isntZero` isn't actually checked, we haven't actually proved that any of these numbers are or aren't 0, even though we've calculated the answer correctly. We can fix this by adding additional constraints.

```haskell
def isntZero x = {
  def xi = fresh (1 | x);
  x * (1 - xi * x) = 0;
  xi * x
};
```

That new constraint checks that either `x = 0` or `xi * x = 1`. It's worth thinking carefully about how this constraint is affecting the output of the function. If the input is non-zero, we can calculate its inverse. By definition, this inverse will be 1 when multiplied by the input. If the input is zero, the inverse cannot exist, so checking that `xi * x = 1` guarantees that `x` is not zero. If `x` is zero, the constraint becomes trivial. This means the value of `xi` is now unconstrained. However, `xi`, being multiplied by `x` in the output, will not be able to affect the output's value. Ultimately, the constraint forces the output to be unique. If we calculated witnesses incorrectly, then the constraint would fail and the proof would be invalid.

This sort of thinking is at the heart of any meaningful usage of `fresh`. Whenever it's used, we should think of the circuit as defining a relation whose validity ranges over every possible value. By putting constraints on the possible values of a fresh witness, we are narrowing that relation, making the statement of the circuit more specific.
