# Guessing Game
# Jogo de Adivinhação

Let’s jump into Rust by working through a hands-on project together! This
chapter introduces you to a few common Rust concepts by showing you how to use
them in a real program. You’ll learn about `let`, `match`, methods, associated
functions, using external crates, and more! The following chapters will explore
these ideas in more detail. In this chapter, you’ll practice the fundamentals.

Vamos entrar de cabeça no Rust e colocar a mão na massa! Este capítulo vai lhe
apresentar alguns conceitos bem comuns no Rust, mostrando como usá-los em um
programa de verdade. Você vai aprender sobre `let`, `match`, métodos, funções
associadas, crates externos, e mais! Os capítulos seguintes vão explorar essas
ideias em mais detalhes. Neste capítulo, você vai praticar o básico.

We’ll implement a classic beginner programming problem: a guessing game. Here’s
how it works: the program will generate a random integer between 1 and 100. It
will then prompt the player to enter a guess. After entering a guess, it will
indicate whether the guess is too low or too high. If the guess is correct, the
game will print congratulations and exit.

Vamos implementar um clássico problema de programação para iniciantes: um jogo
de adivinhação. Eis como ele funciona: o programa vai gerar um número inteiro
aleatório entre 1 e 100. Então, ele vai pedir ao jogador que digite um palpite.
Após darmos nosso palpite, ele vai nos indicar se o palpite é muito baixo ou
muito alto. Uma vez que o palpite estiver correto, ele vai nos dar os parabéns e
sair.

## Setting Up a New Project
## Preparando um Novo Projeto

To set up a new project, go to the *projects* directory that you created in
Chapter 1, and make a new project using Cargo, like so:

Para iniciar um novo projeto, vá ao seu diretório de projetos que você criou no
Capítulo 1, e execute os comandos do Cargo a seguir:

```text
$ cargo new guessing_game --bin
$ cd guessing_game
```

```text
$ cargo new jogo_de_advinhacao --bin
$ cd jogo_de_advinhacao
```

The first command, `cargo new`, takes the name of the project (`guessing_game`)
as the first argument. The `--bin` flag tells Cargo to make a binary project,
similar to the one in Chapter 1. The second command changes to the new
project’s directory.

O primeiro comando, `cargo new`, recebe o nome do projeto (`jogo_de_advinhacao`)
como primeiro argumento. A flag `--bin` diz ao Cargo que faça um projeto
binário, similar ao do Capítulo 1. O segundo comando muda a pasta atual para o
diretório do projeto.

Look at the generated *Cargo.toml* file:

Confira o arquivo *Cargo.toml* gerado:

<span class="filename">Filename: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]

[dependencies]
```

<span class="filename">Arquivo: Cargo.toml</span>

```toml
[package]
name = "jogo_de_advinhacao"
version = "0.1.0"
authors = ["Seu Nome <voce@exemplo.com>"]

[dependencies]
```

If the author information that Cargo obtained from your environment is not
correct, fix that in the file and save it again.

Se as informações sobre o autor, que o Cargo obtém do seu ambiente, não
estiverem corretas, faça os reparos necessários e salve o arquivo.

As you saw in Chapter 1, `cargo new` generates a “Hello, world!” program for
you. Check out the *src/main.rs* file:

Assim como no Capítulo 1, `cargo new` gera um programa "Hello, world!" para nós.
Confira em *src/main.rs*:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Now let’s compile this “Hello, world!” program and run it in the same step
using the `cargo run` command:

Agora vamos compilar esse programa "Hello, world!" e executá-lo de uma vez só
usando o comando `cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Hello, world!
```

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/jogo_de_advinhacao`
Hello, world!
```

The `run` command comes in handy when you need to rapidly iterate on a project,
and this game is such a project: we want to quickly test each iteration
before moving on to the next one.

O comando `run` é uma boa opção quando precisamos iterar rapidamente em um
projeto, que é o caso neste jogo: nós queremos testar rapidamente cada iteração
antes de movermos para a próxima.

Reopen the *src/main.rs* file. You’ll be writing all the code in this file.

Abra novamente o arquivo *src/main.rs*. Escreveremos todo nosso código nele.

## Processing a Guess
## Processando um Palpite

The first part of the program will ask for user input, process that input, and
check that the input is in the expected form. To start, we’ll allow the player
to input a guess. Enter the code in Listing 2-1 into *src/main.rs*.

A primeira parte do programa vai pedir uma entrada ao usuário, processar essa
entrada, e conferir se ela está no formato esperado. Pra começar, vamos permitir
que o jogador entre com um palpite. Coloque este código no arquivo
*src/main.rs*:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listing 2-1: Code to get a guess from the user and print
it out</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Advinhe o número!");

    println!("Digite o seu palpite.");

    let mut palpite = String::new();

    io::stdin().read_line(&mut palpite)
        .expect("Falha ao ler entrada");

    println!("Você disse: {}", palpite);
}
```

<span class="caption">Listagem 2-1: Código para ler um palpite do usuário e
imprimí-lo na tela.</span>

This code contains a lot of information, so let’s go over it bit by bit. To
obtain user input and then print the result as output, we need to bring the
`io` (input/output) library into scope. The `io` library comes from the
standard library (which is known as `std`):

Esse código tem muita informação, vamos ver uma parte de cada vez. Para obter a
entrada do usuário, e então imprimir o resultado como saída, precisaremos trazer
ao escopo a biblioteca `io` (de entrada/saída). A biblioteca `io` provém da
biblioteca padrão (chamada de `std`):

```rust,ignore
use std::io;
```

By default, Rust brings only a few types into the scope of every program in
[the *prelude*][prelude]<!-- ignore -->. If a type you want to use isn’t in the
prelude, you have to bring that type into scope explicitly with a `use`
statement. Using the `std::io` library provides you with a number of useful
`io`-related features, including the functionality to accept user input.

Por padrão, o Rust traz apenas alguns tipos para o escopo de todos os programas
no [*prelúdio*][prelude]<!-- ignore -->. Se um tipo que você quiser usar não
estiver no prelúdio, você terá que importá-lo explicitamente através do `use`.
A biblioteca `std::io` oferece várias ferramentas de entrada/saída, incluindo a
funcionalidade de ler dados de entrada do usuário.

[prelude]: ../../std/prelude/index.html

As you saw in Chapter 1, the `main` function is the entry point into the
program:

Como visto no Capítulo 1, a função `main` é o ponto de entrada do programa:

```rust,ignore
fn main() {
```

The `fn` syntax declares a new function, the `()` indicate there are no
parameters, and `{` starts the body of the function.

A sintaxe `fn` declara uma nova função, o `()` indica que não há parâmetros, e
o `{` inicia o corpo da função.

As you also learned in Chapter 1, `println!` is a macro that prints a string to
the screen:

Como você também já aprendeu no Capítulo 1, `println!` é uma macro que imprime
uma string na tela:

```rust,ignore
println!("Guess the number!");

println!("Please input your guess.");
```

```rust,ignore
println!("Advinhe o número!");

println!("Digite o seu palpite.");
```

This code is printing a prompt stating what the game is and requesting input
from the user.

Este código está exibindo uma mensagem que diz de que se trata o jogo e solicita
uma entrada do usuário.  

### Storing Values with Variables
### Armazenando Valores em Variáveis

Next, we’ll create a place to store the user input, like this:

Próximo passo, vamos criar um local para armazenar a entrada do usuário:

```rust,ignore
let mut guess = String::new();
```

```rust,ignore
let mut palpite = String::new();
```

Now the program is getting interesting! There’s a lot going on in this little
line. Notice that this is a `let` statement, which is used to create
*variables*. Here’s another example:

Agora o programa está ficando interessante! Tem muita coisa acontecendo nesta
pequena linha. Repare que esta é uma declaração `let`, que é usada para criar
*variáveis*. Segue outro exemplo:

```rust,ignore
let foo = bar;
```

This line will create a new variable named `foo` and bind it to the value
`bar`. In Rust, variables are immutable by default. The following example shows
how to use `mut` before the variable name to make a variable mutable:

