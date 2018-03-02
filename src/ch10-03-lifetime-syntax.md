## Validating References with Lifetimes

## Validando Referências com Tempos de Vida

When we talked about references in Chapter 4, we left out an important detail:
every reference in Rust has a *lifetime*, which is the scope for which that
reference is valid. Most of the time lifetimes are implicit and inferred, just
like most of the time types are inferred. Similarly to when we have to annotate
types because multiple types are possible, there are cases where the lifetimes
of references could be related in a few different ways, so Rust needs us to
annotate the relationships using generic lifetime parameters so that it can
make sure the actual references used at runtime will definitely be valid.

Quandos falamos sobre referêcias no Capítulo 4, nós deixamos de fora um detalhe
importante: toda referência em Rust tem um *tempo de vida*, que é o escopo no qual
aquela referência é válida. Na maior parte das vezes tempos de vida são implícitos e
inferidos, assim como a maior parte do tempo tipos são inferidos. Similarmente
quando temos que anotar typos porque múltiplos tipos são possíveis, há casos em
que os tempos de vida das referências poderiam estar relacionados de alguns modos
diferentes, então Rust precisa que nós anotemos as relações usando parâmetros
genéricos de tempo de vida para que ele tenha certeza que as referênciais atuais
usadas em tempo de execução serão definitivamente válidas.

Yes, it’s a bit unusual, and will be different to tools you’ve used in other
programming languages. Lifetimes are, in some ways, Rust’s most distinctive
feature.

Sim, é um pouco incomum, e será diferente de ferramentas que você usou em 
outras linguagens de programação. Tempos de vida são, de alguns jeitos, a 
característica mais distinta de Rust. 

Lifetimes are a big topic that can’t be covered in entirety in this chapter, so
we’ll cover common ways you might encounter lifetime syntax in this chapter to
get you familiar with the concepts. Chapter 19 will contain more advanced
information about everything lifetimes can do.

Tempos de vida são um tópico grande que não poderão ser cobertos inteiramente 
nesse capítulo, então nós vamos cobrir algumas formas comuns que você pode 
encontrar a sintaxe de tempo de vida nesse capítulo para que você se 
familiarize com os conceitos. O Capítulo 19 conterá informações mais avançadas
sobre tudo que tempos de vida podem fazer.

### Lifetimes Prevent Dangling References

### Tempos de Vida Previnem Referências Soltas

The main aim of lifetimes is to prevent dangling references, which will cause a
program to reference data other than the data we’re intending to reference.
Consider the program in Listing 10-18, with an outer scope and an inner scope.
The outer scope declares a variable named `r` with no initial value, and the
inner scope declares a variable named `x` with the initial value of 5. Inside
the inner scope, we attempt to set the value of `r` as a reference to `x`. Then
the inner scope ends, and we attempt to print out the value in `r`:

O principal alvo de lifetimes é prevenir referências soltas, que fazem com 
que o programa referencie dados quais nós não estamos querendo referenciar.
Considere o programa na Listagem 10-18, com um escopo exterior e um interior.
O escopo exterior declara uma variável chamada `r` com nenhum valor inicial, e
o escopo interior declara uma variável chamada `x` com o valor inicial de 5.
Dentro do escopo interior, nós tentamos estabelecer o valor de `r` como uma 
referência para `x`. Então, o escopo interior acaba, e nós tentamos imprimir o
valor de `r`:

```rust,ignore
{
    let r;

    {
        let x = 5;
        r = &x;
    }

    println!("r: {}", r);
}
```

```rust,ignore
{
    let r;

    {
        let x = 5;
        r = &x;
    }

    println!("r: {}", r);
}
```

<span class="caption">Listing 10-18: An attempt to use a reference whose value
has gone out of scope</span>

<span class="caption">Listagem 10-18: Uma tentativa de usar uma refência cujo
valor saiu de escopo</span>

> #### Uninitialized Variables Cannot Be Used
>
> The next few examples declare variables without giving them an initial value,
> so that the variable name exists in the outer scope. This might appear to be
> in conflict with Rust not having null. However, if we try to use a variable
> before giving it a value, we’ll get a compile-time error. Try it out!

> #### Variáveis Não Inicializadas Não Podem Ser Usadas
>
> Os próximos exemplos declaram vaŕiáveis sem darem a elas um valor inicial, 
> então o nome da variável existe no escopo exterior. Isso pode parecer um 
> conflito com Rust não ter null. No entanto, se tentarmos usar uma variável
> antes de atribuir um valor a ela, nós teremos um erro em tempo de compilação.
> Tente!

When we compile this code, we’ll get an error:

Quando compilarmos esse códgiso, nós teremos um erro:

```text
error: `x` does not live long enough
   |
6  |         r = &x;
   |              - borrow occurs here
7  |     }
   |     ^ `x` dropped here while still borrowed
...
10 | }
   | - borrowed value needs to live until here
```

```text
error: `x` does not live long enough
   |
6  |         r = &x;
   |              - borrow occurs here
7  |     }
   |     ^ `x` dropped here while still borrowed
...
10 | }
   | - borrowed value needs to live until here
```

The variable `x` doesn’t “live long enough.” Why not? Well, `x` is going to go
out of scope when we hit the closing curly bracket on line 7, ending the inner
scope. But `r` is valid for the outer scope; its scope is larger and we say
that it “lives longer.” If Rust allowed this code to work, `r` would be
referencing memory that was deallocated when `x` went out of scope, and
anything we tried to do with `r` wouldn’t work correctly. So how does Rust
determine that this code should not be allowed?

A variável `x` não "vive o suficiente". Por que não? Bem, `x` vai sair de 
escopo quando passarmos pela chaves na linha 7, terminando o escopo interior.
Mas `r` é válida para o escopo exterior; seu escopo é maior e dizemos que ela
"vive mais tempo". Se Rust permitisse que esse código funcionasse, `r` estaria
fazendo uma referência à memória que foi desalocada quando `x` saiu de escopo,
e qualquer coisa que tentássemos fazer com `r` não funcionaria corretamente.
Então como o Rust determina que esse código não deve ser permitido?

#### The Borrow Checker

#### O Verificador de Empréstimos

The part of the compiler called the *borrow checker* compares scopes to
determine that all borrows are valid. Listing 10-19 shows the same example from
Listing 10-18 with annotations showing the lifetimes of the variables:

A parte do compilador chamada de *verificador de empréstimos* compara escopos
para determinar que todos os empréstimos são válidos. A Listagem 10-19 mostra o
mesmo exemplo da Listagem 10-18 com anotações mostrando os tempos de vida das
variáveis.

```rust,ignore
{
    let r;         // -------+-- 'a
                   //        |
    {              //        |
        let x = 5; // -+-----+-- 'b
        r = &x;    //  |     |
    }              // -+     |
                   //        |
    println!("r: {}", r); // |
                   //        |
                   // -------+
}
```


```rust,ignore
{
    let r;         // -------+-- 'a
                   //        |
    {              //        |
        let x = 5; // -+-----+-- 'b
        r = &x;    //  |     |
    }              // -+     |
                   //        |
    println!("r: {}", r); // |
                   //        |
                   // -------+
}
```

<span class="caption">Listing 10-19: Annotations of the lifetimes of `r` and
`x`, named `'a` and `'b` respectively</span>

<span class="caption">Listagem 10-19: Anotações de tempos de vida de `r` e `x`,
chamadas de `a` e `b` respectivamente</span>

