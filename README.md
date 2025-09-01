## Objetivo do trabalho

O objetivo deste trabalho é desenvolver uma API capaz de integrar-se a sistemas externos para resolver um problema real de algum setor da sociedade. 

Com esse propósito, criamos a API que atenderá ao Web Site **ConectaBairro**, integrando-a a dois serviços externos:

- **API ViaCep**: utilizada para melhorar a experiência do usuário no preenchimento de endereços, tanto no cadastro quanto na busca por empreendimentos.
- **API OpenWeatherMap**: fornece informações sobre clima e umidade da cidade pesquisada. A integração com essa API foi pensada visando a escalabilidade do projeto, pois futuramente, ao atender também a um front-end, permitirá exibir essas informações diretamente aos usuários quando acessarem a página.


## 1. Visão Geral

Muitas vezes nos deslocamos por grandes distâncias em busca de um serviço ou produto que estamos precisando. No entanto, na maioria das vezes, ali pertinho — ou mesmo um pouco mais longe, mas ainda dentro do mesmo bairro — há alguém que oferece exatamente aquilo que procuramos, seja um serviço ou um produto.
<br>

Pensando nisso, idealizamos o ConectaBairro, que tem como objetivo simplificar a forma como os moradores encontram serviços e empreendimentos locais. A proposta promove a economia colaborativa e ajuda pequenos empreendedores — e até mesmo pequenas empresas — a conquistarem maior visibilidade junto aos moradores da região onde estão localizados.
<br>

A ideia resgata a função que, antigamente, era desempenhada pelos "jornalzinhos de bairro", agora em formato digital e com acesso via web. Empresas e empreendedores podem cadastrar seus empreendimentos para divulgar seus serviços e produtos, enquanto usuários comuns podem consultar opções próximas de forma fácil e eficiente — evitando deslocamentos desnecessários e fortalecendo a economia local.

---

## 📋 Sumário