Essa linha cria uma nova variável chamada `foo`, e a vincula ao valor `bar`. Em
Rust, variáveis são imutáveis por padrão. O exemplo a seguir mostra como usar
`mut` antes do nome da variável para torná-la mutável:

```rust
let foo = 5; // immutable
let mut bar = 5; // mutable
```

```rust
let foo = 5; // imutável
let mut bar = 5; // mutável
```

> Note: The `//` syntax starts a comment that continues until the end of the
> line. Rust ignores everything in comments.

> Nota: A sintaxe `//` inicia um comentário, que continua até o fim da linha.
> Rust ignora tudo o que estiver nos comentários.

Now you know that `let mut guess` will introduce a mutable variable named
`guess`. On the other side of the equal sign (`=`) is the value that `guess` is
bound to, which is the result of calling `String::new`, a function that returns
a new instance of a `String`. [`String`][string]<!-- ignore --> is a string
type provided by the standard library that is a growable, UTF-8 encoded bit of
text.

Agora você sabe que `let mut palpite` vai introduzir uma variável mutável de
nome `palpite`. No outro lado do símbolo `=` está o valor ao qual `palpite` está
vinculado, que é o resultado da chamada `String::new`, uma função que retorna
uma nova instância de `String`. [`String`][string]<!-- ignore --> é um tipo
fornecido pela biblioteca padrão que representa uma cadeia expansível de
caracteres codificados em UTF-8.

[string]: ../../std/string/struct.String.html

The `::` syntax in the `::new` line indicates that `new` is an *associated
function* of the `String` type. An associated function is implemented on a type,
in this case `String`, rather than on a particular instance of a `String`. Some
languages call this a *static method*.

A sintaxe `::` na linha `::new` indica que `new` é uma *função associada* do
tipo `String`. Uma função associada é implementada sobre um tipo, neste caso
`String`, em vez de uma instância particular de `String`. Algumas linguagens
dão a isso o nome *método estático*.

This `new` function creates a new, empty `String`. You’ll find a `new` function
on many types, because it’s a common name for a function that makes a new value
of some kind.

Esta função `new()` cria uma nova `String` vazia. Você encontrará uma função
`new()` em muitos tipos, já que é um nome comum para uma função que produz um
novo valor de algum tipo.

To summarize, the `let mut guess = String::new();` line has created a mutable
variable that is currently bound to a new, empty instance of a `String`. Whew!

Para resumir, a linha `let mut palpite = String::new();` criou uma variável
mutável que está atualmente vinculada a uma nova instância vazia de uma
`String`. Ufa!

Recall that we included the input/output functionality from the standard
library with `use std::io;` on the first line of the program. Now we’ll call an
associated function, `stdin`, on `io`:

Lembre-se de que incluímos a funcionalidade de entrada/saída da biblioteca
padrão por meio do `use std::io;` na primeira linha do programa. Agora vamos
chamar uma função associada, `stdin`, em `io`:

```rust,ignore
io::stdin().read_line(&mut guess)
    .expect("Failed to read line");
```

```rust,ignore
io::stdin().read_line(&mut palpite)
    .expect("Falha ao ler entrada");
```

If we didn’t have the `use std::io` line at the beginning of the program, we
could have written this function call as `std::io::stdin`. The `stdin` function
returns an instance of [`std::io::Stdin`][iostdin]<!-- ignore -->, which is a
type that represents a handle to the standard input for your terminal.

Se não tivéssemos a linha `use std::io` no início do programa, poderíamos ter
escrito esta chamada como `std::io::stdin`. A função `stdin` retorna uma
instância de [`std::io::Stdin`][iostdin]<!-- ignore -->, um tipo que representa
um manipulador (_handle_) da entrada padrão do seu terminal.

[iostdin]: ../../std/io/struct.Stdin.html

The next part of the code, `.read_line(&mut guess)`, calls the
[`read_line`][read_line]<!-- ignore --> method on the standard input handle to
get input from the user. We’re also passing one argument to `read_line`: `&mut
guess`.

A próxima parte do código, `.read_line(&mut palpite)`, chama o método
[`read_line`][read_line]<!-- ignore --> do _handle_ da entrada padrão para obter
entrada do usuário. Também estamos passando um argumento para `read_line`:
`&mut palpite`.

[read_line]: ../../std/io/struct.Stdin.html#method.read_line

The job of `read_line` is to take whatever the user types into standard input
and place that into a string, so it takes that string as an argument. The
string argument needs to be mutable so the method can change the string’s
content by adding the user input.

O trabalho da função `read_line` é receber o que o usuário digita na entrada
padrão e colocar isso numa string, por isso ela recebe essa string como
argumento. A string do argumento deve ser mutável para que o método consiga
alterar o seu conteúdo, adicionando a entrada do usuário.

The `&` indicates that this argument is a *reference*, which gives you a way to
let multiple parts of your code access one piece of data without needing to
copy that data into memory multiple times. References are a complex feature,
and one of Rust’s major advantages is how safe and easy it is to use
references. You don’t need to know a lot of those details to finish this
program: Chapter 4 will explain references more thoroughly. For now, all you
need to know is that like variables, references are immutable by default.
Hence, we need to write `&mut guess` rather than `&guess` to make it mutable.

O símbolo `&` indica que o argumento é uma *referência*, o que permite múltiplas
partes do seu código acessar um certo dado sem precisar criar várias cópias dele
na memória. Referências são uma característica complexa, e uma das maiores
vantagens do Rust é o quão fácil e seguro é usar referências. Você não precisa
conhecer muitos desses detalhes para finalizar esse programa. O Capítulo 4 vai
explicar sobre referências de forma mais aprofundada. Por enquanto, tudo que
você precisa saber é que, assim como as variáveis, referências são imutáveis por
padrão. Por isso, precisamos escrever `&mut palpite`, em vez de apenas
`&palpite`, para fazer com que o palpite seja mutável.

We’re not quite done with this line of code. Although it’s a single line of
text, it’s only the first part of the single logical line of code. The second
part is this method:

Ainda não finalizamos completamente esta linha de código. Embora esta seja uma
única linha de texto, é apenas a primeira parte de uma linha lógica de código. A
segunda parte é a chamada para este método:

```rust,ignore
.expect("Failed to read line");
```

```rust,ignore
.expect("Falha ao ler entrada");
```

When you call a method with the `.foo()` syntax, it’s often wise to introduce a
newline and other whitespace to help break up long lines. We could have
written this code as:

Quando você chama um método com a sintaxe `.foo()`, geralmente é bom introduzir
uma nova linha e outro espaço para ajudar a dividir linhas muito compridas.
Poderíamos ter feito assim:

```rust,ignore
io::stdin().read_line(&mut palpite).expect("Falha ao ler entrada");
```

However, one long line is difficult to read, so it’s best to divide it, two
lines for two method calls. Now let’s discuss what this line does.

Porém, uma linha muito comprida fica difícil de ler. Então é melhor dividirmos a
linha em duas, uma para cada método chamado. Agora vamos falar sobre o que essa
linha faz.

### Handling Potential Failure with the `Result` Type
### Tratando Potenciais Falhas com o Tipo `Result`

As mentioned earlier, `read_line` puts what the user types into the string we’re
passing it, but it also returns a value—in this case, an
[`io::Result`][ioresult]<!-- ignore -->. Rust has a number of types named
`Result` in its standard library: a generic [`Result`][result]<!-- ignore --> as
well as specific versions for submodules, such as `io::Result`.

Como mencionado anteriormente, `read_line` coloca o que o usuário escreve dentro
da string que passamos como argumento, mas também retorna um valor - neste
caso, um [`io::Result`][ioresult]<!-- ignore -->. Rust tem uma variedade de
tipos com o nome `Result` em sua biblioteca padrão: um [`Result`][result]
genérico e as versões específicas dos submódulos, como `io::Result`.

[ioresult]: ../../std/io/type.Result.html
[result]: ../../std/result/enum.Result.html

