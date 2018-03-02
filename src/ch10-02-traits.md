## Traits: Defining Shared Behavior

## Traits: Definindo Comportamento Compartilhado

Traits allow us to use another kind of abstraction: they let us abstract over
behavior that types can have in common. A *trait* tells the Rust compiler about
functionality a particular type has and might share with other types. In
situations where we use generic type parameters, we can use *trait bounds* to
specify, at compile time, that the generic type may be any type that implements
a trait and therefore has the behavior we want to use in that situation.

Traits nos permitem usar outro tipo de abstração: eles nos permitem abstrair 
sobre o comportamento que tipos têm em comum. Um *trait* diz ao compilador de
Rust sobre uma funcionalidade que um tipo particular possui e pode compartilhar
com outros tipos. Em situações onde nós usamos parâmetros de tipos genéricos,
nós podemos usar *limites de trait* para especificar, em tempo de compilação,
que o tipo genérico pode ser qualquer tipo que implementa um trait e por
conseguinte tem o comportamento que queremos usar nessa situação.

> Note: *Traits* are similar to a feature often called ‘interfaces’ in other
> languages, though with some differences.

> Nota: *Traits* são similares a um recurso frequentemente chamado de 
> 'interface' em outras linguagens, com algumas diferenças.

### Defining a Trait

### Definindo um Trait

The behavior of a type consists of the methods we can call on that type.
Different types share the same behavior if we can call the same methods on all
of those types. Trait definitions are a way to group method signatures together
in order to define a set of behaviors necessary to accomplish some purpose.

O comportamento de um tipo consiste nos métodos que podemos chamar para aquele
tipo. Tipos diferentes dividem o mesmo comportamento se podemos chamar os 
mesmos métodos em todos esses tipos. Definições de traits são um modo de 
agrupar métodos de assinaturas juntos a fim de definir um conjunto de 
comportamentos para atingir algum propósito.

For example, say we have multiple structs that hold various kinds and amounts
of text: a `NewsArticle` struct that holds a news story filed in a particular
place in the world, and a `Tweet` that can have at most 140 characters in its
content along with metadata like whether it was a retweet or a reply to another
tweet.

Por exemplo, digamos que temos múltiplos structs que contém vários tipos e
quantidades de texto: um struct `ArtigoDeNoticias`que contém uma notícia 
preenchida em um lugar do mundo, e um `Tweet` que pode ter no máximo 140
caracteres em seu conteúdo além dos metadados como se ele foi um retweet ou uma
resposta a outro tweet.

We want to make a media aggregator library that can display summaries of data
that might be stored in a `NewsArticle` or `Tweet` instance. The behavior we
need each struct to have is that it’s able to be summarized, and that we can
ask for that summary by calling a `summary` method on an instance. Listing
10-12 shows the definition of a `Summarizable` trait that expresses this
concept:

Nós queremos fazer uma biblioteca agregadora de mídia que pode mostrar resumos
de dados que podem estar guardados em uma instância de `ArtigoDeNoticia` ou 
`Tweet`. O comportamento que precisamos cada struct possua é que seja capaz de
ser resumido, e que nós possamos pedir pelo resumo chamando um método `resumo`
em uma instância. A Listagem 10-12 mostra a definição de um trait `Resumir` que
expressa esse conceito:

<span class="filename">Filename: lib.rs</span>

<span class="filename">Nome do arquivo: lib.rs</span>

```rust
pub trait Summarizable {
    fn summary(&self) -> String;
}
```

```rust
pub trait Resumir {
    fn resumo(&self) -> String;
}
```

<span class="caption">Listing 10-12: Definition of a `Summarizable` trait that
consists of the behavior provided by a `summary` method</span>

<span class="caption">Listagem 10-12: Definição de um trait `Resumir` que 
consiste no comportamento fornecido pelo método `resumo`</span>

We declare a trait with the `trait` keyword, then the trait’s name, in this
case `Summarizable`. Inside curly brackets we declare the method signatures
that describe the behaviors that types that implement this trait will need to
have, in this case `fn summary(&self) -> String`. After the method signature,
instead of providing an implementation within curly brackets, we put a
semicolon. Each type that implements this trait must then provide its own
custom behavior for the body of the method, but the compiler will enforce that
any type that has the `Summarizable` trait will have the method `summary`
defined for it with this signature exactly.

Nós declaramos um trait com a palavra-chave `trait`, e então o nome do trait,
nesse caso `Resumir`. Dentro de chaves declaramos a assinatura do método que
descreve o comportamento que tipos que implementam esse trait precisarão ter,
nesse caso `fn resumo(&self) -> String;`. Depois da assinatura do método ao
invés de fornecer uma implementação dentro de chaves, nós colocamos um ponto e
vírgula. Cada tipo que implementa esse trait precisa então fornecer seu próprio
comportamento customizado para o corpo do método, mas o compilador vai reforçar
que qualquer qualquer tipo que tenha o trait `Resumir` terá o método `resumo`
definido para ele com esse exata assinatura.

