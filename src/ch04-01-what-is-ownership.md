## What Is Ownership?
## O Que É Ownership? 

Rust’s central feature is *ownership*. Although the feature is straightforward
to explain, it has deep implications for the rest of the language.

A característica central do Rust é *ownership*. Embora seja bem direta de
explicar, ela tem implicações profundas em todo o resto da linguagem.

All programs have to manage the way they use a computer’s memory while running.
Some languages have garbage collection that constantly looks for no longer used
memory as the program runs; in other languages, the programmer must explicitly
allocate and free the memory. Rust uses a third approach: memory is managed
through a system of ownership with a set of rules that the compiler checks at
compile time. No run-time costs are incurred for any of the ownership features.

Todos os programas têm que decidir de que forma vão usar a memória do computador
durante a execução. Algumas linguagens possuem _garbage collection_ (coleta de
lixo), que constantemente busca segmentos de memória que já não são mais
utilizados enquanto o programa executa; em outras linguagens, o programador deve
alocar e liberar memória de forma explícita. Rust usa uma terceira abordagem: a
memória é gerenciada através de um sistema de posse, que tem um conjunto de
regras verificadas em tempo de compilação. Nenhuma característica relacionada ao
ownership implica qulaquer custo em tempo de execução.

Because ownership is a new concept for many programmers, it does take some time
to get used to. The good news is that the more experienced you become with Rust
and the rules of the ownership system, the more you’ll be able to naturally
develop code that is safe and efficient. Keep at it!

Como ownership é um conceito novo para muitos programadores, leva um pouco de
tempo para se acostumar. A boa notícia é que quanto mais experiente você se
torna em Rust e nas regras do sistema de posse, mais você será capaz de
escrever, naturalmente, código seguro e eficiente. Fique aí!

When you understand ownership, you’ll have a solid foundation for understanding
the features that make Rust unique. In this chapter, you’ll learn ownership by
working through some examples that focus on a very common data structure:
strings.

Quando você entender ownership, você terá uma fundação sólida para entender as
características que fazem o Rust ser único. Neste capítulo, você vai aprender
ownership trabalhando em alguns exemplos com foco em uma estrutura de dados
muito comum: _strings_.   

<!-- PROD: START BOX -->

