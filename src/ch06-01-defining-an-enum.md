## Defining an Enum
## Definindo uma Enum

Let’s look at a situation we might want to express in code and see why enums
are useful and more appropriate than structs in this case. Say we need to work
with IP addresses. Currently, two major standards are used for IP addresses:
version four and version six. These are the only possibilities for an IP
address that our program will come across: we can *enumerate* all possible
values, which is where enumeration gets its name.

Vamos ver um caso em que enums podem ser mais apropriadas do que structs e
descobrir como elas podem ser úteis. Digamos que estamos trabalhando com
endereços IP. Atualmente, existem duas versões do protocolo IP que são mais
utilizadas: a quatro e a seis. Estas são as únicas possibilidades para um
endereço IP com que o nosso programa vai trabalhar: nós podemos *enumerar*
todos os possíveis valores, é daí que vem o nome enumeração.

Any IP address can be either a version four or a version six address but not
both at the same time. That property of IP addresses makes the enum data
structure appropriate for this case, because enum values can only be one of the
variants. Both version four and version six addresses are still fundamentally
IP addresses, so they should be treated as the same type when the code is
handling situations that apply to any kind of IP address.

Um endereço IP qualquer pode ser ou da versão quatro ou da versão seis, mas
nunca das duas ao mesmo tempo. Esta propriedade dos endereços IP faz com que a
enum seja bem apropriada para este caso, pois enums só podem assumir o valor de
uma de suas variantes. Os endereços de ambas as versões, seja quatro ou seis,
ainda são, fundamentalmente, endereços IP, e deveriam ser tratados pelo mesmo
tipo no código em situações que se aplicam a qualquer versão de endereço IP.

We can express this concept in code by defining an `IpAddrKind` enumeration and
listing the possible kinds an IP address can be, `V4` and `V6`. These are known
as the *variants* of the enum:

Podemos expressar esse conceito em código definindo uma enum `VersaoIp` e
listando os possíveis tipos de que um endereço IP pode ser: `V4` e `V6`. Estas
são as chamadas *variantes* da enum:

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

```rust
enum VersaoIp {
    V4,
    V6,
}
```

`IpAddrKind` is now a custom data type that we can use elsewhere in our code.

`VersaoIp` é um tipo de dados que agora nós podemos usar em qualquer lugar no
nosso código.

### Enum Values
### Valores de uma Enum

We can create instances of each of the two variants of `IpAddrKind` like this:

Podemos criar instâncias de cada uma das duas variantes de `VersaoIp`, da
seguinte forma:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

```rust
# enum VersaoIp {
#     V4,
#     V6,
# }
#
let quatro = VersaoIp::V4;
let seis = VersaoIp::V6;
```

Note that the variants of the enum are namespaced under its identifier, and we
use a double colon to separate the two. The reason this is useful is that now
both values `IpAddrKind::V4` and `IpAddrKind::V6` are of the same type:
`IpAddrKind`. We can then, for instance, define a function that takes any
`IpAddrKind`:

Repare que as variantes pertencem ao _namespace_ da enum, e se usa `::` para
separar os dois. Isso é útil porque agora ambos os valores `VersaoIp::V4` e
`VersaoIp::V6` são do mesmo tipo: `VersaoIp`. Agora nós podemos, por exemplo,
definir uma função que usa qualquer `VersaoIp`.

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
fn route(ip_type: IpAddrKind) { }
```

```rust
# enum VersaoIp {
#     V4,
#     V6,
# }
#
fn rotear(versao_ip: VersaoIp) { }
```

And we can call this function with either variant:

E podemos ainda chamar esta função passando qualquer uma das variantes:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn route(ip_type: IpAddrKind) { }
#
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

```rust
# enum VersaoIp {
#     V4,
#     V6,
# }
#
# fn rotear(versao_ip: VersaoIp) { }
#
rotear(VersaoIp::V4);
rotear(VersaoIp::V6);
```

Using enums has even more advantages. Thinking more about our IP address type,
at the moment we don’t have a way to store the actual IP address *data*; we
only know what *kind* it is. Given that you just learned about structs in
Chapter 5, you might tackle this problem as shown in Listing 6-1:

O uso de enums tem ainda mais vantagens. Pensando mais a fundo sobre o nosso
tipo de endereço IP, ainda não temos uma forma de representar o *endereço* em
si, apenas sabemos qual a *versão* dele. Tendo em vista o que você acabou de
aprender sobre structs no Capítulo 5, você poderia abordar esse problema assim
como visto na Listagem 6-1:

```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

