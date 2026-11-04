# Como instalar/configurar/usar o `shell scripts` no `Linux Ubuntu`

## Resumo

Guia direto para instalar o `shell scripts` no seu projeto.

## _Abstract_

_A straightforward guide to installing `shell scripts` in your project._


## Descrição

### `shell scripts`

O `shell scripts` é um repositório do `GitHub` para consolidar códigos genéricos em `shell` para
serem utilizados em qualquer outro projeto. Ele também inclui um _script_ chamado `prepare_repo.sh`
que clona outros repos dentro do seu projeto com demais códigos genéricos em diversas linguagens e
arquivos que são convenientes existirem em projetos.


## Pré-requisitos

- `Git` instalado
- `SSH key` configurada no `GitHub`
- Acesso ao `GitHub` (repositório `edftechnology/shell_scripts`)


## 1. Clonar o repo `shell scripts` no repositório do seu projeto

Para clonar o `shell scripts`, siga os passos abaixo:

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

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



3. Acessar a pasta do seu projeto:

    ```bash
    cd <caminho_ate_o_projeto>
    ```

4. Clonar o `shell scripts`:

    ```bash
    git submodule add \
        git@github.com:edftechnology/shell_scripts.git \
        subs/submodules/shell_scripts
    ```


## 1.1 Código completo para configurar/instalar/usar

Para configurar/instalar/usar o `shell scripts` no `Linux Ubuntu` sem precisar digitar linha por linha, você pode seguir estas etapas:

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

    ```bash
    Ctrl + Alt + T
    ```

2. Digite o seguinte comando e pressione `Enter`:

    ```bash
    sudo apt clean
    sudo apt autoclean
    sudo apt autoremove -y
    sudo apt update
    sudo apt --fix-broken install
    sudo apt clean
    sudo apt list --upgradable
    sudo apt full-upgrade -y
    cd <caminho_ate_o_projeto>
    git submodule add git@github.com:edftechnology/shell_scripts.git subs/submodules/shell_scripts
    git submodule update --init --recursive
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh
    ```


## 2. Inicializar `submodules` existentes (se aplicável)

1. Abrir o `Terminal Emulator`. Você pode fazer isso pressionando:

    ```bash
    Ctrl + Alt + T
    ```

2. Clonar um repo com `submodules` com referência circular ou **NÃO**:

    ```bash
    git clone --depth 1 -b <branch> <repo>
    cd <repo>
    git submodule update --init --depth 1
    ```

3. Se o seu repositório já tiver um `.gitmodules`, inicialize com:

    ```bash
    git submodule update --init --recursive
    ```


## 3. (Opcional) Executar o `prepare_repo.sh`

1. É conveniente executar o `scripts/prepare_repo.sh` depois de clonar o `shell scripts`,
pois ele verifica se o seu repo está com todos os arquivos que é recomendado que um projeto
tenha. Arquivos existentes e divergentes só são substituídos após confirmação; o `.gitignore` é
complementado com regras ausentes, sem remover regras locais. Para isso, na raiz do seu projeto,
execute o comando:

    ```bash
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh
    ```

2. Se quiser escolher explicitamente o tipo de estrutura a preparar, use `--profile`:

    ```bash
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile app
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile app-latex
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile latex
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile latex --force
    ```

    Os perfis disponíveis são:

    - `app`: prepara a estrutura padrão de aplicação, com diretórios como `data`, `inputs`, `outputs`, `scripts`, `tests` e `venvs`.
    - `app-latex`: combina a estrutura de aplicação com a estrutura típica de projetos `LaTeX`.
    - `latex`: prepara apenas a estrutura esperada para projetos `LaTeX`, com diretórios como `figures`, `referencesbibliograficas`, `tables`, `docs/references` e `docs/.references`.

    `--force` substitui arquivos divergentes copiados das fontes padrão sem solicitar confirmação. Ele não remove diretórios, não força atualizações/reset de submódulos e não substitui o `.gitignore`: as regras deste arquivo são sempre complementadas, preservando as regras locais.

    `--yes` e `-y` são aliases: confirmam as ações que solicitam confirmação durante a preparação. Diferentemente de `--force`, eles também podem confirmar operações não relacionadas à substituição de arquivos.

    No perfil `latex`, o `.gitignore` mantém trackáveis os PDFs com nomes `main*.pdf`, para que a saída compilada possa ser consultada mesmo quando a fonte atual não compila. Para sincronizar os repositórios Git aninhados antes da preparação, use `--sync-repositories`; a sincronização publica commits locais apenas quando a árvore está limpa, aplica somente fast-forward e interrompe ao encontrar alterações não commitadas ou histórico divergente.

