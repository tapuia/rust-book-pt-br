# Generic Types, Traits, and Lifetimes

#Tipos Genéricos, _Traits_, e Tempos de vida (_Lifetimes_)

Every programming language has tools to deal effectively with duplication of
concepts; in Rust, one of those tools is *generics*. Generics are abstract
stand-ins for concrete types or other properties. When we’re writing and
compiling the code we can express properties of generics, such as their
behavior or how they relate to other generics, without needing to know what
will actually be in their place.

Cada linguagem de programação tem ferramentas para lidar de forma efetiva com a
duplicata de conceitos; em Rust, uma dessas ferramentas é a que cria tipos
genéricos. Tipos genéricos são _stand-ins_ abstratos para tipos concretos ou 
para outras propriedades. Quando estamos escrevendo e compilando o código 
podemos expressar propriedades de tipos genéricos, como seu comportamento ou 
como eles se relacionam com outros tipos genéricos, sem precisar saber o que 
realmente estará no lugar deles.

In the same way that a function takes parameters whose value we don’t know in
order to write code once that will be run on multiple concrete values, we can
write functions that take parameters of some generic type instead of a concrete
type like `i32` or `String`. We’ve already used generics in Chapter 6 with
`Option<T>`, Chapter 8 with `Vec<T>` and `HashMap<K, V>`, and Chapter 9 with
`Result<T, E>`. In this chapter, we’ll explore how to define our own types,
functions, and methods with generics!

Do mesmo modo que uma função aceita parâmetros cujos valores não sabemos
para escrever código que será processado em múltiplos valores concretos, nós
podemos escrever funções que recebem parâmetros de alguns tipos genéricos ao
invés de tipos concretos como `i32` ou `String`. Nós já usamos tipos genéricos
no Capítulo 6 com `Option<T>`, no Capítulo 8 com `Vec<T>` e `HashMap<K, V>`, e 
no Capítulo 9 com `Result<T, E>`. Nesse capítulo, vamos explorar como definir
nossos próprios tipos, funções e métodos usando tipos genéricos!

First, we’re going to review the mechanics of extracting a function that
reduces code duplication. Then we’ll use the same mechanics to make a generic
function out of two functions that only differ in the types of their
parameters. We’ll go over using generic types in struct and enum definitions
too.

Primeiro, nós vamos revisar as mecânicas de extrair uma função que reduz
duplicação de código. Então usaremos a mesma mecânica para fazer uma função
genérica usando duas funções que só diferem uma da outra nos tipos dos seus
parâmetros. Nós vamos usar tipos genéricos em definições de struct e enum
também.

After that, we’ll discuss *traits*, which are a way to define behavior in a
generic way. Traits can be combined with generic types in order to constrain a
generic type to those types that have a particular behavior, rather than any
type at all.

Depois disso, nós vamos discutir traits, que são um modo de definir
comportamento de uma forma genérica. Traits podem ser combinados com tipos
genéricos para restringir um tipo genérico aos tipos que tem um comportamento
particular ao invés de qualquer tipo.

Finally, we’ll discuss *lifetimes*, which are a kind of generic that let us
give the compiler information about how references are related to each other.
Lifetimes are the feature in Rust that allow us to borrow values in many
situations and still have the compiler check that references will be valid.

Finalmente, nós discutiremos *tempos de vida*, que são um tipo de generalismo 
que nos permite dar ao compilador informações sobre como as referências são
relacionadas umas com as outras. Tempos de vida são as características em Rust
que nos permitem pegar valores emprestados em muitas situações e ainda ter a 
aprovação do compilador de que as referências serão válidas. 

## Removing Duplication by Extracting a Function

## Removendo Duplicatas por meio da Extração de uma Função

Before getting into generics syntax, let’s first review a technique for dealing
with duplication that doesn’t use generic types: extracting a function. Once
that’s fresh in our minds, we’ll use the same mechanics with generics to
extract a generic function! In the same way that you recognize duplicated code
to extract into a function, you’ll start to recognize duplicated code that can
use generics.

