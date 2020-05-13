## Type-checking vs. type-inference

1. `E : T?` In this case, both an expression `E` and a type `T` are given. Now, is `E` really a `T`? This scenario is known as `type-checking`.
2. `E : _?` Here, only the expression is known. So, what type is `E`? If there is a way to derive a type for `E`, then we have accomplished `type inference`.
3. `_ : T?` The other way round. Given only a type, is there any expression for it or does the type have no values? Is there any example of a `T`? And in light of the `Curry–Howard isomorphism`, is there a proof for `T`?

## Dependency generator (ocamldep)

1. 我就应该从一开始就读Ocaml的参考手册，而不是去看些有的没的二手书籍

2. 熊英飞的课程真是个难得的资料

3. ```
   ocamldep [options] *.mli *.ml > .depend
   ```

## Lexer and parser generators (ocamllex, ocamlyacc)

1. ***ocamllex***: produces a lexical analyzer from a set of regular expressions with associated semantic actions
2. ***ocamlyacc***: produces a parser from a grammar with associated semantic actions

