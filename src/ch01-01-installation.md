## Instalação

O primeiro passo para se usar a linguagem Rust é instalá-la. Você vai precisar
de uma conexão com a internet para executar os comandos contidos neste
capítulo, pois vamos baixar o Rust da internet.

Iremos mostrar diversos comandos usando o terminal, e todas as linhas que se
referem ao terminal iniciam com `$`. Você não precisa digitar o caractere `$`,
ele serve apenas para indicar o início de cada comando. Você vai notar que
muitos tutorias disponíveis por aí seguem esta convenção: `$` para comandos que
são executados como um usuário normal, e `#` para comandos que você deve
executar como administrador. As linhas que não iniciam com o caractere `$`
mostram o _output_ (saída) do comando anterior.

### Instalando no Linux ou Mac

Se você está em um ambiente Linux ou Mac, tudo o que você precisa é abrir um
terminal e digitar o seguinte comando:

```text
$ curl https://sh.rustup.rs -sSf | sh
```

Este comando vai baixar um script e iniciar a instalação. Talvez seja solicitado
que você digite sua senha. Se tudo ocorrer bem, a mensagem abaixo vai aparecer:

```text
Rust is installed now. Great!
```

Claro, se você não aprova o uso do `curl | sh`, você pode baixar o _script_,
inspecioná-lo e executá-lo da maneira que achar melhor.

O _script_ de instalação já adiciona automaticamente o Rust à variável PATH do
seu sistema logo após o seu próximo _login_. Se você quiser usar o Rust
imediatamente, execute o seguinte comando no seu terminal:

```text
$ source $HOME/.cargo/env
```

Outra opção é adicionar a linha abaixo no seu `~/.bash_profile`:

```text
$ export PATH="$HOME/.cargo/bin:$PATH"
```

### Instalando no Windows

No Windows, visite o _site_
[https://rustup.rs](https://rustup.rs/)<!-- ignore --> e siga as instruções para
baixar o arquivo rustup-init.exe. Execute este arquivo e siga as demais
instruções que aparecerem na sua tela.

O restante dos comandos específicos do Windows neste livro partem da premissa de
que você está utilizando o `cmd` como o seu _shell_. Se você está usando um
_shell_ diferente, talvez você poderá usar os mesmos comandos que os usuários de
Linux ou Mac usam. Se algum comando não funcionar, consulte a documentação
referente ao _shell_ que você está utilizando.

### Instalação customizada

Se, por alguma razão, você preferir não usar o rustup.rs, consulte [a página de
instalação do Rust](https://www.rust-lang.org/pt-BR/install.html) para outras
opções de instalação.

### Atualizando o Rust

Tendo o Rust instalado na sua máquina, atualizar para a última versão é fácil.
Do seu _shell_, execute o _script_ de atualização:

```text
$ rustup update
```

### Desinstalando

Desinstalar Rust é tão fácil quanto instalá-lo. Do seu _shell_, execute o
_script_ de desinstalação:

```text
$ rustup self uninstall
```

### Solução de Problemas

Após Rust ser instalado em sua máquina, você pode abrir o seu _shell_, e digitar
a linha abaixo:

```text
$ rustc --version
```

Você deverá ver a versão, o _hash_ e a data do _commit_ em um formato
similar ao seguinte, indicando a última estável mais recente no momento da
instalação:

```text
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

Se aparecer a mensagem acima, Rust foi instalado com sucesso!
Parabéns!

Se não aparecer a mensagem acima e você está em um ambiente Windows, verifique
se o Rust aparece na variável `%PATH%` do seu sistema.

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

### Documentação local

O instalador também inclui uma cópia local da documentação para que você possa
acessá-la _offline_. Execute o comando `rustup doc` para abrir a documentação
local no seu navegador.

Sempre que você se deparar com um tipo ou função fornecido pela biblioteca
padrão que você não tem certeza do que ele faz, use a documentação da API para
descobrir!