<span class="caption">Listing 6-1: Storing the data and `IpAddrKind` variant of
an IP address using a `struct`</span>

```rust
enum VersaoIp {
    V4,
    V6,
}

struct EnderecoIp {
    versao: VersaoIp,
    endereco: String,
}

let local = EnderecoIp {
    versao: VersaoIp::V4,
    endereco: String::from("127.0.0.1"),
};

let loopback = EnderecoIp {
    versao: VersaoIp::V6,
    endereco: String::from("::1"),
};
```

<span class="caption">Listagem 6-1: Representação do endereço e da variante
`VersaoIp` de um endereço IP usando uma `struct`</span>

Here, we’ve defined a struct `IpAddr` that has two fields: a `kind` field that
is of type `IpAddrKind` (the enum we defined previously) and an `address` field
of type `String`. We have two instances of this struct. The first, `home`, has
the value `IpAddrKind::V4` as its `kind` with associated address data of
`127.0.0.1`. The second instance, `loopback`, has the other variant of
`IpAddrKind` as its `kind` value, `V6`, and has address `::1` associated with
it. We’ve used a struct to bundle the `kind` and `address` values together, so
now the variant is associated with the value.

Aqui nós definimos uma struct `EnderecoIp` que tem dois membros: `versao`, do
tipo `VersaoIp` (que definimos anteriormente) e `endereco`, do tipo `String`.
Temos duas instâncias dessa struct. A primeira, `local`, tem o valor
`VersaoIp::V4` como sua `versao`, e um endereço associado igual a `127.0.0.1`.
A segunda instância, `loopback`, tem como sua `versao` a outra variante de
`VersaoIp`, `V6`, e o endereço `::1` associado a ela. Nós usamos uma struct
para encapsular os valores de `versao` e `endereco`, agora a variante está
associada ao valor.

We can represent the same concept in a more concise way using just an enum,
rather than an enum inside a struct, by putting data directly into each enum
variant. This new definition of the `IpAddr` enum says that both `V4` and `V6`
variants will have associated `String` values:

Podemos representar o mesmo conceito de uma forma mais concisa usando apenas
uma enum, em vez de uma enum dentro de uma struct, colocando dados dentro de
cada variante da enum, diretamente. Esta nova definição da enum `EnderecoIp`
diz que ambas as variantes, `V4` e `V6`, terão uma `String` associada:

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));
```

```rust
enum EnderecoIp {
    V4(String),
    V6(String),
}

let local = EnderecoIp::V4(String::from("127.0.0.1"));

let loopback = EnderecoIp::V6(String::from("::1"));
```

We attach data to each variant of the enum directly, so there is no need for an
extra struct.

Podemos anexar dados a cada variante da enum diretamente, assim não existe mais
a necessidade de uma struct adicional.

There’s another advantage to using an enum rather than a struct: each variant
can have different types and amounts of associated data. Version four type IP
addresses will always have four numeric components that will have values
between 0 and 255. If we wanted to store `V4` addresses as four `u8` values but
still express `V6` addresses as one `String` value, we wouldn’t be able to with
a struct. Enums handle this case with ease:

Há uma outra vantagem de se usar uma enum em vez de uma struct: cada variante
pode conter dados de diferentes tipos e quantidades. Os endereços IP da versão
quatro têm sempre quatro componentes numéricas, cada uma com valor de 0 a 255.
Se quiséssemos representar endereços `V4` como quatro valores `u8`, e ao mesmo
tempo manter os endereços `V6` como uma `String`, não poderíamos usar uma
struct. Já as enums podem facilmente atender a este caso:

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

```rust
enum EnderecoIp {
    V4(u8, u8, u8, u8),
    V6(String),
}

