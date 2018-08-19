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