<!-- Just checking I'm reading this right: the inside block is the b lifetime,
correct? I want to leave a note for production, make sure we can make that
clear -->
<!-- Yes, the inside block for the `'b` lifetime starts with the `let x = 5;`
line and ends with the first closing curly bracket on the 7th line. Do you
think the text art comments work or should we make an SVG diagram that has
nicer looking arrows and labels? /Carol -->

We’ve annotated the lifetime of `r` with `'a` and the lifetime of `x` with
`'b`. As you can see, the inner `'b` block is much smaller than the outer `'a`
lifetime block. At compile time, Rust compares the size of the two lifetimes
and sees that `r` has a lifetime of `'a`, but that it refers to an object with
a lifetime of `'b`. The program is rejected because the lifetime `'b` is
shorter than the lifetime of `'a`: the subject of the reference does not live
as long as the reference.

Nós anotamos o tempo de vida de `r` com `a` e o tempo de vida de `x` com `b`.
Como você pode ver, o bloco interior de `'b` é bem menor que o bloco de tempo
de vida do exterior `'a'`. Em tempo de compilação, o Rust compara o tamanho dos
dois tempos de vida e vê que `r` tem um tempo de vida de `'a`, mas que ele se
refere a um objeto com um tempo de vida `'b`. O programa é rejeitado porque o 
tempo de vida de `'b` é mais curto que o tempo de vida de `'a`: o sujeito da
referência não vive tanto quanto a referência.

Let’s look at an example in Listing 10-20 that doesn’t try to make a dangling
reference and compiles without any errors:

Vamos olhar para o exemplo na Listagem 10-20 que não tenta fazer uma referência
solta e compila sem nenhum erro:

```rust
{
    let x = 5;            // -----+-- 'b
                          //      |
    let r = &x;           // --+--+-- 'a
                          //   |  |
    println!("r: {}", r); //   |  |
                          // --+  |
}                         // -----+
```

```rust
{
    let x = 5;            // -----+-- 'b
                          //      |
    let r = &x;           // --+--+-- 'a
                          //   |  |
    println!("r: {}", r); //   |  |
                          // --+  |
}                         // -----+
```

<span class="caption">Listing 10-20: A valid reference because the data has a
longer lifetime than the reference</span>

<span class="caption">Listagem 10-20: Uma referência válida porque os dados têm
um tempo de vida maior do que o da referência</span>

Here, `x` has the lifetime `'b`, which in this case is larger than `'a`. This
means `r` can reference `x`: Rust knows that the reference in `r` will always
be valid while `x` is valid.

Aqui, `x` tem o tempo de vida de `'b`, que nesse caso tem um tempo de vida 
maior que o de `'a`. Isso quer dizer que `r` pode referenciar `x`: o Rust sabe
que a referência em `r` será sempre válida enquanto `x` for válido.

Now that we’ve shown where the lifetimes of references are in a concrete
example and discussed how Rust analyzes lifetimes to ensure references will
always be valid, let’s talk about generic lifetimes of parameters and return
values in the context of functions.

Agora que mostramos onde os tempos de vida de referências estão em um exemplo
concreto e discutimos como Rust analisa tempos de vida para garantir que
referências sempre serão válidas, vamos falar sobre tempos de vidas genéricos
de parâmetros e retornar valores no contexto das funções.

### Generic Lifetimes in Functions

### Tempos de Vida Génericos em Funções

Let’s write a function that will return the longest of two string slices. We
want to be able to call this function by passing it two string slices, and we
want to get back a string slice. The code in Listing 10-21 should print `The
longest string is abcd` once we’ve implemented the `longest` function:

Vamos escrever uma função que retornará a mais longa de dois cortes de string. 
Nós  queremos ser capazes de chamar essa função passando para ela dois cortes 
de strings, e queremos que retorne uma string. O código na Listagem 10-21
deve imprimir `A string mais longa é abcd` uma vez que tivermos implementado a
função `maior`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}
```

```rust,ignore
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let resultado = maior(string1.as_str(), string2);
    println!("A string mais longa é {}", resultado);
}
```

<span class="caption">Listing 10-21: A `main` function that calls the `longest`
function to find the longest of two string slices</span>

<span class="caption">Listagem 10-21: Uma função `main` que chama pela função
`maior` para achar a mais longa entre duas strings</span>

Note that we want the function to take string slices (which are references, as
we talked about in Chapter 4) since we don’t want the `longest` function to
take ownership of its arguments. We want the function to be able to accept
slices of a `String` (which is the type of the variable `string1`) as well as
string literals (which is what variable `string2` contains).

Note que queremos que a função pegue cortes de string (que são referências, 
como falamos no Capítulo 4) já que não queremos que a função `maior` tome posse
de seus argumentos. Nós queremos que uma função seja capaz de aceitar cortes de
uma `String` (que é o tipo de variável `string1`) assim como literais de string
(que é o que a variável `strin2` contém).

<!-- why is `a` a slice and `b` a literal? You mean "a" from the string "abcd"? -->
<!-- I've changed the variable names to remove ambiguity between the variable
name `a` and the "a" from the string "abcd". `string1` is not a slice, it's a
`String`, but we're going to pass a slice that refers to that `String` to the
`longest` function (`string1.as_str()` creates a slice that references the
`String` stored in `string1`). We chose to have `string2` be a literal since
the reader might have code with both `String`s and string literals, and the way
most readers first get into problems with lifetimes is involving string slices,
so we wanted to demonstrate the flexibility of taking string slices as
arguments but the issues you might run into because string slices are
references.
All of the `String`/string slice/string literal concepts here are covered
thoroughly in Chapter 4, which is why we put two back references here (above
and below). If these topics are confusing you in this context, I'd be
interested to know if rereading Chapter 4 clears up that confusion.
/Carol -->

Refer back to the “String Slices as Parameters” section of Chapter 4 for more
discussion about why these are the arguments we want.

Recorra à seção do Capítulo 4 "Cortes de Strings como Parâmetros" para mais 
discussões sobre porque esses são os argumentos que queremos.

If we try to implement the `longest` function as shown in Listing 10-22, it
won’t compile:

Se tentarmos implementar a função `maior` como mostrado na Listagem 10-22 ela
não vai compilar:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

```rust,ignore
fn maior(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

<span class="caption">Listing 10-22: An implementation of the `longest`
function that returns the longest of two string slices, but does not yet
compile</span>

<span class="caption">Listagem 10-22: Uma implementação da função `maior` que
retorna o mais longo de dois cortes de string, mas ele não compila ainda</span>

Instead we get the following error that talks about lifetimes:

Ao invés disso recebemos o seguinte erro que fala sobre tempos de vida:

```text
error[E0106]: missing lifetime specifier
   |
1  | fn longest(x: &str, y: &str) -> &str {
   |                                 ^ expected lifetime parameter
   |
   = help: this function's return type contains a borrowed value, but the
   signature does not say whether it is borrowed from `x` or `y`
```

```text
error[E0106]: missing lifetime specifier
   |
1  | fn maior(x: &str, y: &str) -> &str {
   |                                 ^ expected lifetime parameter
   |
   = help: this function's return type contains a borrowed value, but the
   signature does not say whether it is borrowed from `x` or `y`
```

The help text is telling us that the return type needs a generic lifetime
parameter on it because Rust can’t tell if the reference being returned refers
to `x` or `y`. Actually, we don’t know either, since the `if` block in the body
of this function returns a reference to `x` and the `else` block returns a
reference to `y`!

O texto de ajuda está nos dizendo que o tipo de retorno precisa de um parâmetro
de tempo de vida genérico nele porque o Rust não pode dizer se a referência que
está sendo retornada se refere a `x` ou `y`. Atualmente, nós também não 
sabemos, já que o bloco `if` no corpo dessa função retorna uma referência para 
`x` e o bloco `else` retorna uma referência para `y`!

As we’re defining this function, we don’t know the concrete values that will be
passed into this function, so we don’t know whether the `if` case or the `else`
case will execute. We also don’t know the concrete lifetimes of the references
that will be passed in, so we can’t look at the scopes like we did in Listings
10-19 and 10-20 in order to determine that the reference we return will always
be valid. The borrow checker can’t determine this either, because it doesn’t
know how the lifetimes of `x` and `y` relate to the lifetime of the return
value. We’re going to add generic lifetime parameters that will define the
relationship between the references so that the borrow checker can perform its
analysis.

Enquanto estamos definindo essa função, não sabemos os valores concretos que 
serão passados para essa função, então não sabemos se o caso `if` ou o caso
`else` será executado. Nós também não sabemos os tempos de vida concretos das
referências que serão passadas, então não podemos olhar para esses escopos como
fizemos nas Listagem 10-19 e 10-20 afim de determinar que a referência que
retornaremos sempre será válida. O verificador de empréstimos não consegue 
determinar isso também porque não sabe como os tempos de vida de `x` e `y` se
relacionam com o tempo de vida do valor de retorno. Nós vamos adicionar 
parâmetros genéricos de tempo de vida que definirão a relação entre as 
referências para que o verificador de empréstimos possa fazer sua análise.

### Lifetime Annotation Syntax

### Sintaxe de Anotação de Tempo de Vida

Lifetime annotations don’t change how long any of the references involved live.
In the same way that functions can accept any type when the signature specifies
a generic type parameter, functions can accept references with any lifetime
when the signature specifies a generic lifetime parameter. What lifetime
annotations do is relate the lifetimes of multiple references to each other.

Anotações de tempo de vida não mudam quanto tempo qualquer uma das referências
envolvidas viverão. Do mesmo modo que funções podem aceitar qualquer tipo de 
assinatura que especifica um parâmetro de tipo genérico, funções podem aceitar
referências com qualquer tempo de vida quando a assinatura especificar um 
parâmetro genérico de tempo de vida. O que anotações de tempo de vida fazem é
relacionar os tempos de vida de múltiplas referências uns com os outros.

Lifetime annotations have a slightly unusual syntax: the names of lifetime
parameters must start with an apostrophe `'`. The names of lifetime parameters
are usually all lowercase, and like generic types, their names are usually very
short. `'a` is the name most people use as a default. Lifetime parameter
annotations go after the `&` of a reference, and a space separates the lifetime
annotation from the reference’s type.

Anotações de tempo de vida tem uma sintaxe levemente incomum: os nomes dos
parâmetros de tempos de vida precisam começar com uma apóstrofe `'`. Os nomes 
dos parâmetros dos tempos de vida são usualmente todos em caixa baixa, e como 
tipos genéricos, seu nome usualmente são bem curtos. `'a` é o nome que a maior
parte das pessoas usam por padrão. Parâmetros de anotações de tempos de vida 
vão depois do `&` de uma referência, e um espaço separa a anotação de tempo de 
vida do tipo da referência.

Here’s some examples: we’ve got a reference to an `i32` without a lifetime
parameter, a reference to an `i32` that has a lifetime parameter named `'a`,
and a mutable reference to an `i32` that also has the lifetime `'a`:

Aqui vão alguns exemplos: nós temos uma referência para um `i32` sem um 
parâmetro tempo de vida, uma referência para um `i32` que tem um parâmetro de
tempo de vida chamado `'a`:

```rust,ignore
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```


```rust,ignore
&i32        // uma referência
&'a i32     // uma referência com um tempo de vida explícito
&'a mut i32 // uma referência mutável com um tempo de vida explícito
```

One lifetime annotation by itself doesn’t have much meaning: lifetime
annotations tell Rust how the generic lifetime parameters of multiple
references relate to each other. If we have a function with the parameter
`first` that is a reference to an `i32` that has the lifetime `'a`, and the
function has another parameter named `second` that is another reference to an
`i32` that also has the lifetime `'a`, these two lifetime annotations that have
the same name indicate that the references `first` and `second` must both live
as long as the same generic lifetime.

Uma anotação de tempo de vida por si só não tem muito significado: anotações de
tempos de vida dizem ao Rust como os parâmetros genéricos de tempos de vida de
múltiplas referências se relacionam umas com as outras. Se tivermos uma função
com o parâmetro `primeiro` que é uma referência para um `i32` que tem um tempo
de vida de `'a`, e a função tem outro parâmetro chamado `segundo` que é outra
referência para um `i32` que também possui um tempo de vida `'a`, essas duas
anotações de tempo de vida com o mesmo nome indicam que as referências 
`primeiro` e `segundo` precisam ambas viver tanto quanto o mesmo tempo de vida
genérico.

### Lifetime Annotations in Function Signatures

### Anotações de Tempo de Vida em Assinaturas de Funções

Let’s look at lifetime annotations in the context of the `longest` function
we’re working on. Just like generic type parameters, generic lifetime
parameters need to be declared within angle brackets between the function name
and the parameter list. The constraint we want to tell Rust about for the
references in the parameters and the return value is that they all must have
the same lifetime, which we’ll name `'a` and add to each reference as shown in
Listing 10-23:

Vamos olhar para anotações de tempo de vida no contexto da função `maior` que
estamos trabalhando. Assim como parâmetros de tipos genéricos, parâmetros de 
tempos de vida genéricos precisam ser declarados dentro de colchetes angulares
entre o nome da função e a lista de parâmetros. A limitanção que queremos 
dar ao Rust é que para as referências nos parâmetros e o valor de retorno devem
ter o mesmo tempo de vida, o qual nomearemos `'a` e adicionaremos para cada uma
das referências como mostrado na Listagem 10-23:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do Arquivo: src/main.rs</span>

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

```rust
fn maior<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

<span class="caption">Listing 10-23: The `longest` function definition that
specifies all the references in the signature must have the same lifetime,
`'a`</span>

<span class="caption">Listagem 10-23: A definição da função `maior` especifica
todas as referências na assinatura como tendo o mesmo tempo de vida, `'a`</span>

This will compile and will produce the result we want when used with the `main`
function in Listing 10-21.

Isso compilará e produzirá o resultado que queremos quando usada com a função
`main` na Listagem 10-21.

The function signature now says that for some lifetime `'a`, the function will
get two parameters, both of which are string slices that live at least as long
as the lifetime `'a`. The function will return a string slice that also will
last at least as long as the lifetime `'a`. This is the contract we are telling
Rust we want it to enforce.

A assinatura de função agora diz que pra algum tempo de vida `'a`, a função
receberá dois parâmetros, ambos serão cortes de string que vivem pelo menos
tanto quanto o tempo de vida `'a`. A função retornará um corte de string que 
também vai durar tanto quanto o tempo de vida `'a`. Esse é o contrato que 
estamos dizendo ao Rust que queremos garantir.

By specifying the lifetime parameters in this function signature, we are not
changing the lifetimes of any values passed in or returned, but we are saying
that any values that do not adhere to this contract should be rejected by the
borrow checker. This function does not know (or need to know) exactly how long
`x` and `y` will live, but only needs to know that there is some scope that
can be substituted for `'a` that will satisfy this signature.

Especificando os parâmetros de tempo de vida nessa assinatura de função, não
estamos modificando os tempos de vida de quaisquer valores passados ou 
retornados, mas estamos dizendo que quaisqueres valores que não concordem com
esse contrato devem ser rejeitados pelo verificador de empréstimos. Essa função
não sabe (ou não precisa saber) exatamente quanto tempo `x` e `y` vão viver,
apenas precisa saber que existe algum escopo que pode ser substituído por `'a`
que irá satisfazer essa assinatura.

When annotating lifetimes in functions, the annotations go on the function
signature, and not in any of the code in the function body. This is because
Rust is able to analyze the code within the function without any help, but when
a function has references to or from code outside that function, the lifetimes
of the arguments or return values will potentially be different each time the
function is called. This would be incredibly costly and often impossible for
Rust to figure out. In this case, we need to annotate the lifetimes ourselves.

Quando estiver anotando tempos de vidas em funções, as anotações vão na 
assinatura da função, e não no código no corpo da função. Isso acontece porque
o Rust consegue analisar o código dentro da função sem nenhuma ajuda, mas 
quando uma função tem referências para ou de códigos de fora daquela função,
os tempos de vida dos argumentos ou os valores de retorno poderão ser 
diferentes cada vez que a função é chamada. Isso seria incrivelmente custoso e
frequentemente impossível para o Rust descobrir. Nesse caso, precisamos anotar
os tempos de vida nós mesmos.

When concrete references are passed to `longest`, the concrete lifetime that
gets substituted for `'a` is the part of the scope of `x` that overlaps with
the scope of `y`. Since scopes always nest, another way to say this is that the
generic lifetime `'a` will get the concrete lifetime equal to the smaller of
the lifetimes of `x` and `y`. Because we’ve annotated the returned reference
with the same lifetime parameter `'a`, the returned reference will therefore be
guaranteed to be valid as long as the shorter of the lifetimes of `x` and `y`.

Quando referências concretas são passadas para `maior`, o tempo de vida 
concreto que é substituído por `'a` é a parte do escopo de `x` que sobrepõe o
escopo de `y`. Já que escopos sempre se aninham, outra maneira de dizer isso é
que o tempo de vida genérico `'a` terá um tempo de vida concreto igual ao menor
dos tempos de vida de `x` e `y`. Porque nós anotamos a referência retornada com
o mesmo parâmetro `'a`, a referência retornada será portanto garantida de ser
válida tanto quanto for o tempo de vida mais curto de `x` e `y`.

Let’s see how this restricts the usage of the `longest` function by passing in
references that have different concrete lifetimes. Listing 10-24 is a
straightforward example that should match your intuition from any language:
`string1` is valid until the end of the outer scope, `string2` is valid until
the end of the inner scope, and `result` references something that is valid
until the end of the inner scope. The borrow checker approves of this code; it
will compile and print `The longest string is long string is long` when run:

Vamos ver como isso restringe o uso da função `maior` passando referências que
tem diferentes tempos de vida concretos. A Listagem 10-25 é um exemplo direto
que deve corresponder suas intuições de qualquer linguage: `string1` é válida
até o final do escopo exterior, `strin2` é válida até o final do escopo, a
`string2` é válida até o final do escopo interior. Com o verificador de 
empréstimos aprovando esse código; ele vai compilar e imprimir 
`A string mais longa é longa a string longa é longa`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
# fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
#     if x.len() > y.len() {
#         x
#     } else {
#         y
#     }
# }
#
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}
```

```rust
# fn maior<'a>(x: &'a str, y: &'a str) -> &'a str {
#     if x.len() > y.len() {
#         x
#     } else {
#         y
#     }
# }
#
fn main() {
    let string1 = String::from("a string longa é longa");

    {
        let string2 = String::from("xyz");
        let resultado = maior(string1.as_str(), string2.as_str());
        println!("A string mais longa é longa {}", resultado);
    }
}
```

<span class="caption">Listing 10-24: Using the `longest` function with
references to `String` values that have different concrete lifetimes</span>

<span class="caption">Listagem 10-24: Usando a função `maior` com referências
para valores de `String` que tem tempos de vida concretos diferentes</span>

Next, let’s try an example that will show that the lifetime of the reference in
`result` must be the smaller lifetime of the two arguments. We’ll move the
declaration of the `result` variable outside the inner scope, but leave the
assignment of the value to the `result` variable inside the scope with
`string2`. Next, we’ll move the `println!` that uses `result` outside of the
inner scope, after it has ended. The code in Listing 10-25 will not compile:

Em seguida, vamos tentar um exemplo que vai mostrar que o tempo de vida da 
referência em `resultado` precisa ser o menor dos tempos de vida dos dois 
argumentos. Nós vamos mover a declaração da variável `resultado` para fora do
escopo interior, mas deixar a assinatura do valor para a variável `resultado`
dentro do escopo com `string2`. Em seguida, vamos mover o `println!` que usa o
`resultado` fora do escopo interior, depois que ele terminou. O código na 
Listagem 10-25 não compilará:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let string1 = String::from("long string is long");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}
```

