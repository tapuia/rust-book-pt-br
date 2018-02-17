## Slices
## _Slices_

Another data type that does not have ownership is the *slice*. Slices let you
reference a contiguous sequence of elements in a collection rather than the
whole collection.

Outro tipo de dados em que não há ownership é a *slice* (do inglês, fatia).
Slices lhe permitem referenciar uma sequência contígua de elementos em uma
coleção em vez de referenciar a coleção inteira.

Here’s a small programming problem: write a function that takes a string and
returns the first word it finds in that string. If the function doesn’t find a
space in the string, it means the whole string is one word, so the entire
string should be returned.

Aqui está um pequeno problema de programação: escrever uma função que pega uma
string e retorna a primeira palavra que encontrar dentro dela. Se a função não
encontrar um espaço na string, significa que a string inteira é uma palavra só,
então a string toda deve ser retornada.

Let’s think about the signature of this function:

Vamos pensar sobre a assinatura desta função:

```rust,ignore
fn first_word(s: &String) -> ?
```

```rust,ignore
fn primeira_palavra(s: &String) -> ?
```

This function, `first_word`, has a `&String` as a parameter. We don’t want
ownership, so this is fine. But what should we return? We don’t really have a
way to talk about *part* of a string. However, we could return the index of the
end of the word. Let’s try that as shown in Listing 4-5:

Esta função, `primeira_palavra`, tem uma `&String` como parâmetro. Nós não
queremos tomar posse dela, então tudo bem. Mas o que nós deveríamos retornar?
Não temos uma forma de falar sobre *parte* de uma string. No entanto, poderíamos
retornar o índice do final de uma palavra. Vamos tentar fazer isso, conforme
mostrado na Listagem 4-5:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

```rust
fn primeira_palavra(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }

    s.len()
}
```

<span class="caption">Listing 4-5: The `first_word` function that returns a
byte index value into the `String` parameter</span>

<span class="caption">Listagem 4-5: A função `primeira_palavra`, que retorna um
índice para um byte da `String` passada como parâmetro.</span>

Let’s break down this code a bit. Because we need to go through the `String`
element by element and check whether a value is a space, we’ll convert our
`String` to an array of bytes using the `as_bytes` method:

Vamos dividir este código em algumas partes. Como precisamos varrer a `String`
elemento por elemento, e verificar se algum valor é um espaço, vamos converter
nossa `String` em um array de bytes usando o método `as_bytes`:

```rust,ignore
let bytes = s.as_bytes();
```

Next, we create an iterator over the array of bytes using the `iter` method:

Depois, criamos um iterador sobre o array de bytes usando o método `iter`:

```rust,ignore
for (i, &item) in bytes.iter().enumerate() {
```

We’ll discuss iterators in more detail in Chapter 13. For now, know that `iter`
is a method that returns each element in a collection, and `enumerate` wraps
the result of `iter` and returns each element as part of a tuple instead. The
first element of the returned tuple is the index, and the second element is a
reference to the element. This is a bit more convenient than calculating the
index ourselves.

Vamos discutir sobre iteradores em mais detalhes no Capítulo 13. Por enquanto,
saiba que `iter` é um método que retorna cada elemento em uma coleção, e
`enumerate` encapsula o resultado do `iter` e retorna cada elemento como parte
de uma tupla. O primeiro elemento da tupla é o índice, e o segundo elemento é
uma referência ao valor. Isto é um pouco mais conveniente do que calcular o
índice nós mesmos.

Because the `enumerate` method returns a tuple, we can use patterns to
destructure that tuple, just like everywhere else in Rust. So in the `for`
loop, we specify a pattern that has `i` for the index in the tuple and `&item`
for the single byte in the tuple. Because we get a reference to the element
from `.iter().enumerate()`, we use `&` in the pattern.

Como o método `enumerate` retorna uma tupla, podemos usar padrões para
desestruturar esta tupla, assim como qualquer outra coisa em Rust. Então, no
`for`, especificamos um padrão que tem `i` para o índice na tupla e `&item` para
o byte. Como pegamos uma referência ao elemento através do
`.iter().enumerate()`, usamos um `&` neste padrão.

We search for the byte that represents the space by using the byte literal
syntax. If we find a space, we return the position. Otherwise, we return the
length of the string by using `s.len()`:

Nós procuramos o byte que representa um espaço usando a sintaxe de byte literal.
Se encontrarmos um espaço, retornamos a posição dele. Caso contrário, retornamos
o tamanho da string usando `s.len()`:

