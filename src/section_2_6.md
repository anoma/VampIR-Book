# Higher-Order Functions
<a name="HOF"></a>

Vamp-IR has rather robust support for higher-order functions. One can manipulate functions more or less like any other value. One can save a partially instantiated function.

```haskell
def f x y = x * y;
def g = f 2;

g 3 = 6;
```

Notice that `g` is defined in terms of `f` with only one of its two arguments applied. The types are given as

```bash
> f[4]: (int -> (int -> int))
> g[5]: (int -> int)
```

one can see that `g`'s type is exactly the return type of `f`. This is no different than if `f` returned an integer instead of a function.

One can also write functions that take a function as input.

```haskell
def app2 f x = f (f x);
def times2 x = 2 * x;

app2 times2 3 = 12;
```

Looking at the types;

```bash
> f[4]: app2[4]: (([10] -> [10]) -> ([10] -> [10]))
```

one can see that `app2` takes a function from the (variable) type `[10]` onto itself, and creates a new function, also from `[10]` onto itself. At this point, it may be clear that `iter`, and, in fact, `fold` as well, are examples of higher-order functions.

As mentioned in section on [functions](section_2_2.md), the constraints of a function are not called unless the function is fully instantiated. The following program produces a valid proof.

```haskell
def f x y = {0 = 1; x};

f 2;
```

While `f` is called, it's not fully instantiated, and so `0 = 1` does not become part of the generated circuit.

There are many higher-order combinators that may be useful for some applications.

```haskell
def comp f g x = f (g x);
def const x y = x;
def flip f x y = f y x;
def delta f x = f x x;
```

Vamp-IR provides a notation for anonymous functions using the `fun` keyword.

```haskell
(fun x {x}) 15 = 15;
(fun x y {x + y}) 15 20 = 35;
(fun (x, y) {x + y}) (15, 3) = 18;
flip (fun x y {x - y}) 15 3 = (-12);
```

Notice that one can bind multiple arguments and pattern match on tuples using anonymous functions. These can also be used as return values in definitions.

```haskell
def id = fun x {x};

id 2 = 2;
```

one can also place constraints inside anonymous functions.

```haskell
(fun x {x = 2; x}) 2;
(fun x {x = 5}) 5 = ();
```

as you can see, anonymous functions don't need to return data. Like `def`s, `fun`s that don't return a value still return a `()`.

We cannot use functions in comparisons; only first-order data can be compared. 

```haskell
(fun x { 2 }) = (fun x { 1 + 1 });
```

will produce an error.

One can also create functions to curry and uncurry our functions

```haskell
def curry f x y = f (x, y);
def uncurry f (x, y) = f x y;

curry (fun (x, y) {x + y}) 3 4 = uncurry (fun x y {x + y}) (3, 4);
```

Vamp-IR does not give unrestricted access to higher-order functions; any appearing in a program must be simply typable. For example, `fun x { x x };` will produce a typing error. This means that, in particular, one cannot define a fixed-point combinator, which would be necessary to implement general recursion. Still, there are many powerful techniques made available by what is given. In particular, one can define and utilize lambda-encoded data.

If Vamp-IR didn't already have built-in pairs, one could implement them as

```haskell
def pair x y p = p x y;
```

By issuing different arguments to `pair x y`, one can get the underlying components.

```haskell
def fst p = p (fun x y {x});
fst (pair 1 2) = 1;

def snd p = p (fun x y {y});
snd (pair 1 2) = 2;
```

While one cannot compare these pairs using `=` directly, one can define a bespoke comparison function.

```haskell
def eq p q = {
  fst p = fst q; 
  snd p = snd q
};

eq (pair 1 2) (pair 1 2);
```

Further structural manipulation functions can easily be defined.

```haskell
def swap p = p (fun x y {pair y x});
eq (swap (pair 1 2)) (pair 2 1);

def assoc p = 
  fun f {p (fun x yz {yz (fun y z {
      f (pair x y) z
  })})};
eq (fst (assoc (pair 1 (pair 2 3)))) (pair 1 2);
snd (assoc (pair 1 (pair 2 3))) = 3;
```

Functions converting between the two formats can also easily be defined;

```haskell
def lpair (x, y) = pair x y;
def upair p = p (fun x y {(x, y)});
eq (lpair (1, 2)) (pair 1 2);
(1, 2) = upair (pair 1 2);
```

This is all interesting but doesn't give us anything that wasn't already provided. However, a variant of this idea can encode more datatypes. For example, we can encode maybe/option types;

```haskell
def nothing n j = n;
def just x n j = j x;

def maybe d m = m d (fun x {x});
```

We can also encode coproducts, allowing us to emulate branching programs, at least during circuit generation.

```haskell
def inl x l r = l x;
def inr x l r = r x;

def case f g c = c f g;

case (fun x {x + 1}) (fun x {x + 2}) (inl 0) = 1;
case (fun x {x + 1}) (fun x {x + 2}) (inr 0) = 2;
```

If they didn't already exist, we could implement lists as well through this method.

```haskell
def nil n c = n;
def cons x xs n c = c x (xs n c);

def ex_list = cons 1 (cons 2 (cons 3 nil));
// Same as
def ex_list2 n c = c 1 (c 2 (c 3 n));
```

One can also issue arguments into the list, whose structure explicitly represents folding a function over the list.

```haskell
ex_list 0 (fun x y {x + y}) = 6;
```

One can define many well-founded recursive functions this way.

```haskell
def append l1 l2 = fun n c { l1 (l2 n c) c };

def map f l n c = l n (fun x xs { c (f x) xs });
```

We can easily convert between built-in lists and encoded ones.

```haskell
llist l = fold nil cons l;
ulist l = l [] (fun x y {x:y});

ulist (llist (1:2:3:4:[])) = 1:2:3:4:[];
```

This method is limited by the simple type system of Vamp-IR. While taking the head and tail of these kinds of lists is possible without first converting to a normal list, iterating such a function cannot type check. This prevents us, for example, from defining analogs of `nth` in a type-safe manner. Nonetheless, this technique is useful for cases where we only need to fold over a tree-like structure to produce first-order data. Any kind of well-founded tree can be encoded and folded over via similar methods. For example, binary trees can be encoded using;

```haskell
def leaf lf br = lf;
def branch l r lf br = br (l lf br) (r lf br);
```

This opens the door to many powerful functional programming techniques, including free monads, for example.

It should be noted that there are some fundamental limitations to this method. Mainly, it can't interact non-trivially with the underlying field. One cannot, for example, translate a field element into a Church-encoded number or a boolean. While one can certainly implement those in Vamp-IR (in fact, `app2` from earlier is a Church numeral, and `const` is a Church boolean), they cannot be used to actually reason about field elements. The methods described in this section are exclusively useful for data **structure** manipulation, but not data manipulation in general. This is because data structures are fixed at circuit generation time, but field values are not completely fixed until proof generation, which occurs after circuit generation. This implies that the circuit's structure cannot depend on field values.