```rust,ignore
fn main() {
    let string1 = String::from("a string longa é longa");
    let resultado;
    {
        let string2 = String::from("xyz");
        resultado = longest(string1.as_str(), string2.as_str());
    }
    println!("A string mais longa é {}", resultado);
}
```

<span class="caption">Listing 10-25: Attempting to use `result` after `string2`
has gone out of scope won’t compile</span>

<span class="caption">Listagem 10-25: A tentativa de usar `resultado` depois 
que `string2` saiu de escopo não compilará</span>

If we try to compile this, we’ll get this error:

Se tentarmos compilar isso, receberemos esse erro:

```text
error: `string2` does not live long enough
   |
6  |         result = longest(string1.as_str(), string2.as_str());
   |                                            ------- borrow occurs here
7  |     }
   |     ^ `string2` dropped here while still borrowed
8  |     println!("The longest string is {}", result);
9  | }
   | - borrowed value needs to live until here
```

```text
error: `string2` does not live long enough
   |
6  |         resultadod = longest(string1.as_str(), string2.as_str());
   |                                            ------- borrow occurs here
7  |     }
   |     ^ `string2` dropped here while still borrowed
8  |     println!("The longest string is {}", result);
9  | }
   | - borrowed value needs to live until here
```

The error is saying that in order for `result` to be valid for the `println!`,
`string2` would need to be valid until the end of the outer scope. Rust knows
this because we annotated the lifetimes of the function parameters and return
values with the same lifetime parameter, `'a`.

O erro está dizendo que para `resultado` ser válido para `println!`, a 
`string2` teria que ser válida até o final do escopo exterior. Rust sabe disso
porque nós anotamos os tempos de vida dos parâmetros da função e retornamos
valores com o mesmo parâmetro do tempo de vida, `'a`.

