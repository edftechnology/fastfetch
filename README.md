# Como instalar/configurar/usar o `fastfetch` no Linux Ubuntu

## Resumo

Guia para instalar, verificar, configurar, usar e remover o `fastfetch` no `Linux Ubuntu`.

## _Abstract_

_A guide to install, verify, configure, use, and remove `fastfetch` on `Ubuntu Linux`._

## Descrição

O `fastfetch` coleta informações do sistema e as apresenta no `Terminal Emulator`. O projeto é escrito principalmente em `C`. Este guia baixa o pacote oficial `.deb` do projeto `fastfetch-cli/fastfetch` com `curl` e o instala globalmente usando `apt`.

## Pré-requisitos

- `Linux Ubuntu` compatível com os pacotes oficiais (Ubuntu 20.04 ou superior, conforme o projeto).
- Arquitetura `amd64` ou `arm64`; conexão com a internet.
- `curl`, `apt`, `dpkg` e `ca-certificates` disponíveis.
- `sudo` para instalar e remover o pacote.
- Identificar qualquer `fastfetch` preexistente antes de instalar; interromper se sua origem não for conhecida.

## 1. Instalação

O procedimento usa os pacotes `.deb` oficiais do projeto e instala com `apt`, que resolve dependências e mantém os arquivos sob gerenciamento do sistema. O endereço de _download_ `latest` não fixa uma versão; conferir a versão e os ativos na página oficial de lançamentos.

1. Abrir o `Terminal Emulator`. Atalho de teclado:

    ```bash
    Ctrl + Alt + T
    ```

2. Certifique-se de que seu sistema esteja limpo e atualizado.

    2.1 Limpar o `cache` do gerenciador de pacotes `apt`. Especificamente, ele remove todos os arquivos de pacotes (`.deb`) baixados pelo `apt` e armazenados em `/var/cache/apt/archives/`. Digite o seguinte comando:
    ```bash
    sudo apt clean
    ```

    2.2 Remover pacotes `.deb` antigos ou duplicados do `cache` local. É útil para liberar espaço, pois remove apenas os pacotes que não podem mais ser baixados (ou seja, versões antigas de pacotes que foram atualizados). Digite o seguinte comando:
    ```bash
    sudo apt autoclean
    ```

    2.3 Remover pacotes que foram automaticamente instalados para satisfazer as dependências de outros pacotes e que não são mais necessários. Digite o seguinte comando:
    ```bash
    sudo apt autoremove -y
    ```

    2.4 Buscar as atualizações disponíveis para os pacotes que estão instalados em seu sistema. Digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt update
    ```

    2.5 **Corrigir pacotes quebrados**: Isso atualizará a lista de pacotes disponíveis e tentará corrigir pacotes quebrados ou com dependências ausentes:
    ```bash
    sudo apt --fix-broken install
    ```

    2.6 Limpar o `cache` do gerenciador de pacotes `apt` novamente:
    ```bash
    sudo apt clean
    ```

    2.7 Para ver a lista de pacotes a serem atualizados, digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt list --upgradable
    ```

    2.8 Realmente atualizar os pacotes instalados para as suas versões mais recentes, com base na última vez que você executou `sudo apt update`. Digite o seguinte comando e pressione `Enter`:
    ```bash
    sudo apt full-upgrade -y
    ```



Os comandos de manutenção do item 2 foram preservados do template. Eles não são pré-requisitos do `fastfetch`; `apt autoremove -y`, `apt --fix-broken install` e `apt full-upgrade -y` podem remover, reparar ou atualizar pacotes do sistema. Revisar cada ação e seus efeitos antes de executá-la.

3. Verificar a distribuição, arquitetura e instalação existente. Se já houver um comando, identificar sua origem com `type -a` e `dpkg-query -S`; resolver conflitos antes de instalar.

    ```bash
    . /etc/os-release
    printf '%s %s\n' "$NAME" "$VERSION_ID"
    dpkg --print-architecture
    command -v fastfetch || true
    type -a fastfetch 2>/dev/null || true
    if command -v fastfetch >/dev/null 2>&1; then
        dpkg-query -S "$(readlink -f "$(command -v fastfetch)")" || true
    fi
    ```