Antes de entrar na sintaxe de tipos genéricos, vamos primeiro revisar uma
técnica para lidar com duplicatas que não usa tipos genéricos: extraindo uma
função. Uma vez que isso esteja fresco em nossas mentes, usaremos as mesmas
mecânicas com tipos genéricos para extrair uma função genérica! Do mesmo modo 
que você reconhece código duplicado para extrair para uma função, você começará
a reconhecer código duplicado que pode usar tipos genéricos.

Consider a small program that finds the largest number in a list, shown in
Listing 10-1:

Considere um pequeno programa que acha o maior número em uma lsita, mostrado
na Listagem 10-1:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("The largest number is {}", largest);
#  assert_eq!(largest, 100);
}
```

```rust
fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let mut maior = lista_numero[0];

    for numero in lista_numero {
        if numero > maior {
            maior = numero;
        }
    }

    println!("O maior número é {}", maior);
#  assert_eq!(maior, 100);
}
```

<span class="caption">Listing 10-1: Code to find the largest number in a list
of numbers</span>

<span class="caption">Listagem 10-1: Código para achar o maior número em uma
lista de números</span>

This code takes a list of integers, stored here in the variable `number_list`. It
puts the first item in the list in a variable named `largest`. Then it iterates
through all the numbers in the list, and if the current value is greater than
the number stored in `largest`, it replaces the value in `largest`. If the
current value is smaller than the largest value seen so far, `largest` is not
changed. When all the items in the list have been considered, `largest` will
hold the largest value, which in this case is 100.

Esse código recebe uma lista de inteiros, guardados aqui na variável 
`lista_numero`. Coloca o primeiro item da lista na variável chamada `maior`.
Então ele itera por todos os números da lista, e se o valor atual é maior que 
o número guardado em `maior`, substitui o valor em `maior`. Se o valor atual é
menor que o valor visto até então, `maior` não é mudado. Quando todos os items
da lista foram considerados, `maior` terá o maior valor, que nesse caso é 100.

If we needed to find the largest number in two different lists of numbers, we
could duplicate the code in Listing 10-1 and have the same logic exist in two
places in the program, as in Listing 10-2:

Se nós precisássemos encontrar o maior número em duas listas diferentes de
números, nós poderíamos duplicar o código da Listagem 10-1 e usar a mesma
lógica nas duas partes do programa, como na Listagem 10-2:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("The largest number is {}", largest);

    let number_list = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let mut largest = number_list[0];

    for number in number_list {
        if number > largest {
            largest = number;
        }
    }

    println!("The largest number is {}", largest);
}
```

```rust
fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let mut maior = lista_numero[0];

    for numero in lista_numero {
        if numero > maior {
            maior = numero;
        }
    }

    println!("O maior número é {}", maior);

    let lista_numero = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let mut maior = lista_numero[0];

    for numero in lista_numero {
        if numero > maior {
            maior = numero;
        }
    }

    println!("O maior número é {}", maior);
}
```

<span class="caption">Listing 10-2: Code to find the largest number in *two*
lists of numbers</span>

<span class="caption">Listagem 10-2: Código para encontrar o maior número em
duas listas de números</span>

While this code works, duplicating code is tedious and error-prone, and means
we have multiple places to update the logic if we need to change it.

Ao passo que esse código funciona, duplicar código é tedioso e tende a causar
erros, e significa que temos múltiplos lugares para atualizar a lógica se
precisarmos muda-lo.

<!-- Are we safe assuming the reader will be familiar with the term
"abstraction" in this context, or do we want to give a brief definition? -->
<!-- Yes, our audience will be familiar with this term. /Carol -->

To eliminate this duplication, we can create an abstraction, which in this case
will be in the form of a function that operates on any list of integers given
to the function in a parameter. This will increase the clarity of our code and
let us communicate and reason about the concept of finding the largest number
in a list independently of the specific places this concept is used.

Para eliminar essa duplicata, nós podemos criar uma abstração, que nesse caso
será na forma de uma função que opera em uma lista de inteiros passadas à 
função como um parâmetro. Isso aumentará a clareza do nosso código e nos
permitirá comunicar e pensar sobre o conceito de achar o maior número em uma
lista independentemente do lugar no qual esse conceito é usado.

