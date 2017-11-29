## The `match` Control Flow Operator
## Operador `match` de Controle de Fluxo

Rust has an extremely powerful control-flow operator called `match` that allows
us to compare a value against a series of patterns and then execute code based
on which pattern matches. Patterns can be made up of literal values, variable
names, wildcards, and many other things; Chapter 18 covers all the different
kinds of patterns and what they do. The power of `match` comes from the
expressiveness of the patterns and the compiler checks that make sure all
possible cases are handled.

O Rust tem um excelente operador de controle de fluxo chamado `match`, que nos
permite comparar um valor com uma série de padrões e executar um código com
base no padrão que combinar. Padrões podem ser compostos de valores literais,
variáveis, _wildcards_ e várias outras coisas. O Capítulo 18 aborda todos os
tipos de padrões e o que eles fazem. A grande utilidade do `match` vem da
expressividade dos padrões e das análises feitas pelo compilador, tendo certeza
de que todos os casos possíveis estão sendo tratados.

Think of a `match` expression kind of like a coin sorting machine: coins slide
down a track with variously sized holes along it, and each coin falls through
the first hole it encounters that it fits into. In the same way, values go
through each pattern in a `match`, and at the first pattern the value “fits,”
the value will fall into the associated code block to be used during execution.

Imagine que expressão `match` funciona como uma máquina de contar moedas: as
moedas passam por um canal que possui furos de vários tamanhos, e cada moeda
cai no primeiro furo em que ela couber. Da mesma forma, os valores passam por
cada padrão de um `match`, e logo no primeiro padrão que o valor "se encaixar",
o bloco de código que estiver associado a ele será executado.

Because we just mentioned coins, let’s use them as an example using `match`! We
can write a function that can take an unknown United States coin and, in a
similar way as the counting machine, determine which coin it is and return its
value in cents, as shown here in Listing 6-3:

Aproveitando que acabamos de falar sobre moedas, vamos usá-las como exemplo de
utilização do `match`! Podemos escrever uma função que recebe uma moeda
qualquer dos Estados Unidos e, assim como uma máquina, determina qual moeda ela
é e retorna seu valor em _cents_, como mostra a Listagem 6-3:

> **Nota do tradutor:** diferentemente do que acontece na maioria dos países,
> as moedas dos Estados Unidos possuem nomes: as de 1 _cent_ são chamadas de
> _Penny_; as de 5 _cents_, de _Nickel_; as de 10 _cents_, de _Dime_; e as de 25
> _cents_, de _Quarter_.

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

<span class="caption">Listing 6-3: An enum and a `match` expression that has
the variants of the enum as its patterns.</span>

```rust
enum Moeda {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn valor_em_cents(moeda: Moeda) -> u32 {
    match moeda {
        Moeda::Penny => 1,
        Moeda::Nickel => 5,
        Moeda::Dime => 10,
        Moeda::Quarter => 25,
    }
}
```

<span class="caption">Listagem 6-3: Uma enum e uma expressão `match` em que os
padrões comparados são as variantes da enum.</span>

Let’s break down the `match` in the `value_in_cents` function. First, we list
the `match` keyword followed by an expression, which in this case is the value
`coin`. This seems very similar to an expression used with `if`, but there’s a
big difference: with `if`, the expression needs to return a Boolean value.
Here, it can be any type. The type of `coin` in this example is the `Coin` enum
that we defined in Listing 6-3.

Vamos analisar o `match` da função `valor_em_cents`. Primeiro, usamos a
palavra-chave `match` seguida de uma expressão, que neste caso é o valor
`moeda`. É parecido a uma expressão utilizada com `if`, mas tem uma grande
diferença: com `if`, a expressão precisa retornar um valor _booleano_. Aqui,
pode ser de qualquer tipo. O tipo da variável `moeda`, neste exemplo, é a enum
`Moeda`, que definimos na Listagem 6-3.

