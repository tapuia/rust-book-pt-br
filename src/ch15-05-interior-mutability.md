## `RefCell<T>` and the Interior Mutability Pattern

## `RefCell<T>` e a Pattern de Mutabilidade Interior

*Interior mutability* is a design pattern in Rust that allows you to mutate
data even when there are immutable references to that data: normally, this
action is disallowed by the borrowing rules. To do so, the pattern uses
`unsafe` code inside a data structure to bend Rust’s usual rules that govern
mutation and borrowing. We haven’t yet covered unsafe code; we will in Chapter
19. We can use types that use the interior mutability pattern when we can
ensure that the borrowing rules will be followed at runtime, even though the
compiler can’t guarantee that. The `unsafe` code involved is then wrapped in a
safe API, and the outer type is still immutable.

*Mutabilidade interior* (*interior mutability*) é uma design pattern em Rust que
lhe permite modificar um dado mesmo quando há referências imutáveis a ele:
normalmente, esta ação é proibida pelas regras de empréstimo. Para fazer isso, a
pattern usa código `unsafe` (*inseguro*) dentro de uma estrutura de dados para
dobrar as regras normais do Rust que governam mutação e empréstimo. Nós ainda
não cobrimos código unsafe; faremos isso no Capítulo 19. Podemos usar tipos que
usam a pattern de mutabilidade interior quando podemos garantir que as regras de
empréstimo serão seguidas em tempo de execução, ainda que o compilador não o
possa garantir. O código `unsafe` envolvido é então embrulhado em uma API safe,
e o tipo exterior permanece imutável.

Let’s explore this concept by looking at the `RefCell<T>` type that follows the
interior mutability pattern.

Para explorar este conceito, vamos ver o tipo `RefCell<T>` que segue a pattern
de mutabilidade interior.

### Enforcing Borrowing Rules at Runtime with `RefCell<T>`

### Aplicando Regras de Empréstimo em Tempo de Execução com o `RefCell<T>`

Unlike `Rc<T>`, the `RefCell<T>` type represents single ownership over the data
it holds. So, what makes `RefCell<T>` different than a type like `Box<T>`?
Recall the borrowing rules you learned in Chapter 4:

Diferente do `Rc<T>`, o tipo `RefCell<T>` representa posse única sobre o dado
que ele contém. Então o que torna o `RefCell<T>` diferente de um tipo como o
`Box<T>`? Lembre-se das regras de empréstimo que você aprendeu no Capítulo 4:

* At any given time, you can have *either* but not both of the following: one
  mutable reference or any number of immutable references.
* References must always be valid.

* Em qualquer momento, você pode ter *um dos* mas não ambos os seguintes: uma
  única referência mutável *ou* qualquer número de referências imutáveis;
* Referências devem sempre ser válidas.

With references and `Box<T>`, the borrowing rules’ invariants are enforced at
compile time. With `RefCell<T>`, these invariants are enforced *at runtime*.
With references, if you break these rules, you’ll get a compiler error. With
`RefCell<T>`, if you break these rules, your program will `panic!` and exit.

Com referências e com o `Box<T>`, as invariantes das regras de empréstimo são
aplicadas em tempo de compilação. Com o `RefCell<T>`, essas invariantes são
aplicadas *em tempo de execução*. Com referências, se você quebra essas regras,
você recebe um erro de compilação. Com o `RefCell<T>`, se você quebrar essas
regras, seu programa irá sofrer um `panic!` e terminar.

The advantages of checking the borrowing rules at compile time are that errors
will be caught sooner in the development process, and there is no impact on
runtime performance because all the analysis is completed beforehand. For those
reasons, checking the borrowing rules at compile time is the best choice in the
majority of cases, which is why this is Rust’s default.

As vantagens de checar as regras de empréstimo em tempo de compilação são que
erros são pegos mais cedo no processo de desenvolvimento, e não há nenhum custo
de desempenho de execução porque toda a análise é completada de antemão. Por
esses motivos, checar as regras de empréstimo em tempo de compilação é a melhor
opção na maioria dos casos, e por isso este é o padrão do Rust.

The advantage of checking the borrowing rules at runtime instead is that
certain memory safe scenarios are then allowed, whereas they are disallowed by
the compile time checks. Static analysis, like the Rust compiler, is inherently
conservative. Some properties of code are impossible to detect by analyzing the
code: the most famous example is the Halting Problem, which is beyond the scope
of this book but is an interesting topic to research.

A vantagem de checar as regras de empréstimo em tempo de execução,
alternativamente, é que certos cenários *memory-safe* (*seguros em termos de
memória*) são então permitidos, ao passo que seriam proibidos pelas checagens em
tempo de compilação. A análise estática, como a do compilador Rust, é
inerentemente conservadora. Algumas propriedades do programa são impossíveis de
detectar analisando o código: o exemplo mais famoso é o Problema da Parada, que
está além do escopo deste livro mas é um tópico interessante para pesquisa.

Because some analysis is impossible, if the Rust compiler can’t be sure the
code complies with the ownership rules, it might reject a correct program; in
this way, it’s conservative. If Rust accepted an incorrect program, users
wouldn’t be able to trust in the guarantees Rust makes. However, if Rust
rejects a correct program, the programmer will be inconvenienced, but nothing
catastrophic can occur. The `RefCell<T>` type is useful when you’re sure your
code follows the borrowing rules, but the compiler is unable to understand and
guarantee that.