We can look at this code as humans and see that `string1` is longer, and
therefore `result` will contain a reference to `string1`. Because `string1` has
not gone out of scope yet, a reference to `string1` will still be valid for the
`println!`. However, what we’ve told Rust with the lifetime parameters is that
the lifetime of the reference returned by the `longest` function is the same as
the smaller of the lifetimes of the references passed in. Therefore, the borrow
checker disallows the code in Listing 10-25 as possibly having an invalid
reference.

Nós podemos olhar para esse código como humanos e ver que a `string1` é mais
longa, e portanto `resultado` conterá a referência para a `string1`. Porque a
`string1` não saiu de escopo ainda, a referência para `string1` ainda será 
válida para o `println!`. No entanto, o que dissemos ao Rust com os parâmetros
de tempo de vida é que o tempo de vida da referência retornado pela função 
`maior` é o mesmo que o menor dos tempos de vida das referências passadas. 
Portanto, o verificador de empréstimos não permite o código da Listagem 10-25
como possível já que tem um referência inválida.

Try designing some more experiments that vary the values and lifetimes of the
references passed in to the `longest` function and how the returned reference
is used. Make hypotheses about whether your experiments will pass the borrow
checker or not before you compile, then check to see if you’re right!

Tente fazer mais alguns experimentos que variam os valores e os tempos de vidas
das referências passadas para a função `maior` e como a referência retornada é
usada. Crie hipóteses sobre seus experimentos se eles vão passar pelo 
verificador de empréstimos ou não antes de você compilar, e então cheque para
ver se você está certo!

### Thinking in Terms of Lifetimes

### Pensando em Termos de Tempos de Vida

The exact way to specify lifetime parameters depends on what your function is
doing. For example, if we changed the implementation of the `longest` function
to always return the first argument rather than the longest string slice, we
wouldn’t need to specify a lifetime on the `y` parameter. This code compiles:

O modo exato de especificar parâmetros de tempos de vida depende do que sua 
função está fazendo. Por exemplo, se mudaramos a implementação da função 
`maior` para sempre retornar o primeiro argumento ao invés do corte de string
mais longo, não precisaríamos especificar um tempo de vida no parâmetro `y`.
Este código compila:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

```rust
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

In this example, we’ve specified a lifetime parameter `'a` for the parameter
`x` and the return type, but not for the parameter `y`, since the lifetime of
`y` does not have any relationship with the lifetime of `x` or the return value.

Nesse exemplo, especificamos o tempo de vida do parâmetro `'a` para o parâmetro
`x` e o tipo de retorno, mas, não para o parâmetro `y`, já que o tempo de vida 
de `y` não tem qualquer relação com o tempo de vida `x` ou o valor retornado.

When returning a reference from a function, the lifetime parameter for the
return type needs to match the lifetime parameter of one of the arguments. If
the reference returned does *not* refer to one of the arguments, the only other
possibility is that it refers to a value created within this function, which
would be a dangling reference since the value will go out of scope at the end
of the function. Consider this attempted implementation of the `longest`
function that won’t compile:

Quando retornarmos uma referência de um uma função, o parâmetro de tempo de 
vida para o tipo de retorno precisa combinar o parâmetro do tempo de vida de um
dos argumentos. Se a referência retornada *não* refere a nenhum dos argumentos,
a única outra possibilidade é que refira a um valor criado dentro da função, o
que seria uma referência solta já que o valor sairá de escopo no fim da função.
Considere essa tentativa da função `maior` que não compilará:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust,ignore
fn longest<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str()
}
```

```rust,ignore
fn maior<'a>(x: &str, y: &str) -> &'a str {
    let resultado = String::from("string muito longa");
    resultado.as_str()
}
```

Even though we’ve specified a lifetime parameter `'a` for the return type, this
implementation fails to compile because the return value lifetime is not
related to the lifetime of the parameters at all. Here’s the error message we
get:

Mesmo especificando um parâmetro de tempo de vida `'a` para o tipo de retorno,
essa implementação falha em compilar porque o valor de retorno do tempo de vida
não é relacionado com o tempo de vida dos parâmetros de forma alguma. Esta é a
mensagem de erro que recebemos:

```text
error: `result` does not live long enough
  |
3 |     result.as_str()
  |     ^^^^^^ does not live long enough
4 | }
  | - borrowed value only lives until here
  |
note: borrowed value must be valid for the lifetime 'a as defined on the block
at 1:44...
  |
1 | fn longest<'a>(x: &str, y: &str) -> &'a str {
  |                                             ^
```

```text
error: `resultado` does not live long enough
  |
3 |     resultado.as_str()
  |     ^^^^^^ does not live long enough
4 | }
  | - borrowed value only lives until here
  |
note: borrowed value must be valid for the lifetime 'a as defined on the block
at 1:44...
  |
1 | fn maior<'a>(x: &str, y: &str) -> &'a str {
  |                                             ^
```

The problem is that `result` will go out of scope and get cleaned up at the end
of the `longest` function, and we’re trying to return a reference to `result`
from the function. There’s no way we can specify lifetime parameters that would
change the dangling reference, and Rust won’t let us create a dangling
reference. In this case, the best fix would be to return an owned data type
rather than a reference so that the calling function is then responsible for
cleaning up the value.

O problema é que `resultado` sairá de escopo e será limpo no final da função 
`maior`, e estamos tentando retornar uma referência para `resultado` da função.
Não há nenhum modo que possamos especificar parâmetros de tempo de vida que
mudariam uma referência solta, e o Rust não nos deixará criar uma referência 
solta. Nesse caso, a melhor solução seria retornar um tipo de dado com posse
ao invés de uma referência de modo que a função chamadora é então responsável
por limpar o valor.

Ultimately, lifetime syntax is about connecting the lifetimes of various
arguments and return values of functions. Once they’re connected, Rust has
enough information to allow memory-safe operations and disallow operations that
would create dangling pointers or otherwise violate memory safety.

Em última análise, a sintaxe de tempo de vida é sobre conectar tempos de vida
de vários argumentos e retornar valores de funções. Uma vez que estão 
conectados, o Rust tem informação o suficiente para permitir operações seguras
de memória e não permitir operações que criariam ponteiros soltos ou outro tipo
de violação à segurança da memória.

### Lifetime Annotations in Struct Definitions

### Anotações de Tempo de Vida em Definições de Struct

Up until now, we’ve only defined structs to hold owned types. It is possible
for structs to hold references, but we need to add a lifetime annotation on
every reference in the struct’s definition. Listing 10-26 has a struct named
`ImportantExcerpt` that holds a string slice:

Até agora, nós só definimos structs para conter tipos com posse. É possível 
para structs manter referências, mas precisamos adicionar anotações de tempo de
vida em todas as referências na definição do struct. A Listagem 10-26 tem a 
struct chamada `ExcertoImportante` que contém um corte de string:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.')
        .next()
        .expect("Could not find a '.'");
    let i = ImportantExcerpt { part: first_sentence };
}
```

```rust
struct ExcertoImportante<'a> {
    parte: &'a str,
}

