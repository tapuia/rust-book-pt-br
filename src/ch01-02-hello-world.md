## Olá, Mundo!

Agora que Rust já está instalado, vamos escrever nosso primeiro programa. Quando
aprendemos uma nova linguagem de programação, é tradicional escrever um pequeno
programa que imprime "Olá, mundo!" (_“Hello, world!”_) na tela, e é exatamente
isso que vamos fazer nesta seção.

> Nota: Este livro assume que o leitor tem um pouco de familiaridade com a
> linha de comando. Rust não exige que você use um determinado editor ou IDE,
> ou seja, você está livre para usar o que bem entender para escrever seu código
> Rust.

### Criando um Diretório de Projeto

Primeiramente, crie uma pasta para colocar o seu código Rust. O Rust não se
importa onde você vai armazenar o seu código, mas neste livro, nós sugerimos
criar um diretório chamado *projetos* e armazenar todos os seus projetos ali.
Abra o seu terminal e digite os seguintes comandos:

Linux e Mac:

```text
$ mkdir ~/projetos
$ cd ~/projetos
$ mkdir ola_mundo
$ cd ola_mundo
```

Windows CMD:

```cmd
> mkdir %USERPROFILE%\projetos
> cd %USERPROFILE%\projetos
> mkdir ola_mundo
> cd ola_mundo
```

Windows PowerShell:

```powershell
> mkdir $env:USERPROFILE\projetos
> cd $env:USERPROFILE\projetos
> mkdir ola_mundo
> cd ola_mundo
```

### Escrevendo e Executando um Programa em Rust

Crie um novo arquivo *main.rs*. Arquivos relacionados à linguagem Rust sempre
terminam com a extensão *.rs*. Se o nome do seu arquivo tem mais de uma palavra,
use um _underscore_ (`_`) para separá-los. Por exemplo, você deve nomear o seu
arquivo *ola_mundo.rs* em vez de *olamundo.rs*.

Agora abra o arquivo *main.rs* que você acabou de criar e digite o seguinte
código:

<span class="filename">Arquivo: main.rs</span>

```rust
fn main() {
    println!("Olá, mundo!");
}
```

Salve o arquivo e volte ao seu terminal. No Linux ou OSX, digite os seguintes
comandos:

```text
$ rustc main.rs
$ ./main
Olá, mundo!
```

Para executar o seu programa no Windows, digite `.\main.exe` em vez de `./main`.
Independente do seu sistema operacional, você deverá ver a mensagem
`Olá, mundo!` no seu terminal. Se você chegou até aqui, parabéns! Você escreveu
o seu primeiro programa em Rust. Isso faz de você um programador Rust! Seja
bem-vindo!

### Anatomia de um Programa em Rust

Agora vamos ver o que aconteceu com o seu programa "Olá, mundo!" em detalhes.
Aqui está a primeira peça do quebra-cabeça:

```rust
fn main() {

}
```

Estas linhas definem uma *função* em Rust. A função `main` é especial: é a
primeira coisa que é executada em cada programa escrito em Rust. A primeira
linha diz: "Estou declarando uma função chamada `main` que não contém nenhum
parâmetro e que não retorna nada." Se existissem parâmetros, eles estariam
dentro dos parênteses, `(` e `)`.

Também repare que o corpo da função está envolvido por duas chaves, `{` e `}`.
Rust requer essas chaves no começo e no fim do corpo de cada função.
Considera-se boa prática colocar a chave inicial na mesma linha da declaração
da função, com um espaço entre elas.

Dentro da função `main`:

```rust
    println!("Olá, mundo!");
```

Esta linha faz todo o trabalho nesse pequeno programa: imprime um texto na tela.
Existem alguns detalhes a se notar aqui. O primeiro é que o estilo de indentação
do Rust usa quatro espaços, e não um _tab_.

A segunda parte importante é o `println!`. Este comando está chamando uma
*macro*, que é a forma de se fazer metaprogramação em Rust. Se estivéssemos
chamando uma função, ficaria assim: `println` (sem o `!`). Vamos discutir
_macros_ em Rust com mais detalhes no Apêndice D, mas por agora, você só precisa
saber que quando usamos um `!`, significa que estamos chamando uma _macro_ em
vez de uma função.

Em seguida vem `"Olá, mundo!"`, que é uma *string*. Nós passamos esta _string_
como um argumento para a _macro_ `println!`, que por sua vez imprime a _string_
na tela. Fácil!

