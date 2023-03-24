# More Arithmetic

With range checks in place, we can get a more complete set of arithmetic operations. The division with remainder for positive $a$ and $b$ can be checked with the constraints;

```haskell
def div_rem n a b = {
  def q = fresh (a\b);
  def r = fresh (a%b);
  
  isNegative n r = 0;
  less n r b = 1;
  a = b * q + r;

  (q, r)
};
```
