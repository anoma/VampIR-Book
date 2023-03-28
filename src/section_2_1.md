# Basic Arithmetic


Vamp-IR's arithmetic denotations are fairly straightforward, being very similar to what's present in many mainstream languages.

Vamp-IR provides four different methods to denote numbers. One can use

1. standard decimal notation
2. hexadecimal notation, so long as the number is prefixed by `0x`
3. octal notation, so long as the number is prefixed by `0o`
4. binary notation, so long as the number is prefixed by `0b`

The following example program demonstrates all the number notations.

```haskell
0xee9a592ba9a9518 = 1074572035719075096;
1074572035719075096 = 0o73515131127246512430;
0o73515131127246512430 = 0b111011101001101001011001001010111010100110101001010100011000;
```

Notice that Vamp-IR allows for many top-level equations in the same program.

One can add, subtract, and multiply numbers, using, respectively, the infix operators `+`, `-`, and `*`.

```haskell
20 * 3 - 20 = 20 + 20;
```

Vamp-IR uses a standard order of operations where multiplication takes precedence over addition and subtraction, and everything is left-associated otherwise. Further clarification can be made using parentheses.

```haskell
1 + 2 + 3 - 4 * 5 - 6 + 7 =
((((1 + 2) + 3) - (4 * 5)) - 6) + 7;
```

Vamp-IR also allows for division in arithmetic circuits, denoted with the infix operator `/`.

```haskell
15/3 = 5;
```

Division has the same precedence as multiplication, and associates to the left, otherwise. 

```haskell
2/3*4/5 = ((2/3)*4)/5;
```

This operation will give an output using the division of the underlying field, meaning constraints may differ in truth value depending on the compilation target. If one uses a prime field of size

`52435875175126190479447740508185965837690552500527637822603658699938581184513`,

used, for example, in BLS12-381, then the following is valid.

```haskell
2/3 = 0x26a48d1bb889d46d66689d580335f2ac713f36abaaaa1eaa5555555500000001;
```

If one tries dividing by zero, they will get an error at some point in the process depending on where the division occurs. `1/0 = 1;` will produce an error during type inference. `1/x = 1;` will produce an error if `x` is set to 0 during witness solicitation.

Vamp-IR also possesses a single unary operation in the form of negation, denoted with `-`. Negation must be surrounded by parentheses unless it's iterated with additional negations.

```haskell
(-10) = 0 - 10;
0 - 10 = (---10);
```

Exponentiation is also available via the infix operator `^`.

```haskell
2 ^ 2 = 4;
```

Exponentiation has greater precedence than any other arithmetic operator. 

```haskell
5 * 6 ^ 7 = 5 * (6 ^ 7);
```

Only constant exponents are allowed, as they can be translated into iterated multiplication at circuit compilation time. `2 ^ x = 4;` will create an error during type inference.

Exponentiation by 0 will always produce 1, even if the base is 0.

```haskell
2 ^ 0 = 1;
0 ^ 0 = 1;
0 ^ 2 = 0;
```

<p style="color:red;">Note: this section may describe a bug instead of intended behaviour</p>

Exponentiation by negatives is a bit weird. They are offset by 1 from what one might expect.

```haskell
2 ^ (-5) = 1 / 2 ^ 4;
6 ^ (-2) = 1 / 6;
5 ^ (-3) = 1 / 5 ^ 2;
```

Exponentiating by -1 will always produce 1, with the exception of 0.

```haskell
2 ^ (-1) = 1;
6 ^ (-1) = 1;
5 ^ (-1) = 1;
0 ^ (-1) = 0;
```

There are a handful of additional arithmetic operators which may be used so long as the circuit can be transformed so they aren't referenced. These operators will be detailed in full in the section on [expanded arithmetic](section_3_2.md). For now, the modulus, `%`, will be used as an example.

```haskell
6 = 15 % 9;
```

will create a valid proof. This is because `15 % 9` can be immediately simplified into `6` during circuit generation. If one attempts to use an uninstantiated variable, that is one without a value defined in the file, they will get an error.

```haskell
6 = x % 9;
```

will generate an error during circuit creation informing us that the modulus is not a supported constraint.

