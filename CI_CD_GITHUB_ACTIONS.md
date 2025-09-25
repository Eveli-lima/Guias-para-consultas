# Guia de Automação com CI/CD e GitHub Actions

Este guia prático ensina como automatizar o ciclo de vida do seu software usando CI/CD (Integração e Entrega Contínua) com GitHub Actions. Vamos criar um "robô" que testa nosso código automaticamente a cada `push`, garantindo a qualidade e a estabilidade do projeto.

## 📜 Índice

1.  [O que é CI/CD?](#-o-que-é-cicd)
    * [Integração Contínua (CI)](#integração-contínua-ci)
    * [Entrega/Implantação Contínua (CD)](#entregaimplantação-contínua-cd)
2.  [Anatomia de uma GitHub Action](#-anatomia-de-uma-github-action)
3.  [Parte 1: Criando seu Primeiro Workflow](#-parte-1-criando-seu-primeiro-workflow-de-ci)
    * [3.1: A Estrutura de Pastas](#31-a-estrutura-de-pastas)
    * [3.2: O Arquivo do Workflow (`.yml`)](#32-o-arquivo-do-workflow-yml)
    * [3.3: Entendendo o Arquivo Passo a Passo](#33-entendendo-o-arquivo-passo-a-passo)
4.  [Parte 2: Vendo a Mágica Acontecer](#-parte-2-vendo-a-mágica-acontecer)
    * [Como Acionar o Workflow](#como-acionar-o-workflow)
    * [Analisando os Resultados](#analisando-os-resultados)
5.  [Conectando Todos os Nossos Guias](#-conectando-todos-os-nossos-guias)
6.  [Expandindo o Workflow: Próximos Passos](#-expandindo-o-workflow-próximos-passos)
7.  [Resumo da Sintaxe (Cheatsheet)](#-resumo-da-sintaxe-cheatsheet)

---

## 🤖 O que é CI/CD?

CI/CD é uma prática de engenharia de software que visa automatizar a construção, os testes e a implantação (deploy) de código.

### Integração Contínua (CI)

É a prática de mesclar (integrar) as alterações de código de vários desenvolvedores em um repositório central de forma frequente. Cada integração é **automaticamente verificada** por um "build" e por testes automatizados.

* **Objetivo**: Detectar problemas de integração e bugs o mais cedo possível.
* **Gatilho**: Geralmente, um `git push` para qualquer branch.
* **O que faz**: Compila o código, instala dependências e **roda os testes**.

### Entrega/Implantação Contínua (CD)

É a continuação lógica da CI. Após o código passar por todos os testes na fase de CI, ele é automaticamente preparado e pode ser implantado em um ambiente (homologação ou produção).

* **Entrega Contínua (Continuous Delivery)**: A aplicação está pronta para ser implantada com um clique de botão.
* **Implantação Contínua (Continuous Deployment)**: A aplicação é implantada em produção **automaticamente**, sem intervenção humana.

---

## ⚙️ Anatomia de uma GitHub Action

O GitHub Actions funciona com base em alguns conceitos-chave definidos em arquivos de texto no formato `YAML`.

* **Workflow**: O processo automatizado como um todo. Um repositório pode ter vários workflows.
* **Event**: O gatilho que inicia um workflow (ex: `push`, `pull_request`, agendamento).
* **Job**: Um conjunto de passos que rodam em uma mesma máquina virtual (chamada de `runner`).
* **Runner**: O servidor (Linux, Windows ou macOS) que irá executar seu job. O GitHub oferece runners gratuitos.
* **Step**: Uma tarefa individual dentro de um job. Pode ser um comando de shell ou uma **Action**.
* **Action**: Um pedaço de código reutilizável que executa uma tarefa complexa (ex: `actions/checkout` para baixar o código, `actions/setup-python` para configurar o Python).

---

## 🚀 Parte 1: Criando seu Primeiro Workflow de CI

Vamos criar um workflow que, para cada `push` na branch `main`:
1.  Prepara um ambiente Linux.
2.  Baixa o nosso código.
3.  Instala o Python e as dependências do projeto.
4.  Roda os testes automatizados com `pytest`.

### 3.1: A Estrutura de Pastas

Dentro da raiz do seu projeto, você precisa criar uma estrutura de pastas específica:
```
meu-projeto/
├── .github/
│   └── workflows/
│       └── ci.yml   <-- Nosso workflow fica aqui!
├── ... (resto do seu código)
└── requirements.txt
```
O GitHub automaticamente procura por arquivos `.yml` ou `.yaml` dentro da pasta `.github/workflows/`.

### 3.2: O Arquivo do Workflow (`.yml`)

Crie o arquivo `ci.yml` e cole o seguinte conteúdo nele.

```yaml
# Nome do workflow que aparecerá na aba "Actions" do GitHub
name: Python CI

# 1. Evento (Gatilho): quando o workflow deve rodar
on:
  push:
    branches: [ "main" ] # Roda em push na branch main
  pull_request:
    branches: [ "main" ] # Roda ao abrir um pull request para a main

# 2. Jobs: lista de trabalhos a serem executados
jobs:
  build:
    # 3. Runner: define a máquina virtual
    runs-on: ubuntu-latest

    # 4. Steps: sequência de tarefas do job
    steps:
      # Step 1: Baixa o código do seu repositório para o runner
      - name: Checkout repository
        uses: actions/checkout@v3

      # Step 2: Configura o ambiente Python
      - name: Set up Python 3.9
        uses: actions/setup-python@v3
        with:
          python-version: "3.9"

      # Step 3: Instala as dependências
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest # Instala a biblioteca de testes

      # Step 4: Roda os testes com pytest
      - name: Run tests
        run: |
          pytest
```

### 3.3: Entendendo o Arquivo Passo a Passo

* **`name`**: O nome que você verá na interface do GitHub.
* **`on`**: Define os gatilhos. Aqui, ele roda em `push` ou `pull_request` para a branch `main`.
* **`jobs`**: Define os trabalhos. Temos um único job chamado `build`.
* **`runs-on: ubuntu-latest`**: Especifica que nosso job rodará na versão mais recente do Ubuntu Linux oferecida pelo GitHub.
* **`steps`**: A parte mais importante, a sequência de ações.
    * **`uses: actions/checkout@v3`**: Usa uma Action pronta da comunidade para fazer o `git clone` do seu repositório.
    * **`uses: actions/setup-python@v3`**: Usa outra Action pronta para instalar e configurar a versão do Python que especificamos.
    * **`run: ...`**: Executa comandos de shell diretamente no terminal do runner. Usamos `|` para rodar múltiplos comandos. Primeiro instalamos as dependências do `requirements.txt`, depois o `pytest`.
    * **`run: pytest`**: Executa a suíte de testes. Se algum teste falhar, este passo falhará e o workflow inteiro será marcado como "falho".

---

## ✅ Parte 2: Vendo a Mágica Acontecer

### Como Acionar o Workflow

1.  Adicione, faça o commit e envie os arquivos (`.github/workflows/ci.yml` e o resto do seu projeto) para o seu repositório no GitHub.
    ```bash
    git add .
    git commit -m "feat: Adiciona workflow de CI com GitHub Actions"
    git push origin main
    ```
2.  O `push` para a branch `main` irá acionar o workflow automaticamente.

### Analisando os Resultados

1.  Vá para a página do seu repositório no GitHub.
2.  Clique na aba **"Actions"**.
3.  Você verá seu workflow "Python CI" em execução (um ícone amarelo) ou já finalizado (um `✓` verde para sucesso ou um `X` vermelho para falha).
4.  Clique no workflow para ver os detalhes de cada `job` e cada `step`, incluindo o log de todos os comandos que foram executados.

![Exemplo da aba Actions no GitHub](https://storage.googleapis.com/gemini-prod/images/47b46d7e-72ba-4b06-a070-070868f11559)

---

## 🔗 Conectando Todos os Nossos Guias

Este workflow é a culminação de tudo que você organizou:

* **Git e GitHub**: O `git push` é o gatilho que inicia todo o processo.
* **Ambiente Virtual**: O runner do GitHub cria um ambiente limpo e isolado para cada execução, um conceito similar ao `venv`, mas a nível de sistema operacional.
* **Linha de Comando**: Todos os comandos na seção `run` são executados no terminal do runner.
* **Docker (Próximo Passo)**: Um passo comum em workflows de CI/CD é usar o que aprendemos para construir uma imagem Docker e enviá-la para um registro como o Docker Hub.

---

## 📈 Expandindo o Workflow: Próximos Passos

Este é um workflow de CI básico. Você pode expandi-lo para:
* **Adicionar um Linter**: Incluir um step para verificar a formatação do código com `flake8` ou `black`.
* **Construir uma Imagem Docker**: Adicionar steps para fazer login no Docker Hub, construir sua imagem com `docker build` e enviá-la com `docker push`.
* **Fazer Deploy**: Em workflows mais avançados, você pode adicionar jobs para fazer deploy da sua aplicação em serviços de nuvem (AWS, Azure, Heroku) automaticamente após os testes passarem.

---

## 📋 Resumo da Sintaxe (Cheatsheet)

| Chave YAML | Descrição | Exemplo |
|---|---|---|
| `name` | Nome do workflow. | `name: My Awesome Workflow` |
| `on` | Gatilho do workflow. | `on: [push, pull_request]` |
| `jobs` | Contêiner para os trabalhos. | `jobs:` |
| `runs-on` | Máquina virtual para o job. | `runs-on: macos-latest` |
| `steps` | Contêiner para os passos. | `steps:` |
| `uses` | Executa uma Action reutilizável. | `uses: actions/checkout@v3` |
| `run` | Executa um comando de shell. | `run: npm install` |
| `with` | Passa parâmetros para uma Action. | `with: { node-version: 18 }`|