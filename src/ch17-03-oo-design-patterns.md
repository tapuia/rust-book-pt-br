## Implementando um padrão de projeto orientado a objetos

O *padrão de estado* é um padrão de projeto orientado a objetos. O ponto crucial do padrão
é que um valor tem algun estado interno, que é representado por um conjunto
de *objetos de estado* e o comportamento do valor é alterado com base no estado
interno. Os objetos de estado compartilham funcionalidade: em Rust, é claro, usamos
estruturas e traits em vez de de objetos e herança. Cada objeto de estado é
responsável por seu próprio comportamento e por administrar quando dev mudar para outro
estado. O valor que contém um objeto de estado não sabe nada sobre o
comportamento diferente dos estados ou quando fazer transição entre os estados.

Usando o padrão de estados significa que quando os requisitos do negócio do programa
mudam, não precisamos mudar o código do valor que detém o estado ou o
código que usa o valor. Precisamos apenas atualizar o código dentro de um dos
objetos de estados para mudar suas regras ou talvez adicionar mais objetos de estados. Vamos ver
um exemplo de padrão de projeto de estados e como usá-lo no Rust.

Implementaremos um fluxo de trabalho de postagens de maneira incremental. A funcionalidade
final do blog será assim:

1. Uma postagem no blog começa como um rascunho vazio.
2. Quando o rascunho é concluído, é necessária uma revisão da postagem.
3. Quando a postagem é aprovada, ela é aprovada.
4. Apenas postagens de blog publicadas retornam conteúdo para impressão, portanto, postagens não aprovadas não podem ser
publicadas por acidente.

Quaisquer outras tentativas de mudança em uma postagem não deve ter efeito. Por exemplo, se
tentarmos aprovar um rascunho de postagem no blog antes de solicitarmos uma revisão, a postagem
a postagem deve permanecer em rascunho não publicado.

Listagem 17-11 mostra esse fluxo de trabalho em forma de código: este é um exemplo de uso de
API que implementaremos em um biblioteca crate chamada `blog`. Isso ainda não foi compilado,
porque não tempos implementado o crate `blog`:

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
extern crate blog;
use blog::Post;

fn main() {
    let mut post = Post::new();

    post.add_text("I ate a salad for lunch today");
    assert_eq!("", post.content());

    post.request_review();
    assert_eq!("", post.content());

    post.approve();
    assert_eq!("I ate a salad for lunch today", post.content());
}
```

<span class="caption">Listagem 17-11: Código que demonstra o desejado
comportamento que queremos que o nosso crate`blog` tenha</span>

Queremos permitir que o usuário crie uma nova postagem de blog com `Post :: new`.
Então, queremos permitir que o texto seja adicionado à postagem do blog enquanto ela estiver no estado de
rascunho. Se tentarmos obter o conteúdo da postagem imediatamente, antes da aprovação,
nada deve acontecer porque a postagem ainda é um rascunho. Adicionamos
`assert_eq!` no código para fins de demonstração. Um excelente teste unitário para
isso seria afirmar que uma postagem do blog em rascunho retorna uma string vazia do método
`content`, mas não vamos escrever testes para este exemplo.

Em seguida, queremos possibilitar uma solicitação de revisão para a postagem e queremos que o
`content ` retorne uma string vazia enquanto aguarda a revisão. Quand a postagem
for aprovada, deve ser publicada, significa que o texto da postagem
será retornada quando o `content` for chamado.

Observe que o único tipo com o qual estamos interagindo, do crate, é o
tipo `Post`. Esse tipo usará padrão de estados e terá um valor que será
um dos três estados de objeto, representam os vários estados em que uma postagem pode estar
em - rascunho, esperando por revisão ou publicada. Mudar de um estado para outro será 
gerenciado internamente com o tipo `Post`. Os estados mudam em
resposta aos métodos chamados pelos usuários da bibliotéca sobre a instância `Post`,
mas eles não precisam gerenciar as alterações de estados diretamente. Além disso, usuários não podem
cometer erros nos estados, como publicar uma postagem antes de revisá-la.

### Definindo `Post` e criando uma nova instância no estado de rascunho

Vamos começar a implementação da biblioteca! Sabemos que precisamos da
estrutura pública `Post` que contenha algum conteúdo, por isso começaremos com a
definição da estrutura e a função pública `new` associada para criar uma
instância de `Post`, como mostra a Listageḿ 17-12. Também faremos um trait privado
`State`. Então o `Post` conterá um objeto trait `Box<State>` dentro de um
`Option` em um campo privado, chamado `state`. Você verá porquê o `Option`
é necessário.

O trait `State` define o comportamento compartilhado por diferentes estados de postagem e os
estados `Draft`, `PendingReview` e `Published` implementarão todos os
trait `State`. Por equanto, o trait não tem nenhum método; e começaremos definindo
apenas o estado `Draft`, porque esse é o estado em que queremos uma postagem inicialmente:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
pub struct Post {
    state: Option<Box<State>>,
    content: String,
}

impl Post {
    pub fn new() -> Post {
        Post {
            state: Some(Box::new(Draft {})),
            content: String::new(),
        }
    }
}

trait State {}

struct Draft {}

impl State for Draft {}
```

