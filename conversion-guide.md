# Conversion Guide

## Keep "Real World OCaml" text untouched in any case

> I believe the main issue is still RWO’s license prohibiting modification of the text. - [**@jordwalke**](https://github.com/jordwalke)

We're using the book's [public html version](https://realworldocaml.org/v1/en/html/index.html) as text source. And the integrity of the text must be kept for the sake of any potential copyright issues, which means we do replace neither word OCaml to Reason nor any syntax differences in original text.

see [https://github.com/facebook/reason/issues/1356](https://github.com/facebook/reason/issues/1356) for more details

## Using tab to convert code sample

{% tabs %}
{% tab title="Reason" %}
```swift
let result = Js.(
  [|1, 2, 3, 4|]
  |> Array.filter(x => x > 2)
  |> Array.mapi((x, i) => x + i)
  |> Array.reduce((x, y) => x + y, 0)
)
```
{% endtab %}

{% tab title="OCaml" %}
```ocaml
let result = Js.(
  [|1; 2; 3; 4|]
  |> Array.filter (fun x -> x > 2)
  |> Array.mapi (fun x i -> x + i)
  |> Array.reduce (fun x y -> x + y) 0
)
```
{% endtab %}
{% endtabs %}

Noticed that the codes in **OCaml** tab are always copied from book and remain unchanged. **It is NOT the OCaml code the Reason one will convert to.**

## Using Paragraph rather than Quote for "Real World OCaml" text

One of the principle of this project is make the whole thing highly readable as its own, to prevent new comers from switching between book, code sample and editor all the time.

## Provide hints for any Reason variance

However, for any notably variance,  a Tips using Gitbook's **hint** need to be given to elaborate the difference. For example:

{% hint style="info" %}
**Reason** is pronounced differently with OCaml, obviously. 
{% endhint %}

## Reason hints before code sample

To not confuse our readers, we should always provide hints about Reason variance before using these variances in our code snippet.