Next are the `match` arms. An arm has two parts: a pattern and some code. The
first arm here has a pattern that is the value `Coin::Penny` and then the `=>`
operator that separates the pattern and the code to run. The code in this case
is just the value `1`. Each arm is separated from the next with a comma.

Em seguida vêm os braços do `match`. Um braço é composto por duas partes: um
padrão e algum código. O primeiro braço deste exemplo tem um padrão, que é o
valor `Moeda::Penny`, e o operador `=>` que separa o padrão do código associado
a ele. O código, nesse caso, é apenas o valor `1`. Os braços são separados uns
dos outros com uma vírgula.

When the `match` expression executes, it compares the resulting value against
the pattern of each arm, in order. If a pattern matches the value, the code
associated with that pattern is executed. If that pattern doesn’t match the
value, execution continues to the next arm, much like a coin sorting machine.
We can have as many arms as we need: in Listing 6-3, our `match` has four arms.

Quando o `match` é executado, ele compara o valor resultante com o padrão de
cada braço, na ordem. Se o valor se encaixa com um determinado padrão, o código
associado a esse padrão é executado. Se o valor não se encaixa nesse padrão, a
execução passa para o próximo braço, bem parecido com a máquina de contar
moedas. Podemos ter tantos braços quanto precisamos. No caso da Listagem 6-3,
nosso `match` tem quatro braços.

The code associated with each arm is an expression, and the resulting value of
the expression in the matching arm is the value that gets returned for the
entire `match` expression.

O código associado a cada braço é uma expressão, e o seu valor resultante, no
braço que combinar, é o que será retornado pela expressão `match`.

Curly brackets typically aren’t used if the match arm code is short, as it is
in Listing 6-3 where each arm just returns a value. If you want to run multiple
lines of code in a match arm, you can use curly brackets. For example, the
following code would print out “Lucky penny!” every time the method was called
with a `Coin::Penny` but would still return the last value of the block, `1`:

Tipicamente não se usa chaves se o braço do `match` for curto, como é o caso na
Listagem 6-3, em que cada braço retorna apenas um valor. Se você quiser
executar mais de uma linha de código em um braço, você pode usar chaves para
delimitá-las. Por exemplo, o código seguinte vai escrever na tela "Moeda da
sorte!" sempre que o método for chamado com uma `Moeda::Penny`, mas ainda vai
retornar o último valor do bloco, `1`:

```rust
# enum Coin {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter,
# }
#
fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => {
            println!("Lucky penny!");
            1
        },
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

```rust
# enum Moeda {
#    Penny,
#    Nickel,
#    Dime,
#    Quarter,
# }
#
fn valor_em_cents(moeda: Moeda) -> u32 {
    match moeda {
        Moeda::Penny => {
            println!("Moeda da sorte!");
            1
        },
        Moeda::Nickel => 5,
        Moeda::Dime => 10,
        Moeda::Quarter => 25,
    }
}
```

### Patterns that Bind to Values
### Padrões Atrelados a Valores

Another useful feature of match arms is that they can bind to parts of the
values that match the pattern. This is how we can extract values out of enum
variants.

Outra característica útil dos braços do `match` é que eles podem ser atrelados
a partes dos valores que se encaixam no padrão. É assim que podemos extrair
valores dentro de uma variante de uma enum.

As an example, let’s change one of our enum variants to hold data inside it.
From 1999 through 2008, the United States minted quarters with different
designs for each of the 50 states on one side. No other coins got state
designs, so only quarters have this extra value. We can add this information to
our `enum` by changing the `Quarter` variant to include a `UsState` value stored
inside it, which we’ve done here in Listing 6-4:

Por exemplo, vamos alterar uma das nossas variantes, inserindo dados dentro
dela. De 1999 até 2008, os Estados Unidos cunhou _quarters_ com um _design_
diferente para cada um dos 50 estados em um dos lados da moeda. Nenhuma outra
moeda tinha essa diferença no _design_, apenas os _quarters_. Podemos adicionar
essa informação à nossa `enum` alterando a variante `Quarter` para incluir o
valor `Estado`, como é feito na Listagem 6-4:

```rust
#[derive(Debug)] // So we can inspect the state in a minute
enum UsState {
    Alabama,
    Alaska,
    // ... etc
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

<span class="caption">Listing 6-4: A `Coin` enum where the `Quarter` variant
also holds a `UsState` value</span>

```rust
#[derive(Debug)] // Para podermos ver qual é o estado com mais facilidade
enum Estado {
    Alabama,
    Alaska,
    // ... etc
}

enum Moeda {
    Penny,
    Nickel,
    Dime,
    Quarter(Estado),
}
```

<span class="caption">Listagem 6-4: Enum `Moeda`, cuja variante `Quarter`
também guarda o valor `Estado`.</span>

Let’s imagine that a friend of ours is trying to collect all 50 state quarters.
While we sort our loose change by coin type, we’ll also call out the name of
the state associated with each quarter so if it’s one our friend doesn’t have,
they can add it to their collection.

Vamos imaginar que um amigo nosso está tentando colecionar todas os _quarters_
dos 50 estados. Enquanto separamos nosso troco por tipo de moeda, vamos também
dizer o nome do estado associado a cada _quarter_. Se for um dos que o nosso
amigo ainda não tem, ele pode colocá-lo na sua coleção.

In the match expression for this code, we add a variable called `state` to the
pattern that matches values of the variant `Coin::Quarter`. When a
`Coin::Quarter` matches, the `state` variable will bind to the value of that
quarter’s state. Then we can use `state` in the code for that arm, like so:

Na expressão `match` desse código, vamos adicionar uma variável chamada
`estado` ao padrão que abrange os valores da variante `Moeda::Quarter`. Quando
uma `Moeda::Quarter` é testada, a variável `estado` vai ser atrelada ao valor
do estado daquele _quarter_. Assim vamos poder usar o `estado` no código do
braço, desse jeito:

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
#
fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
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
#
fn valor_em_cents(moeda: Moeda) -> u32 {
    match moeda {
        Moeda::Penny => 1,
        Moeda::Nickel => 5,
        Moeda::Dime => 10,
        Moeda::Quarter(estado) => {
            println!("Quarter do estado {:?}!", estado);
            25
        },
    }
}
```

If we were to call `value_in_cents(Coin::Quarter(UsState::Alaska))`, `coin`
would be `Coin::Quarter(UsState::Alaska)`. When we compare that value with each
of the match arms, none of them match until we reach `Coin::Quarter(state)`. At
that point, the binding for `state` will be the value `UsState::Alaska`. We can
then use that binding in the `println!` expression, thus getting the inner
state value out of the `Coin` enum variant for `Quarter`.

Se executarmos `valor_em_cents(Moeda::Quarter(Estado::Alaska))`, `moeda` seria
`Moeda::Quarter(Estado::Alaska)`. Quando comparamos esse valor em cada um dos
braços do `match`, nenhum deles vai combinar enquanto não chegar em
`Moeda::Quarter(estado)`. Nesse ponto, `estado` vai estar atrelado ao valor
`Estado::Alaska`. Podemos, então, usar esse valor na expressão `println!`,
obtendo o estado contido na variante `Quarter` da enum `Moeda`.

### Matching with `Option<T>`
### Usando `match` com `Option<T>`

In the previous section we wanted to get the inner `T` value out of the `Some`
case when using `Option<T>`; we can also handle `Option<T>` using `match` as we
did with the `Coin` enum! Instead of comparing coins, we’ll compare the
variants of `Option<T>`, but the way that the `match` expression works remains
the same.

Na seção anterior, queríamos obter o valor `T` contido em um `Some` quando era
o caso em uma `Option<T>`. Também podemos manipular uma `Option<T> usando
`match`, assim como fizemos com a enum `Moeda`! Em vez de comparar moedas,
vamos comparar as variantes de `Option<T>`, mas a forma de trabalhar com a
expressão `match` continua a mesma.

Let’s say we want to write a function that takes an `Option<i32>`, and if
there’s a value inside, adds one to that value. If there isn’t a value inside,
the function should return the `None` value and not attempt to perform any
operations.

Digamos que queremos escrever uma função que recebe um `Option<i32>`, e se
houver um valor embutido nele, soma um a esse valor. Se não houver um valor,
a função deve retornar `None`, e nem tentar executar nenhuma operação.

This function is very easy to write, thanks to `match`, and will look like
Listing 6-5:

Essa função é bem fácil de implementar, graças ao `match`, e vai ficar conforme
visto na Listagem 6-5:

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

<span class="caption">Listing 6-5: A function that uses a `match` expression on
an `Option<i32>`</span>

```rust
fn mais_um(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}

let cinco = Some(5);
let seis = mais_um(cinco);
let nenhum = mais_um(None);
```

<span class="caption">Listagem 6-5: Uma função que usa um `match` para tratar
uma `Option<i32>`.</span>

#### Matching `Some(T)`
#### Conferindo `Some(T)`

Let’s examine the first execution of `plus_one` in more detail. When we call
`plus_one(five)`, the variable `x` in the body of `plus_one` will have the
value `Some(5)`. We then compare that against each match arm.

Vamos examinar a primeira execução de `mais_um` em mais detalhes. Quando
chamamos `mais_um(cinco)`, a variável `x` no corpo da função `mais_um` vai ter
o valor `Some(5)`. Então comparamos ele a cada braço do `match`.

```rust,ignore
None => None,
```

The `Some(5)` value doesn’t match the pattern `None`, so we continue to the
next arm.

O valor `Some(5)` não confere com o padrão `None`, então seguimos para o
próximo braço.

```rust,ignore
Some(i) => Some(i + 1),
```

Does `Some(5)` match `Some(i)`? Well yes it does! We have the same variant.
The `i` binds to the value contained in `Some`, so `i` takes the value `5`. The
code in the match arm is then executed, so we add one to the value of `i` and
create a new `Some` value with our total `6` inside.

`Some(5) confere com `Some(i)`? Sim, confere! Temos a mesma variante. O `i`
está atrelado ao valor contido em `Some`, então `i` passa a ter o valor `5`. O
código desse braço é executado, então somamos um ao valor de `i` e criamos um
novo `Some` contendo nosso total de `6`.

#### Matching `None`
#### Conferindo `None`

Now let’s consider the second call of `plus_one` in Listing 6-5 where `x` is
`None`. We enter the `match` and compare to the first arm.

Agora vamos considerar a segunda chamada da função `mais_um` na Listagem 6-5,
em que `x` é `None`. Nós entramos no `match` e comparamos com o primeiro braço.

```rust,ignore
None => None,
```

It matches! There’s no value to add to, so the program stops and returns the
`None` value on the right side of `=>`. Because the first arm matched, no other
arms are compared.

Confere! Não há nenhum valor para somar, então o programa pára e retorna o
valor `None` do lado direito do `=>`. Como o primeiro braço já combinou, nenhum
dos demais será testado.

Combining `match` and enums is useful in many situations. You’ll see this
pattern a lot in Rust code: `match` against an enum, bind a variable to the
data inside, and then execute code based on it. It’s a bit tricky at first, but
once you get used to it, you’ll wish you had it in all languages. It’s
consistently a user favorite.

A combinação de enums e a expressão `match` é útil em diversas situações. Você
verá muito esse padrão em Rust: fazer o `match` de uma enum, associar uma
variável ao valor embutido, e então executar um código baseado nele. Pode
parecer complicado no começo, mas uma vez que você se acostume, você vai querer
que tivesse isso em todas as linguagens. É, sistematicamente, um favorito dos
usuários.

### Matches Are Exhaustive
### _Matches_ São Exaustivos

There’s one other aspect of `match` we need to discuss. Consider this version
of our `plus_one` function:

Há outro aspecto do `match` que precisamos discutir. Considere essa versão da
nossa função `mais_um`:

```rust,ignore
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
    }
}
```

```rust,ignore
fn mais_um(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
    }
}
```

We didn’t handle the `None` case, so this code will cause a bug. Luckily, it’s
a bug Rust knows how to catch. If we try to compile this code, we’ll get this
error:

Nós não tratamos o caso `None`, logo vai ocorrer um _bug_ no nosso código. Por
sorte, é um _bug_ que o Rust sabe detectar. Se tentarmos compilar esse código,
vamos ter esse erro:

```text
error[E0004]: non-exhaustive patterns: `None` not covered
 -->
  |