<span class="caption">Listagem 17-12: Definição da estrutura `Post` e a função `new`,
que cria uma nova instância de `Post`, um trait `State` e uma 
estrutura `Draft`</span>

Quando criamos um novo `Post`, definimos seu campo `state` como um valor `Some`, que
conterá um `Box`. Este `Box` aponta para uma nova instância da estrutura `Draft`. Isso
garante que sempre criamos uma nova instância de `Post`, ela começará como um
rascunho. Como o campo `state` do `Post` é privado, não há como
criar um `Post` em qualquer outro estado!

### Armazenando o texto do conteúdo do post

Na função `Post::new`, definimos o campo `content` como uma novo
`String` vazia. Listagem 17-11 mostrou que queremos poder chamar o método chamado
`add_text` e passar um `&str` que é então adicionado ao conteúdo do texto da
postagem do blog. Implementamos isso como uma método, em vez de expor o campo `content`
como `pub`. Isso significa que podemos implementar um método posteriormente que controlará
como os dados do campo `content` são lidos. O método `add_text` é bastante direto,
então vamos adicionar a implementação na Listagem 17-13 ao bloco
`impl Post`:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub struct Post {
#     content: String,
# }
#
impl Post {
    // --snip--
    pub fn add_text(&mut self, text: &str) {
        self.content.push_str(text);
    }
}
```

<span class="caption">Listagem 17-13: Implementando o método `add_text` para adicionar o
texto ao `content` da postagem</span>

O método `add_text` usa uma referência mutável ao `self`, porque estamos
mudando a instância `Post` que estamos chamando a partir de`add_text`. Então chamamos
`push_str` na `String` em `content` e passamos o argumento `text` para adicionar ao `content`
salvo. Esse comportamento não depende do estado em que a postagem está,
portanto, não faz parte do padrão de estados. O método `add_text` não interage
com o campo `state`, mas faz parte do comportamento que queremos
suportar.

### Garantindo que o conteúdo de um rascunho de postagem esteja vazio

Mesmo depois que chamamos `add_text` e adicionamos algum conteúdo para nossa postagem, ainda
queremos que o método `content` retorne um pedaço de string vazia, porque a postagem ainda
está no está de rascunho, como mostrado na linha 8 da Listagem 17-11. Por hora, vamos
implementar o método `content` com a coisa mais simples que atenderá a esse
requisito: sempre retornando um pedaço de string vazia. Mudaremos isso mais tarde,
quando implementaremos a possibilidade de mudar o estado de uma postagem para que ela possa ser publicada.
Até agora, postagens apenas podem estar no estado de rascunho, portanto, o conteúdo da publicação deve estar
vazio. Listagem 17-14 mostra essa implementação substituta:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub struct Post {
#     content: String,
# }
#
impl Post {
    // --snip--
    pub fn content(&self) -> &str {
        ""
    }
}
```

<span class="caption">LIstagem 17-14: Adicionando temporária para
o método `content` do `Post` que sempre retorna uma string vazia</span>

Como o método `content` adicionado, tudo na Listagem 17-11 até a linha 8
funciona como prentendido.

### Solicitando uma revisão da postagem que altera seu estado

Em seguida, nós precisamos adicionar funcionalidade para solicitar uma revisão da postagem, que deve
mudar seu estado de `Draft` para `PendingReview`. Listagem 17-15 mostra este código:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub struct Post {
#     state: Option<Box<State>>,
#     content: String,
# }
#
impl Post {
    // --snip--
    pub fn request_review(&mut self) {
        if let Some(s) = self.state.take() {
            self.state = Some(s.request_review())
        }
    }
}

trait State {
    fn request_review(self: Box<Self>) -> Box<State>;
}

struct Draft {}

impl State for Draft {
    fn request_review(self: Box<Self>) -> Box<State> {
        Box::new(PendingReview {})
    }
}

struct PendingReview {}

impl State for PendingReview {
    fn request_review(self: Box<Self>) -> Box<State> {
        self
    }
}
```

<span class="caption">Listagem 17-15: Implementando método `request_review` no
`Post` e no trait `State`</span>

Nós fornecemos ao `Post` um método público chamado `request_review` que irá tormar uma referência
mutável para `self`. Em seguida, chamamos internamente o método `request_review`
do estado atual do `Post` e esse segundo método `request_review` consome o
estado atual e retorna um novo estado.

Adicionamos o método `request_review` para o trait `State`; todos os tipos
que implementam o trait, agora precisarão implementar o método `request_review`.
Note que em vez de ter `self`, `&self` ou `&mut self` como
primeiro parâmetro do método, temos `self: Box<Self> `. Essa sintaxe significa que
o método é apenas válido quando chamado em um `Box` contendo o tipo. Essa sintaxe apropria-se
do `Box<Self>`, invalidando o antigo estado para que o valor de estado do
`Post` possa se transfor em um novo estado.

Para consumir o antigo estado, o método `request_review` precisa apropriar-se
do valor do estado. Este é o lugar onde o `Option` no campo `state` do `Post`:
chamamos o método `take` para tirar o valor de `Some` do campo `state`
e deixar um `None` no lugar, porque Rust não nos permite ter
campos não preenchidos nas estruturas. Isso nos permite mover o valor do `state` para fora
do `Post` em vez de pedir emprestado. Em seguida, definiremos o valor do `state` da postagem como
resultado da operação.

Precisamos definir o `state` como `None` temporariamente em vez de configurá-la diretamente
com o código `self.state = self.state.request_review();` para obter a posse do
valor de `state`. Isso garante que o `Post` não pode usar o antigo valor do `state` depois
de transformá-lo em um novo estado.

O método `request_review` no `Draft` precisa retornar uma nova instância em caixa de
uma nova estrutura `PendingReview`, que representa o estado quando uma postagem está aguardando
uma revisão. A estrutura `PendingReview` também implementa o método `request_review`,
mas não faz nenhuma transformação. Em vez disso, ele retorna a si mesmo, porque
quando solicitamos uma revisão em uma publicação já no estado `PendingReview`, ele deve
permanecer no estado `PendingReview`.

Agora podemos começar a ver as vantagens do padrçao de estados: o método
`request_review` no `Post` é o mesmo, não importa seu valor `state`. Cada
estado é responsável por suas próprias regras.

Deixaremos o método `content` do `Post` como está, retornando uma string
vazia. Agora podemos ter um `Post` no estado `PendingReview`, bem como no estado
`Draft`, mas queremos o mesmo comportamento no estado `PendingReview`.
Listagem 17-11 agora funciona até a linha 11!

### Adicionando o método `approve` que muda o coportamento do `content`

O método `approve` será semelhante ao método `request_review`: ele
definirá `state` com um valor que o estado atual diz que deve ter quando esse
estado é aprovado, como mostra a Listagem 17-16:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub struct Post {
#     state: Option<Box<State>>,
#     content: String,
# }
#
impl Post {
    // --snip--
    pub fn approve(&mut self) {
        if let Some(s) = self.state.take() {
            self.state = Some(s.approve())
        }
    }
}

trait State {
    fn request_review(self: Box<Self>) -> Box<State>;
    fn approve(self: Box<Self>) -> Box<State>;
}

struct Draft {}

impl State for Draft {
#     fn request_review(self: Box<Self>) -> Box<State> {
#         Box::new(PendingReview {})
#     }
#
    // --snip--
    fn approve(self: Box<Self>) -> Box<State> {
        self
    }
}

struct PendingReview {}

impl State for PendingReview {
#     fn request_review(self: Box<Self>) -> Box<State> {
#         self
#     }
#
    // --snip--
    fn approve(self: Box<Self>) -> Box<State> {
        Box::new(Published {})
    }
}

struct Published {}

impl State for Published {
    fn request_review(self: Box<Self>) -> Box<State> {
        self
    }

    fn approve(self: Box<Self>) -> Box<State> {
        self
    }
}
```

<span class="caption">Listagem 17-16: Implementando o método `approve` no
`Post` e o trait `State`</span>

Adicionamos o método `approve` para o trait `State` e adicionamos uma nova estrutura que
implementa `State`, o estado `Published`.

Semelhante ao `request_review`, se chamarmos o método `approve` no `Draft`, ele
não terá efeito, porque ele retornará `self`. Quando chamamos `approve` do
`PendingReview`, ele retorna uma nova instância em caixa da estrutura `Published`.
A estrutura `Published` implementa o trait `State` e, tanto para
o método `request_review` quanto para o `approve`, ele retorna si próprio., porque
a postagem deve permanecer no estado `Published` nesses casos.

Agora, precisamos atualizar o método `content` do `Post`: se o estado for
`Published`, queremos que retorne o valor do campo `content` da publicação;
caso contrário, queremos que retorne uma string vazia, como mostra a Listagem 17-17:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# trait State {
#     fn content<'a>(&self, post: &'a Post) -> &'a str;
# }
# pub struct Post {
#     state: Option<Box<State>>,
#     content: String,
# }
#
impl Post {
    // --snip--
    pub fn content(&self) -> &str {
        self.state.as_ref().unwrap().content(&self)
    }
    // --snip--
}
```

<span class="caption">Listagem 17-17: Atualizando o método `content` do `Post` para
encarregar o método `content` em `State`</span>

Porque o objetivo é manter todos essas regras dentro das estruturas que implementam
`State`, chamamos o método `content`no valor em `state` e passamos a instância
da postagem (que é, `self`) como um argumento. Então retornamos o valor que é
retornado usando o método `content` do valor do `state`.

Nós chamamos o método `as__ref` do `Option` porque queremos uma referência ao valor
do `Option` em vez da propriedade do valor. Como `state`
é um `Option<Box<State>>`, quando chamamos `as_ref`, um `Option<Box<State>>` é
retornado. Se não chamarmos `as__ref`, receberíamos um erro,
porque não podemos obter `state` emprestado do `&self` do parâmetro da função.

Então chamamos o método `unwrap`, que sabemos que nunca vai entrar em pânico, porque sabemos
que os métodos em `Post`garantem que o `state` sempre conterá um valor `Some`
quando esses métodos forem realizados. Esse é um dos casos sobre os quais falamos na
seção "Casos em que Você Tem Mais Informação Que o Compilador" do Capítulo
9, quando sabemos que um valor `None` nunca é possível, mesmo que o compilador não
consiga ententer isso.

Nesse momento, quando chamamos `content` no `&Box<State>`, a coerção deref terá
efeito no `&` e no `Nox`, então finalmente o método
`content` é chamado no tipo que implementa o trait `State`. Isso significa que precisamos adicionar
`content` à definição do trait `State` e que é onde colocaremos
a lógica de qual conteúdo retornar, dependendo do estado que temos, como mostra
a Listagem 17-18:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub struct Post {
#     content: String
# }
trait State {
    // --snip--
    fn content<'a>(&self, post: &'a Post) -> &'a str {
        ""
    }
}

// --snip--
struct Published {}

impl State for Published {
    // --snip--
    fn content<'a>(&self, post: &'a Post) -> &'a str {
        &post.content
    }
}
```

<span class="caption">Listagem 17-18: Adicionando o método `content` ao trait
`State`</span>

Adicionamos uma implementação padrão para o método `content`, que retorna uma
string vazia. Isso significa que não preciamos implementar `content` nas estruturas `Draft` e
`PendingReview`. A estrutura `Published` irá sobrepor o método `content`
e retornar o valor do `post.content`.

Observe que precisamos anotações de vida útil nesse método, como discutimos no
Capítulo 10. Estamos fazendo uma referência a um `post` como argumento e retornando uma
referência a parte desse `post`, então o tempo de vida útil da referência retornada é
relacionada ao tempo de vida útil do argumento `post`.

E estamos prontos - tudo da Listagem 17-11 agora funcionam! Nós implementamos o padrão de estados
com as regras do fluxo de trabalho da postagem no blog. A lógica relacionada
às regras vive nos objetos de estados, em vez de estar espalhada por todo o `Post`.