## Linketinder – Groovy

**Autor:** Henrique Roberto dos Santos

---
## Descrição

Este é o projeto do sistema **Linketinder**, uma aplicação inspirada na união entre perfis profissionais no estilo **LinkedIn** e a lógica de interação interativa de perfis no estilo **Tinder**.

O objetivo é permitir a interação entre **candidatos** e **empresas** por meio de um sistema de match parcialmente anônimo: antes do match, apenas informações relevantes são exibidas — protegendo a identidade dos envolvidos. Após o match, todos os dados ficam visíveis para ambos os lados.

O sistema possibilita que usuários realizem cadastro, login e demonstrem interesse (Like) em vagas ou candidatos, gerando um **Match** automático quando há reciprocidade entre candidato e vaga.

A aplicação foi desenvolvida em **Groovy**, utilizando **Programação Orientada a Objetos**, **Interfaces** e o padrão **MVC (Model–View–Controller)**.

## Front-end

O front-end do projeto foi desenvolvido utilizando **HTML, CSS e TypeScript**, seguindo uma abordagem simples e organizada baseada em manipulação direta do DOM, sem o uso de frameworks. A interface é composta por páginas dinâmicas que se adaptam ao tipo de usuário (candidato ou empresa), com renderização condicional de conteúdos como perfil, vagas e matches. A navegação e as interações são controladas por controllers (como `CandidatoController` e `EmpresaController`), que centralizam a lógica de exibição e comportamento da interface. O sistema inclui funcionalidades como criação e edição de vagas, gerenciamento de perfil, sistema de likes e matches, além de visualização detalhada de candidatos e vagas. Também foram implementadas melhorias de usabilidade, como compatibilidade com dispositivos móveis, dropdowns dinâmicos e feedback visual para ações do usuário. O estado da aplicação é persistido localmente através do `localStorage`, garantindo uma experiência contínua mesmo após recarregar a página.

---

# Funcionalidades

### Candidato
- **Login e Cadastro:** Autenticação via e-mail e senha, com validação de e-mail único.
- **Perfil Próprio:** Visualização e edição dos dados da conta logada.
- **Gerenciamento de Competências:** CRUD completo de competências do perfil (adicionar, editar, excluir, listar).
- **Exploração de Vagas:** Navegação por vagas disponíveis uma a uma, com as ações:
  - **[L] Like:** Demonstrar interesse na vaga.
  - **[P] Próximo:** Pular para a próxima vaga.
  - **[S] Sair:** Retornar ao menu principal.
- **Sistema de Match:** Identificação em tempo real de match entre candidato e vaga.
- **Lista de Matches:** Exibição das vagas com match, incluindo os dados completos da empresa responsável.

### Empresa
- **Login e Cadastro:** Autenticação via e-mail e senha, com validação de e-mail único.
- **Perfil Próprio:** Visualização e edição dos dados da conta logada.
- **Gerenciamento de Vagas:** CRUD completo de vagas (criar, editar, excluir, listar).
- **Competências por Vaga:** Gerenciamento de competências dentro de cada vaga (adicionar, excluir, listar).
- **Exploração de Candidatos:** Visualização de candidatos um a um com dados restritos (descrição, competências, estado e CEP — sem nome, CPF, idade ou e-mail), com as ações:
  - **[L] Like:** Demonstrar interesse no candidato.
  - **[P] Próximo:** Pular para o próximo candidato.
  - **[S] Sair:** Retornar ao menu principal.
- **Sistema de Match:** Identificação em tempo real de match ao curtir um candidato.
- **Lista de Matches por Vaga:** Exibição das vagas com match e os candidatos correspondentes, com dados completos.

### Regra de Match
O match ocorre quando:
1. A empresa dá like no candidato.
2. O candidato dá like em uma vaga dessa mesma empresa.

O match é vinculado ao **id único da vaga**. Se a vaga for excluída, o match some automaticamente.

---

### Dados do Candidato
- Nome, E-mail, CPF, Idade, Estado, CEP, Descrição pessoal e Competências.

### Dados da Empresa
- Nome, E-mail corporativo, CNPJ, País, Estado, CEP e Descrição da empresa.