let local = EnderecoIp::V4(127, 0, 0, 1);

let loopback = EnderecoIp::V6(String::from("::1"));
```

We’ve shown several different possibilities that we could define in our code
for storing IP addresses of the two different varieties using an enum. However,
as it turns out, wanting to store IP addresses and encode which kind they are
is so common that [the standard library has a definition we can
use!][IpAddr]<!-- ignore --> Let’s look at how the standard library defines
`IpAddr`: it has the exact enum and variants that we’ve defined and used, but
it embeds the address data inside the variants in the form of two different
structs, which are defined differently for each variant:

[IpAddr]: ../../std/net/enum.IpAddr.html

Acabamos de ver algumas possibilidades que poderíamos usar para representar
endereços IP das duas versões por meio de uma enum. Acontece que essa
necessidade de representar endereços IP, incluindo sua versão, é tão comum que
a biblioteca padrão já possui uma definição que podemos usar! ([Veja a
documentação em inglês][IpAddr]<!-- ignore -->). Vamos ver como a biblioteca
padrão define `IpAddr`: ele tem basicamente a mesma enum e as mesmas variantes
que nós definimos e usamos anteriormente, mas os dados do endereço são
embutidos dentro das variantes na forma de duas structs separadas, que são
definidas de um jeito diferente pra cada variante.

[IpAddr]: https://doc.rust-lang.org/std/net/enum.IpAddr.html

```rust
struct Ipv4Addr {
    // details elided
}

