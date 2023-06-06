# Intrinsics
<a name="HOF"></a>

Vamp-IR comes with a small set of built-in functions. Each can be treated in isolation as we would any other function. The current set of functions are used in the following file;

```haskell
def myFresh = fresh;
def myIter = iter;
def myFold = fold;
```

`fresh` will be described in the next section. Compiling this file gives the following output;

```bash
[...]
** Inferring types...
myFresh[9]: ([18] -> [18])
myIter[10]: (int -> (([19] -> [19]) -> ([19] -> [19])))
myFold[11]: ([[20]] -> (([20] -> ([21] -> [21])) -> ([21] -> [21])))
[...]
```

demonstrating the types of these functions in isolation. We can treat them on their own the same as we would treat any user defined function; they do not have special syntax rules associated to them.