Como algumas análises são impossíveis, se o compilador Rust não consegue se
assegurar que o código obedece às regras de posse, ele pode rejeitar um programa
correto; neste sentido, ele é conservador. Se o Rust aceitasse um programa
incorreto, os usuários não poderiam confiar nas garantias que ele faz. Se, por
outro lado, o Rust rejeita um programa correto, o programador terá alguma
inconveniência, mas nada catastrófico pode acontecer. O tipo `RefCell<T>` é útil
quando você tem certeza que seu código segue as regras de empréstimo, mas o
compilador é incapaz de entender e garantir isso.

Similar to `Rc<T>`, `RefCell<T>` is only for use in single-threaded scenarios
and will give you a compile time error if you try using it in a multithreaded
context. We’ll talk about how to get the functionality of `RefCell<T>` in a
multithreaded program in Chapter 16.

Assim como o `Rc<T>`, o `RefCell<T>` é apenas para uso em cenários de thread
única e lhe darão um erro de compilação se você tentar usá-lo em um contexto de
múltiplas threads. Falaremos sobre como obter a funcionalidade de um
`RefCell<T>` em um programa multithread no Capítulo 16.

Here is a recap of the reasons to choose `Box<T>`, `Rc<T>`, or `RefCell<T>`:

Aqui está uma recapitulação das razões para escolher o `Box<T>`, o `Rc<T>` ou o
`RefCell<T>`:

* `Rc<T>` enables multiple owners of the same data; `Box<T>` and `RefCell<T>`
  have single owners.
* `Box<T>` allows immutable or mutable borrows checked at compile time; `Rc<T>`
  only allows immutable borrows checked at compile time; `RefCell<T>` allows
  immutable or mutable borrows checked at runtime.
* Because `RefCell<T>` allows mutable borrows checked at runtime, we can mutate
  the value inside the `RefCell<T>` even when the `RefCell<T>` is immutable.

* O `Rc<T>` permite múltiplos possuidores do mesmo dado; `Box<T>` e `RefCell<T>`
  têm possuidores únicos.
* O `Box<T>` permite empréstimos imutáveis ou mutáveis checados em tempo de
  compilação; o `Rc<T>` permite apenas empréstimos imutáveis em tempo de
  compilação; o `RefCell<T>` permite empréstimos imutáveis ou mutáveis checados
  em tempo de execução.
* Como o `RefCell<T>` permite empréstimos mutáveis checados em tempo de
  execução, nós podemos modificar o valor dentro de um `RefCell<T>` mesmo quando
  o `RefCell<T>` é imutável.

Mutating the value inside an immutable value is the *interior mutability*
pattern. Let’s look at a situation in which interior mutability is useful and
examine how it’s possible.

Modificar o valor dentro de um valor imutável é a pattern de *mutabilidade
interior*. Vamos dar uma olhada em uma situação em que a mutabilidade interior é
útil e examinar como ela é possível.

### Interior Mutability: A Mutable Borrow to an Immutable Value

### Mutabilidade Interior: Um Empréstimo Mutável de um Valor Imutável

A consequence of the borrowing rules is that when we have an immutable value,
we can’t borrow it mutably. For example, this code won’t compile:

Uma consequência das regras de empréstimo é que quando temos um valor imutável,
nós não podemos pegá-lo emprestado mutavelmente. Por exemplo, este código não
compila:

```rust,ignore
fn main() {
    let x = 5;
    let y = &mut x;
}
```

```rust,ignore
fn main() {
    let x = 5;
    let y = &mut x;
}
```

When we try to compile this code, we’ll get the following error:

Quando tentamos compilar este código, recebemos o seguinte erro:

```text
error[E0596]: cannot borrow immutable local variable `x` as mutable
 --> src/main.rs:3:18
  |
2 |     let x = 5;
  |         - consider changing this to `mut x`
3 |     let y = &mut x;
  |                  ^ cannot borrow mutably
```

```text
erro[E0596]: não posso pegar emprestado a variável local imutável `x` como
mutável
 --> src/main.rs:3:18
  |
2 |     let x = 5;
  |         - considere mudar isto para `mut x`
3 |     let y = &mut x;
  |                  ^ não posso pegar emprestado mutavelmente
```

However, there are situations in which it would be useful for a value to mutate
itself in its methods, but to other code, the value would appear immutable.
Code outside the value’s methods would not be able to mutate the value. Using
`RefCell<T>` is one way to get the ability to have interior mutability. But
`RefCell<T>` doesn’t get around the borrowing rules completely: the borrow
checker in the compiler allows this interior mutability, and the borrowing
rules are checked at runtime instead. If we violate the rules, we’ll get a
`panic!` instead of a compiler error.

Contudo, há situações em que seria útil para um valor modificar a si mesmo em
seus métodos, mas continuar parecendo imutável para código externo. Código fora
dos métodos do valor não teriam como modificá-lo. Usar o `RefCell<T>` é um jeito
de obter a habilidade de ter mutabilidade interior. Mas o `RefCell<T>` não dá a
volta nas regras de empréstimo por completo: o borrow checker no compilador
permite esta mutabilidade interior, e as regras de empréstimo são em vez disso
checadas em tempo de execução. Se violarmos as regras, receberemos um `panic!`
em vez de um erro de compilação.