6 |         match x {
  |               ^ pattern `None` not covered
```

Rust knows that we didn’t cover every possible case and even knows which
pattern we forgot! Matches in Rust are *exhaustive*: we must exhaust every last
possibility in order for the code to be valid. Especially in the case of
`Option<T>`, when Rust prevents us from forgetting to explicitly handle the
`None` case, it protects us from assuming that we have a value when we might
have null, thus making the billion dollar mistake discussed earlier.

O Rust sabe que nós não cobrimos todos os casos possíveis, e sabe até de qual
padrão nos esquecemos! _Matches_ em Rust são *exaustivos*: precisamos extinguir
até a última possibilidade pra que o nosso código seja válido. Especialmente no
caso de uma `Option<T>`, em que o Rust não nos deixa esquecer de tratar
explicitamente o caso `None`. Ele nos impede de assumir que temos um valor
válido quando possivelmente temos um valor nulo, e portanto, cometer o erro de
um bilhão de dólares que vimos mais cedo.

### The `_` Placeholder
### O _Placeholder_ `_`

Rust also has a pattern we can use in situations when we don’t want to list all
possible values. For example, a `u8` can have valid values of 0 through 255. If
we only care about the values 1, 3, 5, and 7, we don’t want to have to list out
0, 2, 4, 6, 8, 9 all the way up to 255. Fortunately, we don’t have to: we can
use the special pattern `_` instead:

O Rust também tem um padrão que podemos usar em situações em que não queremos
listar todos os valores possíveis. Por exemplo, um `u8` pode ter valores
válidos de 0 a 255. Se nos importamos apenas com os valores 1, 3, 5 e 7, não
queremos ser obrigados a listar o 0, 2, 4, 6, 8, 9, e assim por diante até 255.
Felizmente, nem precisamos: em vez disso, podemos usar o padrão especial `_`.

```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
```

```rust
let algum_valor_u8 = 0u8;
match algum_valor_u8 {
    1 => println!("um"),
    3 => println!("três"),
    5 => println!("cinco"),
    7 => println!("sete"),
    _ => (),
}
```

The `_` pattern will match any value. By putting it after our other arms, the
`_` will match all the possible cases that aren’t specified before it. The `()`
is just the unit value, so nothing will happen in the `_` case. As a result, we
can say that we want to do nothing for all the possible values that we don’t
list before the `_` placeholder.

O padrão `_` confere com qualquer valor. Colocando ele depois dos demais
braços, o `_` vai combinar com todos os casos possíveis que não foram
especificados antes dele. O `()` é só o valor-unidade, pra que nada aconteça no
caso `_`. Como resultado, podemos dizer que não queremos fazer nada com os
possíveis valores que não listamos antes do _placeholder_ `_`.

However, the `match` expression can be a bit wordy in a situation in which we
only care about *one* of the cases. For this situation, Rust provides `if let`.

Contudo, a expressão `match` pode ser um tanto verbosa em uma situação em que
queremos apenas lidar com *um* dos casos. Pra essa situação, o Rust oferece o
`if let`.
