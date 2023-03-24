# Expanded Arithmetic


In addition to the main field operations, there are three more operations available.

`\` calculates integer division, rounded toward 0.

```haskell
(3 \ 2) = 1;
(5 \ 2) = 2;
(233 \ 55) = 4;
```

Dividing with negatives can seem to behave strangely, as integers are converted into field elements. Generally, one will observe that `((-k) \ n) = ((-1) \ n) - (k \ n)`.

```haskell
((-3) \ 2) = ((-1) \ 2) - 1;
((-5) \ 2) = ((-1) \ 2) - 2;
((-233) \ 55) = ((-1) \ 55) - 4;
```

and dividing by a negative will usually end up as 0 since it gets interpreted as dividing by a very large number.

```haskell
(2 \ (-1)) = 0;
(2234 \ (-100)) = 0;
(5555555 \ (-2)) = 0;
```

`%` calculates the modulus.

```haskell
(3 % 2) = 1;
(5 % 2) = 2;
(233 % 55) = 4;
```

The modulus has similar strange behavior when interacting with negatives. Taking the modulus of a negative number can vary based on the underlying field size, as `(-n) % k` will be the same as  `p - n % k`, where `p` is the field size. For a prime field of size

52435875175126190479447740508185965837690552500527637822603658699938581184513

we'd have

```haskell
(-6) % 5 = 2;
(-233) % 55 = 10;
```

taking the modulus by a negative will usually end up doing nothing as it's interpreted as taking the modulo by a very large number.

```haskell
(2 % (-1)) = 2;
(2234 % (-100)) = 2234;
(5555555 % (-2)) = 5555555;
```

`|` calculates ordinary division in the underlying field, however, it doesn't generate an error when dividing by 0.

```haskell
15|3 = 15/3;
15|0 = 0;
```