Let’s work through a practical example where we can use `RefCell<T>` to mutate
an immutable value and see why that is useful.

Vamos trabalhar com um exemplo prático onde podemos usar o `RefCell<T>` para
modificar um valor imutável e ver por que isto é útil.

#### A Use Case for Interior Mutability: Mock Objects

#### Um Caso de Uso para a Mutabilidade Interior: Objetos Simulados

A *test double* is the general programming concept for a type used in place of
another type during testing. *Mock objects* are specific types of test doubles
that record what happens during a test so we can assert that the correct
actions took place.

Um *dublê de teste* (*test double*) é um conceito geral de programação para um
tipo usado no lugar de outro durante os testes. *Objetos simulados* (*mock
objects*) são tipos específicos de dublês de teste que registram o que acontece
durante o teste para que possamos confirmar que as ações corretas aconteceram.

Rust doesn’t have objects in the same sense as other languages have objects,
and Rust doesn’t have mock object functionality built into the standard library
like some other languages do. However, we can definitely create a struct that
will serve the same purposes as a mock object.

Rust não tem objetos da mesma forma que outras linguagens, e não tem
funcionalidade de objetos simulados embutida na biblioteca padrão como algumas
outras linguagens. Contudo, certamente podemos criar uma struct que serve os
mesmos propósitos que um objeto simulado.

Here’s the scenario we’ll test: we’ll create a library that tracks a value
against a maximum value and sends messages based on how close to the maximum
value the current value is. This library could be used for keeping track of a
user’s quota for the number of API calls they’re allowed to make, for example.

Eis o cenário que vamos testar: vamos criar uma biblioteca que acompanha um
valor contra um valor máximo e envia mensagens com base em quão próximo do valor
máximo o valor atual está. Esta biblioteca pode ser usada para acompanhar a cota
de um usuário para o número de chamadas de API que ele tem direito a fazer, por
exemplo.

Our library will only provide the functionality of tracking how close to the
maximum a value is and what the messages should be at what times. Applications
that use our library will be expected to provide the mechanism for sending the
messages: the application could put a message in the application, send an
email, send a text message, or something else. The library doesn’t need to know
that detail. All it needs is something that implements a trait we’ll provide
called `Messenger`. Listing 15-20 shows the library code:

Nossa biblioteca irá prover somente a funcionalidade de acompanhar quão perto do
máximo um valor está e o que as mensagens deveriam ser em quais momentos. As
aplicações que usarem nossa biblioteca terão a responsabilidade de prover o
mecanismo para enviar as mensagens: a aplicação pode pôr a mensagem na própria
aplicação, enviar um email, uma mensagem de texto, ou alguma outra coisa. A
biblioteca não precisa saber deste detalhe. Tudo que ela precisa é de algo que
implemente uma trait que iremos prover chamada `Mensageiro`. A
Listagem 15-20 mostra o código da biblioteca:

<span class="filename">Arquivo: src/lib.rs</span>

```rust
pub trait Messenger {
    fn send(&self, msg: &str);
}

pub struct LimitTracker<'a, T: 'a + Messenger> {
    messenger: &'a T,
    value: usize,
    max: usize,
}

impl<'a, T> LimitTracker<'a, T>
    where T: Messenger {
    pub fn new(messenger: &T, max: usize) -> LimitTracker<T> {
        LimitTracker {
            messenger,
            value: 0,
            max,
        }
    }

    pub fn set_value(&mut self, value: usize) {
        self.value = value;

        let percentage_of_max = self.value as f64 / self.max as f64;

        if percentage_of_max >= 0.75 && percentage_of_max < 0.9 {
            self.messenger.send("Warning: You've used up over 75% of your quota!");
        } else if percentage_of_max >= 0.9 && percentage_of_max < 1.0 {
            self.messenger.send("Urgent warning: You've used up over 90% of your quota!");
        } else if percentage_of_max >= 1.0 {
            self.messenger.send("Error: You are over your quota!");
        }
    }
}
```

```rust
pub trait Mensageiro {
    fn enviar(&self, msg: &str);
}

pub struct AvisaLimite<'a, T: 'a + Mensageiro> {
    mensageiro: &'a T,
    valor: usize,
    max: usize,
}

impl<'a, T> AvisaLimite<'a, T>
    where T: Mensageiro {
    pub fn new(mensageiro: &T, max: usize) -> AvisaLimite<T> {
        AvisaLimite {
            mensageiro,
            valor: 0,
            max,
        }
    }

    pub fn set_valor(&mut self, valor: usize) {
        self.valor = valor;

        let porcentagem_do_max = self.valor as f64 / self.max as f64;

        if porcentagem_do_max >= 0.75 && porcentagem_do_max < 0.9 {
            self.mensageiro.enviar("Aviso: Você usou mais de 75% da sua cota!");
        } else if porcentagem_do_max >= 0.9 && porcentagem_do_max < 1.0 {
            self.mensageiro.enviar("Aviso urgente: Você usou mais de 90% da sua cota!");
        } else if porcentagem_do_max >= 1.0 {
            self.mensageiro.enviar("Erro: Você excedeu sua cota!");
        }
    }
}
```

