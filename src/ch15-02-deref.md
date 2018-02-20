## Treating Smart Pointers Like Regular References with the `Deref` Trait

## Tratando Ponteiros Inteligentes como Referências Normais com a Trait `Deref`

Implementing the `Deref` trait allows us to customize the behavior of the
*dereference operator*, `*` (as opposed to the multiplication or glob
operator). By implementing `Deref` in such a way that a smart pointer can be
treated like a regular reference, we can write code that operates on references
and use that code with smart pointers too.

Implementar a trait `Deref` nos permite personalizar o comportamento do
*operador de desreferência* (*dereference operator*), `*` (que é diferente do
operador de multiplicação ou de glob). Implementando a `Deref` de tal modo que o
ponteiro inteligente possa ser tratado como uma referência normal, podemos
escrever código que opere sobre referências e usar esse código com ponteiros
inteligentes também.

Let’s first look at how `*` works with regular references, and then try to
define our own type like `Box<T>` and see why `*` doesn’t work like a reference
on our newly defined type. We’ll explore how implementing the `Deref` trait
makes it possible for smart pointers to work in a similar way as references.
Then we’ll look at Rust’s *deref coercion* feature and how it lets us work with
either references or smart pointers.

Primeiro vamos ver como o `*` funciona com referências normais, e então vamos
tentar definir nosso próprio tipo a la `Box<T>` e ver por que o `*` não funciona
como uma referência no nosso tipo recém-criado. Vamos explorar como a trait
`Deref` torna possível aos ponteiros inteligentes funcionarem de um jeito
similar a referências. E então iremos dar uma olhada na funcionalidade de
*coerção de desreferência* (*deref coercion*) e como ela nos permite trabalhar
tanto com referências quanto com ponteiros inteligentes.

### Following the Pointer to the Value with `*`

### Seguindo o Ponteiro até o Valor com `*`

A regular reference is a type of pointer, and one way to think of a pointer is
as an arrow to a value stored somewhere else. In Listing 15-6, we create a
reference to an `i32` value and then use the dereference operator to follow the
reference to the data:

Uma referência normal é um tipo de ponteiro, e um jeito de pensar sobre um
ponteiro é como uma seta até um valor armazenado em outro lugar. Na Listagem
15-6, nós criamos uma referência a um valor `i32` e em seguida usamos o operador
de desreferência para seguir a referência até o dado:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

```rust
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

<span class="caption">Listing 15-6: Using the dereference operator to follow a
reference to an `i32` value</span>

<span class="caption">Listagem 15-6: Usando o operador de desreferência para
seguir uma referência a um valor `i32`</span>

The variable `x` holds an `i32` value, `5`. We set `y` equal to a reference to
`x`. We can assert that `x` is equal to `5`. However, if we want to make an
assertion about the value in `y`, we have to use `*y` to follow the reference
to the value it’s pointing to (hence *dereference*). Once we dereference `y`,
we have access to the integer value `y` is pointing to that we can compare with
`5`.

A variável `x` contém um valor `i32`, `5`. Nós setamos `y` igual a uma
referência a `x`. Podemos conferir (coloquialmente, "assertar") que `x` é igual
a `5`. Contudo, se queremos fazer uma asserção sobre o valor em `y`, temos que
usar `*y` para seguir a referência até o valor ao qual `y` aponta (por isso
"desreferência"). Uma vez que desreferenciamos `y`, temos acesso ao valor
inteiro ao qual `y` aponta para podermos compará-lo com `5`.

If we tried to write `assert_eq!(5, y);` instead, we would get this compilation
error:

Se em vez disso tentássemos escrever `assert_eq!(5, y);`, receberíamos este erro
de compilação:

```text
error[E0277]: the trait bound `{integer}: std::cmp::PartialEq<&{integer}>` is
not satisfied
 --> src/main.rs:6:5
  |
6 |     assert_eq!(5, y);
  |     ^^^^^^^^^^^^^^^^^ can't compare `{integer}` with `&{integer}`
  |
  = help: the trait `std::cmp::PartialEq<&{integer}>` is not implemented for
  `{integer}`
```

```text
erro[E0277]: a trait bound `{integer}: std::cmp::PartialEq<&{integer}>` não foi
satisfeita
 --> src/main.rs:6:5
  |
6 |     assert_eq!(5, y);
  |     ^^^^^^^^^^^^^^^^^ não posso comparar `{integer}` com `&{integer}`
  |
  = ajuda: a trait `std::cmp::PartialEq<&{integer}>` não está implementada para
  `{integer}`
