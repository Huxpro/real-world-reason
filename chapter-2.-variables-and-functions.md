# Chapter 2. Variables and Functions

Variables and functions are fundamental ideas that show up in virtually all programming languages. OCaml has a different take on these concepts than most languages you're likely to have encountered, so this chapter will cover OCaml's approach to variables and functions in some detail, starting with the basics of how to define a variable, and ending with the intricacies of functions with labeled and optional arguments.

Don't be discouraged if you find yourself overwhelmed by some of the details, especially toward the end of the chapter. The concepts here are important, but if they don't connect for you on your first read, you should return to this chapter after you've gotten a better sense for the rest of the language.

## Variable

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
In Reason, you will use good old _block scope_ to creating bindings available only at local level. Reason also turned `in` into `;` for visual familiarity. See the [design decision](https://reasonml.github.io/docs/en/let-binding.html#design-decisions) for more details.
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