4. Selecionar o ativo conforme a arquitetura (`amd64` para `x86_64`, `arm64` para `aarch64`):

    ```bash
    case "$(dpkg --print-architecture)" in
        amd64) asset=fastfetch-linux-amd64.deb ;;
        arm64) asset=fastfetch-linux-aarch64.deb ;;
        *) printf 'Arquitetura não coberta.\n' >&2; exit 1 ;;
    esac
    ```

5. Criar um diretório temporário exclusivo e baixar o ativo oficial com `curl`:

    ```bash
    download_dir="$(mktemp -d)"
    curl -fL --output "$download_dir/$asset" \
        "https://github.com/fastfetch-cli/fastfetch/releases/latest/download/$asset"
    ```

6. Instalar o arquivo `.deb` com `apt`. Isso requer `sudo`; o download não requer privilégios. Os arquivos ficam nos diretórios administrados pelo `dpkg`/`apt`, sem cópia manual para `/usr/bin` ou `/usr/local/bin`:

    ```bash
    sudo apt install "$download_dir/$asset"
    ```

7. Remover o arquivo baixado e o diretório temporário após a instalação bem-sucedida e confirmar a versão:

    ```bash
    rm -- "$download_dir/$asset"
    rmdir -- "$download_dir"
    fastfetch --version
    ```

### 1.1 Código completo para configurar/instalar/usar

1. Abrir o `Terminal Emulator`. Atalho de teclado:

    ```bash
    Ctrl + Alt + T
    ```

2. Copiar e colar o bloco no terminal. O bloco seleciona a arquitetura suportada, baixa o pacote oficial para um diretório temporário exclusivo, instala-o e mostra a versão. Antes, identificar a origem de qualquer instalação preexistente.

    ```bash
    set -eu
    download_dir="$(mktemp -d)"
    trap 'rm -rf -- "$download_dir"' EXIT
    case "$(dpkg --print-architecture)" in
        amd64) asset=fastfetch-linux-amd64.deb ;;
        arm64) asset=fastfetch-linux-aarch64.deb ;;
        *) printf 'Arquitetura não coberta.\n' >&2; exit 1 ;;
    esac
    curl -fL --output "$download_dir/$asset" \
        "https://github.com/fastfetch-cli/fastfetch/releases/latest/download/$asset"
    sudo apt install "$download_dir/$asset"
    fastfetch --version
    fastfetch
    ```

## 2. Verificação da instalação

1. Verificar resolução no `PATH`, propriedade do arquivo, versão e ajuda. A saída varia conforme o sistema; nenhuma saída de exemplo é presumida.

    ```bash
    command -v fastfetch
    type -a fastfetch
    dpkg-query -S "$(readlink -f "$(command -v fastfetch)")"
    fastfetch --version
    fastfetch --help
    ```

    O esperado é que o comando seja encontrado, pertença ao pacote `fastfetch` e termine sem erro. A existência do arquivo, por si só, não comprova funcionamento.

## 3. Configuração e utilização

- Executar `fastfetch` para exibir informações do sistema.
- Consultar opções com `fastfetch --help`; elas variam conforme a versão.
- Usar `fastfetch --gen-config` para iniciar o gerador interativo disponível nas versões recentes; por padrão, a configuração é salva em `~/.config/fastfetch/config.jsonc`.
- Revisar configurações obtidas de terceiros antes de usá-las: o módulo `Command` pode executar comandos de shell. A saída também pode conter informações de rede; revisar antes de compartilhá-la.
- Consultar os caminhos reconhecidos com `fastfetch --list-config-paths`.
- Para atualizar, baixar o ativo novo e reinstalar com `apt`; conferir os lançamentos, pois `latest` não fixa versão.

## 4. Desinstalação

Este procedimento vale somente para o pacote instalado por `apt`. Não use `apt remove` para binários manuais em `/usr/local/bin`, instalações em `/opt` ou arquivos de outra origem.

1. Confirmar caminho, propriedade e estado do pacote:

    ```bash
    command -v fastfetch
    type -a fastfetch
    dpkg-query -S "$(readlink -f "$(command -v fastfetch)")"
    dpkg-query -W -f='${Package} ${Version} ${db:Status-Abbrev}\n' fastfetch
    ```

2. Se a origem for o pacote `fastfetch`, removê-lo:

    ```bash
    sudo apt remove fastfetch
    ```