### Dados da Vaga
- Nome, Descrição, Horário, Localização, Remuneração e Competências exigidas.

---

## 🗄️ Banco de Dados

O banco de dados foi modelado utilizando **PostgreSQL**. A modelagem foi criada com o **draw.io**.

### Modelo Entidade-Relacionamento (DER)

![DER Linketinder](database/DER-linketinder.png)

### Tabelas
- `candidatos` — dados dos candidatos
- `empresas` — dados das empresas
- `vagas` — vagas criadas pelas empresas
- `competencias` — catálogo único de competências compartilhado entre candidatos e vagas
- `candidato_competencia` — relação N:N entre candidatos e competências
- `vaga_competencia` — relação N:N entre vagas e competências
- `likes_candidato` — likes dados por candidatos em vagas
- `likes_empresa` — likes dados por empresas em candidatos
- `matches` — matches gerados quando há reciprocidade

O script completo de criação e inserts está em [`database/linketinder.sql`](database/linketinder.sql).

---

## 🔌 Parte 2 — Integração com Banco de Dados (JDBC)

Nessa etapa foi desenvolvida a integração completa da aplicação Groovy com o banco de dados PostgreSQL utilizando **JDBC**, sem o uso de JPA/Hibernate.

### O que foi implementado

- **`ConexaoBanco.groovy`** — classe exclusiva para gerenciar a conexão com o banco via `DriverManager`
- **DAOs separados por entidade**, cada um responsável pelo CRUD da sua tabela:
  - `CandidatoDAO` — CRUD de candidatos + busca de competências via JOIN
  - `EmpresaDAO` — CRUD de empresas + buscas por id e email
  - `VagaDAO` — CRUD de vagas + busca de competências via JOIN
  - `CompetenciaDAO` — CRUD de competências + gerenciamento das relações N:N
- **Relação N:N** entre candidato e competência tratada via tabela `candidato_competencia`
- **Relação N:N** entre vaga e competência tratada via tabela `vaga_competencia`
- **Relação 1:N** entre empresa e vagas representada pelo campo `id_empresa` na tabela `vagas`
- **Estratégia `buscarOuInserir`** na tabela de competências — evita duplicatas e reutiliza competências já cadastradas
- **Remoção dos dados mock** (`DadosMock.groovy`) — a aplicação agora persiste tudo no banco
- **IDs gerados pelo banco** (`SERIAL`) substituem o contador em memória que existia nos models
- Arquivo `database/linketinder.sql` com criação das tabelas e inserção de 5 candidatos e 5 empresas fictícios

### Estrutura da camada DAO

```
src/main/groovy/linketinder/
└── dao/
    ├── ConexaoBanco.groovy
    ├── CandidatoDAO.groovy
    ├── CompetenciaDAO.groovy
    ├── EmpresaDAO.groovy
    └── VagaDAO.groovy
```

---

## 🛠️ Tecnologias Utilizadas

- **Groovy 4**
- **PostgreSQL**
- **JDBC** — integração entre aplicação e banco de dados
- **draw.io** — modelagem do banco de dados
- **Padrão MVC** — organização em Model, View e Controller
- **Spock Framework** — testes unitários para validação das regras de negócio
- **HTML5**
- **CSS3**
- **TypeScript**

---

## 💻 Ambiente de Desenvolvimento

- **SO:** Linux (Pop!_OS)

---


---

## 🔌 Microserviço de Cadastro

### O que foi implementado

Um microserviço independente de cadastro, sem uso de frameworks, que expõe dois endpoints HTTP para registrar candidatos e empresas diretamente no banco de dados PostgreSQL.

O microserviço possui seu próprio ponto de entrada (`CadastroMain`) e pode ser executado em paralelo à aplicação principal em uma segunda instância do IntelliJ.

### Estrutura

```
src/main/groovy/linketinder/microservice/
├── CadastroMain.groovy     ← ponto de entrada independente
├── CadastroServer.groovy   ← sobe o HttpServer na porta 8080 e injeta dependências
└── CadastroHandler.groovy  ← trata as requisições e chama os services
```

