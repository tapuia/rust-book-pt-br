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