The `Result` types are [*enumerations*][enums]<!-- ignore -->, often referred
to as *enums*. An enumeration is a type that can have a fixed set of values,
and those values are called the enum’s *variants*. Chapter 6 will cover enums
in more detail.

Os tipos `Result` são [*enumerações*][enums]<!-- ignore -->, comumente chamadas
de *enums*. Uma enumeração é um tipo que pode ter um conjunto fixo de valores,
os quais são chamados de *variantes* da enum. O Capítulo 6 vai abordar enums em
mais detalhes.

[enums]: ch06-00-enums.html

For `Result`, the variants are `Ok` or `Err`. `Ok` indicates the operation was
successful, and inside the `Ok` variant is the successfully generated value.
`Err` means the operation failed, and `Err` contains information about how or
why the operation failed.

Para `Result`, as variantes são `Ok` ou `Err`. `Ok` indica que a operação teve
sucesso, e dentro da variante `Ok` está o valor resultante. `Err` significa que
a operação falhou, e contém informações sobre como ou por que isso ocorreu.

The purpose of these `Result` types is to encode error handling information.
Values of the `Result` type, like any type, have methods defined on them. An
instance of `io::Result` has an [`expect` method][expect]<!-- ignore --> that
you can call. If this instance of `io::Result` is an `Err` value, `expect` will
cause the program to crash and display the message that you passed as an
argument to `expect`. If the `read_line` method returns an `Err`, it would
likely be the result of an error coming from the underlying operating system.
If this instance of `io::Result` is an `Ok` value, `expect` will take the
return value that `Ok` is holding and return just that value to you so you
could use it. In this case, that value is the number of bytes in what the user
entered into standard input.

O propósito destes tipos `Result` é codificar informações de manipulação de
erros. Valores do tipo `Result`, assim como qualquer tipo, possuem métodos
definidos. Uma instância de `io::Result` tem um [método `expect`][expect]<!-- ignore -->
que você pode chamar. Se esta instância de `io::Result` é um `Err`, `expect` vai
terminar o programa com erro e mostrar a mensagem que você passou como argumento
ao `expect`. Se o método `read_line` retornar um `Err`, provavelmente seria o
resultado de um erro vindo do sistema operacional que está por trás. Se esta
instância de `io::Result` é um `Ok`, `expect` vai obter o valor contido no `Ok`
e retorná-lo para que você possa usá-lo. Neste caso, o valor é o número de bytes
dos dados que o usuário inseriu através da entrada padrão.

[expect]: ../../std/result/enum.Result.html#method.expect

If we don’t call `expect`, the program will compile, but we’ll get a warning:

Se não chamarmos `expect`, nosso programa vai compilar, mas vamos ter um aviso:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
warning: unused `std::result::Result` which must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: #[warn(unused_must_use)] on by default
```

```text
$ cargo build
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
warning: unused `std::result::Result` which must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut palpite);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: #[warn(unused_must_use)] on by default
```

Rust warns that we haven’t used the `Result` value returned from `read_line`,
indicating that the program hasn’t handled a possible error. The right way to
suppress the warning is to actually write error handling, but since we want to
crash this program when a problem occurs, we can use `expect`. You’ll learn
about recovering from errors in Chapter 9.

Rust avisa que não usamos o valor `Result`, retornado por `read_line`, indicando
que o programa deixou de tratar um possível erro. A maneira correta de suprimir
o aviso é realmente escrevendo um tratador de erro, mas como queremos que o
programa seja encerrado caso ocorra um problema, podemos usar `expect`. Você
aprenderá sobre recuperação de erros no Capítulo 9.

### Printing Values with `println!` Placeholders
### Exibindo Valores com Curingas do `println!`

Aside from the closing curly brackets, there’s only one more line to discuss in
the code added so far, which is the following:

Tirando a chave que delimita a função `main`, há apenas uma linha mais a ser
discutida no código que fizemos até agora, que é a seguinte:

```rust,ignore
println!("You guessed: {}", guess);
```

```rust,ignore
println!("Você disse: {}", guess);
```

This line prints out the string we saved the user’s input in. The set of `{}`
is a placeholder that holds a value in place. You can print more than one value
using `{}`: the first set of `{}` holds the first value listed after the format
string, the second set holds the second value, and so on. Printing out multiple
values in one call to `println!` would look like this:

Esta linha imprime a string na qual salvamos os dados inseridos pelo usuário. O
`{}` é um curinga que reserva o lugar de um valor. Você pode imprimir mais de um
valor usando `{}`: o primeiro conjunto de `{}` guarda o primeiro valor listado
após a string de formatação, o segundo conjunto guarda o segundo valor, e
assim por diante. Imprimir múltiplos valores em uma só chamada a `println!`
seria assim:

```rust
let x = 5;
let y = 10;

println!("x = {} and y = {}", x, y);
```

```rust
let x = 5;
let y = 10;

println!("x = {} e y = {}", x, y);
```

This code would print out `x = 5 and y = 10`.

Esse código imprime `x = 5 e y = 10`.

### Testing the First Part
### Testando a Primeira Parte

Let’s test the first part of the guessing game. You can run it using
`cargo run`:

Vamos testar a primeira parte do jogo de advinhação. Você pode executá-lo usando
`cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/jogo_de_advinhacao`
Advinhe o número!
Digite o seu palpite.
6
Você disse: 6
```

At this point, the first part of the game is done: we’re getting input from the
keyboard and then printing it.

Nesse ponto, a primeira parte do jogo está feita: podemos coletar entrada do
teclado e mostrá-la na tela.

## Generating a Secret Number
## Gerando um Número Secreto

Next, we need to generate a secret number that the user will try to guess. The
secret number should be different every time so the game is fun to play more
than once. Let’s use a random number between 1 and 100 so the game isn’t too
difficult. Rust doesn’t yet include random number functionality in its standard
library. However, the Rust team does provide a [`rand` crate][randcrate].

A seguir, precisamos gerar um número secreto que o usuário vai tentar advinhar.
O número secreto deve ser diferente a cada execução, para que o jogo tenha graça
em ser jogado mais de uma vez. Vamos usar um número aleatório entre 1 e 100,
para que o jogo não seja tão difícil. Rust ainda não inclui uma funcionalidade
de geração de números aleatórios em sua biblioteca padrão. Porém, a equipe Rust
fornece um [crate `rand`][randcrate].

[randcrate]: https://crates.io/crates/rand

### Using a Crate to Get More Functionality
### Usando um Crate para Ter Mais Funcionalidades

Remember that a *crate* is a package of Rust code. The project we’ve been
building is a *binary crate*, which is an executable. The `rand` crate is a
*library crate*, which contains code intended to be used in other programs.

Lembre-se que um *crate* é um pacote de código Rust. O projeto que estamos
construindo é um *crate binário*, que é um executável. Já o `rand` é um
*crate de biblioteca*, que contém código cujo objetivo é ser usado por outros
programas.

Cargo’s use of external crates is where it really shines. Before we can write
code that uses `rand`, we need to modify the *Cargo.toml* file to include the
`rand` crate as a dependency. Open that file now and add the following line to
the bottom beneath the `[dependencies]` section header that Cargo created for
you:

É no uso de crates externos que Cargo realmente brilha. Antes que possamos
escrever o código usando `rand`, precisamos modificar o arquivo *Cargo.toml*
para incluir o crate `rand` como uma dependência. Abra o arquivo e adicione
esta linha no final, abaixo do cabeçalho da seção `[dependencies]` que o Cargo
criou para você:

<span class="filename">Filename: Cargo.toml</span>

<span class="filename">Arquivo: Cargo.toml</span>

```toml
[dependencies]

rand = "0.3.14"
```

In the *Cargo.toml* file, everything that follows a header is part of a section
that continues until another section starts. The `[dependencies]` section is
where you tell Cargo which external crates your project depends on and which
versions of those crates you require. In this case, we’ll specify the `rand`
crate with the semantic version specifier `0.3.14`. Cargo understands [Semantic
Versioning][semver]<!-- ignore --> (sometimes called *SemVer*), which is a
standard for writing version numbers. The number `0.3.14` is actually shorthand
for `^0.3.14`, which means “any version that has a public API compatible with
version 0.3.14.”