<span class="caption">Listing 15-20: A library to keep track of how close to a
maximum value a value is and warn when the value is at certain levels</span>

<span class="caption">Listagem 15-20: Uma biblioteca para acompanhar quão perto
do máximo um valor está e avisar quando o valor está em certos níveis</span>

One important part of this code is that the `Messenger` trait has one method
called `send` that takes an immutable reference to `self` and text of the
message. This is the interface our mock object needs to have. The other
important part is that we want to test the behavior of the `set_value` method
on the `LimitTracker`. We can change what we pass in for the `value` parameter,
but `set_value` doesn’t return anything for us to make assertions on. We want
to be able to say that if we create a `LimitTracker` with something that
implements the `Messenger` trait and a particular value for `max`, when we pass
different numbers for `value`, the messenger is told to send the appropriate
messages.

Uma parte importante deste código é que a trait `Mensageiro` tem um método
chamado `enviar` que recebe uma referência imutável a `self` e o texto da
mensagem. Esta é a interface que nosso objeto simulado precisa ter. A outra
parte importante é que queremos testar o comportamento do método `set_valor` no
`AvisaLimite`. Podemos mudar o que passamos para o parâmetro `valor`, mas o
`set_valor` não retorna nada sobre o qual possamos fazer asserções. Queremos
poder dizer que se criarmos um `AvisaLimite` com algo que implemente a trait
`Mensageiro` e um valor específico de `max`, quando passarmos diferentes números
para o `valor`, o mensageiro receberá o comando para enviar as mensagens
apropriadas.

We need a mock object that instead of sending an email or text message when we
call `send` will only keep track of the messages it’s told to send. We can
create a new instance of the mock object, create a `LimitTracker` that uses the
mock object, call the `set_value` method on `LimitTracker`, and then check that
the mock object has the messages we expect. Listing 15-21 shows an attempt of
implementing a mock object to do just that but that the borrow checker won’t
allow:

Precisamos de um objeto simulado que, em vez de enviar um email ou mensagem de
texto quando chamarmos `enviar`, irá apenas registrar as mensagens que recebeu
para enviar. Podemos criar uma nova instância do objeto simulado, criar um
`AvisaLimite` que use o objeto simulado, chamar o método `set_valor` no
`AvisaLimite`, e então verificar se o objeto simulado tem as mensagens que
esperamos. A Listagem 15-21 mostra uma tentativa de implementar um objeto
simulado para fazer exatamente isto, mas que o borrow checker não permite:

<span class="filename">Filename: src/lib.rs</span>

<span class="filename">Arquivo: src/lib.rs</span>

```rust
#[cfg(test)]
mod tests {
    use super::*;

    struct MockMessenger {
        sent_messages: Vec<String>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger { sent_messages: vec![] }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        let mock_messenger = MockMessenger::new();
        let mut limit_tracker = LimitTracker::new(&mock_messenger, 100);

        limit_tracker.set_value(80);

        assert_eq!(mock_messenger.sent_messages.len(), 1);
    }
}
```

```rust
#[cfg(test)]
mod tests {
    use super::*;

    struct MensageiroSimulado {
        mensagens_enviadas: Vec<String>,
    }

    impl MensageiroSimulado {
        fn new() -> MensageiroSimulado {
            MensageiroSimulado { mensagens_enviadas: vec![] }
        }
    }

    impl Mensageiro for MensageiroSimulado {
        fn enviar(&self, mensagem: &str) {
            self.mensagens_enviadas.push(String::from(mensagem));
        }
    }

    #[test]
    fn envia_uma_mensagem_de_aviso_de_acima_de_75_porcento() {
        let mensageiro_simulado = MensageiroSimulado::new();
        let mut avisa_limite = AvisaLimite::new(&mensageiro_simulado, 100);

        avisa_limite.set_valor(80);

        assert_eq!(mensageiro_simulado.mensagens_enviadas.len(), 1);
    }
}
```

<span class="caption">Listing 15-21: An attempt to implement a `MockMessenger`
that isn’t allowed by the borrow checker</span>

<span class="caption">Listagem 15-21: Uma tentativa de implementar um
`MensageiroSimulado` que não é permitida pelo borrow checker</span>

This test code defines a `MockMessenger` struct that has a `sent_messages`
field with a `Vec` of `String` values to keep track of the messages it’s told
to send. We also define an associated function `new` to make it convenient to
create new `MockMessenger` values that start with an empty list of messages. We
then implement the `Messenger` trait for `MockMessenger` so we can give a
`MockMessenger` to a `LimitTracker`. In the definition of the `send` method, we
take the message passed in as a parameter and store it in the `MockMessenger`
list of `sent_messages`.

Este código de teste define uma struct `MensageiroSimulado` que tem um campo
`mensagens_enviadas` com um `Vec` de valores `String` para registrar as
mensagens que ele recebe para enviar. Também definimos uma função associada
`new` para facilitar a criação de novos valores `MensageiroSimulado` que começam
com uma lista vazia de mensagens. Então implementamos a trait `Mensageiro` para
o `MensageiroSimulado` para que possamos passar um `MensageiroSimulado` a um
`AvisaLimite`. Na definição do método `enviar`, nós pegamos a mensagem passada
como parâmetro e a armazenamos na lista `mensagens_enviadas` do
`MensageiroSimulado`.

