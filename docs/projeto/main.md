##  __***Roteiro Do Projeto***__
## ETAPA 1

## Introdução
Este roteiro tem como objetivo apresentar nosso projeto de Computação em Nuvem, no qual desenvolvemos uma API com três endpoints conectados a um banco de dados MySQL. Para autenticação, utilizamos o sistema JWT (JSON Web Token). A API possui três funcionalidades principais: a criação de usuário, que permite cadastrar um novo usuário e, em caso de sucesso, gera e retorna um token JWT; o login, que permite que um usuário existente se autentique e receba um novo token JWT; e a consulta da cotação do dólar, que exige um token válido para acesso e, quando chamado com sucesso, retorna a cotação atual do dólar. A cotação do dólar é obtida via web scraping com requests e BeautifulSoup. O endpoint /consultar faz uma requisição GET para o site x-rates.com, simula um navegador com User-Agent e analisa o HTML retornado. O código procura links com from=USD&to=BRL, extrai os valores, converte para float e inclui um timestamp UTC. O resultado é retornado em JSON.


## Como executar a aplicação
Para executar a aplicação, siga os passos abaixo:
1. Certifique-se de ter o Docker instalado em sua máquina.
2. baixe ou crie um arquivo `docker-compose.yaml` com o conteúdo fornecido abaixo.
3. Execute o comando `docker-compose up --build` no terminal, dentro do diretório onde o arquivo `docker-compose.yaml` está localizado.



## Documentação dos Endpoints

### 1. Registrar Usuário

**Endpoint:** `POST /registrar`  
**Descrição:** Cria um novo usuário no sistema e retorna um token JWT em caso de sucesso.

**Request Body:**  
```json
{
  "nome": "string",
  "email": "string",
  "senha": "string"
}
```
**Response:**
200 - Sucesso
```json
{
  "access_token": "string",
  "token_type": "string"
}
```
422 - Erro de validação
```json
{
  "detail": [
    {
      "loc": ["string", 0],
      "msg": "string",
      "type": "string"
    }
  ]
}   
```

### 2. Login
**Endpoint:** `POST /login`  
**Descrição:** Autentica um usuário existente e retorna um token JWT em caso de sucesso.

**Request Body:**  
```json
{
  "email": "string",
  "senha": "string"
}
```
**Response:**
200 - Sucesso
```json
{
  "access_token": "string",
  "token_type": "string"
}
```
422 - Erro de validação
```json
{
  "detail": [
    {
      "loc": ["string", 0],
      "msg": "string",
      "type": "string"
    }
  ]
}
```

### 3. Consultar Cotação do Dólar
**Endpoint:** `GET /consultar`  
**Descrição:** Retorna a cotação do dólar (USD/BRL) em tempo real.  
**Requer autenticação** (token JWT válido no header Authorization).
**Request Headers:**


**Respostas:**

- **200 – Sucesso**
  ```json
  {
    "cotacao": 5.25,
    "timestamp": "2024-05-27T14:00:00Z"
  }

    ```
- **401 – Não autorizado**
```json
{
  "detail": "Token inválido ou ausente"
}
```

### 4. Health Check

**Endpoint:** `GET /health-check`  
**Descrição:** Verifica se a API está em funcionamento.

**Request Parameters:**  
Nenhum parâmetro necessário.

**Respostas:**

- **200 – Sucesso**
  


## screenshot com os endpoints testados;




## video de execução da aplicação 

# Comando para subir a imagem no Docker Hub
```bash
docker build -t tanaka7/projetonuvem_ianvitor2:latest ./api
docker push tanaka7/projetonuvem_ianvitor2:latest
```



## link para o docker hub do projeto;
https://hub.docker.com/r/tanaka7/projetonuvem_ianvitor2

## compose.yaml

```yaml
version: '3.8'

services:
  api:
    image: tanaka7/projetonuvem_ianvitor2:latest

    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=mysql+mysqlconnector://${MYSQL_USER}:${MYSQL_PASSWORD}@{MYSQL_HOST}:3306/${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER:-root}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD:-1412}
      - MYSQL_HOST=db
      - MYSQL_DATABASE=${MYSQL_DATABASE:-cloud}
      - SECRET_KEY=${SECRET_KEY:-09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7}
      - ALGORITHM=${ALGORITHM:-HS256}
      - ACCESS_TOKEN_EXPIRE_MINUTES=${ACCESS_TOKEN_EXPIRE_MINUTES:-30}
      - DEBUG=${DEBUG:-true}
    depends_on:
      db:
        condition: service_healthy

  db:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  mysql_data:
```












