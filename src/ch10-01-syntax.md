## Generic Data Types

## Tipos Genéricos de Dados

Using generics where we usually place types, like in function signatures or
structs, lets us create definitions that we can use for many different concrete
data types. Let’s take a look at how to define functions, structs, enums, and
methods using generics, and at the end of this section we’ll discuss the
performance of code using generics.

Usando tipos genéricos onde usualmente colocamos tipos, como em assinaturas de
funções ou estruturas, vamos criar definições que podemos usar muitos tipos 
diferentes de tipos concretos de dados. Vamos dar uma olhada em como definir
funções, structs, enums e métodos usando tipos genéricos, e ao final dessa
seção discutiremos a performance do código usando tipos genéricos.

### Using Generic Data Types in Function Definitions

### Usando Tipos Genéricos de Dados em Definições de Funções

We can define functions that use generics in the signature of the function
where the data types of the parameters and return value go. In this way, the
code we write can be more flexible and provide more functionality to callers of
our function, while not introducing code duplication.

Nós podemos definir funções que usam tipos genéricos na assinatura da função
onde os tipos de dados dos parâmetros e os retornos vão. Desse modo, o código
que escrevemos pode ser mais flexível e pode fornecer mais funcionalidades para 
os chamadores da nossa função, e ainda diminuir duplicação de código.

Continuing with our `largest` function, Listing 10-4 shows two functions
providing the same functionality to find the largest value in a slice. The
first function is the one we extracted in Listing 10-3 that finds the largest
`i32` in a slice. The second function finds the largest `char` in a slice:

Continuando com nossa função `maior`, a Listagem 10-4 mostra duas funções que 
oferecem a mesma funcionalidade de encontrar o maior valor dado um corte. A
primeira função é a que extraímos na Listagem 10-3 que encontra o maior `ì32` 
em um corte. A segunda função encontra o maior `char` em um corte:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do Arquivo: src/main.rs</span>

```rust
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn largest_char(list: &[char]) -> char {
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

    let result = largest_i32(&number_list);
    println!("The largest number is {}", result);
#    assert_eq!(result, 100);

    let char_list = vec!['y', 'm', 'a', 'q'];

    let result = largest_char(&char_list);
    println!("The largest char is {}", result);
#    assert_eq!(result, 'y');
}
```

```rust
fn maior_i32(lista: &[i32]) -> i32 {
    let mut maior = list[0];

    for &item in lista.iter() {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn maior_char(lista: &[char]) -> char {
    let mut maior = lista[0];

    for &item in lista.iter() {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let resultado = maior_i32(&lista_numero);
    println!("O maior número {}", resultado);
#    assert_eq!(resultado, 100);

    let lista_char = vec!['y', 'm', 'a', 'q'];

    let resultado = maior_char(&lista_char);
    println!("O maior char é {}", resultado);
#    assert_eq!(resultado, 'y');
}
```

<span class="caption">Listing 10-4: Two functions that differ only in their
names and the types in their signatures</span>

<span class="caption">Listing 10-4: Duas funções que diferem apenas em seus
nomes e nos tipos de suas assinaturas</span>

Here, the functions `largest_i32` and `largest_char` have the exact same body,
so it would be nice if we could turn these two functions into one and get rid
of the duplication. Luckily, we can do that by introducing a generic type
parameter!

Aqui as funções `maior_i32` e `maior_char` tem exatamente o mesmo corpo, então 
seria bom se pudéssemos transformar essas duas funções em uma e nos livrar da 
duplicação. Por sorte, nós podemos fazer isso introduzindo um parâmetro de 
tipo genérico!

To parameterize the types in the signature of the one function we’re going to
define, we need to create a name for the type parameter, just like how we give
names for the value parameters to a function. We’re going to choose the name
`T`. Any identifier can be used as a type parameter name, but we’re choosing
`T` because Rust’s type naming convention is CamelCase. Generic type parameter
names also tend to be short by convention, often just one letter. Short for
“type”, `T` is the default choice of most Rust programmers.