In the test, we’re testing what happens when the `LimitTracker` is told to set
`value` to something that is more than 75 percent of the `max` value. First, we
create a new `MockMessenger`, which will start with an empty list of messages.
Then we create a new `LimitTracker` and give it a reference to the new
`MockMessenger` and a `max` value of 100. We call the `set_value` method on the
`LimitTracker` with a value of 80, which is more than 75 percent of 100. Then
we assert that the list of messages that the `MockMessenger` is keeping track
of should now have one message in it.

No teste, estamos testando o que acontece quando o `AvisaLimite` recebe o
comando para setar o `valor` para algo que é mais do que 75 porcento do valor
`max`. Primeiro, criamos um novo `MensageiroSimulado`, que irá começar com uma
lista vazia de mensagens. Então criamos um novo `AvisaLimite` e lhe damos uma
referência ao novo `MensageiroSimulado` e um valor `max` de 100. Nós chamamos o
método `set_valor` no `AvisaLimite` com um valor de 80, que é mais do que 75
porcento de 100. Então conferimos se a lista de mensagens que o
`MensageiroSimulado` está registrando agora tem uma mensagem nela.

However, there’s one problem with this test, as shown here:

Entretanto, há um problema neste teste, conforme abaixo:

```text
error[E0596]: cannot borrow immutable field `self.sent_messages` as mutable
  --> src/lib.rs:52:13
   |
51 |         fn send(&self, message: &str) {
   |                 ----- use `&mut self` here to make mutable
52 |             self.sent_messages.push(String::from(message));
   |             ^^^^^^^^^^^^^^^^^^ cannot mutably borrow immutable field
```

```text
erro[E0596]: não posso pegar emprestado o campo imutável
             `self.mensagens_enviadas` como mutável
  --> src/lib.rs:52:13
   |
51 |         fn send(&self, message: &str) {
   |                 ----- use `&mut self` aqui para torná-lo mutável
52 |             self.sent_messages.push(String::from(message));
   |             ^^^^^^^^^^^^^^^^^^ não posso pegar emprestado mutavelmente um
                                    campo imutável
```

We can’t modify the `MockMessenger` to keep track of the messages because the
`send` method takes an immutable reference to `self`. We also can’t take the
suggestion from the error text to use `&mut self` instead because then the
signature of `send` wouldn’t match the signature in the `Messenger` trait
definition (feel free to try and see what error message you get).

Não podemos modificar o `MensageiroSimulado` para registrar as mensagens porque
o método `enviar` recebe uma referência imutável a `self`. Também não podemos
seguir a sugestão do texto de erro e usar `&mut self` em vez disso porque a
assinatura de `enviar` não corresponderia à assinatura na definição da trait
`Mensageiro` (fique à vontade para tentar e ver qual mensagem de erro você
recebe).

This is a situation in which interior mutability can help! We’ll store the
`sent_messages` within a `RefCell<T>`, and then the `send` message will be
able to modify `sent_messages` to store the messages we’ve seen. Listing 15-22
shows what that looks like:

Esta é uma situação em que a mutabilidade interior pode ajudar! Vamos armazenas
as `mensagens_enviadas` dentro de um `RefCell<T>`, e então o método `enviar`
poderá modificar `mensagens_enviadas` para armazenar as mensagens que já vimos.
A Listagem 15-22 mostra como fica isto:

<span class="filename">Filename: src/lib.rs</span>

<span class="filename">Arquivo: src/lib.rs</span>

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use std::cell::RefCell;

    struct MockMessenger {
        sent_messages: RefCell<Vec<String>>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger { sent_messages: RefCell::new(vec![]) }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.borrow_mut().push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        // --snip--
#         let mock_messenger = MockMessenger::new();
#         let mut limit_tracker = LimitTracker::new(&mock_messenger, 100);
#         limit_tracker.set_value(75);

        assert_eq!(mock_messenger.sent_messages.borrow().len(), 1);
    }
}
```

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use std::cell::RefCell;

    struct MensageiroSimulado {
        mensagens_enviadas: RefCell<Vec<String>>,
    }

    impl MensageiroSimulado {
        fn new() -> MensageiroSimulado {
            MensageiroSimulado { mensagens_enviadas: RefCell::new(vec![]) }
        }
    }

    impl Mensageiro for MensageiroSimulado {
        fn enviar(&self, mensagem: &str) {
            self.mensagens_enviadas.borrow_mut().push(String::from(mensagem));
        }
    }

    #[test]
    fn envia_uma_mensagem_de_aviso_de_acima_de_75_porcento() {
        // --snip--
#         let mensageiro_simulado = MensageiroSimulado::new();
#         let mut avisa_limite = AvisaLimite::new(&mensageiro_simulado, 100);
#         avisa_limite.set_valor(75);

        assert_eq!(mensageiro_simulado.mensagens_enviadas.borrow().len(), 1);
    }
}
```
<span class="caption">Listing 15-22: Using `RefCell<T>` to mutate an inner
value while the outer value is considered immutable</span>

<span class="caption">Listagem 15-22: Usando `RefCell<T>` para modificar um
valor interno enquanto o valor externo é considerado imutável</span>

The `sent_messages` field is now of type `RefCell<Vec<String>>` instead of
`Vec<String>`. In the `new` function, we create a new `RefCell<Vec<String>>`
instance around the empty vector.