```

Comparing a number and a reference to a number isn’t allowed because they’re
different types. We must use `*` to follow the reference to the value it’s
pointing to.

Comparar um número com uma referência a um número não é permitido porque eles
são de tipos diferentes. Devemos usar `*` para seguir a referência até o valor
ao qual ela está apontando.

### Using `Box<T>` Like a Reference

### Usando `Box<T>` como uma Referência

We can rewrite the code in Listing 15-6 to use a `Box<T>` instead of a
reference, and the dereference operator will work the same way as shown in
Listing 15-7:

Podemos reescrever o código na Listagem 15-6 para usar um `Box<T>` em vez de uma
referência, e o operador de desreferência vai funcionar do mesmo jeito que na
Listagem 15-7:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

```rust
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

<span class="caption">Listing 15-7: Using the dereference operator on a
`Box<i32>`</span>

<span class="caption">Listagem 15-7: Usando o operador de desreferência em um
`Box<i32>`</span>

The only difference between Listing 15-7 and Listing 15-6 is that here we set
`y` to be an instance of a box pointing to the value in `x` rather than a
reference pointing to the value of `x`. In the last assertion, we can use the
dereference operator to follow the box’s pointer in the same way that we did
when `y` was a reference. Next, we’ll explore what is special about `Box<T>`
that enables us to use the dereference operator by defining our own box type.

A única diferença entre a Listagem 15-7 e a Listagem 15-6 é que aqui nós setamos
`y` para ser uma instância de um box apontando para o valor em `x` em vez de uma
referência apontando para o valor de `x`. Na última asserção, podemos usar o
operador de desreferência para seguir o ponteiro do box do mesmo jeito que
fizemos quando `y` era uma referência. A seguir, vamos explorar o que tem de
especial no `Box<T>` que nos permite usar o operador de desreferência, criando
nosso próprio tipo box.

### Defining Our Own Smart Pointer

### Definindo Nosso Próprio Ponteiro Inteligente

Let’s build a smart pointer similar to the `Box<T>` type provided by the
standard library to experience how smart pointers behave differently to
references by default. Then we’ll look at how to add the ability to use the
dereference operator.

Vamos construir um smart pointer parecido com o tipo `Box<T>` fornecido pela
biblioteca padrão para vermos como ponteiros inteligentes, por padrão, se
comportam diferente de referências. Em seguida, veremos como adicionar a
habilidade de usar o operador de desreferência.

The `Box<T>` type is ultimately defined as a tuple struct with one element, so
Listing 15-8 defines a `MyBox<T>` type in the same way. We’ll also define a
`new` function to match the `new` function defined on `Box<T>`:

O tipo `Box<T>` no fim das contas é definido como uma struct-tupla (*tuple
struct*) de um elemento, então a Listagem 15-8 define um tipo `MeuBox<T>` da
mesma forma. Também vamos definir uma função `new` como a definida no `Box<T>`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}
```

```rust
struct MeuBox<T>(T);

