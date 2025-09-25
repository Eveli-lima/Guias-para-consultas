# Guia Essencial de Git e GitHub: Do Clone ao Merge

Este é um guia prático para o fluxo de trabalho completo com Git e GitHub, desde a configuração inicial até a colaboração com branches. O objetivo é ser um documento de referência para consultas rápidas no dia a dia.

## 📜 Índice

1.  [Conceitos Fundamentais: Git vs. GitHub](#-conceitos-fundamentais-git-vs-github)
2.  [Parte 1: Configuração Inicial](#-parte-1-configuração-inicial)
    * [1.1: Instalando o Git](#11-instalando-o-git)
    * [1.2: Criando uma Conta no GitHub](#12-criando-uma-conta-no-github)
    * [1.3: Configurando o Git Localmente](#13-configurando-o-git-localmente)
3.  [Parte 2: O Fluxo Básico de um Projeto](#-parte-2-o-fluxo-básico-de-um-projeto)
    * [2.1: Criando um Repositório no GitHub](#21-criando-um-repositório-no-github-remoto)
    * [2.2: Iniciando um Repositório Local](#22-iniciando-um-repositório-local)
    * [2.3: O Ciclo Principal: Add & Commit](#23-o-ciclo-principal-add--commit)
    * [2.4: Conectando Local com Remoto](#24-conectando-o-repositório-local-ao-remoto)
    * [2.5: Enviando Alterações (Push)](#25-enviando-alterações-para-o-github-push)
4.  [Parte 3: Trabalhando com Branches](#-parte-3-trabalhando-com-branches)
    * [3.1: O que são Branches?](#31-o-que-são-branches)
    * [3.2: Criando e Trocando de Branch](#32-criando-e-trocando-de-branch)
    * [3.3: Juntando Alterações (Merge)](#33-juntando-alterações-merge)
    * [3.4: Resolvendo Conflitos de Merge](#34-resolvendo-conflitos-de-merge-básico)
5.  [Resumo dos Comandos (Cheatsheet)](#-resumo-dos-comandos-cheatsheet)

---

## 🤔 Conceitos Fundamentais: Git vs. GitHub

-   **Git**: É o **sistema de controle de versão**. É uma ferramenta que roda na sua máquina e que controla o histórico de alterações dos seus arquivos. Pense nele como um sistema de "salvamentos" superpoderoso que permite que você volte no tempo no seu código.
-   **GitHub**: É uma **plataforma de hospedagem** para repositórios Git. É um serviço na nuvem (um site) onde você pode guardar seus projetos. Pense nele como uma "rede social para códigos", que facilita a colaboração, o backup e o compartilhamento dos seus projetos.

**Em resumo**: Git é a ferramenta, GitHub é o lugar onde você guarda o trabalho feito com a ferramenta.

---

## 💻 Parte 1: Configuração Inicial

### 1.1: Instalando o Git

Se você ainda não tem o Git, baixe e instale a partir do site oficial:

-   ➡️ **[git-scm.com/downloads](https://git-scm.com/downloads)**

Siga o instalador padrão. Para verificar se a instalação foi bem-sucedida, abra um terminal e digite `git --version`.

### 1.2: Criando uma Conta no GitHub

-   Acesse ➡️ **[github.com](https://github.com)** e crie sua conta gratuita.

### 1.3: Configurando o Git Localmente

Antes do primeiro uso, configure seu nome de usuário e e-mail. **Eles devem ser os mesmos da sua conta do GitHub**.

```bash
git config --global user.name "Seu Nome de Usuário"
git config --global user.email "seuemail@exemplo.com"
```
Esta configuração é feita apenas uma vez.

---

## 🚀 Parte 2: O Fluxo Básico de um Projeto

Aqui vamos criar um projeto do zero e enviá-lo para o GitHub.

### 2.1: Criando um Repositório no GitHub (Remoto)

1.  No canto superior direito do GitHub, clique no sinal `+` e em `New repository`.
2.  Dê um nome ao seu repositório (ex: `meu-primeiro-projeto`).
3.  Escolha se ele será `Public` (público) ou `Private` (privado).
4.  **Importante**: Por enquanto, **não** marque as opções "Add a README file", ".gitignore" ou "license". Faremos isso localmente.
5.  Clique em `Create repository`.

### 2.2: Iniciando um Repositório Local

1.  Na sua máquina, crie uma pasta para o projeto e entre nela pelo terminal.

    ```bash
    mkdir meu-primeiro-projeto
    cd meu-primeiro-projeto
    ```

2.  Inicie o Git nesta pasta.

    ```bash
    git init
    ```

Isso cria uma subpasta oculta `.git` que irá rastrear todas as suas alterações.

### 2.3: O Ciclo Principal: `Add` & `Commit`

Este é o fluxo que você mais usará no dia a dia.

1.  Crie um arquivo no seu projeto (ex: `README.md`).
2.  Use `git status` para ver os arquivos que não estão sendo rastreados (`untracked`).
3.  **Adicione** o arquivo à "área de preparação" (*Staging Area*). É aqui que você seleciona quais alterações farão parte do próximo "salvamento".

    ```bash
    # Para adicionar um arquivo específico
    git add README.md

    # Para adicionar todos os arquivos modificados na pasta atual
    git add .
    ```

4.  **Commite** as alterações. O commit é o "salvamento" em si, um ponto no histórico do projeto. A mensagem (`-m`) deve ser clara e descrever o que foi feito.

    ```bash
    git commit -m "feat: Adiciona o arquivo README inicial"
    ```

### 2.4: Conectando o Repositório Local ao Remoto

Agora, precisamos dizer ao nosso Git local onde está o repositório remoto (no GitHub).

Copie a URL do seu repositório no GitHub (a versão HTTPS é mais fácil para iniciantes) e use o comando abaixo. O nome `origin` é uma convenção para se referir ao repositório remoto principal.

```bash
git remote add origin [https://github.com/SEU-USUARIO/meu-primeiro-projeto.git](https://github.com/SEU-USUARIO/meu-primeiro-projeto.git)
```

### 2.5: Enviando Alterações para o GitHub (`push`)

Finalmente, envie seu commit para o GitHub.

```bash
# O '-u' na primeira vez cria um link entre sua branch local e a remota
git push -u origin main
```

> **Nota**: A branch principal pode se chamar `main` (padrão mais novo) ou `master` (padrão antigo). Use o nome que o GitHub indicar na página do seu repositório.

---

## 🌿 Parte 3: Trabalhando com Branches

### 3.1: O que são Branches?

Branches (ramos) são linhas de desenvolvimento independentes. Você cria uma branch para trabalhar em uma nova funcionalidade ou corrigir um bug sem afetar a linha principal (`main`).

### 3.2: Criando e Trocando de Branch

1.  Para criar uma nova branch e já mudar para ela, use `checkout -b`:

    ```bash
    # Cria a branch 'nova-funcionalidade' e muda para ela
    git checkout -b nova-funcionalidade
    ```

2.  Agora você está na branch `nova-funcionalidade`. Faça suas alterações, adicione e commite normalmente.

    ```bash
    # Ex: crie/edite um arquivo
    git add .
    git commit -m "feat: Implementa a nova funcionalidade X"
    ```

3.  Para voltar para a branch principal:

    ```bash
    git checkout main
    ```

### 3.3: Juntando Alterações (`merge`)

Depois de terminar o trabalho na sua branch, você vai querer juntar (fazer o merge) essas alterações na branch `main`.

1.  Primeiro, volte para a branch que vai **receber** as alterações (a `main`).

    ```bash
    git checkout main
    ```

2.  Use o comando `git merge` para trazer as alterações da outra branch.

    ```bash
    git merge nova-funcionalidade
    ```

3.  Agora, a `main` contém todo o trabalho que foi feito na `nova-funcionalidade`. Você pode enviar a `main` atualizada para o GitHub.

    ```bash
    git push origin main
    ```

4.  (Opcional) Após o merge, você pode deletar a branch que não será mais usada.

    ```bash
    git branch -d nova-funcionalidade
    ```
### 3.4: Resolvendo Conflitos de Merge (Básico)

Um conflito ocorre quando a mesma linha de código é alterada em ambas as branches. O Git não sabe qual versão manter e pede sua ajuda.

1. Rode `git merge <branch>`, e o Git avisará sobre o conflito.
2. Abra o arquivo conflitante no seu editor de código. Você verá marcações como:
   ```
   <<<<<<< HEAD
   código da sua branch atual (ex: main)
   =======
   código da branch que você está tentando juntar
   >>>>>>> nome-da-outra-branch
   ```
3. Edite o arquivo, removendo as marcações do Git (`<<<<<`, `=====`, `>>>>>`) e deixando o código da forma correta (pode ser uma versão, a outra, ou uma mistura das duas).
4. Salve o arquivo.
5. Adicione e commite a resolução do conflito:
   ```bash
   git add .
   git commit -m "fix: Resolve conflito de merge"
   ```

---

## 📋 Resumo dos Comandos (Cheatsheet)

| Ação | Comando |
|---|---|
| **Configuração** | `git config --global user.name "Seu Nome"` |
| **Iniciar** | `git init` (local) ou `git clone <url>` (remoto) |
| **Verificar Status** | `git status` |
| **Adicionar ao Staging** | `git add <arquivo>` ou `git add .` |
| **Salvar Alterações** | `git commit -m "mensagem descritiva"` |
| **Conectar ao Remoto** | `git remote add origin <url>` |
| **Enviar para Remoto** | `git push origin <branch>` |
| **Baixar do Remoto** | `git pull origin <branch>` |
| **Listar Branches** | `git branch` |
| **Criar Nova Branch** | `git checkout -b <nome-da-branch>` |
| **Mudar de Branch** | `git checkout <nome-da-branch>` |
| **Juntar Branches** | `git merge <branch-a-ser-juntada>` |
| **Ver Histórico** | `git log --oneline` |