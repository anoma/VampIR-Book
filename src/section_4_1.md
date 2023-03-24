# Polynomial Logic

The most basic technique for creating propositions in arithmetic circuits is to manipulate polynomials so they have specific roots. The most basic equations are simply checking that a number is a constant, such as;

```haskell
x = 5;
```

However, what if one wanted to say that something is either 5 *or* 6? One can reformulate each as `(x - 5) = 0` and `(x - 6) = 0`. These polynomials can then be multiplied into;

```haskell
(x - 5) * (x - 6) = 0;
```

creating our disjunctive constraint. Similarly, if one have two polynomials, `p` and `q` which must both be equal to `0`, one can form the constraint

```haskell
p^2 + q^2 = 0;
```

The reason one needs to square the values is so that `p`'s (possibly negative) value doesn't cancel out `q`'s (also possibly negative) value. There's also the caveat that squaring and adding very large values could cause those values to exceed the size of the underlying field, so a range check or some other guarantee may need to be made for this to be valid. In most cases, anyone is better off simply stating `p = 0` and `q = 0` as separate constraints.

This basic idea where one treats `0` as denoting truth and use any non-zero value to denote falsity can be extended to a variety of logical operations. Contrast this with the usual presentation of boolean logic, where `0` if false and `1` is true. 

We can easily define higher-order functions which can combine polynomials the same way we might combine field elements.

```haskell
def const x y = x;
def id x = x;
def sum p q x = p x + q x;
def prod p q x = p x * q x;
def comp p q x = p (q x);
def sub p q x = p x - q x;
def div p q x = p x / q x;
```

