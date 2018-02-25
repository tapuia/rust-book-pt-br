## References and Borrowing
## Referências e _Borrowing_

The issue with the tuple code at the end of the preceding section is that we
have to return the `String` to the calling function so we can still use the
`String` after the call to `calculate_length`, because the `String` was moved
into `calculate_length`.

O problema de usar tuplas, que vimos no fim da seção anterior, é que precisamos
retornar a `String`, de forma que ainda possamos usá-la após a chamada à função
`calcula_tamanho`, para dentro da qual a `String` foi movida.

Here is how you would define and use a `calculate_length` function that has a
*reference* to an object as a parameter instead of taking ownership of the
value:

Aqui está uma forma de como você poderia definir e usar uma função
`calcula_tamanho` que recebe uma *referência* para um objeto como parâmetro, em
vez de pegar este valor para si:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

```rust
fn main() {
    let s1 = String::from("texto");

    let tamanho = calcula_tamanho(&s1);

    println!("O tamanho de '{}' é {}.", s1, tamanho);
}

fn calcula_tamanho(s: &String) -> usize {
    s.len()
}
```

First, notice that all the tuple code in the variable declaration and the
function return value is gone. Second, note that we pass `&s1` into
`calculate_length`, and in its definition, we take `&String` rather than
`String`.

Primeiro, repare que todo aquele código usando uma tupla na declaração da
variável e no retorno da função já se foi. Segundo, note que passamos `&s1` para
`calcula_tamanho`, e na sua definição, temos `&String` em vez de apenas
`String`.

These ampersands are *references*, and they allow you to refer to some value
without taking ownership of it. Figure 4-5 shows a diagram.

Esses `&` são *referências*, e eles permitem que você se refira a algum valor
sem tomar posse dele. A Figura 4-5 mostra um diagrama.

<img alt="&String s pointing at String s1" src="img/trpl04-05.svg" class="center" />

<img alt="&String s apontando para String s1" src="img/trpl04-05.svg" class="center" />

<span class="caption">Figure 4-5: `&String s` pointing at `String s1`</span>

<span class="caption">Figura 4-5: `&String s` apontando para `String s1`</span>

> Note: The opposite of referencing by using `&` is *dereferencing*, which is
> accomplished with the dereference operator, `*`. We’ll see some uses of the
> dereference operator in Chapter 8 and discuss details of dereferencing in
> Chapter 15.

> Nota: O oposto de referenciar usando `&` é *derreferenciar*, feito por meio do
> operador derreferenciador, `*`. Veremos alguns usos do operador
> derreferenciador no Capítulo 8 e vamos discutir detalhes da derreferenciação
> no Capítulo 15.

Let’s take a closer look at the function call here:

Vamos olhar mais de perto esta chamada de função:

```rust
# fn calculate_length(s: &String) -> usize {
#     s.len()
# }
let s1 = String::from("hello");

let len = calculate_length(&s1);
```

```rust
# fn calcula_tamanho(s: &String) -> usize {
#     s.len()
# }
let s1 = String::from("texto");

let tamanho = calcula_tamanho(&s1);
```

The `&s1` syntax lets us create a reference that *refers* to the value of `s1`
but does not own it. Because it does not own it, the value it points to will
not be dropped when the reference goes out of scope.

A sintaxe `&s1` nos permite criar uma referência que *se refere* ao valor `s1`,
mas não o possui. Como ela não o possui, o valor a que ela aponta não será
destruído quando a referência sair de escopo.

Likewise, the signature of the function uses `&` to indicate that the type of
the parameter `s` is a reference. Let’s add some explanatory annotations:

Da mesma forma, a assinatura da função usa `&` para indicar que o tipo do
parâmetro `s` é uma referência. Vamos adicionar algumas anotações para explicar:

```rust
fn calculate_length(s: &String) -> usize { // s is a reference to a String
    s.len()
} // Here, s goes out of scope. But because it does not have ownership of what
  // it refers to, nothing happens.
```

```rust
fn calcula_tamanho(s: &String) -> usize { // s é uma referência para uma String
    s.len()
} // Aqui, s sai de escopo. Mas como ela não possui o valor a que se refere,
  // nada acontece.
```

The scope in which the variable `s` is valid is the same as any function
parameter’s scope, but we don’t drop what the reference points to when it goes
out of scope because we don’t have ownership. Functions that have references as
parameters instead of the actual values mean we won’t need to return the values
in order to give back ownership, since we never had ownership.