1. [Visão Geral](#1-visão-geral)  
2. [Funcionalidades](#2-funcionalidades)  
3. [Tecnologias Utilizadas](#3-tecnologias-utilizadas)  
4. [Arquitetura do Projeto](#4-arquitetura-do-projeto)  
5. [Instalação e Execução](#5-instalação-e-execução)  
6. [Testando os Endpoints Principais](#6-testando-os-endpoints-principais)  
7. [Integração com API de Clima](#7-integração-com-api-de-clima)  
8. [Validações](#8-validações)  
9. [Respostas da API](#9-respostas-da-api)  
10. [Contribuindo](#10-contribuindo)  
11. [Licença](#11-licença) 


---






## 2. Funcionalidades

- Cadastro e login de usuários 
- Cadastro de empreendimentos e serviços prestados 
- Listagem de empreendimentos por CEP, rua, bairro, cidade, estado ou palavras-chave  
- Integração com API ViaCEP para autocompletar endereço  
- Integração com API OpenWeather para consulta do clima por cidade  
- Operações CRUD para empreendimentos (criar, listar, editar, deletar)  
- Rotas protegidas por JWT  
- Diferenciação de permissões (usuário comum vs usuário que cadastrou o empreendimento)  

---

## 3. Tecnologias Utilizadas

- **Node.js** + **Express**  
- **MongoDB** + **Mongoose**  
- **JWT (JSON Web Token)** para autenticação  
- **bcryptjs** para hash de senhas  
- **Fetch API** para consumo de APIs externas (ViaCEP, OpenWeather)  
- **Jest** + **Supertest** para testes automatizados  
- **MongoMemoryServer** para testes sem banco real  
- **Nodemon** para desenvolvimento  

---

## 4. Arquitetura do Projeto

O backend foi baseado na arquitetura MVC (Model-View-Controller) e adaptado para uma API RESTful em Node.js:

A estrutura de pastas segue o seguinte modelo:

```plaintext
EMPREENDIMENTOSAPI/
├─ docs/
│   └─ architecture.md                  # Documentação técnica e diagramas da
                                          arquitetura
├─ node_modules/                        # Dependências instaladas via npm
├─ postman/                             # Coleções e ambientes para testes manuais
                                          da API
├─ src/
│   ├─ config/
│   │   └─ db.js                        # Conexão com MongoDB Atlas usando Mongoose
│   ├─ controllers/                     # Lógica das requisições HTTP
│   │   ├─ cepController.js             # Busca empreendimentos por CEP via ViaCEP
│   │   ├─ climaController.js           # Consulta clima atual via OpenWeather
│   │   ├─ empreendimentosController.js # CRUD de empreendimentos
│   │   └─ usuariosController.js        # Cadastro e login de usuários
│   ├─ middleware/
│   │   └─ authMiddleware.js            # Verificação de token JWT e proteção de
                                          rotas
│   ├─ models/
│   │   ├─ Empreendimento.js            # Schema do empreendimento com campos
                                          normalizados
│   │   └─ Usuario.js                   # Schema do usuário com criptografia de
                                          senha
│   ├─ routes/
│   │   ├─ cep.js                       # Rota para busca por CEP
│   │   ├─ climaRoutes.js               # Rota para consulta de clima
│   │   ├─ empreendimentos.js           # Rotas de CRUD de empreendimentos
│   │   └─ usuarios.js                  # Rotas de cadastro e login
│   ├─ services/
│   │   └─ viaCepService.js             # Serviço que consome a API ViaCEP
│   ├─ utils/
│   │   └─ funcoesUtils.js              # Funções auxiliares para filtros e
                                          normalização
│   ├─ app.js                           # Configuração principal do Express e rotas
│   └─ server.js                        # Inicialização do servidor na porta
                                          definida
├─ testes
    ├─ endpoints.test.js                # Testes automatizados com Jest e Supertest
    ├─ setupTestDB.js                   # Banco em memória para testes isolados
├─ .env                                 # Variáveis de ambiente (PORT, MONGO_URI,
                                          JWT_SECRET, OPENWEATHER_API_KEY)
├─ jest.config.js                       # Configuração do ambiente de testes
├─ package-lock.json                    # Controle de versões das dependências
├─ package.json                         # Dependências e scripts do projeto
└─ README.md                            # Documentação principal do projeto


```

## Explicação da Arquitetura da API 

Essa arquitetura segue uma abordagem modular e escalável, ideal para aplicações REST modernas. 

## 📌 Diagrama da arquitetura

<img src="docs/img/diagramaAPI.png" alt="Diagrama" width="550">

<br> 

<b>Aqui está o detalhamento de cada parte:</b>

#### 1 - Cliente/API

🔹 Representa qualquer consumidor da API — pode ser um frontend web, aplicativo mobile ou ferramentas como Postman. Eles enviam requisições HTTP para os endpoints da API.

#### 2 - Express Server (server.js)

🔹 É o ponto de entrada da aplicação. Inicializa o servidor Express, carrega variáveis de ambiente, conecta ao banco de dados (exceto em testes), e define as rotas da API.

#### 3 - Rotas (routes/)

🔹 Cada rota define os caminhos da API e delega a lógica para os controllers correspondentes:

```
/usuarios → Cadastro e login de usuários

/empreendimentos → CRUD de empreendimentos (com autenticação)

/externo/cep → Busca de empreendimentos por CEP via ViaCEP

/api/clima → Consulta de clima via OpenWeather
```

#### 4 - Controllers (controllers/)

🔹 Contêm a lógica de negócio:

```
usuariosController.js: Cadastro e login com JWT

empreendimentosController.js: Listagem, criação, edição e exclusão de empreendimentos

cepController.js: Integração com ViaCEP

climaController.js: Integração com OpenWeather
```

#### 5 - Middleware (middleware/)

🔹 authMiddleware.js: Protege rotas sensíveis usando JWT. Verifica se o token é válido e injeta o usuário na requisição.

#### 6 - Services (services/)

🔹 viaCepService.js: Abstrai a chamada à API ViaCEP, retornando dados de endereço a partir de um CEP.

#### 7 - Models (models/)

🔹 Definem os esquemas do MongoDB:

```
Usuario.js: Contém nome, email e senha (criptografada)

Empreendimento.js: Contém dados do empreendimento e campos normalizados para facilitar buscas
```

#### 8 - Banco de Dados

🔹 MongoDB é usado como banco principal:

```
Produção: MongoDB Atlas

Testes: MongoMemoryServer (banco em memória para testes isolados)
```

#### 9 - Testes Automatizados (test/)

🔹 Utilizam Jest + Supertest para validar o fluxo completo:

```
Cadastro e login de usuários

CRUD de empreendimentos

Proteção de rotas

Banco em memória garante testes limpos e independentes
```

---


## 5. Instalação e Execução

Antes de começar, certifique-se de ter instalado:

- [Node.js](https://nodejs.org/) (versão 18 ou superior recomendada)
- Um editor de código, como [Visual Studio Code](https://code.visualstudio.com/)

<br>

Siga os passos abaixo para instalar e rodar o projeto localmente:

### Clone o repositório

```bash
git clone https://github.com/seu-usuario/conectabairro.git
cd conectabairro 
```
### Instale as dependências

```
npm install
```
### Configure as variáveis de ambiente 

Crie um arquivo .env na raiz do projeto com o seguinte conteúdo:

```
PORT=3000
MONGO_URI=coloque_sua_string_de_conexao_do_mongodb_atlas
JWT_SECRET=sua_chave_secreta_para_token
OPENWEATHER_API_KEY=sua_chave_da_api_openweather

``` 
💡 Dica: você pode obter a chave da OpenWeather em https://openweathermap.org/api

### Execute o servidor em modo desenvolvimento

```
npm run dev
```
Obs: O servidor será iniciado em http://localhost:3000


### Scripts disponíveis

| Script         | Descrição                                                                 |
|----------------|---------------------------------------------------------------------------|
| `npm run dev`  | Inicia o servidor com **nodemon**                                         |
| `npm start`    | Inicia o servidor com **Node.js padrão**                                  |
| `npm test`     | Executa os testes automatizados com **Jest** e **Supertest** usando banco em memória |


---

## 6. Testando os Endpoints Principais

Você pode testar a API utilizando ferramentas como **Postman** / **Insomnia ou similares** 

- **Headers:**
  - `Content-Type: application/json`
  - `Authorization: Bearer SEU_TOKEN_JWT` (somente para rotas protegidas)

- **Body:**
  - Selecione a opção `raw`
  - Escolha o tipo `JSON`
  - Insira os dados conforme o exemplo da rota

  
### Tabela de Endpoints Principais

| Método | Rota                        | Descrição                              | Protegida | Exemplo de uso |
|--------|-----------------------------|----------------------------------------|-----------|--------------------|
| POST   | `/usuarios/cadastroUsuario` | Cadastro de novo usuário               | Não       | [Exemplo de uso](#ao-cadastrar-usuário) |
| POST   | `/usuarios/login`           | Autenticação e geração de token JWT    | Não       | [Exemplo de uso](#ao-realizar-login) |
| POST   | `/empreendimentos`          | Cadastro de empreendimento             | Sim       | [Exemplo de uso](#ao-cadastrar-empreendimento-rota-protegida) |
| PUT    | `/empreendimentos/:id`      | Edição de empreendimento               | Sim       | [Exemplo de uso](#ao-editar-empreendimento-rota-protegida) |
| DELETE | `/empreendimentos/:id`      | Exclusão de empreendimento             | Sim       | [Exemplo de uso](#ao-deletar-empreendimento-rota-protegida) |
| GET    | `/empreendimentos`          | Listagem e busca de empreendimentos    | Não       | [Exemplo de uso](#ao-listar-e-buscar-empreendimentos-rota-pública) |


### 🔐 Autenticação

Algumas rotas são protegidas e só podem ser acessadas com token JWT válido, que é gerado após o login do usuário.

> ⚠️ Rotas públicas, como login e cadastro de usuário, não exigem o token JWT.



### Cadastro de usuário (rota pública)

Para cadastrar um novo usuário, siga os passos abaixo:

1. Utilize o método POST na rota: 
```/usuarios/cadastroUsuario```

2. Insira os dados no seguinte formato:

```json
{
  "nome": "Teste",
  "email": "teste@email.com",
  "senha": "123456"
}
```
A senha deve conter no mínimo 6 caracteres e o e-mail não deve ter sido cadastrado anteriormente.

<b>[Veja a resposta esperada aqui](#cadastrar-usuário)

### Login

Para realizar o login, siga os passos abaixo:

1. Utilize o método POST na rota:
``` /usuarios/login```

2. Insira os dados no seguinte formato:

```json
{
  "nome": "Teste",
  "email": "teste@email.com",
  "senha": "123456"
}
```
O <b>token JWT </b> será retornado na resposta. Copie e use nas rotas protegidas:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

[Veja a resposta esperada aqui](#login)

## Endpoins de Empreendimentos


### Cadastrar empreendimento (rota protegida)

1. Utilize o método POST na rota: 
```/empreendimentos```

2. Insira o token JWT no header da requisição:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

3. Insira os dados no seguinte formato:

```json
{
  "nome": "Peixaria do Seu Zé",
  "descricao": "Comércio de frutos do mar",
  "endereco": {
    "cep": "60180-900",
    "rua": "Rua Benedito Macêdo",
    "bairro": "Cais do Porto",
    "numero": "100",
    "complemento": "",
    "cidade": "Fortaleza",
    "estado": "CE"
  },
  "telefone": "85998989999",
  "email": "peixe_do_ze@teste.com",
  "palavrasChave": [
    "peixe",
    "camarão",
    "frutos do mar",
    "polvo"
  ]
}
```

 💡 É possível cadastrar usando apenas o cep da rua desejada, pois a API fará uma chamada para a API ViaCEP que autocompletará o cadastro preenchendo os campos "rua", "bairro", "cidade" e "estado":

```json
{
  "nome": "Peixaria do Seu Zé",
  "descricao": "Comércio de frutos do mar",
  "endereco": {
    "cep": "60180-900",
    "rua": "",
    "bairro": "",
    "numero": "100",
    "complemento": "",
    "cidade": "",
    "estado": ""
  },
  "telefone": "85998989999",
  "email": "peixe_do_ze@teste.com",
  "palavrasChave": [
    "peixe",
    "camarão",
    "frutos do mar",
    "polvo"
  ]
}
```

[Veja a resposta esperada aqui](#cadastrar-empreendimento)

### Editar empreendimento (rota protegida)

1. Utilize o método PUT na rota: 

```/empreendimentos/:id```

<b>Substitua :id pelo ID do empreendimento que deseja editar.

2. Insira o token JWT no header da requisição:

```http
Authorization: Bearer SEU_TOKEN_JWT
```

3. Insira os dados no seguinte formato:

✅ Exemplo 1:
```json
{
  "descricao": "Comércio de mariscos"
}
```

✅ Exemplo 2: Atualizar apenas o número do endereço

```json
{
  "endereco": {
    "numero": "300"
  }
}
```

✅ Exemplo 3: Atualizar a rua do endereço

```json
{
  "endereco": {
    "rua": "Rua Lindoya"
  }
}
```

✅ Exemplo 4: Atualizar múltiplos campos ao mesmo tempo

```json
{
  "descricao": "Comércio de mariscos",
  "telefone": "85999999999",
  "endereco": {
    "rua": "Rua Lindoya",
    "numero": "300"
  },
  "palavrasChave": ["praia", "fortaleza", "mar"]
}
```

[Veja a resposta esperada aqui](#editar-empreendimento)

⚠️ Importante: Se você alterar o campo endereco.cep, a API irá buscar automaticamente os dados atualizados (rua, bairro, cidade, estado) usando a integração com a API ViaCEP.


### Deletar empreendimento (rota protegida)

1. Utilize o método DELETE na rota:
``` /empreendimentos/:id```

2. Insira o token JWT no header da requisição:

```http
Authorization: Bearer SEU_TOKEN_JWT
``` 

Exemplo:

```/empreendimentos/idQueDesejaExcluir```

[Veja a resposta esperada aqui](#deletar-empreendimento)

### Listar e Buscar Empreendimentos (rota pública)

1. Utilize o método GET na rota: 
```
/empreendimentos
```

2. Para realizar buscas específicas, utilize os filtros via query params:

| Filtro            | Exemplo de Requisição                                         | Descrição                            |
|-------------------|---------------------------------------------------------------|---------------------------------------|
| Cidade + Bairro    | `GET /empreendimentos?cidade=cidadeDesejadaAqui&bairro=bairroDesejadoAqui` | Busca empreendimentos por cidade e bairro |
| CEP                | `GET /empreendimentos?cep=cepDesejadoAqui`                    | Busca empreendimentos pelo CEP         |
| Rua                | `GET /empreendimentos?rua=ruaDesejadaAqui`                    | Busca empreendimentos pela rua         |
| Bairro             | `GET /empreendimentos?bairro=bairroDesejadoAqui`              | Busca empreendimentos pelo bairro      |
| Cidade             | `GET /empreendimentos?cidade=cidadeDesejadaAqui`              | Busca empreendimentos pela cidade      |
| Estado             | `GET /empreendimentos?estado=estadoDesejadoAqui`              | Busca empreendimentos pelo estado      |
| Palavra-chave       | `GET /empreendimentos?palavra=palavraDesejadaAqui`            | Busca empreendimentos por palavra-chave |

<br>

---
## 7. Integração com API de Clima

A API está integrada ao serviço **OpenWeather**, permitindo consultar o clima atual de qualquer cidade informada.

Embora essa funcionalidade ainda tenha uso limitado no backend, ela foi implementada pensando na futura integração com o frontend. A ideia é que, ao acessar a plataforma, o usuário visualize automaticamente o clima da sua cidade, oferecendo uma experiência mais contextualizada e útil.

Essa integração também cumpre o requisito de comunicação com sistemas externos, e está pronta para ser expandida conforme o projeto evoluir.

### Exemplo de uso:

```http
GET /api/clima?cidade=Fortaleza
```

---

## 8. Validações

A API realiza validações nos dados enviados para garantir integridade, segurança e consistência. Abaixo estão as principais regras aplicadas:

### 👤 Cadastro de Usuário

- `nome`: obrigatório
- `email`: obrigatório, deve ser único e válido
- `senha`: obrigatório, mínimo de 6 caracteres
- Verificação de duplicidade de email antes de criar o usuário
- Criptografia da senha com bcrypt antes de salvar no banco

### 🔑 Login de Usuário

- `email` e `senha`: obrigatórios
- Verificação de credenciais com comparação segura usando bcrypt

### 🏢 Cadastro de Empreendimento

- `nome`: obrigatório
- `descricao`: obrigatório
- `endereco.cep`: obrigatório e validado antes de chamar a API ViaCEP
- Campos como `rua`, `bairro`, `cidade` e `estado` podem ser deixados em branco, pois são preenchidos automaticamente via ViaCEP

### 🔐 Autorização

- Rotas protegidas exigem token JWT válido
- Verificação se o usuário autenticado é o criador do empreendimento antes de permitir edição ou exclusão

---

## 9. Respostas da API

A API retorna mensagens claras para facilitar o consumo e o tratamento de erros. Abaixo estão alguns exemplos de respostas que podem ser esperadas:

### ✅ Respostas de sucesso



#### Ao Cadastrar Usuário

```json
{
    "mensagem": "Usuário cadastrado com sucesso",
    "_id": "68b4d4cdb98dfb4abe6addff",
    "nome": "Testando Usuário",
    "email": "teste@teste.com",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjY4YjRkNGNkYjk4ZGZiNGFiZTZhZGRmZiIsImlhdCI6MTc1NjY4MTQyMiwiZXhwIjoxNzU2NzY3ODIyfQ.SW-PKqXcy0_yxIxKblU_Qo2NEWXuVdfjMcH0TpNA0sU"
}
```
[Veja o exemplo de requisição em Cadastrar Usuário](#cadastro-de-usuário-rota-pública)

#### Ao realizar Login

```json
{
    "_id": "689fb6a91270e26fb9e6b14a",
    "nome": "Teste Usuário",
    "email": "teste@teste.com",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjY4OWZiNmE5MTI3MGUyNmZiOWU2YjE0YSIsImlhdCI6MTc1NjMwNTA1NSwiZXhwIjoxNzU2MzkxNDU1fQ.fzb-UdhcmNu7kTmfQWxD6WATpujEIxfU2Ey8bb-AEMU"
}
```
[Veja o exemplo de requisição em Login](#login)

#### Ao Cadastrar Empreendimento

Ex:

```json
{
    "mensagem": "Empreendimento cadastrado com sucesso",
    "empreendimento": {
        "nome": "Peixaria do Seu Zé",
        "descricao": "Comércio de frutos do mar",
        "endereco": {
            "cep": "60180900",
            "rua": "Rua Benedito Macêdo",
            "bairro": "Cais do Porto",
            "numero": "100",
            "complemento": "",
            "cidade": "Fortaleza",
            "estado": "CE"
        },
        "telefone": "85998989999",
        "email": "peixe_do_ze@teste.com",
        "palavrasChave": [
            "peixe",
            "camarão",
            "frutos do mar",
            "polvo"
        ],
        "cidadeNormalizada": "fortaleza",
        "bairroNormalizado": "cais do porto",
        "palavrasChaveNormalizadas": [
            "peixe",
            "camarao",
            "frutos do mar",
            "polvo"
        ],
        "criadoPor": "68b4e46d43368a8855a1d76a",
        "_id": "68b4e88dbed3289d2d46e6d3",
        "createdAt": "2025-09-01T00:27:57.178Z",
        "updatedAt": "2025-09-01T00:27:57.178Z",
        "__v": 0
    }
}
```

[Veja o exemplo de requisição em Cadastrar Empreendimento](#cadastrar-empreendimento-rota-protegida)

#### Ao Editar Empreendimento

```json
{
    "mensagem": "Empreendimento atualizado com sucesso",
    "empreendimento": {
        "endereco": {
            "cep": "60180650",
            "rua": "Rua Lindoya",
            "bairro": "Cais do Porto",
            "numero": "300",
            "complemento": "",
            "cidade": "Fortaleza",
            "estado": "CE"
        },
        "_id": "68af1af559c823c81a7ba1f3",
        "nome": "Peixaria do seu Zé",
        "descricao": "Comércio de mariscos",
        "telefone": "85999999999",
        "email": "peixe_do_Ze@teste.com",
        "palavrasChave": [
            "peixe",
            "camarão",
            "frutos do mar",
            "polvo"
        ],
        "cidadeNormalizada": "fortaleza",
        "bairroNormalizado": "cais do porto",
        "palavrasChaveNormalizadas": [
            "peixe",
            "camarao",
            "frutos do mar",
            "polvo"
        ],
        "criadoPor": "689fb6a91270e26fb9e6b14a",
        "createdAt": "2025-08-27T14:49:25.334Z",
        "updatedAt": "2025-08-27T14:49:58.836Z",
        "__v": 0
    }
}
```

[Veja o exemplo de requisição em Editar Empreendimento](#editar-empreendimento-rota-protegida)

#### Ao Deletar Empreendimento

```json
{
    "mensagem": "Empreendimento deletado com sucesso"
}
```

[Veja o exemplo de requisição em Deletar Empreendimento](#deletar-empreendimento-rota-protegida)


### ⚠️ Em caso de erro, a API retorna mensagens padronizadas como:

```json
{
  "erro": "Usuário já cadastrado"
}
```
```json
{
  "erro": "Credenciais inválidas"
}
```
```json
{
  "erro": "Token inválido ou expirado"
}
```
```json
{
  "erro": "Usuário não autorizado para esta ação"
}
```
```json
{
  "erro": "Erro ao editar empreendimento",
  "camposObrigatorios": [
    "endereco.rua",
    "endereco.bairro",
    "endereco.cidade",
    "endereco.estado"
  ],
  "mensagem": "Os seguintes campos são obrigatórios e não podem estar em branco: endereco.rua, endereco.bairro, endereco.cidade, endereco.estado"
}
```
<br>

---

## 10. Contribuindo

Contribuições são bem-vindas! Para colaborar com o projeto:

1. Faça um fork deste repositório
2. Crie uma branch: `git checkout -b minha-feature`
3. Faça suas alterações e commit: `git commit -m 'Minha contribuição'`
4. Envie um pull request

Sinta-se à vontade para abrir issues com sugestões ou melhorias!

---

## 11. Licença

Este projeto está licenciado sob os termos da [MIT License](LICENSE).