In the program in Listing 10-3, we’ve extracted the code that finds the largest
number into a function named `largest`. This program can find the largest
number in two different lists of numbers, but the code from Listing 10-1 only
exists in one spot:

No programa na Listagem 10-3, nós extraímos o código que encontra o maior 
número para uma função chamada `maior`. Esse programa pode achar o maior número
em duas listas de números diferentes, mas o código da lista 10-1 existe apenas
em um lugar:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Nome do arquivo: src/main.rs</span>

```rust
fn largest(list: &[i32]) -> i32 {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let number_list = vec![34, 50, 25, 100, 65];

    let result = largest(&number_list);
    println!("The largest number is {}", result);
#    assert_eq!(result, 100);

    let number_list = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let result = largest(&number_list);
    println!("The largest number is {}", result);
#    assert_eq!(result, 6000);
}
```

```rust
fn maior(list: &[i32]) -> i32 {
    let mut maior = list[0];

    for &item in list.iter() {
        if item > maior {
            maior = item;
        }
    }

    maior
}

fn main() {
    let lista_numero = vec![34, 50, 25, 100, 65];

    let resultado = maior(&lista_numero);
    println!("O maior número é {}", resultado);
#    assert_eq!(resultado, 100);

    let lista_numero = vec![102, 34, 6000, 89, 54, 2, 43, 8];

    let resultado = maior(&lista_numero);
    println!("O maior número é {}", resultado);
#    assert_eq!(resultado, 6000);
}
```

<span class="caption">Listing 10-3: Abstracted code to find the largest number
in two lists</span>

<span class="caption">Listagem 10-3: Código abstraído para encontrar o maior
numḿero em duas listas</span>

The function has a parameter, `list`, which represents any concrete slice of
`i32` values that we might pass into the function. The code in the function
definition operates on the `list` representation of any `&[i32]`. When we call
the `largest` function, the code actually runs on the specific values that we
pass in.

A função tem o parâmetro, `list`, que representa qualquer corte concreto de
valores `i32` que podemos passar para uma função. O código na definição da
função opera na representação da `list` de qualquer `&[i32]`. Quando nós
passamos a função `maior`, o código é executado com os valores específicos
que nós passamos.

The mechanics we went through to get from Listing 10-2 to Listing 10-3 were
these steps:

As mecânicas que usamos da Listagem 10-2 para a Listagem 10-3 foram as
seguintes:

1. We noticed there was duplicate code.
2. We extracted the duplicate code into the body of the function, and specified
   the inputs and return values of that code in the function signature.
3. We replaced the two concrete places that had the duplicated code to call the
   function instead.

1. Nós notamos que havia código duplicado.
2. Nós extraímos o código duplicado para o corpo da função, e especificamos as
   entradas e os valores de retorno daquele código na assinatura da função.
3. Nós substituímos os dois locais concretos que tinham código duplicado para
chamar a função.

We can use these same steps with generics to reduce code duplication in
different ways in different scenarios. In the same way that the function body
is now operating on an abstract `list` instead of concrete values, code using
generics will operate on abstract types. The concepts powering generics are the
same concepts you already know that power functions, just applied in different
ways.

Nós podemos usar os mesmos passos usando tipos genéricos para reduzir a 
duplicação de código de diferentes modos em diferentes cenários. Do mesmo modo
que o corpo da função agora é operado em uma `list` abstrata ao invés de 
valores concretos, códigos usando tipos genéricos operarão em tipos abstratos. 
Os conceitos empoderando tipos genéricos são os mesmos conceitos que você já 
conhece que empodera funções, só que aplicado de modos diferentes.

What if we had two functions, one that found the largest item in a slice of
`i32` values and one that found the largest item in a slice of `char` values?
How would we get rid of that duplication? Let’s find out!

E se nós tivéssemos duas funções, uma que acha o maior item em um corte de 
valores `i32` e um que acha o maior item em um corte de valores `char`? Como 
nos livraríamos dessa duplicação? Vamos descobrir!
