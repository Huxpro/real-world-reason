# Read Me

This project comments on "Real World OCaml" with Reason hints, and provide Reason codes to complement the OCaml ones. 

This project is for early experimenting purpose and its final goal is upstreaming to the [RWO V2](https://github.com/realworldocaml/book). 

See [Reason\#1356](https://github.com/facebook/reason/issues/1356#issuecomment-397518319) for more details.

## Real World OCaml?

"Real World OCaml" is a well-known, highly-praised and possibly "the only one you need" book for learning OCaml. The book has a [public html version](https://realworldocaml.org/v1/en/html/index.html), so we simply refer all text from there.

## Reason?

If you haven't heard of Reason. From its own website:

> What Is Reason?
>
> Reason is not a new language; it's a new syntax and toolchain powered by the battle-tested language, OCaml. Reason gives OCaml a familiar syntax geared toward JavaScript programmers, and caters to the existing NPM/Yarn workflow folks already know.
>
> In that regard, Reason can almost be considered as a solidly statically typed, faster and simpler cousin of JavaScript, minus the historical crufts, plus the features of ES2030 you can use today, and with access to both the JS and the OCaml ecosystem!
>
> Reason compiles to JavaScript thanks to our partner project, BuckleScript, which compiles OCaml/Reason into readable JavaScript with smooth interop. Reason also compiles to fast, barebone assembly, thanks to OCaml itself.

## Then why an OCaml book in Reason?

Although Reason is very similar to JavaScript in terms of syntax, and  
"80% of OCaml's semantics \(aka how it runs\) already straightforwardly maps over to modern JavaScript and vice-versa", Reason still undoubtedly has its root and core in OCaml.

Reason will keep going on to make it more and more friendly to JavaScript developers and to grow its own community, documents, tutorials, etc., but for now, people who only has JavaScript background and/or no OCaml background might still need a serious OCaml book to really understand how Reason \(OCaml\) works and how to think in Reason \(OCaml\).

## How does this conversion works?

{% page-ref page="meta/conversion-guide.md" %}

