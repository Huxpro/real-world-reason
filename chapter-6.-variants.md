# Chapter 6. Variants

Variant types are one of the most useful features of OCaml and also one of the most unusual. They let you represent data that may take on multiple different forms, where each form is marked by an explicit tag. As we'll see, when combined with pattern matching, variants give you a powerful way of representing complex data and of organizing the case-analysis on that information.

The basic syntax of a variant type declaration is as follows:

{% tabs %}
{% tab title="Reason" %}

```rust
type <variant> =  
  | <Tag> [ (<type> [, <type>]...) ]  
  | <Tag> [ (<type> [, <type>]...) ]  
  | ...
```

{% endtab %}
{% tab title="OCaml" %}

```ocaml
type <variant> =  
  | <Tag> [ of <type> [* <type>]... ]  
  | <Tag> [ of <type> [* <type>]... ]  
  | ...
```

{% endtab %}
{% endtabs %}

Each row essentially represents a case of the variant. Each case has an associated tag and may optionally have a sequence of fields, where each field has a specified type.

Let's consider a concrete example of how variants can be useful. Almost all terminals support a set of eight basic colors, and we can represent those colors using a variant. Each color is declared as a simple tag, with pipes used to separate the different cases. Note that variant tags must be capitalized:

{% tabs %}
{% tab title="Reason" %}

```rust
# type basic_color =
   | Black | Red | Green | Yellow | Blue | Magenta | Cyan | White;
  type basic_color =
     Black
   | Red
   | Green
   | Yellow
   | Blue
   | Magenta
   | Cyan
   | White
# Cyan ;
- : basic_color = Cyan
# [Blue, Magenta, Red] ;
- : list(basic_color) = [Blue, Magenta, Red]
```

{% endtab %}
{% tab title="OCaml" %}

```ocaml
# type basic_color =
   | Black | Red | Green | Yellow | Blue | Magenta | Cyan | White ;;
  type basic_color =
     Black
   | Red
   | Green
   | Yellow
   | Blue
   | Magenta
   | Cyan
   | White
# Cyan ;;
- : basic_color = Cyan
# [Blue; Magenta; Red] ;;
- : basic_color list = [Blue; Magenta; Red]
```

{% endtab %}
{% endtabs %}

The following function uses pattern matching to convert a `basic_color` to a corresponding integer. The exhaustiveness checking on pattern matches means that the compiler will warn us if we miss a color:

{% tabs %}
{% tab title="Reason" %}

```rust
# let basic_color_to_int = fun
  | Black => 0 | Red     => 1 | Green => 2 | Yellow => 3
  | Blue  => 4 | Magenta => 5 | Cyan  => 6 | White  => 7 ;
let basic_color_to_int: basic_color => int = <fun>;
# List.map(basic_color_to_int, [Blue, Red]);
- : list(int) = [4, 1]
```

{% endtab %}
{% tab title="OCaml" %}

```ocaml
# let basic_color_to_int = function
  | Black -> 0 | Red     -> 1 | Green -> 2 | Yellow -> 3
  | Blue  -> 4 | Magenta -> 5 | Cyan  -> 6 | White  -> 7 ;;
val basic_color_to_int : basic_color -> int = <fun>
# List.map ~f:basic_color_to_int [Blue;Red];;
- : int list = [4; 1]
```

{% endtab %}
{% endtabs %}

Using the preceding function, we can generate escape codes to change the color of a given string displayed in a terminal:

{% tabs %}
{% tab title="Reason" %}

```rust
let color_by_number = (number, text) =>
  "\033[3" ++ string_of_int(number) ++ "m" ++ text ++"\033[0m";

let blue = color_by_number(basic_color_to_int(Blue), "Blue");

Js.log("Hello" ++ blue ++ "World!\n");
```

{% endtab %}
{% tab title="OCaml" %}

```ocaml
# let color_by_number number text =
    sprintf "\027[38;5;%dm%s\027[0m" number text;;
val color_by_number : int -> string -> string = <fun>
# let blue = color_by_number (basic_color_to_int Blue) "Blue";;
val blue : string = "\027[38;5;4mBlue\027[0m"
# printf "Hello %s World!\n" blue;;
Hello Blue World!
```

{% endtab %}
{% endtabs %}

On most terminals, that word "Blue" will be rendered in blue.

In this example, the cases of the variant are simple tags with no associated data. This is substantively the same as the enumerations found in languages like C and Java. But as we'll see, variants can do considerably more than represent a simple enumeration. As it happens, an enumeration isn't enough to effectively describe the full set of colors that a modern terminal can display. Many terminals, including the venerable `xterm`, support 256 different colors, broken up into the following groups:

- The eight basic colors, in regular and bold versions
- A 6 × 6 × 6 RGB color cube
- A 24-level grayscale ramp

We'll also represent this more complicated color space as a variant, but this time, the different tags will have arguments that describe the data available in each case. Note that variants can have multiple arguments, which are separated by `*`s:

```rust
type weight = Regular | Bold;

type color =
  | Basic(basic_color, weight) /* basic colors, regular and bold */
  | RGB(int, int, int)         /* 6x6x6 color cube */
  | Gray(int);                 /* 24 grayscale levels */

[RGB(250,70,70), Basic(Green, Regular)]
/* - : list(color) = [RGB(250, 70, 70), Basic(Green, Regular)] */
```

```text
# type weight = Regular | Bold
  type color =
  | Basic of basic_color * weight (* basic colors, regular and bold *)
  | RGB   of int * int * int       (* 6x6x6 color cube *)
  | Gray  of int                   (* 24 grayscale levels *)
;;type weight = Regular | Bold
type color =
    Basic of basic_color * weight
  | RGB of int * int * int
  | Gray of int
# [RGB (250,70,70); Basic (Green, Regular)];;- : color list = [RGB (250, 70, 70); Basic (Green, Regular)]
```

Once again, we'll use pattern matching to convert a color to a corresponding integer. But in this case, the pattern matching does more than separate out the different cases; it also allows us to extract the data associated with each tag:

```rust
let color_to_int = fun
  | Basic(basic_color, weight) => {
      let base = switch (weight) {
        | Bold => 8
        | Regular => 0
      };
      base + basic_color_to_int(basic_color);
    }
  | RGB(r, g, b) => 16 + b + g * 6 + r * 36
  | Gray(i) => 232 + i;
/* let color_to_int: color => int = <fun>; */
```

```text
# let color_to_int = function
    | Basic (basic_color,weight) ->
      let base = match weight with Bold -> 8 | Regular -> 0 in
      base + basic_color_to_int basic_color
    | RGB (r,g,b) -> 16 + b + g * 6 + r * 36
    | Gray i -> 232 + i ;;
val color_to_int : color -> int = <fun>
```

Now, we can print text using the full set of available colors:

```rust
let color_print = (color, s) =>
  color_by_number(color_to_int(color), s)
  |> Js.log;

color_print(Basic(Red, Bold), "A bold red!");
color_print(Gray(4), "A muted gray...");
```

```text
# let color_print color s =
     printf "%s\n" (color_by_number (color_to_int color) s);;val color_print : color -> string -> unit = <fun># color_print (Basic (Red,Bold)) "A bold red!";;A bold red!# color_print (Gray 4) "A muted gray...";;A muted gray...
```