```rust,ignore
    if item == b' ' {
        return i;
    }
}
s.len()
```

We now have a way to find out the index of the end of the first word in the
string, but there’s a problem. We’re returning a `usize` on its own, but it’s
only a meaningful number in the context of the `&String`. In other words,
because it’s a separate value from the `String`, there’s no guarantee that it
will still be valid in the future. Consider the program in Listing 4-6 that
uses the `first_word` function from Listing 4-5:

Agora temos uma forma de descobrir o índice do fim da primeira palavra na
string, mas tem um problema. Estamos retornando um `usize` por si só, mas ele só
possui um significado no contexto da `&String`. Em outras palavras, como é um
valor separado da `String`, não há garantia de que ele ainda será válido no
futuro. Considere o programa na Listagem 4-6, que usa a função da Listagem 4-5:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# fn first_word(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word will get the value 5.

    s.clear(); // This empties the String, making it equal to "".

    // word still has the value 5 here, but there's no more string that
    // we could meaningfully use the value 5 with. word is now totally invalid!
}
```

```rust
# fn primeira_palavra(s: &String) -> usize {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return i;
#         }
#     }
#
#     s.len()
# }
#
fn main() {
    let mut s = String::from("texto longo");

    let palavra = primeira_palavra(&s); // palavra vai ter o valor 5.

    s.clear(); // Isso esvazia a String, deixando ela igual a "".

    // palavra ainda tem o valor 5, aqui, mas já não há mais uma string para a
    // qual o valor 5 faça algum sentido. palavra agora é totalmente inválida!
}
```

<span class="caption">Listing 4-6: Storing the result from calling the
`first_word` function then changing the `String` contents</span>

<span class="caption">Listagem 4-6: Armazenando o resultado de uma chamada à
função `primeira_palavra`, e depois, mudando o conteúdo da `String`.</span>

This program compiles without any errors and also would if we used `word` after
calling `s.clear()`. `word` isn’t connected to the state of `s` at all, so
`word` still contains the value `5`. We could use that value `5` with the
variable `s` to try to extract the first word out, but this would be a bug
because the contents of `s` have changed since we saved `5` in `word`.

Este programa compila sem erros, e também o faria se usássemos a variável
`palavra` depois de chamar `s.clear()`. `palavra` não está conectada ao estado
de `s` de nenhuma forma, então, `palavra` ainda contém o valor `5`. Poderíamos
usar esse valor `5` com a variável `s` para tentar extrair a primeira palavra da
string, mas isso seria um bug, pois o conteúdo de `s` já mudou após termos salvo
o valor `5` na variável `word`.

Having to worry about the index in `word` getting out of sync with the data in
`s` is tedious and error prone! Managing these indices is even more brittle if
we write a `second_word` function. Its signature would have to look like this:

Ter que se preocupar sobre o índice da `palavra` ficar fora de sincronia com os
dados em `s` é tedioso e propenso a erros! Gerenciar esses índices é ainda mais
delicado se escrevermos uma função `segunda_palavra`. Sua assinatura teria que
ser algo do tipo:

```rust,ignore
fn second_word(s: &String) -> (usize, usize) {
```

```rust,ignore
fn segunda_palavra(s: &String) -> (usize, usize) {
```

Now we’re tracking a start *and* an ending index, and we have even more values
that were calculated from data in a particular state but aren’t tied to that
state at all. We now have three unrelated variables floating around that need
to be kept in sync.

Agora estamos rastreando os índices do início *e* do final, e temos ainda mais
valores que são calculados a partir dos dados em um estado particular, mas que
não estão vinculados a este estado de nenhuma forma. Agora temos três variáveis
não relacionadas flutuando que precisam ser mantidas em sincronia.

Luckily, Rust has a solution to this problem: string slices.

Felizmente, Rust possui uma solução para este problema: slices de string.

### String Slices
### Slices de String

A *string slice* is a reference to part of a `String`, and looks like this:

Uma *slice de string* é uma referência para uma parte de uma `String`, e tem a
seguinte forma:

```rust
let s = String::from("hello world");

let hello = &s[0..5];
let world = &s[6..11];
```

```rust
let s = String::from("texto longo");

let texto = &s[0..5];
let longo = &s[6..11];
```

This is similar to taking a reference to the whole `String` but with the extra
`[0..5]` bit. Rather than a reference to the entire `String`, it’s a reference
to a portion of the `String`. The `start..end` syntax is a range that begins at
`start` and continues up to, but not including, `end`.

Isto é similar a pegar uma referência à `String` inteira, mas com um `[0..5]` a
mais. Em vez de uma referência à `String` inteira, trata-se de uma referência a
uma porção da `String`. A sintaxe `início..fim` representa um _range_
(uma faixa) que começa em `início` e continua até, mas não incluindo, `fim`.

We can create slices using a range within brackets by specifying
`[starting_index..ending_index]`, where `starting_index` is the first position
included in the slice and `ending_index` is one more than the last position
included in the slice. Internally, the slice data structure stores the starting
position and the length of the slice, which corresponds to `ending_index` minus
`starting_index`. So in the case of `let world = &s[6..11];`, `world` would be
a slice that contains a pointer to the 6th byte of `s` and a length value of 5.

Podemos criar slices usando um range entre colchetes especificando
`[índice_inicial..índice_final]`, em que `índice_inicial` é a primeira posição
inclusa na slice, e `índice_final` é um a mais que a última posição inclusa na
slice. Internamente, a estrutura de dados de uma slice armazena a posição
inicial e o tamanho da slice, que corresponde a `índice_final` menos
`índice_inicial`. Então, no caso do `let longo = &s[6..11];`, `longo` seria uma
slice que contém um ponteiro para o sétimo byte de `s` (índice 6) e um tamanho
igual a 5.

Figure 4-6 shows this in a diagram.

A Figura 4-6 mostra isto em um diagrama.

<img alt="world containing a pointer to the 6th byte of String s and a length 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<img alt="mundo contendo um ponteiro para o 7o byte da String s e um tamanho 5" src="img/trpl04-06.svg" class="center" style="width: 50%;" />

<span class="caption">Figure 4-6: String slice referring to part of a
`String`</span>

<span class="caption">Figura 4-6: Slice referente a uma parte de uma
`String`</span>

With Rust’s `..` range syntax, if you want to start at the first index (zero),
you can drop the value before the two periods. In other words, these are equal:

Com a sintaxe de range do Rust (`..`), se você quiser começar com o primeiro
elemento (índice zero), você pode omitir o valor antes dos dois pontos. Em
outras palavras, estas formas são equivalentes:

```rust
let s = String::from("hello");

let slice = &s[0..2];
let slice = &s[..2];
```

```rust
let s = String::from("texto");

let slice = &s[0..2];
let slice = &s[..2];
```

By the same token, if your slice includes the last byte of the `String`, you
can drop the trailing number. That means these are equal:

Da mesma forma, se a sua slice inclui o último byte da `String`, você pode
omitir o último número. Isso significa que as seguintes formas são equivalentes:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[3..len];
let slice = &s[3..];
```

```rust
let s = String::from("texto");

let tamanho = s.len();

let slice = &s[3..tamanho];
let slice = &s[3..];
```

You can also drop both values to take a slice of the entire string. So these
are equal:

Você também pode omitir ambos os valores para pegar uma slice da string inteira.
Logo, essas duas formas são equivalentes:

```rust
let s = String::from("hello");

let len = s.len();

let slice = &s[0..len];
let slice = &s[..];
```

```rust
let s = String::from("texto");

let tamanho = s.len();

let slice = &s[0..tamanho];
let slice = &s[..];
```

> Note: String slice range indices must occur at valid UTF-8 character
> boundaries. If you attempt to create a string slice in the middle of a
> multibyte character, your program will exit with an error. For the purposes
> of introducing string slices, we are assuming ASCII only in this section; a
> more thorough discussion of UTF-8 handling is in the “Strings” section of
> Chapter 8.

> Nota: Os índices do range de uma slice de string devem coincidir com os
> limites entre caracteres UTF-8 válidos. Se você tentar criar uma slice de
> string no meio de um caractere que tenha mais de um byte, seu programa vai
> terminar com erro. Para introduzir slices de string, estamos utilizando
> apenas caracteres ASCII nesta seção; uma discussão mais detalhada sobre
> manipulação de caracteres UTF-8 será feita na seção "Strings" do Capítulo 8.

With all this information in mind, let’s rewrite `first_word` to return a
slice. The type that signifies “string slice” is written as `&str`:

Com toda essa informação em mente, vamos reescrever a função `primeira_palavra`
para retornar uma slice. O tipo que representa "slice de string" é escrito como
`&str`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
fn first_word(s: &String) -> &str {
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
fn primeira_palavra(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

We get the index for the end of the word in the same way as we did in Listing
4-5, by looking for the first occurrence of a space. When we find a space, we
return a string slice using the start of the string and the index of the space
as the starting and ending indices.

Pegamos o índice para o fim da palavra da mesma forma como fizemos na Listagem
4-5, buscando a primeira ocorrência de um espaço. Quando o encontramos,
retornamos uma slice de string usando o início da string e o índice do espaço
como índices inicial e final, respectivamente.

Now when we call `first_word`, we get back a single value that is tied to the
underlying data. The value is made up of a reference to the starting point of
the slice and the number of elements in the slice.

Agora, quando chamamos `primeira_palavra`, pegamos de volta um único valor que
está vinculado à string. O valor é composto de uma referência para o ponto
inicial da slice e o número de elementos que ela contém.

Returning a slice would also work for a `second_word` function:

Retornar uma slice também funcionaria para uma função `segunda_palavra`:

```rust,ignore
fn second_word(s: &String) -> &str {
```

```rust,ignore
fn segunda_palavra(s: &String) -> &str {
```

We now have a straightforward API that’s much harder to mess up, since the
compiler will ensure the references into the `String` remain valid. Remember
the bug in the program in Listing 4-6, when we got the index to the end of the
first word but then cleared the string so our index was invalid? That code was
logically incorrect but didn’t show any immediate errors. The problems would
show up later if we kept trying to use the first word index with an emptied
string. Slices make this bug impossible and let us know we have a problem with
our code much sooner. Using the slice version of `first_word` will throw a
compile time error:

Agora, temos uma API bem direta que é bem mais difícil de bagunçar, uma vez que
o compilador vai se certificar que as referências dentro da `String`
permanecerão válidas. Lembra do bug do programa na Listagem 4-6, quando
obtivemos o índice para o fim da primeira palavra mas depois limpamos a string,
invalidando o índice obtido? Aquele código era logicamente incorreto, mas não
mostrava nenhum erro imediato. Os problemas apareceriam mais tarde quando
tentássemos usar o índice da primeira palavra com uma string que foi esvaziada.
Slices tornam esse bug impossível de acontecer e nos permitem saber que temos um
problema no código muito mais cedo. Na versão usando slice, a função
`primeira_palavra` vai lançar um erro em tempo de compilação:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // Error!
}
```

```rust,ignore
fn main() {
    let mut s = String::from("texto longo");

    let palavra = first_word(&s);

    s.clear(); // Erro!
}
```

Here’s the compiler error:

Aqui está o erro:

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:5
  |
4 |     let word = first_word(&s);
  |                            - immutable borrow occurs here
5 |
6 |     s.clear(); // Error!
  |     ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

```text
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:5
  |
4 |     let word = primeira_palavra(&s);
  |                                  - immutable borrow occurs here
5 |
6 |     s.clear(); // Erro!
  |     ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

Recall from the borrowing rules that if we have an immutable reference to
something, we cannot also take a mutable reference. Because `clear` needs to
truncate the `String`, it tries to take a mutable reference, which fails. Not
only has Rust made our API easier to use, but it has also eliminated an entire
class of errors at compile time!

Voltando às regras de borrowing, lembre-se que, se temos uma referência imutável
para algum valor, não podemos também obter uma referência mutável do mesmo. Como
`clear` precisa truncar a `String`, esse método tenta obter uma referência
mutável, e acaba falhando. O Rust não só tornou nossa API mais fácil de usar,
como também eliminou uma classe inteira de erros em tempo de compilação!

#### String Literals Are Slices
#### Strings Literais São Slices

Recall that we talked about string literals being stored inside the binary. Now
that we know about slices, we can properly understand string literals:

Lembre-se de que falamos sobre strings literais serem armazenadas dentro do
binário. Agora que conhecemos slices, podemos entender strings literais
adequadamente:

```rust
let s = "Hello, world!";
```

```rust
let s = "Olá, mundo!";
```

The type of `s` here is `&str`: it’s a slice pointing to that specific point of
the binary. This is also why string literals are immutable; `&str` is an
immutable reference.

O tipo de `s` aqui é `&str`: é uma slice apontando para aquele ponto específico
do binário. Também é por isso que strings literais são imutáveis; `&str` é uma
referência imutável.

#### String Slices as Parameters
#### Slices de Strings como Parâmetros

Knowing that you can take slices of literals and `String`s leads us to one more
improvement on `first_word`, and that’s its signature:

Saber que você pode obter slices de literais e `String`s nos levam a mais um
aprimoramento da função `primeira_palavra`, e aqui está sua assinatura:

```rust,ignore
fn first_word(s: &String) -> &str {
```

```rust,ignore
fn primeira_palavra(s: &String) -> &str {
```

A more experienced Rustacean would write the following line instead because it
allows us to use the same function on both `String`s and `&str`s:

Um Rustáceo mais experiente escreveria esta função conforme a seguir, permitindo
utilizar a mesma função com `String`s e `&str`s:

```rust,ignore
fn first_word(s: &str) -> &str {
```

```rust,ignore
fn primeira_palavra(s: &str) -> &str {
```

If we have a string slice, we can pass that directly. If we have a `String`, we
can pass a slice of the entire `String`. Defining a function to take a string
slice instead of a reference to a String makes our API more general and useful
without losing any functionality:

Se temos uma slice de string, podemos passá-la diretamente. Se temos uma
`String`, podemos passar uma slice da `String` inteira. Definir uma função que
recebe uma slice em vez de uma referência para uma String deixa nossa API mais
genérica e útil sem perder nenhuma funcionalidade:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# fn first_word(s: &str) -> &str {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return &s[0..i];
#         }
#     }
#
#     &s[..]
# }
fn main() {
    let my_string = String::from("hello world");

    // first_word works on slices of `String`s
    let word = first_word(&my_string[..]);

    let my_string_literal = "hello world";

    // first_word works on slices of string literals
    let word = first_word(&my_string_literal[..]);

    // since string literals *are* string slices already,
    // this works too, without the slice syntax!
    let word = first_word(my_string_literal);
}
```

```rust
# fn primeira_palavra(s: &str) -> &str {
#     let bytes = s.as_bytes();
#
#     for (i, &item) in bytes.iter().enumerate() {
#         if item == b' ' {
#             return &s[0..i];
#         }
#     }
#
#     &s[..]
# }
fn main() {
    let minha_string = String::from("texto longo");

    // primeira_palavra funciona com slices de `String`s
    let palavra = primeira_palavra(&minha_string[..]);

    let minha_string_literal = "texto longo";

    // primeira_palavra funciona com strings literais
    let palavra = primeira_palavra(&minha_string_literal[..]);
	
	// uma vez que strings literais *são* slices de strings,
	// isso também funciona, sem nem usar sintaxe de slice!
    let palavra = primeira_palavra(minha_string_literal);
}
```

### Other Slices
### Outras Slices

String slices, as you might imagine, are specific to strings. But there’s a
more general slice type, too. Consider this array:

Slices de string, como você pode imaginar, são específicas de strings. Mas há
também um tipo de slice mais genérico. Considere esta array:

```rust
let a = [1, 2, 3, 4, 5];
```

Just like we might want to refer to a part of a string, we might want to refer
to part of an array and would do so like this:

Assim como às vezes queremos nos referir a uma parte de uma string, podemos
também querer nos referir a uma parte de uma array, e faríamos isso da seguinte
forma:

```rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

This slice has the type `&[i32]`. It works the same way as string slices do, by
storing a reference to the first element and a length. You’ll use this kind of
slice for all sorts of other collections. We’ll discuss these collections in
detail when we talk about vectors in Chapter 8.

Essa slice tem o tipo `&[i32]`. Ela funciona da mesma forma que as slices de
string, armazenando uma referência para o primeiro elemento e um tamanho. Você
vai usar esse tipo de slice para todos os tipos de coleções. Vamos discutir
essas coleções em mais detalhe quando falarmos sobre vetores no Capítulo 8.

## Summary
## Resumo

The concepts of ownership, borrowing, and slices are what ensure memory safety
in Rust programs at compile time. The Rust language gives you control over your
memory usage like other systems programming languages, but having the owner of
data automatically clean up that data when the owner goes out of scope means
you don’t have to write and debug extra code to get this control.

Os conceitos de ownership, borrowing, e slices são o que garantem a segurança de
memória dos programas em Rust em tempo de compilação. A linguagem Rust lhe dá
controle sobre o uso da memória, assim como outras linguagens de programação de
sistemas, mas como o dono dos dados limpa automaticamente a memória quando ele
sai de escopo, você não tem que escrever e debugar código extra para ter esse
controle.

Ownership affects how lots of other parts of Rust work, so we’ll talk about
these concepts further throughout the rest of the book. Let’s move on to the
next chapter and look at grouping pieces of data together in a `struct`.

O ownership afeta o funcionamento de várias outras partes do Rust, por isso
vamos falar um pouco mais sobre esses conceitos neste livro daqui para a frente.
Vamos seguir para o próximo capítulo e ver como agrupar dados em uma `struct`.
