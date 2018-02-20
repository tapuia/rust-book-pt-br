## `Box<T>` Points to Data on the Heap and Has a Known Size

## `Box<T>` Aponta para Dados no Heap e Tem Tamanho Conhecido

The most straightforward smart pointer is a *box*, whose type is written
`Box<T>`. Boxes allow you to store data on the heap rather than the stack. What
remains on the stack is the pointer to the heap data. Refer to Chapter 4 to
review the difference between the stack and the heap.

O ponteiro inteligente mais simples é um *box* (literalmente, "caixa"), cujo
tipo é escrito `Box<T>`. *Boxes* (plural de *box*) lhe permitem armazenar dados
no heap em vez de na pilha. O que fica na pilha é o ponteiro para o dado no
heap. Confira o Capítulo 4 para rever a diferença entre pilha e heap.

Boxes don’t have performance overhead, other than storing their data on the heap
instead of on the stack. But they don’t have many extra capabilities either.
You’ll use them most often in these situations:

Boxes não têm custo adicional de desempenho além de armazenar dados no heap em
vez de na pilha. Mas eles também não têm muitas habilidades a mais. Você irá
usá-los mais comumente nestas situações:

* When you have a type whose size can’t be known at compile time, and you want
  to use a value of that type in a context that needs to know an exact size
* When you have a large amount of data and you want to transfer ownership but
  ensure the data won’t be copied when you do so
* When you want to own a value and only care that it’s a type that implements a
  particular trait rather than knowing the concrete type

* Quando você tem um tipo cujo tamanho não é possível saber em tempo de
  compilação, e você quer usar um valor desse tipo em um contexto que precisa
  saber um tamanho exato;
* Quando você tem uma quantidade grande de dados e você quer transferir a posse
  mas garantir que os dados não serão copiados quando você o fizer;
* Quando você quer possuir um valor e só se importa se é um tipo que implementa
  uma trait específica, em vez de saber o tipo concreto.

We’ll demonstrate the first situation in this section. But before we do so,
we’ll elaborate on the other two situations a bit more: in the second case,
transferring ownership of a large amount of data can take a long time because
the data is copied around on the stack. To improve performance in this
situation, we can store the large amount of data on the heap in a box. Then,
only the small amount of pointer data is copied around on the stack, and the
data stays in one place on the heap. The third case is known as a *trait
object*, and Chapter 17 devotes an entire section just to that topic. So what
you learn here you’ll apply again in Chapter 17!

Vamos demonstrar a primeira situação nesta seção. Mas antes disso, vamos falar
um pouco mais sobre as outras duas situações: no segundo caso, transferir posse
de uma quantidade grande de dados pode levar muito tempo porque os dados são
copiados de um lado para o outro na pilha. Para melhorar o desempenho nessa
situação, podemos armazenar essa quantidade grande de dados no heap em um box.
Assim, apenas uma quantidade pequena de dados referentes ao ponteiro é copiada
na pilha, e os dados em si ficam em um lugar só no heap. O terceiro caso é
conhecido como um *objeto de trait* (*trait object*), e o Capítulo 17 dedica uma
seção inteira somente a esse tópico. Então o que você aprender aqui você irá
aplicar de novo no Capítulo 17!

### Using a `Box<T>` to Store Data on the Heap

### Usando um `Box<T>` para Armazenar Dados no Heap

Before we discuss this use case for `Box<T>`, we’ll cover the syntax and how to
interact with values stored within a `Box<T>`.

Antes de discutirmos esse caso de uso para o `Box<T>`, vamos cobrir a sintaxe e
como interagir com valores armazenados dentro de um `Box<T>`.

Listing 15-1 shows how to use a box to store an `i32` value on the heap:

