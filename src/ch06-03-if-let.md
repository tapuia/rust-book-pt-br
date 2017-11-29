## Concise Control Flow with `if let`
## Controle de Fluxo Conciso com `if let`

The `if let` syntax lets you combine `if` and `let` into a less verbose way to
handle values that match one pattern and ignore the rest. Consider the program
in Listing 6-6 that matches on an `Option<u8>` value but only wants to execute
code if the value is three:

A sintaxe do `if let` permite combinar `if` e `let` em uma forma menos verbosa
de tratar apenas os valores que se encaixam em um padrão e ignorar os demais.
Considere o programa da Listagem 6-6, que confere um valor do tipo
`Option<u8>`, mas só executa um código se houver um valor associado igual a
três:

```rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```

<span class="caption">Listing 6-6: A `match` that only cares about executing
code when the value is `Some(3)`</span>

```rust
let algum_valor_u8 = Some(0u8);
match algum_valor_u8 {
    Some(3) => println!("três"),
    _ => (),
}
```

<span class="caption">Listagem 6-6: Um `match` que só executa um código quando
o valor é `Some(3)`.</span>

We want to do something with the `Some(3)` match but do nothing with any other
`Some<u8>` value or the `None` value. To satisfy the `match` expression, we
have to add `_ => ()` after processing just one variant, which is a lot of
boilerplate code to add.

Queremos fazer alguma coisa com o `Some(3)`, mas não queremos fazer nada com
nenhum outro valor, seja `Some<u8>` ou `None`. Pra satisfazer a expressão
`match`, temos que colocar `_ => ()` após processar apenas uma variante, ou
seja, é muito código para pouca coisa.

Instead, we could write this in a shorter way using `if let`. The following
code behaves the same as the `match` in Listing 6-6:

Em vez disso, poderíamos escrever o mesmo código de uma forma mais compacta,
usando `if let`. O código seguinte tem o mesmo comportamento do `match` na
Listagem 6-6:

```rust
# let some_u8_value = Some(0u8);
if let Some(3) = some_u8_value {
    println!("three");
}
```

```rust
# let algum_valor_u8 = Some(0u8);
if let Some(3) = algum_valor_u8 {
    println!("três");
}
```

`if let` takes a pattern and an expression separated by an `=`. It works the
same way as a `match`, where the expression is given to the `match` and the
pattern is its first arm.

`if let` recebe um padrão e uma expressão separados por um `=`. Isso funciona
da mesma forma que um `match`, em que a expressão seria passada para o `match`,
e o padrão apareceria no primeiro braço.

Using `if let` means you have less to type, less indentation, and less
boilerplate code. However, we’ve lost the exhaustive checking that `match`
enforces. Choosing between `match` and `if let` depends on what you’re doing in
your particular situation and if gaining conciseness is an appropriate
trade-off for losing exhaustive checking.

Usar o `if let` implica menos código pra digitar e menos indentação. Porém,
perdemos a verificação exaustiva que é garantida pelo `match`. A escolhe entre
`match` e `if let` depende do que você está fazendo em uma situação particular,
e se a redução no volume de código compensa a perda da verificação exaustiva.

In other words, you can think of `if let` as syntax sugar for a `match` that
runs code when the value matches one pattern and then ignores all other values.

Em outras palavras, você pode enxergar o `if let` como um _syntax sugar_ (um
atalho) para um `match` que só executa um código quando o valor se encaixa em
um único padrão, e ignora todos os outros valores.

We can include an `else` with an `if let`. The block of code that goes with the
`else` is the same as the block of code that would go with the `_` case in the
`match` expression that is equivalent to the `if let` and `else`. Recall the
`Coin` enum definition in Listing 6-4, where the `Quarter` variant also held a
`UsState` value. If we wanted to count all non-quarter coins we see while also
announcing the state of the quarters, we could do that with a `match`
expression like this:

Também podemos incluir um `else` em um `if let`. O bloco de código que vai no
`else` é o mesmo que iria no caso `_` da expressão `match` equivalente.
Lembre-se da enum `Moeda` que definimos na Listagem 6-4, cuja variante
`Quarter` guardava um valor do tipo `Estado`. Se queremos contar todas as
moedas que não forem _quarters_, enquanto também anunciamos o estado dos
_quarters_, poderíamos fazer isso com uma expressão `match` igual a esta:

```rust
# #[derive(Debug)]
# enum UsState {
#    Alabama,
#    Alaska,
# }
#
# enum Coin {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter(UsState),
# }
# let coin = Coin::Penny;
let mut count = 0;
match coin {
    Coin::Quarter(state) => println!("State quarter from {:?}!", state),
    _ => count += 1,
}
```

```rust
# #[derive(Debug)]
# enum Estado {
#    Alabama,
#    Alaska,
# }
#
# enum Moeda {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter(Estado),
# }
# let moeda = Coin::Penny;
let mut contagem = 0;
match moeda {
    Moeda::Quarter(estado) => println!("Quarter do estado {:?}!", estado),
    _ => contagem += 1,
}
```

Or we could use an `if let` and `else` expression like this:

Ou poderíamos usar um `if let` e um `else` desta forma:

```rust
# #[derive(Debug)]
# enum UsState {
#    Alabama,
#    Alaska,
# }
#
# enum Coin {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter(UsState),
# }
# let coin = Coin::Penny;
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

```rust
# #[derive(Debug)]
# enum Estado {
#    Alabama,
#    Alaska,
# }
#
# enum Moeda {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter(Estado),
# }
# let moeda = Moeda::Penny;
let mut contagem = 0;
if let Moeda::Quarter(estado) = moeda {
    println!("Quarter do estado {:?}!", estado);
} else {
    contagem += 1;
}
```

If you have a situation in which your program has logic that is too verbose to
express using a `match`, remember that `if let` is in your Rust toolbox as well.

Se a lógica do seu programa fica muito verbosa quando é expressa por meio de um
`match`, lembre-se que você também dispõe do `if let`.

## Summary
## Resumo

We’ve now covered how to use enums to create custom types that can be one of a
set of enumerated values. We’ve shown how the standard library’s `Option<T>`
type helps you use the type system to prevent errors. When enum values have
data inside them, you can use `match` or `if let` to extract and use those
values, depending on how many cases you need to handle.

Nós acabamos de ver como usar enums para criar tipos customizados a partir de
um conjunto de valores enumerados. Mostramos como o tipo `Option<T>`, da
biblioteca padrão, ajuda você a usar o sistema de tipos para evitar erros.
Quando as enums contêm dados, você pode usar `match` ou `if let` para extrair
e usar esses valores, dependendo de quantos casos você precisa tratar.

Your Rust programs can now express concepts in your domain using structs and
enums. Creating custom types to use in your API ensures type safety: the
compiler will make certain your functions only get values of the type each
function expects.

Agora, seus programas em Rust podem expressar conceitos em seu domínio usando
structs e enums. Criar tipos customizados para a sua _API_ aumenta sua
segurança: o compilador vai se certificar de que suas funções recebem apenas
os valores que correspondem aos tipos esperados.

In order to provide a well-organized API to your users that is straightforward
to use and only exposes exactly what your users will need, let’s now turn to
Rust’s modules.

Para fornecer uma API bem organizada aos seus usuários, que seja simples de
usar, e que exponha apenas o que é necessário aos usuários, vamos agora passar
para os módulos em Rust.