[semver]: http://semver.org

No arquivo *Cargo.toml*, tudo que vem depois de um cabeçalho é parte de uma
seção que segue até o início de outra. A seção `[dependencies]` é onde você diz
ao Cargo de quais crates externos o seu projeto depende, e quais versões desses
crates você exige. Neste caso, especificamos o crate `rand` com a versão
semântica `0.3.14`. Cargo compreende [Versionamento Semântico][semver]<!-- ignore -->
(às vezes chamado *SemVer*), um padrão para escrever números de versões. O
número `0.3.14` é, na verdade, uma forma curta de escrever `^0.3.14`, que
significa "qualquer versão que tenha uma API pública compatível com a versão
0.3.14".

[semver]: https://semver.org/lang/pt-BR/

Now, without changing any of the code, let’s build the project, as shown in
Listing 2-2:

Agora, sem mudar código algum, vamos compilar nosso projeto, conforme mostrado
na Listagem 2-2:

```text
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

<span class="caption">Listing 2-2: The output from running `cargo build` after
adding the rand crate as a dependency</span>

```text
$ cargo build
    Updating registry `https://github.com/rust-lang/crates.io-index`
 Downloading rand v0.3.14
 Downloading libc v0.2.14
   Compiling libc v0.2.14
   Compiling rand v0.3.14
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

<span class="caption">Listagem 2-2: Resultado da execução de `cargo build`
depois de adicionar o crate `rand` como dependência.</span>

You may see different version numbers (but they will all be compatible with
the code, thanks to SemVer!), and the lines may be in a different order.

Talvez pra você apareçam versões diferentes (mas elas são todas compatíveis com
o código, graças ao Versionamento Semântico!), e as linhas talvez apareçam em
ordem diferente.

Now that we have an external dependency, Cargo fetches the latest versions of
everything from the *registry*, which is a copy of data from
[Crates.io][cratesio]. Crates.io is where people in the Rust ecosystem post
their open source Rust projects for others to use.

Agora que temos uma dependência externa, Cargo busca as versões mais recentes de
tudo no *registro*, que é uma cópia dos dados do [Crates.io][cratesio].
Crates.io é onde as pessoas do ecossistema Rust postam seus projetos
_open source_ para que os outros possam usar.

[cratesio]: https://crates.io

After updating the registry, Cargo checks the `[dependencies]` section and
downloads any you don’t have yet. In this case, although we only listed `rand`
as a dependency, Cargo also grabbed a copy of `libc`, because `rand` depends on
`libc` to work. After downloading them, Rust compiles them and then compiles
the project with the dependencies available.

Após atualizar o registro, Cargo verifica a seção `[dependencies]` e baixa todas
as que você não tem ainda. Neste caso, embora tenhamos listado apenas `rand`
como dependência, o Cargo também puxou uma cópia da `libc`, porque `rand`
depende da `libc` para funcionar. Depois de baixá-las, o Cargo as compila e
então compila nosso projeto.

If you immediately run `cargo build` again without making any changes, you won’t
get any output. Cargo knows it has already downloaded and compiled the
dependencies, and you haven’t changed anything about them in your *Cargo.toml*
file. Cargo also knows that you haven’t changed anything about your code, so it
doesn’t recompile that either. With nothing to do, it simply exits. If you open
up the *src/main.rs* file, make a trivial change, then save it and build again,
you’ll only see two lines of output:

Se, logo em seguida, você executar `cargo build` novamente sem fazer mudanças,
não vai aparecer nenhuma mensagem de saída. O Cargo sabe que já baixou e
compilou as dependências, e você não alterou mais nada sobre elas no seu arquivo
*Cargo.toml*. Cargo também sabe que você não mudou mais nada no seu código, e
por isso não o recompila. Sem nada a fazer, ele simplesmente sai. Se você abrir
*src/main.rs*, fizer uma modificação trivial, salvar e compilar de novo, vai
aparecer uma mensagem de apenas duas linhas:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

```text
$ cargo build
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
```

These lines show Cargo only updates the build with your tiny change to the
*src/main.rs* file. Your dependencies haven’t changed, so Cargo knows it can
reuse what it has already downloaded and compiled for those. It just rebuilds
your part of the code.

Essas linhas mostram que o Cargo só atualiza o _build_ com a sua pequena mudança
no arquivo *src/main.rs*. Suas dependências não mudaram, então o Cargo sabe que
pode reutilizar o que já tiver sido baixado e compilado para elas. Ele apenas
recompila a sua parte do código.

#### The *Cargo.lock* File Ensures Reproducible Builds
#### O Arquivo *Cargo.lock* Garante _Builds_ Reproduzíveis

Cargo has a mechanism that ensures you can rebuild the same artifact every time
you or anyone else builds your code: Cargo will use only the versions of the
dependencies you specified until you indicate otherwise. For example, what
happens if next week version `v0.3.15` of the `rand` crate comes out and
contains an important bug fix but also contains a regression that will break
your code?

O Cargo tem um mecanismo que assegura que você pode reconstruir o mesmo artefato
toda vez que você ou outra pessoa compilar o seu código. O Cargo vai usar apenas
as versões das dependências que você especificou, até que você indique o
contrário. Por exemplo, o que acontece se, na semana que vem, sair a versão
`v0.3.15` contendo uma correção de bug, mas também uma regressão que não
funciona com o seu código?

The answer to this problem is the *Cargo.lock* file, which was created the
first time you ran `cargo build` and is now in your *guessing_game* directory.
When you build a project for the first time, Cargo figures out all the
versions of the dependencies that fit the criteria and then writes them to
the *Cargo.lock* file. When you build your project in the future, Cargo will
see that the *Cargo.lock* file exists and use the versions specified there
rather than doing all the work of figuring out versions again. This lets you
have a reproducible build automatically. In other words, your project will
remain at `0.3.14` until you explicitly upgrade, thanks to the *Cargo.lock*
file.

A resposta para isso está no arquivo *Cargo.lock*, que foi criado na primeira
vez que você executou `cargo build`, e agora está no seu diretório
*jogo_de_advinhacao*. Quando você compila o seu projeto pela primeira vez, o
Cargo descobre as versões de todas as dependências que preenchem os critérios
e então as escreve no arquivo *Cargo.lock*. Quando você compilar o seu projeto
futuramente, o Cargo verá que o arquivo *Cargo.lock* existe e usará as versões
especificadas lá, em vez de refazer todo o trabalho descobrir as versões
novamente. Isto lhe permite ter um _build_ reproduzível automaticamente. Em
outras palavras, seu projeto vai continuar com a versão `0.3.14` até que você
faça uma atualização explícita, graças ao arquivo *Cargo.lock*.

#### Updating a Crate to Get a New Version
#### Atualizando um Crate para Obter uma Nova Versão

When you *do* want to update a crate, Cargo provides another command, `update`,
which will:

Quando você *quiser* atualizar um crate, o Cargo tem outro comando, `update`,
que faz o seguinte:

1. Ignore the *Cargo.lock* file and figure out all the latest versions that fit
your specifications in *Cargo.toml*.
1. If that works, Cargo will write those versions to the *Cargo.lock* file.

1. Ignora o arquivo *Cargo.lock* e descobre todas as versões mais recentes que
   atendem as suas especificações no *Cargo.toml*.
1. Se funcionar, o Cargo escreve essas versões no arquivo *Cargo.lock*.

But by default, Cargo will only look for versions larger than `0.3.0` and
smaller than `0.4.0`. If the `rand` crate has released two new versions,
`0.3.15` and `0.4.0`, you would see the following if you ran `cargo update`:

Mas, por padrão, o Cargo vai procurar as versões maiores que `0.3.0` e menores
que `0.4.0`. Se o crate `rand` já tiver lançado duas novas versões, `0.3.15` e
`0.4.0`, você verá a seguinte mensagem ao executar `cargo update`:

