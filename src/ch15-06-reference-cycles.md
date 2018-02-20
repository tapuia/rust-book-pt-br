## Reference Cycles Can Leak Memory

## Ciclos de Referências Podem Vazar Memória

Rust’s memory safety guarantees make it *difficult* but not impossible to
accidentally create memory that is never cleaned up (known as a *memory leak*).
Preventing memory leaks entirely is not one of Rust’s guarantees in the same
way that disallowing data races at compile time is, meaning memory leaks are
memory safe in Rust. We can see that Rust allows memory leaks by using `Rc<T>`
and `RefCell<T>`: it’s possible to create references where items refer to each
other in a cycle. This creates memory leaks because the reference count of each
item in the cycle will never reach 0, and the values will never be dropped.

As garantias de segurança de memória do Rust tornam *difícil* mas não impossível
acidentalmente criar memória que nunca é liberada (conhecida como um *vazamento
de memória*, ou *memory leak*). O Rust garante em tempo de compilação que não
haverá corridas de dados, mas não garante a prevenção de vazamentos de memória
por completo da mesma forma, o que significa que vazamentos de memória são
*memory safe* em Rust. Podemos ver que o Rust permite vazamentos de memória
usando `Rc<T>` e `RefCell<T>`: é possível criar referências onde os itens se
referem uns aos outros em um ciclo. Isso cria vazamentos de memória porque a
contagem de referências de cada item no ciclo nunca chegará a 0, e os valores
nunca serão destruídos.

### Creating a Reference Cycle

### Criando um Ciclo de Referências

Let’s look at how a reference cycle might happen and how to prevent it,
starting with the definition of the `List` enum and a `tail` method in Listing
15-25:

Vamos dar uma olhada em como um ciclo de referências poderia acontecer e como
preveni-lo, começando com a definição do enum `List` e um método `tail`
(*cauda*) na Listagem 15-25:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

<!-- Hidden fn main is here to disable the automatic wrapping in fn main that
doc tests do; the `use List` fails if this listing is put within a main -->

<!-- A fn main escondida (primeira linha) está aqui para desabilitar o wrapping
automático em uma fn main que os doc tests fazem; o `use List` falha se esta
listagem é colocada dentro de uma main -->

```rust
# fn main() {}
use std::rc::Rc;
use std::cell::RefCell;
use List::{Cons, Nil};

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}

impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match *self {
            Cons(_, ref item) => Some(item),
            Nil => None,
        }
    }
}
```

```rust
# fn main() {}
use std::rc::Rc;
use std::cell::RefCell;
use List::{Cons, Nil};

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}

impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match *self {
            Cons(_, ref item) => Some(item),
            Nil => None,
        }
    }
}
```

<span class="caption">Listing 15-25: A cons list definition that holds a
`RefCell<T>` so we can modify what a `Cons` variant is referring to</span>

<span class="caption">Listagem 15-25: Uma definição de cons list que contém um
`RefCell<T>` para que possamos modificar ao que se refere uma variante
`Cons`</span>

We’re using another variation of the `List` definition in Listing 15-5. The
second element in the `Cons` variant is now `RefCell<Rc<List>>`, meaning that
instead of having the ability to modify the `i32` value like we did in Listing
15-24, we want to modify which `List` a `Cons` variant is pointing to. We’re
also adding a `tail` method to make it convenient for us to access the second
item if we have a `Cons` variant.

Estamos usando outra variação da definição de `List` da Listagem 15-5. O segundo
elemento na variante `Cons` agora é um `RefCell<Rc<List>>`, o que significa que
em vez de ter a habilidade de modificar o valor `i32` como fizemos na Listagem
15-24, queremos modificar a `List` à qual a variante `Cons` está apontando.
Também estamos adicionando um método `tail` para nos facilitar o acesso ao
segundo item quando tivermos uma variante `Cons`.

In Listing 15-26, we’re adding a `main` function that uses the definitions in
Listing 15-25. This code creates a list in `a` and a list in `b` that points to
the list in `a`, and then modifies the list in `a` to point to `b`, which
creates a reference cycle. There are `println!` statements along the way to
show what the reference counts are at various points in this process:

Na Listagem 15-26, estamos adicionando uma função `main` que usa as definições
da Listagem 15-25. Este código cria uma lista em `a` e uma lista em `b` que
aponta para a lista em `a`, e depois modifica a lista em `a` para apontar para
`b`, o que cria um ciclo de referências. Temos declarações de `println!` ao
longo do caminho para mostrar quais são as contagens de referências em vários
pontos do processo:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use List::{Cons, Nil};
# use std::rc::Rc;
# use std::cell::RefCell;
# #[derive(Debug)]
# enum List {
#     Cons(i32, RefCell<Rc<List>>),
#     Nil,
# }
#
# impl List {
#     fn tail(&self) -> Option<&RefCell<Rc<List>>> {
#         match *self {
#             Cons(_, ref item) => Some(item),
#             Nil => None,
#         }
#     }
# }
#
fn main() {
    let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));

    println!("a initial rc count = {}", Rc::strong_count(&a));
    println!("a next item = {:?}", a.tail());

    let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a))));

    println!("a rc count after b creation = {}", Rc::strong_count(&a));
    println!("b initial rc count = {}", Rc::strong_count(&b));
    println!("b next item = {:?}", b.tail());

    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    println!("b rc count after changing a = {}", Rc::strong_count(&b));
    println!("a rc count after changing a = {}", Rc::strong_count(&a));

    // Uncomment the next line to see that we have a cycle; it will
    // overflow the stack
    // println!("a next item = {:?}", a.tail());
}
```

```rust
# use List::{Cons, Nil};
# use std::rc::Rc;
# use std::cell::RefCell;
# #[derive(Debug)]
# enum List {
#     Cons(i32, RefCell<Rc<List>>),
#     Nil,
# }
#
# impl List {
#     fn tail(&self) -> Option<&RefCell<Rc<List>>> {
#         match *self {
#             Cons(_, ref item) => Some(item),
#             Nil => None,
#         }
#     }
# }
#
fn main() {
    let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));

    println!("a: contagem de referências inicial = {}", Rc::strong_count(&a));
    println!("a: próximo item = {:?}", a.tail());

    let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a))));

    println!("a: contagem de referências depois da criação de b = {}", Rc::strong_count(&a));
    println!("b: contagem de referências inicial = {}", Rc::strong_count(&b));
    println!("b: próximo item = {:?}", b.tail());

    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    println!("b: contagem de referências depois de mudar a = {}", Rc::strong_count(&b));
    println!("a: contagem de referências depois de mudar a = {}", Rc::strong_count(&a));

    // Descomente a próxima linha para ver que temos um ciclo; ela irá
    // estourar a pilha
    // println!("a: próximo item = {:?}", a.tail());
}
```

<span class="caption">Listing 15-26: Creating a reference cycle of two `List`
values pointing to each other</span>

<span class="caption">Listagem 15-26: Criando um ciclo de referências de dois
valores `List` apontando um para o outro</span>

We create an `Rc<List>` instance holding a `List` value in the variable `a`
with an initial list of `5, Nil`. We then create an `Rc<List>` instance
holding another `List` value in the variable `b` that contains the value 10 and
then points to the list in `a`.

Nós criamos uma instância de `Rc<List>` segurando um valor `List` na variável
`a` com a lista inicial de `5, Nil`. Então criamos uma instância de `Rc<List>`
segurando outro valor `List` na variável `b` que contém o valor 10 e aponta para
a lista em `a`.

We modify `a` so it points to `b` instead of `Nil`, which creates a cycle. We
do that by using the `tail` method to get a reference to the
`RefCell<Rc<List>>` in `a`, which we put in the variable `link`. Then we use
the `borrow_mut` method on the `RefCell<Rc<List>>` to change the value inside
from an `Rc<List>` that holds a `Nil` value to the `Rc<List>` in `b`.

Nós modificamos `a` para que aponte para `b` em vez de `Nil`, o que cria um
ciclo. Fazemos isso usando o método `tail` para obter uma referência ao
`RefCell<Rc<List>>` em `a`, a qual colocamos na variável `link`. Então usamos o
método `borrow_mut` no `RefCell<Rc<List>>` para modificar o valor interno: de um
`Rc<List>` que guarda um valor `Nil` para o `Rc<List>` em `b`.

When we run this code, keeping the last `println!` commented out for the
moment, we’ll get this output:

Quando rodamos esse código, mantendo o último `println!` comentado por ora,
obtemos esta saída:

```text
a initial rc count = 1
a next item = Some(RefCell { value: Nil })
a rc count after b creation = 2
b initial rc count = 1
b next item = Some(RefCell { value: Cons(5, RefCell { value: Nil }) })
b rc count after changing a = 2
a rc count after changing a = 2
```

```text
a: contagem de referências inicial = 1
a: próximo item = Some(RefCell { value: Nil })
a: contagem de referências depois da criação de b = 2
b: contagem de referências inicial = 1
b: próximo item = Some(RefCell { value: Cons(5, RefCell { value: Nil }) })
b: contagem de referências depois de mudar a = 2
a: contagem de referências depois de mudar a = 2
```

The reference count of the `Rc<List>` instances in both `a` and `b` are 2
after we change the list in `a` to point to `b`. At the end of `main`, Rust
will try to drop `b` first, which will decrease the count in each of the
`Rc<List>` instances in `a` and `b` by one.

A contagem de referências das instâncias de `Rc<List>` em ambos `a` e `b` é 2
depois que mudamos a lista em `a` para apontar para `b`. No final da `main`, o
Rust tentará destruir `b` primeiro, o que diminuirá em 1 a contagem em cada uma
das instâncias de `Rc<List>` em `a` e `b`.

However, because `a` is still referencing the `Rc<List>` that was in `b`,
that `Rc<List>` has a count of 1 rather than 0, so the memory the
`Rc<List>` has on the heap won’t be dropped. The memory will just sit there
with a count of one, forever. To visualize this reference cycle, we’ve created
a diagram in Figure 15-4:

Contudo, como a variável `a` ainda está se referindo ao `Rc<List>` que estava em
`b`, ele terá uma contagem de 1 em vez de 0, então a memória que ele tem no heap
não será destruída. A memória irá ficar lá com uma contagem de 1, para sempre.
Para visualizar esse ciclo de referências, criamos um diagrama na Figura 15-4:

<img alt="Reference cycle of lists" src="img/trpl15-04.svg" class="center" />

<img alt="Ciclo de referências de listas" src="img/trpl15-04.svg" class="center"
/>

<span class="caption">Figure 15-4: A reference cycle of lists `a` and `b`
pointing to each other</span>

<span class="caption">Figura 15-4: Um ciclo de referências das listas `a` e `b`
apontando uma para a outra</span>

If you uncomment the last `println!` and run the program, Rust will try to
print this cycle with `a` pointing to `b` pointing to `a` and so forth until it
overflows the stack.

Se você descomentar o último `println!` e rodar o programa, o Rust tentará
imprimir esse ciclo com `a` apontando para `b` apontando para `a` e assim por
diante até estourar a pilha.

In this case, right after we create the reference cycle, the program ends. The
consequences of this cycle aren’t very dire. If a more complex program
allocates lots of memory in a cycle and holds onto it for a long time, the
program would use more memory than it needs and might overwhelm the system,
causing it to run out of available memory.

Nesse exemplo, logo depois que criamos o ciclo de referências, o programa
termina. As consequências desse ciclo não são muito graves. Se um programa mais
complexo aloca um monte de memória em um ciclo e não a libera por muito tempo,
ele acaba usando mais memória do que precisa e pode sobrecarregar o sistema,
fazendo com que fique sem memória disponível.

Creating reference cycles is not easily done, but it’s not impossible either.
If you have `RefCell<T>` values that contain `Rc<T>` values or similar nested
combinations of types with interior mutability and reference counting, you must
ensure that you don’t create cycles; you can’t rely on Rust to catch them.
Creating a reference cycle would be a logic bug in your program that you should
use automated tests, code reviews, and other software development practices to
minimize.

Criar ciclos de referências não é fácil de fazer, mas também não é impossível.
Se você tem valores `RefCell<T>` que contêm valores `Rc<T>` ou combinações
aninhadas de tipos parecidas, com mutabilidade interior e contagem de
referências, você deve se assegurar de que não está criando ciclos; você não
pode contar com o Rust para pegá-los. Criar ciclos de referências seria um erro
de lógica no seu programa, e você deve usar testes automatizados, revisões de
código e outras práticas de desenvolvimento de software para minimizá-los.

Another solution for avoiding reference cycles is reorganizing your data
structures so that some references express ownership and some references don’t.
As a result, you can have cycles made up of some ownership relationships and
some non-ownership relationships, and only the ownership relationships affect
whether or not a value can be dropped. In Listing 15-25, we always want `Cons`
variants to own their list, so reorganizing the data structure isn’t possible.
Let’s look at an example using graphs made up of parent nodes and child nodes
to see when non-ownership relationships are an appropriate way to prevent
reference cycles.

Outra solução para evitar ciclos de referências é reorganizar suas estruturas de
dados para que algumas referências expressem posse e outras não. Assim, você
pode ter ciclos feitos de algumas relações de posse e algumas relações de não
posse, e apenas as relações de posse afetam se um valor pode ou não ser
destruído. Na Listagem 15-25, nós sempre queremos que as variantes `Cons`
possuam sua lista, então reorganizar a estrutura de dados não é possível. Vamos
dar uma olhada em um exemplo usando grafos feitos de vértices pais e vértices
filhos para ver quando relações de não posse são um jeito apropriado de evitar
ciclos de referências.

### Preventing Reference Cycles: Turn an `Rc<T>` into a `Weak<T>`

### Prevenindo Ciclos de Referência: Transforme um `Rc<T>` em um `Weak<T>`

So far, we’ve demonstrated that calling `Rc::clone` increases the
`strong_count` of an `Rc<T>` instance, and an `Rc<T>` instance is only
cleaned up if its `strong_count` is 0. We can also create a *weak reference* to
the value within an `Rc<T>` instance by calling `Rc::downgrade` and passing a
reference to the `Rc<T>`. When we call `Rc::downgrade`, we get a smart
pointer of type `Weak<T>`. Instead of increasing the `strong_count` in the
`Rc<T>` instance by one, calling `Rc::downgrade` increases the `weak_count`
by one. The `Rc<T>` type uses `weak_count` to keep track of how many
`Weak<T>` references exist, similar to `strong_count`. The difference is the
`weak_count` doesn’t need to be 0 for the `Rc<T>` instance to be cleaned up.

Até agora, demonstramos que chamar `Rc::clone` aumenta a `strong_count`
(*contagem de referências fortes*) de uma instância `Rc<T>`, e que a instância
`Rc<T>` só é liberada se sua `strong_count` é 0. Também podemos criar uma
*referência fraca* (*weak reference*) ao valor dentro de uma instância `Rc<T>`
chamando `Rc::downgrade` e passando-lhe uma referência ao `Rc<T>`. Quando
chamamos `Rc::downgrade`, nós obtemos um ponteiro inteligente do tipo `Weak<T>`.
Em vez de aumentar em 1 a `strong_count` na instância `Rc<T>`, chamar
`Rc::downgrade` aumenta em 1 a `weeak_count` (*contagem de referências fracas*).
O tipo `Rc<T>` usa a `weak_count` para registrar quantas referências `Weak<T>`
existem, parecido com a `strong_count`. A diferença é que a `weak_count` não
precisa ser 0 para a instância `Rc<T>` ser destruída.

Strong references are how we can share ownership of an `Rc<T>` instance. Weak
references don’t express an ownership relationship. They won’t cause a
reference cycle because any cycle involving some weak references will be broken
once the strong reference count of values involved is 0.

Referências fortes são o modo como podemos compartilhar posse de uma instância
`Rc<T>`. Referências fracas não expressam uma relação de posse. Elas não irão
causar um ciclo de referências porque qualquer ciclo envolvendo algumas
referências fracas será quebrado uma vez que a contagem de referências fortes
dos valores envolvidos for 0.

Because the value that `Weak<T>` references might have been dropped, to do
anything with the value that a `Weak<T>` is pointing to, we must make sure the
value still exists. We do this by calling the `upgrade` method on a `Weak<T>`
instance, which will return an `Option<Rc<T>>`. We’ll get a result of `Some` if
the `Rc<T>` value has not been dropped yet and a result of `None` if the
`Rc<T>` value has been dropped. Because `upgrade` returns an `Option<T>`, Rust
will ensure that we handle the `Some` case and the `None` case, and there won’t
be an invalid pointer.

Como o valor ao qual o `Weak<T>` faz referência pode ter sido destruído, para
fazer qualquer coisa com ele, precisamos nos assegurar de que ele ainda exista.
Fazemos isso chamando o método `upgrade` na instância `Weak<T>`, o que nos
retornará uma `Option<Rc<T>>`. Iremos obter um resultado de `Some` se o valor do
`Rc<T>` ainda não tiver sido destruído e um resultado de `None` caso ele já
tenha sido destruído. Como o `upgrade` retorna uma `Option<T>`, o Rust irá
garantir que lidemos com ambos os casos `Some` e `None`, e não haverá um
ponteiro inválido.

As an example, rather than using a list whose items know only about the next
item, we’ll create a tree whose items know about their children items *and*
their parent items.

Como exemplo, em vez de usarmos uma lista cujos itens sabem apenas a respeito do
próximo item, iremos criar uma árvore cujos itens sabem sobre seus itens filhos
*e* sobre seus itens pais.

#### Creating a Tree Data Structure: a `Node` with Child Nodes

#### Criando uma Estrutura de Dados em Árvore: Um `Vertice` com Vértices Filhos

To start, we’ll build a tree with nodes that know about their child nodes.
We’ll create a struct named `Node` that holds its own `i32` value as well as
references to its children `Node` values:

Para começar, vamos construir uma árvore com vértices que saibam apenas sobre
seus vértices filhos. Iremos criar uma estrutura chamada `Vertice` que contenha
seu próprio valor `i32`, além de referências para seus valores filhos do tipo
`Vertice`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
use std::rc::Rc;
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    children: RefCell<Vec<Rc<Node>>>,
}
```

