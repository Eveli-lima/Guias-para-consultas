# Guia Prático de Docker para Desenvolvedores

Este guia oferece um passo a passo para entender e utilizar Docker, desde os conceitos fundamentais até a criação de um ambiente de desenvolvimento "containerizado" e seguro. O objetivo é transformar a famosa frase "mas na minha máquina funciona" em uma relíquia do passado.

## 📜 Índice

1.  [Conceitos Fundamentais](#-conceitos-fundamentais)
    * [O que é Docker?](#o-que-é-docker)
    * [Contêineres vs. Máquinas Virtuais](#contêineres-vs-máquinas-virtuais)
    * [A Tríade do Docker: Imagem, Contêiner e Dockerfile](#a-tríade-do-docker-imagem-contêiner-e-dockerfile)
2.  [Parte 1: Configuração do Ambiente](#-parte-1-configuração-do-ambiente)
    * [Instalando o Docker Desktop](#instalando-o-docker-desktop)
3.  [Parte 2: "Dockerizando" uma Aplicação Python](#-parte-2-dockerizando-uma-aplicação-python)
    * [2.1: Nossa Aplicação de Exemplo](#21-nossa-aplicação-de-exemplo-flask)
    * [2.2: Escrevendo o `Dockerfile`](#22-escrevendo-o-dockerfile)
    * [2.3: Construindo a Imagem](#23-construindo-a-imagem-docker-build)
    * [2.4: Rodando o Contêiner](#24-rodando-o-contêiner-docker-run)
4.  [Parte 3: Gerenciamento e Comandos Essenciais](#-parte-3-gerenciamento-e-comandos-essenciais-cheatsheet)
5.  [Parte 4: Indo Além com Docker Compose](#-parte-4-indo-além-com-docker-compose)
6.  [Parte 5: Segurança e Boas Práticas](#-parte-5-segurança-e-boas-práticas)
    * [Rodando Contêineres sem `root`](#rodando-contêineres-sem-root-a-prática-essencial)
    * [Outras Boas Práticas](#outras-boas-práticas)

---

## 🐳 Conceitos Fundamentais

### O que é Docker?

Docker é uma plataforma de **containerização** que "empacota" uma aplicação e todas as suas dependências (bibliotecas, arquivos de sistema, código-fonte) em uma unidade padronizada chamada **contêiner**.

A melhor analogia é a de um contêiner de navio: não importa o que há dentro (eletrônicos, bananas, carros), o contêiner em si tem um formato padrão que pode ser transportado e operado por qualquer navio ou guindaste no mundo. Da mesma forma, um contêiner Docker pode ser executado em qualquer máquina que tenha Docker instalado, independentemente do sistema operacional ou das bibliotecas instaladas nela.

### Contêineres vs. Máquinas Virtuais

| Característica | Máquina Virtual (VM) | Contêiner Docker |
| :--- | :--- | :--- |
| **Isolamento** | Completo (virtualiza o Hardware) | De Processos (compartilha o Kernel do SO Host) |
| **Tamanho** | Pesado (Gigabytes) | Leve (Megabytes) |
| **Inicialização**| Lenta (minutos) | Rápida (segundos ou menos) |
| **Recursos** | Consome muita RAM e CPU | Baixo consumo de recursos |

### A Tríade do Docker: Imagem, Contêiner e Dockerfile

1.  **Dockerfile**: É um arquivo de texto, uma "receita" com instruções passo a passo sobre como construir uma Imagem. Ex: "Comece com o Python 3.9, copie os arquivos da minha aplicação, instale as dependências...".
2.  **Imagem**: É o resultado da "compilação" do Dockerfile. É um pacote **imutável** e autocontido que inclui tudo que a aplicação precisa para rodar. É como o `.exe` ou o instalador de um programa.
3.  **Contêiner**: É a **instância em execução** de uma Imagem. É o processo vivo, isolado, rodando na sua máquina. Você pode ter vários contêineres rodando a partir da mesma Imagem.

---

## 💻 Parte 1: Configuração do Ambiente

### Instalando o Docker Desktop

Baixe e instale a versão correspondente ao seu sistema operacional. Ele inclui o motor do Docker, a linha de comando e uma interface gráfica.

-   ➡️ **[docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/)**

Após instalar, abra o Docker Desktop e aguarde ele indicar que está rodando. Verifique no terminal com `docker --version`.

---

## 🚀 Parte 2: "Dockerizando" uma Aplicação Python

Vamos criar um contêiner para uma aplicação web simples usando Flask.

### 2.1: Nossa Aplicação de Exemplo (Flask)

Crie uma pasta para o projeto e, dentro dela, crie estes dois arquivos:

**`app.py`**
```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return '<h1>Olá, mundo com Docker!</h1>'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**`requirements.txt`**
```
Flask==3.0.0
```
*Nota: `host='0.0.0.0'` é crucial para que o Flask seja acessível de fora do contêiner.*

### 2.2: Escrevendo o `Dockerfile`

Na mesma pasta, crie um arquivo chamado `Dockerfile` (sem extensão). Esta versão já inclui as boas práticas de segurança, executando a aplicação com um usuário não-privilegiado.

```dockerfile
# 1. Imagem base oficial do Python (versão "slim" é mais leve)
FROM python:3.9-slim

# 2. Define o diretório de trabalho dentro do contêiner
WORKDIR /app

# 3. Cria um grupo e um usuário de sistema para a nossa aplicação
#    --system: cria um usuário de sistema, sem senha, sem diretório home
RUN addgroup --system app && adduser --system --group app

# 4. Copia o arquivo de dependências
#    Fazemos isso separadamente para aproveitar o cache do Docker.
#    As dependências só serão reinstaladas se o requirements.txt mudar.
COPY requirements.txt .

# 5. Instala as dependências
RUN pip install --no-cache-dir -r requirements.txt

# 6. Copia o resto do código da aplicação
COPY . .

# 7. Define o usuário padrão para executar a aplicação
USER app

# 8. Comando para executar a aplicação quando o contêiner iniciar
CMD ["python", "app.py"]
```

### 2.3: Construindo a Imagem (`docker build`)

Abra o terminal na pasta do projeto e execute o comando abaixo.

```bash
# O '-t' define um nome (tag) para a sua imagem. O '.' indica que o Dockerfile está no diretório atual.
docker build -t meu-app-flask .
```

O Docker vai executar cada passo do `Dockerfile`. Ao final, você terá uma imagem pronta. Verifique com `docker images`.

### 2.4: Rodando o Contêiner (`docker run`)

Agora, vamos criar e rodar um contêiner a partir da imagem que acabamos de construir.

```bash
# '-p 5000:5000' mapeia a porta 5000 da sua máquina (host) para a porta 5000 do contêiner.
docker run -p 5000:5000 meu-app-flask
```

Abra seu navegador e acesse **[http://localhost:5000](http://localhost:5000)**. Você verá a mensagem "Olá, mundo com Docker!". Para parar o contêiner, pressione `Ctrl+C` no terminal.

---

## 📋 Parte 3: Gerenciamento e Comandos Essenciais (Cheatsheet)

| Ação | Comando |
|---|---|
| **Construir** uma imagem | `docker build -t <nome-da-imagem> .` |
| **Listar** imagens | `docker images` |
| **Rodar** um contêiner | `docker run <nome-da-imagem>` |
| Rodar em **background** (detached) | `docker run -d <nome-da-imagem>` |
| Rodar **mapeando portas** | `docker run -p <porta-host>:<porta-container> <nome-da-imagem>`|
| **Listar** contêineres **ativos** | `docker ps` |
| Listar **todos** os contêineres | `docker ps -a` |
| **Parar** um contêiner | `docker stop <id-ou-nome-do-container>` |
| **Iniciar** um contêiner parado | `docker start <id-ou-nome-do-container>` |
| **Ver logs** de um contêiner | `docker logs <id-ou-nome-do-container>` |
| **Remover** um contêiner parado | `docker rm <id-ou-nome-do-container>` |
| **Remover** uma imagem | `docker rmi <nome-da-imagem>` |

---

## 📦 Parte 4: Indo Além com Docker Compose

**Docker Compose** é uma ferramenta para definir e gerenciar aplicações Docker com **múltiplos contêineres** (ex: sua aplicação Python + um banco de dados). A configuração é feita em um único arquivo `docker-compose.yml`.

Crie este arquivo na mesma pasta do projeto:

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/app
```
* **`volumes: - .:/app`**: Essa linha "espelha" a pasta atual para dentro do contêiner. Qualquer alteração no seu código local será refletida **instantaneamente** dentro do contêiner, ideal para desenvolvimento.

**Para usar:**
-   Para subir o ambiente: `docker-compose up`
-   Para derrubar o ambiente: `docker-compose down`

---

## 🛡️ Parte 5: Segurança e Boas Práticas

### Rodando Contêineres sem `root` (A Prática Essencial)

Como já fizemos em nosso `Dockerfile`, executar a aplicação com um usuário não-privilegiado é a prática de segurança mais importante em Docker.

**Os Riscos de Usar `root`:**

* **Potencial de "Container Escape"**: Se um invasor explorar uma falha na sua aplicação, ele terá privilégios de `root` dentro do contêiner. Isso aumenta o risco de uma "fuga" do contêiner para o sistema hospedeiro.
* **Modificações Indesejadas**: Como `root`, a aplicação pode alterar qualquer arquivo dentro do contêiner, podendo corromper o ambiente.
* **Problemas de Permissão com Volumes**: Arquivos criados por um processo `root` em um volume compartilhado pertencerão ao `root` também na sua máquina, causando problemas de permissão.

**Pontos de Atenção ao usar um usuário não-`root`:**

* **Portas Privilegiadas**: Usuários comuns não podem se vincular a portas abaixo de 1024 (como a porta 80). Se precisar expor sua aplicação na porta 80, mapeie a porta alta do contêiner para a porta 80 do host (`-p 80:5000`).
* **Permissões de Arquivos**: Em cenários complexos, pode ser necessário garantir que o usuário `app` tenha as permissões corretas para ler/executar arquivos, usando o comando `RUN chown -R app:app /app` no `Dockerfile` após copiar os arquivos.

### Outras Boas Práticas

1.  **Use `.dockerignore`**: Crie um arquivo `.dockerignore` (com sintaxe similar ao `.gitignore`) para evitar que arquivos desnecessários (como `.venv`, `__pycache__`, `.git`) sejam copiados para dentro da sua imagem, mantendo-a mais leve e segura.
2.  **Imagens "Slim"**: Sempre que possível, use imagens base `slim` ou `alpine` (ex: `python:3.9-slim`), que são significativamente menores.
3.  **Builds em Múltiplos Estágios**: Para linguagens compiladas, use "multi-stage builds" para que a imagem final contenha apenas o artefato compilado, sem as ferramentas de compilação.