```text
$ cargo update
    Updating registry `https://github.com/rust-lang/crates.io-index`
    Updating rand v0.3.14 -> v0.3.15
```

At this point, you would also notice a change in your *Cargo.lock* file noting
that the version of the `rand` crate you are now using is `0.3.15`.

Nesse ponto, você vai notar também uma mudança no seu arquivo *Cargo.lock*
dizendo que a versão do crate `rand` que você está usando agora é a `0.3.15`. 

If you wanted to use `rand` version `0.4.0` or any version in the `0.4.x`
series, you’d have to update the *Cargo.toml* file to look like this instead:

Se você quisesse usar a versão `0.4.0`, ou qualquer versão da série `0.4.x` do
`rand`, você teria que atualizar o seu *Cargo.toml* dessa forma:

```toml
[dependencies]

rand = "0.4.0"
```

The next time you run `cargo build`, Cargo will update the registry of crates
available and reevaluate your `rand` requirements according to the new version
you specified.

Na próxima vez que você executar `cargo build`, o Cargo vai atualizar o registro
de crates disponíveis e reavaliar os seus requisitos sobre o `rand` de acordo
com a nova versão que você especificou.

There’s a lot more to say about [Cargo][doccargo]<!-- ignore --> and [its
ecosystem][doccratesio]<!-- ignore --> that Chapter 14 will discuss, but for
now, that’s all you need to know. Cargo makes it very easy to reuse libraries,
so Rustaceans are able to write smaller projects that are assembled from a
number of packages.

Há muito mais a ser dito sobre [Cargo][doccargo]<!-- ignore --> e o [seu
ecossistema][doccratesio]<!-- ignore --> que vai ser discutido no Capítulo 14,
mas por ora isto é tudo que você precisa saber. Cargo facilita muito reutilizar
bibliotecas, de forma que os _rustáceos_ consigam escrever projetos menores que
são montados a partir de diversos pacotes.

[doccargo]: http://doc.crates.io
[doccratesio]: http://doc.crates.io/crates-io.html

### Generating a Random Number
### Gerando um Número Aleatório

Let’s start *using* `rand`. The next step is to update *src/main.rs*, as shown
in Listing 2-3:

Agora vamos *usar*, de fato, o `rand`. O próximo passo é atualizar o
*src/main.rs* conforme mostrado na Listagem 2-3:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listing 2-3: Code changes needed in order to generate a
random number</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    println!("O número secreto é: {}", numero_secreto);

    println!("Digite o seu palpite.");

    let mut palpite = String::new();

    io::stdin().read_line(&mut palpite)
        .expect("Falha ao ler entrada");

    println!("Você disse: {}", palpite);
}
```

<span class="caption">Listagem 2-3: Mudanças necessárias do código para gerar um
número aleatório.</span>

We’re adding a `extern crate rand;` line to the top that lets Rust know we’ll be
using that external dependency. This also does the equivalent of calling `use
rand`, so now we can call anything in the `rand` crate by prefixing it with
`rand::`.

Estamos adicionando a linha `extern crate rand` ao topo do arquivo para indicar
ao Rust que estamos usando uma dependência externa. Isto também é equivalente a
um `use rand;`, assim podemos chamar qualquer coisa que esteja no crate `rand`
prefixando-a com `rand::`.

Next, we’re adding another `use` line: `use rand::Rng`. `Rng` is a trait that
defines methods that random number generators implement, and this trait must be
in scope for us to use those methods. Chapter 10 will cover traits in detail.

Em seguida, adicionamos outra linha `use`: `use rand::Rng`. `Rng` é um trait
que define métodos a serem implementados pelos geradores de números aleatórios,
e esse trait deve estar dentro do escopo para que possamos usar esses métodos. O
Capítulo 10 vai abordar traits em mais detalhes.

Also, we’re adding two more lines in the middle. The `rand::thread_rng` function
will give us the particular random number generator that we’re going to use:
one that is local to the current thread of execution and seeded by the
operating system. Next, we call the `gen_range` method on the random number
generator. This method is defined by the `Rng` trait that we brought into
scope with the `use rand::Rng` statement. The `gen_range` method takes two
numbers as arguments and generates a random number between them. It’s inclusive
on the lower bound but exclusive on the upper bound, so we need to specify `1`
and `101` to request a number between 1 and 100.

Tem outras duas linhas que adicionamos no meio. A função `rand::thread_rng` nos
dá o gerador de números aleatórios que vamos usar, um que é local à _thread_
corrente e que é inicializado pelo sistema operacional. Depois, vamos chamar o
método `gen_range` no gerador de números aleatórios. Esse método está definido
pelo trait `Rng` que trouxemos ao escopo por meio do `use rand::Rng`. Este
método recebe dois argumentos e gera um número aleatório entre eles. Ele inclui
o limite inferior mas exclui o superior, então precisamos passar `1` e `101`
para obter um número de 1 a 100.

Knowing which traits to use and which functions and methods to call from a
crate isn’t something that you’ll just *know*. Instructions for using a crate
are in each crate’s documentation. Another neat feature of Cargo is that you
can run the `cargo doc --open` command that will build documentation provided
by all of your dependencies locally and open it in your browser. If you’re
interested in other functionality in the `rand` crate, for example, run `cargo
doc --open` and click `rand` in the sidebar on the left.

Saber quais traits devem ser usadas e quais funções e métodos de um crate
devem ser chamados não é nada trivial. As instruções de como usar um crate
estão na documentação de cada um. Outra coisa boa do Cargo é que você pode rodar
o comando `cargo doc --open` que vai construir localmente a documentação
fornecida por todas as suas dependências e abrí-las no seu navegador. Se você
estiver interessado em outras funcionalidades do crate `rand`, por exemplo,
execute `cargo doc --open` e clique em `rand`, no menu ao lado esquerdo.

The second line that we added to the code prints the secret number. This is
useful while we’re developing the program to be able to test it, but we’ll
delete it from the final version. It’s not much of a game if the program prints
the answer as soon as it starts!

A segunda linha que adicionamos imprime o número secreto. Isto é útil enquanto
estamos desenvolvendo o programa para podermos testá-lo, mas vamos retirá-la da
versão final. Um jogo não é muito interessante se ele mostra a resposta logo no
início!

Try running the program a few times:

Tente rodar o programa algumas vezes:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/jogo_de_advinhacao`
Advinhe o número!
O número secreto é: 7
Digite o seu palpite.
4
Você disse: 4
$ cargo run
     Running `target/debug/jogo_de_advinhacao`
Advinhe o número!
O número secreto é: 83
Digite o seu palpite.
5
Você disse: 5
```

You should get different random numbers, and they should all be numbers between
1 and 100. Great job!

Você já deve obter números aleatórios diferentes, e eles devem ser todos entre 1
e 100. Bom trabalho!

## Comparing the Guess to the Secret Number
## Comparando o Palpite com o Número Secreto

Now that we have user input and a random number, we can compare them. That
step is shown in Listing 2-4:

Agora que nós temos a entrada do usuário e o número secreto, vamos compará-los.
Esta estapa é mostrada na Listagem 2-4:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