> ### The Stack and the Heap
> ### A Pilha e a _Heap_
>
> In many programming languages, we don’t have to think about the stack and the
> heap very often. But in a systems programming language like Rust, whether a
> value is on the stack or the heap has more of an effect on how the language
> behaves and why we have to make certain decisions. We’ll describe parts of
> ownership in relation to the stack and the heap later in this chapter, so here
> is a brief explanation in preparation.
>
> Em muitas linguagens de programação, não temos que pensar muito sobre a pilha
> e sobre a _heap_. Mas em uma linguagem de programação de sistemas, como Rust,
> o fato de um valor estar na pilha ou na heap tem impacto na forma como a
> linguagem se comporta e no porquê de termos que tomar certas decisões. Vamos
> descrever partes do ownership em relação à pilha e à heap mais para a frente
> neste capítulo, então aqui vai uma explicação preparatória.
>
> Both the stack and the heap are parts of memory that is available to your code
> to use at runtime, but they are structured in different ways. The stack stores
> values in the order it gets them and removes the values in the opposite order.
> This is referred to as *last in, first out*. Think of a stack of plates: when
> you add more plates, you put them on top of the pile, and when you need a
> plate, you take one off the top. Adding or removing plates from the middle or
> bottom wouldn’t work as well! Adding data is called *pushing onto the stack*,
> and removing data is called *popping off the stack*.
>
> Tanto a pilha como a heap são partes da memória que estão disponíveis ao seu
> código para uso em tempo de execução, mas elas são estruturadas de formas
> diferentes. A pilha armazena valores na ordem em que eles chegam, e os remove
> na ordem inversa. Isto é chamado de *last in, first out* (último a chegar,
> primeiro a sair). Imagine uma pilha de pratos: quando você coloca mais pratos,
> você os põe em cima da pilha, e quando você precisa de um prato, você pega o
> que está no topo. Adicionar ou remover pratos do meio ou do fundo não funciona
> tão bem! Dizemos fazer um *push* na pilha quando nos refererimos a inserir
> dados, e fazer um *pop* da pilha quando nos referimos a remover dados.
>
> The stack is fast because of the way it accesses the data: it never has to
> search for a place to put new data or a place to get data from because that
> place is always the top. Another property that makes the stack fast is that
> all data on the stack must take up a known, fixed size.
>
> A pilha é rápida por conta da forma como ela acessa os dados: ela nunca tem
> que procurar um lugar para colocar novos dados, ou um lugar de onde obter
> dados, este lugar é sempre o topo da pilha. Outra propriedade que faz a pilha
> ser rápida é que todos os dados contidos nela devem ocupar um tamanho fixo e
> conhecido.
>
> For data with a size unknown to us at compile time or a size that might
> change, we can store data on the heap instead. The heap is less organized:
> when we put data on the heap, we ask for some amount of space. The operating
> system finds an empty spot somewhere in the heap that is big enough, marks it
> as being in use, and returns to us a *pointer*, which is the address of that
> location. This process is called *allocating on the heap*, and sometimes we
> abbreviate the phrase as just “allocating.” Pushing values onto the stack is
> not considered allocating. Because the pointer is a known, fixed size, we can
> store the pointer on the stack, but when we want the actual data, we have to
> follow the pointer.
>
> Para dados com um tamanho desconhecido em tempo de compilação, ou com um
> tamanho que pode mudar, podemos usar a heap em vez da pilha. A heap é menos
> organizada: quando colocamos dados na heap, nós pedimos um certo espaço de
> memória. O sistema operacional encontra um espaço vazio em algum lugar na heap
> que seja grande o suficiente, marca este espaço como em uso, e nos retorna um
> *ponteiro*, que é o endereço deste local. Este processo é chamado de
> *alocar na heap*, e às vezes se abrevia esta frase como apenas "alocação".
> Colocar valores na pilha não é considerado uma alocação. Como o ponteiro tem
> um tamanho fixo e conhecido, podemos armazená-lo na pilha, mas quando queremos
> os dados, de fato, temos que seguir o ponteiro. 
>
> Think of being seated at a restaurant. When you enter, you state the number of
> people in your group, and the staff finds an empty table that fits everyone
> and leads you there. If someone in your group comes late, they can ask where
> you’ve been seated to find you.
>
> Imagine que você está sentado em um restaurante. Quando você entra, você diz
> o número de pessoas que estão com você, o atendente encontra uma mesa vazia
> que acomode todos e os leva para lá. Se alguém do seu grupo chegar mais tarde,
> poderá perguntar onde vocês estão para encontrá-los.
>
> Accessing data in the heap is slower than accessing data on the stack because
> we have to follow a pointer to get there. Contemporary processors are faster
> if they jump around less in memory. Continuing the analogy, consider a server
> at a restaurant taking orders from many tables. It’s most efficient to get
> all the orders at one table before moving on to the next table. Taking an
> order from table A, then an order from table B, then one from A again, and
> then one from B again would be a much slower process. By the same token, a
> processor can do its job better if it works on data that’s close to other
> data (as it is on the stack) rather than farther away (as it can be on the
> heap). Allocating a large amount of space on the heap can also take time.
>
> Acessar dados na heap é mais lento do que acessar dados na pilha, porque você
> precisa seguir um ponteiro para chegar lá. Processadores de hoje em dia são
> mais rápidos se não precisarem pular tanto de um lugar para outro na memória.
> Continuando com a analogia, considere um garçom no restaurante anotando os
> pedidos de várias mesas. É mais eficiente anotar todos os pedidos de uma única
> mesa antes de passar para a mesa seguinte. Anotar um pedido da mesa A, depois
> um da mesa B, depois outro da mesa A, e outro da mesa B novamente seria um
> processo bem mais lento. Da mesma forma, um processador pode cumprir melhor
> sua tarefa se trabalhar em dados que estão próximos uns dos outros (assim como
> estão na pilha) em vez de dados afastados entre si (como podem estar na heap).
> Alocar um espaço grande na heap também pode levar tempo.
>
> When our code calls a function, the values passed into the function
> (including, potentially, pointers to data on the heap) and the function’s
> local variables get pushed onto the stack. When the function is over, those
> values get popped off the stack.
>
> Quando nosso código chama uma função, os valores passados para ela (incluindo
> possíveis ponteiros para dados na heap) e as variáveis locais da função são
> colocados na pilha. Quando a função termina, esses valores são removidos dela.
>
> Keeping track of what parts of code are using what data on the heap,
> minimizing the amount of duplicate data on the heap, and cleaning up unused
> data on the heap so we don’t run out of space are all problems that ownership
> addresses. Once you understand ownership, you won’t need to think about the
> stack and the heap very often, but knowing that managing heap data is why
> ownership exists can help explain why it works the way it does.
>
> Rastrear quais partes do código estão usando quais dados na heap, minimizar a
> quantidade de dados duplicados na heap e limpar segmentos inutilizados da heap
> para que não fiquemos sem espaço são todos problemas tratados pelo ownership.
> Uma vez que você entende ownership, você não vai mais precisar pensar tanto
> sobre a pilha e a heap, mas saber que ownership existe para gerenciar os dados
> na heap pode ajudar a explicar como e por que ele funciona.
>
<!-- PROD: END BOX -->

### Ownership Rules
### Regras de Ownership

First, let’s take a look at the ownership rules. Keep these rules in mind as we
work through the examples that illustrate the rules:

Primeiro, vamos dar uma olhada nas regras de ownership. Mantenha em mente essas
regras quando trabalharmos com os exemplos em seguida:

> 1. Each value in Rust has a variable that’s called its *owner*.
> 2. There can only be one owner at a time.
> 3. When the owner goes out of scope, the value will be dropped.

> 1. Cada valor em Rust possui uma variável que é dita seu *owner* (sua dona).
> 2. Pode apenas haver um owner por vez.
> 3. Quando o owner sai fora de escopo, o valor será destruído.

### Variable Scope
### Escopo de Variáveis

We’ve walked through an example of a Rust program already in Chapter 2. Now
that we’re past basic syntax, we won’t include all the `fn main() {` code in
examples, so if you’re following along, you’ll have to put the following
examples inside a `main` function manually. As a result, our examples will be a
bit more concise, letting us focus on the actual details rather than
boilerplate code.

Já analisamos um exemplo de programa em Rust no Capítulo 2. Agora que já
passamos da sintaxe básica, não vamos incluir o código `fn main() {` nos
próximos exemplos, então se você estiver acompanhando, terá que colocá-los
manualmente dentro de uma função `main`. Como resultado, nossos exemplos serão
um pouco mais concisos, mantendo o foco nos detalhes que realmente interessam.

As a first example of ownership, we’ll look at the *scope* of some variables. A
scope is the range within a program for which an item is valid. Let’s say we
have a variable that looks like this:

Como um primeiro exemplo de ownership, vamos olhar para o *escopo* de algumas
variáveis. Um escopo é a área dentro de um programa para a qual um item é
válido. Digamos que nós temos uma variável como esta:

```rust
let s = "hello";
```

```rust
let s = "olá";
```

The variable `s` refers to a string literal, where the value of the string is
hardcoded into the text of our program. The variable is valid from the point at
which it’s declared until the end of the current *scope*. Listing 4-1 has
comments annotating where the variable `s` is valid:

A variável `s` se refere a uma string literal cujo valor é fixo no código. A
variável é válida do ponto em que é declarada até o fim do atual *escopo*. A
Listagem 4-1 tem comentários indicando onde a variável `s` é válida:

```rust
{                      // s is not valid here, it’s not yet declared
    let s = "hello";   // s is valid from this point forward

    // do stuff with s
}                      // this scope is now over, and s is no longer valid
```

```rust
{                      // s não é válida aqui, ainda não está declarada
    let s = "texto";   // s é válida deste ponto em diante

    // faz alguma coisa com s
}                      // agora este escopo terminou, e s não é mais válida
```

<span class="caption">Listing 4-1: A variable and the scope in which it is
valid</span>

<span class="caption">Listagem 4-1: Uma variável e o escopo em que ela é
válida.</span>

In other words, there are two important points in time here:

Em outras palavras, existem dois pontos no tempo que são importantes aqui:

1. When `s` comes *into scope*, it is valid.
1. It remains so until it goes *out of scope*.

1. Quando `s` *entra no escopo*, ela é válida.
1. Permanece dessa maneira até que ela *saia de escopo*.

At this point, the relationship between scopes and when variables are valid is
similar to other programming languages. Now we’ll build on top of this
understanding by introducing the `String` type.

Neste ponto, a relação entre escopos e quando variáveis são válidas é similar a
outras linguagens de programação. Agora vamos construir sobre este entendimento,
apresentando o tipo `String`.

### The `String` Type
### O Tipo `String`

To illustrate the rules of ownership, we need a data type that is more complex
than the ones we covered in Chapter 3. The types covered in the “Data Types”
section are all stored on the stack and popped off the stack when their scope
is over, but we want to look at data that is stored on the heap and explore how
Rust knows when to clean up that data.

Para ilustrar as regras de ownership, precisamos de um tipo de dados que seja
mais complexo do que aqueles abordados no Capítulo 3. Os tipos abordados na
seção "Tipos de Dados" são todos armazenados na pilha, e retirados dela quando
seu escopo termina, mas queremos ver dados que são armazenados na heap e
explorar como o Rust faz para saber quando limpar esses dados.

We’ll use `String` as the example here and concentrate on the parts of `String`
that relate to ownership. These aspects also apply to other complex data types
provided by the standard library and that you create. We’ll discuss `String` in
more depth in Chapter 8.

Vamos usar `String` como exemplo aqui, e concentrar nas partes de `String` que
estão relacionadas ao ownership. Esses aspectos também se aplicam aos outros
tipos complexos de dados fornecidos pela biblioteca padrão e os que você mesmo
cria. Vamos discutir `String` mais a fundo no Capítulo 8. 

We’ve already seen string literals, where a string value is hardcoded into our
program. String literals are convenient, but they aren’t always suitable for
every situation in which you want to use text. One reason is that they’re
immutable. Another is that not every string value can be known when we write
our code: for example, what if we want to take user input and store it? For
these situations, Rust has a second string type, `String`. This type is
allocated on the heap and as such is able to store an amount of text that is
unknown to us at compile time. You can create a `String` from a string literal
using the `from` function, like so:

Já vimos strings literais, em que um valor de string é fixado pelo código do
nosso programa. Strings literais são convenientes, mas nem sempre são adequadas
para situações em que queremos usar texto. Um motivo é que elas são imutáveis.
Outro é que nem todos os valores de string são conhecidos enquanto escrevemos
nosso código: por exemplo, o que fazer se queremos obter uma entrada do usuário
e armazená-la? Para essas situações, Rust tem um segundo tipo de strings,
`String`. Este tipo é alocado na heap, e como tal, é capaz de armazenar uma
quantidade de texto que é desconhecida em tempo de compilação. Você pode criar
uma `String` de uma string literal usando a função `from`, da seguinte forma:

```rust
let s = String::from("hello");
```

```rust
let s = String::from("texto");
```

The double colon (`::`) is an operator that allows us to namespace this
particular `from` function under the `String` type rather than using some sort
of name like `string_from`. We’ll discuss this syntax more in the “Method
Syntax” section of Chapter 5 and when we talk about namespacing with modules in
Chapter 7.

O `::` é um operador que nos permite indicar que o _namespace_ desta função
`from`, em particular, é o tipo `String`, de forma que não precisamos usar um
nome específico como `string_from`. Vamos discutir esta sintaxe na seção
"Sintaxe do Método" do Capítulo 5, e quando falarmos sobre _namespaces_ com
módulos no Capítulo 7.

This kind of string *can* be mutated:

Este tipo de string *pode* ser alterada:

```rust
let mut s = String::from("hello");

s.push_str(", world!"); // push_str() appends a literal to a String

println!("{}", s); // This will print `hello, world!`
```

```rust
let mut s = String::from("olá");

s.push_str(", mundo!"); // push_str() adiciona um literal à String

println!("{}", s); // Isso vai exibir `olá, mundo!`
```

So, what’s the difference here? Why can `String` be mutated but literals
cannot? The difference is how these two types deal with memory.

