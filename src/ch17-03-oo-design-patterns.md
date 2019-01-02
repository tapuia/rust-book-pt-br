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