impl<T> MeuBox<T> {
    fn new(x: T) -> MeuBox<T> {
        MeuBox(x)
    }
}
```

<span class="caption">Listing 15-8: Defining a `MyBox<T>` type</span>

<span class="caption">Listagem 15-8: Definindo um tipo `MeuBox<T>`</span>

We define a struct named `MyBox` and declare a generic parameter `T`, because
we want our type to hold values of any type. The `MyBox` type is a tuple struct
with one element of type `T`. The `MyBox::new` function takes one parameter of
type `T` and returns a `MyBox` instance that holds the value passed in.

Definimos um struct chamado `MeuBox` e declaramos um parâmetro genérico `T`,
porque queremos que nosso tipo contenha valores de qualquer tipo. O tipo
`MeuBox` é uma struct-tupla de um elemento do tipo `T`. A função `MeuBox::new`
recebe um argumento do tipo `T` e retorna uma instância de `MeuBox` que contém o
valor passado.

Let’s try adding the `main` function in Listing 15-7 to Listing 15-8 and
changing it to use the `MyBox<T>` type we’ve defined instead of `Box<T>`. The
code in Listing 15-9 won’t compile because Rust doesn’t know how to dereference
`MyBox`:

Vamos tentar adicionar a função `main` da Listagem 15-7 à Listagem 15-8 e
alterá-la para usar o tipo `MeuBox<T>` que definimos em vez de `Box<T>`. O
código na Listagem 15-9 não irá compilar porque o Rust não sabe como
desreferenciar `MeuBox`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

```rust,ignore
fn main() {
    let x = 5;
    let y = MeuBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

<span class="caption">Listing 15-9: Attempting to use `MyBox<T>` in the same
way we used references and `Box<T>`</span>

<span class="caption">Listagem 15-9: Tentando usar o `MeuBox<T>` do mesmo jeito
que usamos referências e o `Box<T>`</span>

Here’s the resulting compilation error:

Aqui está o erro de compilação resultante:

```text
error[E0614]: type `MyBox<{integer}>` cannot be dereferenced
  --> src/main.rs:14:19
   |
14 |     assert_eq!(5, *y);
   |                   ^^
```

```text
erro[E0614]: tipo `MeuBox<{integer}>` não pode ser desreferenciado
  --> src/main.rs:14:19
   |
14 |     assert_eq!(5, *y);
   |                   ^^
```

Our `MyBox<T>` type can’t be dereferenced because we haven’t implemented that
ability on our type. To enable dereferencing with the `*` operator, we
implement the `Deref` trait.

Nosso tipo `MeuBox<T>` não pode ser desreferenciado porque não implementamos
essa habilidade nele. Para habilitar desreferenciamento com o operador `*`,
temos que implementar a trait `Deref`.

### Treating a Type Like a Reference by Implementing the `Deref` Trait

### Implementando a Trait `Deref` para Tratar um Tipo como uma Referência

As discussed in Chapter 10, to implement a trait, we need to provide
implementations for the trait’s required methods. The `Deref` trait, provided
by the standard library, requires us to implement one method named `deref` that
borrows `self` and returns a reference to the inner data. Listing 15-10
contains an implementation of `Deref` to add to the definition of `MyBox`:

Conforme discutimos no Capítulo 10, para implementar uma trait, precisamos
prover implementações para os métodos exigidos por ela. A trait `Deref`,
disponibilizada pela biblioteca padrão, requer que implementemos um método
chamado `deref` que pega emprestado `self` e retorna uma referência para os
dados internos. A Listagem 15-10 contém uma implementação de `Deref` que
agrega à definição de `MeuBox`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
use std::ops::Deref;

# struct MyBox<T>(T);
impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}
```

```rust
use std::ops::Deref;