A linha termina com um ponto e vírgula (`;`). O `;` indica que esta expressão
acabou, e que a próxima está pronta para começar. A maioria das linhas de código
em Rust terminam com um `;`.

### Compilação e Execução São Etapas Diferentes

Na seção "Escrevendo e Executando um Programa em Rust", mostramos como você pode
executar um programa que você acabou de criar. A partir de agora, vamos dividir
este processo em partes e examinar cada uma delas.

Antes de executar qualquer programa em Rust, você deve compilá-lo. Você pode
usar o compilador do Rust utilizando o comando `rustc`, passando o nome do seu
arquivo fonte conforme o exemplo abaixo:

```text
$ rustc main.rs
```

Se você já programou em C ou C++, irá notar que esta etapa é bem similar ao uso
do `gcc` ou `clang`. Após a compilação ser realizada com sucesso, o Rust deve
gerar como saída um binário executável, que você pode conferir com o comando
`ls` no seu terminal, se estiver em um ambiente Linux ou OSX:

```text
$ ls
main  main.rs
```

No Windows, digite o seguinte:

```cmd
> dir /B %= a opção /B serve para mostrar apenas nomes de arquivos =%
main.exe
main.rs
```

Temos aqui dois arquivos: o código-fonte, que termina com a extensão *.rs*, e o
arquivo executável (*main.exe* no Windows, *main* nos demais sistemas). O que
nos resta fazer é executar o arquivo *main* ou *main.exe*, desta forma:

```text
$ ./main  # ou .\main.exe no Windows
```

Se o arquivo *main.rs* tiver o código do programa "Olá, mundo!", vai aparecer no
seu terminal a mensagem `Olá, mundo!`.

Se você vem de uma linguagem dinâmica como Ruby, Python, ou JavaScript, você
provavelmente não está acostumado a ver a compilação e a execução como etapas
separadas. Rust é uma linguagem _ahead-of-time compiled_, isso significa que
você pode compilar um programa, enviá-lo para alguém, e essa pessoa pode
executar o seu programa mesmo que não tenha o Rust instalado em seu ambiente. Se
você enviar a alguém um arquivo `.rb`, `.py`, ou `.js`, a pessoa vai precisar
ter instalado um interpretador Ruby, Python, ou JavaScript (respectivamente),
mas nesse caso você só precisa de um único comando para compilar e executar o
seu programa. Em design de linguagens de programação, tudo é uma relação de
compromisso.

Compilar usando `rustc` serve bem para programas simples, mas conforme o seu
projeto cresce, com certeza você vai querer gerenciar todas as opções possíveis
para o seu projeto e compartilhar facilmente o seu código com outras pessoas e
projetos. Na sequência, vamos lhe apresentar uma ferramenta chamada Cargo, que
ajuda a escrever programas em Rust usados no mundo real.

## Olá, Cargo!

Cargo é o sistema de _build_ e gerenciador de pacotes (_package manager_) do
Rust. Nós, Rustaceans, usamos Cargo para gerenciar nossos projetos em Rust
porque ele facilita muito as coisas. Por exemplo, Cargo se encarrega do processo
de _build_ do seu código, baixa as bibliotecas das quais seu código depende e
compila essas bibliotecas. As bibliotecas necessárias ao seu código nós chamamos
de *dependências*.

Programas simples em Rust, como o que escrevemos a pouco, não têm nenhuma
dependência, então por enquanto, você só vai usar a parte do Cargo que cuida do
_build_ do seu código. À medida que você escrever programas mais complexos em
Rust, você vai querer adicionar dependências, e utilizando o Cargo, será bem
mais fácil fazer isso. 

Como a vasta maioria dos projetos em Rust usam Cargo, vamos assumir que você vai
usá-lo pelo resto do livro. O Cargo já vem instalado com o próprio Rust, se você
utilizou algum dos instaladores oficiais abordados na seção sobre instalação. Se
você utilizou outros meios para instalar Rust, você pode verificar se tem o
Cargo instalado digitando o seguinte comando no seu terminal:

```text
$ cargo --version
```

Se aparecer um número de versão, ótimo! Mas, se aparecer um erro como `command
not found`, você deve olhar a documentação do método de instalação utilizado,
para verificar como instalar Cargo em seu ambiente.