3. Se um perfil já tiver sido aplicado e você executar outro perfil, o `prepare_repo.sh` troca o perfil de forma conservadora. Por exemplo, se você executou `--profile app` por engano e depois executar `--profile latex`, ele remove os artefatos seguros do perfil `app` antes de aplicar o perfil `latex`, como se o perfil `app` não tivesse sido a escolha final. Diretórios não vazios e arquivos modificados são preservados para evitar perda de trabalho.

    ```bash
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile app
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile latex
    ```

4. Para desfazer explicitamente uma preparação, use `undo`. Antes de remover qualquer coisa, é recomendável inspecionar com `--dry-run`:

    ```bash
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh undo --profile app --dry-run --yes
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh undo --profile app
    ```

    Em execuções feitas com versões recentes do script, ele grava um snapshot Git antes de alterar o repositório. Nesses casos, também é possível desfazer via Git:

    ```bash
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh undo --revert
    bash subs/submodules/shell_scripts/scripts/prepare_repo.sh undo --hard
    ```

    Use `--revert` para criar commit(s) de reversão mantendo o histórico. Use `--hard` apenas quando quiser voltar o índice e a árvore de trabalho exatamente ao snapshot anterior à última execução do `prepare_repo.sh`.

5. Quando o comando é executado em um `Terminal Emulator` interativo sem `--profile`, ele pergunta qual perfil deve usar. Em contexto não interativo, o padrão atual é `app`.

    Ao iniciar em `Terminal Emulator` interativo, o `prepare_repo.sh` também verifica periodicamente se o submódulo `shell_scripts` está atrás do remoto. Se estiver, ele mostra um aviso no estilo:

    ```bash
    [shell_scripts] A newer version is available. Would you like to update now? [Y/n]
    ```

    O padrão é `N`: `Enter` ou `n` continuam a execução normalmente sem atualizar. Se o usuário responder `y`, `yes`, `1`, `true` ou `on`, o script executa `git pull --ff-only`, reinicia o próprio `prepare_repo.sh` e segue já com a versão nova. Em contexto sem `TTY`, ele não pergunta e mostra apenas o aviso de que há versão mais nova disponível. Para desativar essa checagem, defina `SHELL_SCRIPTS_UPDATE_CHECK=0`.

6. O _script_ faz, de forma resumida:

    - Garante/atualiza submodules essenciais
    - Cria pastas padrão do projeto de acordo com o perfil selecionado
    - Cria `__init__.py` onde for apropriado
    - Nos perfis `latex` e `app-latex`, grava `.prepare_repo_profile` com o perfil aplicado
    - Nos perfis `latex` e `app-latex`, gera `.lso_preserve_local`, usado pelo `latex_synchronizer_with_overleaf` durante `--prune-local`
    - Grava um snapshot Git para permitir `prepare_repo.sh undo --revert` ou `prepare_repo.sh undo --hard` em execuções futuras

    Em preparação normal, `--dry-run` não aplica o perfil nem altera arquivos ou metadados Git. Como a preparação pode envolver submódulos e arquivos externos, essa opção informa que nenhuma alteração foi feita; a prévia detalhada fica disponível para as operações `undo` compatíveis.


## 4. Governança documental mínima

Para projetos analíticos, modelos, automações críticas ou ambientes regulados, recomenda-se manter no `README.ipynb` apenas um resumo de governança suficiente para orientar instalação, reprodução e manutenção. A documentação técnica completa deve ficar em arquivos específicos do projeto.

### Versões do ambiente tecnológico

| Componente | Versão/Comando | Observação |
| --- | --- | --- |
| `Git` | `git --version` | Controle de versão e submodules |
| `Bash` | `bash --version` | Execução do `prepare_repo.sh` |
| `Python` | `python3 --version` | Conversões, notebooks e scripts auxiliares |
| `Jupyter` | `jupyter --version` | Edição/execução do `README.ipynb`, quando aplicável |
| Submodules | `git submodule status` | Versões dos repositórios auxiliares |

