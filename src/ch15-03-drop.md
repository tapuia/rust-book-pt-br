## The `Drop` Trait Runs Code on Cleanup

## A Trait `Drop` Roda Código durante a Limpeza

The second trait important to the smart pointer pattern is `Drop`, which lets
us customize what happens when a value is about to go out of scope. We can
provide an implementation for the `Drop` trait on any type, and the code we
specify can be used to release resources like files or network connections.
We’re introducing `Drop` in the context of smart pointers because the
functionality of the `Drop` trait is almost always used when implementing a
smart pointer. For example, `Box<T>` customizes `Drop` to deallocate the space
on the heap that the box points to.

A segunda trait de importância para a pattern de ponteiros inteligentes é a
`Drop`, que nos permite personalizar o que acontece quando um valor está prestes
a sair de escopo. Nós podemos prover uma implementação da trait `Drop` para
qualquer tipo, e o código que especificarmos pode ser usado para liberar
recursos como arquivos ou conexões de rede. Estamos introduzindo `Drop` no
contexto de ponteiros inteligentes porque a funcionalidade da trait `Drop` é
usada quase sempre quando estamos implementando ponteiros inteligentes. Por
exemplo, o `Box<T>` customiza `Drop` para desalocar o espaço no heap para o qual
o box aponta.

In some languages, the programmer must call code to free memory or resources
every time they finish using an instance of a smart pointer. If they forget,
the system might become overloaded and crash. In Rust, we can specify that a
particular bit of code should be run whenever a value goes out of scope, and
the compiler will insert this code automatically. As a result, we don’t need to
be careful about placing cleanup code everywhere in a program that an instance
of a particular type is finished with, but we still won’t leak resources!

Em algumas linguagens, a pessoa que está programando deve chamar código para
liberar memória ou recursos toda vez que ela termina de usar uma instância de um
ponteiro inteligente. Se ela esquece, o sistema pode ficar sobrecarregado e
falhar. No Rust, podemos especificar que um pedaço específico de código deva ser
rodado sempre que um valor sair de escopo, e o compilador irá inserir esse
código automaticamente. Assim, não precisamos cuidadosamente colocar código de
limpeza em todos os lugares de um programa em que uma instância de um tipo
específico deixa de ser usada, e ainda assim não vazaremos recursos!

We specify the code to run when a value goes out of scope by implementing the
`Drop` trait. The `Drop` trait requires us to implement one method named `drop`
that takes a mutable reference to `self`. To see when Rust calls `drop`, let’s
implement `drop` with `println!` statements for now.

Para especificar o código que vai rodar quando um valor sair de escopo, nós
implementamos a trait `Drop`. A trait `Drop` requer que implementemos um método
chamado `drop` que recebe uma referência mutável de `self`. Para ver quando o
Rust chama `drop`, vamos implementar `drop` com declarações de `println!` por
ora.

Listing 15-14 shows a `CustomSmartPointer` struct whose only custom
functionality is that it will print `Dropping CustomSmartPointer!` when the
instance goes out of scope. This example demonstrates when Rust runs the `drop`
function:

A Listagem 15-14 mostra uma struct `CustomSmartPointer`
("PonteiroInteligentePersonalizado") cuja única funcionalidade é que ela irá
imprimir `Destruindo CustomSmartPointer!` quando a instância sair de escopo.
Este exemplo demonstra quando o Rust roda a função `drop`:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer { data: String::from("my stuff") };
    let d = CustomSmartPointer { data: String::from("other stuff") };
    println!("CustomSmartPointers created.");
}
```

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Destruindo CustomSmartPointer com dados `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer { data: String::from("alocado primeiro") };
    let d = CustomSmartPointer { data: String::from("alocado por último") };
    println!("CustomSmartPointers criados.");
}
```

<span class="caption">Listing 15-14: A `CustomSmartPointer` struct that
implements the `Drop` trait where we would put our cleanup code</span>

<span class="caption">Listagem 15-14: Uma struct `CustomSmartPointer` que
implementa a trait `Drop` onde colocaríamos nosso código de limpeza</span>

The `Drop` trait is included in the prelude, so we don’t need to import it. We
implement the `Drop` trait on `CustomSmartPointer` and provide an
implementation for the `drop` method that calls `println!`. The body of the
`drop` function is where you would place any logic that you wanted to run when
an instance of your type goes out of scope. We’re printing some text here to
demonstrate when Rust will call `drop`.

A trait `Drop` é incluída no prelúdio, então não precisamos importá-la. Nós
implementamos a trait `Drop` no `CustomSmartPointer` e providenciamos uma
implementação para o método `drop` que chama `println!`. O corpo da função
`drop` é onde você colocaria qualquer que fosse a lógica que você gostaria que
rodasse quando uma instância do seu tipo for sair de escopo. Aqui estamos
imprimindo um texto para demonstrar o momento em que o Rust chama `drop`.

In `main`, we create two instances of `CustomSmartPointer` and then print
`CustomSmartPointers created.`. At the end of `main`, our instance of
`CustomSmartPointer` will go out of scope, and Rust will call the code we put
in the `drop` method, printing our final message. Note that we didn’t need to
call the `drop` method explicitly.

Na `main`, nós criamos duas instâncias do `CustomSmartPointer` e então
imprimimos `CustomSmartPointers criados.`. No final da `main`, nossas instâncias
de `CustomSmartPointer` sairão de escopo, e o Rust irá chamar o código que
colocamos no método `drop`, imprimindo nossa mensagem final. Note que não
tivemos que chamar o método `drop` explicitamente.

When we run this program, we’ll see the following output:

Quando rodarmos esse programa, veremos a seguinte saída:

```text
CustomSmartPointers created.
Dropping CustomSmartPointer with data `other stuff`!
Dropping CustomSmartPointer with data `my stuff`!
```

```text
CustomSmartPointers criados.
Destruindo CustomSmartPointer com dados `alocado por último`!
Destruindo CustomSmartPointer com dados `alocado primeiro`!
```

Rust automatically called `drop` for us when our instance went out of scope,
calling the code we specified. Variables are dropped in the reverse order of
the order in which they were created, so `d` was dropped before `c`. This
example just gives you a visual guide to how the `drop` method works, but
usually you would specify the cleanup code that your type needs to run rather
than a print message.

O Rust chamou automaticamente `drop` para nós quando nossa instância saiu de
escopo, chamando o código que especificamos. Variáveis são destruídas na ordem
contrária à de criação, então `d` foi destruída antes de `c`. Esse exemplo serve
apenas para lhe dar um guia visual de como o método `drop` funciona, mas
normalmente você especificaria o código de limpeza que o seu tipo precisa rodar
em vez de imprimir uma mensagem.

### Dropping a Value Early with `std::mem::drop`

### Destruindo um Valor Cedo com `std::mem::drop`

Unfortunately, it’s not straightforward to disable the automatic `drop`
functionality. Disabling `drop` isn’t usually necessary; the whole point of the
`Drop` trait is that it’s taken care of automatically. Occasionally, you might
want to clean up a value early. One example is when using smart pointers that
manage locks: you might want to force the `drop` method that releases the lock
to run so other code in the same scope can acquire the lock. Rust doesn’t let
us call the `Drop` trait’s `drop` method manually; instead we have to call the
`std::mem::drop` function provided by the standard library if we want to force
a value to be dropped before the end of its scope.

Infelizmente, não é simples desabilitar a funcionalidade automática de `drop`.
Desabilitar o `drop` normalmente não é necessário; o ponto todo da trait `Drop`
é que isso seja feito automaticamente. Mas ocasionalmente, você pode querer
limpar um valor cedo. Um exemplo é quando usamos ponteiros inteligentes que
gerenciam locks: você pode querer forçar o método `drop` que libera o lock a
rodar para que outro código no mesmo escopo possa adquiri-lo. O Rust não nos
deixa chamar o método `drop` da trait `Drop` manualmente; em vez disso, temos
que chamar a função `std::mem::drop` disponibilizada pela biblioteca padrão se
queremos forçar um valor a ser destruído antes do fim de seu escopo.

Let’s see what happens when we try to call the `Drop` trait’s `drop` method
manually by modifying the `main` function in Listing 15-14, as shown in Listing
15-15:

Vamos ver o que acontece quando tentamos chamar o método `drop` da trait `Drop`
manualmente, modificando a função `main` da Listagem 15-14, conforme mostra a
Listagem 15-15:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust,ignore
fn main() {
    let c = CustomSmartPointer { data: String::from("some data") };
    println!("CustomSmartPointer created.");
    c.drop();
    println!("CustomSmartPointer dropped before the end of main.");
}
```

```rust,ignore
fn main() {
    let c = CustomSmartPointer { data: String::from("algum dado") };
    println!("CustomSmartPointer criado.");
    c.drop();
    println!("CustomSmartPointer destruído antes do fim da main.");
}
```

<span class="caption">Listing 15-15: Attempting to call the `drop` method from
the `Drop` trait manually to clean up early</span>

<span class="caption">Listagem 15-15: Tentando chamar o método `drop` da trait `Drop` manualmente para limpar cedo</span>

When we try to compile this code, we’ll get this error:

Quando tentamos compilar esse código, recebemos este erro:

```text
error[E0040]: explicit use of destructor method
  --> src/main.rs:14:7
   |
14 |     c.drop();
   |       ^^^^ explicit destructor calls not allowed
```

```text
erro[E0040]: uso explícito de método destrutor
  --> src/main.rs:14:7
   |
14 |     c.drop();
   |       ^^^^ chamadas explícitas a destrutores não são permitidas
```

This error message states that we’re not allowed to explicitly call `drop`. The
error message uses the term *destructor*, which is the general programming term
for a function that cleans up an instance. A *destructor* is analogous to a
*constructor* that creates an instance. The `drop` function in Rust is one
particular destructor.

Essa mensagem de erro afirma que não nos é permitido chamar explicitamente
`drop`. A mensagem de erro usa o termo *destrutor*, que é um termo geral de
programação para uma função que limpa uma instância. Um *destrutor* é análogo a
um *construtor*, que cria uma instância. A função `drop` em Rust é um destrutor
específico.

