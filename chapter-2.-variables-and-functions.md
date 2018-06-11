# Chapter 2. Variables and Functions

Variables and functions are fundamental ideas that show up in virtually all programming languages. OCaml has a different take on these concepts than most languages you're likely to have encountered, so this chapter will cover OCaml's approach to variables and functions in some detail, starting with the basics of how to define a variable, and ending with the intricacies of functions with labeled and optional arguments.

Don't be discouraged if you find yourself overwhelmed by some of the details, especially toward the end of the chapter. The concepts here are important, but if they don't connect for you on your first read, you should return to this chapter after you've gotten a better sense for the rest of the language.

## VARIABLES

At its simplest, a variable is an identifier whose meaning is bound to a particular value. In OCaml these bindings are often introduced using the `let` keyword. We can type a so-called _top-level_ `let` binding with the following syntax. Note that variable names must start with a lowercase letter or an underscore: 

```ocaml
let <variable> = <expr>
```

As we'll see when we get to the module system in [Chapter 4, Files, Modules, and Programs](https://realworldocaml.org/v1/en/html/files-modules-and-programs.html), this same syntax is used for `let` bindings at the top level of a module.

Every variable binding has a _scope_, which is the portion of the code that can refer to that binding. When using **utop**, the scope of a top-level `let` binding is everything that follows it in the session. When it shows up in a module, the scope is the remainder of that module.

{% hint style="info" %}
Reason comes with a REPL called **rtop**. Unfortunately, rtop doesn't work easily with packages and externals. You are suggested to use the [**Try**](https://reasonml.github.io/en/try.html%20) for our Reason snippets when external modules are used.
{% endhint %}

Here's a simple example:

{% tabs %}
{% tab title="Reason" %}
```ocaml
# let x = 3;
let x: int = 3;
# let y = 4;
let y: int = 4
# let z = x + y;
let z: int = 7
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let x = 3;;
val x : int = 3
# let y = 4;;
val y : int = 4
# let z = x + y;;
val z : int = 7
```
{% endtab %}
{% endtabs %}

`let` can also be used to create a variable binding whose scope is limited to a particular expression, using the following syntax