O escopo no qual a variável `s` é válida é o mesmo escopo de qualquer parâmetro
de função, mas não destruímos o valor apontado pela referência quando ela sai de
escopo, pois ela não tem posse dele. Funções que têm referências como
parâmetros, em vez dos próprios valores, não precisam retornar os valores para
devolver a posse deles, já que nunca tiveram esta posse.

We call having references as function parameters *borrowing*. As in real life,
if a person owns something, you can borrow it from them. When you’re done, you
have to give it back.

Colocar referências como parâmetros de funções é chamado de *borrowing* (do
inglês, empréstimo). Assim como na vida real, se uma pessoa possui alguma coisa,
você pode pegar emprestado dela. Quando você termina de usar, você deve
devolver.

So what happens if we try to modify something we’re borrowing? Try the code in
Listing 4-4. Spoiler alert: it doesn’t work!

E o que acontece se tentarmos modificar alguma coisa que pegamos emprestado?
Tente rodar o código da Listagem 4-4. Alerta de spoiler: não funciona!

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");
}
```

```rust,ignore
fn main() {
    let s = String::from("texto");

    modifica(&s);
}

fn modifica(uma_string: &String) {
    uma_string.push_str(" longo");
}
```

<span class="caption">Listing 4-4: Attempting to modify a borrowed value</span>

<span class="caption">Listagem 4-4: Tentativa de modificar um valor emprestado</span>

Here’s the error:

Aqui está o erro:

```text
error[E0596]: cannot borrow immutable borrowed content `*some_string` as mutable
 --> error.rs:8:5
  |
