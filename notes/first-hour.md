# A First Hour with OCaml
[via the docs](https://ocaml.org/docs/first-hour)

## Running progs

Seems like most of this is gonna happen in the REPL. I'm gonna use UTop. So since I installed utop in the default switch, I have to specify that one before I can use `utop` or any other tool I install for that matter. I gotta figure out how to set it by default...

Remember to always end an expression with `;;`.

Something about `#use` I don't care.

## Expressions

Every expression in ocaml has a type. The expression `50 * 50;;` evaluated to `2500` with the type `int`.

We can use `let` to store values in variable like `let x = 50;;`. Now here's something I only partially understand.

```ocaml
# let a = 1 in
  let b = 2 in
    a + b;;
- : int = 3
```

Before I move on I'm going to guess. If we simplify this, to something like `let a = 1 in a + 2;;`, then the interpretation is more straightforward. "In the epxression a + 2, a is 1". So the above should be read from the inside going out.

Defining functions looks similar. Say we want a square function. That looks like 

```ocaml
# let sqr x = x * x;;
val sqr : int -> int = <fun>
```

The repl seems to know we're going to be working with ints. I wonder how. Anyway, just call the function like `square 50;;`. The function def says _"square is a function with one argument, namely x, and that the result of the function is the result of evaluating the expression x * x with the given value associated with x."_

Now lets check if a square is even

```ocaml
# let square_is_even x =
    square x mod 2 = 0;;
val square_is_even : int -> bool = <fun>
```

Okay things are getting weird. Let's make some inferences before moving on.
* **Inference one:** The equality operator in ocaml is just a single `=`. That is honestly so fucked up.
* **Inference two:** OCaml uses snake case. That's less fucked up, I guess.

The weirdness that _the docs_ wants to point out is that funcions with multiple args don't have parens. Like

```ocaml
# let ordered a b c =
    a <= b && b <= c;;
val ordered : 'a -> 'a -> 'a -> bool = <fun>
# ordered 1 1 2;;
- : bool = true
```

Granted, that is pretty weird. Okay so get this. To do floating point addition, you have to do `+.`, which is probably how the repl knew that square operated on `int`s.

```ocaml
# let average a b =
    (a +. b) /. 2.0;;
val average : float -> float -> float = <fun>
```

OCaml never does implicit casts. So you can't do `1 + 2.5` or even `1 +. 2.5`. You have to do `1. +. 2.5`. Very strict and kind of annoying. Functions will just made this so much nicer. I see you _functional programming language_. There are casting functions to get around this. To cast an integer to a float, you would use `float_of_int`. To add some integer `i` to some float `f`, you would have to do `float_of_int i +. f`. Functional.

## Recursive functions

You have to _specifically_ define a function as recursive in ocaml by using `let rec` instead of the good ol' `let`.

```ocaml
# let rec range a b =
    if a > b then []
    else a :: range (a + 1) b;;
val range : int -> int -> int list = <fun>
```

Neat! Lots of new syntax here. BTW this makes an array with all elements on the range `[a, b]`. `let rec` also has scoping implications. Okay I think I get it. If we had just used `let`, the recursive call to `range` would have looked up an _already defined_ function called `range` and not the its self.

Okay but what is thie `x :: y` syntax? If I had to guess it would mean "make an array with elements `x`, `y`. But here `y` is going to be another array with elements `[a+1, b]`.

## Types

This is definitely taking more than an hour. lol.

The basic types are
* int
* float
* bool
* char
* string

Fun that char and string are different. Always curious to see how that'll go. Strings have their own internal representation and are _not_ just lists of chars. They are also immutable.

Every expression has one and only one type. When typed into the repl, ocaml will print the type. For a function that accepts n arguments `arg1, arg1, ..., argn-1, argn` and had return type `rettype`, the compiler will print 

```ocaml
f: arg1 -> arg2 -> ... -> argn -> rettype
```

We'll get into the arrow syntax later. I think I know what it means though. Some type properties of ocaml are
* OCamle is strongly typed. Each expression has one type and is determined before a program is run.
* OCaml uses type inference to work out the types, so you don't have to. OCaml toplevel tells you the inferred types.
* OCaml doesn't do implicit type conversion. As a side effect, functions (including operators (what are those)) can't have overloaded definitions.

## Pattern Matching

Oh boy here we go. This is gonna be wild. I think this time I'm going to read then reflect instead of reflecting as I read.

Okay this is a very simple example but it's nonetheless instructive. I think. Take for example the factorial `n! = n * n-1 * n-2 * ... * 1`

```ocaml
# let rec factorial n =
    if n <= 1 then 1 else n * factorial (n - 1);;
val factorial : int -> int = <fun>
```

We can replace the `if ... else ... then` construct with pattern matching. Here are three examples that I'll reflect on after.

```ocaml
# let rec factorial n =
    match n with
    | 0 | 1 -> 1
    | x -> x * factorial (x - 1);;
val factorial : int -> int = <fun>

# let rec factorial n =
    match n with
    | 0 | 1 -> 1
    | _ -> n * factorial (n - 1);;
val factorial : int -> int = <fun>

# let rec factorial = function
    | 0 | 1 -> 1
    | n -> n * factorial (n - 1);;
val factorial : int -> int = <fun>
```
NOTES!!!!
* start with a bar `|` I guess
* the return value goes after a `->`
  * tbh I like this
* `_` is the universal pattern and matches with anything
* we can use the `function` keyword which directly introduces pattern matching so we don't have to say `match n with ...`

More to come when more complicated data structures are introduced!

## Lists

Lists are a "compound type". Sounds fancy. An ordered collection of elements of like type. Elements are separated with semicolons `;`. OCaml supports nested lists! What's the difference between a list and an array? Maybe nothing.

```ocaml
[];;
- : 'a list
[1; 2; 3];;
- : int list
[[1; 2]; [3; 4]; [5; 6]]
- : int list list
```

Each list has a head and a tail. The head is the first element and the tail is the list of the remaining elements. Lists have _two_ built-in operators. `::`, or cons operator, adds one element to the front of the list. We saw that earlier with `range`. `@`, or append operator` combines two lists.

```ocaml
# 1 :: [2; 3];;
- : int list = [1; 2; 3]
# [1] @ [2; 3];;
- : int list = [1; 2; 3]
```

We can use pattern matching to write functions that operate over lists. This is going to be pretty wild and honestly I don't have the vocabulary to describe it but I think I understand it.

```ocaml
# let rec total l =
    match l with
    | [] -> 0
    | h :: t -> h + total t;;
val total : int list -> int = <fun>
# total [1; 3; 5; 3; 1];;
- : int = 13
```

So the cases here are `[]` and `h :: t`.
* `[]` of course means an empty list. This is more trivial and it's clear how a list `l` might match it.
* `h :: t` is a little less straightforward but I think we can arrive at what's going on. Recall that for the cons operator, the left operand is an element and the right operand is a list. So this is saying "can we match `l` with something that may come from the result of running `h :: t`?" If so, give me what `h` and `t` would have been. In other words, give me the head `h` and tail `t` of the list `l`.

This, to me, is similar to list or object decomposition in JavaScript. `{fieldA, fieldB} = someObj`. Except setting it equal to `someObj` is implicit and we can then use `fieldA` and `fieldB` in some expression.

OCaml will warn us if we miss a case with pattern matching. Whoah there's no built-in length??? Also it turns out the `'a list` thing is pronounced "alpha list".

Functions with irrelevant types are called polymorphic functions. You can pass functions to other functions. Think map. A map function would be an example of a higher order function. You can also define anonymous functions with the `fun` keyword like `fun x -> x * 2`. These are good for passing into higher order functions.

Okay now this is weird. You don't need to give a function all of its values at once.

```ocaml
# let add a b = a + b;;
val add : int -> int -> int = <fun>
# add;;
- : int -> int -> int = <fun>
# let f = add 6;;
val f : int -> int = <fun>
# f 7;;
- : int = 13
```

Think back to that `in` business. This is probably similar to what's going on. We're working our way out. `add 6` is like adding a `let a = 6 in` statement. Further more, `let add a b = a * b` is like saying `a + b`. `add 6` is like saying `let b = 6 in a + b`. I mean that's probably what's going on or something like it. This can even be extended to map! This is actually so cool!!

```ocaml
# map (add 6) [1; 2; 3]
```

I wonder why we need the parens? Lets experiment.

```ocaml
# map add 6 [1; 2; 3];;
Error: This function has type ('a -> 'b) -> 'a list -> 'b list
       It is applied to too many arguments; maybe you forgot a `;'.
```

Hmm... Oh. It's interpreting this as though I'm calling `map(add, 6, [1; 2; 3])`. Okay I get it now. I mean I don't know _why_ that's how it's choosing to interpret it but I get why I need to do `(add 6)`. We can also use partial application to map over a list of lists. That is we want to map each sub-list but not the outermost list.

## Other Built-In Types

Jeez we're only like half way through this.

Two other types:
* the tuple, which is a _fixed length_ collection of elements of _any type_.
* the record, which is like a tuple but with names elements. Like a JS object or a Go struct. 

```ocaml
(* tuple *)
let t = (1, "one", '1');;

(* record *)
let person = 
  {first_name : string;
   surname : string;
   age : int};;
let frank = 
  {first_name = "Frank";
   surname = "Smith";
   age = 40;};;
```

the type for the tuple will be `val t : int * string * char = (1, "one", '1')`. The type for `person` is `type person = { first_name : string; surname : string; age : int; }` while the type for `frank` is `val frank : person = {first_name = "Frank"; surname = "Smith"; age = 40}`. Note that ocaml inferred the type of frank.

You can access the elements of a record instance (syntax????) with the dot operator.

```ocaml
# let s = frank.surname;;
val s : string = "Smith"
```

## Our Own Data Types

Okay this is getting messy. But. You can define your own types with the `type` keyword. They can be recursive meaning they can containt themselves. Each "type constructor" has to start with a capital

```ocaml
# type color = Red | Blue | Green | Yellow;;
type color = Red | Blue | Green | Yellow

# let l = [Red; Blue; Red];;
val l : color list = [Red; Blue; Red]
```

But what to each of these "constructors" represet? Just like... data? You can also do something like 

```ocaml
# type color =
   | Red
   | Blue
   | Green
   | Yellow
   | RGB of int * int * int;;
type colour = Red | Blue | Green | Yellow | RGB of int * int * int
```

Oh is it like "all these values shall be considered values of type `color`"? Sort of how you can just be like `let x = 5` and ocaml is will be like "oh yeah that's an `int`"? That's gonna be my head-canon until I learn otherwise. Here's some binary tree stuff.

```ocaml
type 'a tree =
   | Leaf
   | Node of 'a tree * 'a * 'a tree;;
type 'a tree = Leaf | Node of 'a tree * 'a * 'a tree
```

This is a polymorphic tree, so it can have whatever values inside but all nodes have to have the same value type. The two constructors are `Leaf`, which holds no data, and `Node`, which holds a left tree, a value, and a right tree.

Also hey guess what ocaml is garbage collected yay.

## Dealing With Errors

I'm gonna come back and write notes later. Just gonna read the rest for now...