O campo `mensagens_enviadas` agora é do tipo `RefCell<Vec<String>>` em vez de
`Vec<String>`. Na função `new`, nós criamos uma nova instância de
`RefCell<Vec<String>>` em torno do vetor vazio.

For the implementation of the `send` method, the first parameter is still an
immutable borrow of `self`, which matches the trait definition. We call
`borrow_mut` on the `RefCell<Vec<String>>` in `self.sent_messages` to get a
mutable reference to the value inside the `RefCell<Vec<String>>`, which is
the vector. Then we can call `push` on the mutable reference to the vector to
keep track of the messages sent during the test.

Para a implementação do método `enviar`, o primeiro parâmetro ainda é um
empréstimo imutável de `self`, que corresponde à definição da trait. Nós
chamamos `borrow_mut` no `RefCell<Vec<String>>` em `self.mensagens_enviadas`
para obter uma referência mutável ao valor dentro do `RefCell<Vec<String>>`, que
é o vetor. Então podemos chamar `push` na referência mutável ao vetor para
registrar as mensagens enviadas durante o teste.

The last change we have to make is in the assertion: to see how many items are
in the inner vector, we call `borrow` on the `RefCell<Vec<String>>` to get an
immutable reference to the vector.

A última mudança que temos que fazer é na asserção: para ver quantos itens estão
no vetor interno, chamamos `borrow` no `RefCell<Vec<String>>` para obter uma
referência imutável ao vetor.

Now that you’ve seen how to use `RefCell<T>`, let’s dig into how it works!

Agora que você viu como usar o `RefCell<T>`, vamos nos aprofundar em como ele
funciona!

#### `RefCell<T>` Keeps Track of Borrows at Runtime

#### O `RefCell<T>` Registra Empréstimos em Tempo de Execução

When creating immutable and mutable references, we use the `&` and `&mut`
syntax, respectively. With `RefCell<T>`, we use the `borrow` and `borrow_mut`
methods, which are part of the safe API that belongs to `RefCell<T>`. The
`borrow` method returns the smart pointer type `Ref<T>`, and `borrow_mut`
returns the smart pointer type `RefMut<T>`. Both types implement `Deref` so
we can treat them like regular references.

Quando estamos criando referências imutáveis e mutáveis, usamos as sintaxes `&`
e `&mut`, respectivamente. Com o `RefCell<T>`, usamos os métodos `borrow` e
`borrow_mut`, que são parte da API safe que pertence ao `RefCell<T>`. O método
`borrow` retorna o ponteiro inteligente `Ref<T>`, e o `borrow_mut` retorna o
ponteiro inteligente `RefMut<T>`. Ambos os tipos implementam `Deref`, então
podemos tratá-los como referências normais.

The `RefCell<T>` keeps track of how many `Ref<T>` and `RefMut<T>` smart
pointers are currently active. Every time we call `borrow`, the `RefCell<T>`
increases its count of how many immutable borrows are active. When a `Ref<T>`
value goes out of scope, the count of immutable borrows goes down by one. Just
like the compile time borrowing rules, `RefCell<T>` lets us have many immutable
borrows or one mutable borrow at any point in time.

O tipo `RefCell<T>` mantém registro de quantos ponteiros inteligentes `Ref<T>` e
`RefMut<T>` estão atualmente ativos. Cada vez que chamamos `borrow`, o
`RefCell<T>` aumenta seu contador de quantos empréstimos imutáveis estão ativos.
Quando um valor `Ref<T>` sai de escopo, o contador de empréstimos imutáveis
diminui em um. Assim como as regras de empréstimo em tempo de compilação, o
`RefCell<T>` nos permite ter vários empréstimos imutáveis ou um empréstimo
mutável em um dado momento.

If we try to violate these rules, rather than getting a compiler error like we
would with references, the implementation of `RefCell<T>` will `panic!` at
runtime. Listing 15-23 shows a modification of the implementation of `send` in
Listing 15-22. We’re deliberately trying to create two mutable borrows active
for the same scope to illustrate that `RefCell<T>` prevents us from doing this
at runtime:

Se tentarmos violar estas regras, em vez de receber um erro do compilador como
iríamos com referências, a implementação de `RefCell<T>` chamará `panic!` em
tempo de execução. A Listagem 15-23 mostra uma modificação da implementação do
`enviar` da Listagem 15-22. Estamos deliberadamente tentando criar dois
empréstimos mutáveis ativos para o mesmo escopo para ilustrar que o `RefCell<T>`
nos impede de fazer isto em tempo de execução:

<span class="filename">Filename: src/lib.rs</span>

<span class="filename">Arquivo: src/lib.rs</span>

```rust,ignore
impl Messenger for MockMessenger {
    fn send(&self, message: &str) {
        let mut one_borrow = self.sent_messages.borrow_mut();
        let mut two_borrow = self.sent_messages.borrow_mut();

        one_borrow.push(String::from(message));
        two_borrow.push(String::from(message));
    }
}
```

```rust,ignore
impl Mensageiro for MensageiroSimulado {
    fn enviar(&self, mensagem: &str) {
        let mut emprestimo_um = self.mensagens_enviadas.borrow_mut();
        let mut emprestimo_dois = self.mensagens_enviadas.borrow_mut();

        emprestimo_um.push(String::from(mensagem));
        emprestimo_dois.push(String::from(mensagem));
    }
}
```

