# The Iter Function

Frequently, we will want to repeat a function over and over again. Vamp-IR includes the `iter` function for this exact purpose. It takes three arguments;

1. A non-negative constant.
2. A function to iterate.
3. An argument to iterate on.

For example, if the exponential function was not already built-in, we could implement it via `iter`.

```haskell
def exp_rec m r = m * r;
def exp m n = iter n (exp_rec m) 1;

exp 3 2 = 9;
```

`iter` must be able to be unfolded at circuit-generation time, meaning the constant cannot be a field element that would be decided at proving time. `iter x f 3` for an unbound `x` will give an error indicating that `x` must be a constant.

The main limitation of `iter` is that the function has to take a (simply typed) X and produce another X. If the input and output types do not match, we will get a typing error. This largely prevents useful interactions between `iter` and tuples. For example, trying the following

```haskell
iter 2 tail (1, 2, 3, 4, ());
```

will yield an error indicating that `([138], [137])` cannot unify with `[137]`. This tells us that the input and output types to `tail` cannot be made the same; the input must be a tuple, `([138], [137])`, while the output must be of the same type as the second element of the tuple, `[137]`.

