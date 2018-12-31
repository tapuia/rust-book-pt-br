## Usando objetos trait que permitem valores de tipos diferentes

No Capítulo 8, mencionamos que uma limitação dos vetores é que eles apenas podem
armazenar elementos do mesmo tipo. Criamos uma solução alternativa na Listagem 8-10, onde
definimos um enum chamado `SpreadsheetCell` que tinha variantes para conter inteiros, flutuantes
e texto. Isso significa que poderiamos armazenar diferentes tipos de dados em cada célula e
ainda ter um vetor que representasse uma linha de células. Isso é uma solução ótima
quando nossos itens intercambiáveis são um conjunto fixo de tipos que sabemos
quando nosso código é compilado.

No entanto, algumas vezes queremos que nosso usuário de biblioteca seja capaz de estender o conjunto de
tipos que são válidos em uma situação específica. Para mostrar como podemos alcançar
isso, criaremos um exemplo de ferramenta de interface gráfica (GUI) que interage
através de uma lista de itens, chamando um método `draw` em cada um para desenhá-lo
na tela - uma técnica comum para ferramentas GUI. Criaremos uma crate chamada 
`gui` que contém a estrutura da biblioteca GUI. Essa crate pode incluir
alguns tipos para as pessoas usarem, como um `Button` ou `TextField`. Além disso,
usuários de `gui`vão querer criar seus próprios tipos que podem ser desenhados: por
exemplo, um programados pode adicionar uma `Image` e outro pode adicionar um
`SelectBox`.

Não implementamos uma biblioteca gráfica completa para esse exemplo, mas mostraremos
como as peças se encaixariam. No momento de escrever a biblioteca, não podemos
saber e definir todos os tipos que outros programadores podem querer criar. Mas sabemos
que `gui` precisa manter o controle de diferentes valores de diferentes tipos e ele
precisa chamar o método `draw` em cada um desses diferentes tipos de valores. Não
é necessário saber exatamente o que acontecerá quando chamarmos o método `draw`,
apenas que o valor tera este método disponível para executarmos.

Para fazer isso em uma linguagem com herança, podemos definir uma classe chamada
`Component` que possui um método chamado `draw`. As outras classes, como as
`Button`, `Image` e `SelectBox`, herdam de `Component` e, assim,
herdam o método `draw`. Cada uma pode sobrescrever o método `draw` para definir
seu comportamento próprio, mas o framework poderia tratar todos esses tipos se
eles fossem instâncias de `Component` e chamar `draw` neles. Mas como Rust
não tem herança, precisamos de outra maneira para estruturar a biblioteca `gui` para
perminir que os usuários o estendam com novos tipos.

### Definindo um Trait para componentes comuns

Para implementar o comportamento que queremos que `gui` tenha, definiremos um trait chamado
`Draw` que terá um método chamado `draw`. Então podemos definir um vetor
que tenha um *objeto trait*. Um objeto trait aponta para uma instância de um tipo que
implmenta o trait que especificamos. Criamos um objeto trait especificando alguns
tipos de ponteiros, como uma referência `&` ou um ponteiro `Box<T>` e
especificando um trait relevante (falaremos sobre o motimo pelo qual os objetos trait
devem ser usados no Capítulo 19, na seção "Tipos e tamanhos dimensionados dinamicamente").
Podemos usar objetos trait no lugar de um tipo genérico ou concreto. Onde quer que usemos
um objeto trait, o sistema de tipos do Rust irá garantir em tempo de compilação que qualquer
valor usado nesse contexto implementará o trait de um objeto trait.
Consequentemente, não precisamos saber todos os possíveis tipos em tempo de compilação.

Mencionamos que em Rust evitamos de chamar estruturas e enums de
"objetos" para distingui-los dos objetos de outras linguagens. Em uma estrutura ou
enum, o dado nos campos e o comportamento no bloco `impl` são
separados, enquanto em outras linguagens o dado e o comportamento são combinados em um
conceito muitas vezes chamado de objeto. No entanto, objetos trait *são* mais como
objetos em outras linguagens no sentido de combinar dados e comportamento.
Mas objetos trait diferem de objetos tradicionais, pois não podemos adicionar dados
a um objeto trait. Objetos trait geralmente não são proveitosas como um objeto de outras
linguagens: sua finalidade é simplemente possibilitar a abstração entre
comportamento comum.

Listagem 17-3 mostra como definir um trait chamado `Draw` com um método chamado
`draw`:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
pub trait Draw {
    fn draw(&self);
}
```

<span class="caption">Listagem 17-3: Definição do trait `Draw`</span>

Essa sintaxe deve parecer familiar de outras discussões de como definir traits
do Capítulo 10. Em seguida, vem uma nova sintaxe: A Listagem 17-4 define uma estrutuca chamada
`Screen` que contém um vetor chamado `components`. Esse vetor é do tipo
`Box<Draw>`, que é um objeto trait: é um substituto para qualquer tipo dentro de um
`Box` que implementa o trait `Draw`.

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub trait Draw {
#     fn draw(&self);
# }
#
pub struct Screen {
    pub components: Vec<Box<Draw>>,
}
```

<span class="caption">Listagem 17-4: Definição da estrutura `Screen` com um
campo `components` que contém um vetor de objetos trait que implementam o
trait `Draw`</span>

Na estrutura `Screen`, definiremos um método chamado `run` que irá chamar o
método `draw` em cada item do `components`, como mostrado na Listagem 17-5:

<span class="filename">Filename: src/lib.rs</span>

```rust
# pub trait Draw {
#     fn draw(&self);
# }
#
# pub struct Screen {
#     pub components: Vec<Box<Draw>>,
# }
#
impl Screen {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

<span class="caption">Listagem 17-5: Implementando um método`run` na `Screen`
que chama o método `draw` para cada componente</span>

Isso funciona de forma diferente do que definir uma estrutura que usa um parâmetro de tipo
genérico com trait bounds. Um parâmetro de tipo genérico pode
apenas ser substituido por um tipo concreto de cada vez, enquanto objetos trait permitem vários tipos
concretos para preencher o objeto trait em tempo de execução. Por exemplo, poderíamos
ter definido a estrutura `Screen` usando um tipo genérico e um trait bounds
como na Listagem 17-6:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
# pub trait Draw {
#     fn draw(&self);
# }
#
pub struct Screen<T: Draw> {
    pub components: Vec<T>,
}

impl<T> Screen<T>
    where T: Draw {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

<span class="caption">Listagem 17-6: Uma implementação alternativa da estrutura `Screen`
e seu método `run` usando genéricos e trait bounds</span>

Isso nos restringe a uma instância de `Screen` que tem uma lista de componentes, todos
do tipo `Button` ou do tipo `TextField`. Se você tiver somente coleções do mesmo tipo,
usar genéricos e  trait bounds é preferível, porque as
definições serão monomorfizadas em tempo de compilação para os tipos concretos.

Por outro lado, com o método usando objetos trait, uma instância de `Screen`
pode conter um `Vec` que contém um `Box<Button>` assim como um `Box<TextField>`.
Vamos ver como isso funciona e falaremos sobre as impliciações do desempenho
em tempo de compilação.