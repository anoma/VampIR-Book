# The fresh keyword


It's not always practical to calculate values purely using the operations over a finite field. As such, Vamp-IR adds an additional construct for calculating values, called `fresh`.

```haskell
fresh 22 = 22;
```

This expression can call all arithmetic operators described in section on [arithmetic](section_2_1.md), along with additional operators not native to arithmetic circuits which will be detailed in [an upcoming section](section_3_2.md).

`fresh` can make unrestricted calls to variables within the environment, and it is often used to calculate values involving them.

```haskell
fresh (x / y) = 2;
```

Anything called inside of fresh is essentially a black box to the circuit. The circuit, in this case, does not verify that the result is actually the result of a quotient; rather, it's just a witness value, like any provided by the end user during witness solicitation. `fresh` allows us to calculate such witnesses automatically instead of them being provided by the end user. This means that properties of fresh witnesses need to be enforced by separate constraints in order to prove anything; see the section on [gated witnesses](section_3_4.md) for details.

The fact that `fresh` acts as a witness generator can be observed with the following code;

```haskell
6 = fresh 15 % 9;
```

Similar to the situation described in the section on [expanded arithmetic](section_3_2.md), an error declaring that `%` is an unsupported constraint is generated. `fresh (15)` does not get turned into a number; it gets turned into a hole for a witness which is then automatically filled with `15` during proof generation. However,

```haskell
6 = fresh (x % 9);
```

will produce a valid circuit, even if `x` is a free variable which will be filled with a solicited witness.

Note that fresh prevents the creation of constraints equating its generated value with the expression tree generating that value. It does not, however, remove other constraints produced inside of it. Consider the program;

```haskell
def c = { 1 = 0; 1 };

1 = fresh c;
```

This will produce an invalid proof. `fresh` does not erase constraints, but, rather, prevents new constraints from arising from expressions directly under `fresh`.

<p style="color:red;">Note: It seems to me that the restriction of exponentiation, that it cannot have variables in its exponent, should be lifted inside of fresh. Currently, it is not.</p>

`fresh` will generally not interact well with higher-order functions or tuples.

```haskell
def id x = x;
def fid = fresh id;

6 = fid 6;
```

will produce a cryptic error informing us that the application at `fid 6` failed; although it seems to have accepted making a "fresh" version of `id`. However, we can use tuples and lists inside of fresh just fine.

```haskell
(1, 6) = fresh (1, 6);
1:2:3:[] = fresh (1:2:3:[]);
```

Generally, `fresh` should only be used to generate first-order structures such as field elements and tuples or lists of first order structures. So long as the return type is such a thing, `fresh` can use any available functions and capabilities within Vamp-IR.

```haskell
7 = fresh ((fun (x, y) {x + y}) (1, 6));
```

works just fine, for example.