### Checklist documental

- Objetivo, uso previsto, escopo e público-alvo do projeto
- Responsáveis por desenvolvimento, uso, revisão e aprovação
- Fontes de dados, premissas, limitações, entradas e saídas esperadas
- Testes, validações, critérios de aceite, monitoramento e evidências relevantes
- Riscos operacionais conhecidos e plano de contingência para falhas de execução, dados ou dependências
- Histórico de versões, motivo da mudança, responsável e data

## Sincronizar repositórios Git

O script `scripts/sync_repositories.sh` aceita a opção `--directory DIRECTORY` para
indicar a pasta que contém os repositórios. Sem essa opção, usa o diretório atual.
Por exemplo, a partir da raiz do checkout `unix`:

```bash
bash ubuntu/shell_scripts/scripts/sync_repositories.sh --directory .
```

Para selecionar outra pasta explicitamente, informe o caminho depois de `--directory`:

```bash
bash ubuntu/shell_scripts/scripts/sync_repositories.sh --directory /caminho/para/repositorios
```

O script percorre repositórios aninhados sob a pasta selecionada. Por padrão, a sincronização é conservadora: worktrees sujas ficam intactas e causam aviso de falha; commits locais à frente são enviados; commits remotos à frente são integrados apenas por fast-forward; históricos divergentes são reportados sem stash, rebase ou reset. `--hard-reset` seleciona o comportamento legado de stash e reset e deve ser usado somente quando essa substituição local for intencional.

Para atualizar checkouts de submódulos padrão, use `scripts/update_named_repositories.sh`.
Sem `--name`, o script procura `agents`, `codex_configuration_scripts`,
`gemini_configuration_scripts`, `git_scripts`, `markdown_scripts`, `python_scripts`,
`shell_scripts` e `txt_scripts`. A busca exclui diretórios `.git` e backups da sincronização:

```bash
bash subs/submodules/shell_scripts/scripts/update_named_repositories.sh
```

Use `--directory ROOT` para definir a raiz e `--name NAME` para limitar a busca a um nome.
A busca
é recursiva para alcançar submódulos aninhados, mas só atualiza diretórios que sejam a raiz
de um checkout Git independente. Pastas comuns e diretórios internos são ignorados e
contabilizados no resumo final. Submódulos em `detached HEAD` seguem o commit registrado pelo
repositório pai; o script só altera um submódulo limpo quando seu checkout não corresponde ao
gitlink do pai.
O script segue o upstream configurado em cada checkout; se não houver upstream, usa
`origin/main` por padrão, que pode ser alterado com `--branch BRANCH`. Worktrees sujas,
branches destacadas e históricos divergentes ficam intactos; commits locais à frente não
são enviados. Checkouts independentes no mesmo nível são processados em paralelo, usando até
o número de CPUs disponíveis informado por `nproc` (ou `getconf` como fallback); níveis
aninhados são processados em sequência para preservar a ordem entre repositórios pais e
submódulos. O código de saída é diferente de zero se algum checkout precisar de atenção.

### Clonar e preparar `shell_scripts` como submodule

Execute a sequência a partir da raiz do repositório que deve receber o submodule,
somente quando `subs/submodules/shell_scripts` ainda não estiver configurado:

```bash
gitsubmoduleaddshellscripts
sleep 10
bash subs/submodules/shell_scripts/scripts/prepare_repo.sh --profile latex
```

O primeiro comando é um alias do shell que adiciona/clona o submodule; o intervalo
permite a conclusão da operação antes de `prepare_repo.sh` ajustar o repositório.


## Compatibilidade

- Testado em Linux Ubuntu (recomendado 20.04+).
- Deve funcionar em Debian e WSL, desde que o Git e o Bash estejam disponíveis.


## Licença

Este repositório inclui o arquivo `LICENSE.txt`.

## Contato e suporte

Para dúvidas ou problemas, abra uma issue no repositório do `GitHub`.


## Referências

[1] OPENAI. **Instalar o shell scripts no linux ubuntu pelo terminal emulator**. Disponível em: <https://chatgpt.com/c/shell-scripts>. ChatGPT. Acessado em: 02/04/2026.