Mas então, qual é a diferença aqui? Por que `String` pode ser alterada enquanto
literais não podem? A diferença está em como esses dois tipos lidam com memória.

### Memory and Allocation
### Memória e Alocação

In the case of a string literal, we know the contents at compile time so the
text is hardcoded directly into the final executable, making string literals
fast and efficient. But these properties only come from its immutability.
Unfortunately, we can’t put a blob of memory into the binary for each piece of
text whose size is unknown at compile time and whose size might change while
running the program.

No caso de uma string literal, sabemos o seu conteúdo em tempo de compilação,
então o texto é injetado diretamente para dentro do executável final, o que
faz strings literais serem rápidas e eficientes. Mas essas propriedades provêm
apenas da sua imutabilidade. Infelizmente, não podemos colocar um segmento de
memória dentro do binário para cada texto cujo tamanho é desconhecido em tempo
de compilação, e cujo tamanho pode mudar ao longo da execução do programa.

With the `String` type, in order to support a mutable, growable piece of text,
we need to allocate an amount of memory on the heap, unknown at compile time,
to hold the contents. This means:

Com o tipo `String`, para poder acomodar um trecho mutável e expansível de
texto, precisamos alocar uma quantidade de memória na heap, que é desconhecida
em tempo de compilação, para manter o seu conteúdo. Isto significa que:

1. The memory must be requested from the operating system at runtime.
2. We need a way of returning this memory to the operating system when we’re
done with our `String`.

1. A memória deve ser solicitada ao sistema operacional em tempo de execução.
2. Precisamos de uma forma de retornar esta memória ao sistema operacional
quando tivermos finalizado nossa `String`.

That first part is done by us: when we call `String::from`, its implementation
requests the memory it needs. This is pretty much universal in programming
languages.

A primeira parte é feita por nós: quando chamamos `String::from`, sua
implementação solicita a memória de que precisa. Isso é meio que universal em
linguagens de programação.

However, the second part is different. In languages with a *garbage collector
(GC)*, the GC keeps track and cleans up memory that isn’t being used anymore,
and we, as the programmer, don’t need to think about it. Without a GC, it’s the
programmer’s responsibility to identify when memory is no longer being used and
call code to explicitly return it, just as we did to request it. Doing this
correctly has historically been a difficult programming problem. If we forget,
we’ll waste memory. If we do it too early, we’ll have an invalid variable. If
we do it twice, that’s a bug too. We need to pair exactly one `allocate` with
exactly one `free`.

No entanto, a segunda parte é diferente. Em linguagnes com um *garbage collector
(GC)*, o GC rastreia e limpa a memória que não está mais sendo usada, e nós,
como programadores, não precisamos pensar sobre isso. Sem um GC, é
resposabilidade do programador identificar quando a memória não está mais sendo
usada e chamar, explicitamente, um código que a retorne, assim como fizemos para
solicitá-la. Fazer isso corretamente tem sido, historicamente, um problema
difícil de programação. Se esquecermos, vamos desperdiçar memória. Se fizermos
cedo demais, teremos uma variável inválida. Se fizermos duas vezes, também será
um bug. Precisamos casar exatamente um `allocate` (alocar) com exatamente um
`free` (liberar).

Rust takes a different path: the memory is automatically returned once the
variable that owns it goes out of scope. Here’s a version of our scope example
from Listing 4-1 using a `String` instead of a string literal:

Rust segue um caminho diferente: a memória é automaticamente retornada assim que
a variável que a possui sai de escopo. Aqui está uma versão do nosso exemplo de
escopo da Listagem 4-1 usando uma `String` em vez de uma string literal:

```rust
{
    let s = String::from("hello"); // s is valid from this point forward

    // do stuff with s
}                                  // this scope is now over, and s is no
                                   // longer valid
```

```rust
{
    let s = String::from("texto"); // s é válida deste ponto em diante

    // faz alguma coisa com s
}                                  // agora este escopo terminou, e s não é
                                   // mais válida
```

There is a natural point at which we can return the memory our `String` needs
to the operating system: when `s` goes out of scope. When a variable goes out
of scope, Rust calls a special function for us. This function is called `drop`,
and it’s where the author of `String` can put the code to return the memory.
Rust calls `drop` automatically at the closing `}`.

Existe um ponto natural em que podemos retornar ao sistema operacional a memória
da qual precisa nossa `String`: quando `s` sai de escopo. Quando uma variável
sai de escopo, o Rust chama para nós uma função especial. Essa função é chamada
`drop`, e é aí que o autor de `String` pode colocar o código que retorna
a memória. Rust chama `drop` automaticamente ao fechar chaves (`}`).

> Note: In C++, this pattern of deallocating resources at the end of an item’s
> lifetime is sometimes called *Resource Acquisition Is Initialization (RAII)*.
> The `drop` function in Rust will be familiar to you if you’ve used RAII
> patterns.

> Nota: Em C++, esta forma de desalocar recursos no fim do tempo de vida útil de
> um item às vezes é chamado de *Resource Acquisition Is Initialization* (RAII,
> do inglês, Aquisição de Recurso É Inicialização). A função `drop` em Rust vai
> lhe ser bastante familar se você já tiver usado padrões RAII.

This pattern has a profound impact on the way Rust code is written. It may seem
simple right now, but the behavior of code can be unexpected in more
complicated situations when we want to have multiple variables use the data
we’ve allocated on the heap. Let’s explore some of those situations now.