<span class="caption">Listing 2-4: Handling the possible return values of
comparing two numbers</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    println!("O número secreto é: {}", numero_secreto);

    println!("Digite o seu palpite.");

    let mut palpite = String::new();

    io::stdin().read_line(&mut palpite)
        .expect("Falha ao ler entrada");

    println!("Você disse: {}", palpite);

    match palpite.cmp(&numero_secreto) {
        Ordering::Less => println!("Muito baixo!"),
        Ordering::Greater => println!("Muito alto!"),
        Ordering::Equal => println!("Você acertou!"),
    }
}
```

<span class="caption">Listagem 2-4: Tratando os possíveis resultados da
comparação de dois números.</span>

The first new bit here is another `use`, bringing a type called
`std::cmp::Ordering` into scope from the standard library. `Ordering` is
another enum, like `Result`, but the variants for `Ordering` are `Less`,
`Greater`, and `Equal`. These are the three outcomes that are possible when you
compare two values.

A primeira novidade aqui é outro `use`, que traz ao escopo um tipo da biblioteca
padrão chamado `std::cmp::Ordering`. `Ordering` é outra enum, igual a `Result`,
mas as suas variantes são `Less`, `Greater` e `Equal` (elas significam menor,
maior e igual, respectivamente). Estes são os três possíveis resultados quando
você compara dois valores.

Then we add five new lines at the bottom that use the `Ordering` type:

Depois, adicionamos cinco novas linhas no final que usam o tipo `Ordering`:

```rust,ignore
match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => println!("You win!"),
}
```

```rust,ignore
match palpite.cmp(&numero_secreto) {
    Ordering::Less => println!("Muito baixo!"),
    Ordering::Greater => println!("Muito alto!"),
    Ordering::Equal => println!("Você acertou!"),
}
```

The `cmp` method compares two values and can be called on anything that can be
compared. It takes a reference to whatever you want to compare with: here it’s
comparing the `guess` to the `secret_number`. `cmp` returns a variant of the
`Ordering` enum we brought into scope with the `use` statement. We use a
[`match`][match]<!-- ignore --> expression to decide what to do next based on
which variant of `Ordering` was returned from the call to `cmp` with the values
in `guess` and `secret_number`.

O método `cmp` compara dois valores, e pode ser chamado a partir de qualquer
coisa que possa ser comparada. Ele recebe uma referência de qualquer coisa que
você queira comparar. Neste caso, está comparando o `palpite` com o
`numero_secreto`. `cmp` retorna uma variante do tipo `Ordering`, que trouxemos
ao escopo com `use`. Nós usamos uma expressão [`match`][match]<!-- ignore -->
para decidir o que fazer em seguida, com base em qual variante de `Ordering` foi
retornada pelo método `cmp`, que foi chamado com os valores `palpite` e
`numero_secreto`.

[match]: ch06-02-match.html

A `match` expression is made up of *arms*. An arm consists of a *pattern* and
the code that should be run if the value given to the beginning of the `match`
expression fits that arm’s pattern. Rust takes the value given to `match` and
looks through each arm’s pattern in turn. The `match` construct and patterns
are powerful features in Rust that let you express a variety of situations your
code might encounter and helps ensure that you handle them all. These features
will be covered in detail in Chapter 6 and Chapter 18, respectively.

Uma expressão `match` é composta de *braços*. Um braço consiste em um *padrão*
mais o código que deve ser executado se o valor colocado no início do `match` se
encaixar no padrão deste braço. O Rust pega o valor passado ao `match` e o
compara com o padrão de cada braço na sequência. A expressão `match` e os
padrões são ferramentas poderosas do Rust que lhe permitem expressar uma
variedade de situações que seu código pode encontrar, e ajuda a assegurar que
você tenha tratado todas elas. Essas ferramentas serão abordadas em detalhes nos
capítulos 6 e 18, respectivamente.

Let’s walk through an example of what would happen with the `match` expression
used here. Say that the user has guessed 50, and the randomly generated secret
number this time is 38. When the code compares 50 to 38, the `cmp` method will
return `Ordering::Greater`, because 50 is greater than 38. `Ordering::Greater`
is the value that the `match` expression gets. It looks at the first arm’s
pattern, `Ordering::Less`, but the value `Ordering::Greater` does not match
`Ordering::Less`, so it ignores the code in that arm and moves to the next arm.
The next arm’s pattern, `Ordering::Greater`, *does* match
`Ordering::Greater`! The associated code in that arm will execute and print
`Too big!` to the screen. The `match` expression ends because it has no need to
look at the last arm in this particular scenario.

Vamos acompanhar um exemplo do que aconteceria na expressão `match` usada aqui.
Digamos que o usuário tenha colocado 50 como palpite, e o número secreto
aleatório desta vez é 38. Quando o código compara 50 com 38, o método `cmp` vai
retornar `Ordering::Greater`, porque 50 é maior que 38. `Ordering::Greater` é o
valor passado ao `match`. Ele olha para o padrão `Ordering::Less` do primeiro
braço, mas o valor `Ordering::Greater` não casa com `Ordering::Less`, então ele
ignora o código desse braço e avança para o próximo. Já o padrão do próximo
braço, `Ordering::Greater`, *casa* com `Ordering::Greater`! O código associado a
este braço vai ser executado e mostrar `Muito alto!` na tela. A expressão
`match` termina porque já não tem mais necessidade de verificar o último braço
nesse caso particular.

However, the code in Listing 2-4 won’t compile yet. Let’s try it:

Porém, o código da Listagem 2-4 ainda não vai compilar. Vamos tentar:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `std::string::String`, found integral variable
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `guessing_game`.
```

```text
$ cargo build
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match palpite.cmp(&numero_secreto) {
   |                       ^^^^^^^^^^^^^^^ expected struct `std::string::String`, found integral variable
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `jogo_de_advinhacao`.
```

The core of the error states that there are *mismatched types*. Rust has a
strong, static type system. However, it also has type inference. When we wrote
`let guess = String::new()`, Rust was able to infer that `guess` should be a
`String` and didn’t make us write the type. The `secret_number`, on the other
hand, is a number type. A few number types can have a value between 1 and 100:
`i32`, a 32-bit number; `u32`, an unsigned 32-bit number; `i64`, a 64-bit
number; as well as others. Rust defaults to an `i32`, which is the type of
`secret_number` unless we add type information elsewhere that would cause Rust
to infer a different numerical type. The reason for the error is that Rust will
not compare a string and a number type.

O que este erro está dizendo é que temos *tipos incompatíveis*. Rust tem um
sistema de tipos forte e estático. Porém, Rust também tem inferência de tipos.
Quando escrevemos `let palpite = String::new()`, Rust foi capaz de inferir que
`palpite` deveria ser uma `String`, então ele não nos faz escrever o tipo. O
`numero_secreto`, por outro lado, é de um tipo numérico. Existem alguns tipos
numéricos capazes de guardar um valor entre 1 e 100: `i32`, que é um número de
32 bits; `u32`, um número de 32 bits sem sinal; `i64`, um número de 64 bits; e
mais alguns outros. O tipo numérico padrão do Rust é `i32`, que é o tipo do
`numero_secreto`, a não ser que adicionemos, em algum lugar, uma informação de
tipo que faça o Rust inferir outro tipo numérico. A razão do erro é que o Rust
não pode comparar uma string e um tipo numérico.

Ultimately, we want to convert the `String` the program reads as input into a
real number type so we can compare it to the guess numerically. We can do
that by adding the following two lines to the `main` function body:

Em última análise, queremos converter a `String` que lemos como entrada em um
tipo numérico de verdade, de forma que possamos compará-lo numericamente com o
palpite. Podemos fazer isso com mais duas linhas no corpo da função `main`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    println!("O número secreto é: {}", numero_secreto);

    println!("Digite o seu palpite.");

    let mut palpite = String::new();

    io::stdin().read_line(&mut palpite)
        .expect("Falha ao ler entrada");

    let palpite: u32 = palpite.trim().parse()
        .expect("Por favor, digite um número!");

    println!("Você disse: {}", palpite);

    match palpite.cmp(&numero_secreto) {
        Ordering::Less => println!("Muito baixo!"),
        Ordering::Greater => println!("Muito alto!"),
        Ordering::Equal => println!("Você acertou!"),
    }
}
```

The two new lines are:

As duas linhas novas são:

```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```

```rust,ignore
let palpite: u32 = palpite.trim().parse()
    .expect("Por favor, digite um número!");
