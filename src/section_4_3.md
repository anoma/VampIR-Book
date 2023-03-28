# Bit Decomposition and Range Checks
One of the most common, and also heaviest, operations necessary for the execution of regular programs is bit decomposition, where a number is decomposed into the binary bits making it up. One of the most common operations necessary for basic arithmetic is checking that a number is within a certain range. Both of these operations are typically done simultaneously. If a number can be decomposed into 8 bits, then it must be in the range \\([0, 2^8)\\).

To get the nth bit of a number, one merely needs to integer-divide by \\(2^{n-1}\\), then mod by \\(2\\). Repeating this for every bit one cares about will get us the full decomposition.

```haskell
def decomp8 x = {
  def x0 = fresh ((x\2^0) % 2); isBool x0;
  def x1 = fresh ((x\2^1) % 2); isBool x1;
  def x2 = fresh ((x\2^2) % 2); isBool x2;
  def x3 = fresh ((x\2^3) % 2); isBool x3;
  def x4 = fresh ((x\2^4) % 2); isBool x4;
  def x5 = fresh ((x\2^5) % 2); isBool x5;
  def x6 = fresh ((x\2^6) % 2); isBool x6;
  def x7 = fresh ((x\2^7) % 2); isBool x7;
  x = x0 + 2*x1 + 2^2*x2 + 2^3*x3 + 2^4*x4 + 2^5*x5 + 2^6*x6 + 2^7*x7;
  (x0, x1, x2, x3, x4, x5, x6, x7)
};

decomp8 166 = (0, 1, 1, 0, 0, 1, 0, 1);
```

Notice that each bit is actually checked to ensure it's a boolean. Additionally, we check that the decomposition is correct prior to returning the tuple. This implementation returns bits in little-endian format, though this can easily be changed to suit the needs of the user.

We can automatically generate bit decompositions using `iter`.

```haskell
def decomp_rec bits a = {
  def a0 = fresh (a%2); isBool a0;
  def a1 = fresh (a\2);
  a = a0 + 2*a1;
  (a0 : bits a1)
};
def decomp n = iter n decomp_rec (fun x {[]});

decomp 8 166 = 0:1:1:0:0:1:0:1:[];
```

By modifying the input number prior to checking the range, the range can be modified. For example, the following checks that something is in the range \\([-2^7, 2^7)\\)

```haskell
def intDecomp n x = decomp n (x + 2^n);

intDecomp 8 ((-55)) = 1:0:0:1:0:0:1:1:[];
```

This will return what is \textit{almost} the two's complement representation. The main difference is that the last bit, indicating the sign, is the opposite of what it is in usual presentations of two's complement. With it, the sign of a number can be checked.

```haskell
def isNegative n a = last (intDecomp n a);
def isPositive n a = 1 - last (intDecomp n (a-1));
```

and one can further check if one number is less than another. The following will act as a valid \\(<\\) indicator, so long as both `x` and `y` are in the range \\([-2^{n-2}, 2^{n-2})\\).

```haskell
def less n a b = isNegative n (a - b);
```

Using these range checks, an arbitrary range whose width is less than twice the original can be checked via overlapping checks. The following will check that a number, `x`, is within \\([a, b)\\), so long as \\(b > a\\) and \\(b - a < 2^{n+1}\\).

```haskell
def range n x a b = {
  decomp n (x - a); // check x in [a, 2^n + a)
  decomp n (x + 2^n - b) // check x in [b - 2^n, b)
};
```

