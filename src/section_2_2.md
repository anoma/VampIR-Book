# Functions

The `def` construct allows one to define, not just constants, but functions as well.

```haskell
def square x = x ^ 2;

square 4 = 16;
```

The name of the function is denoted by the first token after `def`. Function application is denoted by juxtaposition, a la some functional languages such as Haskell.

During compilation, the type checker will identify `square`'s type as a function from integers to integers.

```bash
> square[3]: (int -> int)
```

Functions can have as many arguments as you want.

```haskell
def f x y z = x * z + y;

f 4 5 6 = 29;
```

The type of this function indicates multiple arguments.

```bash
> f[5]: (int -> (int -> (int -> int)))
```

This tells us that each argument will take in an integer and produce a function, until the third argument, after which it will produce an integer. This indicates that application is left-associative and functions are curried by default (more on this in the section on [higher-order functions](section_2_6.md)).

```haskell
((f 4) 5) 6 = f 4 5 6;
```

Definitions can contain definitions inside of them.

```haskell
def g x = {
  def k = 20;
  x * k
};

g 3 = 60;
```

Notice the usage of curly braces to define blocks that scope the function. Also, notice that the semicolon for the definition comes after the closing curly brace, and there is no semicolon for the last line before that brace. 

Internal `def` statements should be used similarly to `let` statements common in many functional languages. Internal `def` statements can be functions as well.

```haskell
def cube x = {
  def square x = x * x;
  x * square x
};

cube 3 = 27;
```

Types for internal `def` statements are not given during compilation, but they are still checked.

There is no limit to the number of lines or nestings within a definition. 

```haskell
def power x = {
  def hypercube x = {
    def square x = x * x;
    square (square x)
  };
  def cube x = {
    def square x = x * x;
    x * square x
  };
  cube (hypercube x)
 };

power 2 = 4096;
```

Overlapping names are allowed. References to names will refer to the most recent binding.

```haskell
def x = 4;
def x = 8;
x = 8;
```

During type checking, both variables are listed.

```bash
> x[2]: int
> x[3]: int
```

The numbers next to the names are, in some sense, the "true" names of the variables. Each variable has a unique number identifying it, allowing the system to avoid confusing variables with the same name.

\label{FSTUNIT}
Equations can also appear in definitions. Definitions may or may not return a value and can act as gates for other values.

```haskell
def g1 x = {x = 4; x};
g1 4;

def g2 x = {x = 10};
g2 10;
```

Looking at the types, one sees something interesting. 

```bash
> g2[5]: (int -> ())
```

`g2` does, in fact, return a value; something of type `()`. This will be explained in the section on [tuples](section_2_3.md).

This illustrates that a Vamp-IR file does not need any top-level equations, but will still represent a proposition through the equations enforced by function calls. `g1 5;` will produce an invalid proof, in this case.

\label{CALLREF}

Equations aren't enforced if their parent function is never actually called.

```haskell
def j x = {0 = 1; x};

1 = 1;
```

will produce a valid proof because `j` is never fully instantiated. Equations become part of the circuit when their parent function is fully instantiated. `def k = {0 = 1; 4};` **will** produce an invalid proof since `k` is already fully instantiated.

Using polynomial constraints, one can create sophisticated, reusable checks. The following, for example, checks that a field element is a boolean; either 0 or 1.

```haskell
def isBool x = { 
  (x - 1) * x = 0;
  x
};

isBool 0;
isBool 1;
```

\label{ARITHEX}
There are a handful of additional arithmetic operators which may be used so long as the circuit can be transformed so they aren't referenced. These operators will be detailed in full in section on [expanded arithmetic](section_3_2.md). For now, the modulus, `%`, will be used as an example.

```haskell
6 = 15 % 9;
```

will create a valid proof. This is because `15 % 9` can be immediately simplified into `6` during circuit generation. If one attempts to use an uninstantiated variable, that is one without a value defined in the file, they will get an error.

```haskell
6 = x % 9;
```

will generate an error during circuit creation informing us that the modulus is not a supported constraint.