A Listagem 15-1 mostra como usar um box para armazenar um valor `i32` no heap:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
}
```

```rust
fn main() {
    let b = Box::new(5);
    println!("b = {}", b);
}
```

<span class="caption">Listing 15-1: Storing an `i32` value on the heap using a
box</span>

<span class="caption">Listagem 15-1: Armazenando um valor `i32` no heap usando
um box</span>

We define the variable `b` to have the value of a `Box` that points to the value
`5`, which is allocated on the heap. This program will print `b = 5`; in this
case, we can access the data in the box in a similar way as we would if this
data was on the stack. Just like any owned value, when a box goes out of scope
like `b` does at the end of `main`, it will be deallocated. The deallocation
happens for the box (stored on the stack) and the data it points to (stored on
the heap).

Nós definimos a variável `b` como tendo o valor de um `Box` que aponta para o
valor `5`, que está alocado no heap. Esse programa irá imprimir `b = 5`; nesse
caso, podemos acessar o dado no box de um jeito similar ao que usaríamos se esse
dado estivesse na pilha. Da mesma forma que com qualquer valor possuído, quando
um box sai de escopo, como o `b` no fim da `main`, ele é desalocado. A
desalocação acontece para o box (armazenado na pilha) e para os dados aos quais
ele aponta (armazenados no heap).

Putting a single value on the heap isn’t very useful, so you won’t use boxes by
themselves in this way very often. Having values like a single `i32` on the
stack, where they’re stored by default, is more appropriate in the majority of
situations. Let’s look at a case where boxes allow us to define types that we
wouldn’t be allowed to if we didn’t have boxes.

Colocar um único valor no heap não é muito útil, então você normalmente não vai
usar boxes sozinhos desse jeito. Ter valores como um único `i32` na pilha, onde
são armazenados por padrão, é mais apropriado para a maioria das situações.
Vamos dar uma olhada em um caso onde o box nos possibilita definir tipos que não
poderíamos definir sem ele.

### Boxes Enable Recursive Types

### Boxes Possibilitam Tipos Recursivos

At compile time, Rust needs to know how much space a type takes up. One type
whose size can’t be known at compile time is a *recursive type*, where a value
can have as part of itself another value of the same type. Because this nesting
of values could theoretically continue infinitely, Rust doesn’t know how much
space a value of a recursive type needs. However, boxes have a known size, so by
inserting a box in a recursive type definition, we can have recursive types.

Em tempo de compilação, o Rust precisa saber quanto espaço um tipo ocupa. Um
*tipo recursivo* (*recursive type*), onde um valor pode ter como parte de si
mesmo outro valor do mesmo tipo, é um tipo cujo tamanho não se pode saber em
tempo de compilação. Como esse aninhamento de valores poderia em teoria
continuar infinitamente, o Rust não sabe quanto espaço um valor de um tipo
recursivo precisa. Porém, boxes têm um tamanho conhecido, então podemos ter
tipos recursivos inserindo um box em sua definição.

Let’s explore the *cons list*, which is a data type common in functional
programming languages, as an example of a recursive type. The cons list type
we’ll define is straightforward except for the recursion; therefore, the
concepts in the example we’ll work with will be useful any time you get into
more complex situations involving recursive types.

Vamos explorar a *lista ligada* (*cons list*), que é um tipo de dados comum em
linguagens de programação funcional, como um exemplo de tipo recursivo. O tipo
para lista ligada que vamos definir é bem básico exceto pela recursão; portanto,
os conceitos no exemplo que vamos trabalhar vão ser úteis sempre que você se
encontrar em situações mais complexas envolvendo tipos recursivos.

#### More Information About the Cons List

#### Mais Informações sobre a Cons List

A *cons list* is a data structure that comes from the Lisp programming language
and its dialects. In Lisp, the `cons` function (short for “construct function”)
constructs a new pair from its two arguments, which usually are a single value
and another pair. These pairs containing pairs form a list.

A *cons list* é uma estrutura de dados que vem da linguagem de programação Lisp
e seus dialetos. Em Lisp, a função `cons` (abreviação de "construction
function", função de construção) constrói um novo par a partir de seus dois
argumentos, que geralmente são um valor único e um outro par. Esses pares
contendo pares formam uma lista.

The cons function concept has made its way into more general functional
programming jargon: “to cons x onto y” informally means to construct a new
container instance by putting the element x at the start of this new container,
followed by the container y.

O conceito da função cons acabou se tornando parte do jargão mais geral de
programação funcional: "to cons x onto y" ("consar" x em y, grosso modo) em
inglês informalmente significa construir uma nova instância de um par, colocando
o elemento x no começo desse novo par, seguido pelo par y.

Each item in a cons list contains two elements: the value of the current item
and the next item. The last item in the list contains only a value called `Nil`
without a next item. A cons list is produced by recursively calling the `cons`
function. The canonical name to denote the base case of the recursion is `Nil`.
Note that this is not the same as the “null” or “nil” concept in Chapter 6,
which is an invalid or absent value.

Cada item em uma cons list contém dois elementos: o valor do item atual e o
próximo item. O último item na lista contém apenas um valor chamado de `Nil`,
sem um próximo item. Uma cons list é produzida chamando-se recursivamente a
função `cons`. O nome canônico que denota o caso base da recursão é `Nil`. Note
que isso não é o mesmo que o conceito de "null" ou "nil" visto no Capítulo 6,
que é um valor inválido ou ausente.

Although functional programming languages use cons lists frequently, it isn’t a
commonly used data structure in Rust. Most of the time when you have a list of
items in Rust, `Vec<T>` is a better choice to use. Other, more complex recursive
data types *are* useful in various situations, but by starting with the cons
list, we can explore how boxes let us define a recursive data type without much
distraction.

Apesar de linguagens de programação funcionais usarem cons lists frequentemente,
essa não é uma estrutura de dados muito usada em Rust. Na maioria das vezes em
que você tem uma lista de itens em Rust, `Vec<T>` é uma escolha melhor. Outros
tipos recursivos *são* úteis em diversas situações. Mas começando com a cons
list, podemos explorar como boxes nos permitem definir um tipo recursivo sem
muita distração.

Listing 15-2 contains an enum definition for a cons list. Note that this code
won’t compile yet because the `List` type doesn’t have a known size, which we’ll
demonstrate:

A Listagem 15-2 contém uma definição de um enum para a cons list. Note que este
código não compila ainda porque o tipo `List` não tem um tamanho conhecido, como
demonstraremos:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
enum List {
    Cons(i32, List),
    Nil,
}
```

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
enum List {
    Cons(i32, List),
    Nil,
}
```

<span class="caption">Listing 15-2: The first attempt at defining an enum to
represent a cons list data structure of `i32` values</span>

<span class="caption">Listagem 15-2: A primeira tentativa de definir um enum
para representar uma estrutura de dados *cons list* de valores `i32` </span>

> Note: We’re implementing a cons list that only holds `i32` values for the
> purposes of this example. We could have implemented it using generics, as we
> discussed in Chapter 10, to define a cons list type that could store values of
> any type.

> Nota: estamos implementando uma cons list que guarda apenas valores `i32` para
> os propósitos deste exemplo. Poderíamos tê-la implementado usando tipos
> genéricos, conforme discutimos no Capítulo 10, para definir um tipo cons list
> que poderia armazenar valores de qualquer tipo.

Using the `List` type to store the list `1, 2, 3` would look like the code in
Listing 15-3:

A listagem 15-3 mostra como fica o uso do tipo `List` para armazenar a lista `1,
2, 3`.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use List::{Cons, Nil};

fn main() {
    let list = Cons(1, Cons(2, Cons(3, Nil)));
}
```

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
use List::{Cons, Nil};

