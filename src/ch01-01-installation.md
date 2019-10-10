## Instalação

O primeiro passo é instalar Rust. Nós vamos baixar Rust através do `rustup`,
uma ferramenta de linha de comando para gerenciar versões de Rust e ferramentas
associadas. Você vai precisar de uma conexão com a internet para executar o
download.

> Nota: se você prefere não usar o `rustup` por algum motivo, por favor veja o
> [a página de instalação de Rust](https://www.rust-lang.org/tools/install)
> para outras opções.

Os próximos passos irão instalar a última versão estável do compilador de Rust.
A garantias de estabilidade de Rust certificam-se de que todos os exemplos
neste livro que compilam vao continuar a compilar com novas versões de Rust.
As saídas podem variar um pouco de versão a versão, porque Rust frequentemente
melhora mensagens de erro e alertas. Em outras palavras, qualquer versão nova,
estável, de Rust qu você instalar usando esses passos devem funcionar como o
esperado com o conteúdo deste livro.

> ### Notação de Linha de Comando
>
> Neste capítulo e ao longo do livro, nós iremos mostrar alguns comandos usados
> no terminal. Linhas que você deve digitar num terminal todas começam com `$`.
> Você não precisa digitar o caráctere `$`. Ele indica o início de cada comando.
> Linhas que não comecem com `$` tipicamente mostram a saída do comando anterior.
> Adicionalmente, exemplos específicos de PowerShell irão utilizar `>` ao invés
> de `$`.

### Instalando `rustup` em Linux ou Mac

Se você utiliza Linux ou Mac, abra um terminal e digite o seguinte comando:

```text
$ curl https://sh.rustup.rs -sSf | sh
```

Este comando baixa um script e inicia a instalação da ferramenta `rustup`, a
qual instala a última versão estável de Rust. Talvez seja solicitado que você
digite sua senha. Se a instalação for bem-sucedida, a seguinte linha irá
aparecer:

```text
Rust is installed now. Great!
```

Se preferir, sinta-se livre para baixar o script e inspecioná-lo antes de
executar.

O script de instalação adiciona automaticamente Rust à variável PATH do
seu ambiente após o seu próximo login. Se você quiser usar Rust imediatamente
ao invés de reiniciar o seu terminal, execute o seguinte comando no seu shell
para adicionar Rust à PATH do seu sistema manualmente:

```text
$ source $HOME/.cargo/env
```

Outra opção é adicionar a linha abaixo ao seu `~/.bash_profile`:

```text
$ export PATH="$HOME/.cargo/bin:$PATH"
```

### Instalando no Windows

No Windows, vá até [https://www.rust-lang.org/tools/install][install] e siga
as instruções para instalar Rust. Em algum ponto da instalação, você receberá
uma mensagem explicando que você também irá precisar de ferramentas de
compilação de C++ para o Visual Studio 2013 ou superior. A forma mais fácil de
adquiri-las é instalando as [Ferramentas de Compilação para o Visual 
Studio 2019][visualstudio]. As ferramentas estão na seção Other Tools and
Frameworks.

[install]: https://www.rust-lang.org/tools/install
[visualstudio]: https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2019

O restante deste livro utiliza comandos que funcionam tanto no *cmd.exe* quanto
no PowerShell. Se existirem diferenças específicas, nós iremos apontar qual 
utilizar.

### Atualizando e desinstalando

Após instalar Rust pelo `rustup`, atualizar para a última versão é fácil.
Do seu seu shell, rode o seguinte script de atualização:

```text
$ rustup update
```

Para desinstalar Rust e o `rustup`, rode o seguinte script de desinstalação do
seu shell:

```text
$ rustup self uninstall
```

### Solução de Problemas

Para checar se você tem Rust instalado corretamente, abra um terminal e digite
esta linha:

```text
$ rustc --version
```

Você deverá ver o número, o hash e a data do commit da última versão estável
lançada no seguinte formato:

```text
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

Se aparecer a mensagem acima, você instalou Rust com sucesso! Se você não ver
esta informação e você estiver no Windows, cheque que Rust está na sua variável
de ambiente `%PATH%`. Se tudo isso estiver correto e Rust ainda não funcionar,
existem vários locais em que você pode pedir ajuda. O mais fácil é o canal
#beginners no [Discord oficial de Rust][discord]. Lá, você pode falar com
outros Rustáceos (um apelido bobo que damos a nós mesmos) que podem te ajudar.
Outros ótimos recursos incluem [o fórum de usários][users] e o [Stack
Overflow][stackoverflow].

[discord]: https://discord.gg/rust-lang
[users]: https://users.rust-lang.org/
[stackoverflow]: http://stackoverflow.com/questions/tagged/rust

### Documentação local

O instalador também inclui uma cópia local da documentação para que você possa
acessá-la offline. Execute o comando `rustup doc` para abrir a documentação no
seu navegador.

Sempre que um tipo ou for fornecido pela biblioteca padrão e você não tiver
certeza do que ele faz ou de como usá-lo, use a documentação da API para
descobrir!
