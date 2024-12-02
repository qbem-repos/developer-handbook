# 🚀 **Diretrizes e Boas Práticas para APIs da QBem**

Este documento estabelece diretrizes e práticas recomendadas para o desenvolvimento e consumo das APIs da QBem, incluindo **APIs RESTful** e **Search Engine (Typesense)**. Ele foca na criação de APIs intuitivas, seguras e consistentes, com uma experiência de desenvolvedor (DX) positiva.

---

## **Índice**

1. [Idioma Padrão](#idioma-padrão)
2. [APIs RESTful](#apis-restful)
   - [Estrutura de URLs](#estrutura-de-urls)
   - [Métodos HTTP](#métodos-http)
   - [Códigos de Status HTTP](#códigos-de-status-http)
   - [Cabeçalhos e Metadados](#cabeçalhos-e-metadados)
   - [Versionamento](#versionamento)
3. [APIs de Search Engine (Typesense)](#apis-de-search-engine-typesense)
   - [Estrutura de URLs para Busca](#estrutura-de-urls-para-busca)
   - [Parâmetros de Consulta](#parâmetros-de-consulta)
   - [Cabeçalhos e Boas Práticas](#cabeçalhos-e-boas-práticas)
4. [Segurança 🔒](#segurança)
5. [Documentação 📚](#documentação)
6. [Autenticação 🛡️](#autenticação)

---

## 🌍 **Idioma Padrão**

- **Padrão**: Toda comunicação deve ser em **inglês**, incluindo mensagens de erro e campos de respostas.
- **Racional**: Garante acessibilidade global e consistência em ambientes internacionais.

**Exemplo de Resposta de Erro**:
```json
{
  "error": "Invalid request parameter",
  "message": "The field 'created_at' is required."
}
```

[Voltar ao Índice](#índice)

---

## 🛠️ **APIs RESTful**

As APIs RESTful devem ser projetadas para serem simples, previsíveis e consistentes, seguindo os princípios REST.

### 📍 **Estrutura de URLs**

- **Use substantivos no plural**: Evite verbos. Exemplo: `/operators`, não `/getOperators`.
- **Mantenha hierarquia clara**: Exemplo: `/operators/{operator_id}/tasks`.
- **Inclua a versão na URL**: Facilita o gerenciamento de versões. Exemplo: `/v1`.

**Exemplo de Endpoints:**
- Listar Operadoras:
  ```http
  GET http://api.qbem.net.br/v1/operators
  ```
- Detalhar uma Operadora:
  ```http
  GET http://api.qbem.net.br/v1/operators/{operator_id}
  ```

### 🔧 **Métodos HTTP**

Cada método HTTP deve representar a ação correspondente no recurso:

| Método  | Descrição                  | Exemplo                      |
|---------|----------------------------|------------------------------|
| `GET`   | Ler recursos               | `/operators`                 |
| `POST`  | Criar um novo recurso      | `/operators`                 |
| `PUT`   | Atualizar recurso (completo) | `/operators/{operator_id}`   |
| `PATCH` | Atualizar recurso (parcial) | `/operators/{operator_id}`   |
| `DELETE`| Excluir recurso            | `/operators/{operator_id}`   |

### 🏷️ **Códigos de Status HTTP**

Utilize códigos de status padronizados para comunicar o resultado das operações:

| Código | Descrição                     |
|--------|-------------------------------|
| 200    | Sucesso (requisição OK)       |
| 201    | Recurso criado                |
| 204    | Sem conteúdo                  |
| 400    | Erro de requisição (cliente)  |
| 401    | Não autorizado                |
| 403    | Proibido                      |
| 404    | Não encontrado                |
| 500    | Erro no servidor              |

### 🛠️ **Cabeçalhos e Metadados**

- **X-Request-Id**: ID único para rastrear a requisição.
- **X-Content-Size**: Tamanho total da resposta (útil em paginação).
- **Content-Type**: Sempre inclua `application/json` para respostas JSON.

### 🔄 **Versionamento**

Utilize versionamento explícito para evitar quebra de compatibilidade em mudanças:

- **Na URL**: `/v1/operators`
- **Nos Cabeçalhos** (opcional): `X-API-Version: 1`

[Voltar ao Índice](#índice)

---

## 🔍 **APIs de Search Engine (Typesense)**

APIs de Search Engine utilizam o Typesense para oferecer buscas rápidas e eficientes.

### 📍 **Estrutura de URLs para Busca**

- **Busca em coleções**: Utilize o endpoint `/collections/{collection}/documents/search` para consultas.
- **Operações com documentos**: Adicione, atualize ou exclua documentos diretamente nas coleções.

**Exemplo de Busca com Filtro**:
```http
GET http://search.qbem.net.br/v1/collections/operators/documents/search?query_by=name&filter_by=active:true
```

### 🔍 **Parâmetros de Consulta**

- **query_by**: Campos que serão utilizados para a busca.
- **filter_by**: Condições para filtrar os resultados. Exemplo: `filter_by=active:true`.
- **sort_by**: Ordenação dos resultados. Exemplo: `sort_by=created_at:desc`.

**Exemplo Completo de Busca**:
```http
GET http://search.qbem.net.br/v1/collections/operators/documents/search?query_by=name&filter_by=active:true&sort_by=created_at:desc
```

### 📊 **Cabeçalhos e Boas Práticas**

- **X-Search-Request-Id**: ID único da requisição para auditoria.
- **X-Result-Count**: Número total de resultados encontrados.

[Voltar ao Índice](#índice)

---

## 🔒 **Segurança**

- **Autenticação**: Utilize OAuth 2.0 ou JWT para validar e autorizar requisições.
- **HTTPS**: Todas as APIs devem operar exclusivamente sobre HTTPS.
- **Rate Limiting**: Implemente limites de requisição por IP para evitar abusos.
- **Sanitização**: Filtre e valide entradas para evitar injeções de código ou dados maliciosos.

[Voltar ao Índice](#índice)

---

## 📚 **Documentação**

Documente suas APIs de forma clara e detalhada, fornecendo exemplos para desenvolvedores.

### 🗂️ **Estrutura Recomendada**

```
/docs
 ├── api_reference.md           # Referência técnica das APIs
 ├── requests.http              # Exemplos de chamadas HTTP
 ├── postman_collection.json    # Coleção do Postman para testes
 └── README.md                  # Guia de introdução
```

### 💡 **Boas Práticas para Documentação**

1. Inclua exemplos reais de entrada e saída para cada endpoint.
2. Documente erros comuns e como solucioná-los.
3. Mantenha a documentação atualizada com cada mudança na API.

[Voltar ao Índice](#índice)

---

## 🛡️ **Autenticação**

### Passo 1: Obtenha um Token

Use um fluxo de autenticação como OAuth 2.0 ou JWT.

**Exemplo de Obtenção de Token**:
```http
POST https://auth.qbem.net.br/oauth/token
Content-Type: application/json

{
  "client_id": "your-client-id",
  "client_secret": "your-client-secret",
  "grant_type": "password",
  "username": "your-username",
  "password": "your-password"
}
```

**Resposta**:
```json
{
  "access_token": "your-access-token",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

### Passo 2: Inclua o Token nas Requisições

Todas as requisições autenticadas devem incluir o cabeçalho `Authorization`:

```http
Authorization: Bearer your-access-token
```

**Exemplo**:
```http
GET http://api.qbem.net.br/v1/operators
Authorization: Bearer your-access-token
```

[Voltar ao Índice](#índice)