fn main() {
    let list = Cons(1, Cons(2, Cons(3, Nil)));
}
```

<span class="caption">Listing 15-3: Using the `List` enum to store the list `1,
2, 3`</span>

<span class="caption">Listagem 15-3: Usando o enum `List` para armazenar a lista
`1, 2, 3`</span>

The first `Cons` value holds `1` and another `List` value. This `List` value is
another `Cons` value that holds `2` and another `List` value. This `List` value
is one more `Cons` value that holds `3` and a `List` value, which is finally
`Nil`, the non-recursive variant that signals the end of the list.

O primeiro valor `Cons` contém `1` e outro valor `List`. Esse valor `List` é
outro `Cons` que contém `2` e outro valor `List`. Esse valor `List` é mais um
`Cons` que contém 3 e um valor `List`, que finalmente é `Nil`, a variante não
recursiva que sinaliza o final da lista.

If we try to compile the code in Listing 15-3, we get the error shown in Listing
15-4:

Se tentarmos compilar o código na listagem 15-3, receberemos o erro mostrado na
listagem 15-4:

```text
error[E0072]: recursive type `List` has infinite size
 --> src/main.rs:1:1
  |
1 | enum List {
  | ^^^^^^^^^ recursive type has infinite size
2 |     Cons(i32, List),
  |               ----- recursive without indirection
  |
  = help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to
  make `List` representable
```

```text
erro[E0072]: tipo recursivo `List` tem tamanho infinito
 --> src/main.rs:1:1
  |
1 | enum List {
  | ^^^^^^^^^ tipo recursivo tem tamanho infinito
2 |     Cons(i32, List),
  |               ----- recursivo sem indireção
  |
  = ajuda: insira indireção (ex.: um `Box`, `Rc` ou `&`) em algum lugar para
  tornar `List` representável
```

<span class="caption">Listing 15-4: The error we get when attempting to define a
recursive enum</span>

<span class="caption">Listagem 15-4: O erro que recebemos quando tentamos
definir um enum recursivo</span>

The error shows this type “has infinite size.” The reason is that we’ve defined
`List` with a variant that is recursive: it holds another value of itself
directly. As a result, Rust can’t figure out how much space it needs to store a
`List` value. Let’s break down why we get this error a bit: first, let’s look at
how Rust decides how much space it needs to store a value of a non-recursive
type.

O erro diz que esse tipo "tem tamanho infinito". A razão é que nós definimos
`List` com uma variante que é recursiva: ela contém um outro valor de si mesma
diretamente. Como resultado, o Rust não consegue determinar quanto espaço ele
precisa para armazenar um valor `List`. Vamos analizar por partes por que
recebemos esse erro: primeiro, vamos ver como o Rust decide quanto espaço
precisa para armazenar o valor de um tipo *não* recursivo.

#### Computing the Size of a Non-Recursive Type

#### Computando o Tamanho de um Tipo Não Recursivo

Recall the `Message` enum we defined in Listing 6-2 when we discussed enum
definitions in Chapter 6:

Recorde o enum `Mensagem` que definimos na Listagem 6-2 quando discutimos
definições de enums no Capítulo 6:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

```rust
enum Mensagem {
    Sair,
    Mover { x: i32, y: i32 },
    Escrever(String),
    MudarCor(i32, i32, i32),
}
```

To determine how much space to allocate for a `Message` value, Rust goes through
each of the variants to see which variant needs the most space. Rust sees that
`Message::Quit` doesn’t need any space, `Message::Move` needs enough space to
store two `i32` values, and so forth. Because only one variant will be used, the
most space a `Message` value will need is the space it would take to store the
largest of its variants.

Para determinar quanto espaço alocar para um valor `Mensagem`, o Rust percorre
cada variante para ver qual precisa de mais espaço. O Rust vê que
`Mensagem::Sair` não precisa de nenhum espaço, `Mensagem::Mover` precisa de
espaço suficiente para armazenar dois valores `i32`, e assim por diante. Como
apenas uma variante será usada, o máximo de espaço de que um valor `Mensagem`
vai precisar é o espaço que levaria para armazenar a maior de suas variantes.

Contrast this to what happens when Rust tries to determine how much space a
recursive type like the `List` enum in Listing 15-2 needs. The compiler starts
by looking at the `Cons` variant, which holds a value of type `i32` and a value
of type `List`. Therefore, `Cons` needs an amount of space equal to the size of
an `i32` plus the size of a `List`. To figure out how much memory the `List`
type needs, the compiler looks at the variants, starting with the `Cons`
variant. The `Cons` variant holds a value of type `i32` and a value of type
`List`, and this process continues infinitely, as shown in Figure 15-1:

Contraste isso com o que acontece quando o Rust tenta determinar quanto espaço é
necessário para um tipo recursivo como o enum `List` na Listagem 15-2. O
compilador começa olhando a variante `Cons`, que contém um valor do tipo `i32` e
um valor do tipo `List`. Portanto, `Cons` precisa de uma quantidade de espaço
igual ao tamanho de um `i32` mais o tamanho de um `List`. Para determinar de
quanta memória o tipo `List` precisa, o compilador olha para suas variantes,
começando com a `Cons`. A variante `Cons` contém um valor do tipo `i32` e um
valor do tipo `List`, e esse processo continua infinitamente, conforme mostra a
Figura 15-1:

<img alt="An infinite Cons list" src="img/trpl15-01.svg" class="center"
style="width: 50%;" />

<img alt="Uma cons list infinita" src="img/trpl15-01.svg" class="center"
style="width: 50%;" />

<span class="caption">Figure 15-1: An infinite `List` consisting of infinite
`Cons` variants</span>

<span class="caption">Figura 15-1: Uma `List` infinita feita de infinitas
variantes `Cons`</span>

#### Using `Box<T>` to Get a Recursive Type with a Known Size

#### Usando `Box<T>` para Conseguir um Tipo Recursivo de Tamanho Conhecido

Rust can’t figure out how much space to allocate for recursively defined types,
so the compiler gives the error in Listing 15-4. But the error does include this
helpful suggestion:

Como o Rust não consegue descobrir quanto espaço alocar para tipos definidos
recursivamente, o compilador dá o erro na Listagem 15-4. Mas o erro inclui esta
útil sugestão:

```text
  = help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to
  make `List` representable
```

```text
  = ajuda: insira indireção (ex.: um `Box`, `Rc` ou `&`) em algum lugar para
  tornar `List` representável
```

In this suggestion, “indirection” means that instead of storing a value
directly, we’ll change the data structure to store the value indirectly by
storing a pointer to the value instead.

Nessa sugestão, "indireção" significa que, em vez de armazenar um valor
diretamente, devemos mudar a estrutura de dados para armazenar um ponteiro para
o valor.

Because a `Box<T>` is a pointer, Rust always knows how much space a `Box<T>`
needs: a pointer’s size doesn’t change based on the amount of data it’s pointing
to. This means we can put a `Box<T>` inside the `Cons` variant instead of
another `List` value directly. The `Box<T>` will point to the next `List` value
that will be on the heap rather than inside the `Cons` variant. Conceptually, we
still have a list, created with lists “holding” other lists, but this
implementation is now more like the items being next to one another rather than
inside one another.

Como um `Box<T>` é um ponteiro, o Rust sempre sabe de quanto espaço ele precisa:
o tamanho de um ponteiro não muda dependendo da quantidade de dados para a qual
ele aponta. Isso significa que podemos colocar um `Box<T>` dentro da variante
`Cons` em vez de outro valor `List` diretamente. O `Box<T>` vai apontar para o
próximo valor `List`, que vai estar no heap em vez de dentro da variante `Cons`.
Conceitualmente, ainda temos uma lista, criada de listas "contendo" outras
listas, mas essa implementação agora é mais como os itens estando um do lado do
outro do que um dentro do outro.

We can change the definition of the `List` enum in Listing 15-2 and the usage of
the `List` in Listing 15-3 to the code in Listing 15-5, which will compile:

Podemos mudar a definição do enum `List` na Listagem 15-2 e o uso de `List` na
Listagem 15-3 para o código na Listagem 15-5, que compila:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};

fn main() {
    let list = Cons(1,
        Box::new(Cons(2,
            Box::new(Cons(3,
                Box::new(Nil))))));
}
```

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};

fn main() {
    let list = Cons(1,
        Box::new(Cons(2,
            Box::new(Cons(3,
                Box::new(Nil))))));
}
```

<span class="caption">Listing 15-5: Definition of `List` that uses `Box<T>` in
order to have a known size</span>

<span class="caption">Listagem 15-5: Definição de `List` que usa `Box<T>` para
ter um tamanho conhecido</span>

The `Cons` variant will need the size of an `i32` plus the space to store the
box’s pointer data. The `Nil` variant stores no values, so it needs less space
than the `Cons` variant. We now know that any `List` value will take up the size
of an `i32` plus the size of a box’s pointer data. By using a box, we’ve broken
the infinite, recursive chain, so the compiler can figure out the size it needs
to store a `List` value. Figure 15-2 shows what the `Cons` variant looks like
now:

A variante `Cons` vai precisar do tamanho de um `i32` mais o espaço para
armazenar os dados do ponteiro box. A variante `Nil` não armazena nenhum valor,
então ela precisa de menos espaço que a variante `Cons`. Agora sabemos que
qualquer valor `List` irá ocupar o tamanho de um `i32` mais o tamanho dos dados
de um ponteiro box. Usando um box, nós quebramos a cadeia recursiva, infinita,
para que o compilador pudesse determinar o espaço que ele precisa para
armarzenar um valor `List`. A Figura 15-2 mostra como ficou a variante `Cons`
agora:

<img alt="A finite Cons list" src="img/trpl15-02.svg" class="center" />

<img alt="Uma lista de Cons infinita" src="img/trpl15-02.svg" class="center" />

<span class="caption">Figure 15-2: A `List` that is not infinitely sized because
`Cons` holds a `Box`</span>

<span class="caption">Figura 15-2: Um `List` que não tem tamanho infinito porque
`Cons` contém um `Box`</span>

Boxes only provide the indirection and heap allocation; they don’t have any
other special capabilities, like those we’ll see with the other smart pointer
types. They also don’t have any performance overhead that these special
capabilities incur, so they can be useful in cases like the cons list where the
indirection is the only feature we need. We’ll look at more use cases for boxes
in Chapter 17, too.

Boxes apenas proveem a indireção e a alocação no heap; eles não têm nenhuma
outra habilidade especial, como as que vamos ver nos outros tipos de ponteiros
inteligentes. Eles também não têm nenhum dos custos adicionais de desempenho que
essas habilidades demandam, então eles podem ser úteis em casos como o da cons
list onde a indireção é a única funcionalidade de que precisamos. No Capítulo
17 também vamos ver mais casos de uso para as boxes.

The `Box<T>` type is a smart pointer because it implements the `Deref` trait,
which allows `Box<T>` values to be treated like references. When a `Box<T>`
value goes out of scope, the heap data that the box is pointing to is cleaned up
as well because of the `Drop` trait implementation. Let’s explore these two
traits in more detail. These two traits will be even more important to the
functionality provided by the other smart pointer types we’ll discuss in the
rest of this chapter.

O tipo `Box<T>` é um ponteiro inteligente porque ele implementa a trait `Deref`,
o que permite que valores `Box<T>` sejam usados como referências. Quando um
valor `Box<T>` sai de escopo, os dados do heap para os quais o box aponta também
são liberados porque o tipo implementa a trait `Drop`. Vamos explorar essas duas
traits em mais detalhe. Elas serão ainda mais importantes para a funcionalidade
provida pelos outros ponteiros inteligentes que vamos discutir no resto deste
capítulo.
