# Tuples

One of the few first-order data structures provided in Vamp-IR is the tuple. One can use them in place of numbers to check multiple things in parallel.

```haskell
def xs = (1, 2);

xs = (1, 2);
```

During type checking, `xs`'s type is given as

```bash
> xs: (int, int)
```

This indicates that `xs` is a pair of integers. One can also iterate tuples as much as they want.

```haskell
def xs = (1, 2, 3, 4, 5, 6, 7, 8, 9);

xs = (1, 2, 3, 4, 5, 6, 7, 8, 9);
```

During type checking, one can observe the type

```bash
> xs[2]: (int, (int, (int, (int, (int, (int, (int, (int, int))))))))
```

This indicates that tuples with more than two entries are, internally, just nested pairs associated to the right.

```haskell
(1, 2, 3) = (1, (2, 3));
```

`(1, 2, 3) = ((1, 2), 3);` will produce a type error.

One cannot compare tuples to numbers, so `1 = (1, 1);` will produce an error.

One also cannot treat tuples as numbers, so, for example, `(1, 2) + (3, 4);` will produce an error.

Vamp-IR provides the ability to define functions over tuples using pattern matching.

```haskell
def add (a, b) (x, y) = (a + x, b + y);

add (1, 2) (3, 4) = (4, 6);
```

Many standard tuple construction and manipulation functions can be defined.

```haskell
def fst (x, y) = x;
def snd (x, y) = y;
def third (x, y, z) = z;
def dup x = (x, x);
def swap (x, y) = (y, x);
def assoc (x, (y, z)) = ((x, y), z);
```

As you can see by `assoc`, one can pattern match a tuple within another tuple. Additionally, the fact that n-tuples are nested pairs means that `fst` acts uniformly on tuples; using that definition we'd have `fst (1, 2, 3) = 1`.

The types here tell us something interesting. One sees, for example, that;

```bash
> fst[4]: (([24], [25]) -> [24])
> dup[13]: ([43] -> ([43], [43]))
> swap[16]: (([50], [51]) -> ([51], [50]))
```

The numbers in square braces denote variable types. These functions do not have a fixed type, but their types can be instantiated to be different types, depending on the context. Different calls within the same program can also have different types. For example, one can have both of these equations in the same file:

```haskell
fst (1, 2) = 1;
fst ((1, 2), 3) = (1, 2);
```

Even though the same function is called on both lines, different types are returned each time. On the first line, it returns an integer while on the second it returns a pair.

\label{UNITEXP}
Vamp-IR also provides a 0-tuple denoted `()`, analogous to the return value of a unit type in many functional languages or a void type in many imperative languages. It's the closest thing to getting a function to return nothing.

```haskell
def tt = ();
def f x = {
  x = 2;
  ()
};

f 2 = tt;
```

During type inference, the following is shown;

```bash
 > tt[2]: ()
 > f[4]: (int -> ())
```

indicating that `tt` is an element of the unit type and that `f` will return an element of the unit type on an integer input. This shows that functions that have only equations do the same thing as functions that explicitly return a `()`, as mentioned in section on [functions](section_2_2.md).

Using units, one can design some of our functions to act like list manipulation programs. That is if one thinks of `(x, y)` as `Cons x y` and `()` as `Nil`, they can encode a list like `[1, 2, 3, 4]` as `(1, 2, 3, 4, ())`. The utility of this lies in the fact that each entry will appear in the head of a pair. This allows us to define more flexible programs. Modifying a few of our previous examples, one can have

```haskell
def swap (x, y, r) = (y, x, r);
swap (1, 2, ()) = (2, 1, ());
swap (1, 2, 3, ()) = (2, 1, 3, ());
swap (1, 2, 3, 4, ()) = (2, 1, 3, 4, ());

def tail (x, y) = y;
tail (1, 2, ()) = (2, ());
tail (1, 2, 3, ()) = (2, 3, ());    $
tail (1, 2, 3, 4, ()) = (2, 3, 4, ());

def total (x, y, r) = x+y;
total (1, 2, ()) = 3;
total (1, 2, 3, ()) = 3;    $
total (1, 2, 3, 4, ()) = 3;

def add (a, b, q) (x, y, r) = (a + x, b + y, ());
add (1, 2, ()) (3, 4, ()) = (4, 6, ());
add (1, 2, 3, 4, 5, ()) (6, 7, 8, ()) = (7, 9, ());

def zip (a, b, q) (x, y, r) = ((a, x), (b, y), ());
zip (1, 2, 3, 4, 5, ()) (6, 7, 8, ()) = ((1, 6), (2, 7), ());
```

Vamp-IR ultimately generates a fixed, finitely iterated data type, meaning we cannot treat iterated tuples as a proper list type. As such, many list manipulation programs that one might take for granted can't be defined in full generality using tuples. However, this technique can recover some of that functionality in practice. Proper lists are available, see the section on [lists](section_2_5.md).

Witness solicitation interacts in a somewhat interesting way with pairs. If one compiles the program

```haskell
x = (1, 2);
```

it seems like they will need to input a pair. However, during proof creation, Vamp-IR will ask;

```bash
> * Soliciting circuit witnesses...
> ** x.0[9] (private): 
> ** x.1[10] (private): 
```

It splits the variable into two sub-variables, named `x.0` (the first element of `x`) and `x.1` (the second element of `x`). So the compiled circuit does not, in fact, have a hole in the shape of a pair; rather it has two holes corresponding to the elements of the pair.

Without a variable-free first-order datatype for each uninstantiated variable, circuit generation is not possible. If one tries compiling `x = y;` without definitions for x or y, they will get an error indicating such.


