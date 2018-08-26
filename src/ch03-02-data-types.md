# Tipos de dados

Todo valor em Rust é um *tipo de dado*, que informa ao Rust que tipos de
datos estão sendo especificados para que saiba como trabalhar com esses dados. Vamos olhar para
dois subconjuntos tipos de dados: escalar e composto.

Tenha em ment que Rust é uma linguagem de *tipagem estática*, o que significa
que deve conhecer os tipos de todas as variáveis em tempo de compilação. O compilador
geralmente pode inferir que tipo queremos com base no valor e como o usamos. Nos casos
em que são é possível vários tipos de dados, como quando convertemos uma `String` em um tipo numérico
usando `parse` na seção" Comparando o Adivinha ao Número Secreto" no
Capítulo 2, devemos adicionar uma anotação de tipo, como a seguinte:

```rust
let guess: u32 = "42".parse().expect("Não é um número!");
```

Se não adicionarmos uma anotção de tipo, Rust irá mostrar o seguinte erro,
que significa que o compilador precisa de mais informaçoes para saber qual tipo de dados
queremos usar:

```text
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Não é um número!");
  |         ^^^^^
  |         |
  |         cannot infer type for `_`
  |         consider giving `guess` a type
```

Você verá anotações de tipos diferentes para outros tipos de dados.

### Tipos escalares

Um tipo *escalar* representa um valor único. Rust tem quatro tipos escalares primários:
inteiros, números de ponto flutuante, booleanos e caracteres. You pode reconhecer
esses tipos de outras linguagens de programação. Vamos pular para como eles funcionam no Rust.

### Tipos inteiros

Um *inteiro* é um número sem a parte fracionária. Usamos
um tipo inteiro no Capítulo 2, o tipo `u32`. Esse tipo de
declaração indica que
o valor associado com deve ser um inteiro sem sinal (tipos inteiros com sinal começam com `i`, em vez de `u`) que ocupa 32 bits de espaço. Tabela 3-1 mostra
os tipos inteiros internos ao Rust. Cada variante está na
coluna com sinal e sem sinal (por exemplo, `i16`) pode ser usada para declara um valor do tipo
inteiro.

<span class="caption">Tabela 3-1: Tipos inteiros no Rust</span>

| Tamanho | Signed  | Unsigned |
|---------|---------|----------|
| 8-bit   | `i8`    | `u8`     |
| 16-bit  | `i16`   | `u16`    |
| 32-bit  | `i32`   | `u32`    |
| 64-bit  | `i64`   | `u64`    |
| arch    | `isize` | `usize`  |

Cada variante pode ser com ou sem sinal e ter tamanho explícito.
*Signed* e *unsigned* refere-se à possibilidade do número ser
negativo ou positivo - em outras palavras, se o número precisa de um sinal
com ele (signed) ou se sempre for sempre
positivo pode ser representado sem um sinal (unsigned). É como escrevemos números no papel: Quando
o sinal importa, o número é mostrado com um sinal de mais ou menos; contudo,
quando é seguro assimit que o número é positivo, é mostrado sem sinal.
Números com sinais são armazenados usando a representação complemento de dois (se você não tiver
certeza do que é isso, você pode procurar sobre isso na internet; uma explicação está fora do escopo
deste livro).

Cada variante com sinap pode armazenar números de -(2<sup>n - 1</sup>) até 2<sup>n -
1</sup> - 1 incluso, sendo *n* o número de bits que varia de acordo com o uso. Então, um
`i8` pode armazenar números de -(2<sup>7</sup>) até 2<sup>7</sup> - 1, que é  igual
a -128 até 127. Variantes sem sinal pode pode armazenar números de 0 até 2<sup>n</sup> - 1,
entao um `u8` pode armazenar números de 0 até 2<sup>8</sup> - 1, que é de 0 até 255.

Além disso, os tipos `isize` e `usize` dependem do computador em que seu programa
está rodando: 64 bits se estiver em uma arquitetura de 64-bit e 32 bits
se sua arquitetura for 32-bit.W

Você criar inteiros literais em qualquer uma das formas mostrada na Tabela 3-2. Observe
que todos os literais de números, exceto o byte literal, permitem um sufixo de tipo,
como por exemplo, `57u8` e `_` são separadores visuais, tal como `1_000`.

<span class="caption">Tabela 3-2: Inteiros Literais no Rust</span>

| Números literais    | Exemplo       |
|---------------------|---------------|
| Decimal             | `98_222`      |
| Hexadecimal         | `0xff`        |
| Octal               | `0o77`        |
| Binário             | `0b1111_0000` |
| Byte (`u8` apenas)  | `b'A'`        |

Então como você pode saber qual tipo de inteiro usar? Se sentir-se inseguro, as
escolhas padrões do Rust são geralmente são boas, e por padrão os inteiros são do tipo `i32`: Esse
tipo geralmente é o mais rápido, até em sistemas de 64-bit. A
principal situação em que você usuaria `isize` ou `usize` é indexar algum tipo de coleção.

