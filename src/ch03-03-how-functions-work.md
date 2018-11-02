# Funções

Funções são difundidas em códigos em Rust. Você já viu uma das mais
importantes funções da linguagem: a função `main`, que é o
ponto de entrada de diversos programas. Você também já viu a notação `fn`, que permite você
declarar uma nova função.

Códigos em Rust usam, por convenção, o estilo *snake case* para nomes de função e
veriável. No snake case, todas as letras são minúsculas e sublinhado (underline) separa as palavras.
Aqui está um programa que contém uma definição de função de exemplo:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    println!("Olá, mundo!");

    outra_funcao();
}

fn outra_funcao() {
    println!("Outra função.");
}
```

A definição de funções em Rust iniciam com `fn` e tem um par de parênteses
depois do nome da função. As chaves dizem ao compilador onde o
corpo da função começa e termina.

Podemos chamar qualqer função que tenhamos definido, inserindo seu nome, seguido de um
conjunto de parenteses. Pelo fato da `outra_funcao` ter sido definida no programa, ela pode
ser chamada dentro da função `main`. Note que definimos `outra_funcao`
*depois* da função `main`; poderíamos ter definido antes
também. Rust não se importa onde você definiu suas funções, apenas que elas foram
definidas em algum lugar.

Vamos começar um novo projeto binário, chamado *funcoes* para explorar mais
funções. Coloque o exemplo `outra_funcao` em *src/main.rs* e execute-o. Você
verá a seguinte saída:

```text
$ cargo run
   Compiling funcoes v0.1.0 (file:///projects/funcoes)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28 secs
     Running `target/debug/funcoes`
Olá, mundo!
Outra função.
```

As linhas sã executadas na ordem em que aparecem na função `main`.
Primeiro, a mensagem "Olá, mundo!" é exibida, e então
`outra_funcao` é chamada e exibida a mensagem.

### Parâmetros de função

Funções também podem ser definidas tendo *parâmetros*, que são variáveis especiais
que fazem parte da assinatura da função. Quando uma função tem parâmetros, você
pode fornecer específicos para esses parâmetros. Tecnicamente, os
valores definidos são chamados de *argumentos*, mas informalmente, as pessoas tendem
a usar as palavras *parâmetro* e *argumento* para falar tanto de
variáveis da definição da função como os valores passados quando você
chama uma função.

A seguinte versão (reescrita) da `outra_funcao` mostra como os parâmetros
aparecem no Rust:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    outra_funcao(5);
}

fn outra_funcao(x: i32) {
    println!("O valor de x é: {}", x);
}
```

Tente executar este programa; você verá a seguinte saída:

```text
$ cargo run
   Compiling funcoes v0.1.0 (file:///projects/funcoes)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21 secs
     Running `target/debug/funcoes`
O valor de x é: 5
```

A declaração de `outra_funcao` tem um parâmetro chamado `x`. O tipo do
`x` é especificado como `i32`. Quando `5` é ássadp para a `outra_funcao`, a macro
`println!` coloca `5` onde o par de chaves estava na string
de formato.

Nas assinaturas de função, você *deve* declarar o tipo de cada parâmetro. Essa é
decisão deliberada no deign do Rust: exigir anotações de tipo na definição da função,
significa que o compilador quase nunca precisará que as use em outr lugar do código
para especificar o que você quer.

Quando você precisa que uma função tenha vários parâmetros, separe as declarações de parâmetros
com vírgula, como a seguir:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    outra_funcao(5, 6);
}

fn outra_funcao(x: i32, y: i32) {
    println!("O valor de x é: {}", x);
    println!("O valor de y é: {}", y);
}
```

Este exemplo criar uma função com dois parâmetros, ambos com o 
tipo `i32`. Então a função exibe os valores de ambos os parâmetros. Note que os
parâmetros de função não precisam ser do mesmo tipo, isto apenas
aconteceu neste exemplo.

Vamos tentar executar este código. Substitua o programa *src/main.rs*, atualmente em seu projeto *funcoes*
com o exemplo anterior e execute-o usando `cargo
run`:

```text
$ cargo run
   Compiling funcoes v0.1.0 (file:///projects/funcoes)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/funcoes`
O valor de x é: 5
O valor de y é: 6
```

Porque nós chamamos a função com `5` sendo o valor de` x` e `6` é passado
como o valor de `y`, as duas cadeias são impressas com esses valores.

### Corpos de função

Corpos de são constituídos por uma série de declarações que terminam,
opcionalmente, em uma expressão. Até agora, foram apresentadas apenas funções sem uma expressão final,
mas você viu uma expressão como parte de instruções. Porque Rust é uma
linguagem baseada em expressão, essa é uma importante distinção a ser entendida.
Outras linguagens não têm as mesmas distinções, então, vamos ver o que são
declarações e expressões e como elas afetamo os corpos
das funções.