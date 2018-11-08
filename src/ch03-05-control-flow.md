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

#### Gerenciando Múltiplas Condições com `else if`

Você pode ter várias condições combinando `if` e` else` em um `else if`.
Por exemplo:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let numero = 6;

    if numero % 4 == 0 {
        println!("número é divisível por 4");
    } else if numero % 3 == 0 {
        println!("número é divisível por 3");
    } else if numero % 2 == 0 {
        println!("número é divisível por 2");
    } else {
        println!("número não é divisível por 4, 3 ou 2");
    }
}
```

Este programa tem quatro caminhos possíveis. Depois de executá-lo, você deve
ver a seguinte saída:

```text
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/branches`
número é divisível por 3
```

Quando este programa é executado, ele verifica cada expressão `if` por sua vez e executa
o primeiro corpo para o qual a condição é verdadeira. Note que mesmo que 6 seja
divisível por 2, nós não vemos a saída `o número é divisível por 2`, nem vemos o
texto `número não é divisível por 4, 3 ou 2` do bloco `else`.
Isso ocorre porque o Rust só executa o bloco para a primeira condição verdadeira e,
depois de encontrar um, não verifica o restante.

Usar muitas expressões `else if` pode confundir seu código, portanto, se você tiver
mais de uma, convém refatorar seu código. O Capítulo 6 descreve uma poderosa
construção de ramificação em Rust chamada `match` para esses casos.

#### Usando `if` em uma declaração `let`

Pelo fato de `if` ser uma expressão, podemos usá-la do lado direito de uma declaração `let`,
como na Listagem 3-2:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let condicao = true;
    let numero = if condicao {
        5
    } else {
        6
    };

    println!("O valor do número é: {}", numero);
}
```

<span class = "caption"> Listagem 3-2: Atribuindo o resultado de uma expressão `if`
para uma variável </span>

A variável `numero` será ligada a um valor baseado no resultado da expressão `if`.
Execute este código para ver o que acontece:

```text
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/branches`
O valor do número é: 5
```

Lembre-se de que os blocos de código são avaliados até a última expressão, e os
números por si mesmos também são expressões. Neste caso, o valor de
toda a expressão `if` depende de qual bloco de código é executado. Isso significa que
os valores que têm o potencial de serem resultados de cada braço do `if` e que devem ser
do mesmo tipo; na Listagem 3-2, os resultados do braço `if` e do `else`
eram inteiros `i32`. Se os tipos forem incompatíveis, como no exemplo a
seguir, receberemos um erro:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let condicao = true;

    let numero = if condicao {
        5
    } else {
        "seis"
    };

    println!("O valor do número é: {}", numero);
}
```

Quando tentamos executar esse código, recebemos um erro. Os braços `if` e` else` possuem
valores de tipos que são incompatíveis, e Rust indica exatamente onde encontrar o
problema no programa:

```text
error[E0308]: if and else have incompatible types
 --> src/main.rs:4:18
  |
4 |       let numero = if condicao {
  |  __________________^
5 | |         5
6 | |     } else {
7 | |         "seis"
8 | |     };
  | |_____^ expected integral variable, found &str
  |
  = note: expected type `{integer}`
             found type `&str`
```

A expressão no bloco `if` é avaliada como um inteiro, e a expressão no bloco` else` é
avaliada como uma string. Isso não funcionará porque as variáveis precisam ter
um único tipo. Rust precisa saber em tempo de compilação qual é o tipo da variável `numero`,
definitivamente, para que possa verificar em tempo de compilação que seu tipo é
válido em todos os lugares em que usamos `numero`. Rust não seria capaz de fazer isso se o tipo
de `numero` fosse determinado apenas em tempo de execução; o compilador seria mais complexo e
faria menos garantias sobre o código se tivesse que manter o controle de
vários tipos hipotéticos para qualquer variável.