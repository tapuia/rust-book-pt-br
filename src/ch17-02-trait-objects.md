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