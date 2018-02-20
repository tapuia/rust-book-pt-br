# Smart Pointers

# Ponteiros Inteligentes (Smart Pointers)

A *pointer* is a general concept for a variable that contains an address in
memory. This address refers to, or “points at,” some other data. The most common
kind of pointer in Rust is a reference, which you learned about in Chapter 4.
References are indicated by the `&` symbol and borrow the value they point to.
They don’t have any special capabilities other than referring to data. Also,
they don’t have any overhead and are the kind of pointer we use most often.

Um *ponteiro* é um conceito geral para uma variável que contém um endereço de
memória. Esse endereço se refere a, ou "aponta para", algum outro dado. O tipo
mais comum de ponteiro em Rust é a referência, sobre a qual você aprendeu no
Capítulo 4. Referências são indicadas pelo símbolo `&`, e pegam emprestado o
valor para o qual apontam. Elas não têm nenhuma outra habilidade senão
referir-se a dados. Além disso, elas não têm nenhum custo adicional e são o tipo
de ponteiro que usamos com maior frequência.

*Smart pointers*, on the other hand, are data structures that act like a pointer
but also have additional metadata and capabilities. The concept of smart
pointers isn’t unique to Rust: smart pointers originated in C++ and exist in
other languages as well. In Rust, the different smart pointers defined in the
standard library provide extra functionality beyond that provided by references.
One example that we’ll explore in this chapter is the *reference counting* smart
pointer type. This pointer enables you to have multiple owners of data by
keeping track of the number of owners and, when no owners remain, taking care of
cleaning up the data.

*Ponteiros inteligentes* (*smart pointers*), por outro lado, são estruturas de
dados que agem como um ponteiro mas também têm metadados e habilidades
adicionais. O conceito de ponteiros inteligentes não é exclusivo do Rust: ele
teve origem no C++ e também existe em outras linguagens. No Rust, os diferentes
ponteiros inteligentes definidos na biblioteca padrão proveem funcionalidades
além daquelas providas pelas referências. Um exemplo que vamos explorar neste
capítulo é o tipo de ponteiro inteligente de *contagem de referências*
(*reference counting*). Esse ponteiro lhe permite ter múltiplos possuidores de
um dado. Ele mantém registro do número de possuidores e, quando não resta
nenhum, cuida de limpar o dado.

In Rust, where we have the concept of ownership and borrowing, an additional
difference between references and smart pointers is that references are pointers
that only borrow data; in contrast, in many cases, smart pointers *own* the data
they point to.

Em Rust, onde temos os conceitos de posse (*ownership*) e empréstimo
(*borrowing*), uma diferença adicional entre referências e ponteiros
inteligentes é que referências são ponteiros que apenas *pegam emprestados* os
dados; em contraste, em muitos casos, ponteiros inteligentes *têm posse* dos
dados aos quais apontam.

We’ve already encountered a few smart pointers in this book, such as `String`
and `Vec<T>` in Chapter 8, although we didn’t call them smart pointers at the
time. Both these types count as smart pointers because they own some memory and
allow you to manipulate it. They also have metadata (such as their capacity) and
extra capabilities or guarantees (such as with `String` ensuring its data will
always be valid UTF-8).

Nós já encontramos alguns ponteiros inteligentes neste livro, como `String` e
`Vec<T>` no Capítulo 8, apesar de não os termos chamado de ponteiros
inteligentes naquele momento. Ambos esses tipos contam como ponteiros
inteligentes porque eles têm posse de uma parte da memória e permitem que você a
manipule. Eles também têm metadados (como sua capacidade) e habilidades extras
ou garantias (como a garantia que `String` dá de que seus dados serão sempre
UTF-8 válido).

Smart pointers are usually implemented using structs. The characteristic that
distinguishes a smart pointer from an ordinary struct is that smart pointers
implement the `Deref` and `Drop` traits. The `Deref` trait allows an instance of
the smart pointer struct to behave like a reference so we can write code that
works with either references or smart pointers. The `Drop` trait allows us to
customize the code that is run when an instance of the smart pointer goes out of
scope. In this chapter, we’ll discuss both traits and demonstrate why they’re
important to smart pointers.

Ponteiros inteligentes normalmente são implementados usando structs. A
característica que distingue um ponteiro inteligente de uma struct qualquer é
que ele implementa as traits `Deref` e `Drop`. A trait `Deref` permite que uma
instância da struct do ponteiro inteligente se comporte como uma referência.
Assim podemos escrever código que funcione tanto com referências quanto com
ponteiros inteligentes. A trait `Drop` nos permite personalizar o código que é
executado quando uma instância do smart pointer sai de escopo. Neste capítulo,
discutiremos ambas as traits e demonstraremos porque são importantes para
ponteiros inteligentes.

Given that the smart pointer pattern is a general design pattern used frequently
in Rust, this chapter won’t cover every existing smart pointer. Many libraries
have their own smart pointers, and you can even write your own. We’ll cover the
most common smart pointers in the standard library:

Dado que os ponteiros inteligentes são um padrão de projeto (*design pattern*)
usado com frequência em Rust, este capítulo não irá cobrir todo ponteiro
inteligente que existe. Muitas bibliotecas têm seus próprios ponteiros
inteligentes, e você pode até mesmo criar seus próprios. Nós vamos cobrir os
ponteiros inteligentes mais comuns na biblioteca padrão:

* `Box<T>` for allocating values on the heap
* `Rc<T>`, a reference counted type that enables multiple ownership
* `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that enforces
  the borrowing rules at runtime instead of compile time

* `Box<T>`, para alocar valores no heap
* `Rc<T>`, um tipo com contagem de referências que permite posse múltipla
* `Ref<T>` e `RefMut<T>`, acessados através de `RefCell<T>`, um tipo que aplica
  as regras de empréstimo em tempo de execução em vez de em tempo de compilação

In addition, we’ll cover the *interior mutability* pattern where an immutable
type exposes an API for mutating an interior value. We’ll also discuss
*reference cycles*: how they can leak memory and how to prevent them.

Além disso, vamos cobrir a pattern de *mutabilidade interior* (*interior
mutability*), onde um tipo imutável expõe uma API para modificar um valor
interno. Também vamos discutir *ciclos de referências*: como eles podem vazar
memória e como evitá-los.

Let’s dive in!

Mergulhemos!