A trait can have multiple methods in its body, with the method signatures
listed one per line and each line ending in a semicolon.

Um trait pode ter vários métodos no seu corpo, com os métodos das assinaturas
listados um por linha e cada linha terminando com um ponto e vírgula.

### Implementing a Trait on a Type

### Implementando um Trait em um Tipo

Now that we’ve defined the `Summarizable` trait, we can implement it on the
types in our media aggregator that we want to have this behavior. Listing 10-13
shows an implementation of the `Summarizable` trait on the `NewsArticle` struct
that uses the headline, the author, and the location to create the return value
of `summary`. For the `Tweet` struct, we’ve chosen to define `summary` as the
username followed by the whole text of the tweet, assuming that tweet content
is already limited to 140 characters.

Agora que deifnimos o trait `Resumir`, podemos implementa-lo nos tipos do nosso
agregador de mídias que queremos que tenham esse comportamento. A Listagem 
10-13 mostra uma implementação do trait `Resumir` no struct `ArtigoNotícia` que
possui o título, o autor e a localização para criar e retornar o valor de 
`resumo`. Para o struct `Tweet`, nós escolhemos definir `resumo` como o nome de
usuário seguido por todo o texto do tweet, assumindo que o conteúdo do tweet já
está limitado a 140 caracteres.

<span class="filename">Filename: lib.rs</span>

<span class="filename">Nome do arquivo: lib.rs</span>

```rust
# pub trait Summarizable {
#     fn summary(&self) -> String;
# }
#
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summarizable for NewsArticle {
    fn summary(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summarizable for Tweet {
    fn summary(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

```rust
# pub trait Resumir {
#     fn summary(&self) -> String;
# }
#
pub struct ArtigoDeNoticia {
    pub titulo: String,
    pub local: String,
    pub autor: String,
    pub conteudo: String,
}

impl Resumir for ArtigoDeNoticia {
    fn summary(&self) -> String {
        format!("{}, by {} ({})", self.titulo, self.autor, self.local)
    }
}

pub struct Tweet {
    pub nomeusuario: String,
    pub conteudo: String,
    pub resposta: bool,
    pub retweet: bool,
}