7 | fn change(some_string: &String) {
  |                        ------- use `&mut String` here to make mutable
8 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^ cannot borrow as mutable
```

```text
error[E0596]: cannot borrow immutable borrowed content `*uma_string` as mutable
 --> main.rs:8:5
  |
7 | fn modifica(uma_string: &String) {
  |                         ------- use `&mut String` here to make mutable
8 |     uma_string.push_str(" longo");
  |     ^^^^^^^^^^ cannot borrow as mutable
```

Just as variables are immutable by default, so are references. We’re not
allowed to modify something we have a reference to.

Assim como as variáveis são imutáveis por padrão, referências também são. Não
temos permissão para modificar algo para o qual temos uma referência.

### Mutable References
### Referências Mutáveis

We can fix the error in the code from Listing 4-4 with just a small tweak:

Podemos corrigir o erro no código da Listagem 4-4 com um pequeno ajuste:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

```rust
fn main() {
    let mut s = String::from("texto");

    modifica(&mut s);
}

fn modifica(uma_string: &mut String) {
    uma_string.push_str(" longo");
}
```

First, we had to change `s` to be `mut`. Then we had to create a mutable
reference with `&mut s` and accept a mutable reference with `some_string: &mut
String`.

Primeiro, temos que fazer com que `s` seja `mut`. Depois, temos que criar uma
referência mutável com `&mut s` e aceitar uma referência mutável com
`uma_string: &mut String`.

But mutable references have one big restriction: you can only have one mutable
reference to a particular piece of data in a particular scope. This code will
fail:

Mas referências mutáveis possuem uma grande restrição: você só pode ter uma
referência mutável para um determinado dado em um determinado escopo. Este
código vai falhar:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;
```

```rust,ignore
let mut s = String::from("texto");

let r1 = &mut s;
let r2 = &mut s;
```

Here’s the error:

Aqui está o erro:

```text
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> borrow_twice.rs:5:19
  |
4 |     let r1 = &mut s;
  |                   - first mutable borrow occurs here
5 |     let r2 = &mut s;
  |                   ^ second mutable borrow occurs here
6 | }
  | - first borrow ends here
```

```text
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> main.rs:5:19
  |
4 |     let r1 = &mut s;
  |                   - first mutable borrow occurs here
5 |     let r2 = &mut s;
  |                   ^ second mutable borrow occurs here
6 | }
  | - first borrow ends here
```

This restriction allows for mutation but in a very controlled fashion. It’s
something that new Rustaceans struggle with, because most languages let you
mutate whenever you’d like. The benefit of having this restriction is that Rust
can prevent data races at compile time.

Esta restrição permite a mutação, mas de uma forma bem controlada. Isto é algo
com que novos Rustáceos passam trabalho, porque a maioria das linguagens de
programação permitem modificar um valor quando você quiser. O benefício de ter
esta restrição é que o Rust previne _data races_ em tempo de compilação. 

A *data race* is similar to a race condition and happens when these three
behaviors occur:

Um *data race* é parecido com uma condição de corrida, e acontece quando esses
três fatores ocorrem:

1. Two or more pointers access the same data at the same time.
1. At least one of the pointers is being used to write to the data.
1. There’s no mechanism being used to synchronize access to the data.

1. Dois ou mais ponteiros acessam o mesmo dado ao mesmo tempo.
1. Ao menos um dos ponteiros é usado para escrever sobre o dado.
1. Não há nenhum mecanismo sendo usado para sincronizar o acesso ao dado.

Data races cause undefined behavior and can be difficult to diagnose and fix
when you’re trying to track them down at runtime; Rust prevents this problem
from happening because it won’t even compile code with data races!

Data races causam comportamento indefinido e pode ser difíceis de diagnosticar
e corrigir quando você está tentando rastreá-los em tempo de execução. Rust
previne este problema de acontecer porque não vai nem deixar compilar um código
com data races!

As always, we can use curly brackets to create a new scope, allowing for
multiple mutable references, just not *simultaneous* ones:

Como sempre, podemos usar chaves (`{}`) para criar um novo escopo, permitindo
múltiplas referências mutáveis, mas não *simultâneas*:

```rust
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // r1 goes out of scope here, so we can make a new reference with no problems.

let r2 = &mut s;
```

```rust
let mut s = String::from("texto");

{
    let r1 = &mut s;

} // aqui r1 sai de escopo, então já podemos criar uma nova referência sem
  // problema nenhum.

let r2 = &mut s;
```

A similar rule exists for combining mutable and immutable references. This code
results in an error:

Existe uma regra parecida para combinar referências mutáveis e imutáveis. Este
código resulta em erro:

```rust,ignore
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
let r3 = &mut s; // BIG PROBLEM
```

```rust,ignore
let mut s = String::from("texto");

let r1 = &s; // sem problema
let r2 = &s; // sem problema
let r3 = &mut s; // PROBLEMA GRANDE
```

Here’s the error:

Aqui está o erro:

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as
immutable
 --> borrow_thrice.rs:6:19
  |
4 |     let r1 = &s; // no problem
  |               - immutable borrow occurs here
5 |     let r2 = &s; // no problem
6 |     let r3 = &mut s; // BIG PROBLEM
  |                   ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as
immutable
 --> main.rs:6:19
  |
4 |     let r1 = &s; // sem problema
  |               - immutable borrow occurs here
5 |     let r2 = &s; // sem problema
6 |     let r3 = &mut s; // PROBLEMA GRANDE
  |                   ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

Whew! We *also* cannot have a mutable reference while we have an immutable one.
Users of an immutable reference don’t expect the values to suddenly change out
from under them! However, multiple immutable references are okay because no one
who is just reading the data has the ability to affect anyone else’s reading of
the data.

Eita! Nós *também* não podemos ter uma referência mutável enquanto temos uma
imutável. Usuários de uma referência imutável não esperam que os valores mudem
de repente! Porém, múltiplas referências imutáveis são permitidas, pois ninguém
que esteja apenas lendo os dados será capaz de afetar a leitura que está sendo
feita em outra parte do código.

Even though these errors may be frustrating at times, remember that it’s the
Rust compiler pointing out a potential bug early (at compile time rather than
at runtime) and showing you exactly where the problem is instead of you having
to track down why sometimes your data isn’t what you thought it should be.

Mesmo que esses erros sejam frustrantes às vezes, lembre-se que é o compilador
do Rust apontando um bug potencial antecipadamente (em tempo de compilação,
em vez de execuçao), e mostrando exatamente onde está o problema, em vez de você
ter que investigar por que algumas vezes os seus dados não são aquilo que você
esperava que fosse.

### Dangling References
### Referências Soltas

In languages with pointers, it’s easy to erroneously create a *dangling
pointer*, a pointer that references a location in memory that may have been
given to someone else, by freeing some memory while preserving a pointer to
that memory. In Rust, by contrast, the compiler guarantees that references will
never be dangling references: if we have a reference to some data, the compiler
will ensure that the data will not go out of scope before the reference to the
data does.

Em linguagens com ponteiros, é fácil criar erroneamente um *ponteiro solto*, um
ponteiro que referencia um local na memória que pode ter sido dado para outra
parte do programa, basta liberar alguma memória e preservar um ponteiro para
ela. Por outro lado, em Rust, o compilador garante que nenhuma referência será
uma referência solta: se temos uma referência para algum dado, o compilador vai
se certificar que esse dado não vai sair de escopo antes da referência.

Let’s try to create a dangling reference, which Rust will prevent with a
compile-time error:

Vamos tentar criar uma referência solta, que o Rust vai impedir com um erro em
tempo de compilação:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

```rust,ignore
fn main() {
    let referencia_para_o_nada = soltar();
}

fn soltar() -> &String {
    let s = String::from("texto");

    &s
}
```

Here’s the error:

Aqui está o erro:

```text
error[E0106]: missing lifetime specifier
 --> dangle.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^ expected lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is
  no value for it to be borrowed from
  = help: consider giving it a 'static lifetime
```

```text
error[E0106]: missing lifetime specifier
 --> main.rs:5:16
  |
5 | fn soltar() -> &String {
  |                ^ expected lifetime parameter
  |
  = help: this function's return type contains a borrowed value, but there is
  no value for it to be borrowed from
  = help: consider giving it a 'static lifetime
```

This error message refers to a feature we haven’t covered yet: *lifetimes*.
We’ll discuss lifetimes in detail in Chapter 10. But, if you disregard the
parts about lifetimes, the message does contain the key to why this code is a
problem:

Esta mensagem de erro se refere a uma característica que não abordamos ainda:
*lifetimes*. Vamos discutir lifetimes em detalhe no Capítulo 10. Mas, se você
desconsiderar a parte sobre lifetimes, a mensagem mostra a razão deste código
ser um problema:

```text
this function's return type contains a borrowed value, but there is no value
for it to be borrowed from.
```

> Tradução: o tipo de retorno desta função contém um valor emprestado, mas não
> há nenhum valor que se possa pegar emprestado.

Let’s take a closer look at exactly what’s happening at each stage of our
`dangle` code:

Vamos dar uma olhada mais de perto no que está acontecendo, exatamente, em cada
estágio da nossa função `soltar`:

```rust,ignore
fn dangle() -> &String { // dangle returns a reference to a String

    let s = String::from("hello"); // s is a new String

    &s // we return a reference to the String, s
} // Here, s goes out of scope, and is dropped. Its memory goes away.
  // Danger!
```

```rust,ignore
fn soltar() -> &String { // soltar retorna uma referência a uma String

    let s = String::from("texto"); // s é uma nova String

    &s // retornamos uma referência a uma String, s
} // Aqui, s sai de escopo e é destruída. Sua memória é devolvida.
  // Perigo!
```

Because `s` is created inside `dangle`, when the code of `dangle` is finished,
`s` will be deallocated. But we tried to return a reference to it. That means
this reference would be pointing to an invalid `String`! That’s no good. Rust
won’t let us do this.

Como `s` é criada dentro da função `soltar`, quando o código desta função
termina, `s` é desalocada. Mas nós tentamos retornar uma referência para ela.
Isto significa que esta referência apontaria para uma `String` inválida! Isso
não é bom. Rust não vai nos deixar fazer isso.

The solution here is to return the `String` directly:

A solução aqui é retornar a `String` diretamente:

```rust
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

```rust
fn não_soltar() -> String {
    let s = String::from("texto");

    s
}
```

This works without any problems. Ownership is moved out, and nothing is
deallocated.

Isto funciona sem nenhum problema. A `String` é movida para fora, e nada é
desalocado.

### The Rules of References
### As Regras de Referências

Let’s recap what we’ve discussed about references:

Vamos recapitular o que discutimos sobre referências:

1. At any given time, you can have *either* but not both of:
  * One mutable reference.
  * Any number of immutable references.
2. References must always be valid.

1. Em um dado momento, você pode ter *um ou outro*, mas não os dois:
  * Uma referência mutável.
  * Qualquer número de referências imutáveis.
2. Referências devem ser válidas sempre.

Next, we’ll look at a different kind of reference: slices.

Em seguida, vamos ver um tipo diferente de referências: _slices_.