fn main() {
    let romance = String::from("Chame-me Ishmael. Há alguns anos...");
    let primeira_sentenca = romance.split('.')
        .next()
        .expect("Não pôde achar um '.'");
    let i = ImportantExcerpt { parte: primeira_sentenca };
}
```

<span class="caption">Listing 10-26: A struct that holds a reference, so its
definition needs a lifetime annotation</span>

<span class="caption">Listagem 10-26: Um struct que contém uma referência,
então sua definição precisa de uma anotação de tempo de vida</span>

This struct has one field, `part`, that holds a string slice, which is a
reference. Just like with generic data types, we have to declare the name of
the generic lifetime parameter inside angle brackets after the name of the
struct so that we can use the lifetime parameter in the body of the struct
definition.

Esse struct tem um campo, `parte`, que contém um corte de string, que é uma
referência. Assim como tipos genéricos de dados, temos que declarar o nome do
parâmetro genérico de tempo de vida dentro de colchetes angulares depois do
nome do struct para que possamos usar o parâmetro de tempo de vida no corpo da
definição do struct.

The `main` function here creates an instance of the `ImportantExcerpt` struct
that holds a reference to the first sentence of the `String` owned by the
variable `novel`.

A função `main` cria uma instância da struct `ExcertoImportante` que contém uma
referência pra a primeira sentença da `String` com posse da variável `romance`.

### Lifetime Elision

### Elisão de Tempo de Vida 

In this section, we’ve learned that every reference has a lifetime, and we need
to specify lifetime parameters for functions or structs that use references.
However, in Chapter 4 we had a function in the “String Slices” section, shown
again in Listing 10-27, that compiled without lifetime annotations:

Nessa seção, nós aprendemos que toda referência tem um tempo de vida, e nós
precisamos especificar os parâmetros dos tempos de vida para funções ou 
estruturas que usam referências. No entanto, no Capítulo 4 nós tínhamos a 
função na seção "Cortes de Strings", mostradas novamente na Listagem 10-27, que
compilam sem anotações de tempo de vida:

<span class="filename">Filename: src/lib.rs</span>

<span class="filename">Nome do arquivo: src/lib.rs</span>

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

```rust
fn primeira_palavra(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

<span class="caption">Listing 10-27: A function we defined in Chapter 4 that
compiled without lifetime annotations, even though the parameter and return
type are references</span>

<span class="caption">Listagem 10-27: Uma função definida no Capítulo 4 que
compila sem anotações de tempo de vida, mesmo o parâmetro e o tipo de retorno
sendo referências</span>

The reason this function compiles without lifetime annotations is historical:
in early versions of pre-1.0 Rust, this indeed wouldn’t have compiled. Every
reference needed an explicit lifetime. At that time, the function signature
would have been written like this:

A razão pela qual essa função compila sem anotações de tempo de vida é 
histórica: em versões mais antigas de pre Rust-1.0, isso não teria compilado.
Toda referência precisava de um tempo de vida explícito. Naquele tempo, a 
assinatura da função teria sido escrita da seguinte forma:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &'a str {
```

```rust,ignore
fn primeira_palavra<'a>(s: &'a str) -> &'a str {
```

After writing a lot of Rust code, the Rust team found that Rust programmers
were typing the same lifetime annotations over and over in particular
situations. These situations were predictable and followed a few deterministic
patterns. The Rust team then programmed these patterns into the Rust compiler’s
code so that the borrow checker can infer the lifetimes in these situations
without forcing the programmer to explicitly add the annotations.

Depois de escrever muito código em Rust, o time de Rust descobriu que os 
programadores de Rust estavam digitando as mesmas anotações de tempo de vida
de novo e de novo. Essas situações eram previsíveis e seguiam alguns padrões
determinísticos. O time de Rust programou esses padrões no compilador de código 
de Rust para que o verificador de empréstimos pode inferir os tempos de vida
dessas situações sem forçar o programador adicionar essas anotações 
explicitamente.

We mention this piece of Rust history because it’s entirely possible that more
deterministic patterns will emerge and be added to the compiler. In the future,
even fewer lifetime annotations might be required.

Nós mencionamos essa parte da história de Rust porque é inteiramente possível
que mais padrões determinísticos surgirão e serão adicionado ao compilador. No
futuro, até menos anotações de tempo de vida serão necessárias.

The patterns programmed into Rust’s analysis of references are called the
*lifetime elision rules*. These aren’t rules for programmers to follow; the
rules are a set of particular cases that the compiler will consider, and if
your code fits these cases, you don’t need to write the lifetimes explicitly.

Os padrões programados nas análises de referência de Rust são chamados de 
*regras de elisão de tempo de vida*. Essas não são regras para o programador
seguir; as regras são um conjunto de casos particular que o compilador irá
considerar, e se seu código se encaixa nesses casos, você não precisa escrever
os tempos de vida explicitamente.

The elision rules don’t provide full inference: if Rust deterministically
applies the rules but there’s still ambiguity as to what lifetimes the
references have, it won’t guess what the lifetime of the remaining references
should be. In this case, the compiler will give you an error that can be
resolved by adding the lifetime annotations that correspond to your intentions
for how the references relate to each other.

As regras de elisão não fornecem total inferência: se o Rust de forma 
determinista aplicar as regras mas ainda houver ambiguidade como quais tempos 
de vida as referências restantes deveriam ter. Nesse caso, o compilador dará um
erro que pode ser solucionado adicionando anotações de tempo de vida que 
correspondem com as suas intenções para como as referências se relacionam umas
com as outras.

First, some definitions: Lifetimes on function or method parameters are called
*input lifetimes*, and lifetimes on return values are called *output lifetimes*.

Primeiro, algumas definições: Tempos de vida em parâmetros de funções ou 
métodos são chamadas *tempos de vida de entrada*, e tempos de vida em valores
de retorno são chamados de *tempos de vida de saída*.

Now, on to the rules that the compiler uses to figure out what lifetimes
references have when there aren’t explicit annotations. The first rule applies
to input lifetimes, and the second two rules apply to output lifetimes. If the
compiler gets to the end of the three rules and there are still references that
it can’t figure out lifetimes for, the compiler will stop with an error.

Agora, as regras que o compilador usa para descobrir quais referências de 
tempos de vidas têm quando não há anotações explícitas. A primeira regra se 
aplica a tempos de vida de entrada, e a segunda regra se aplica a tempos de 
vida de saída. Se o compilador chega no fim das três regras e ainda há 
referências que ele não consegue descobrir tempos de vida, o compilador irá
parar com um erro.

1. Each parameter that is a reference gets its own lifetime parameter. In other
   words, a function with one parameter gets one lifetime parameter: `fn
   foo<'a>(x: &'a i32)`, a function with two arguments gets two separate
   lifetime parameters: `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`, and so on.

1. Cada parâmetro que é uma referência tem seu próprio parâmetro de tempo de
  vida. Em outras palavras, uma função com um parâmetro tem um parâmetro de 
  tempo de vida: `fn foo<'a>(x: &'a i32)`, uma função com dois argumentos 
  recebe dois parâmetros de tempo de vida separados: 
  `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`, e assim por diante.

2. If there is exactly one input lifetime parameter, that lifetime is assigned
   to all output lifetime parameters: `fn foo<'a>(x: &'a i32) -> &'a i32`.

2. Se há exatamente uma entrada de parâmetro de tempo de vida, aquele tempo de
  vida é atribuído para todos os parâmetros de saída do tempo de vida:
  `fn foo<'a>(x: &'a i32) -> &'a i32`.

3. If there are multiple input lifetime parameters, but one of them is `&self`
   or `&mut self` because this is a method, then the lifetime of `self` is
   assigned to all output lifetime parameters. This makes writing methods much
   nicer.