impl Resumir for Tweet {
    fn summary(&self) -> String {
        format!("{}: {}", self.nomeusuario, self.conteudo)
    }
}
```

<span class="caption">Listing 10-13: Implementing the `Summarizable` trait on
the `NewsArticle` and `Tweet` types</span>

<span class="caption">Listagem 10-13: Implementando o trait `Resumir` nos tipos 
`ArtigoDeNoticia` e `Tweet`</span>

Implementing a trait on a type is similar to implementing methods that aren’t
related to a trait. The difference is after `impl`, we put the trait name that
we want to implement, then say `for` and the name of the type that we want to
implement the trait for. Within the `impl` block, we put the method signatures
that the trait definition has defined, but instead of putting a semicolon after
each signature, we put curly brackets and fill in the method body with the
specific behavior that we want the methods of the trait to have for the
particular type.

Implementar um trait em um tipo é similar a implementar métodos que não estão
relacionados com um trait. A diferença está depois de `impl`, nós colocamos o
nome do trait que queremos implementar, então dizemos `for` e o nome do tipo 
que queremos implementar. Dentro do bloco `impl`, nós colocamos as assinaturas
dos métodos que a definição do trait definiu, mas ao invés de colocar um ponto
e vírgula depois de cada assinatura, nós colocamos chaves e preenchemos o corpo
do método com o comportamento específico que queremos que os métodos dos traits
tenham para um tipo particular.

Once we’ve implemented the trait, we can call the methods on instances of
`NewsArticle` and `Tweet` in the same manner that we call methods that aren’t
part of a trait:

Uma vez que implementamos o trait, nós podemos chamar os métodos nas instâncias
de `ArtigoDeNoticia` e `Tweet` da mesma maneira que nós chamamos métodos que não
são parte de um trait:

```rust,ignore
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summary());
```

```rust,ignore
let tweet = Tweet {
    nomeUsuario: String::from("horse_ebooks"),
    conteudo: String::from("claro, como vocês provavelmente já sabem, 
    pessoas"),
    resposta: false,
    retweet: false,
};

println!("1 novo tweet: {}", tweet.summary());
```

This will print `1 new tweet: horse_ebooks: of course, as you probably already
know, people`.

Isso irá imprimir `1 novo tweet: claro, como vocês provavelmente já sabem,
pessoas`

Note that because we’ve defined the `Summarizable` trait and the `NewsArticle`
and `Tweet` types all in the same `lib.rs` in Listing 10-13, they’re all in the
same scope. If this `lib.rs` is for a crate we’ve called `aggregator`, and
someone else wants to use our crate’s functionality plus implement the
`Summarizable` trait on their `WeatherForecast` struct, their code would need
to import the `Summarizable` trait into their scope first before they could
implement it, like in Listing 10-14:

Note que porque nós definimos o trait `Resumir` e os tipos `ArtigoDeNoticia` e
`Tweet` todos na mesma `lib.rs` na listagem 10-13, eles estão todos no mesmo 
escopo. Se essa `lib.rs` é para um crate nós chamamos `agregador`, e se outra
pessoa quiser usar a funcionalidade do nosso crate e implementar o trait 
`Resumir` na sua struct `PrevisaoTempo`, o código deles precisaria importar o
trait `Resumir` no escopo deles primeiro antes deles poderem implementá-lo, 
como na Listagem 10-14:

<span class="filename">Filename: lib.rs</span>

<span class="filename">Nome do arquivo: lib.rs</span>

```rust,ignore
extern crate aggregator;

use aggregator::Summarizable;

struct WeatherForecast {
    high_temp: f64,
    low_temp: f64,
    chance_of_precipitation: f64,
}

impl Summarizable for WeatherForecast {
    fn summary(&self) -> String {
        format!("The high will be {}, and the low will be {}. The chance of
        precipitation is {}%.", self.high_temp, self.low_temp,
        self.chance_of_precipitation)
    }
}
```

```rust,ignore
extern crate aggregator;

use aggregator::Resumir;

struct PrevisaoTempo {
    alta_temp: f64,
    baixa_temp: f64,
    chance_de_chuva: f64,
}

impl Resumir for PrevisaoTempo {
    fn resumo(&self) -> String {
        format!("A alta será de {}, e a baixa de {}. A chance de precipitação é
        {}%.", self.alta_temp, self.baixa_temp, self.chance_de_chuva)
    }
}
```

<span class="caption">Listing 10-14: Bringing the `Summarizable` trait from our
`aggregator` crate into scope in another crate</span>

<span class="caption">Listagem 10-14: Trazendo o trait `Resumir` do nosso crate 
`aggregator` para o escopo de outro crate</span>

This code also assumes `Summarizable` is a public trait, which it is because we
put the `pub` keyword before `trait` in Listing 10-12.

Esse código também assume que `Resumir` é um trait público, o que é verdade 
porque colocamos a palavra-chave `pub` antes de `trait` na Listagem 10-12.

One restriction to note with trait implementations: we may implement a trait on
a type as long as either the trait or the type are local to our crate. In other
words, we aren’t allowed to implement external traits on external types. We
can’t implement the `Display` trait on `Vec`, for example, since both `Display`
and `Vec` are defined in the standard library. We are allowed to implement
standard library traits like `Display` on a custom type like `Tweet` as part of
our `aggregator` crate functionality. We could also implement `Summarizable` on
`Vec` in our `aggregator` crate, since we’ve defined `Summarizable` there. This
restriction is part of what’s called the *orphan rule*, which you can look up
if you’re interested in type theory. Briefly, it’s called the orphan rule
because the parent type is not present. Without this rule, two crates could
implement the same trait for the same type, and the two implementations would
conflict: Rust wouldn’t know which implementation to use. Because Rust enforces
the orphan rule, other people’s code can’t break your code and vice versa.

Uma restrição para se prestar atenção na implementação de traits: nós podemos
implementar um trait em um tipo desde que o trait ou o tipo forem locais para o
nosso crate. Em outras palavras, nós não estamos autorizados a implementar 
traits externos em tipos externos. Nós não podemos implementar o trait 
`Display` em `Vec`, por exemplo, já que ambos `Display` e `Vec` são definidos na
biblioteca padrão. Nós temos a permissão de implementar traits da biblioteca
padrão como `Display` em um tipo personalizado como `Tweet` como parte da
funcionalidade do nosso crate `aggregator`, já que nós já havíamos definido
`Resumir` lá. Essa restrição é parte do que é chamado de a *regra do ṍrfão*,
qual você pode procurar se estiver interessado nesse tipo de teoria. De forma
curta, é chamada de a regra do órfão porque o tipo pai não está presente. Sem
essa regra, dois crates poderiam implementar o mesmo trait para o mesmo tipo,
e as duas implementações entrariam em conflito: o Rust não saberia qual
implementação usar. Porque o Rust impõe a regra do órfão, os códigos de outras 
pessoas não podem quebrar seu código e vice e versa.

### Default Implementations

### Implementações Padrão

Sometimes it’s useful to have default behavior for some or all of the methods
in a trait, instead of making every implementation on every type define custom
behavior. When we implement the trait on a particular type, we can choose to
keep or override each method’s default behavior.

As vezes é útil ter um comportamento padrão pra alguns ou todos os métodos em
um trait, ao invés de fazer toda implementação em todo tipo e definir um 
comportamento personalizado. Quando implementamos o trait em um tipo 
particular, nós podemos escolher manter ou sobrescrever o comportamento padrão
de cada método.

Listing 10-15 shows how we could have chosen to specify a default string for
the `summary` method of the `Summarize` trait instead of choosing to only
define the method signature like we did in Listing 10-12:

A Listagem 10-15 mostra como poderíamos ter escolhido especificar uma string 
padrão para o método `resumo` do trait `Resumir` ao invés de escolher de apenas
definir a assinatura do método como fizemos na Listagem 10-12:

<span class="filename">Filename: lib.rs</span>

<span class="filename">Nome do arquivo: lib.rs</span>

```rust
pub trait Summarizable {
    fn summary(&self) -> String {
        String::from("(Read more...)")
    }
}
```

```rust
pub trait Resumir {
    fn resumo(&self) -> String {
        String::from("(Leia mais...)")
    }
}
```

<span class="caption">Listing 10-15: Definition of a `Summarizable` trait with
a default implementation of the `summary` method</span>

<span class="caption">Listagem 10-15: Definição de um trait `Resumir` com a 
implementação padrão do método `resumo`</span>

If we wanted to use this default implementation to summarize instances of
`NewsArticle` instead of defining a custom implementation like we did in
Listing 10-13, we would specify an empty `impl` block:

Se nós quiséssemos usar a implementação padrão para resumir as instâncias de
`ArtigoDeNoticia` ao invés de definir uma implementação personalizada como 
fizemos na Listagem 10-13, nós especificaríamos um bloco `impl` vazio:

```rust,ignore
impl Summarizable for NewsArticle {}
```

```rust,ignore
impl Resumir for ArtigoDeNoticia {}
```

Even though we’re no longer choosing to define the `summary` method on
`NewsArticle` directly, since the `summary` method has a default implementation
and we specified that `NewsArticle` implements the `Summarizable` trait, we can
still call the `summary` method on an instance of `NewsArticle`:

Mesmo que não estejamos mais escolhendo definir o método `resumo` diretamente 
em `ArtigoDeNoticia`, já que o método `resumo` tem uma implementação padrão e 
nós especificamos que `ArtigoDeNoticia` implementa o trait `Resumir`, nós ainda
podemos chamar o método `resumo` em uma instância de `ArtigoDeNoticia`:

```rust,ignore
let article = NewsArticle {
    headline: String::from("Penguins win the Stanley Cup Championship!"),
    location: String::from("Pittsburgh, PA, USA"),
    author: String::from("Iceburgh"),
    content: String::from("The Pittsburgh Penguins once again are the best
    hockey team in the NHL."),
};

println!("New article available! {}", article.summary());
```

```rust,ignore
let artigo = ArtigoDeNoticia {
    titulo: String::from("Os Penguins ganham a copa do campeonato Stanley"),
    lugar: String::from("Pittsburgh, PA, USA"),
    autor: String::from("Iceburgh"),
    conteudo: String::from("Os Penguins de Pittsburgh são novamente o melhor
    time de hockey da NHL."),
};

println!("Novo artigo disponível! {}", artigo.summary());
```

This code prints `New article available! (Read more...)`.

Esse código imprime `Novo artigo disponível! (Leia mais...)`

Changing the `Summarizable` trait to have a default implementation for
`summary` does not require us to change anything about the implementations of
`Summarizable` on `Tweet` in Listing 10-13 or `WeatherForecast` in Listing
10-14: the syntax for overriding a default implementation is exactly the same
as the syntax for implementing a trait method that doesn’t have a default
implementation.

Mudando o trait `Resumir` para ter uma implementação padrão para `resumo` não
requer que nós mudemos nada na implementação de `Resumir` em `Tweet` na 
Listagem 10-13 ou em `PrevisaoTempo` na Listagem 10-14: a sintaxe para sobrepor
uma implementação padrão é exatamente a mesma de uma sintaxe para implementar
um método de trait que não tem uma implementação padrão.

Default implementations are allowed to call the other methods in the same
trait, even if those other methods don’t have a default implementation. In this
way, a trait can provide a lot of useful functionality and only require
implementors to specify a small part of it. We could choose to have the
`Summarizable` trait also have an `author_summary` method whose implementation
is required, then a `summary` method that has a default implementation that
calls the `author_summary` method:

Implementações padrões são autorizadas a chamar outros métodos no mesmo trait,
mesmo se os outros métodos não tiverem uma implementação padrão. Desse modo, um
trait pode prover muitas funcionalidades úteis e apenas requerir implementações
para especificar uma pequena parte dele. Nós poderíamos escolher que o trait
`Resumir` também tivesse o método `resumo_autor` qual a implementação é 
necessária, então um método `resumo` que tem a implementação padrão que chama
pelo método `resumo_autor`:

```rust
pub trait Summarizable {
    fn author_summary(&self) -> String;

    fn summary(&self) -> String {
        format!("(Read more from {}...)", self.author_summary())
    }
}
```

```rust
pub trait Resumir {
    fn resumo_autor(&self) -> String;

    fn resumo(&self) -> String {
        format!("(Leia mais de {}...)", self.resumo_autor())
    }
}
```

In order to use this version of `Summarizable`, we’re only required to define
`author_summary` when we implement the trait on a type:

Para usar essa versão de `Resumir`, nós só precisamos definir `resumo_autor`
quando nós implementamos o trait em um tipo:

```rust,ignore
impl Summarizable for Tweet {
    fn author_summary(&self) -> String {
        format!("@{}", self.username)
    }
}
```

```rust,ignore
impl Resumir for Tweet {
    fn autor_resumo(&self) -> String {
        format!("@{}", self.nomeusuario)
    }
}
```

Once we define `author_summary`, we can call `summary` on instances of the
`Tweet` struct, and the default implementation of `summary` will call the
definition of `author_summary` that we’ve provided.

Uma vez que definimos `resumo_autor`, nós podemos chamar `resumo` em instâncias
do struct `Tweet`, e a implementação padrão de `resumo` chamará a definição de
`resumo_autor` que fornecemos.

```rust,ignore
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summary());
```

```rust,ignore
let tweet = Tweet {
    nomeusuario: String::from("horse_ebooks"),
    conteudo: String::from("claro, como vocês provavelmente já sabem, 
    pessoas"),
    resposta: false,
    retweet: false,
};

println!("1 novo tweet: {}", tweet.resumo());
```

This will print `1 new tweet: (Read more from @horse_ebooks...)`.

Isso irá imprimir `1 novo tweet: (Leia mais de @horse_ebooks...)`.

Note that it is not possible to call the default implementation from an
overriding implementation.

Note que não é possível chamar a implementação padrão de uma implementação
primordial.

### Trait Bounds

### Limites de traits

Now that we’ve defined traits and implemented those traits on types, we can use
traits with generic type parameters. We can constrain generic types so that
rather than being any type, the compiler will ensure that the type will be
limited to those types that implement a particular trait and thus have the
behavior that we need the types to have. This is called specifying *trait
bounds* on a generic type.

Agora que definimos traits e os implementamos em tipos, podemos usar traits com
parâmetros de tipos genéricos. Podemos restringir tipos genéricos para que ao
invés de serem qualquer tipo, o compilador tenha certeza que o tipo estará 
limitado a aqueles tipos que implementam um trait em particular e por 
consequência tenham o comportamento que precisamos que os tipos tenham. Isso é
chamado de especificar os *limites dos traits* em um tipo genérico.

For example, in Listing 10-13, we implemented the `Summarizable` trait on the
types `NewsArticle` and `Tweet`. We can define a function `notify` that calls
the `summary` method on its parameter `item`, which is of the generic type `T`.
To be able to call `summary` on `item` without getting an error, we can use
trait bounds on `T` to specify that `item` must be of a type that implements
the `Summarizable` trait:

Por exemplo, na Listagem 10-13, nós implementamos o trait `Resumir` nos tipos
`ArtigoDeNoticia` e `Tweet`. Nós podemos definir uma função `notificar` que chama
o método `resumo` no seu parâmetro `item`, que é do tipo genérico `T`. Para 
ser possível chamar `resumo` em `item` sem receber um erro, podemos usar os 
limites de traits em `T` para especificar que `item` precisa ser de um tipo que
implementa o trait `Resumir`:

```rust,ignore
pub fn notify<T: Summarizable>(item: T) {
    println!("Breaking news! {}", item.summary());
}
```

```rust,ignore
pub fn notificar<T: Resumir>(item: T) {
    println!("Notícias de última hora! {}", item.resumo());
}
```

Trait bounds go with the declaration of the generic type parameter, after a
colon and within the angle brackets. Because of the trait bound on `T`, we can
call `notify` and pass in any instance of `NewsArticle` or `Tweet`. The
external code from Listing 10-14 that’s using our `aggregator` crate can call
our `notify` function and pass in an instance of `WeatherForecast`, since
`Summarizable` is implemented for `WeatherForecast` as well. Code that calls
`notify` with any other type, like a `String` or an `i32`, won’t compile, since
those types do not implement `Summarizable`.

Limites de traits vão juntos com a declaração de um parâmetro de tipo genérico,
depois de uma vírgula e entre colchetes angulares. Por causa do limite de trait
em  `T`, nós podemos chamar `notificar` e passar qualquer instância de 
`ArtigoDeNoticia` ou `Tweet`. O código externo da Listagem 10-14 que está 
usando nosso crate `aggregator` pode chamar nossa função `notificar` e passar
uma instância de `PrevisaoTempo`, já que `Resumir` é implementado para 
`PrevisaoTempo` também. O código que chama `notificar` com qualquer outro tipo,
como uma `String` ou um `i32`, não compilará, já que esses tipos não 
implementam `Resumir`.

We can specify multiple trait bounds on a generic type by using `+`. If we
needed to be able to use display formatting on the type `T` in a function as
well as the `summary` method, we can use the trait bounds `T: Summarizable +
Display`. This means `T` can be any type that implements both `Summarizable`
and `Display`.

Nós podemos especificar múltiplos limites de traits em um tipo genérico usando
`+`. Se nós precisássemos ser capazes de usar mostrar formatação no tipo `T` em
uma função assim como no método `resumo`, nós podemos usar os limites de trait
`T: Resumir + Mostrar`. Isso signifca que `T` pode ser qualquer tipo que 
implemente ambos `Resumir` e `Mostrar`.

For functions that have multiple generic type parameters, each generic has its
own trait bounds. Specifying lots of trait bound information in the angle
brackets between a function’s name and its parameter list can get hard to read,
so there’s an alternate syntax for specifying trait bounds that lets us move
them to a `where` clause after the function signature. So instead of:

Para funções que têm múltiplos parâmetros de tipos genéricos, cada tipo 
genérico tem seu próprio limite de trait. Especificar muitas informações de 
limites de trait dentro de chaves angulares entre o nome de uma função e sua
lista de parâmetros pode tornar o código difícil de ler, então há uma sintaxe 
alternativa para especificar limites de traits que nos permite movê-los para
uma cláusula depois da assinatura da função. Então ao invés de:

```rust,ignore
fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U) -> i32 {
```

```rust,ignore
fn alguma_funcao<T: Mostrar + Clone, U: Clone + Debug>(t: T, u: U) -> i32 {
```

We can write this instead with a `where` clause:

Nós podemos escrever isso com uma cláusula de `where`:

```rust,ignore
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

```rust,ignore
fn alguma_funcao<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

This is less cluttered and makes this function’s signature look more similar to
a function without lots of trait bounds, in that the function name, parameter
list, and return type are close together.

Isso é menos confuso e faz a assinatura da função ficar mais parecida à uma
função sem ter vários limites de trait, nela o nome da função, a lista de
parâmetros, e o tipo de retorno estão mais próximos.

### Fixing the `largest` Function with Trait Bounds

### Consertando a Função `maior` com Limites de Traits

So any time you want to use behavior defined by a trait on a generic, you need
to specify that trait in the generic type parameter’s type bounds. We can now
fix the definition of the `largest` function that uses a generic type parameter
from Listing 10-5! When we set that code aside, we were getting this error:

Então qualquer hora que você queira usar um comportamento definido por um trait
em um tipo genérico, você precisa especificar aquele trait nos limites dos
parâmetros dos tipos genéricos. Agora podemos consertar a definição da função 
`maior` que usa um parâmetro de tipo genérico da Listagem 10-5! Quando deixamos
esse código de lado, nós recebemos esse erro:


```text
error[E0369]: binary operation `>` cannot be applied to type `T`
  |
5 |         if item > largest {
  |            ^^^^
  |
note: an implementation of `std::cmp::PartialOrd` might be missing for `T`
```

```text
error[E0369]: binary operation `>` cannot be applied to type `T`
  |
5 |         if item > maior {
  |            ^^^^
  |
note: an implementation of `std::cmp::PartialOrd` might be missing for `T`
```

In the body of `largest` we wanted to be able to compare two values of type `T`
using the greater-than operator. That operator is defined as a default method
on the standard library trait `std::cmp::PartialOrd`. So in order to be able to
use the greater-than operator, we need to specify `PartialOrd` in the trait
bounds for `T` so that the `largest` function will work on slices of any type
that can be compared. We don’t need to bring `PartialOrd` into scope because
it’s in the prelude.

No corpo de `maior` nós queríamos ser capazes de comparar dois valores de tipo
`T` usando o operador maior-que. Esse operador é definido com o método padrão 
na biblioteca padrão de trait `std::cmp::PartialOrd`. Então para que possamos
usar o operador maior-que, precisamos especificar `PartialOrd` nos limites do
trait  para `T` para que a função `maior` funcione em partes de qualquer tipo
que possa ser comparada. Não precisamos trazer `PartialOrd` para o escopo 
porque está no prelúdio.

```rust,ignore
fn largest<T: PartialOrd>(list: &[T]) -> T {
```

```rust,ignore
fn maior<T: PartialOrd>(list: &[T]) -> T {
```

If we try to compile this, we’ll get different errors:

Se tentarmos compilar isso, receberemos diferentes erros:

```text
error[E0508]: cannot move out of type `[T]`, a non-copy array
 --> src/main.rs:4:23
  |
4 |     let mut largest = list[0];
  |         -----------   ^^^^^^^ cannot move out of here
  |         |
  |         hint: to prevent move, use `ref largest` or `ref mut largest`

error[E0507]: cannot move out of borrowed content
 --> src/main.rs:6:9
  |
6 |     for &item in list.iter() {
  |         ^----
  |         ||
  |         |hint: to prevent move, use `ref item` or `ref mut item`
  |         cannot move out of borrowed content
```

```text
error[E0508]: cannot move out of type `[T]`, a non-copy array
 --> src/main.rs:4:23
  |
4 |     let mut maior = list[0];
  |         -----------   ^^^^^^^ cannot move out of here
  |         |
  |         hint: to prevent move, use `ref maior` or `ref mut maior`

error[E0507]: cannot move out of borrowed content
 --> src/main.rs:6:9
  |
6 |     for &item in list.iter() {
  |         ^----
  |         ||
  |         |hint: to prevent move, use `ref item` or `ref mut item`
  |         cannot move out of borrowed content
```

The key to this error is `cannot move out of type [T], a non-copy array`.
With our non-generic versions of the `largest` function, we were only trying to
find the largest `i32` or `char`. As we discussed in Chapter 4, types like
`i32` and `char` that have a known size can be stored on the stack, so they
implement the `Copy` trait. When we changed the `largest` function to be
generic, it’s now possible that the `list` parameter could have types in it
that don’t implement the `Copy` trait, which means we wouldn’t be able to move
the value out of `list[0]` and into the `largest` variable.

A chave para esse erro é `cannot move out of type [T], a non-copy array`. Com
nossas versões não genéricas da função `maior`, nós estávamos apenas tentando
encontrar o maior `i32` ou `char`. Como discutimos no Capítulo 4, tipos como o
`i32` e `char` que têm um tamanho conhecido podem ser armazenados na pilha,
então eles implementam o trait `Copia`. Quando mudamos a função `maior` para 
ser genérica, agora é possível que o parâmetro `list` poderia ter tipos nele
que não implementam o trait `Copia`, o que significa que não seríamos capazes 
de mover o valor para fora de `list[0]` para a variável `maior`.

If we only want to be able to call this code with types that are `Copy`, we can
add `Copy` to the trait bounds of `T`! Listing 10-16 shows the complete code of
a generic `largest` function that will compile as long as the types of the
values in the slice that we pass into `largest` implement both the `PartialOrd`
and `Copy` traits, like `i32` and `char`:

Se quisermos ser capazes de chamar esse código com tipos que são `Copia`, nós
podemos adicionar `Copia` para os limites de trait de `T`! A Listagem 10-16 
mostra o código completo de uma função `maior` genérica que compilará desde que
os tipos dos valores nessa parte que passamos para `maior` implementem ambos os
traits `PartialOrd` e `Copia`, como `i32` e `char`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn largest<T: PartialOrd + Copy>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = largest(&char_list);
    println!("The largest char is {}", result);
}
```

```rust
fn maior<T: PartialOrd + Copy>(list: &[T]) -> T {
    let mut maior = list[0];

    for &item in list.iter() {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let result = maior(&lista_numero);
    println!("O maior número é {}", result);

    let lista_char = vec!['y', 'm', 'a', 'q'];

    let result = maior(&lista_char);
    println!("O maior char é {}", result);
}
```

<span class="caption">Listing 10-16: A working definition of the `largest`
function that works on any generic type that implements the `PartialOrd` and
`Copy` traits</span>

<span class="caption">Listagem 10-16: Uma definição funcional da função `maior`
que funciona em qualquer tipo genérico que implementa os traits `PartialOrd` e
`Copia`</span>

If we don’t want to restrict our `largest` function to only types that
implement the `Copy` trait, we could specify that `T` has the trait bound
`Clone` instead of `Copy` and clone each value in the slice when we want the
`largest` function to have ownership. Using the `clone` function means we’re
potentially making more heap allocations, though, and heap allocations can be
slow if we’re working with large amounts of data. Another way we could
implement `largest` is for the function to return a reference to a `T` value in
the slice. If we change the return type to be `&T` instead of `T` and change
the body of the function to return a reference, we wouldn’t need either the
`Clone` or `Copy` trait bounds and we wouldn’t be doing any heap allocations.
Try implementing these alternate solutions on your own!

Se não quisermos restringir nossa função `maior` para apenas tipos que 
implementam o trait `Copia`, podemos especificar que `T` tem o limite de trait
`Clone` ao invés de `Copia` e clonar cada valor na parte quando quisermos que a
função `maior` tenha domínio. Usando a função `clone` significa que 
potencialmente estamos fazendo mais alocações no heap, porém, e alocações no 
heap podem ser vagarosas se estivermos trabalhando com grande quantidade de 
dados. Outro jeito que podemos implementar `maior` é para a função retornar uma
referência ao valor de `T` em uma parte. Se retornarmos o tipo de retorno para
ser `&T` ao invés de `T` e mudar o corpo da função para retornar uma 
referência, não precisaríamos usar os limites de traits `Clone` ou `Copia` e
nós não estaríamos fazendo nenhuma alocação de heap.
Tente implementar essas soluções alternativas você mesmo! 

### Using Trait Bounds to Conditionally Implement Methods

### Usando Limites de Trait para Implementar Métodos Condicionalmente

By using a trait bound with an `impl` block that uses generic type parameters,
we can conditionally implement methods only for types that implement the
specified traits. For example, the type `Pair<T>` in listing 10-17 always
implements the `new` method, but `Pair<T>` only implements the `cmp_display` if
its inner type `T` implements the `PartialOrd` trait that enables comparison
and the `Display` trait that enables printing:

Usando um limite de trait com um bloco `impl` que usa parâmetros de tipos 
genéricos podemos implementar métodos condicionalmente apenas para tipos que
implementam os traits específicos. Por exemplo, o tipo `Par<T>` na listagem 
10-17 sempre implementa o método `novo`, mas `Par<T>` implementa apenas o
`cmp_display` se seu tipo interno `T` implementa o trait `PartialOrd` que 
permite a comparação e do trait `Display` que permite a impressão:

```rust
use std::fmt::Display;

struct Pair<T> {
    x: T,
    y: T,
}

impl<T> Pair<T> {
    fn new(x: T, y: T) -> Self {
        Self {
            x,
            y,
        }
    }
}

impl<T: Display + PartialOrd> Pair<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("The largest member is x = {}", self.x);
        } else {
            println!("The largest member is y = {}", self.y);
        }
    }
}
```

```rust
use std::fmt::Display;

struct Par<T> {
    x: T,
    y: T,
}

impl<T> Par<T> {
    fn novo(x: T, y: T) -> Self {
        Self {
            x,
            y,
        }
    }
}

impl<T: Display + PartialOrd> Par<T> {
    fn cmp_display(&self) {
        if self.x >= self.y {
            println!("O maior membro é x = {}", self.x);
        } else {
            println!("O maior membro é y = {}", self.y);
        }
    }
}
```

<span class="caption">Listing 10-17: Conditionally implement methods on a
generic type depending on trait bounds</span>

<span class="caption">Listagem 10-17: Implementa métodos condicionalmente em um
tipo genérico dependendo dos limites de trait</span>

We can also conditionally implement a trait for any type that implements a
trait. Implementations of a trait on any type that satisfies the trait bounds
are called *blanket implementations*, and are extensively used in the Rust
standard library. For example, the standard library implements the `ToString`
trait on any type that implements the `Display` trait. This `impl` block looks
similar to this code:

Podemos também condicionalmente implementar um trait para qualquer tipo que
implementa um trait. Implementações de trait de qualquer tipo que satisfazem os
limites de trait são chamadas de *implementações cobertores*, e são 
extesivamente utilizadas na biblioteca padrão de Rust. Por exemplo, a 
biblioteca padrão implementa o trait `Display`. Esse bloco `impl` se parece com
este código:

```rust,ignore
impl<T: Display> ToString for T {
    // --snip--
}
```

```rust,ignore
impl<T: Display> ToString for T {
    // --snip--
}
```

Because the standard library has this blanket implementation, we can call the
`to_string` method defined by the `ToString` type on any type that implements
the `Display` trait. For example, we can turn integers into their corresponding
`String` values like this since integers implement `Display`:

Porque a biblioteca padrão tem essa implementação cobertor, podemos chamar
o método `to_string` definido pelo tipo `ToString` em qualquer tipo que 
implemente o trait `Display`. Por exemplo, nós podemos transformar inteiros em
seus correspondentes valores de `String` do seguinte modo, já que inteiros 
implementam `Display`:

```rust
let s = 3.to_string();
```

```rust
let s = 3.to_string();
```

Blanket implementations appear in the documentation for the trait in the
“Implementors” section.

Implementações cobertor aparecem na documentação para traits na seção 
"Implementadores".

Traits and trait bounds let us write code that uses generic type parameters in
order to reduce duplication, but still specify to the compiler exactly what
behavior our code needs the generic type to have. Because we’ve given the trait
bound information to the compiler, it can check that all the concrete types
used with our code provide the right behavior. In dynamically typed languages,
if we tried to call a method on a type that the type didn’t implement, we’d get
an error at runtime. Rust moves these errors to compile time so that we’re
forced to fix the problems before our code is even able to run. Additionally,
we don’t have to write code that checks for behavior at runtime since we’ve
already checked at compile time, which improves performance compared to other
languages without having to give up the flexibility of generics.

Traits e limites de traits nos deixam escrever código que usam parâmetros de
tipos genéricos para reduzir a duplicação, mas ainda sim especificam para o
compilador exatamente qual o comportamento que nosso código precisa que o tipo
genérico tenha. Porque demos a informação do limite de trait para o compilador,
ele pode checar que todos os tipos concretos usados no nosso código 
proporcionam o comportamento correto. Em linguagens dinamicamente tipadas, se
nós tentássemos chamar um método em um tipo que não implementamos, nós 
receberíamos um erro em tempo de execução. O Rust move esses erros para o temp
de compilação para que possamos ser forçados a resolver os problemas antes que 
nosso código seja capaz de rodar. Além disso, nós não temos que escrever código
que checa o comportamento em tempo de execução já que já checamos em tempo de
compilação, o que melhora o desempenho comparado com outras linguagens sem ter
que abrir mão da flexibilidade de tipos genéricos. 

There’s another kind of generics that we’ve been using without even realizing
it called *lifetimes*. Rather than helping us ensure that a type has the
behavior we need it to have, lifetimes help us ensure that references are valid
as long as we need them to be. Let’s learn how lifetimes do that.

Há outro tipo de tipos genéricos que estamos usando sem nem ao menos perceber
chamados *lifetimes*. Em vez de nos ajudar a garantir que um tipo tenha o
comportamento que precisamos, lifetimes nos ajudam a garantir que as 
referências são válidas tanto quanto precisam ser. Vamos aprender como 
lifetimes fazem isso.