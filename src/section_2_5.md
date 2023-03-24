# Lists


Vamp-IR provides built-in lists which can be manipulated in a way that's akin to lists in functional languages like Haskell or ML. The empty list is denoted `[]` while list constructors are denoted `:`. There is no syntax sugar for lists, so one must explicitly end them with `[]`.

```haskell
def exList = 1:2:3:[];

exList = 1:2:3:[];
```

As you can see, we can check multiple things in parallel, just like with tuples. The main difference is in the type. During proof generation, we should see the following;

```bash
> exList: [int]
```

The square braces indicate that `exList` is a list of `int`s. This is, of course, not to be confused with the square braces which also surround type variables. A list of variable type will have two layers of square braces. Unlike with tuples, every entry is assumed to have the same type. This allows for type-safe interoperability with `iter`.

```haskell
def hd (h:t) = h;
def tl (h:t) = t;
def nth l n = hd (iter n tl l);

nth (1:2:3:4:[]) 2 = 3;
```

As you can see, we can pattern match on lists just like we can with tuples. There is no way to case on lists, instead, it's up to the programmer to ensure all patterns are valid. If one tries `hd []`, they will get an error during circuit generation indicating that the pattern `(h : t)` cannot match `[]`.

This does not mean, however, that one must always indicate the length of the list when recursing over a list. Instead, Vamp-IR provides the `fold` functions for recursing over lists. `fold` is equivalent to right-folding functions, such as `foldr`, common to functional programming languages. It takes three arguments;

1. A base value to return on an empty list.
2. A function which incorporates an element of the list with the recursive 
3. A list to fold over.

As a basic example, we can sum over the elements of a list with

```haskell
def plus x y = x + y;
def sum l = fold 0 plus l;

sum (1:2:3:4:[]) = 10;
```

With these in place, most common list manipulation functions can be defined.

```haskell
def cons x y = x:y;
def append xs = fold xs cons;

def take_rec take (h:t) = h:(take t);
def take n = iter n take_rec (fun x {[]});

def map_rec f x acc = (f x):acc;
def map f xs = fold xs (map_rec f) [];

def zip_rec x z (y:ys) = (x, y):(z ys);
def zip xs = fold xs zip_rec (fun x {[]});

def zipWith_rec f x z (y:ys) = (f x y):(z ys);
def zipWith f xs = fold xs (zipWith_rec f) (fun x {[]});

def reverse = fold [] (fun x r { append r (x:[]) });

def last l = hd (reverse l);
```

There are some limitations that aren't present in most languages. Lists cannot be directly compared. Instead, list comparisons will be zipped into underlying field comparisons. If this isn't possible, an error will occur as a field element will try to be compared to nothing. `1:[] = 1:2:[]`, for example, won't create an invalid proof; rather, we'll get an error indicating the equality is simply invalid.

Unlike with tuples, one cannot use them for witness solicitation. `x = 1:2:3:[];` for an unbounded `x` will produce an error indicating that `x` is an undefined list.


