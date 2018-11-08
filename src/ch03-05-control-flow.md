## Controle de fluxo

Decidir se deve ou não executar algum código, dependendo se uma condição é verdadeira
e decidir executar algum código repetidamente enquanto uma condição é verdadeira,
são blocos de construção básicos na maioria das linguagens de programação. As construções
mais comuns que permitem controlar o fluxo de execução do código Rust são as expressões `if` e
laços de repetição.

### Expressão `if`

Uma expressão `if` permite ramificar seu código dependendo das condições. Você
fornecer uma condição e, em seguida, estado, "Se esta condição for cumprida, execute este bloco
de código. Se a condição não for atendida, não execute este bloco de código. ”

Crie um novo projeto chamado *branches* no seu diretório *projects* para explorar
a expressão `if`. No arquivo * src / main.rs *, digite o seguinte:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let numero = 3;

    if numero < 5 {
        println!("condição era verdadeira");
    } else {
        println!("condição era falsa");
    }
}
```

<!-- NEXT PARAGRAPH WRAPPED WEIRD INTENTIONALLY SEE #199 -->

Todas as expressões `if` começam com a palavra-chave` if`, que é seguida por uma
condição. Neste caso, a condição verifica se a variável
`number` tem um valor menor que 5. O bloco de código que queremos executar se o
condição é verdadeira é colocada imediatamente após a condição dentro de chaves.
Blocos de código associados às condições em expressões `if` são
às vezes chamado de *divisões*, assim como as expressões `de combinação` que nós
discutido na seção “Comparando o Palpite ao Número Secreto” de
Capítulo 2.

Opcionalmente, também podemos incluir uma expressão `else`, que escolhemos
fazer aqui, para dar ao programa um bloco de código alternativo a ser executado, caso a
condição seja avaliada como falsa. Se você não fornecer uma expressão `else` e a
condição for falsa, o programa simplesmente ignorará o bloco` if` e passará para o
próximo bit de código.

Tente executar este código; você deve ver a seguinte saída:

```text
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
condição era verdadeira
```

Vamos tentar alterar o valor de `numero` para um valor que torne a condição
`false` para ver o que acontece:

```rust,ignore
let numero = 7;
```

Execute o programa novamente e observe a saída:

```text
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
condição era falsa
```

Também é importante notar que a condição neste código *deve* ser um `bool`. E se
a condição não é um `bool`, nós vamos receber um erro. Por exemplo:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let numero = 3;

    if numero {
        println!("número era 3");
    }
}
```

A condição `if` é avaliada para um valor de `3` desta vez, e Rust lança um
erro:

```text
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if numero {
  |        ^^^^^^ expected bool, found integral variable
  |
  = note: expected type `bool`
             found type `{integer}`
```

O erro indica que Rust esperava um `bool`, mas obteve um inteiro. Ao contrário de
linguagens como Ruby e JavaScript, o Rust não tentará automaticamente
converter tipos não-booleanos em um booleano. Você deve explícitar e sempre fornecer
`if` com um booleano como sua condição. Se quisermos que o bloco de código `if` seja executado
somente quando um número não é igual a `0`, por exemplo, podemos mudar o `if`
para o seguinte:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let numero = 3;

    if numero != 0 {
        println!("número era algo diferente de zero");
    }
}
```

A execução deste código irá imprimir `número era algo diferente de zero`.