struct Ipv6Addr {
    // details elided
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

```rust
struct Ipv4Addr {
    // detalhes omitidos
}

struct Ipv6Addr {
    // detalhes omitidos
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

This code illustrates that you can put any kind of data inside an enum variant:
strings, numeric types, or structs, for example. You can even include another
enum! Also, standard library types are often not much more complicated than
what you might come up with.

Esse código mostra que você pode colocar qualquer tipo de dados dentro de uma
variante de enum: strings, tipos numéricos ou structs, por exemplo. Você pode
até mesmo incluir outra enum! Além disso, os tipos definidos pela biblioteca
padrão não são tão mais complicados do que o que talvez você pensaria em fazer.

Note that even though the standard library contains a definition for `IpAddr`,
we can still create and use our own definition without conflict because we
haven’t brought the standard library’s definition into our scope. We’ll talk
more about bringing types into scope in Chapter 7.

Repare que, mesmo havendo um `IpAddr`definido pela biblioteca padrão, nós ainda
podemos criar e utilizar nossa própria definição (com o mesmo nome, inclusive)
sem nenhum conflito, porque não trouxemos a definição da biblioteca padrão para
dentro do nosso escopo. Falaremos mais sobre a inclusão de tipos em um escopo
no Capítulo 7.

Let’s look at another example of an enum in Listing 6-2: this one has a wide
variety of types embedded in its variants:

Vamos ver outro exemplo de uma enum na Listagem 6-2: esta tem uma grande
variedade de tipos embutidos nas suas variantes:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

<span class="caption">Listing 6-2: A `Message` enum whose variants each store
different amounts and types of values</span>

```rust
enum Mensagem {
    Sair,
    Mover { x: i32, y: i32 },
    Escrever(String),
    MudarCor(i32, i32, i32),
}
```

<span class="caption">Listagem 6-2: Enum `Mensagem`, cujas variantes contêm,
cada uma, diferentes tipos e quantidades de dados</span>

This enum has four variants with different types:

Esta enum tem quatro variantes de diferentes tipos:

* `Quit` has no data associated with it at all.
* `Move` includes an anonymous struct inside it.
* `Write` includes a single `String`.
* `ChangeColor` includes three `i32` values.

* `Sair` não tem nenhum dado associado.
* `Mover` contém uma struct anônima.
* `Escrever` contém uma única `String`.
* `MudarCor` contém três valores do tipo `i32`.

Defining an enum with variants like the ones in Listing 6-2 is similar to
defining different kinds of struct definitions except the enum doesn’t use the
`struct` keyword and all the variants are grouped together under the `Message`
type. The following structs could hold the same data that the preceding enum
variants hold:

Definir uma enum com variantes iguais às da Listagem 6-2 é similar a definir
diferentes tipos de struct, exceto que a enum não usa a palavra-chave `struct`,
e todas as variantes são agrupadas dentro do tipo `Mensagem`. As structs
seguintes podem guardar os mesmos dados que as variantes da enum anterior:

```rust
struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
```

```rust
struct MensagemSair; // unit struct
struct MensagemMover {
    x: i32,
    y: i32,
}
struct MensagemEscrever(String); // tuple struct
struct MensagemMudarCor(i32, i32, i32); // tuple struct
```

But if we used the different structs, which each have their own type, we
wouldn’t be able to as easily define a function that could take any of these
kinds of messages as we could with the `Message` enum defined in Listing 6-2,
which is a single type.

Mas se usarmos structs diferentes, cada uma tendo seu próprio tipo, não vamos
conseguir tão facilmente definir uma função que possa receber qualquer um
desses tipos de mensagens, assim como fizemos com a enum `Mensagem`, definida
na Listagem 6-2, que consiste em um tipo único.

There is one more similarity between enums and structs: just as we’re able to
define methods on structs using `impl`, we’re also able to define methods on
enums. Here’s a method named `call` that we could define on our `Message` enum:

Há mais uma similaridade entre enums e structs: da mesma forma como podemos
definir métodos em structs usando `impl`, também podemos definir métodos em
enums. Aqui está um método chamado `invocar`, que poderia ser definido na nossa
enum `Mensagem`:

```rust
# enum Message {
#     Quit,
#     Move { x: i32, y: i32 },
#     Write(String),
#     ChangeColor(i32, i32, i32),
# }
#
impl Message {
    fn call(&self) {
        // method body would be defined here
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

```rust
# enum Mensagem {
#     Sair,
#     Mover { x: i32, y: i32 },
#     Escrever(String),
#     MudarCor(i32, i32, i32),
# }
#
impl Mensagem {
    fn invocar(&self) {
        // o corpo do método é definido aqui
    }
}

let m = Mensagem::Escrever(String::from("olá"));
m.invocar();
```

The body of the method would use `self` to get the value that we called the
method on. In this example, we’ve created a variable `m` that has the value
`Message::Write(String::from("hello"))`, and that is what `self` will be in the body of the
`call` method when `m.call()` runs.

O corpo do método usaria o valor `self` para obter a mensagem sobre a qual o
método foi chamado. Neste exemplo, criamos a variável `m`, que contém o valor
`Mensagem::Escrever(String::from("olá"))`, e é isso que `self` vai ser no corpo
do método `invocar` quando `m.invocar()` for executado.

Let’s look at another enum in the standard library that is very common and
useful: `Option`.

Vamos ver agora outra enum da biblioteca padrão que também é muito útil e
comum: `Option`.

### The `Option` Enum and Its Advantages Over Null Values
### A Enum `Option` e Suas Vantagens Sobre Valores Nulos

In the previous section, we looked at how the `IpAddr` enum let us use Rust’s
type system to encode more information than just the data into our program.
This section explores a case study of `Option`, which is another enum defined
by the standard library. The `Option` type is used in many places because it
encodes the very common scenario in which a value could be something or it
could be nothing. Expressing this concept in terms of the type system means the
compiler can check that you’ve handled all the cases you should be handling,
which can prevent bugs that are extremely common in other programming languages.

Na seção anterior, vimos como a enum `EnderecoIp` nos permite usar o sistema de
tipos do Rust para codificar em nosso programa mais informação do que apenas os
dados que queremos representar. Essa seção explora um caso de estudo da
`Option`, que é outra enum definida pela biblioteca padrão. O tipo `Option` é
muito utilizado, pois engloba um cenário muito comum, em que um valor pode ser
algo ou pode não ser nada. Expressar esse conceito por meio do sistema de tipos
significa que o compilador pode verificar se você tratou, ou não, todos os
casos que deveriam ser tratados, podendo evitar _bugs_ que são extremamente
comuns em outras linguagens de programação.

Programming language design is often thought of in terms of which features you
include, but the features you exclude are important too. Rust doesn’t have the
null feature that many other languages have. *Null* is a value that means there
is no value there. In languages with null, variables can always be in one of
two states: null or not-null.

O _design_ de uma linguagem de programação é geralmente tratado em termos de
quais características são incluídas, mas as que são excluídas também têm 
importância. Rust não tem o valor nulo (_null_) que outras linguagens têm. O
valor nulo quer dizer que não há nenhum valor. Em linguagens que têm essa
característica, as variáveis sempre estão em um dos dois estados: nulo ou não
nulo.

In “Null References: The Billion Dollar Mistake,” Tony Hoare, the inventor of
null, has this to say:

Em uma conferência, Tony Hoare, inventor do valor nulo, disse o seguinte:

> I call it my billion-dollar mistake. At that time, I was designing the first
> comprehensive type system for references in an object-oriented language. My
> goal was to ensure that all use of references should be absolutely safe, with
> checking performed automatically by the compiler. But I couldn’t resist the
> temptation to put in a null reference, simply because it was so easy to
> implement. This has led to innumerable errors, vulnerabilities, and system
> crashes, which have probably caused a billion dollars of pain and damage in
> the last forty years.

> Eu o chamo meu erro de um bilhão de dólares. Naquela época, eu estava
> projetando o primeiro sistema abrangente de tipos para referências em uma
> linguagem orientada a objetos. Meu objetivo era garantir que todo uso de
> referências deveria ser absolutamente seguro, com verificação automática
> feita pelo compilador. Mas não pude resistir à tentação de colocar uma
> referência nula, simplesmente porque era tão fácil de implementar. Isso tem
> provocado inúmeros erros, vulnerabilidades, e falhas de sistemas que
> provavelmente causaram um bilhão de dólares de dor e danos nos últimos
> quarenta anos.

The problem with null values is that if you try to actually use a value that’s
null as if it is a not-null value, you’ll get an error of some kind. Because
this null or not-null property is pervasive, it’s extremely easy to make this
kind of error.

O problema com valores nulos é que, se você tentar usar um valor nulo como se
fosse não nulo, vai acontecer algum tipo de erro. Pelo fato dessa propriedade
de nulo e não nulo ser tão sutil, é extremamente fácil cometer esse tipo de
erro.

However, the concept that null is trying to express is still a useful one: a
null is a value that is currently invalid or absent for some reason.

Porém, o conceito que o valor nulo tenta expressar ainda é útil: um valor nulo
representa algo que, por algum motivo, está inválido ou ausente no momento.

The problem isn’t with the actual concept but with the particular
implementation. As such, Rust does not have nulls, but it does have an enum
that can encode the concept of a value being present or absent. This enum is
`Option<T>`, and it is [defined by the standard library][option]<!-- ignore -->
as follows:

[option]: ../../std/option/enum.Option.html

O problema, na verdade, não está no conceito, mas na implementação em
particular. Por isso, Rust não possui valores nulos, mas sim uma enum que
engloba o conceito de um valor estar presente ou ausente. Esta enum é a
`Option<T>`, que está definida na biblioteca padrão da seguinte forma:
([Veja a documentação em inglês][option]<!-- ignore -->).

[option]: https://doc.rust-lang.org/std/option/enum.Option.html

```rust
enum Option<T> {
    Some(T),
    None,
}
```

```rust
enum Option<T> {
    Some(T), // algum valor
    None, // nenhum valor
}
```

The `Option<T>` enum is so useful that it’s even included in the prelude; you
don’t need to bring it into scope explicitly. In addition, so are its variants:
you can use `Some` and `None` directly without prefixing them with `Option::`.
`Option<T>` is still just a regular enum, and `Some(T)` and `None` are still
variants of type `Option<T>`.

A enum `Option<T>` é tão útil que ela já vem inclusa no prelúdio: você não
precisa trazê-la explicitamente para o seu escopo. Além disso, o mesmo ocorre
com suas variantes: você pode usar `Some` e `None` diretamente sem prefixá-las
com `Option::`. `Option<T>` continua sendo uma enum como qualquer outra, e
`Some(T)` e `None` ainda são variantes do tipo `Option<T>`.

The `<T>` syntax is a feature of Rust we haven’t talked about yet. It’s a
generic type parameter, and we’ll cover generics in more detail in Chapter 10.
For now, all you need to know is that `<T>` means the `Some` variant of the
`Option` enum can hold one piece of data of any type. Here are some examples of
using `Option` values to hold number types and string types:

A sintaxe do `<T>` é uma característica do Rust de que não falamos ainda.
Trata-se de um parâmetro de tipo genérico, vamos abordá-lo com mais detalhe no
Capítulo 10. Por ora, tudo que você precisa saber é que `<T>` significa que a
variante `Some` da enum `Option` pode conter um dado de qualquer tipo. Aqui vão
alguns exemplos de `Option` contendo tipos de número e texto:

```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

```rust
let algum_numero = Some(5);
let algum_texto = Some("um texto");

let numero_ausente: Option<i32> = None;
```

If we use `None` rather than `Some`, we need to tell Rust what type of
`Option<T>` we have, because the compiler can’t infer the type that the `Some`
variant will hold by looking only at a `None` value.

Se usamos `None` em vez de `Some`, precisamos dizer ao Rust qual é o tipo de
`Option<T>` que nós temos, porque o compilador não consegue inferir qual tipo
estará contido na variante `Some` apenas olhando para um valor `None`.

When we have a `Some` value, we know that a value is present, and the value is
held within the `Some`. When we have a `None` value, in some sense, it means
the same thing as null: we don’t have a valid value. So why is having
`Option<T>` any better than having null?

Quando temos um `Some`, sabemos que um valor está presente, contido dentro do
`Some`. Já quando temos um `None`, de certa forma, significa o mesmo que um
valor nulo: não temos um valor que seja válido. Então por que a `Option<T>` é
tão melhor que usar um valor nulo?

In short, because `Option<T>` and `T` (where `T` can be any type) are different
types, the compiler won’t let us use an `Option<T>` value as if it was
definitely a valid value. For example, this code won’t compile because it’s
trying to add an `i8` to an `Option<i8>`:

Em resumo, é porque `Option<T>` e `T` (podendo `T` ser de qualquer tipo) são
tipos diferentes, por isso, o compilador não vai permitir usar um valor do tipo
`Option<T>` como se ele definitivamente tivesse um valor válido. Por exemplo,
o código seguinte não vai compilar, porque ele está tentando somar um `i8` a um
`Option<i8>`:

```rust,ignore
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
```

```rust,ignore
let x: i8 = 5;
let y: Option<i8> = Some(5);

let soma = x + y;
```

If we run this code, we get an error message like this:

Quando executamos esse código, temos uma mensagem de erro como essa:

```text
error[E0277]: the trait bound `i8: std::ops::Add<std::option::Option<i8>>` is
not satisfied
 -->
  |
5 |     let sum = x + y;
  |                 ^ no implementation for `i8 + std::option::Option<i8>`
  |
```

Intense! In effect, this error message means that Rust doesn’t understand how
to add an `i8` and an `Option<i8>`, because they’re different types. When we
have a value of a type like `i8` in Rust, the compiler will ensure that we
always have a valid value. We can proceed confidently without having to check
for null before using that value. Only when we have an `Option<i8>` (or
whatever type of value we’re working with) do we have to worry about possibly
not having a value, and the compiler will make sure we handle that case before
using the value.

Intenso! O que essa mensagem quer dizer é que o Rust não consegue entender como
somar um `i8` e um `Option<i8>`, porque eles são de tipos diferentes. Quando
temos um valor de um tipo como `i8` em Rust, o compilador tem certeza de que
temos sempre um valor válido. Podemos prosseguir com confiança, sem ter de
verificar se o valor é nulo antes de usá-lo. Somente quando temos um
`Option<i8>` (ou qualquer que seja o tipo com que estamos trabalhando), vamos
ter de nos preocupar com a possibilidade de não haver um valor, e o compilador
vai se certificar de que nós estamos tratando este caso antes de usar o valor.

In other words, you have to convert an `Option<T>` to a `T` before you can
perform `T` operations with it. Generally, this helps catch one of the most
common issues with null: assuming that something isn’t null when it actually
is.

Em outras palavras, você tem que converter um `Option<T>` em um `T` antes de
poder executar operações com ele. Geralmente, isso ajuda a detectar um dos
problemas mais comuns com valores nulos: assumir que algo não é nulo quando,
na verdade, ele é.

Not having to worry about missing an assumption of having a not-null value
helps you to be more confident in your code. In order to have a value that can
possibly be null, you must explicitly opt in by making the type of that value
`Option<T>`. Then, when you use that value, you are required to explicitly
handle the case when the value is null. Everywhere that a value has a type that
isn’t an `Option<T>`, you *can* safely assume that the value isn’t null. This
was a deliberate design decision for Rust to limit null’s pervasiveness and
increase the safety of Rust code.

Só de não ter que se preocupar com a possibilidade de ter deixado um valor nulo
escapar já lhe dá mais confiança em seu código. Pra ter um valor que pode ser
nulo em algum momento, você precisa, explicitamente, marcá-lo como sendo do
tipo `Option<T>`. A partir daí, sempre que for usar o valor, você será obrigado
a tratar, de forma explícita, o caso do valor sendo nulo. Sempre que houver um
valor que não seja um `Option<T>`, você *pode* assumir, com segurança, que o
valor não é nulo. Esta foi uma decisão deliberada de projeto do Rust para
limitar as sutilezas dos valores nulos e aumentar a segurança do código.

So, how do you get the `T` value out of a `Some` variant when you have a value
of type `Option<T>` so you can use that value? The `Option<T>` enum has a large
number of methods that are useful in a variety of situations; you can check
them out in [its documentation][docs]<!-- ignore -->. Becoming familiar with
the methods on `Option<T>` will be extremely useful in your journey with Rust.

[docs]: ../../std/option/enum.Option.html

Então, como obter o valor `T` da variante `Some` quando se tem um `Option<T>`,
para que se possa usar seu valor? A enum `Option<T>` possui diversos métodos
que são úteis em uma variedade de situações, você pode pesquisá-los na
[documentação][docs]<!-- ignore --> (em inglês). Será extremamente útil na sua
jornada com Rust se familizarizar com os métodos da enum `Option<T>`.

[docs]: https://doc.rust-lang.org/std/option/enum.Option.html

In general, in order to use an `Option<T>` value, we want to have code that
will handle each variant. We want some code that will run only when we have a
`Some(T)` value, and this code is allowed to use the inner `T`. We want some
other code to run if we have a `None` value, and that code doesn’t have a `T`
value available. The `match` expression is a control flow construct that does
just this when used with enums: it will run different code depending on which
variant of the enum it has, and that code can use the data inside the matching
value.

Em geral, pra usar um valor `Option<T>`, queremos ter um código que trate cada
uma das variantes. Queremos um código que só será executado quando tivermos um
valor `Some(T)`, e esse código terá permissão para usar o valor `T` que está
embutido. Queremos também um outro código que seja executado se tivermos um
valor `None`, e esse código não terá um valor `T` disponível. A expressão
`match` é uma instrução de controle de fluxo que faz exatamente isso quando
usada com enums: ela executa códigos diferentes dependendo de qual variante
tiver a enum, e esse código poderá usar os dados contidos na variante
encontrada.