### Criando um Projeto com Cargo

Vamos criar um novo projeto usando Cargo e ver o que muda em relação ao nosso
primeiro projeto `ola_mundo`. Volte ao seu diretório de projetos (ou aonde quer
que você tenha colocado o seu código):

Linux e Mac:

```text
$ cd ~/projetos
```

Windows:

```cmd
> cd %USERPROFILE%\projetos
```

E então execute o comando abaixo:

```text
$ cargo new ola_cargo --bin
$ cd ola_cargo
```

Passamos o argumento `--bin` para o comando `cargo new` porque o nosso objetivo
é fazer uma aplicação executável, em vez de uma biblioteca. Executáveis são
arquivos binários, muitas vezes chamados apenas de *binários*. Demos o nome
`ola_cargo` ao nosso projeto, e o Cargo já criou os arquivos dele em uma nova
pasta com o mesmo nome, que agora podemos acessar.

Se listarmos os arquivos na pasta *ola_cargo*, podemos ver que o Cargo gerou
dois arquivos e um diretório para nós: um arquivo *Cargo.toml* e um diretório
*src*, com um arquivo *main.rs* dentro dele. Também automaticamente já
inicializou um repositório git na pasta *ola_cargo*, junto com um arquivo
*.gitignore*. Você pode mudar isso se quiser usar um sistema de controle de
versão diferente, ou se não quiser usar nenhum. Basta usar a flag `--vcs`.

Abra o arquivo *Cargo.toml* no seu editor de texto. O conteúdo dele é algo
assim:

<span class="filename">Arquivo: Cargo.toml</span>

```toml
[package]
name = "ola_cargo"
version = "0.1.0"
authors = ["Seu Nome <voce@exemplo.com>"]

[dependencies]
```

Este arquivo está no formato [*TOML*][toml]<!-- ignore --> (_Tom’s Obvious,
Minimal Language_). TOML é similar a INI com algumas coisinhas a mais e é
usado como formato de configuração do Cargo.

[toml]: https://github.com/toml-lang/toml

A primeira linha, `[package]`, é um cabeçalho de seção que indica que o que vem
em seguida está configurando um pacote. Ao passo que adicionamos mais
informações a este arquivo, vamos adicionar outras seções a ele.

As próximas três linhas indicam as três configurações de que o Cargo precisa
para saber como compilar o seu programa: nome, versão, e o autor. O Cargo
consegue o seu nome e _e-mail_ através do seu ambiente. Se não estiver correto,
corrija e depois salve o arquivo.

A última linha, `[dependencies]`, é o começo da seção em que você deve listar
quaisquer *crates* (como chamamos os pacotes de código em Rust) das quais o seu
projeto irá depender, assim o Cargo sabe que deve baixar e compilar estes
pacotes também. Não vamos precisar de nenhum _crate_ para este projeto, mas sim
para o projeto do jogo de advinhação que faremos no próximo capítulo.

Agora vamos dar uma olhada em *src/main.rs*:

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

O Cargo gerou um "Olá, mundo!" para você, igual ao que escrevemos anteriormente
(porém em inglês)! Esta parte é a mesma. As diferenças entre o nosso projeto
anterior e o projeto gerado pelo Cargo, que vimos até agora, foram:

- Nosso código fica dentro do diretório *src*
- A pasta raiz do nosso projeto contém um arquivo de configuração *Cargo.toml*

O Cargo espera que os seus arquivos de código fiquem no diretório *src*, de
maneira que a pasta raiz seja apenas para _READMEs_, informações a respeito de
licença, arquivos de configuração, e tudo mais que não for relacionado a código.
Assim, o uso de Cargo lhe ajuda a manter os seus projetos bem organizados. Há um
lugar pra cada coisa, e cada coisa fica em seu lugar.

Se você iniciou um projeto que não usa Cargo, como fizemos com nosso projeto na
pasta *ola_mundo*, você pode convertê-lo em um projeto que usa o Cargo. Basta
mover o seu código para um diretório *src* e criar um arquivo *Cargo.toml*.

### Realizando o Build e Executando um projeto Cargo

Agora vamos ver o que muda ao compilar e executar o seu projeto "Olá, Mundo!"
através do Cargo! Para isso, digite os seguintes comandos:

```text
$ cargo build
   Compiling ola_cargo v0.1.0 (file:///projetos/ola_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

Este comando deve criar um arquivo executável em *target/debug/ola_cargo* (ou
*target\debug\ola_cargo.exe* no Windows), que você pode executar com o seguinte
comando:

```text
$ ./target/debug/ola_cargo # ou .\target\debug\ola_cargo.exe no Windows
Hello, world!
```

Bam! Se tudo ocorrer bem, a mensagem `Hello, world!` deve aparecer no seu terminal.

Executar `cargo build` pela primeira vez também faz com que Cargo crie um novo
arquivo chamado *Cargo.lock*, com um conteúdo desse tipo:

<span class="filename">Arquivo: Cargo.lock</span>

```toml
[root]
name = "ola_cargo"
version = "0.1.0"
```

O Cargo usa o arquivo *Cargo.lock* para rastrear as dependências da sua
aplicação. Este projeto não tem dependências, então o arquivo é um pouco
escasso. Na realidade, você nem vai precisar mexer neste arquivo, apenas deixe
que o Cargo se encarregue dele.

Nós acabamos de fazer o _build_ de um projeto utilizando o comando
`cargo build`, e o executamos com o comando `./target/debug/ola_cargo`, mas
podemos usar também o comando `cargo run` para compilar e executar em sequência:

```text
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/ola_cargo`
Hello, world!
```

Repare que, desta vez, não vimos nenhuma mensagem de _output_ (saída) nos
dizendo que Cargo estava compilando `ola_cargo`. O Cargo entendeu que não houve
nenhuma mudança nos arquivos, então ele apenas executou o binário. Se você
tivesse modificado o seu código-fonte, o Cargo teria recompilado o projeto antes
de executá-lo, e você teria visto algo do tipo:

```text
$ cargo run
   Compiling ola_cargo v0.1.0 (file:///projetos/ola_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33 secs
     Running `target/debug/ola_cargo`
Hello, world!
```

Então, mais algumas diferenças que vimos até agora foram:

- Em vez de usar `rustc`, faça o _build_ do projeto usando `cargo build` (ou
faça o build e execute de uma vez só utilizando `cargo run`)
- Em vez de colocar o resultado do _build_ no mesmo diretório do nosso código, o
Cargo irá colocar o resultado do _build_ no diretório *target/debug*.

A outra vantagem de usar o Cargo é que os comandos são os mesmos, não importa em
qual sistema operacional você esteja. Por conta disso, a partir de agora, não
vamos mais fornecer instruções específicas para Linux e Mac ou Windows.

### Compilando para _Release_

Quando o seu projeto está finalmente pronto para ser lançado, você pode usar o
comando `cargo build --release` para compilar o seu projeto com otimizações.
Isso vai criar um arquivo executável na pasta *target/release* em vez de
*target/debug*. Estas otimizações fazem o seu código Rust executar de maneira
mais rápida, mas por outro lado, também fazem o programa levar mais tempo para
compilar. Por isso existem dois perfis diferentes: um para desenvolvimento,
quando você deseja recompilar rapidamente e com mais frequência, e outro para
compilar a versão final do seu programa, que vai ser entregue ao usuário e não
será recompilada, e queremos que rode da maneira mais rápida possível. Se você
está fazendo um _benchmarking_ do tempo de execução do seu código, certifique-se
de executar o comando `cargo build --release`, e usar o executável que se
encontra na pasta *target/release*.

### Cargo como Convenção

Em projetos simples, o Cargo não oferece tanto valor assim em relação a usar
apenas o `rustc`, mas vai fazer diferença à medida que você continua. Em
projetos mais complexos, compostos de múltiplos _crates_, é bem mais fácil
deixar o Cargo coordenar o processo de _build_. Com o Cargo, você pode apenas
executar o comando `cargo build`, e tudo deverá funcionar da maneira correta.
Mesmo esse projeto sendo simples, ele utiliza muito das ferramentas que você
irá usar pelo resto da sua carreira em Rust. Na verdade, você pode começar a
trabalhar em praticamente qualquer projeto em Rust usando os seguintes comandos:

```text
$ git clone algumaurl.com/algumprojeto
$ cd algumprojeto
$ cargo build
```

> Nota: Se você deseja conhecer o Cargo em mais detalhes, confira o
[Guia oficial do Cargo]<!-- ignore --> (em inglês), que aborda todas as suas
características.

[Guia oficial do Cargo]: http://doc.crates.io/guide.html