3. Verificar se o pacote ou outro comando homônimo permanece:

    ```bash
    dpkg-query -W -f='${Package} ${Version} ${db:Status-Abbrev}\n' fastfetch 2>/dev/null || true
    command -v fastfetch || true
    type -a fastfetch 2>/dev/null || true
    ```

`apt remove` pode deixar configuração do sistema em estado residual. `apt purge fastfetch` também remove esses arquivos; inspecione e decida antes de usá-lo. Configurações pessoais em `~/.config/fastfetch/` e dependências compartilhadas não são removidas por este procedimento.

### 4.1 Código completo para desinstalar

1. Abrir o `Terminal Emulator`. Atalho de teclado:

    ```bash
    Ctrl + Alt + T
    ```

2. Copiar e colar o bloco. Ele só chama `apt remove` se o `dpkg` indicar que `fastfetch` está instalado; depois verifica se ainda há um comando no `PATH`.

    ```bash
    if dpkg-query -W -f='${db:Status-Abbrev}' fastfetch 2>/dev/null | grep -q '^ii'; then
        sudo apt remove fastfetch
    else
        printf 'Pacote fastfetch não instalado pelo dpkg; nenhuma remoção feita.\n'
    fi
    dpkg-query -W -f='${Package} ${Version} ${db:Status-Abbrev}\n' fastfetch 2>/dev/null || true
    command -v fastfetch || true
    type -a fastfetch 2>/dev/null || true
    ```

## 5. Submódulos e preparação do repositório

Clonar o código-fonte, adicionar submódulos ou executar `prepare_repo.sh` não é necessário para instalar ou utilizar o aplicativo. Os submódulos deste checkout pertencem à estrutura de desenvolvimento e não integram o procedimento de instalação do `fastfetch`.

## 6. Instalação global e local de executáveis

A instalação é global e gerenciada por `apt`. Não copiar os arquivos do pacote para `/usr/local/bin` nem movê-los dos diretórios gerenciados. Uma instalação em `~/.local/bin` não é necessária neste método. `command -v fastfetch` verifica a resolução pelo `PATH`; `type -a fastfetch` revela possíveis homônimos.

## 7. Compatibilidade

O projeto publica pacotes para `amd64` e `aarch64` e indica Ubuntu 20.04 ou superior para o pacote Debian/Ubuntu. Este guia cobre somente essas arquiteturas. As versões Ubuntu que já incluem `fastfetch` em seus repositórios podem disponibilizar versão diferente. Verificar a versão instalada com `fastfetch --version`; não se presume compatibilidade com outras distribuições.

## 8. Licença

O projeto oficial `fastfetch-cli/fastfetch` declara licença MIT. Consultar o arquivo `LICENSE` upstream para os termos completos. O `LICENSE.txt` deste checkout contém metadados de outro contexto e não foi tratado como licença do `fastfetch`.

## 9. Contato e suporte

Consultar o repositório oficial `fastfetch-cli/fastfetch` no `GitHub` para documentação, lançamentos e suporte. Ao relatar um problema, informar a versão, distribuição e arquitetura, sem incluir dados pessoais exibidos pelo aplicativo.

## 10. Referências

[1] FASTFETCH CLI.
**Fastfetch**.
Disponível em: <https://github.com/fastfetch-cli/fastfetch>.
GitHub.
Acessado em: 04/11/2026.

[2] FASTFETCH CLI.
**Releases**.
Disponível em: <https://github.com/fastfetch-cli/fastfetch/releases>.
GitHub.
Acessado em: 04/11/2026.

[3] FASTFETCH CLI.
**Licença**.
Disponível em: <https://github.com/fastfetch-cli/fastfetch/blob/dev/LICENSE>.
GitHub.
Acessado em: 04/11/2026.

[4] OPENAI.
**Instalar o `fastfetch` no `Linux Ubuntu` pelo `Terminal Emulator`**.
Disponível em: <https://chatgpt.com/g/g-p-6980caf949648191ad6acfcdbe590f9e-instalar/c/6aeb8ed1-8878-83e9-ad2a-4f5d64fbec25>.
ChatGPT. Link fornecido pelo usuário; o conteúdo não pôde ser acessado nesta sessão.
Acessado em: 04/11/2026.