# struct MeuBox<T>(T);
impl<T> Deref for MeuBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}
```

<span class="caption">Listing 15-10: Implementing `Deref` on `MyBox<T>`</span>

<span class="caption">Listagem 15-10: Implementando `Deref` no
`MeuBox<T>`</span>

The `type Target = T;` syntax defines an associated type for the `Deref` trait
to use. Associated types are a slightly different way of declaring a generic
parameter, but you don’t need to worry about them for now; we’ll cover them in
more detail in Chapter 19.

A sintaxe `type Target = T;` define um tipo associado para a trait `Deref` usar.
Tipos associados são um jeito ligeiramente diferente de declarar um parâmetro
genérico, mas você não precisa se preocupar com eles por ora; iremos cobri-los
em mais detalhe no Capítulo 19.

We fill in the body of the `deref` method with `&self.0` so `deref` returns a
reference to the value we want to access with the `*` operator. The `main`
function in Listing 15-9 that calls `*` on the `MyBox<T>` value now compiles
and the assertions pass!

Nós preenchemos o corpo do método `deref` com `&self.0` para que `deref` retorne
uma referência ao valor que queremos acessar com o operador `*`. A função `main`
na Listagem 15-9 que chama `*` no valor `MeuBox<T>` agora compila e as asserções
passam!

Without the `Deref` trait, the compiler can only dereference `&` references.
The `deref` method gives the compiler the ability to take a value of any type
that implements `Deref` and call the `deref` method to get a `&` reference that
it knows how to dereference.

Sem a trait `Deref`, o compilador só consegue desreferenciar referências `&`. O
método `deref` dá ao compilador a habilidade de tomar um valor de qualquer tipo
que implemente `Deref` e chamar o método `deref` para pegar uma referência `&`,
que ele sabe como desreferenciar.

When we entered `*y` in Listing 15-9, behind the scenes Rust actually ran this
code:

Quando entramos `*y` na Listagem 15-9, por trás dos panos o Rust na verdade
rodou este código:

```rust,ignore
*(y.deref())
```

```rust,ignore
*(y.deref())
```

Rust substitutes the `*` operator with a call to the `deref` method and then a
plain dereference so as programmers we don’t have to think about whether or not
we need to call the `deref` method. This Rust feature lets us write code that
functions identically whether we have a regular reference or a type that
implements `Deref`.

O Rust substitui o operador `*` com uma chamada ao método `deref` e em seguida
uma desreferência comum, de modo que nós programadores não precisamos pensar
sobre se temos ou não que chamar o método `deref`. Essa funcionalidade do Rust
nos permite escrever código que funcione identicamente quando temos uma
referência comum ou um tipo que implementa `Deref`.

The reason the `deref` method returns a reference to a value and that the plain
dereference outside the parentheses in `*(y.deref())` is still necessary is due
to the ownership system. If the `deref` method returned the value directly
instead of a reference to the value, the value would be moved out of `self`. We
don’t want to take ownership of the inner value inside `MyBox<T>` in this case
and in most cases where we use the dereference operator.

O fato de o método `deref` retornar uma referência ao valor, e a desreferência
comum fora dos parênteses em `*(y.deref())` ainda ser necessária, é devido ao
sistema de posse (*ownership*). Se o método `deref` retornasse o valor
diretamente em vez de uma referência ao valor, o valor seria movido para fora do
`self`. Nós não queremos tomar posse do valor interno do `MeuBox<T>` neste e na
maioria dos casos em que usamos o operador de desreferência.

Note that the `*` is replaced with a call to the `deref` method and then a call
to `*` just once, each time we type a `*` in our code. Because the substitution
of `*` does not recurse infinitely, we end up with data of type `i32`, which
matches the `5` in `assert_eq!` in Listing 15-9.

Note que o `*` é substituído por uma chamada ao método `deref` e então uma
chamada ao `*` apenas uma vez, cada vez que digitamos um `*` no nosso código.
Como a substituição do `*` não entra em recursão infinita, nós terminamos com o
dado do tipo `i32`, que corresponde ao `5` em `assert_eq!` na Listagem 15-9.

### Implicit Deref Coercions with Functions and Methods

### Coerções de Desreferência Implícitas com Funções e Métodos

*Deref coercion* is a convenience that Rust performs on arguments to functions
and methods. Deref coercion converts a reference to a type that implements
`Deref` into a reference to a type that `Deref` can convert the original type
into. Deref coercion happens automatically when we pass a reference to a
particular type’s value as an argument to a function or method that doesn’t
match the parameter type in the function or method definition. A sequence of
calls to the `deref` method converts the type we provided into the type the
parameter needs.

*Coerção de desreferência* (*deref coercion*) é uma conveniência que o Rust
aplica a argumentos de funções e métodos. A coerção de desreferência converte
uma referência a um tipo que implementa `Deref` em uma referência a um tipo ao
qual a `Deref` pode converter o tipo original. A coerção de desreferência
acontece automaticamente quando passamos uma referência ao valor de um tipo
específico como argumento a uma função ou método e esse tipo não corresponde ao
tipo do parâmetro na definição da função ou método. Uma sequência de chamadas ao
método `deref` converte o tipo que providenciamos no tipo que o parâmetro exige.

Deref coercion was added to Rust so that programmers writing function and
method calls don’t need to add as many explicit references and dereferences
with `&` and `*`. The deref coercion feature also lets us write more code that
can work for either references or smart pointers.

A coerção de desreferência foi adicionada ao Rust para que programadores
escrevendo chamadas a métodos e funções não precisassem adicionar tantas
referências e desreferências explícitas com `&` e `*`. A funcionalidade de
coerção de desreferência também nos permite escrever mais código que funcione
tanto com referências quanto com ponteiros inteligentes.

To see deref coercion in action, let’s use the `MyBox<T>` type we defined in
Listing 15-8 as well as the implementation of `Deref` that we added in Listing
15-10. Listing 15-11 shows the definition of a function that has a string slice
parameter:

Para ver a coerção de desreferência em ação, vamos usar o tipo `MeuBox<T>` que
definimos na Listagem 15-8 e também a implementação de `Deref` que adicionamos
na Listagem 15-10. A Listagem 15-11 mostra a definição de uma função que tem um
parâmetro do tipo string slice:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}
```

```rust
fn ola(nome: &str) {
    println!("Olá, {}!", nome);
}
```

<span class="caption">Listing 15-11: A `hello` function that has the parameter
`name` of type `&str`</span>

