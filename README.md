# Atividade Docker + CI — Leonardo Mendonça Tupinambá

Aluno(a): Leonardo Mendonça Tupinambá Turma: Vespertino Data: 27/07/2026 Aplicação usada: docker/getting-started-app — To-Do em Node.js

## 1. Como executar este projeto

git clone https://github.com/leonardotupinamba/meu-docker-diario.git cd meu-docker-diario cp .env.example .env docker compose up -d --build

Acesse: http://localhost:3000

Para resolver: docker compose down(mantém os dados) ou docker compose down -v(apaga tudo, inclusive o banco).

## 2. Imagem e Dockerfile multiestágio

Estágios utilizados: construtor (instala as dependências com npm ci) e o estágio final (só copia o que já foi instalado + o código, sem levar nada do processo de build).

Imagem base: node:20-alpine Usuário de execução: node, não-root Tamanho final da imagem: 194MB

Por que o multi-estágio ajuda: porque permite usar várias etapas dentro de mesmo Dockerfile, separando o ambiente de construção do ambiente que realmente vai executar a aplicação.

Print 1 - build + docker images

<img width="1900" height="217" alt="imagens" src="https://github.com/user-attachments/assets/166f567c-99e3-4079-9ec2-99c61ca33555" />

Print 2 - Aplicação rodando com tarefas cadastradas

<img width="1127" height="972" alt="print-tarefas-cadastradas" src="https://github.com/user-attachments/assets/225413a9-41c0-4133-9b8b-01cba8cac75a" />

## 3. Volumes e persistência

Volume usado: todo-db → montado em /etc/todos

O primeiro teste foi sem volume: cadastrei tarefas, destruí o container e subi de novo — a lista voltou vazia, porque o banco morreu junto com o container. Depois refiz o teste usando o volume nomeado e, dessa vez, ao recriar o container como tarefas executadas lá, porque os dados ficaram salvos no volume, que existem independente do container.

Print 3 - SEM volume: dados perdidos ao recriar o container

<img width="1917" height="361" alt="recriar o container" src="https://github.com/user-attachments/assets/41398584-c6e8-4214-afcb-0fc23dbf7ff4" />
<img width="1121" height="950" alt="depois de apagar o container" src="https://github.com/user-attachments/assets/67d83344-ee92-4c8e-8859-46f5a2302475" />

Print 4 — Volume COM: dados preservados

<img width="1127" height="972" alt="print-tarefas-cadastradas" src="https://github.com/user-attachments/assets/ce4147d7-4964-47a3-a4f6-68c17d6cb689" />

Evidência extra - volume criado

<img width="1025" height="197" alt="print-volume-criado" src="https://github.com/user-attachments/assets/373992f4-5e47-4eb7-ae6f-9a5b7ff44a67" />

Diferença entre docker compose down e docker compose down -v : Docker compose down para e remove os containers e as redes criadas pelo Compose e o Docker compose down -v faz tudo que o comando anterior faz, mas também remove os volumes.

Imprimir 5 — inspecionar a rede docker

<img width="1066" height="1010" alt="print rede" src="https://github.com/user-attachments/assets/7eb97c34-5402-4c84-b285-e1bf5aaac0b4" />

Print 6 — dados dentro do MySQL (selecione * em todo_items;)

<img width="1127" height="972" alt="print-tarefas-cadastradas" src="https://github.com/user-attachments/assets/cece53e6-14e4-42fe-b1b1-a550e65ecbc8" />

## 5. Docker Compose

Serviços: app, db Rede: todo-net · Volume: todo-mysql-data Healthcheck em: db · depende_on com: condição: service_healthy Variáveis ​​sensíveis: comunicações via .env (não versionado). Modelo em .env.example.

Testei a persistência com o Compose também: cadastrei tarefas, rodei docker compose down (sem -v) e subi de novo — as tarefas acompanhadas lá. Depois rodei docker compose down -ve subi de novo — a lista voltou vazia, confirmando que o -v desligou o volume junto.

Print 7 — docker compose ps

<img width="1497" height="992" alt="docker compose" src="https://github.com/user-attachments/assets/fc2bc015-e276-4c8d-b018-b94889c9cb0a" />

## 6. Integração Contínua (Ações do GitHub)

Arquivo do fluxo de trabalho: .github/workflows/ci.yml Gatilhos: push e pull_request O que o pipeline faz:

valida o compose
construir uma imagem
sobe uma pilha
aguarda a app responder e testa criar uma tarefa via API
baixo uma pilha

Print 8 - CI

<img width="1262" height="1315" alt="ci-verde" src="https://github.com/user-attachments/assets/f83cf7b9-7ff4-43c4-9a9a-7130c57465da" />

## 7. Quebra proposital do CI



## Mapa do repositório

| Arquivo                        | Pra que serve            | Aula |
| ------------------------------ | ------------------------ | ---- |
| `src/habits.js`                | Lógica pura (testável)   | 4    |
| `test/habits.test.js`          | Testes que o CI roda     | 4    |
| `Dockerfile` / `nginx.conf`    | Conteinerização          | 4    |
| `.github/workflows/ci.yml`     | Integração Contínua      | 4    |
| `.github/workflows/deploy.yml` | Entrega Contínua (Pages) | 5    |
| `src/App.jsx` / `App.css`      | A interface da app       | —    |