```

We create a variable named `guess`. But wait, doesn’t the program
already have a variable named `guess`? It does, but Rust allows us to
*shadow* the previous value of `guess` with a new one. This feature is often
used in similar situations in which you want to convert a value from one type
to another type. Shadowing lets us reuse the `guess` variable name rather than
forcing us to create two unique variables, like `guess_str` and `guess` for
example. (Chapter 3 covers shadowing in more detail.)

Nós criamos uma variável chamada `palpite`. Mas espera, o programa já não tinha
uma variável chamada `palpite`? Sim, mas o Rust nos permite *sombrear* o
`palpite` anterior com um novo. Isto é geralmente usado em situações em que você
quer converter um valor de um tipo em outro. O sombreamento nos permite
reutilizar o nome `palpite`, em vez de nos forçar a criar dois nomes únicos como
`palpite_str` e `palpite`, por exemplo. (O Capítulo 3 vai cobrir sombreamento em
mais detalhes).

We bind `guess` to the expression `guess.trim().parse()`. The `guess` in the
expression refers to the original `guess` that was a `String` with the input in
it. The `trim` method on a `String` instance will eliminate any whitespace at
the beginning and end. `u32` can only contain numerical characters, but the
user must press the <span class="keystroke">enter</span> key to satisfy
`read_line`. When the user presses <span class="keystroke">enter</span>, a
newline character is added to the string. For example, if the user types <span
class="keystroke">5</span> and presses <span class="keystroke"> enter</span>,
`guess` looks like this: `5\n`. The `\n` represents “newline,” the enter key.
The `trim` method eliminates `\n`, resulting in just `5`.

Nós vinculamos `palpite` à expressão `palpite.trim().parse()`. O `palpite`, na
expressão, refere-se ao `palpite` original contendo a `String` de entrada do
usuário. O método `trim`, em uma instância de `String`, vai eliminar quaisquer
espaços em branco no início e no fim. `u32` pode conter apenas caracteres
numéricos, mas o usuário precisa pressionar <span class="keystroke">Enter</span>
para satisfazer o `read_line`. Quando o usuário pressiona
<span class="keystroke">Enter</span>, um caractere de nova linha é inserido na
string. Por exemplo, se o usuário digitar <span class="keystroke">5</span> e
depois <span class="keystroke">Enter</span>, `palpite` ficaria assim: `5\n`. O
`\n` representa uma linha nova, a tecla Enter. O método `trim` elimina o `\n`,
deixando apenas `5`.

The [`parse` method on strings][parse]<!-- ignore --> parses a string into some
kind of number. Because this method can parse a variety of number types, we
need to tell Rust the exact number type we want by using `let guess: u32`. The
colon (`:`) after `guess` tells Rust we’ll annotate the variable’s type. Rust
has a few built-in number types; the `u32` seen here is an unsigned, 32-bit
integer. It’s a good default choice for a small positive number. You’ll learn
about other number types in Chapter 3. Additionally, the `u32` annotation in
this example program and the comparison with `secret_number` means that Rust
will infer that `secret_number` should be a `u32` as well. So now the
comparison will be between two values of the same type!

O [método `parse` em strings][parse]<!-- ignore --> converte uma string para
algum tipo de número. Dado que ele pode interpretar uma variedade de tipos
numéricos, precisamos dizer ao Rust qual o tipo exato de número nós queremos, e
para isso usamos `let palpite: u32`. Os dois pontos (`:`) depois de `palpite`
informam ao Rust que estamos anotando seu tipo. O Rust tem alguns tipos
numéricos embutidos, o `u32` visto aqui é um inteiro de 32 bits sem sinal. É uma
boa escolha padrão para um número positivo pequeno. Você vai aprender sobre
outros tipos numéricos no Capítulo 3. Além disso, a anotação `u32` neste
programa de exemplo e a comparação com `numero_secreto` significam que o Rust
vai inferir que `numero_secreto` também deve ser um `u32`. Então agora a
comparação vai ser feita entre valores do mesmo tipo!

[parse]: ../../std/primitive.str.html#method.parse

The call to `parse` could easily cause an error. If, for example, the string
contained `A👍%`, there would be no way to convert that to a number. Because it
might fail, the `parse` method returns a `Result` type, much like the
`read_line` method does as discussed earlier in “Handling Potential Failure
with the Result Type”. We’ll treat this `Result` the same way by
using the `expect` method again. If `parse` returns an `Err` `Result` variant
because it couldn’t create a number from the string, the `expect` call will
crash the game and print the message we give it. If `parse` can successfully
convert the string to a number, it will return the `Ok` variant of `Result`,
and `expect` will return the number that we want from the `Ok` value.

A chamada para `parse` poderia facilmente causar um erro. Por exemplo, se a
string contiver `A👍%`, não haveria como converter isto em um número. Como ele
pode falhar, o método `parse` retorna um `Result`, assim como o método
`read_line`, conforme discutido anteriormente na seção "Tratando Potenciais
Falhas com o Tipo `Result`. Vamos tratar este `Result` da mesma forma usando o
método `expect` de novo. Se o `parse` retornar uma variante `Err` da enum
`Result`, por não conseguir criar um número a partir da string, a chamada ao
`expect` vai causar um _crash_ no jogo e exibir a mensagem que passamos a ele.
Se o `parse` conseguir converter uma string em um número, ele vai retornar a
variante `Ok` da enum `Result` e `expect` vai retornar o número que queremos
extrair do valor `Ok`.

Let’s run the program now!

Agora vamos executar o programa!

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43 secs
     Running `target/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43 secs
     Running `target/jogo_de_advinhacao`
Advinhe o número!
O número secreto é: 58
Digite o seu palpite.
  76
Você disse: 76
Muito alto!
```

Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.

Boa! Até mesmo colocando alguns espaços antes de digitar o palpite, o programa
ainda descobriu que o palpite do usuário é 76. Execute o programa mais algumas
vezes para verificar os diferentes comportamentos com diferentes tipos de
entrada: advinhe o número corretamente, digite um número muito alto, e digite um
número muito baixo.

We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!

Agora já temos a maior parte do jogo funcionando, mas o usuário só consegue dar
um palpite uma vez. Vamos mudar isso adicionando laços!

## Allowing Multiple Guesses with Looping
## Permitindo Múltiplos Palpites Usando _Looping_

The `loop` keyword gives us an infinite loop. Add that now to give users more
chances at guessing the number:

A palavra-chave `loop` nos dá um laço (_loop_) infinito. Use-a para dar aos
usuários mais chances de advinhar o número:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    println!("O número secreto é: {}", numero_secreto);

    loop {
        println!("Digite o seu palpite.");

        let mut palpite = String::new();

        io::stdin().read_line(&mut palpite)
            .expect("Falha ao ler entrada");

        let palpite: u32 = palpite.trim().parse()
            .expect("Por favor, digite um número!");

        println!("Você disse: {}", palpite);

        match palpite.cmp(&numero_secreto) {
            Ordering::Less => println!("Muito baixo!"),
            Ordering::Greater => println!("Muito alto!"),
            Ordering::Equal => println!("Você acertou!"),
        }
    }
}
```

As you can see, we’ve moved everything into a loop from the guess input prompt
onward. Be sure to indent those lines another four spaces each, and run the
program again. Notice that there is a new problem because the program is doing
exactly what we told it to do: ask for another guess forever! It doesn’t seem
like the user can quit!

Como você pode ver, movemos tudo para dentro do laço a partir da mensagem
pedindo o palpite do usuário. Certifique-se de indentar essas linhas mais quatro
espaços cada uma, e execute o programa novamente. Repare que há um novo
problema, porque o programa está fazendo exatamente o que dissemos para ele
fazer: pedir sempre outro palpite! Parece que o usuário não consegue sair!

The user could always halt the program by using the keyboard shortcut
<span class="keystroke">ctrl-C</span>. But there’s another way to escape this
insatiable monster that we mentioned in the `parse` discussion in “Comparing the
Guess to the Secret Number”: if the user enters a non-number answer, the program
will crash. The user can take advantage of that in order to quit, as shown here:

O usuário pode sempre interromper o programa usando as teclas
<span class="keystroke">Ctrl-C</span>. Mas há uma outra forma de escapar deste
monstro insaciável que mencionamos na discussão do método `parse`, na seção
"Comparando o Palpite com o Número Secreto": se o usuário fornece uma resposta
não-numérica, o programa vai sofrer um _crash_. O usuário pode levar vantagem
disso para conseguir sair, como mostrado abaixo:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
     Running `target/jogo_de_advinhacao`