```rust
use std::rc::Rc;
use std::cell::RefCell;

#[derive(Debug)]
struct Vertice {
    valor: i32,
    filhos: RefCell<Vec<Rc<Vertice>>>,
}
```

We want a `Node` to own its children, and we want to share that ownership with
variables so we can access each `Node` in the tree directly. To do this, we
define the `Vec<T>` items to be values of type `Rc<Node>`. We also want to
modify which nodes are children of another node, so we have a `RefCell<T>` in
`children` around the `Vec<Rc<Node>>`.

Queremos que um `Vertice` tenha posse de seus filhos, e queremos compartilhar
essa posse com variáveis para que possamos acessar cada `Vertice` da árvore
diretamente. Para fazer isso, definimos os itens do `Vec<T>` para serem valores
do tipo `Rc<Vertice>`. Também queremos modificar quais vértices são filhos de
outro vértice, então temos um `RefCell<T>` em `filhos` em volta do
`Vec<Rc<Vertice>>`.

Next, we’ll use our struct definition and create one `Node` instance named
`leaf` with the value 3 and no children, and another instance named `branch`
with the value 5 and `leaf` as one of its children, as shown in Listing 15-27:

Em seguida, iremos usar nossa definição de struct e criar uma instância de
`Vertice` chamada `folha` com o valor 3 e nenhum filho, e outra instância
chamada `galho` com o valor 5 e `folha` como um de seus filhos, como mostra a
Listagem 15-27:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use std::rc::Rc;
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Node {
#     value: i32,
#    children: RefCell<Vec<Rc<Node>>>,
# }
#
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        children: RefCell::new(vec![]),
    });

    let branch = Rc::new(Node {
        value: 5,
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });
}
```

```rust
# use std::rc::Rc;
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Vertice {
#     valor: i32,
#     filhos: RefCell<Vec<Rc<Vertice>>>,
# }
#
fn main() {
    let folha = Rc::new(Vertice {
        valor: 3,
        filhos: RefCell::new(vec![]),
    });

    let galho = Rc::new(Vertice {
        valor: 5,
        filhos: RefCell::new(vec![Rc::clone(&folha)]),
    });
}
```

<span class="caption">Listing 15-27: Creating a `leaf` node with no children
and a `branch` node with `leaf` as one of its children</span>

<span class="caption">Listagem 15-27: Criando um vértice `folha` sem filhos e um
vértice `galho` com `folha` como um de seus filhos</span>

We clone the `Rc<Node>` in `leaf` and store that in `branch`, meaning the
`Node` in `leaf` now has two owners: `leaf` and `branch`. We can get from
`branch` to `leaf` through `branch.children`, but there’s no way to get from
`leaf` to `branch`. The reason is that `leaf` has no reference to `branch` and
doesn’t know they’re related. We want `leaf` to know that `branch` is its
parent. We’ll do that next.

Nós clonamos o `Rc<Vertice>` em `folha` e armazenamos o resultado em `galho`, o
que significa que o `Vertice` em `folha` agora tem dois possuidores: `folha` e
`galho`. Podemos ir de `galho` para `folha` através de `galho.filhos`, mas não
temos como ir de `folha` para `galho`. O motivo é que `folha` não tem referência
a `galho` e não sabe que eles estão relacionados. Queremos que `folha` saiba que
`galho` é seu pai. Faremos isso em seguida.

#### Adding a Reference from a Child to Its Parent

#### Adicionando uma Referência de um Filho para o Seu Pai

To make the child node aware of its parent, we need to add a `parent` field to
our `Node` struct definition. The trouble is in deciding what the type of
`parent` should be. We know it can’t contain an `Rc<T>` because that would
create a reference cycle with `leaf.parent` pointing to `branch` and
`branch.children` pointing to `leaf`, which would cause their `strong_count`
values to never be 0.

Para tornar o vértice filho ciente de seu pai, precisamos adicionar um campo
`pai` a nossa definição da struct `Vertice`. O problema é decidir qual deveria
ser o tipo de `pai`. Sabemos que ele não pode conter um `Rc<T>` porque isso
criaria um ciclo de referências com `folha.pai` apontando para `galho` e
`galho.filhos` apontando para `folha`, o que faria com que seus valores de
`strong_count` nunca chegassem a 0.

Thinking about the relationships another way, a parent node should own its
children: if a parent node is dropped, its child nodes should be dropped as
well. However, a child should not own its parent: if we drop a child node, the
parent should still exist. This is a case for weak references!

Pensando sobre as relações de outra forma, um vértice pai deveria ter posse de
seus filhos: se um vértice pai é destruído, seus vértices filhos também deveriam
ser. Entretanto, um filho não deveria ter posse de seu pai: se destruirmos um
vértice filho, o pai ainda deveria existir. Esse é um caso para referências
fracas!

So instead of `Rc<T>`, we’ll make the type of `parent` use `Weak<T>`,
specifically a `RefCell<Weak<Node>>`. Now our `Node` struct definition looks
like this:

Então em vez de `Rc<T>`, faremos com que o tipo de `pai` use `Weak<T>`, mais
especificamente um `RefCell<Weak<Vertice>>`. Agora nossa definição da struct
`Vertice` fica assim:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Vertice {
    valor: i32,
    pai: RefCell<Weak<Vertice>>,
    filhos: RefCell<Vec<Rc<Vertice>>>,
}
```

Now a node will be able to refer to its parent node but doesn’t own its parent.
In Listing 15-28, we update `main` to use this new definition so the `leaf`
node will have a way to refer to its parent, `branch`:

Agora um vértice pode se referir a seu vértice pai, mas não tem posse dele. Na
Listagem 15-28, atualizamos a `main` com essa nova definição para que o vértice
`folha` tenha um jeito de se referir a seu pai, `galho`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use std::rc::{Rc, Weak};
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Node {
#     value: i32,
#     parent: RefCell<Weak<Node>>,
#     children: RefCell<Vec<Rc<Node>>>,
# }
#
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```

```rust
# use std::rc::{Rc, Weak};
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Vertice {
#     valor: i32,
#     pai: RefCell<Weak<Vertice>>,
#     filhos: RefCell<Vec<Rc<Vertice>>>,
# }
#
fn main() {
    let folha = Rc::new(Vertice {
        valor: 3,
        pai: RefCell::new(Weak::new()),
        filhos: RefCell::new(vec![]),
    });

    println!("pai de folha = {:?}", folha.pai.borrow().upgrade());

    let galho = Rc::new(Vertice {
        valor: 5,
        pai: RefCell::new(Weak::new()),
        filhos: RefCell::new(vec![Rc::clone(&folha)]),
    });

    *folha.pai.borrow_mut() = Rc::downgrade(&galho);

    println!("pai de folha = {:?}", folha.pai.borrow().upgrade());
}
```

<span class="caption">Listing 15-28: A `leaf` node with a `Weak` reference to
its parent node `branch`</span>

<span class="caption">Listagem 15-28: Um vértice `folha` com uma referência
`Weak` a seu vértice pai `galho`</span>

Creating the `leaf` node looks similar to how creating the `leaf` node looked
in Listing 15-27 with the exception of the `parent` field: `leaf` starts out
without a parent, so we create a new, empty `Weak<Node>` reference instance.

Criar o vértice `folha` é semelhante a como o criamos na Listagem 15-27, com
exceção do campo `pai`: `folha` começa sem um pai, então criamos uma instância
nova e vazia de uma referência `Weak<Vertice>`.

At this point, when we try to get a reference to the parent of `leaf` by using
the `upgrade` method, we get a `None` value. We see this in the output from the
first `println!` statement:

Nesse ponto, quando tentamos obter uma referência ao pai de `folha` usando o
método `upgrade`, recebemos um valor `None`. Vemos isso na saída do primeiro
comando `println!`:

```text
leaf parent = None
```

```text
pai de folha = None
```

When we create the `branch` node, it will also have a new `Weak<Node>`
reference in the `parent` field, because `branch` doesn’t have a parent node.
We still have `leaf` as one of the children of `branch`. Once we have the
`Node` instance in `branch`, we can modify `leaf` to give it a `Weak<Node>`
reference to its parent. We use the `borrow_mut` method on the
`RefCell<Weak<Node>>` in the `parent` field of `leaf`, and then we use the
`Rc::downgrade` function to create a `Weak<Node>` reference to `branch` from
the `Rc<Node>` in `branch.`

Quando criamos o vértice `galho`, ele também tem uma nova referência
`Weak<Vertice>` no campo `pai`, porque `galho` não tem um vértice pai. Nós ainda
temos `folha` como um dos filhos de `galho`. Uma vez que temos a instância de
`Vertice` em `galho`, podemos modificar `folha` para lhe dar uma referência
`Weak<Vertice>` a seu pai. Usamos o método `borrow_mut` do
`RefCell<Weak<Vertice>>` no campo `pai` de `folha`, e então usamos a função
`Rc::downgrade` para criar uma referência `Weak<Vertice>` a `galho` a partir do
`Rc<Vertice>` em `galho`.

When we print the parent of `leaf` again, this time we’ll get a `Some` variant
holding `branch`: now `leaf` can access its parent! When we print `leaf`, we
also avoid the cycle that eventually ended in a stack overflow like we had in
Listing 15-26: the `Weak<Node>` references are printed as `(Weak)`:

Quando imprimimos o pai de `folha` de novo, dessa vez recebemos uma variante
`Some` contendo `galho`: agora `folha` tem acesso a seu pai! Quando imprimimos
`folha`, nós também evitamos o ciclo que eventualmente terminou em um estouro de
pilha como o que tivemos na Listagem 15-26: as referências `Weak<Vertice>` são
impressas como `(Weak)`:

```text
leaf parent = Some(Node { value: 5, parent: RefCell { value: (Weak) },
children: RefCell { value: [Node { value: 3, parent: RefCell { value: (Weak) },
children: RefCell { value: [] } }] } })
```

```text
pai de folha = Some(Vertice { valor: 5, pai: RefCell { valor: (Weak) },
filhos: RefCell { valor: [Vertice { valor: 3, pai: RefCell { valor: (Weak) },
filhos: RefCell { valor: [] } }] } })
```

The lack of infinite output indicates that this code didn’t create a reference
cycle. We can also tell this by looking at the values we get from calling
`Rc::strong_count` and `Rc::weak_count`.

A falta de saída infinita indica que esse código não criou um ciclo de
referências. Também podemos perceber isso olhando para os valores que obtemos ao
chamar `Rc::strong_count` e `Rc::weak_count`.

#### Visualizing Changes to `strong_count` and `weak_count`

#### Visualizando Mudanças a `strong_count` e `weak_count`

Let’s look at how the `strong_count` and `weak_count` values of the `Rc<Node>`
instances change by creating a new inner scope and moving the creation of
`branch` into that scope. By doing so, we can see what happens when `branch` is
created and then dropped when it goes out of scope. The modifications are shown
in Listing 15-29:

Para ver como os valores de `strong_count` e `weak_count` das instâncias de
`Rc<Vertice>` mudam, vamos criar um novo escopo interno e mover a criação de
`galho` para dentro dele. Fazendo isso, podemos ver o que acontece quando
`galho` é criado e depois destruído quando sai de escopo. As modificações são
mostradas na Listagem 15-29:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# use std::rc::{Rc, Weak};
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Node {
#     value: i32,
#     parent: RefCell<Weak<Node>>,
#     children: RefCell<Vec<Rc<Node>>>,
# }
#
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );

    {
        let branch = Rc::new(Node {
            value: 5,
            parent: RefCell::new(Weak::new()),
            children: RefCell::new(vec![Rc::clone(&leaf)]),
        });

        *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

        println!(
            "branch strong = {}, weak = {}",
            Rc::strong_count(&branch),
            Rc::weak_count(&branch),
        );

        println!(
            "leaf strong = {}, weak = {}",
            Rc::strong_count(&leaf),
            Rc::weak_count(&leaf),
        );
    }

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );
}
```

```rust
# use std::rc::{Rc, Weak};
# use std::cell::RefCell;
#
# #[derive(Debug)]
# struct Vertice {
#     valor: i32,
#     pai: RefCell<Weak<Vertice>>,
#     filhos: RefCell<Vec<Rc<Vertice>>>,
# }
#
fn main() {
    let folha = Rc::new(Vertice {
        valor: 3,
        pai: RefCell::new(Weak::new()),
        filhos: RefCell::new(vec![]),
    });

    println!(
        "folha: fortes = {}, fracas = {}",
        Rc::strong_count(&folha),
        Rc::weak_count(&folha),
    );

    {
        let galho = Rc::new(Vertice {
            valor: 5,
            pai: RefCell::new(Weak::new()),
            filhos: RefCell::new(vec![Rc::clone(&folha)]),
        });

        *folha.pai.borrow_mut() = Rc::downgrade(&galho);

        println!(
            "galho: fortes = {}, fracas = {}",
            Rc::strong_count(&galho),
            Rc::weak_count(&galho),
        );

        println!(
            "folha: fortes = {}, fracas = {}",
            Rc::strong_count(&folha),
            Rc::weak_count(&folha),
        );
    }

    println!("pai de folha = {:?}", folha.pai.borrow().upgrade());
    println!(
        "folha: fortes = {}, fracas = {}",
        Rc::strong_count(&folha),
        Rc::weak_count(&folha),
    );
}
```

<span class="caption">Listing 15-29: Creating `branch` in an inner scope and
examining strong and weak reference counts</span>

<span class="caption">Listagem 15-29: Criando `galho` em um escopo interno e
examinando contagens de referências fortes e fracas</span>

After `leaf` is created, its `Rc<Node>` has a strong count of 1 and a weak
count of 0. In the inner scope, we create `branch` and associate it with
`leaf`, at which point when we print the counts, the `Rc<Node>` in `branch`
will have a strong count of 1 and a weak count of 1 (for `leaf.parent` pointing
to `branch` with a `Weak<Node>`). When we print the counts in `leaf`, we’ll see
it will have a strong count of 2, because `branch` now has a clone of the
`Rc<Node>` of `leaf` stored in `branch.children` but will still have a weak
count of 0.

Depois que `folha` é criada, seu `Rc<Vertice>` tem uma *strong count* de 1 e uma
*weak count* de 0. Dentro do escopo interno, criamos `galho` e o associamos a
`folha`. Nesse ponto, quando imprimimos as contagens, o `Rc<Vertice>` em `galho`
tem uma strong count de 1 e uma weak count de 1 (porque `folha.pai` aponta para
`galho` com uma `Weak<Vertice>`). Quando imprimirmos as contagens de `folha`,
veremos que ela terá uma strong count de 2, porque `galho` agora tem um clone do
`Rc<Vertice>` de `folha` armazenado em `galho.filhos`, mas ainda terá uma weak
count de 0.

When the inner scope ends, `branch` goes out of scope and the strong count of
the `Rc<Node>` decreases to 0, so its `Node` is dropped. The weak count of 1
from `leaf.parent` has no bearing on whether or not `Node` is dropped, so we
don’t get any memory leaks!

Quando o escopo interno termina, `galho` sai de escopo e a strong count do
`Rc<Vertice>` diminui para 0, e então seu `Vertice` é destruído. A weak count de
1 por causa de `folha.pai` não tem nenhuma influência sobre se `Vertice` é
destruído ou não, então não temos nenhum vazamento de memória!

If we try to access the parent of `leaf` after the end of the scope, we’ll get
`None` again. At the end of the program, the `Rc<Node>` in `leaf` has a strong
count of 1 and a weak count of 0, because the variable `leaf` is now the only
reference to the `Rc<Node>` again.

Se tentarmos acessar o pai de `folha` depois do fim do escopo, receberemos
`None` de novo. No fim do programa, o `Rc<Vertice>` em `folha` tem uma strong
count de 1 e uma weak count de 0, porque a variável `folha` agora é de novo a
única referência ao `Rc<Vertice>`.

All of the logic that manages the counts and value dropping is built into
`Rc<T>` and `Weak<T>` and their implementations of the `Drop` trait. By
specifying that the relationship from a child to its parent should be a
`Weak<T>` reference in the definition of `Node`, we’re able to have parent
nodes point to child nodes and vice versa without creating a reference cycle
and memory leaks.

Toda a lógica que gerencia as contagens e a destruição de valores faz parte de
`Rc<T>` e `Weak<T>` e suas implementações da trait `Drop`. Ao especificarmos na
definição de `Vertice` que a relação de um filho para o seu pai deva ser uma
referência `Weak<T>`, somos capazes de ter vértices pai apontando para para
vértices filho e vice-versa sem criar ciclos de referência e vazamentos de
memória.

## Summary

## Resumo

This chapter covered how to use smart pointers to make different guarantees and
trade-offs than those Rust makes by default with regular references. The
`Box<T>` type has a known size and points to data allocated on the heap. The
`Rc<T>` type keeps track of the number of references to data on the heap, so
that data can have multiple owners. The `RefCell<T>` type with its interior
mutability gives us a type that we can use when we need an immutable type but
need to change an inner value of that type; it also enforces the borrowing
rules at runtime instead of at compile time.

Esse capítulo cobriu como usar ponteiros inteligentes para fazer garantias e
trade-offs diferentes daqueles que o Rust faz por padrão com referências
normais. O tipo `Box<T>` tem um tamanho conhecido e aponta para dados alocados
no heap. O tipo `Rc<T>` mantém registro do número de referências a dados no
heap, para que eles possam ter múltiplos possuidores. O tipo `RefCell<T>` com
sua mutabilidade interior nos dá um tipo que podemos usar quando precisamos de
um tipo imutável mas precisamos mudar um valor interno ao tipo; ele também
aplica as regras de empréstimo em tempo de execução em vez de em tempo de
compilação.

Also discussed were the `Deref` and `Drop` traits that enable a lot of the
functionality of smart pointers. We explored reference cycles that can cause
memory leaks and how to prevent them using `Weak<T>`.

Também foram discutidas as traits `Deref` e `Drop` que tornam possível muito da
funcionalidade dos ponteiros inteligentes. Exploramos ciclos de referências que
podem causar vazamentos de memória e como preveni-los usando `Weak<T>`.

If this chapter has piqued your interest and you want to implement your own
smart pointers, check out “The Rustonomicon” at
*https://doc.rust-lang.org/stable/nomicon/* for more useful information.

Se esse capítulo tiver aguçado seu interesse e você quiser implementar seus
próprios ponteiros inteligentes, dê uma olhada no "Rustnomicon" em
*https://doc.rust-lang.org/stable/nomicon/* para mais informação útil.

Next, we’ll talk about concurrency in Rust. You’ll even learn about a few new
smart pointers.

Em seguida, conversaremos sobre concorrência em Rust. Você irá até aprender
sobre alguns novos ponteiros inteligentes.
