# Conversion Guide

## Keep "Real World OCaml" text untouched in any case

> I believe the main issue is still RWO’s license prohibiting modification of the text. - [**@jordwalke**](https://github.com/jordwalke)

We're using the book's [public html version](https://realworldocaml.org/v1/en/html/index.html) as text source. And the integrity of the text must be kept for the sake of any potential copyright issues, which means we do replace neither word OCaml to Reason nor any syntax differences in original text.

see [https://github.com/facebook/reason/issues/1356](https://github.com/facebook/reason/issues/1356) for more details

## Using tab to convert code sample

{% tabs %}
{% tab title="Reason" %}
```swift
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
let languages = "OCaml,Perl,C++,C";;

let dashed_languages =
  let language_list = String.split languages ~on:',' in
  String.concat ~sep:"-" language_list
;;
```
{% endtab %}
{% endtabs %}

Noticed that:

* Codes in **OCaml** tab must be copied from book and remain unchanged.
* Codes in **Reason** will demonstrate the same idea but the library using tends to vary by replacing to `Belt` or `Js` to follow the Reason community.
* An **OCaml \(de-reason\)** might be provided for special explanation needs.

## Using Paragraph rather than Quote for "Real World OCaml" text

One of the principle of this project is make the whole thing highly readable as its own, to prevent new comers from switching between book, code sample and editor all the time.

## Provide hints for any Reason variance

However, for any notably variance, a Tips using Gitbook's **hint** will to be given to elaborate the difference. For example:

{% hint style="info" %}
**Reason** is pronounced differently with OCaml, unsurprisingly.
{% endhint %}

To not confuse our readers, we should always provide hints about Reason variance before using these variances in our code snippet. **Unless the original book choose a post-explaination telling style, then we can follow**. This can help our readers noticing the difference and building the prerequisites to understand the code.

## Be careful with special char

For example, using `|` in table will break the Gitbook's markdown parser. It can be workaround by using ascii code with manual wrapping `<code>&#124;</code>`