3. Se há múltiplas entradas de parâmetros de tempo de vida, mas uma delas é
  `&self` ou `&mut self` porque é um método, então o tempo de vida de `self` é
  atribuído para todos os parâmetro de tempo de vida de saída. Isso faz métodos
  de escrita bem melhores.

Let’s pretend we’re the compiler and apply these rules to figure out what the
lifetimes of the references in the signature of the `first_word` function in
Listing 10-27 are. The signature starts without any lifetimes associated with
the references:

Vamos fingir que somos o compilador e aplicamos essas regras para descobrir 
quais os tempos de vida das referências na assinatura da função 
`primeira_palavra` na Listagem 10-27. A Assinatura começa sem nenhum tempo de 
vida associado com as referências:

```rust,ignore
fn first_word(s: &str) -> &str {
```

```rust,ignore
fn primeira_palavra(s: &str) -> &str {
```

Then we (as the compiler) apply the first rule, which says each parameter gets
its own lifetime. We’re going to call it `'a` as usual, so now the signature is:

Então nós (como o compilador) aplicamos a primeira regra, que diz que cada 
parâmetro tem sem próprio tempo de vida. Nós vamos chama-lo de `'a` como usual,
então agora a assinatura é:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &str {
```

```rust,ignore
fn primeira_palavra<'a>(s: &'a str) -> &str {
```

On to the second rule, which applies because there is exactly one input
lifetime. The second rule says the lifetime of the one input parameter gets
assigned to the output lifetime, so now the signature is:

À segunda regra, que se aplica porque existe apenas um tempo de vida. A
segunda regra diz que o tempo de vida de um parâmetro de entrada é atribuído
a um tempo de vida de saída, então agora a assinatura é:

```rust,ignore
fn first_word<'a>(s: &'a str) -> &'a str {
```

```rust,ignore
fn primeira_palavra<'a>(s: &'a str) -> &'a str {
```

Now all the references in this function signature have lifetimes, and the
compiler can continue its analysis without needing the programmer to annotate
the lifetimes in this function signature.

Agora todas as referências nessa assinatura de função possuem tempos de vida, e
o compilador pode continuar sua análise sem precisar que o programador anote os
tempos de vida na assinatura dessa função.

Let’s do another example, this time with the `longest` function that had no
lifetime parameters when we started working with in Listing 10-22:

Vamos fazer outro exemplo, dessa vez com a função `maior` que não tinha 
parâmetros de tempo de vida quando começamos a trabalhar com ela na Listagem 
10-22:

```rust,ignore
fn longest(x: &str, y: &str) -> &str {
```

```rust,ignore
fn maior(x: &str, y: &str) -> &str {
```

Pretending we’re the compiler again, let’s apply the first rule: each parameter
gets its own lifetime. This time we have two parameters, so we have two
lifetimes:

Fingindo que somos o compilador novamente, vamos aplicar a primeira regra: cada
parâmetro tem seu próprio tempo de vida. Dessa vez temos dois parâmetros, então
temos dois tempos de vida:

```rust,ignore
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str {
```

```rust,ignore
fn maior<'a, 'b>(x: &'a str, y: &'b str) -> &str {
```

Looking at the second rule, it doesn’t apply since there is more than one input
lifetime. Looking at the third rule, this also does not apply because this is a
function rather than a method, so none of the parameters are `self`. So we’re
out of rules, but we haven’t figured out what the return type’s lifetime is.
This is why we got an error trying to compile the code from Listing 10-22: the
compiler worked through the lifetime elision rules it knows, but still can’t
figure out all the lifetimes of the references in the signature.

Olhando para a segunda regra, ela não se aplica já que há mais de uma entrada 
de tempo de vida. Olhando para a terceira regra, ela também não se aplica 
porque isso é uma função e não um método, então nenhum dos parâmetros são 
`self`. Então, acabaram as regras, mas não descobrimos qual é o tipo de 
retorno do tempo de vida. É por isso que recebemos um erro quando tentamos
compilar o código da Listagem 10-22: o compilador usou as regras de elisão de
tempo de vida que sabia, mas ainda sim não conseguiu descobrir todos os tempos
de vida das referências na assinatura.

Because the third rule only really applies in method signatures, let’s look at
lifetimes in that context now, and see why the third rule means we don’t have
to annotate lifetimes in method signatures very often.

Porque a terceira regra só se aplica em assinaturas de métodos, vamos olhar
tempos de vida nesse contexto agora, e ver porque a terceira regra significa 
que não temos que anotar tempos de vida em assinaturas de métodos muito 
frequentemente.

### Lifetime Annotations in Method Definitions

### Anotações de Tempo de Vida em Definições de Métodos

<!-- Is this different to the reference lifetime annotations, or just a
finalized explanation? -->
<!-- This is about lifetimes on references in method signatures, which is where
the 3rd lifetime elision rule kicks in. It can also be confusing where lifetime
parameters need to be declared and used since the lifetime parameters could go
with the struct's fields or with references passed into or returned from
methods. /Carol -->

When we implement methods on a struct with lifetimes, the syntax is again the
same as that of generic type parameters that we showed in Listing 10-11: the
place that lifetime parameters are declared and used depends on whether the
lifetime parameter is related to the struct fields or the method arguments and
return values.

Quando implementamos métodos em uma struct com tempos de vida, a sintaxe é
novamente a mesma da de parâmetros de tipos genéricos que mostramos na Listagem
10-11: o lugar que parâmetros de tempos de vida são declarados e usados depende
se o parâmetro de tempo de vida é relacionado aos campos do struct ou aos
argumentos dos métodos e dos valores de retorno.

Lifetime names for struct fields always need to be declared after the `impl`
keyword and then used after the struct’s name, since those lifetimes are part
of the struct’s type.

Nomes de tempos de vida para campos de estruturas sempre precisam ser 
declarados após a palavra-chave `impl` e então usadas após o nome da struct,
já que esses tempos de vida são partes do tipo da struct.

In method signatures inside the `impl` block, references might be tied to the
lifetime of references in the struct’s fields, or they might be independent. In
addition, the lifetime elision rules often make it so that lifetime annotations
aren’t necessary in method signatures. Let’s look at some examples using the
struct named `ImportantExcerpt` that we defined in Listing 10-26.

Em assinaturas de métodos dentro do bloco `impl`, as referências podem estar
amarradas às referências de tempo de vida nos campos de struct, ou elas podem
ser independentes. Além disso, as regras de elisão de tempo de vida 
constantemente fazem com que anotações não sejam necessárias em assinaturas de
métodos. Vamos ver alguns exemplos usando a struct chamada `ExcertoImportante`
que definimos na Listagem 10-26.

First, here’s a method named `level`. The only parameter is a reference to
`self`, and the return value is just an `i32`, not a reference to anything:

Primeiro, aqui há um método chamado `level`. O único parâmetro é uma referência
para `self`, e o valor de retorno é apenas um `i32`, não uma referência para
nada:

```rust
# struct ImportantExcerpt<'a> {
#     part: &'a str,
# }
#
impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
}
```

```rust
# struct ExcertoImportante<'a> {
#     part: &'a str,
# }
#
impl<'a> ExcertoImportante<'a> {
    fn level(&self) -> i32 {
        3
    }
}
```

The lifetime parameter declaration after `impl` and use after the type name is
required, but we’re not required to annotate the lifetime of the reference to
`self` because of the first elision rule.

A declaração do parâmetro de tempo de vida depois de `impl` e uso depois do 
tipo de nome é obrigatório, mas nós não necessariamente precisamos de anotar o
tempo de vida da referência `self` por causa da primeira regra da elisão.

Here’s an example where the third lifetime elision rule applies:

Aqui vai um exemplo onde a terceira regra da elisão de tempo de vida se aplica:

```rust
# struct ImportantExcerpt<'a> {
#     part: &'a str,
# }
#
impl<'a> ImportantExcerpt<'a> {
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {}", announcement);
        self.part
    }
}
```

```rust
# struct ExcertoImportante<'a> {
#     part: &'a str,
# }
#
impl<'a> ExcertoImportante<'a> {
    fn anuncio_e_parte_de_retorno(&self, anuncio: &str) -> &str {
        println!("Atenção por favor: {}", anuncio);
        self.part
    }
}
```

There are two input lifetimes, so Rust applies the first lifetime elision rule
and gives both `&self` and `announcement` their own lifetimes. Then, because
one of the parameters is `&self`, the return type gets the lifetime of `&self`,
and all lifetimes have been accounted for.

Há dois tempos de vida de entrada, então o Rust aplica a primeira regra de 
elisão de tempos de vida e dá ambos ao `&self` e ao `anuncio` seus próprios
tempos de vida. Então, porque um dos parâmetros é `self`, o tipo de retorno
tem o tempo de vida de `&self` e todos os tempos de vida foram contabilizados.

### The Static Lifetime

### O Tempo de Vida Estático

There is *one* special lifetime we need to discuss: `'static`. The `'static`
lifetime is the entire duration of the program. All string literals have the
`'static` lifetime, which we can choose to annotate as follows:

Há *um* tipo especial de tempo de vida que precisamos discutir: `'static`. O
tempo de vida `static` é a duração completa do programa. Todos os literais de
string têm um tempo de vida `static`, o qual podemos escolher anotar como o
seguinte:

```rust
let s: &'static str = "I have a static lifetime.";
```

```rust
let s: &'static str = "Eu tenho um tempo de vida estático.";
```

The text of this string is stored directly in the binary of your program and
the binary of your program is always available. Therefore, the lifetime of all
string literals is `'static`.

O texto dessa string é guardado diretamente no binário do seu programa e o
binário do seu programa está sempre disponível. Logo, o tempo de vida de todas
as literais de string é `'static`.

<!-- How would you add a static lifetime (below)? -->
<!-- Just like you'd specify any lifetime, see above where it shows `&'static str`. /Carol -->

You may see suggestions to use the `'static` lifetime in error message help
text, but before specifying `'static` as the lifetime for a reference, think
about whether the reference you have is one that actually lives the entire
lifetime of your program or not (or even if you want it to live that long, if
it could). Most of the time, the problem in the code is an attempt to create a
dangling reference or a mismatch of the available lifetimes, and the solution
is fixing those problems, not specifying the `'static` lifetime.

Você pode ver sugestões de usar o tempo de vida `'static` em uma mensagem de 
ajuda de erro, mas antes de especificar `'static` como o tempo de vida para uma
referência, pense sobre se a referência que você tem é uma que vive todo o 
tempo de vida do seu programa ou não (ou mesmo se você quer que ele viva tanto,
se poderia). Na maior parte do tempo, o probléma no código é uma tentativa de 
criar uma referência solta ou uma incompatibilidade dos tempos de vida 
disponíveis, e a solução é consertar esses problemas, não especificar um tempo
de vida `'static`.


### Generic Type Parameters, Trait Bounds, and Lifetimes Together

### Parâmetros de Tipos Genéricos, Limites de Trais e Tempos de Vida Juntos

Let’s briefly look at the syntax of specifying generic type parameters, trait
bounds, and lifetimes all in one function!

Vamos rapidamente olhar para a sintaxe de especificar parâmetros de tipos
genéricos, limites de trair e tempos de vida todos em uma função!

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(x: &'a str, y: &'a str, ann: T) -> &'a str
    where T: Display
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

```rust
use std::fmt::Display;

fn maior_com_um_anuncio<'a, T>(x: &'a str, y: &'a str, ann: T) -> &'a str
    where T: Display
{
    println!("Anúncio! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

This is the `longest` function from Listing 10-23 that returns the longest of
two string slices, but with an extra argument named `ann`. The type of `ann` is
the generic type `T`, which may be filled in by any type that implements the
`Display` trait as specified by the `where` clause. This extra argument will be
printed out before the function compares the lengths of the string slices,
which is why the `Display` trait bound is necessary. Because lifetimes are a
type of generic, the declarations of both the lifetime parameter `'a` and the
generic type parameter `T` go in the same list within the angle brackets after
the function name.

Essa é a função `maior` da Listagem 10-23 que retorna a maior de dois cortes de
string, mas com um argumento extra chamado `ann`. O tipo de `ann` é o tipo 
genérico `T`, que pode ser preenchido por qualquer tipo que implemente o trait
`Display` como está especificado na cláusula `where`. Esse argumento extra será
impresso antes da função comparar os comprimentos dos cortes de string, que é 
porque o trait de `Display` possui um limite. Porque tempos de vida são um tipo
genérico, a declaração de ambos os parâmetros de tempo de vida `'a` e o tipo
genérico `T` vão na mesma lista com chaves angulares depois do nome da função.

## Summary

## Sumário

We covered a lot in this chapter! Now that you know about generic type
parameters, traits and trait bounds, and generic lifetime parameters, you’re
ready to write code that isn’t duplicated but can be used in many different
situations. Generic type parameters mean the code can be applied to different
types. Traits and trait bounds ensure that even though the types are generic,
those types will have the behavior the code needs. Relationships between the
lifetimes of references specified by lifetime annotations ensure that this
flexible code won’t have any dangling references. And all of this happens at
compile time so that run-time performance isn’t affected!

Nós cobrimos várias coisas nesse capítulo! Agora que você sabe sobre parâmetros
de tipos genéricos, traits e limites de traits, e parâmetros genéricos de tempo
de vida, você está pronto para escrever código que não é duplicado mas pode ser
usado em muitas situações. Parâmetros de tipos genéricos significam que o 
código pode ser aplicado a diferentes tipos. Traits e limites de traits 
garantem que mesmo que os tipos sejam genéricos, esses tipos terão o 
comportamento que o código precisa. Relações entre tempos de vida de 
referências especificadas por anotações de tempo de vida garantem que esse 
código flexível não terá referências soltas. E tudo isso acontece em tempo de
compilação para que a performace em tempo de execução não seja afetada!

Believe it or not, there’s even more to learn in these areas: Chapter 17 will
discuss trait objects, which are another way to use traits. Chapter 19 will be
covering more complex scenarios involving lifetime annotations. Chapter 20 will
get to some advanced type system features. Up next, though, let’s talk about
how to write tests in Rust so that we can make sure our code using all these
features is working the way we want it to!

Acredite ou não, há ainda mais para aprender nessas áreas: Capítulo 17 
discutirá objetos de trait, que são outro modo de usar traits. O Capútulo 19
vai cobrir cenários mais complexos envolvendo anotações de tempo de vida. O
Capítulo 20 vai tratar de alguns tipos avançados de características do sistema.
Em seguida, porém, vamos falar sobre como escrever testes em Rust para que
possamos ter certeza que nosso código usando todas essas características está
funcionando do jeito que queremos!