Este padrão tem um profundo impacto na forma de escrever código em Rust. Pode
parecer simples agora, mas o comportamento do código pode ser inesperado em
situações mais complicadas, quando queremos que múltiplas variáveis usem os
dados que alocamos na heap. Vamos explorar algumas dessas situações agora.

#### Ways Variables and Data Interact: Move
#### Formas de Interação Entre Variáveis e Dados: _Move_

Multiple variables can interact with the same data in different ways in Rust.
Let’s look at an example using an integer in Listing 4-2:

Múltiplas variáveis podem interagir com os mesmos dados de diferentes formas em
Rust. Vamos ver um exemplo usando um número inteiro na Listagem 4-2:

```rust
let x = 5;
let y = x;
```

<span class="caption">Listing 4-2: Assigning the integer value of variable `x`
to `y`</span>

<span class="caption">Listagem 4-2: Atribuindo o valor inteiro da variável `x`
para `y`.</span>

We can probably guess what this is doing based on our experience with other
languages: “Bind the value `5` to `x`; then make a copy of the value in `x` and
bind it to `y`.” We now have two variables, `x` and `y`, and both equal `5`.
This is indeed what is happening because integers are simple values with a
known, fixed size, and these two `5` values are pushed onto the stack.

Provavelmente podemos advinhar o que isto faz com base nas nossas experiências
com outras linguagens: "Associe o valor `5` a `x`; depois faça uma cópia do
valor em `x` e a associe a `y`." Agora temos duas variáveis, `x` e `y`, e ambas
são iguais a `5`. É isto mesmo que acontece, porque números inteiros são valores
simples que possuem um tamanho fixo e conhecido, e esses dois valores `5` são
colocados na pilha.

Now let’s look at the `String` version:

Agora vamos ver a versão usando `String`:

```rust
let s1 = String::from("hello");
let s2 = s1;
```

```rust
let s1 = String::from("texto");
let s2 = s1;
```

This looks very similar to the previous code, so we might assume that the way
it works would be the same: that is, the second line would make a copy of the
value in `s1` and bind it to `s2`. But this isn’t quite what happens.

Isso parece bem similar ao código anterior, então poderíamos assumir que
funcionaria da mesma forma, isto é, a segunda linha faria uma cópia do valor em
`s1` e a associaria a `s2`. Mas não é exatamente isso que acontece.

To explain this more thoroughly, let’s look at what `String` looks like under
the covers in Figure 4-1. A `String` is made up of three parts, shown on the
left: a pointer to the memory that holds the contents of the string, a length,
and a capacity. This group of data is stored on the stack. On the right is the
memory on the heap that holds the contents.

Para explicar isso mais detalhadamente, vamos ver como a `String` funciona por
baixo dos panos na Figura 4-1. Uma `String` é feita de três partes, mostradas
à esquerda: um ponteiro para a memória que guarda o conteúdo da string, um
tamanho, e uma capacidade. Este grupo de dados é armazenado na pilha. No lado
direito está a memória na heap que guarda o conteúdo.

<img alt="String in memory" src="img/trpl04-01.svg" class="center" style="width: 50%;" />

<img alt="String na memória" src="img/trpl04-01.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-1: Representation in memory of a `String`
holding the value `"hello"` bound to `s1`</span>

<span class="caption">Figura 4-1: Representação na memória de uma `String`
contendo o valor `"texto"` associado a `s1`.</span>

The length is how much memory, in bytes, the contents of the `String` is
currently using. The capacity is the total amount of memory, in bytes, that the
`String` has received from the operating system. The difference between length
and capacity matters, but not in this context, so for now, it’s fine to ignore
the capacity.

O tamanho representa quanta memória, em bytes, o conteúdo da `String` está
usando atualmente. A capacidade é a quantidade total de memória, em bytes, que
a `String` recebeu do sistema operacional. A diferença entre tamanho e
capacidade é importante, mas não neste contexto, então não há problema em
ignorar a capacidade por enquanto.

When we assign `s1` to `s2`, the `String` data is copied, meaning we copy the
pointer, the length, and the capacity that are on the stack. We do not copy the
data on the heap that the pointer refers to. In other words, the data
representation in memory looks like Figure 4-2.

Quando atribuímos `s1` a `s2`, os dados da `String` são copiados, o que
significa que estamos copiando o ponteiro, o tamanho e a capacidade que estão na
pilha. Não estamos copiando os dados que estão na heap, aos quais o ponteiro se
refere. Em outras palavras, a representação dos dados na memória ocorre como
na Figura 4-2.

<img alt="s1 and s2 pointing to the same value" src="img/trpl04-02.svg" class="center" style="width: 50%;" />

<img alt="s1 e s2 apontando para o mesmo valor" src="img/trpl04-02.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-2: Representation in memory of the variable `s2`
that has a copy of the pointer, length, and capacity of `s1`</span>

<span class="caption">Figura 4-2: Representação na memória da variável `s2`, que
tem uma cópia do ponteiro, tamanho e capacidade de `s1`.</span>

The representation does *not* look like Figure 4-3, which is what memory would
look like if Rust instead copied the heap data as well. If Rust did this, the
operation `s2 = s1` could potentially be very expensive in terms of runtime
performance if the data on the heap was large.

A representação *não* ocorre como na Figura 4-3, que é como ficaria a memória se
o Rust também copiasse os dados da heap. Se o Rust fizesse isso, a operação
`s2 = s1` seria potencialmente bastante custosa em termos de desempenho em tempo
de execução caso os dados na heap fossem grandes.

<img alt="s1 and s2 to two places" src="img/trpl04-03.svg" class="center" style="width: 50%;" />