<span class="caption">Listing 15-23: Creating two mutable references in the
same scope to see that `RefCell<T>` will panic</span>

<span class="caption">Listagem 15-23: Criando duas referências mutáveis no mesmo
escopo para ver que o `RefCell<T>` irá "entrar em pânico" (i.e., executar
`panic!`)</span>

We create a variable `one_borrow` for the `RefMut<T>` smart pointer returned
from `borrow_mut`. Then we create another mutable borrow in the same way in the
variable `two_borrow`. This makes two mutable references in the same scope,
which isn’t allowed. When we run the tests for our library, the code in Listing
15-23 will compile without any errors, but the test will fail:

Nós criamos uma variável `emprestimo_um` para o ponteiro inteligente `RefMut<T>`
retornado por `borrow_mut`. Então criamos outro empréstimo mutável da mesma
forma na variável `emprestimo_dois`. Isto resulta em duas referências mutáveis
no mesmo escopo, o que não é permitido. Quando rodarmos os testes para nossa
biblioteca, o código na Listagem 15-23 irá compilar sem nenhum erro, mas o teste
irá falhar:

```text
---- tests::it_sends_an_over_75_percent_warning_message stdout ----
	thread 'tests::it_sends_an_over_75_percent_warning_message' panicked at
    'already borrowed: BorrowMutError', src/libcore/result.rs:906:4
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

```text
---- tests::envia_uma_mensagem_de_aviso_de_acima_de_75_porcento stdout ----
	thread 'tests::envia_uma_mensagem_de_aviso_de_acima_de_75_porcento' entrou
    em pânico em
    'já emprestado: BorrowMutError', src/libcore/result.rs:906:4
nota: Rode com `RUST_BACKTRACE=1` para um backtrace.
```

Notice that the code panicked with the message `already borrowed:
BorrowMutError`. This is how `RefCell<T>` handles violations of the borrowing
rules at runtime.

Note como o código entrou em pânico com a mensagem `já emprestado:
BorrowMutError`. É assim que o `RefCell<T>` lida com violações das regras de
empréstimo em tempo de execução.

Catching borrowing errors at runtime rather than compile time means that we
would find a mistake in our code later in the development process and possibly
not even until our code was deployed to production. Also, our code will incur a
small runtime performance penalty as a result of keeping track of the borrows
at runtime rather than compile time. However, using `RefCell<T>` makes it
possible for us to write a mock object that can modify itself to keep track of
the messages it has seen while we’re using it in a context where only immutable
values are allowed. We can use `RefCell<T>` despite its trade-offs to get more
functionality than regular references give us.

Pegar erros de empréstimo em tempo de execução em vez de em tempo de compilação
significa encontrar defeitos no nosso código mais tarde no processo de
desenvolvimento, e possivelmente nem mesmo até que nosso código já tenha sido
implantado em produção. Além disso, nosso código irá incorrer em uma pequena
penalidade de desempenho de execução como resultado de manter registro dos
empréstimos em tempo de execução em vez de compilação. Ainda assim, usar o
`RefCell<T>` nos torna possível escrever um objeto simulado que pode se
modificar para registrar as mensagens que ele já viu enquanto o usamos em um
contexto onde apenas valores imutáveis são permitidos. Podemos usar o
`RefCell<T>`, apesar de seus trade-offs, para obter mais funcionalidade do que
referências regulares nos dão.

### Having Multiple Owners of Mutable Data by Combining `Rc<T>` and `RefCell<T>`

### Conseguindo Múltiplos Possuidores de Dados Mutáveis pela Combinação de `Rc<T>` e `RefCell<T>`

A common way to use `RefCell<T>` is in combination with `Rc<T>`. Recall that
`Rc<T>` lets us have multiple owners of some data, but it only gives us
immutable access to that data. If we have an `Rc<T>` that holds a `RefCell<T>`,
we can get a value that can have multiple owners *and* that we can mutate!

Um jeito comum de usar o `RefCell<T>` é em combinação com o `Rc<T>`. Lembre-se
de que o `Rc<T>` nos permite ter múltiplos possuidores de algum dado, mas ele só
nos permite acesso imutável a esse dado. Se temos um `Rc<T>` que contém um
`RefCell<T>`, podemos ter um valor que pode ter múltiplos possuidores *e* que
podemos modificar!

For example, recall the cons list example in Listing 15-18 where we used
`Rc<T>` to let us have multiple lists share ownership of another list. Because
`Rc<T>` only holds immutable values, we can’t change any of the values in the
list once we’ve created them. Let’s add in `RefCell<T>` to gain the ability to
change the values in the lists. Listing 15-24 shows that by using a
`RefCell<T>` in the `Cons` definition, we can modify the value stored in all
the lists:

Por exemplo, lembre-se da cons list na Listagem 15-18 onde usamos o `Rc<T>` para
nos permitir que múltiplas listas compartilhassem posse de outra lista. Como o
`Rc<T>` guarda apenas valores imutáveis, nós não podemos modificar nenhum dos
valores na lista uma vez que os criamos. Vamos adicionar o `RefCell<T>` para
ganhar a habilidade de mudar os valores nas listas. A Listagem 15-24 mostra que,
usando um `RefCell<T>` na definição do `Cons`, podemos modificar o valor
armazenado em todas as listas:

<span class="filename">Filename: src/main.rs</span>

<span class="filename">Arquivo: src/main.rs</span>

```rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));

    let b = Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

```rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let valor = Rc::new(RefCell::new(5));

    let a = Rc::new(Cons(Rc::clone(&valor), Rc::new(Nil)));

    let b = Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *valor.borrow_mut() += 10;

    println!("a depois = {:?}", a);
    println!("b depois = {:?}", b);
    println!("c depois = {:?}", c);
}
```

<span class="caption">Listing 15-24: Using `Rc<RefCell<i32>>` to create a
`List` that we can mutate</span>

<span class="caption">Listagem 15-24: Usando `Rc<RefCell<i32>>` para criar uma
`List` que podemos modificar</span>

We create a value that is an instance of `Rc<RefCell<i32>` and store it in a
variable named `value` so we can access it directly later. Then we create a
`List` in `a` with a `Cons` variant that holds `value`. We need to clone
`value` so both `a` and `value` have ownership of the inner `5` value rather
than transferring ownership from `value` to `a` or having `a` borrow from
`value`.

Nós criamos um valor que é uma instância de `Rc<RefCell<i32>>` e o armazenamos
em uma variável chamada `valor` para que possamos acessá-lo diretamente mais
tarde. Então criamos uma `List` em `a` com uma variante `Cons` que guarda
`valor`.

We wrap the list `a` in an `Rc<T>` so when we create lists `b` and `c`, they
can both refer to `a`, which is what we did in Listing 15-18.

Nós embrulhamos a lista `a` em um `Rc<T>` para que, quando criarmos as listas
`b` e `c`, elas possam ambas se referir a `a`, que é o que fizemos na Listagem
15-18.

After we’ve created the lists in `a`, `b`, and `c`, we add 10 to the value in
`value`. We do this by calling `borrow_mut` on `value`, which uses the
automatic dereferencing feature we discussed in Chapter 5 (see the section
“Where’s the `->` Operator?”) to dereference the `Rc<T>` to the inner
`RefCell<T>` value. The `borrow_mut` method returns a `RefMut<T>` smart
pointer, and we use the dereference operator on it and change the inner value.

Depois de criarmos as listas em `a`, `b` e `c`, adicionamos 10 ao valor em
`valor`. Fazemos isto chamando `borrow_mut` em `valor`, o que usa a
funcionalidade de desreferência automática que discutimos no Capítulo 5 (veja a
seção "Onde está o operador `->`?") para desreferenciar o `Rc<T>` ao valor
interno `RefCell<T>`. O método `borrow_mut` retorna um ponteiro inteligente
`RefMut<T>` no qual usamos o operador de desreferência e modificamos o valor
interno.

When we print `a`, `b`, and `c`, we can see that they all have the modified
value of 15 rather than 5:

Quando imprimimos `a`, `b` e `c`, podemos ver que todos eles têm o valor
modificado de 15 em vez de 5:

```text
a after = Cons(RefCell { value: 15 }, Nil)
b after = Cons(RefCell { value: 6 }, Cons(RefCell { value: 15 }, Nil))
c after = Cons(RefCell { value: 10 }, Cons(RefCell { value: 15 }, Nil))
```

```text
a depois = Cons(RefCell { value: 15 }, Nil)
b depois = Cons(RefCell { value: 6 }, Cons(RefCell { value: 15 }, Nil))
c depois = Cons(RefCell { value: 10 }, Cons(RefCell { value: 15 }, Nil))
```

This technique is pretty neat! By using `RefCell<T>`, we have an outwardly
immutable `List`. But we can use the methods on `RefCell<T>` that provide
access to its interior mutability so we can modify our data when we need to.
The runtime checks of the borrowing rules protect us from data races, and it’s
sometimes worth trading a bit of speed for this flexibility in our data
structures.

Esta técnica é bem bacana! Usando um `RefCell<T>`, temos uma `List`
exteriormente imutável. Mas podemos usar os métodos no `RefCell<T>` que dão
acesso a sua mutabilidade interior para que possamos modificar nossos dados
quando precisarmos. As checagens em tempo de execução das regras de empréstimo
nos protegem de corridas de dados, e às vezes vale a pena trocar um pouco de
velocidade por esta flexibilidade nas nossas estruturas de dados.

The standard library has other types that provide interior mutability, such as
`Cell<T>`, which is similar except that instead of giving references to the
inner value, the value is copied in and out of the `Cell<T>`. There’s also
`Mutex<T>`, which offers interior mutability that’s safe to use across threads;
we’ll discuss its use in Chapter 16. Check out the standard library docs for
more details on the differences between these types.

A biblioteca padrão tem outros tipos que proveem mutabilidade interior, como o
`Cell<T>`, que é parecido, exceto que em vez de dar referências ao valor
interno, o valor é copiado para dentro e para fora do `Cell<T>`. Tem também o
`Mutex<T>`, que oferece mutabilidade interior que é segura de usar entre
threads; vamos discutir seu uso no Capítulo 16. Confira a documentação da
biblioteca padrão para mais detalhes sobre as diferenças entre estes tipos.