{% hint style="info" %}
Reason turned `in` into `;` for visual familiarity, so you will use good old _block scope_ to creating bindings available only at local level. See the [design decision](https://reasonml.github.io/docs/en/let-binding.html#design-decisions) for more details.
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```ocaml
let <variable> = {
  <expr1>; 
  <expr2>
}
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
let <variable> = <expr1> in <expr2>
```
{% endtab %}
{% endtabs %}

This first evaluates _`expr1`_ and then evaluates _`expr2`_ with _`variable`_ bound to whatever value was produced by the evaluation of _`expr1`_. Here's how it looks in practice

{% hint style="info" %}
Reason community embrace a different set of standard libraries provided by BuckleScript, 

* `Js` which binds directly to JavaScript APIs 
* `Belt`,  a effort to provide a standard libraries crossing native and web platform. 

We will use the most closed APIs we can find  to demonstrate the same idea.
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```ocaml
let languages = "OCaml,Perl,C++,C";

let dashed_languages = {
  let language_list = Js.String.split(",", languages);
  Js.Array.joinWith("-", language_list);
};
```
{% endtab %}

{% tab title="OCaml \(de-reason\)" %}
```ocaml
let languages = "OCaml,Perl,C++,C"

let dashed_languages =
  let language_list = Js.String.split "," languages in
  Js.Array.joinWith "-" language_list
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let languages = "OCaml,Perl,C++,C";;
val languages : string = "OCaml,Perl,C++,C"
# let dashed_languages =
    let language_list = String.split languages ~on:',' in
    String.concat ~sep:"-" language_list
  ;;
val dashed_languages : string = "OCaml-Perl-C++-C"
```
{% endtab %}
{% endtabs %}

Note that the scope of `language_list` is just the expression `String.concat ~sep:"-" language_list` and is not available at the toplevel, as we can see if we try to access it now:

{% hint style="info" %}
expression`Js.Array.joinWith("-", language_list);` in Reason sample.
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```swift
Js.log(language_list);
/* The value language_list can't be found */
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# language_list;;Characters -1-13:
Error: Unbound value language_list
```
{% endtab %}
{% endtabs %}

A `let` binding in an inner scope can _shadow_, or hide, the definition from an outer scope. So, for example, we could have written the `dashed_languages` example as follows:

{% tabs %}
{% tab title="Reason" %}
```swift
let languages = "OCaml,Perl,C++,C";

let dashed_languages = {
  let languages = Js.String.split(",", languages);
  Js.Array.joinWith("-", languages);
};
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let languages = "OCaml,Perl,C++,C";;
val languages : string = "OCaml,Perl,C++,C"
# let dashed_languages =
     let languages = String.split languages ~on:',' in
     String.concat ~sep:"-" languages
  ;;
val dashed_languages : string = "OCaml-Perl-C++-C"
```
{% endtab %}
{% endtabs %}

This time, in the inner scope we called the list of strings `languages` instead of `language_list`, thus hiding the original definition of `languages`. But once the definition of `dashed_languages` is complete, the inner scope has closed and the original definition of languages reappears:

{% tabs %}
{% tab title="Reason" %}
```swift
Js.log(languages);  /* "OCaml,Perl,C++,C" */
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# languages;;
- : string = "OCaml,Perl,C++,C"
```
{% endtab %}
{% endtabs %}

One common idiom is to use a series of nested `let`/`in` expressions to build up the components of a larger computation. Thus, we might write:

{% hint style="info" %}
In Reason, we will simply use a anonymous block scope, which looks like a function body.
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```swift
# let area_of_ring = (inner_radius, outer_radius) => {
    let pi = acos(-1.);
    let area_of_circle = r => pi *. r *. r;
    area_of_circle(outer_radius) -. area_of_circle(inner_radius);
  };
let area_of_ring: (float, float) => float = <fun>;
# area_of_ring(1., 3.);
- : float = 25.1327412287183449
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let area_of_ring inner_radius outer_radius =
     let pi = acos (-1.) in
     let area_of_circle r = pi *. r *. r in
     area_of_circle outer_radius -. area_of_circle inner_radius
  ;;
val area_of_ring : float -> float -> float = <fun>
# area_of_ring 1. 3.;;
- : float = 25.1327412287
```
{% endtab %}
{% endtabs %}

It's important not to confuse a sequence of `let` bindings with the modification of a mutable variable. For example, consider how `area_of_ring` would work if we had instead written this purposefully confusing bit of code:

{% tabs %}
{% tab title="Reason" %}
```swift
# let area_of_ring = (inner_radius, outer_radius) => {
     let pi = acos(-1.);
     let area_of_circle = (r) => pi *. r *. r;
     let pi = 0.;
     area_of_circle(outer_radius) -. area_of_circle(inner_radius);
  };

Characters 125-127:
Warning 26: unused variable pi.
let area_of_ring: (float, float) => float = <fun>;
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let area_of_ring inner_radius outer_radius =
     let pi = acos (-1.) in
     let area_of_circle r = pi *. r *. r in
     let pi = 0. in
     area_of_circle outer_radius -. area_of_circle inner_radius
  ;;

Characters 126-128:
Warning 26: unused variable pi.
val area_of_ring : float -> float -> float = <fun>
```
{% endtab %}
{% endtabs %}

Here, we redefined `pi` to be zero after the definition of `area_of_circle`. You might think that this would mean that the result of the computation would now be zero, but in fact, the behavior of the function is unchanged. That's because the original definition of `pi` wasn't changed; it was just shadowed, which means that any subsequent reference to `pi` would see the new definition of `pi`as `0`, but earlier references would be unchanged. But there is no later use of `pi`, so the binding of `pi` to `0.` made no difference. This explains the warning produced by the toplevel telling us that there is an unused definition of `pi`.

{% hint style="warning" %}
Noticed that if `pi` is mutable, and `pi` is captured within `area_of_circle`'s closure by reference. \(e.g. in JavaScript\), we will got a `0`.

```javascript
var area_of_ring = (inner_radius, outer_radius) => {
  var pi = Math.PI;
  var area_of_circle = (r) => pi * r * r;
  var pi = 0;
  return area_of_circle(outer_radius) - area_of_circle(inner_radius);
};
area_of_ring(1, 3); //0
```

Thankfully, if you are using ES6 `let`  rather than `var`, a `syntaxError` would be thrown because `let` bindings can not be re-declared in same lexical scope.
{% endhint %}

In OCaml, `let` bindings are immutable. There are many kinds of mutable values in OCaml, which we'll discuss in [Chapter 8, _Imperative Programming_](file:////Users/jsx/Google%20Drive/NOTES.d/reason/RealWorldReason/imperative-programming-1.html), but there are no mutable variables.

{% hint style="success" %}
### Why Don't Variables Vary?

One source of confusion for people new to OCaml is the fact that variables are immutable. This seems pretty surprising even on linguistic terms. Isn't the whole point of a variable that it can vary?

The answer to this is that variables in OCaml \(and generally in functional languages\) are really more like variables in an equation than a variable in an imperative language. If you think about the mathematical identity `x(y + z) = xy + xz`, there's no notion of mutating the variables `x`, `y`, and `z`. They vary in the sense that you can instantiate this equation with different numbers for those variables, and it still holds.

The same is true in a functional language. A function can be applied to different inputs, and thus its variables will take on different values, even without mutation.
{% endhint %}

### Pattern Matching and let

Another useful feature of `let` bindings is that they support the use of _patterns_ on the lefthand side. Consider the following code, which uses `List.unzip`, a function for converting a list of pairs into a pair of lists:

{% hint style="info" %}
BuckleScript compiles Reason to JavaScript. Under the hood, it represents Reason List \(singly linked list\) as a nested array cell.
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```swift
open Belt;
let (ints, strings) = List.unzip([(1, "one"), (2, "two"), (3, "three")]);

/* imagining this works in rtop */
let ints: list(int) = [1, 2, 3];
let strings: list(string) = ["one", "two", "three"];

/* in try where code compiled to JS */
Js.log(ints);     /* [1,[2,[3,0]]] */
Js.log(strings);  /* ["one",["two",["three",0]]] */
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let (ints,strings) = List.unzip [(1,"one"); (2,"two"); (3,"three")];;
val ints : int list = [1; 2; 3]
val strings : string list = ["one"; "two"; "three"]
```
{% endtab %}
{% endtabs %}

Here, `(ints,strings)` is a pattern, and the `let` binding assigns values to both of the identifiers that show up in that pattern. A pattern is essentially a description of the shape of a data structure, where some components are identifiers to be bound. As we saw in [the section called “Tuples, Lists, Options, and Pattern Matching”](https://realworldocaml.org/v1/en/html/a-guided-tour.html#tuples-lists-options-and-pattern-matching), OCaml has patterns for a variety of different data types.

Using a pattern in a `let` binding makes the most sense for a pattern that is _irrefutable_, _i.e._, where any value of the type in question is guaranteed to match the pattern. Tuple and record patterns are irrefutable, but list patterns are not. Consider the following code that implements a function for upper casing the first element of a comma-separated list:

{% hint style="info" %}
`Js` apis operates on array but we are demonstrating pattern matching against list here. So we have to convert between array and list back and forth. \(We'll provide a better sample in future\)
{% endhint %}

{% tabs %}
{% tab title="Reason" %}
```rust
let upcase_first_entry = line => {
  let [first, ...rest] = Array.to_list(Js.String.split(",", line));
  Js.Array.joinWith(
    ",",
    Array.of_list([Js.String.toUpperCase(first), ...rest]),
  );
};

Warning 8: this pattern-matching is not exhaustive.
Here is an example of a value that is not matched:
[]
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let upcase_first_entry line =
     let (first :: rest) = String.split ~on:',' line in
     String.concat ~sep:"," (String.uppercase first :: rest)
  ;;

Characters 40-53:
Warning 8: this pattern-matching is not exhaustive.
Here is an example of a value that is not matched:
[]
val upcase_first_entry : string -> string = <fun>
```
{% endtab %}
{% endtabs %}

This case can't really come up in practice, because `String.split` always returns a list with at least one element. But the compiler doesn't know this, and so it emits the warning. It's generally better to use a `match` statement to handle such cases explicitly:

{% tabs %}
{% tab title="First Tab" %}
```rust
let upcase_first_entry = line =>
  switch (Array.to_list(Js.String.split(",", line))) {
  | [] => assert false /* String.split returns at least one element */
  | [first, ...rest] =>
    Js.Array.joinWith(
      ",",
      Array.of_list([Js.String.toUpperCase(first), ...rest]),
    )
  };
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# let upcase_first_entry line =
     match String.split ~on:',' line with
     | [] -> assert false (* String.split returns at least one element *)
     | first :: rest -> String.concat ~sep:"," (String.uppercase first :: rest)
  ;;val upcase_first_entry : string -> string = <fun>
```
{% endtab %}
{% endtabs %}

Note that this is our first use of `assert`, which is useful for marking cases that should be impossible. We'll discuss `assert` in more detail in [Chapter 7, Error Handling](https://realworldocaml.org/v1/en/html/error-handling.html).

## FUNCTIONS

Given that OCaml is a functional language, it's no surprise that functions are important and pervasive. Indeed, functions have come up in almost every example we've done so far. This section will go into more depth, explaining the details of how OCaml's functions work. As you'll see, functions in OCaml differ in a variety of ways from what you'll find in most mainstream languages.

### Anonymous Functions

We'll start by looking at the most basic style of function declaration in OCaml: the _anonymous function_. An anonymous function is a function that is declared without being named. These can be declared using the `fun` keyword, as shown here:

{% tabs %}
{% tab title="Reason" %}
```rust
# (x) => x + 1;
- : int => int = <fun>
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
# (fun x -> x + 1);;
- : int -> int = <fun>
```
{% endtab %}
{% endtabs %}

Anonymous functions operate in much the same way as named functions. For example, we can apply an anonymous function to an argument:

```text
# (fun x -> x + 1) 7;;
- : int = 8
```

Or pass it to another function. Passing functions to iteration functions like `List.map` is probably the most common use case for anonymous functions:

```text
# List.map ~f:(fun x -> x + 1) [1;2;3];;
- : int list = [2; 3; 4]
```

You can even stuff them into a data structure:

```text
# let increments = [ (fun x -> x + 1); (fun x -> x + 2) ] ;;
val increments : (int -> int) list = [<fun>; <fun>]
# List.map ~f:(fun g -> g 5) increments;;
- : int list = [6; 7]
```

It's worth stopping for a moment to puzzle this example out, since this kind of higher-order use of functions can be a bit obscure at first. Notice that `(fun g -> g 5)` is a function that takes a function as an argument, and then applies that function to the number `5`. The invocation of `List.map` applies `(fun g -> g 5)` to the elements of the `increments` list \(which are themselves functions\) and returns the list containing the results of these function applications.

The key thing to understand is that functions are ordinary values in OCaml, and you can do everything with them that you'd do with an ordinary value, including passing them to and returning them from other functions and storing them in data structures. We even name functions in the same way that we name other values, by using a `let` binding:

```text
# let plusone = (fun x -> x + 1);;
val plusone : int -> int = <fun>
# plusone 3;;
- : int = 4
```

Defining named functions is so common that there is some syntactic sugar for it. Thus, the following definition of `plusone` is equivalent to the previous definition:

```text
# let plusone x = x + 1;;
val plusone : int -> int = <fun>
```

This is the most common and convenient way to declare a function, but syntactic niceties aside, the two styles of function definition are equivalent.

{% hint style="success" %}
### let and fun

Functions and `let` bindings have a lot to do with each other. In some sense, you can think of the parameter of a function as a variable being bound to the value passed by the caller. Indeed, the following two expressions are nearly equivalent:

```text
# (fun x -> x + 1) 7;;
- : int = 8
# let x = 7 in x + 1;;
- : int = 8
```

This connection is important, and will come up more when programming in a monadic style, as we'll see in [Chapter 18, Concurrent Programming with Async](https://realworldocaml.org/v1/en/html/concurrent-programming-with-async.html).
{% endhint %}



