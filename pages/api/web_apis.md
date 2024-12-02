# 🚀 Diretrizes e Boas Práticas para APIs da QBem

Este documento apresenta as diretrizes e boas práticas para o desenvolvimento de APIs da QBem, com foco em APIs RESTful e Search Engine (Typesense), garantindo uma experiência de desenvolvedor (DX) positiva e APIs consistentes e seguras.

## Índice
1. [Idioma Padrão](#idioma-padrão)
2. [APIs RESTful](#apis-restful)
   - [URLs RESTful](#urls-restful)
   - [Métodos HTTP](#métodos-http)
   - [Códigos de Status](#códigos-de-status)
   - [Metadados e Cabeçalhos](#metadados-e-cabeçalhos)
   - [Versionamento](#versionamento)
3. [APIs de Search Engine (Typesense)](#apis-de-search-engine-typesense)
   - [URLs para Busca](#urls-para-busca)
   - [Filtros e Parâmetros de Consulta](#filtros-e-parâmetros-de-consulta)
   - [Cabeçalhos e Boas Práticas](#cabeçalhos-e-boas-práticas)
4. [Segurança 🔒](#boas-práticas-de-segurança)
5. [Documentação 📚](#documentação-da-api)
6. [Autenticação 🛡️](#autenticação-com-bearer-token)

---

## 🌍 Idioma Padrão

Todas as **mensagens e respostas** devem ser em **inglês** para garantir a consistência e a acessibilidade internacional.

**Exemplo de Endpoint:**
```http
GET http://api.qbem.net.br/v1/operators
```

**Exemplo de Erro:**
```json
{
  "error": "Invalid request parameter",
  "message": "The field 'created_at' is required."
}
```

[Voltar ao Índice](#índice)

---

## 🛠️ APIs RESTful

As APIs RESTful devem ser simples, claras e consistentes.

### 📍 URLs RESTful

1. **Use substantivos, não verbos**: Exemplo: `/operators`, não `/getOperators`.
2. **Nomes no plural**: Exemplo: `/operators`.
3. **Hierarquia de recursos simples**: Evite URLs muito aninhadas.
4. **Versão na URL**: Exemplo: `http://api.qbem.net.br/v1/operators`.

**Exemplo de URLs:**

- Listar Operadoras:
  ```http
  GET http://api.qbem.net.br/v1/operators
  ```
- Filtrar por data e status:
  ```http
  GET http://api.qbem.net.br/v1/operators?created_at=2011&active=true
  ```

### 🔧 Métodos HTTP

- **POST**: Criar (`POST /operators`)
- **GET**: Ler (`GET /operators`)
- **PUT**: Atualizar (`PUT /operators/1234`)
- **DELETE**: Excluir (`DELETE /operators/1234`)

### 🏷️ Códigos de Status

| Código | Descrição           |
|--------|---------------------|
| 200    | OK                  |
| 201    | Created             |
| 400    | Bad Request         |
| 404    | Not Found           |
| 500    | Internal Server Error|

### 🛠️ Metadados e Cabeçalhos

- **X-Request-Id**: ID único da requisição (UUIDv4).
- **X-Content-Size**: Número total de itens (para paginação).

### 🔄 Versionamento

- **Na URL**: `http://api.qbem.net.br/v1/operators`
- **No Cabeçalho**: `X-API-Version: v1`

[Voltar ao Índice](#índice)

---

## 🔍 APIs de Search Engine (Typesense)

As APIs de Search Engine utilizam Typesense para buscas rápidas.

### 📍 URLs para Busca

- **Busca em coleções**: `/collections/{collection}/documents/search`
- **Operações com documentos**: Adicionar, atualizar ou excluir documentos.

**Exemplos de URLs:**

- Buscar com filtro:
  ```http
  GET http://search.qbem.net.br/v1/collections/operators/documents/search?filter_by=active:true
  ```

### 🔍 Filtros e Parâmetros

- **filter_by**: Filtro condicional. Exemplo: `filter_by=created_at:>2020`.
- **sort_by**: Ordenação. Exemplo: `sort_by=name:desc`.

**Exemplo de Busca Completa:**
```http
GET http://search.qbem.net.br/v1/collections/operators/documents/search?query_by=name&filter_by=active:true&sort_by=created_at:desc
```

### 📊 Cabeçalhos e Boas Práticas

- **X-Search-Request-Id**: ID único para rastreamento.
- **X-Result-Count**: Contagem de resultados.

[Voltar ao Índice](#índice)

---

## 🔒 Boas Práticas de Segurança

- **OAuth 2.0 ou JWT**: Para autenticação e autorização.
- **Rate Limiting**: Limite de requisições por IP.
- **Sanitização de Entrada**: Proteção contra injeção de código.
- **HTTPS**: Todas as requisições devem ser feitas via HTTPS.

[Voltar ao Índice](#índice)

---

## 📚 Documentação da API

Toda a documentação deve estar na pasta `docs`, com exemplos de requisições e respostas.

### 🗂️ Estrutura da Pasta `docs`

```
/docs
 ├── requests.http               # Exemplos de requisições
 ├── postman_collection.json      # Coleção para Postman
 └── README.md                    # Instruções de uso
```

### 💡 Boas Práticas para a Documentação

- Mantenha exemplos atualizados.
- Seja consistente no formato de endpoints.

[Voltar ao Índice](#índice)

---

## 🛡️ Autenticação com Bearer Token

### Passo 1: Obter o Bearer Token

```http
POST https://<casdoor-domain>/api/login
```

**Corpo da Requisição**:
```json
{
  "username": "your-username",
  "password": "your-password",
  "client_id": "your-client-id",
  "client_secret": "your-client-secret",
  "grant_type": "password"
}
```

**Resposta de Sucesso**:
```json
{
  "access_token": "your-bearer-token",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

### Passo 2: Configurar o Token nas Requisições

**Cabeçalho de Autorização**:
```http
Authorization: Bearer your-bearer-token
```

**Exemplo de Chamada Autenticada**:
```http
GET http://api.qbem.net.br/v1/operators
Authorization: Bearer your-bearer-token
```

[Voltar ao Índice](#índice)
