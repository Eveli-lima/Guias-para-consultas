# Guia de Maestria do Terminal: Linha de Comando e Shell Scripting

Este guia é um compilado de comandos, conceitos e práticas para transformar o terminal de uma simples tela preta em sua ferramenta mais poderosa de produtividade e automação. Vamos além do `cd` e `ls` e entramos no mundo da manipulação de texto, gerenciamento de processos e criação de scripts.

## 📜 Índice

1.  [A Filosofia do Terminal e Revisão Essencial](#-a-filosofia-do-terminal-e-revisão-essencial)
2.  [Manipulando Texto e Arquivos como um Profissional](#-manipulando-texto-e-arquivos-como-um-profissional)
    * [`grep`: O Caçador de Texto](#grep-o-caçador-de-texto)
    * [`find`: O Localizador de Arquivos](#find-o-localizador-de-arquivos)
3.  [O Superpoder dos Pipes `|` e Redirecionamentos `>`](#-o-superpoder-dos-pipes--e-redirecionamentos-)
    * [Redirecionamento (`>` e `>>`): Salvando a Saída](#redirecionamento--e--salvando-a-saída)
    * [Pipes (`|`): Conectando Comandos](#pipes--conectando-comandos)
4.  [Gerenciando Processos em Segundo Plano](#-gerenciando-processos-em-segundo-plano)
5.  [Introdução ao Shell Scripting: Automatize Tudo](#-introdução-ao-shell-scripting-automatize-tudo)
    * [5.1: Anatomia de um Shell Script](#51-anatomia-de-um-shell-script)
    * [5.2: Variáveis e Argumentos](#52-variáveis-e-argumentos)
    * [5.3: Estruturas de Controle (if e for)](#53-estruturas-de-controle-if-e-for)
    * [5.4: Exemplo Prático: Script para Criar Projetos](#54-exemplo-prático-script-para-criar-projetos)
6.  [Resumo dos Comandos (Cheatsheet)](#-resumo-dos-comandos-cheatsheet)

---

## ⌨️ A Filosofia do Terminal e Revisão Essencial

A filosofia do Unix (base para Linux e macOS) é ter programas pequenos que fazem uma única coisa, mas a fazem bem. A mágica acontece quando você os combina. Antes de combinar, vamos revisar o básico:

* `pwd`: Mostra o diretório atual.
* `ls -la`: Lista **t**odos os arquivos (`a`) em formato **l**ongo (`l`), mostrando permissões, dono, tamanho e data.
* `cd <caminho>`: Navega para um diretório. `cd ..` volta um nível, `cd ~` vai para o diretório home.
* `mkdir <nome>`: Cria um novo diretório.
* `cp <origem> <destino>`: Copia um arquivo ou diretório.
* `mv <origem> <destino>`: Move ou renomeia um arquivo ou diretório.
* `rm <arquivo>`: Remove um arquivo. `rm -r <diretorio>` remove um diretório e seu conteúdo (use com **cuidado**!).
* `clear`: Limpa a tela do terminal.

---

## 🔍 Manipulando Texto e Arquivos como um Profissional

### `grep`: O Caçador de Texto

`grep` busca por padrões de texto dentro de arquivos. É indispensável para encontrar configurações, chamadas de função ou qualquer trecho de código.

**Uso:** `grep "texto_buscado" nome_do_arquivo.txt`

**Opções úteis:**
* `-i`: Ignora se as letras são maiúsculas ou minúsculas.
* `-r`: Busca recursivamente em todos os arquivos de um diretório.
* `-v`: Inverte a busca, mostrando apenas as linhas que **não** contêm o padrão.
* `-n`: Mostra o número da linha onde o texto foi encontrado.

**Exemplo prático:** Encontrar todos os arquivos Python que importam a biblioteca `requests` no diretório atual.
```bash
grep -r "import requests" .
```

### `find`: O Localizador de Arquivos

Enquanto o `grep` busca *dentro* dos arquivos, o `find` busca *pelos próprios arquivos* com base em nome, tipo, tamanho, data, etc.

**Uso:** `find <diretorio_inicial> -name "<padrão_nome>"`

**Opções úteis:**
* `-name "*.py"`: Busca por arquivos que terminam com `.py`.
* `-type f`: Busca apenas por arquivos.
* `-type d`: Busca apenas por diretórios.
* `-mtime -7`: Busca por arquivos modificados nos últimos 7 dias.

**Exemplo prático:** Encontrar todos os arquivos `.md` no seu diretório home.
```bash
find ~ -name "*.md"
```

---

## 管道 O Superpoder dos Pipes `|` e Redirecionamentos `>`

### Redirecionamento (`>` e `>>`): Salvando a Saída

Você pode salvar o resultado de um comando em um arquivo em vez de mostrá-lo na tela.

* `>`: Cria/sobrescreve o arquivo com a saída do comando.
* `>>`: Adiciona a saída do comando ao final do arquivo, sem apagar o conteúdo existente.

**Exemplo prático:** Salvar a lista de arquivos do diretório atual em `lista.txt`.
```bash
ls -la > lista.txt
```

### Pipes (`|`): Conectando Comandos

O pipe é o conceito mais poderoso. Ele pega a **saída** de um comando e a transforma na **entrada** do comando seguinte. Isso permite criar sequências complexas e poderosas.

**Exemplo prático 1:** Listar todos os processos rodando e filtrar para mostrar apenas os relacionados ao "Docker".
```bash
ps aux | grep "docker"
```

**Exemplo prático 2:** Ver seu histórico de comandos e encontrar todos os que usaram `git commit`.
```bash
history | grep "git commit"
```

---

## ⚙️ Gerenciando Processos em Segundo Plano

* `ps aux`: Mostra **t**odos (`a`) os processos rodando no sistema, de **t**odos os **u**suários (`u`), com detalhes (`x`).
* `kill <PID>`: Envia um sinal para terminar um processo. `PID` é o ID do processo, que você encontra com `ps`.
* `kill -9 <PID>`: O "canhão". Força o encerramento de um processo que não responde (sinal `SIGKILL`).
* `comando &`: Executa um comando em **segundo plano** (background), liberando seu terminal imediatamente.
* `jobs`: Lista os processos que estão rodando em segundo plano no seu terminal atual.
* `fg %1`: Traz o job número 1 de volta para o **primeiro plano** (foreground).

---

## 📜 Introdução ao Shell Scripting: Automatize Tudo

Um Shell Script é simplesmente um arquivo de texto contendo uma sequência de comandos do terminal.

### 5.1: Anatomia de um Shell Script

Crie um arquivo chamado `ola.sh`:

```bash
#!/bin/bash

# Este é um comentário.
# A primeira linha, "shebang", diz ao sistema qual interpretador usar (neste caso, o bash).

echo "Olá, mundo dos scripts!"
```

**Para executar:**
1.  **Dê permissão de execução**: `chmod +x ola.sh` (você só faz isso uma vez por arquivo).
2.  **Execute o script**: `./ola.sh`

### 5.2: Variáveis e Argumentos

Scripts ficam poderosos quando podem usar dados variáveis.

* **Variáveis**:
    ```bash
    NOME="Caio"
    echo "Olá, $NOME"
    ```
* **Argumentos da Linha de Comando**: O script pode receber "argumentos" quando você o chama.
    * `$1`: Primeiro argumento
    * `$2`: Segundo argumento
    * `$0`: O nome do próprio script
    * `$#`: Quantidade de argumentos passados

**Exemplo (`saudacao.sh`):**
```bash
#!/bin/bash
echo "Olá, $1! Bem-vindo ao script $0."
```
**Uso:** `./saudacao.sh "Terra"`

### 5.3: Estruturas de Controle (`if` e `for`)

* **`if` (Condicional)**:
    ```bash
    #!/bin/bash
    if [ "$1" == "start" ]; then
        echo "Iniciando o processo..."
    elif [ "$1" == "stop" ]; then
        echo "Parando o processo..."
    else
        echo "Uso: $0 [start|stop]"
    fi
    ```
* **`for` (Laço de Repetição)**:
    ```bash
    #!/bin/bash
    for i in {1..5}; do
        echo "Contando: $i"
    done
    ```

### 5.4: Exemplo Prático: Script para Criar Projetos

Este script automatiza a criação de uma estrutura de projeto Python, unindo tudo que aprendemos nos guias anteriores.

Crie o arquivo `criar_projeto.sh`:
```bash
#!/bin/bash

# Verifica se o nome do projeto foi fornecido
if [ -z "$1" ]; then
    echo "Erro: Forneça um nome para o projeto."
    echo "Uso: $0 <nome-do-projeto>"
    exit 1
fi

NOME_PROJETO=$1

echo "🚀 Criando projeto: $NOME_PROJETO"

# 1. Cria o diretório e entra nele
mkdir $NOME_PROJETO
cd $NOME_PROJETO

# 2. Inicia o repositório Git
echo "🎨 Inicializando Git..."
git init

# 3. Cria o ambiente virtual Python
echo "🐍 Criando ambiente virtual..."
python3 -m venv .venv

# 4. Cria o arquivo .gitignore
echo "📄 Criando .gitignore..."
echo ".venv/" > .gitignore

# 5. Cria um arquivo README inicial
echo "✨ Criando README.md..."
echo "# $NOME_PROJETO" > README.md

echo "✅ Projeto $NOME_PROJETO criado com sucesso!"
echo "Para ativar o ambiente virtual, rode: source .venv/bin/activate"
```
**Uso:**
Dê permissão (`chmod +x criar_projeto.sh`) e depois execute: `./criar_projeto.sh meu-novo-app-python`

---

## 📋 Resumo dos Comandos (Cheatsheet)

| Ação | Comando |
|---|---|
| **Buscar texto** em arquivos | `grep -r "padrão" .` |
| **Encontrar arquivos** por nome | `find . -name "*.py"` |
| **Sobrescrever** arquivo com saída | `comando > arquivo.txt` |
| **Anexar** saída ao arquivo | `comando >> arquivo.txt` |
| **Conectar** comandos | `comando1 | comando2` |
| **Ver processos** detalhados | `ps aux` |
| **Forçar parada** de um processo | `kill -9 <PID>` |
| **Rodar em background** | `comando &` |
| **Tornar script executável** | `chmod +x script.sh`|