<img alt="s1 e s2 em dois lugares" src="img/trpl04-03.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-3: Another possibility of what `s2 = s1` might
do if Rust copied the heap data as well</span>

<span class="caption">Figura 4-3: Outra possibilidade do que `s2 = s1` poderia
fazer se o Rust também copiasse os dados da heap.</span>

Earlier, we said that when a variable goes out of scope, Rust automatically
calls the `drop` function and cleans up the heap memory for that variable. But
Figure 4-2 shows both data pointers pointing to the same location. This is a
problem: when `s2` and `s1` go out of scope, they will both try to free the
same memory. This is known as a *double free* error and is one of the memory
safety bugs we mentioned previously. Freeing memory twice can lead to memory
corruption, which can potentially lead to security vulnerabilities.

Anteriormente, dissemos que, quando uma variável sai de escopo, o Rust
automaticamente chama a função `drop` e limpa a memória da heap para esta
variável. Mas a Figura 4-2 mostra que os dois ponteiros estão apontando para o
mesmo lugar. Isso é um problema: quando `s2` e `s1` saem de escopo, os dois vão
tentar liberar a mesma memória. Isso é conhecido como erro de *double free*
(liberação dupla), e é um dos bugs de segurança de memória que mencionamos
anteriormente. Liberar memória duas vezes pode levar à corrupção da memória,
o que pode, por sua vez, trazer potenciais vulnerabilidades de segurança.

To ensure memory safety, there’s one more detail to what happens in this
situation in Rust. Instead of trying to copy the allocated memory, Rust
considers `s1` to no longer be valid and therefore, Rust doesn’t need to free
anything when `s1` goes out of scope. Check out what happens when you try to
use `s1` after `s2` is created, it won’t work:

Para garantir a segurança de memória, há um outro detalhe sobre o que acontece
nesta situação em Rust. Em vez de tentar copiar a memória alocada, o Rust
considera que `s1` deixa de ser válida, e portanto, o Rust não precisa liberar
nenhuma memória quando `s1` sai de escopo. Veja só o que acontece quando você
tenta usar `s1` depois que `s2` é criada, não vai funcionar:

```rust,ignore
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```

```rust,ignore
let s1 = String::from("texto");
let s2 = s1;

println!("{}", s1);
```

You’ll get an error like this because Rust prevents you from using the
invalidated reference:

Você vai ter um erro como este, porque o Rust lhe impede de usar a referência
que foi invalidada:

```text
error[E0382]: use of moved value: `s1`
 --> src/main.rs:5:28
  |
3 |     let s2 = s1;
  |         -- value moved here
4 |
5 |     println!("{}, world!", s1);
  |                            ^^ value used here after move
  |
  = note: move occurs because `s1` has type `std::string::String`, which does
  not implement the `Copy` trait
```

```text
error[E0382]: use of moved value: `s1`
 --> src/main.rs:5:20
  |
3 |     let s2 = s1;
  |         -- value moved here
4 |
5 |     println!("{}", s1);
  |                    ^^ value used here after move
  |
  = note: move occurs because `s1` has type `std::string::String`, which does
  not implement the `Copy` trait
```

If you’ve heard the terms “shallow copy” and “deep copy” while working with
other languages, the concept of copying the pointer, length, and capacity
without copying the data probably sounds like a shallow copy. But because Rust
also invalidates the first variable, instead of calling this a shallow copy,
it’s known as a *move*. Here we would read this by saying that `s1` was *moved*
into `s2`. So what actually happens is shown in Figure 4-4.

Se você já ouviu os termos "cópia rasa" e "cópia profunda" (_shallow copy_ e
_deep copy_) enquanto trabalhava com outras linguagens, o conceito de copiar o
ponteiro, tamanho e capacidade sem copiar os dados provavelmente parece uma
cópia rasa. Mas como o Rust também invalida a primeira variável, em vez de
chamar isto de cópia rasa, isto é conhecido como um *move*. Aqui poderíamos
dizer que `s1` foi *movida* para `s2`. Então, o que realmente acontece é
mostrado na Figura 4-4.

<img alt="s1 moved to s2" src="img/trpl04-04.svg" class="center" style="width: 50%;" />

<img alt="s1 movida para s2" src="img/trpl04-04.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-4: Representation in memory after `s1` has been
invalidated</span>

<span class="caption">Figura 4-4: Representação na memória depois de `s1` ter
sido invalidada.</span>

That solves our problem! With only `s2` valid, when it goes out of scope, it
alone will free the memory, and we’re done.

Isso resolve o nosso problema! Tendo apenas `s2` válida, quando ela sair de
escopo, somente ela vai liberar a memória, e pronto.

In addition, there’s a design choice that’s implied by this: Rust will never
automatically create “deep” copies of your data. Therefore, any *automatic*
copying can be assumed to be inexpensive in terms of runtime performance.

Ademais, isto implica uma decisão de projeto: Rust nunca vai criar _deep copies_
dos seus dados. Logo, para qualquer cópia *automática* que aconteça, pode-se
assumir que ela não será custosa em termos de desempenho em tempo de execução. 

#### Ways Variables and Data Interact: Clone
#### Formas de Interação Entre Variáveis e Dados: Clone

If we *do* want to deeply copy the heap data of the `String`, not just the
stack data, we can use a common method called `clone`. We’ll discuss method
syntax in Chapter 5, but because methods are a common feature in many
programming languages, you’ve probably seen them before.