Advinhe o número!
O número secreto é: 59
Digite o seu palpite.
45
Você disse: 45
Muito baixo!
Digite o seu palpite.
60
Você disse: 60
Muito alto!
Digite o seu palpite.
59
Você disse: 59
Você acertou!
Digite o seu palpite.
sair
thread 'main' panicked at 'Por favor, digite um número!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/jogo_de_advinhacao` (exit code: 101)
```

Typing `quit` actually quits the game, but so will any other non-number input.
However, this is suboptimal to say the least. We want the game to automatically
stop when the correct number is guessed.

Digitar `sair`, na verdade, sai do jogo, mas isso também acontece com qualquer
outra entrada não numérica. Porém, isto não é o ideal. Queremos que o jogo
termine automaticamente quando o número é advinhado corretamente.

### Quitting After a Correct Guess
### Saindo Após um Palpite Correto

Let’s program the game to quit when the user wins by adding a `break`:

Vamos programar o jogo para sair quando o usuário vencer, colocando um `break`:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = guess.trim().parse()
            .expect("Please type a number!");

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    println!("O número secreto é: {}", numero_secreto);

    loop {
        println!("Digite o seu palpite.");

        let mut palpite = String::new();

        io::stdin().read_line(&mut palpite)
            .expect("Falha ao ler entrada");

        let palpite: u32 = palpite.trim().parse()
            .expect("Por favor, digite um número!");

        println!("Você disse: {}", palpite);

        match palpite.cmp(&numero_secreto) {
            Ordering::Less => println!("Muito baixo"),
            Ordering::Greater => println!("Muito alto!"),
            Ordering::Equal => {
                println!("Você acertou!");
                break;
            }
        }
    }
}
```

By adding the `break` line after `You win!`, the program will exit the loop
when the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.

Adicionando a linha `break` após o `Você acertou!`, o programa vai sair do laço
quando o usuário advinhar corretamente o número secreto. Sair do laço também
significa sair do programa, pois o laço é a última parte da `main`.

### Handling Invalid Input
### Tratando Entradas Inválidas

To further refine the game’s behavior, rather than crashing the program when
the user inputs a non-number, let’s make the game ignore a non-number so the
user can continue guessing. We can do that by altering the line where `guess` is
converted from a `String` to a `u32`:

Para refinar ainda mais o comportamento do jogo, em vez de causar um _crash_ no
programa quando o usuário insere uma entrada não numérica, vamos fazer o jogo
ignorá-la para que o usuário possa continuar tentando. Podemos fazer isso
alterando a linha em que o `palpite` é convertido de `String` para `u32`:

```rust,ignore
let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

```rust,ignore
let palpite: u32 = match palpite.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};
```

Switching from an `expect` call to a `match` expression is how you generally
move from crash on error to actually handling the error. Remember that `parse`
returns a `Result` type, and `Result` is an enum that has the variants `Ok` or
`Err`. We’re using a `match` expression here, like we did with the `Ordering`
result of the `cmp` method.

Trocando uma chamada a `expect` por uma expressão `match` é a forma como você
geralmente deixa de causar um _crash_ em um erro e passa a tratá-lo, de fato.
Lembre-se que o método `parse` retorna um valor do tipo `Result`, uma enum que
contém a variante `Ok` ou `Err`. Estamos usando um `match` aqui, assim como
fizemos com o `Ordering` resultante do método `cmp`.

If `parse` is able to successfully turn the string into a number, it will return
an `Ok` value that contains the resulting number. That `Ok` value will match the
first arm’s pattern, and the `match` expression will just return the `num` value
that `parse` produced and put inside the `Ok` value. That number will end up
right where we want it in the new `guess` variable we’re creating.

Se o `parse` consegue converter a string em um número, ele vai retornar um `Ok`
contendo o número resultante. Esse valor `Ok` vai casar com o padrão do primeiro
braço, e o `match` vai apenas retornar o valor `num` produzido pelo `parse` e
colocado dentro do `Ok`. Esse número vai acabar ficando exatamente onde
queremos, na variável `palpite` que estamos criando. 

If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does match
the `Err(_)` pattern in the second arm. The `_` is a catchall value; in this
example, we’re saying we want to match all `Err` values, no matter what
information they have inside them. So the program will execute the second arm’s
code, `continue`, which means to go to the next iteration of the `loop` and ask
for another guess. So effectively, the program ignores all errors that `parse`
might encounter!

Se o `parse` *não* conseguir converter a string em um número, ele vai retornar
um `Err` que contém mais informações sobre o erro. O valor `Err` não casa com o
padrão `Ok(num)` do primeiro braço do `match`, mas casa com o padrão `Err(_)` do
segundo braço. O `_` é um valor "pega tudo". Neste exemplo, estamos dizendo que
queremos casar todos os valores `Err`, não importa qual informação há dentro
deles. Então o programa vai executar o código do segundo braço, `continue`, que
significa ir para a próxima iteração do `loop` e pedir outro palpite.
Efetivamente, o programa ignora todos os erros que o `parse` vier a encontrar!

Now everything in the program should work as expected. Let’s try it by running
`cargo run`:

Agora, tudo no programa deve funcionar como esperado. Vamos tentar executá-lo
usando o comando `cargo run`:

```text
$ cargo run
   Compiling jogo_de_advinhacao v0.1.0 (file:///projects/jogo_de_advinhacao)
     Running `target/jogo_de_advinhacao`
Advinhe o número!
O número secreto é: 61
Digite o seu palpite.
10
Você disse: 10
Muito baixo!
Digite o seu palpite.
99
Você disse: 99
Muito alto!
Digite o seu palpite.
foo
Digite o seu palpite.
61
Você disse: 61
Você acertou!
```

Awesome! With one tiny final tweak, we will finish the guessing game: recall
that the program is still printing out the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-5 shows the final code:

Demais! Com apenas um último ajuste, vamos finalizar o jogo de adivinhação:
lembre-se que o programa ainda está mostrando o número secreto. Isto foi bom
para testar, mas estraga o jogo. Vamos apagar o `println!` que revela o número
secreto. A Listagem 2-5 mostra o código final:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

<span class="caption">Listing 2-5: Complete code of the guessing game</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate rand;

use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Advinhe o número!");

    let numero_secreto = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Digite o seu palpite.");

        let mut palpite = String::new();

        io::stdin().read_line(&mut palpite)
            .expect("Falha ao ler entrada");

        let palpite: u32 = match palpite.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("Você disse: {}", palpite);

        match palpite.cmp(&numero_secreto) {
            Ordering::Less => println!("Muito baixo!"),
            Ordering::Greater => println!("Muito alto!"),
            Ordering::Equal => {
                println!("Você acertou!");
                break;
            }
        }
    }
}
```

<span class="caption">Listagem 2-5: Código completo do jogo de advinhação.
</span>

## Summary
## Resumo

At this point, you’ve successfully built the guessing game! Congratulations!

Neste ponto, você construiu com sucesso o jogo de adivinhação! Parabéns!

This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, methods, associated functions, using external crates, and more.
In the next few chapters, you’ll learn about these concepts in more detail.
Chapter 3 covers concepts that most programming languages have, such as
variables, data types, and functions, and shows how to use them in Rust.
Chapter 4 explores ownership, which is a Rust feature that is most different
from other languages. Chapter 5 discusses structs and method syntax, and
Chapter 6 endeavors to explain enums.

Este projeto foi uma forma prática de apresentar vários conceitos novos de Rust:
`let`, `match`, métodos, funções associadas, uso de crates externos, e outros.
Nos próximos capítulos, você vai aprender sobre esses conceitos em mais
detalhes. O Capítulo 3 aborda conceitos que a maioria das linguagens de
programação tem, como variáveis, tipos de dados e funções, e mostra como usá-los
em Rust. O Capítulo 4 explora posse (_ownership_), que é a característica do
Rust mais diferente das outras linguagens. O Capítulo 5 discute structs e a
sintaxe de métodos, e o Capítulo 6 se dedica a explicar enums. 