### Tecnologia utilizada

O servidor HTTP foi implementado com `com.sun.net.httpserver.HttpServer`, uma API nativa do JDK — sem nenhuma dependência externa ou framework. O parse e a serialização de JSON foram feitos com `JsonSlurper` e `JsonOutput`, módulos nativos do Groovy.

### Endpoints

| Método | Rota                  | Descrição                      |
|--------|-----------------------|--------------------------------|
| POST   | `/cadastro/candidato` | Cadastra um candidato no banco |
| POST   | `/cadastro/empresa`   | Cadastra uma empresa no banco  |

#### Exemplo — cadastrar candidato

```bash
curl -X POST http://localhost:8080/cadastro/candidato \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Silva",
    "email": "joao@email.com",
    "cpf": "123.456.789-00",
    "idade": 25,
    "estado": "SP",
    "cep": "01310-100",
    "descricao": "Desenvolvedor backend",
    "senha": "senha123"
  }'
```

#### Exemplo — cadastrar empresa

```bash
curl -X POST http://localhost:8080/cadastro/empresa \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Tech Corp",
    "email": "contato@techcorp.com",
    "cnpj": "12.345.678/0001-90",
    "pais": "Brasil",
    "estado": "SP",
    "cep": "01310-100",
    "descricao": "Empresa de tecnologia",
    "senha": "senha123"
  }'
```

#### Respostas

| Status | Situação                              |
|--------|---------------------------------------|
| 201    | Cadastro realizado, retorna o `id`    |
| 400    | E-mail já cadastrado ou dado inválido |
| 404    | Rota não encontrada                   |
| 405    | Método diferente de POST              |
| 500    | Erro interno inesperado               |

### Como executar o microserviço

1. No IntelliJ, clique com o botão direito em `CadastroMain.groovy`
2. Selecione **Run 'CadastroMain'**
3. O microserviço ficará disponível em `http://localhost:8080`

O microserviço pode rodar em paralelo à aplicação principal em uma segunda instância do IntelliJ.

### Sobre a integração com o frontend

A integração entre o frontend web e o microserviço foi explorada como item experimental. O frontend atualmente persiste os dados de cadastro via `localStorage`. Para integrar com o microserviço, o fluxo de cadastro do frontend precisaria enviar os dados via requisição HTTP para os endpoints acima, em vez de salvar localmente. O microserviço já inclui os headers de **CORS** necessários para aceitar requisições do browser. A integração completa não foi implementada pois é um requisito exploratório.

## 🏃 Como Executar

### Pré-requisitos
- PostgreSQL instalado e rodando
- Usuário `app_user` com acesso ao banco `linketinder`

### Back-end (Groovy)

### Passos
1. Clone o repositório:
```bash
git clone https://github.com/HenriqueRoberto/Linketinder-Project-k1-t5.git
```
2. Acesse a pasta do projeto:
```bash
cd Linketinder-Project
```
3. Crie o banco e configure as permissões:
```bash
sudo -u postgres psql
```
```sql
CREATE DATABASE linketinder;
CREATE USER app_user WITH PASSWORD '1234';
GRANT ALL PRIVILEGES ON DATABASE linketinder TO app_user;
GRANT ALL ON SCHEMA public TO app_user;
ALTER DATABASE linketinder OWNER TO app_user;
\q
```
4. Execute o script SQL para criar as tabelas e inserir os dados iniciais:
```bash
psql -U app_user -d linketinder -f database/linketinder.sql
```
5. Execute a aplicação:
```bash
./gradlew run
```
6. Para rodar os testes:
```bash
./gradlew test
```

### Front-end (HTML/CSS/TypeScript)

O front-end não funciona ao ser aberto diretamente no navegador — ele depende de um servidor local para carregar os módulos corretamente.

*Opção 1 — VS Code + Live Server (recomendado)*
1. Abra a pasta do projeto no **VS Code**
2. Instale a extensão **Live Server** (caso não tenha)
3. Clique com o botão direito em `front-end/app.html`
4. Selecione **"Open with Live Server"**

*Opção 2 — Node.js*
```bash
npx serve .
```