Se nós *queremos* fazer uma cópia profunda dos dados da `String` que estão na
heap, e não apenas os dados que estão na pilha, podemos usar um método comum
chamado `clone`. Vamos discutir sintaxe de métodos no Capítulo 5, mas como os
métodos constituem uma característica comum em várias linguagens de programação,
você provavelmente já os viu antes.

Here’s an example of the `clone` method in action:

Aqui está um exemplo de uso do método `clone`:

```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

```rust
let s1 = String::from("texto");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

This works just fine and is how you can explicitly produce the behavior shown
in Figure 4-3, where the heap data *does* get copied.

Isto funciona bem, e é assim que você pode, explicitamente, produzir o
comportamento mostrado na Figura 4-3, onde os dados da heap *são* copiados.

When you see a call to `clone`, you know that some arbitrary code is being
executed and that code may be expensive. It’s a visual indicator that something
different is going on.

Quando você ver uma chamada para `clone`, você sabe que algum código arbitrário
está sendo executado, e que este código talvez seja custoso. É um indicador
visual de que algo diferente está acontecendo.

#### Stack-Only Data: Copy
#### Dados Somente da Pilha: Copy

There’s another wrinkle we haven’t talked about yet. This code using integers,
part of which was shown earlier in Listing 4-2, works and is valid:

Há um outro detalhezinho de que ainda não falamos. Este código usando números
inteiros, parte do qual foi mostrado anteriormente na Listagem 4-2, funciona e é
válido:

```rust
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
```

But this code seems to contradict what we just learned: we don’t have a call to
`clone`, but `x` is still valid and wasn’t moved into `y`.

Mas este código parece contradizer o que acabamos de aprender: não temos uma
chamada ao método `clone`, mas `x` ainda é válido e não foi movido para `y`.

The reason is that types like integers that have a known size at compile time
are stored entirely on the stack, so copies of the actual values are quick to
make. That means there’s no reason we would want to prevent `x` from being
valid after we create the variable `y`. In other words, there’s no difference
between deep and shallow copying here, so calling `clone` wouldn’t do anything
differently from the usual shallow copying and we can leave it out.

O motivo é que tipos como números inteiros têm um tamanho conhecido em tempo de
compilação e são armazenados inteiramente na pilha, e por isso, cópias desses
valores são rápidas de se fazer. Isso significa que não há razão para impedir
`x` de ser válido após criarmos a variável `y`. Em outras palavras, não há
diferença entre cópia rasa e profunda aqui, então chamar o método `clone` não
faria nada diferente de uma cópia rasa, por isso podemos deixá-lo de lado.

Rust has a special annotation called the `Copy` trait that we can place on
types like integers that are stored on the stack (we’ll talk more about traits
in Chapter 10). If a type has the `Copy` trait, an older variable is still
usable after assignment. Rust won’t let us annotate a type with the `Copy`
trait if the type, or any of its parts, has implemented the `Drop` trait. If
the type needs something special to happen when the value goes out of scope and
we add the `Copy` annotation to that type, we’ll get a compile time error. To
learn about how to add the `Copy` annotation to your type, see Appendix C on
Derivable Traits.

O Rust tem uma anotação especial chamada de _trait_ `Copy`, que podemos colocar
em tipos como números inteiros, que são armazenados na pilha (falaremos mais
sobre traits no Capítulo 10). Se um tipo possui o trait `Copy`, uma variável
anterior vai continuar sendo utilizável depois de uma atribuição. O Rust não vai
nos deixar anotar um tipo com o trait `Copy` se este tipo, ou qualquer uma de
suas partes, tiver implementado o trait `Drop`. Se o tipo precisa que algo
especial aconteça quando o valor sair de escopo e há uma anotação `Copy` neste
tipo, vamos ter um erro de compilação. Para aprender sobre como inserir a
anotação `Copy` ao seu tipo, veja o Apêndice C em Traits Deriváveis.

So what types are `Copy`? You can check the documentation for the given type to
be sure, but as a general rule, any group of simple scalar values can be
`Copy`, and nothing that requires allocation or is some form of resource is
`Copy`. Here are some of the types that are `Copy`:

Então quais tipos são `Copy`? Você pode verificar a documentação de um
determinado tipo para se certificar, mas como regra geral, qualquer grupo de
valores simples escalares podem ser `Copy`, e nada que precise de alocação ou
que seja uma forma de recurso é `Copy`. Aqui estão alguns tipos que são `Copy`:

* All the integer types, like `u32`.
* The Boolean type, `bool`, with values `true` and `false`.
* The character type, `char`.
* All the floating point types, like `f64`.
* Tuples, but only if they contain types that are also `Copy`. `(i32, i32)` is
`Copy`, but `(i32, String)` is not.

* Todos os tipos inteiros, como `u32`.
* O tipo booleano, `bool`, com valores `true` e `false`.
* O tipo caractere, `char`.
* Todos os tipos de ponto flutuante, como `f64`.
* Tuplas, mas apenas aquelas que contém tipos que também são `Copy`.
`(i32, i32)` é `Copy`, mas `(i32, String)` não.

### Ownership and Functions
### Ownership e Funções

The semantics for passing a value to a function are similar to assigning a
value to a variable. Passing a variable to a function will move or copy, just
like assignment. Listing 4-3 has an example with some annotations showing where
variables go into and out of scope:

A semântica para passar um valor a uma função é similar à de atribuir um valor
a uma variável. Passar uma variável a uma função irá mover ou copiar, assim como
acontece em uma atribuição. A Listagem 4-3 tem um exemplo com algumas anotações
mostrando onde as variáveis entram e saem de escopo:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope.

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here.

    let x = 5;                      // x comes into scope.

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it’s okay to still
                                    // use x afterward.

} // Here, x goes out of scope, then s. But since s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope.
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope.
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