Rust doesn’t let us call `drop` explicitly because Rust would still
automatically call `drop` on the value at the end of `main`. This would be a
*double free* error because Rust would be trying to clean up the same value
twice.

O Rust não nos deixa chamar `drop` explicitamente porque o `drop` ainda seria
chamado no valor ao final da `main`. Isso seria um erro de *liberação dupla*
(*double free*) porque o Rust estaria tentando limpar o mesmo valor duas vezes.

We can’t disable the automatic insertion of `drop` when a value goes out of
scope, and we can’t call the `drop` method explicitly. So, if we need to force
a value to be cleaned up early, we can use the `std::mem::drop` function.

Nós não podemos desabilitar a inserção automática do `drop` quando um valor sai
de escopo, e também não podemos chamar o método `drop` explicitamente. Então, se
precisamos forçar um valor a ser limpo antes, podemos usar a função
`std::mem::drop`.

The `std::mem::drop` function is different than the `drop` method in the `Drop`
trait. We call it by passing the value we want to force to be dropped early as
an argument. The function is in the prelude, so we can modify `main` in Listing
15-14 to call the `drop` function, as shown in Listing 15-16:

A função `std::mem::drop` é diferente do método `drop` na trait `Drop`. Nós a
chamamos passando como argumento o valor que queremos forçar a ser destruído
cedo. Essa função está no prelúdio, então podemos modificar a `main` na Listagem
15-14 para chamar a função `drop`, como mostra a Listagem 15-16:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
# struct CustomSmartPointer {
#     data: String,
# }
#
# impl Drop for CustomSmartPointer {
#     fn drop(&mut self) {
#         println!("Dropping CustomSmartPointer!");
#     }
# }
#
fn main() {
    let c = CustomSmartPointer { data: String::from("some data") };
    println!("CustomSmartPointer created.");
    drop(c);
    println!("CustomSmartPointer dropped before the end of main.");
}
```

```rust
# struct CustomSmartPointer {
#     data: String,
# }
#
# impl Drop for CustomSmartPointer {
#     fn drop(&mut self) {
#         println!("Destruindo CustomSmartPointer!");
#     }
# }
#
fn main() {
    let c = CustomSmartPointer { data: String::from("algum dado") };
    println!("CustomSmartPointer criado.");
    drop(c);
    println!("CustomSmartPointer destruído antes do final da main.");
}
```

<span class="caption">Listing 15-16: Calling `std::mem::drop` to explicitly
drop a value before it goes out of scope</span>

<span class="caption">Listagem 15-16: Chamando `std::mem::drop` para destruir um
valor explicitamente antes que ele saia de escopo</span>

Running this code will print the following:

Rodar esse código irá imprimir o seguinte:

```text
CustomSmartPointer created.
Dropping CustomSmartPointer with data `some data`!
CustomSmartPointer dropped before the end of main.
```

```text
CustomSmartPointer criado.
Destruindo CustomSmartPointer com dados `algum dado`!
CustomSmartPointer destruído antes do final da main.
```

The text ```Dropping CustomSmartPointer with data `some data`!``` is printed
between the `CustomSmartPointer created.` and `CustomSmartPointer dropped
before the end of main.` text, showing that the `drop` method code is called to
drop `c` at that point.

O texto ```Destruindo CustomSmartPointer com dados `algum dado`!``` é impresso
entre o texto `CustomSmartPointer criado.` e `CustomSmartPointer destruído antes
do final da main.`, mostrando que o método `drop` é chamado para destruir o `c`
naquele ponto.

We can use code specified in a `Drop` trait implementation in many ways to make
cleanup convenient and safe: for instance, we could use it to create our own
memory allocator! With the `Drop` trait and Rust’s ownership system, we don’t
have to remember to clean up because Rust does it automatically.

Podemos usar o código especificado em uma implementação da trait `Drop` de
várias maneiras para tornar a limpeza conveniente e segura: por exemplo,
poderíamos usá-lo para criar nosso próprio alocador de memória! Com a trait
`Drop` e o sistema de posse do Rust, não temos que lembrar de fazer a limpeza
porque o Rust faz isso automaticamente.

We also don’t have to worry about accidentally cleaning up values still in use
because that would cause a compiler error: the ownership system that makes sure
references are always valid also ensures that `drop` gets called only once when
the value is no longer being used.

Também não temos que nos preocupar em acidentalmente limpar valores ainda em uso
porque isso causaria um erro de compilação: o sistema de posse que garante que
as referências são sempre válidas também garante que o `drop` é chamado apenas
uma vez quando o valor não está mais sendo usado.

Now that we’ve examined `Box<T>` and some of the characteristics of smart
pointers, let’s look at a few other smart pointers defined in the standard
library.

Agora que examinamos o `Box<T>` e alguma características de ponteiros
inteligentes, vamos dar uma olhada em alguns outros ponteiros inteligentes
definidos na biblioteca padrão.
