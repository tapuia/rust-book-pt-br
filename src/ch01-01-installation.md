## Installation
## Instalação

The first step to using Rust is to install it. You’ll need an internet
connection to run the commands in this chapter, as we’ll be downloading Rust
from the internet.

O primeiro passo para se usar a linguagem Rust é instalá-la. Você vai precisar
de uma conexão com a internet para executar os comandos contidos neste
capítulo, pois vamos baixar o Rust da internet.

We’ll be showing off a number of commands using a terminal, and those lines all
start with `$`. You don’t need to type in the `$` character; they are there to indicate
the start of each command. You’ll see many tutorials and examples around the web
that follow this convention: `$` for commands run as a regular user, and `#`
for commands you should be running as an administrator. Lines that don’t start
with `$` are typically showing the output of the previous command.

Iremos mostrar diversos comandos usando o terminal, e todas as linhas que se
referem ao terminal iniciam com `$`. Você não precisa digitar o caractere `$`,
ele serve apenas para indicar o início de cada comando. Você vai notar que
muitos tutorias disponíveis por aí seguem esta convenção: `$` para comandos que
são executados como um usuário normal, e `#` para comandos que você deve
executar como administrador. As linhas que não iniciam com o caractere `$`
mostram o _output_ (saída) do comando anterior.


### Installing on Linux or Mac
### Instalando no Linux ou Mac

If you’re on Linux or a Mac, all you need to do is open a terminal and type
this:

Se você está em um ambiente Linux ou Mac, tudo o que você precisa é abrir um
terminal e digitar o seguinte comando:

```text
$ curl https://sh.rustup.rs -sSf | sh
```

This will download a script and start the installation. You may be prompted for
your password. If it all goes well, you’ll see this appear:

Este comando vai baixar um script e iniciar a instalação. Talvez seja solicitado
que você digite sua senha. Se tudo ocorrer bem, a mensagem abaixo vai aparecer:

```text
Rust is installed now. Great!
```

Of course, if you disapprove of the `curl | sh` pattern, you can download, inspect
and run the script however you like.

Claro, se você não aprova o uso do `curl | sh`, você pode baixar o _script_,
inspecioná-lo e executá-lo da maneira que achar melhor.

The installation script automatically adds Rust to your system PATH after your next login. 
If you want to start using Rust right away, run the following command in your shell:

O _script_ de instalação já adiciona automaticamente o Rust à variável PATH do
seu sistema logo após o seu próximo _login_. Se você quiser usar o Rust
imediatamente, execute o seguinte comando no seu terminal:

```text
$ source $HOME/.cargo/env
```

Alternatively, add the following line to your `~/.bash_profile`:

Outra opção é adicionar a linha abaixo no seu `~/.bash_profile`:

```text
$ export PATH="$HOME/.cargo/bin:$PATH"
```

### Installing on Windows
### Instalando no Windows

On Windows, go to [https://rustup.rs](https://rustup.rs/)<!-- ignore --> and
follow the instructions to download rustup-init.exe. Run that and follow the
rest of the instructions it gives you.

No Windows, visite o _site_
[https://rustup.rs](https://rustup.rs/)<!-- ignore --> e siga as instruções para
baixar o arquivo rustup-init.exe. Execute este arquivo e siga as demais
instruções que aparecerem na sua tela.

The rest of the Windows-specific commands in the book will assume that you are
using `cmd` as your shell. If you use a different shell, you may be able to run
the same commands that Linux and Mac users do. If neither work, consult the
documentation for the shell you are using.

O restante dos comandos específicos do Windows neste livro partem da premissa de
que você está utilizando o `cmd` como o seu _shell_. Se você está usando um
_shell_ diferente, talvez você poderá usar os mesmos comandos que os usuários de
Linux ou Mac usam. Se algum comando não funcionar, consulte a documentação
referente ao _shell_ que você está utilizando.

### Custom installations
### Instalação customizada

If you have reasons for preferring not to use rustup.rs, please see [the Rust
installation page](https://www.rust-lang.org/install.html) for other options.

Se, por alguma razão, você preferir não usar o rustup.rs, consulte [a página de
instalação do Rust](https://www.rust-lang.org/pt-BR/install.html) para outras
opções de instalação.

### Updating
### Atualizando o Rust

Once you have Rust installed, updating to the latest version is easy.
From your shell, run the update script:

Tendo o Rust instalado na sua máquina, atualizar para a última versão é fácil.
Do seu _shell_, execute o _script_ de atualização:

```text
$ rustup update
```

### Uninstalling
### Desinstalando

Uninstalling Rust is as easy as installing it. From your shell, run
the uninstall script:

Desinstalar Rust é tão fácil quanto instalá-lo. Do seu _shell_, execute o
_script_ de desinstalação:

```text
$ rustup self uninstall
```

### Troubleshooting
### Solução de Problemas

If you’ve got Rust installed, you can open up a shell, and type this:

Após Rust ser instalado em sua máquina, você pode abrir o seu _shell_, e digitar
a linha abaixo:

```text
$ rustc --version
```

You should see the version number, commit hash, and commit date in a format
similar to this for the latest stable version at the time you install:

Você deverá ver a versão, o _hash_ e a data do _commit_ em um formato
similar ao seguinte, indicando a última estável mais recente no momento da
instalação:

```text
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

If you see this, Rust has been installed successfully!
Congrats!

Se aparecer a mensagem acima, Rust foi instalado com sucesso!
Parabéns!

If you don’t and you’re on Windows, check that Rust is in your `%PATH%` system
variable.

Se não aparecer a mensagem acima e você está em um ambiente Windows, verifique
se o Rust aparece na variável `%PATH%` do seu sistema.

If it still isn’t working, there are a number of places where you can get help.
The easiest is [the #rust IRC channel on irc.mozilla.org][irc]<!-- ignore -->,
which you can access through [Mibbit][mibbit]. Go to that address, and you’ll
be chatting with other Rustaceans (a silly nickname we call ourselves) who can
help you out. Other great resources include [the Users forum][users] and
[Stack Overflow][stackoverflow].

Se mesmo assim não funcionar, existem vários lugares onde você pode pedir ajuda.
A maneira mais fácil é pedir ajuda no
[canal IRC #rust do irc.mozilla.org][irc]<!-- ignore -->, que você pode acessar
via [Mibbit][mibbit]. Você vai falar com vários outros _Rustaceans_ (um apelido
bobo que usamos entre nós) que podem ajudá-lo em suas dúvidas. Você também pode
buscar ajuda no [fórum do Rust][users] e no [Stack Overflow][stackoverflow].

[irc]: irc://irc.mozilla.org/#rust
[mibbit]: http://chat.mibbit.com/?server=irc.mozilla.org&channel=%23rust
[users]: https://users.rust-lang.org/
[stackoverflow]: http://stackoverflow.com/questions/tagged/rust

### Local documentation
### Documentação local

The installer also includes a copy of the documentation locally, so you can
read it offline. Run `rustup doc` to open the local documentation in your
browser.

O instalador também inclui uma cópia local da documentação para que você possa
acessá-la _offline_. Execute o comando `rustup doc` para abrir a documentação
local no seu navegador.

Any time there’s a type or function provided by the standard library and you’re
not sure what it does, use the API documentation to find out!

Sempre que você se deparar com um tipo ou função fornecido pela biblioteca
padrão que você não tem certeza do que ele faz, use a documentação da API para
descobrir!