```rust
fn main() {
    let s = String::from("texto");  // s entra em escopo.

    toma_posse(s);                  // move o valor de s para dentro da função...
                                    // ... e ele não é mais válido aqui.

    let x = 5;                      // x entra em escopo.

    faz_uma_copia(x);               // x seria movido para dentro da função,
                                    // mas i32 é Copy, então está tudo bem em
                                    // usar x daqui para a frente.

} // Aqui, x sai de escopo, e depois s. Mas como o valor de s foi movido, nada
  // de especial acontece.

fn toma_posse(uma_string: String) { // uma_string entra em escopo.
    println!("{}", uma_string);
} // Aqui, uma_string sai de escopo, e o método `drop` é chamado. A memória que
  // guarda seus dados é liberada.

fn faz_uma_copia(um_inteiro: i32) { // um_inteiro entra em escopo.
    println!("{}", um_inteiro);
} // Aqui, um_inteiro sai de escopo. Nada de especial acontece.
```

<span class="caption">Listing 4-3: Functions with ownership and scope
annotated</span>

<span class="caption">Listagem 4-3: Funções com anotações de ownership e
escopo</span>

If we tried to use `s` after the call to `takes_ownership`, Rust would throw a
compile time error. These static checks protect us from mistakes. Try adding
code to `main` that uses `s` and `x` to see where you can use them and where
the ownership rules prevent you from doing so.

Se tentássemos usar `s` após a chamada para `toma_posse`, o Rust iria lançar um
erro de compilação. Essas verificações estáticas nos protegem de certo enganos.
Tente adicionar um código à função `main` que usa `s` e `x` para ver onde você
pode usá-los e onde as regras de ownership lhe impedem de fazê-lo.

### Return Values and Scope
### Retorno de Valores e Escopo

Returning values can also transfer ownership. Here’s an example with similar
annotations to those in Listing 4-3:

Retornar valores também pode transferir a posse de um valor. Aqui está um
exemplo com anotações similares àquelas da Listagem 4-3:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1.

    let s2 = String::from("hello");     // s2 comes into scope.

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3.
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it.

    let some_string = String::from("hello"); // some_string comes into scope.

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function.
}

// takes_and_gives_back will take a String and return one.
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope.

    a_string  // a_string is returned and moves out to the calling function.
}
```

```rust
fn main() {
    let s1 = entrega_valor();           // entrega_valor move o valor retornado
                                        // para s1.

    let s2 = String::from("texto");     // s2 entra em escopo.

    let s3 = pega_e_entrega_valor(s2);  // s2 é movido para dentro da função
                                        // pega_e_entrega_valor, que também
                                        // move o valor retornado para s3.
} // Aqui, s3 sai de escopo e é destruída. s2 sai de escopo, mas já foi movida,
  // então nada demais acontece. s1 sai de escopo e é destruída.

fn entrega_valor() -> String {               // entrega_valor move o valor
                                             // retornado para dentro da função
                                             // que a chamou.

    let uma_string = String::from("olá");    // uma_string entra em escopo.

    uma_string                               // uma_string é retornada e movida
                                             // para a função que chamou
                                             // entrega_valor.
}

// pega_e_entrega_valor vai pegar uma String e retorná-la.
fn pega_e_entrega_valor(uma_string: String) -> String { // uma_string entra em
                                                        // escopo.

    uma_string  // uma_string é retornada e movida para a função que chamou
                // pega_e_entrega_valor.
}
```

The ownership of a variable follows the same pattern every time: assigning a
value to another variable moves it. When a variable that includes data on the
heap goes out of scope, the value will be cleaned up by `drop` unless the data
has been moved to be owned by another variable.

A posse de uma variável segue o mesmo padrão toda vez: atribuir um valor a outra
variável irá movê-lo. Quando uma variável que inclui dados na heap sai de
escopo, o valor será destruído pelo método `drop`, a não ser que os dados tenham
sido movidos para outra variável.

Taking ownership and then returning ownership with every function is a bit
tedious. What if we want to let a function use a value but not take ownership?
It’s quite annoying that anything we pass in also needs to be passed back if we
want to use it again, in addition to any data resulting from the body of the
function that we might want to return as well.

Obter e retornar a posse de um valor em cada função é um pouco tedioso. O que
fazer se queremos deixar uma função usar um valor sem tomar posse dele? É meio
irritante saber que qualquer coisa que passemos a uma função também precisa ser
passado de volta se quisermos usá-lo novamente, além de algum possível resultado
proveniente do corpo da função que também queremos retornar.

It’s possible to return multiple values using a tuple, like this:

É possível retornar múltiplos valores usando uma tupla, da seguinte forma:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String.

    (s, length)
}
```

```rust
fn main() {
    let s1 = String::from("texto");

    let (s2, tamanho) = calcula_tamanho(s1);

    println!("O tamanho de '{}' é {}.", s2, tamanho);
}

fn calcula_tamanho(s: String) -> (String, usize) {
    let tamanho = s.len(); // len() retorna o tamanho de uma String.

    (s, tamanho)
}
```

But this is too much ceremony and a lot of work for a concept that should be
common. Luckily for us, Rust has a feature for this concept, and it’s called
*references*.

Mas isto é muita cerimônia e trabalho para um conceito que deveria ser comum.
Para nossa sorte, Rust tem uma ferramenta para este conceito, e ela é chamada de
*referências*.