Para parametrizar os tipos na assinatura de uma função que vamos definir,
precisamos criar um nome para o tipo parâmetro, assim como damos nomes para os 
valores dos parâmetros de uma função. Nós vamos escolher o nome `T`. Qualquer 
identificador pode ser usado como um nome de tipo de parâmetro, mas estamos 
escolhendo `T` porque a convenção de nomes de tipos de Rust é a CamelCase. 
Nomes de parâmetros de tipos genéricos também tendem a ser curtos por 
convenção, e frequentemente usam apenas uma letra. A abreviatura de "tipo", `T`
é a escolha padrão feita por programadores Rust.

When we use a parameter in the body of the function, we have to declare the
parameter in the signature so that the compiler knows what that name in the
body means. Similarly, when we use a type parameter name in a function
signature, we have to declare the type parameter name before we use it. Type
name declarations go in angle brackets between the name of the function and the
parameter list.

Quando usamos um parâmetro no corpo de uma função, nós temos que declarar o 
parâmetro na assinatura para que o compilador saiba o que aquele nome no corpo
significa. Similarmente, quando usamos um tipo de nome de parâmetro em uma 
assinatura de função, temos que declarar o tipo de nome de parâmetro antes de 
usa-lo. Declarações de tipos de nomes vão em colchetes entre o nome da função e 
a lista de paramêtros.

The function signature of the generic `largest` function we’re going to define
will look like this:

A assinatura da função da função genérica `maior` que vamos definir se parecerá
com isto:

```rust,ignore
fn largest<T>(list: &[T]) -> T {
```

```rust,ignore
fn maior<T>(lista: &[T]) -> T {
```

We would read this as: the function `largest` is generic over some type `T`. It
has one parameter named `list`, and the type of `list` is a slice of values of
type `T`. The `largest` function will return a value of the same type `T`.

Nós leríamos isso como: a função `maior` é genérica sobre algum tipo `T`. Ela
tem um parâmetro chamado `lista`, e o tipo de `lista` é um corte dos valores
do tipo `T`. A função `maior` retornará um valor do mesmo tipo `T`.

Listing 10-5 shows the unified `largest` function definition using the generic
data type in its signature, and shows how we’ll be able to call `largest` with
either a slice of `i32` values or `char` values. Note that this code won’t
compile yet!

