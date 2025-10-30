# Ambientes Virtuais em Python com `venv`

Este é um guia prático para criar, gerenciar e utilizar ambientes virtuais (`virtual environments`) em projetos Python. O objetivo é servir como um material de consulta rápida para garantir a organização e a reprodutibilidade dos seus projetos.

## 📜 Índice

1.  [O que é um Ambiente Virtual?](#-o-que-é-um-ambiente-virtual)
2.  [Por que usar?](#-por-que-usar)
3.  [Passo a Passo: Utilizando `venv`](#-passo-a-passo-utilizando-venv)
    * [Criação do Ambiente](#1-criação-do-ambiente)
    * [Ativação do Ambiente](#2-ativação-do-ambiente)
    * [Instalando Pacotes](#3-instalando-pacotes-no-ambiente)
    * [Desativação do Ambiente](#4-desativação-do-ambiente)
4.  [Gerenciando Dependências com `requirements.txt`](#-gerenciando-dependências-com-requirementstxt)
5.  [Resumo dos Comandos (Cheatsheet)](#-resumo-dos-comandos-cheatsheet)
6.  [Boas Práticas](#-boas-práticas)

---

## 🤔 O que é um Ambiente Virtual?

Um ambiente virtual é um diretório que contém uma instalação Python completa e isolada. Isso significa que as bibliotecas e pacotes instalados em um ambiente virtual não interferem com os pacotes instalados em outros ambientes ou no Python "global" do seu sistema.

É como ter uma "caixa de ferramentas" limpa e específica para cada projeto que você desenvolve.

## ✨ Por que usar?

-   **Isolamento de Dependências**: Evita conflitos de versão. O Projeto A pode usar a versão 1.0 de uma biblioteca, enquanto o Projeto B usa a versão 2.0, sem que um afete o outro.
-   **Reprodutibilidade**: Facilita a replicação do ambiente de desenvolvimento em outras máquinas. Qualquer pessoa (ou servidor) pode recriar o ambiente exato com as mesmas versões de pacotes.
-   **Organização**: Mantém o seu ambiente Python global limpo, contendo apenas ferramentas essenciais.
-   **Controle de Versão**: Permite que você saiba exatamente quais pacotes e versões são necessários para o seu projeto funcionar.

---

## 🚀 Passo a Passo: Utilizando `venv`

`venv` é o módulo padrão para criação de ambientes virtuais e já vem incluído no Python a partir da versão 3.3.

### 1. Criação do Ambiente

Navegue até a pasta raiz do seu projeto pelo terminal e execute o comando abaixo. É uma convenção comum nomear a pasta do ambiente como `.venv` ou `venv`.

```bash
# O ponto no início torna a pasta oculta em sistemas baseados em Unix
python -m venv .venv
```

Isso criará uma pasta chamada `.venv` no seu diretório atual com a estrutura de arquivos do Python.

### 2. Ativação do Ambiente

Para começar a usar o ambiente, você precisa ativá-lo. O comando varia de acordo com o sistema operacional.

#### Windows (cmd.exe)
```powershell
.venv\Scripts\activate
```

#### Windows (PowerShell)
```powershell
.venv\Scripts\Activate
# Se houver erro de política de execução, pode ser necessário rodar:
# Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
```

#### macOS / Linux (bash/zsh)
```bash
source .venv/bin/activate
```

**Como saber se funcionou?** O nome do ambiente virtual aparecerá no início da linha do seu terminal. Ex: `(.venv) C:\caminho\do\projeto>`.

### 3. Instalando Pacotes no Ambiente

Com o ambiente ativo, use o `pip` normalmente. Os pacotes serão instalados **apenas** dentro do `.venv`, não no seu sistema global.

```bash
# Exemplo instalando a biblioteca de requisições HTTP
pip install requests

# Exemplo instalando uma versão específica
pip install pandas==2.0.3
```

### 4. Desativação do Ambiente

Quando terminar de trabalhar no projeto, você pode desativar o ambiente com um simples comando:

```bash
deactivate
```

O nome `(.venv)` desaparecerá do seu terminal.

---

## 📦 Gerenciando Dependências com `requirements.txt`

Para garantir a reprodutibilidade do projeto, você deve salvar uma lista de todas as dependências e suas versões em um arquivo.

#### Para gerar o arquivo `requirements.txt`:

Certifique-se de que o ambiente virtual está ativo e execute:

```bash
pip freeze > requirements.txt
```
Isso criará um arquivo `requirements.txt` com o conteúdo de todos os pacotes instalados no ambiente.

#### Para instalar as dependências a partir do arquivo:

Em uma nova máquina (ou após clonar o repositório), crie e ative o ambiente virtual e então execute:

```bash
pip install -r requirements.txt
```
Isso instalará todas as bibliotecas listadas no arquivo, com suas versões exatas.

---

## 📋 Resumo dos Comandos (Cheatsheet)

| Ação                                       | Comando                                       |
| ------------------------------------------ | --------------------------------------------- |
| **Criar** ambiente virtual                 | `python -m venv .venv`                        |
| **Ativar** (Windows)                       | `.venv\Scripts\activate`                      |
| **Ativar** (macOS/Linux)                   | `source .venv/bin/activate`                   |
| **Instalar** um pacote                     | `pip install <nome-do-pacote>`                |
| **Desativar** ambiente                     | `deactivate`                                  |
| **Gerar** lista de dependências            | `pip freeze > requirements.txt`               |
| **Instalar** a partir da lista             | `pip install -r requirements.txt`             |

---

## 💡 Boas Práticas

1.  **Um Ambiente por Projeto**: Sempre crie um novo ambiente virtual para cada novo projeto.
2.  **Adicione ao `.gitignore`**: A pasta do ambiente virtual (`.venv/`) **nunca** deve ser enviada para o repositório Git. Adicione a seguinte linha ao seu arquivo `.gitignore`:

    ```
    # Arquivo .gitignore
    .venv/
    ```
3.  **Mantenha `requirements.txt` atualizado**: Após instalar ou atualizar um pacote, lembre-se de rodar `pip freeze > requirements.txt` para manter a lista de dependências em dia.