<span class="caption">Listagem 15-11: Uma função `ola` que tem um parâmetro
`nome` do tipo `&str`</span>

We can call the `hello` function with a string slice as an argument, such as
`hello("Rust");` for example. Deref coercion makes it possible to call `hello`
with a reference to a value of type `MyBox<String>`, as shown in Listing 15-12:

Podemos chamar a função `ola` passando uma string slice como argumento, por
exemplo `ola("Rust");`. A coerção de desreferência torna possível chamar `ola`
com uma referência a um valor do tipo `MeuBox<String>`, como mostra a Listagem
15-12:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use std::ops::Deref;
#
# struct MyBox<T>(T);
#
# impl<T> MyBox<T> {
#     fn new(x: T) -> MyBox<T> {
#         MyBox(x)
#     }
# }
#
# impl<T> Deref for MyBox<T> {
#     type Target = T;
#
#     fn deref(&self) -> &T {
#         &self.0
#     }
# }
#
# fn hello(name: &str) {
#     println!("Hello, {}!", name);
# }
#
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);
}
```

```rust
# use std::ops::Deref;
#
# struct MeuBox<T>(T);
#
# impl<T> MeuBox<T> {
#     fn new(x: T) -> MeuBox<T> {
#         MeuBox(x)
#     }
# }
#
# impl<T> Deref for MeuBox<T> {
#     type Target = T;
#
#     fn deref(&self) -> &T {
#         &self.0
#     }
# }
#
# fn ola(name: &str) {
#     println!("Olá, {}!", name);
# }
#
fn main() {
    let m = MeuBox::new(String::from("Rust"));
    ola(&m);
}
```

<span class="caption">Listing 15-12: Calling `hello` with a reference to a
`MyBox<String>` value, which works because of deref coercion</span>

<span class="caption">Listagem 15-12: Chamando `ola` com uma referência a um
valor `MeuBox<String>`, o que só funciona por causa da coerção de
desreferência</span>

Here we’re calling the `hello` function with the argument `&m`, which is a
reference to a `MyBox<String>` value. Because we implemented the `Deref` trait
on `MyBox<T>` in Listing 15-10, Rust can turn `&MyBox<String>` into `&String`
by calling `deref`. The standard library provides an implementation of `Deref`
on `String` that returns a string slice, which is in the API documentation for
`Deref`. Rust calls `deref` again to turn the `&String` into `&str`, which
matches the `hello` function’s definition.

Aqui estamos chamando a função `ola` com o argumento `&m`, que é uma referência
a um valor `MeuBox<String>`. Como implementamos a trait `Deref` em `MeuBox<T>`
na Listagem 15-10, o Rust pode transformar `&MeuBox<String>` em `&String`
chamando `deref`. A biblioteca padrão provê uma implementação de `Deref` para
`String` que retorna uma string slice, documentada na API de `Deref`. O Rust
chama `deref` de novo para transformar o `&String` em `&str`, que corresponde à
definição da função `ola`.

If Rust didn’t implement deref coercion, we would have to write the code in
Listing 15-13 instead of the code in Listing 15-12 to call `hello` with a value
of type `&MyBox<String>`:

Se o Rust não implementasse coerção de desreferência, teríamos que escrever o
código na Listagem 15-13 em vez do código na Listagem 15-12 para chamar `ola`
com um valor do tipo `&MeuBox<String>`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use std::ops::Deref;
#
# struct MyBox<T>(T);
#
# impl<T> MyBox<T> {
#     fn new(x: T) -> MyBox<T> {
#         MyBox(x)
#     }
# }
#
# impl<T> Deref for MyBox<T> {
#     type Target = T;
#
#     fn deref(&self) -> &T {
#         &self.0
#     }
# }
#
# fn hello(name: &str) {
#     println!("Hello, {}!", name);
# }
#
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&(*m)[..]);
}
```

```rust
# use std::ops::Deref;
#
# struct MeuBox<T>(T);
#
# impl<T> MeuBox<T> {
#     fn new(x: T) -> MeuBox<T> {
#         MeuBox(x)
#     }
# }
#
# impl<T> Deref for MeuBox<T> {
#     type Target = T;
#
#     fn deref(&self) -> &T {
#         &self.0
#     }
# }
#
# fn ola(name: &str) {
#     println!("Olá, {}!", name);
# }
#
fn main() {
    let m = MeuBox::new(String::from("Rust"));
    ola(&(*m)[..]);
}
```

