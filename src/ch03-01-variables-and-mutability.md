# Variáveis e Mutabilidade

Como mencionado no Capítulo 2, por padrão, as variáveis são imutáveis. Essa é uma das
maneiras que o Rust lhe dá para escrever o seu código de
modo seguro e a fácil concorrência que Rust oforece. No entanto, você ainda tem
a opção de tornar a sua variável mutável. Vamos explorar como e por que Rust
incentiva você a usar variáveis imutáveis e por que às vezes pode
não optar por utilizá-las.

Quando uma variável é imutável, logo que um valor é associado a uma variável, você não pode mudar este valor.
Para ilustrar isso, vamos criar um projeto chamado *variaveis*
no seu diretório *projetos* usando `cargo new --bin variables`.

Então dentro do novo diretório chamado *variaveis*, abra *src/main.rs* e substitua o
código com o código abaixo, que não irá compilar:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let x = 5;
    println!("O valor de x é: {}", x);
    x = 6;
    println!("O valor de x é: {}", x);
}
```

Salve e execute o programa usando `cargo run`. Você deve receber uma mensagem de erro,
conforme mostrado nesta saída:

```text
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("O valor de x é: {}", x);
4 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
```

Esse exemplo mostra como o compilador ajuda você a encontrar error no seus programas.
Mesmo que erros de compilação sejam frustrantes, eles apenas significam que seu programa
não está fazendo de modo seguro o que você espera fazer; eles *não* siginificam que você
não é um bom programador! Programadores experientes também recebem erros de compilação.

A mensagem indica que a causa do erro é que você
`não pode atribuir mais de uma vez à variáve
imutável x`, porque você tentou atribuir um segundo valor à variável `x`.

É importante que nos recebamos erros em tempo de compilação quando tentamos
alterar um valor que anteriormente foi indicado como imutável, porque
esta esta situação pode ocasionar erros. Se uma parte do seu código funciona assumindo
que o valor nunca será alterdo e outra parte do seu código muda este valor, é
possível que a primeira parte do código não faça o que foi projetada para fazer.
A causa desse tipo de falha pode ser difícil de rastrear,
especialmente quando o segundo trecho de código muda o valor apenas *algumas vezes*.

Em Rust, o compilador garante que quando você afirma que um valor não pode mudar,
ele não mude. Isso significa que quando você está lendo e ecrevendo código,
você não tenha de acompanhar como e onde um valor pode mudar. E assim seu código
fica mais fácil de entender.

Mas mutabilidade pode ser muito útil. Variáveis são imutáveis por padrão; como
você fez no Capítulo 2, você pode torná-las mutáveis adicionando `mut` na frente do
nome da variável. Além de permitir que este valor mude, `mut` transmite
a intenção aos futuros leitores do código, indicando que naquela
parte do código estarão mudando o valor da variável.

Por exemplo, vamos mudar *src/main.rs* para o seguinte:

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let mut x = 5;
    println!("O valor de x é: {}", x);
    x = 6;
    println!("O valor de x é: {}", x);
}
```

Quando executamos o programa, recebemos isso:

```text
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
O valor de x é: 5
O valor de x é: 6
```

Estamos autorizados a mudar o valor `5` contido  em `x` para `6`
quando `mut` é usado. Em alguns casos, você precisará criar uma variável mutável porque ela
será mais conveniente para escrever do que se fosse imutável.

Tem vários compromissos a serem considerados além de prevenção de
falhas. Por exemplo, nos casos em que você usa estruturas de dados grandes,
a alteração em uma instância pode ser mais rápida do que copiar e retornar a nova instância
alocada. Com estruturas de dados menores, criar novas instâncias e escrever
em um estilo de programação funcional pode ser mais fácil de entender, portanto,
um desempenho menor pode ser uma penalidade que vale a pena para obter mais clareza.

### Diferenças entre variáveis e contantes

Ser incapaz de mudar o valor de uma variável, pode ter feito você lembrar de
outro conceito de programação, que a maioria das outras linguagens possui, chamado: *constantes*. Como
variáveis imutáveis, contantes são valores que estão vinculados ao nome e não
podem serem alterados, mas há algumas diferenças entre contantes e
variáveis.

Primeiro, você não pode usar `mut` com constantes. Constante não são apenas
imutáveis por padrão, constante são sempre imutáveis.

Você declara constante usando a palavra-chave `const` em vez de `let`,
e o tipo do valor *necessita* ser específicado. Falaremos sobre tipos de dados
na próxima seção, "Data Type", então
não se preocupe com os detalhes por agora. Apenas saiba que você precisa especificaro tipo.

Contantes podem ser declaradas em qualquer escopo, incluindo o escopo global, o que os tornam
úteis para valores que várias partes do código precisa conhecer.

A última diferença é que as contantes podem serem definidas apenas para uma expressão contante,
ou seja, não pode ser o resultado de uma chamada de função ou qualquer outro valor que só poderia ser
calculado em tempo de execução.

Aqui está um exemplo de uma declaração constante, em que o nome da constante é
`PONTOS_MAXIMOS` e o valor definido é 100,000 (por
convenção, contantes em Rust são nomeadas usando maiúsculas e sublinhado entre as palavras):

```rust
const PONTOS_MAXIMOS: u32 = 100_000;
```

Constante são válidas durante todo o tempo de execução de um programa, dentro do escopo em que
foram declaradas, tornando-as uma ótima escolha para valores no domínio da aplicação,
que várias partes do programa necessitam conhecer, como por exemplo,
o número máximo de pontos um jogador pode ter ou a
velocidade da luz.

A nomeação de valores codificados permanentemente usados em todo o programa como constantes é útil
para transmitir o significado desse valor para futuros mantenedores do código. Também
ajuda ter apenas um lugar em seu código que você precisaria mudar se o
valor codificado precisasse ser atualizado no futuro.