A listagem 10-5 mostra a definição da função unificada `maior` usando um tipo 
genérico de dado na sua assinatura, e mostra quando nós poderemos chamar a 
função `maior` com ou um corte de valores de `i32` ou de valores `char`. Note 
que esse código não compilará ainda! 

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn largest<T>(list: &[T]) -> T {
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


```rust,ignore
fn maior<T>(lista: &[T]) -> T {
    let mut maior = lista[0];

    for &item in lista.iter() {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let resultado = maior(&lista_numero);
    println!("The maior number is {}", resultado);

    let lista_char = vec!['y', 'm', 'a', 'q'];

    let resultado = maior(&char_lista);
    println!("O maior char e {}", resultado);
}
```

<span class="caption">Listing 10-5: A definition of the `largest` function that
uses generic type parameters but doesn’t compile yet</span>

<span class="caption">Listagem 10-5: Uma definição para a função `maior` que 
usa um tipo genérico como parâmetro mas não compila ainda</span>

If we try to compile this code right now, we’ll get this error:

Se nós tentarmos compilar o código agora, nós receberemos esse erro:

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

The note mentions `std::cmp::PartialOrd`, which is a *trait*. We’re going to
talk about traits in the next section, but briefly, what this error is saying
is that the body of `largest` won’t work for all possible types that `T` could
be; since we want to compare values of type `T` in the body, we can only use
types that know how to be ordered. The standard library has defined the trait
`std::cmp::PartialOrd` that types can implement to enable comparisons. We’ll
come back to traits and how to specify that a generic type has a particular
trait in the next section, but let’s set this example aside for a moment and
explore other places we can use generic type parameters first.

A nota menciona `std::cmp::PartialOrd`, que é um *trait*. Nós vamos falar sobre
trait na próxima sessão, mas de forma breve, o que esse erro está dizendo é que
o corpo de `maior` não funcionará para todos os possíveis tipos que `T` poderia
ser; já que queremos comparar valores do tipo `T` no corpo, nós podemos apenas
usar tipos que sabem como ser ordenados. A biblioteca padrão definiu que o 
trait `std::cmp::PartialOrd` que tipos podem implementar para habilitar
comparações. Vamos voltar a traits e em como especificar que um tipo genérico
tenha um trait em particular na próxima sessão, mas vamos deixar isso de lado 
por um momento e explorar outros lugares que podemos usar parâmetros de tipos 
genéricos primeiro.

<!-- Liz: this is the reason we had the topics in the order we did in the first
draft of this chapter; it's hard to do anything interesting with generic types
in functions unless you also know about traits and trait bounds. I think this
ordering could work out okay, though, and keep a stronger thread with the
`longest` function going through the whole chapter, but we do pause with a
not-yet-compiling example here, which I know isn't ideal either. Let us know
what you think. /Carol -->

### Using Generic Data Types in Struct Definitions

### Usando Tipos de Dados Genéros em Definições de Structs

We can define structs to use a generic type parameter in one or more of the
struct’s fields with the `<>` syntax too. Listing 10-6 shows the definition and
use of a `Point` struct that can hold `x` and `y` coordinate values of any type:

Nós podemos definir structs para usar um parâmetro de tipo genérico em um ou 
mais campos de um struct com a sintaxe `<>` também. A listagem 10-6 mostra a 
definição e faz uso do struct `Ponto` que contém as coordenadas `x` e `y` com 
valores de qualquer tipo:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```


```rust
struct Ponto<T> {
    x: T,
    y: T,
}

fn main() {
    let inteiro = Ponto { x: 5, y: 10 };
    let float = Ponto { x: 1.0, y: 4.0 };
}
```

<span class="caption">Listing 10-6: A `Point` struct that holds `x` and `y`
values of type `T`</span>

<span class="caption">Listagem 10-6: Uma struct `Ponto` contém os valores `x` e
`y` do tipo `T`</span>

The syntax is similar to using generics in function definitions. First, we have
to declare the name of the type parameter within angle brackets just after the
name of the struct. Then we can use the generic type in the struct definition
where we would specify concrete data types.

A sintaxe é similar a que se usa em definições de funções usando tipos 
genéricos. Primeiro, nós temos que declarar o nome do tipo de parâmetro dentro
de colchetes angulares logo após o nome da struct. Então nós podemos usar tipos
genéricos na definição da struct onde nós especificaríamos tipos concretos de
dados.

Note that because we’ve only used one generic type in the definition of
`Point`, what we’re saying is that the `Point` struct is generic over some type
`T`, and the fields `x` and `y` are *both* that same type, whatever it ends up
being. If we try to create an instance of a `Point` that has values of
different types, as in Listing 10-7, our code won’t compile:

Note que porque só usamos um tipo genérico na definição de `Ponto`, o que 
estamos dizendo é que o struct `Ponto` é genérico sobre algum tipo `T`, e os
campos `x` e `y` são *ambos* do mesmo tipo, qualquer que seja. Se nós tentarmos
criar uma instância de um `Ponto` que possui valores de tipos diferentes, como
na Listagem 10-7, nosso código não compilará:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let wont_work = Point { x: 5, y: 4.0 };
}
```

```rust,ignore
struct Ponto<T> {
    x: T,
    y: T,
}

fn main() {
    let nao_funciona = Ponto { x: 5, y: 4.0 };
}
```

<span class="caption">Listing 10-7: The fields `x` and `y` must be the same
type because both have the same generic data type `T`</span>

<span class="caption">Listagem 10-7: Os campos `x` e `y` precisam ser do mesmo
tipo porque ambos tem o tipo genérico de dado `T`</span>

If we try to compile this, we’ll get the following error:

Se nós tentarmos compilar isso, receberemos o seguinte erro:

```text
error[E0308]: mismatched types
 -->
  |
7 |     let wont_work = Point { x: 5, y: 4.0 };
  |                                      ^^^ expected integral variable, found
  floating-point variable
  |
  = note: expected type `{integer}`
  = note:    found type `{float}`
```

```text
error[E0308]: mismatched types
 -->
  |
7 |     let nao_funciona = Point { x: 5, y: 4.0 };
  |                                         ^^^ expected integral variable, found
  floating-point variable
  |
  = note: expected type `{integer}`
  = note:    found type `{float}`
```

When we assigned the integer value 5 to `x`, the compiler then knows for this
instance of `Point` that the generic type `T` will be an integer. Then when we
specified 4.0 for `y`, which is defined to have the same type as `x`, we get a
type mismatch error.

Quando atribuímos o valor de 5 para `x`, o compilador sabe que para essa 
instância de `Ponto` o tipo genérico `T` será um número inteiro. Então quando
especificamos 4.0 para `y`, o qual é definido para ter o mesmo tipo de `x`, nós
temos um tipo de erro de incompatibilidade.

If we wanted to define a `Point` struct where `x` and `y` could have different
types but still have those types be generic, we can use multiple generic type
parameters. In listing 10-8, we’ve changed the definition of `Point` to be
generic over types `T` and `U`. The field `x` is of type `T`, and the field `y`
is of type `U`:

Se nós quisermos definir um struct de `Ponto` onde `x` e `y` têm tipos 
diferentes e quisermos fazer com que esses tipos sejam genéricos, nós podemos 
usar parâmetros múltiplos de tipos genéricos. Na listagem 10-8, nós mudamos a
definição do `Ponto` para os tipos genéricos `T` e `U`. O campo `x` é do tipo
`T`, e o campo `y` do tipo `U`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

fn main() {
    let both_integer = Point { x: 5, y: 10 };
    let both_float = Point { x: 1.0, y: 4.0 };
    let integer_and_float = Point { x: 5, y: 4.0 };
}
```

```rust
struct Ponto<T, U> {
    x: T,
    y: U,
}

fn main() {
    let ambos_inteiros = Ponto { x: 5, y: 10 };
    let ambos_floats = Ponto { x: 1.0, y: 4.0 };
    let inteiro_e_float = Ponto { x: 5, y: 4.0 };
}
```

<span class="caption">Listing 10-8: A `Point` generic over two types so that
`x` and `y` may be values of different types</span>

<span class="caption">Listagem 10-8: Um `Ponto` genérico sobre dois tipos `x` e
`y` podem ser valores de tipos diferentes</span>

Now all of these instances of `Point` are allowed! You can use as many generic
type parameters in a definition as you want, but using more than a few gets
hard to read and understand. If you get to a point of needing lots of generic
types, it’s probably a sign that your code could use some restructuring to be
separated into smaller pieces.

Agora todos as instâncias de `Ponto` são permitidas! Você pode usar quantos
parâmetros de tipos genéricos em uma definição quanto quiser, mas usar mais que
alguns começa a tornar o código difícil de ler e entender. Se você chegar em um
ponto que precisa usar muitos tipos genéricos, é provavelmente um sinal que seu
código poderia ser reestruturado e separado em partes menores.

### Using Generic Data Types in Enum Definitions

### Usando Tipos de Dados Genéricos em Definições de Enum

Similarly to structs, enums can be defined to hold generic data types in their
variants. We used the `Option<T>` enum provided by the standard library in
Chapter 6, and now its definition should make more sense. Let’s take another
look:

Similar a structs, enums podem ser definidos para conter tipos genéricos de 
dados nas suas variantes. Nós usamos o enum `Option<T>` concedido pela 
biblioteca padrão no capítulo 6, e agora a definição deve fazer mais sentido. 
Vamos dar uma outra olhada:

```rust
enum Option<T> {
    Some(T),
    None,
}
```

In other words, `Option<T>` is an enum generic in type `T`. It has two
variants: `Some`, which holds one value of type `T`, and a `None` variant that
doesn’t hold any value. The standard library only has to have this one
definition to support the creation of values of this enum that have any
concrete type. The idea of “an optional value” is a more abstract concept than
one specific type, and Rust lets us express this abstract concept without lots
of duplication.

Em outras palavras, `Option<T>` é um enum genérico do tipo `T`. Ele têm duas
variantes: `Some`, que contém o valor do tipo `T`, e uma variante `None` que 
não contém nenhum valor. A biblioteca padrão tem que ter apenas essa deifinição
para suportar a criação de valores desse enum que pode conter qualquer tipo
concreto. A ideia de um "um valor opcional" é um conceito mais abstrato que o 
de um tipo específico, e Rust nos deixa expressar esse conceito abstrato sem 
muitas duplicações.

Enums can use multiple generic types as well. The definition of the `Result`
enum that we used in Chapter 9 is one example:

Enum podem usar tipos múltiplos genéricos também. A definição do enum 
`Resultado` que usamos no Capítulo 9 é um exemplo:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

```rust
enum Resultado<T, E> {
    Ok(T),
    Err(E),
}
```

The `Result` enum is generic over two types, `T` and `E`. `Result` has two
variants: `Ok`, which holds a value of type `T`, and `Err`, which holds a value
of type `E`. This definition makes it convenient to use the `Result` enum
anywhere we have an operation that might succeed (and return a value of some
type `T`) or fail (and return an error of some type `E`). Recall Listing 9-2
when we opened a file: in that case, `T` was filled in with the type
`std::fs::File` when the file was opened successfully and `E` was filled in
with the type `std::io::Error` when there were problems opening the file.

O enum `Resultado` é genérico sobre dois tipos, `T` e `E`. `Resultado` tem duas
variantes: `Ok`, que contém um valor do tipo `T`, e `Err`, que contém um valor
do tipo  `E`. Essa definição faz com que seja conveniente usar o enum 
`Resultado` em qualquer lugar que tenhamos uma operação que possa ser bem 
sucedida (e retornar um valor de algum tipo `T`) ou falhar (e retornar um erro
de algum tipo `E`). Lembre da Listagem 9-2 quando abrimos um arquivo: naquele
caso, `T` tinha o tipo `std::fs::File` quando o arquivo era aberto com sucesso
e `E` tinha o tipo `std::io::Error` quando havia problemas em abrir o arquivo.

When you recognize situations in your code with multiple struct or enum
definitions that differ only in the types of the values they hold, you can
remove the duplication by using the same process we used with the function
definitions to introduce generic types instead.

Quando você reconhece situações no seu código com structs múltiplos ou 
definições de enum que diferem apenas nos tipos de valores que eles contém, 
você pode remover a duplicata usando o mesmo processo usado na definição de 
funções para introduzir tipos genéricos.

### Using Generic Data Types in Method Definitions

### Usando Tipos Genéricos de Dados em Definições de Métodos

Like we did in Chapter 5, we can implement methods on structs and enums that
have generic types in their definitions. Listing 10-9 shows the `Point<T>`
struct we defined in Listing 10-6. We’ve then defined a method named `x` on
`Point<T>` that returns a reference to the data in the field `x`:

Como fizemos no Capítulo 5, nós podemos implementar métodos em estruturas e
enums que têm tipos genéricos em suas definições. A Listagem 10-9 mostra o
struct `Ponto<T>` que definimos na Listagem 10-6. Nós, então, definimos um
método chamado `x` no `Ponto<T>` que retorna a referência para o dado no campo
`x`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

```rust
struct Ponto<T> {
    x: T,
    y: T,
}

impl<T> Ponto<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Ponto { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

<span class="caption">Listing 10-9: Implementing a method named `x` on the
`Point<T>` struct that will return a reference to the `x` field, which is of
type `T`.</span>

<span class="caption">Listagem 10-9: Implementando um método chamado `x` na
struct `Ponto<T>` que retornará uma referência para o campo `x`, que é do tipo
`T`.</span>

Note that we have to declare `T` just after `impl` in order to use `T` in the
type `Point<T>`. Declaring `T` as a generic type after the `impl` is how Rust
knows the type in the angle brackets in `Point` is a generic type rather than a
concrete type. For example, we could choose to implement methods on
`Point<f32>` instances rather than `Point` instances with any generic type.
Listing 10-10 shows that we don’t declare anything after the `impl` in this
case, since we’re using a concrete type, `f32`:

Note que temos que declarar `T` logo após `impl` para usar `T` no tipo 
`Ponto<T>`. Declarar `T` como um tipo genérico depois e `impl` é como o Rust
sabe se o tipo dentro das chaves angulares em `Ponto` é um tipo genérico ou um
tipo concreto. Por exemplo, nós poderíamos escolher implementar métodos nas
instâncias de `Ponto<f32>` ao invés nas de `Ponto` com qualquer tipo genérico.
A listagem 10-10 mostra que não declaramos nada depois de `impl` nesse caso, já
que estamos usanod um tipo concreto, `f32`:

```rust
# struct Point<T> {
#     x: T,
#     y: T,
# }
#
impl Point<f32> {
    fn distance_from_origin(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

```rust
# struct Ponto<T> {
#     x: T,
#     y: T,
# }
#
impl Ponto<f32> {
    fn distancia_da_origem(&self) -> f32 {
        (self.x.powi(2) + self.y.powi(2)).sqrt()
    }
}
```

<span class="caption">Listing 10-10: Building an `impl` block which only
applies to a struct with a specific type is used for the generic type parameter
`T`</span>

<span class="caption">Listagem 10-10: Construindo um bloco de `impl` que só se 
aplica a uma struct com o tipo específico usado pelo parâmetro de tipo genérico
`T`</span>

This code means the type `Point<f32>` will have a method named
`distance_from_origin`, and other instances of `Point<T>` where `T` is not of
type `f32` will not have this method defined. This method measures how far our
point is from the point of coordinates (0.0, 0.0) and uses mathematical
operations which are only available for floating-point types.

Esse código significa que o tipo `Ponto<f32>` terá um método chamado 
`distancia_da_origem`, e outras instâncias do `Ponto<T>` onde `T` não é do tipo
`f32` não terá esse método definido. Esse método quão longe nosso ponto está
das coordenadas (0.0, 0.0) e usa operações matemáticas que só estão disponíveis
para tipos de ponto-flutuantes.

Generic type parameters in a struct definition aren’t always the same generic
type parameters you want to use in that struct’s method signatures. Listing
10-11 defines a method `mixup` on the `Point<T, U>` struct from Listing 10-8.
The method takes another `Point` as a parameter, which might have different
types than the `self` `Point` that we’re calling `mixup` on. The method creates
a new `Point` instance that has the `x` value from the `self` `Point` (which is
of type `T`) and the `y` value from the passed-in `Point` (which is of type
`W`):

Parâmetros de tipos genéricos em uma definição de struct não são sempre os 
parâmetros de tipos genéricos que você quer usar na assinatura de método 
daquela struct. A Listagem 10-11 define um método `mistura` na estrutura
`Ponto<T, U>` da Listagem 10-8. O método recebe outro `Ponto` como parâmetro,
que pode ter tipos diferentes de `self` `Ponto` dos quais usamos no `mistura`.
O método cria uma nova instância de `Ponto` que possui o valor `x` de `self`
`Point` (que é um tipo de `T`) e o valor de `y` passado de `Ponto` (que é do 
tipo `W`):

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>


```rust
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T, U> Point<T, U> {
    fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c'};

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

```rust
struct Ponto<T, U> {
    x: T,
    y: U,
}

impl<T, U> Ponto<T, U> {
    fn mistura<V, W>(self, other: Ponto<V, W>) -> Ponto<T, W> {
        Ponto {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Ponto { x: 5, y: 10.4 };
    let p2 = Ponto { x: "Ola", y: 'c'};

    let p3 = p1.mistura(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

<span class="caption">Listing 10-11: Methods that use different generic types
than their struct’s definition</span>

<span class="caption">Listagem 10-11: Métodos que usam diferentes tipos 
genéricos das suas definições de struct</span>

In `main`, we’ve defined a `Point` that has an `i32` for `x` (with value `5`)
and an `f64` for `y` (with value `10.4`). `p2` is a `Point` that has a string
slice for `x` (with value `"Hello"`) and a `char` for `y` (with value `c`).
Calling `mixup` on `p1` with the argument `p2` gives us `p3`, which will have
an `i32` for `x`, since `x` came from `p1`. `p3` will have a `char` for `y`,
since `y` came from `p2`. The `println!` will print `p3.x = 5, p3.y = c`.

No `main`, nós definimos um `Ponto` que tem um `i32` para o `x` (com o valor de
 `5`) e um `f64` para `y` (com o valor de `10.4`). `p2` é um `Ponto` que tem um
pedaço de string `x` (com o valor `"Ola"`) e um `char` para `y` (com o valor
`c`). Chamando `mistura` no `p1` com o argumento `p2` nos dá `p3`, que terá um
`i32` para `x`, já que `x` veio de `p1`. `p3` terá um `char` para `y`, já que 
`y` veio de `p2`. O `println!` irá imprimir `p3.x = 5, p3.y = c`.

Note that the generic parameters `T` and `U` are declared after `impl`, since
they go with the struct definition. The generic parameters `V` and `W` are
declared after `fn mixup`, since they are only relevant to the method.

Note que os parâmetro genéricos `T` e `U` são declarados depois de `impl`, já
que eles vão com a definição do struct. Os parâmetros genéricos `V` e `Ẁ` são
declarados depois de `fn mistura`, já que elés só são relevantes para esse 
método.

### Performance of Code Using Generics

### Desempenho do Código Usando Genéricos

You may have been reading this section and wondering if there’s a run-time cost
to using generic type parameters. Good news: the way that Rust has implemented
generics means that your code will not run any slower than if you had specified
concrete types instead of generic type parameters!

Você pode estar lendo essa seção e imaginando se há um custo no tempo de 
execução para usar parâmetros de tipos genéricos. Boas notícias: o modo como
Rust implementa tipos genéricos significa que seu código não vai ser executado
mais devagar do que se você tivesse especificado tipos concretos ao invés de 
tipos genéricos como parâmetros!

Rust accomplishes this by performing *monomorphization* of code using generics
at compile time. Monomorphization is the process of turning generic code into
specific code with the concrete types that are actually used filled in.

Rust consegue fazer isso realizando *monomorfização* de código usando tipos 
genéricos em tempo de compilação. Monomorfização é o processo de transformar
código genérico em código específico substituindo os tipos genéricos pelos 
tipos concretos que são realmente utilizados.

What the compiler does is the opposite of the steps that we performed to create
the generic function in Listing 10-5. The compiler looks at all the places that
generic code is called and generates code for the concrete types that the
generic code is called with.

O que o compilador faz é o oposto dos passos que fizemos para criar uma função
de tipo genérico na Listagem 10-5. O compilador olhar para todos os lugares que
o código genérico é chamado e gera o código para os tipos concretos que o
código genérico é chamado.

Let’s work through an example that uses the standard library’s `Option` enum:

Vamos trabalhar sobre o exemplo que usa o padrão de enum `Option` da 
biblioteca:

```rust
let integer = Some(5);
let float = Some(5.0);
```

```rust
let inteiro = Some(5);
let float = Some(5.0);
```


When Rust compiles this code, it will perform monomorphization. The compiler
will read the values that have been passed to `Option` and see that we have two
kinds of `Option<T>`: one is `i32`, and one is `f64`. As such, it will expand
the generic definition of `Option<T>` into `Option_i32` and `Option_f64`,
thereby replacing the generic definition with the specific ones.

Quando o Rust compilar esse código, ele vai fazer a monomorfização. O 
compilador lerá os valores que foram passados para `Option` e ver que temos
dois tipos de `Option<T>`: um é `i32`, e o outro `f64`. Assim sendo, ele 
expandirá a definição genérica de `Option<T>` para `Option_i32` e `Option_64`,
substituindo a definição genérica por definições específicas.

The monomorphized version of our code that the compiler generates looks like
this, with the uses of the generic `Option` replaced with the specific
definitions created by the compiler:

A versão monomorfizada do nosso código que o compilador gera é a seguinte, com
os usos da `Option` genérica substituídos pelas definições específicas criadas 
pelo compilador:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```


```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let inteiro = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

We can write the non-duplicated code using generics, and Rust will compile that
into code that specifies the type in each instance. That means we pay no
runtime cost for using generics; when the code runs, it performs just like it
would if we had duplicated each particular definition by hand. The process of
monomorphization is what makes Rust’s generics extremely efficient at runtime.

Nós podemos escrever códigos não duplicados usando tipos genéricos, e Rust vai
compila-lo em código que especifica o tipo em cada instância. Isso significa 
que não pagamos nenhum custo em tempo de processamento para usar tipos 
genéricos; quando o código roda, ele executa do mesmo modo como executaria se
tivéssemos duplicado cada definição particular a mão. O proccesso de 
monomorfização é o que faz os tipos genéricos de Rust serem extremamente 
eficientes em tempo de processamento.