<span class="caption">Listing 15-13: The code we would have to write if Rust
didn’t have deref coercion</span>

<span class="caption">Listagem 15-13: O código que teríamos que escrever se o
Rust não tivesse coerção de desreferência</span>

The `(*m)` dereferences the `MyBox<String>` into a `String`. Then the `&` and
`[..]` take a string slice of the `String` that is equal to the whole string to
match the signature of `hello`. The code without deref coercions is harder to
read, write, and understand with all of these symbols involved. Deref coercion
allows Rust to handle these conversions for us automatically.

O `(*m)` desreferencia o `MeuBox<String>` em uma `String`. Então o `&` e o
`[..]` obtêm uma string slice da `String` que é igual à string inteira para
corresponder à assinatura de `ola`. O código sem coerção de desreferência é mais
difícil de ler, escrever e entender com todos esses símbolos envolvidos. A
coerção de desreferência permite que o Rust lide com essas conversões
automaticamente para nós.

When the `Deref` trait is defined for the types involved, Rust will analyze the
types and use `Deref::deref` as many times as necessary to get a reference to
match the parameter’s type. The number of times that `Deref::deref` needs to be
inserted is resolved at compile time, so there is no runtime penalty for taking
advantage of deref coercion!

Quando a trait `Deref` está definida para os tipos envolvidos, o Rust analisa os
tipos e usa `Deref::deref` tantas vezes quanto necessário para chegar a uma
referência que corresponda ao tipo do parâmetro. O número de vezes que
`Deref::deref` precisa ser inserida é resolvido em tempo de compilação, então
não existe nenhuma penalidade em tempo de execução para tomar vantagem da
coerção de desreferência.

### How Deref Coercion Interacts with Mutability

### Como a Coerção de Desreferência Interage com a Mutabilidade

Similar to how we use the `Deref` trait to override `*` on immutable
references, Rust provides a `DerefMut` trait for overriding `*` on mutable
references.

De modo semelhante a como usamos a trait `Deref` para redefinir `*` em
referências imutáveis, o Rust provê uma trait `DerefMut` para redefinir `*` em
referências mutáveis.

Rust does deref coercion when it finds types and trait implementations in three
cases:

O Rust faz coerção de desreferência quando ele encontra tipos e implementações
de traits em três casos:

* From `&T` to `&U` when `T: Deref<Target=U>`
* From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
* From `&mut T` to `&U` when `T: Deref<Target=U>`

* De `&T` para `&U` quando `T: Deref<Target=U>`;
* De `&mut T` para `&mut U` quando `T: DerefMut<Target=U>`;
* De `&mut T` para `&U` quando `T: Deref<Target=U>`.

The first two cases are the same except for mutability. The first case states
that if you have a `&T`, and `T` implements `Deref` to some type `U`, you can
get a `&U` transparently. The second case states that the same deref coercion
happens for mutable references.

Os primeiros dois casos são o mesmo exceto pela mutabilidade. O primeiro caso
afirma que se você tem uma `&T`, e `T` implementa `Deref` para algum tipo `U`,
você pode obter um `&U` de maneira transparente. O segundo caso afirma que a
mesma coerção de desreferência acontece para referências mutáveis.

The third case is trickier: Rust will also coerce a mutable reference to an
immutable one. But the reverse is *not* possible: immutable references will
never coerce to mutable references. Because of the borrowing rules, if you have
a mutable reference, that mutable reference must be the only reference to that
data (otherwise, the program wouldn’t compile). Converting one mutable
reference to one immutable reference will never break the borrowing rules.
Converting an immutable reference to a mutable reference would require that
there is only one immutable reference to that data, and the borrowing rules
don’t guarantee that. Therefore, Rust can’t make the assumption that converting
an immutable reference to a mutable reference is possible.

O terceiro caso é mais complicado: o Rust também irá coagir uma referência
mutável a uma imutável. Mas o contrário *não* é possível: referências imutáveis
nunca serão coagidas a referências mutáveis. Por causa das regras de empréstimo,
se você tem uma referência mutável, ela deve ser a única referência àqueles
dados (caso contrário, o programa não compila). Converter uma referência mutável
a uma imutável nunca quebrará as regras de empréstimo. Converter uma referência
imutável a uma mutável exigiria que houvesse apenas uma referência imutável
àqueles dados, e as regras de empréstimo não garantem isso. Portanto, o Rust não
pode assumir que converter uma referência imutável a uma mutável seja possível.
