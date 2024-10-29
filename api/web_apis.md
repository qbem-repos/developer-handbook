# Diretrizes e Boas Práticas para APIs da QBem

Este documento fornece diretrizes e boas práticas para o desenvolvimento de APIs da QBem, com uma seção dedicada para APIs de Search Engine utilizando Typesense. As diretrizes visam assegurar uma API RESTful, consistente, segura e de fácil manutenção, proporcionando uma experiência de desenvolvedor (DX) positiva.

## Índice
- [Diretrizes e Boas Práticas para APIs da QBem](#diretrizes-e-boas-práticas-para-apis-da-qbem)
  - [Índice](#índice)
  - [Idioma Padrão](#idioma-padrão)
  - [APIs RESTful](#apis-restful)
    - [URLs RESTful](#urls-restful)
    - [Métodos HTTP](#métodos-http)
    - [Códigos de Status](#códigos-de-status)
    - [Metadados e Cabeçalhos Personalizados](#metadados-e-cabeçalhos-personalizados)
    - [Versionamento](#versionamento)
  - [APIs de Search Engine (Typesense)](#apis-de-search-engine-typesense)
    - [URLs para Busca](#urls-para-busca)
    - [Filtros e Parâmetros de Consulta](#filtros-e-parâmetros-de-consulta)
    - [Cabeçalhos e Boas Práticas](#cabeçalhos-e-boas-práticas)
  - [Boas Práticas de Segurança](#boas-práticas-de-segurança)
  - [Documentação da API](#documentação-da-api)
    - [Estrutura da Pasta `docs`](#estrutura-da-pasta-docs)
    - [Boas Práticas para a Documentação](#boas-práticas-para-a-documentação)

---

## Idioma Padrão

Para garantir consistência e acessibilidade internacional, todas as **nomenclaturas, respostas e mensagens de erro** da API devem ser escritas em inglês. Isso inclui nomes de endpoints, campos de dados, mensagens de status e cabeçalhos personalizados. Utilizar o inglês como padrão facilita a compreensão global e auxilia na integração com ferramentas e desenvolvedores de diferentes regiões.

**Exemplo de Endpoint:**
```http
GET http://api.qbem.net.br/v1/operators
```

**Mensagem de erro recomendada:**
```json
{
  "error": "Invalid request parameter",
  "message": "The field 'created_at' is required."
}
```

[Voltar ao Índice](#índice)

---

## APIs RESTful

As APIs RESTful da QBem seguem práticas que maximizam a clareza e a consistência, garantindo que cada recurso seja bem definido e fácil de consumir.

### URLs RESTful

1. **Use substantivos, não verbos**: O URL deve representar recursos, não ações. Exemplo: `/operators` ao invés de `/getOperators`.
2. **Nomes de recursos no plural** para consistência. Exemplo: `/operators` em vez de `/operator`.
3. **Hierarquia de recursos limitada**: Utilize `resource/identifier/resource` e evite URLs excessivamente aninhadas.
4. **Versão na base do URL** para facilitar o versionamento da API. Exemplo: `http://api.qbem.net.br/v1/operators`.
5. **Parâmetros de filtro e ordenação** devem ser enviados na query string para consistência.

**Bons Exemplos de URLs:**

- Listar todas as operadoras:
  ```http
  GET http://api.qbem.net.br/v1/operators
  ```

- Filtrar operadoras por data de criação e status:
  ```http
  GET http://api.qbem.net.br/v1/operators?created_at=2011&active=true
  ```

- Obter uma operadora específica:
  ```http
  GET http://api.qbem.net.br/v1/operators/1234
  ```

- Adicionar um novo registro a uma operadora:
  ```http
  POST http://api.qbem.net.br/v1/operators/1234/plans
  ```

### Métodos HTTP

Os métodos HTTP devem ser utilizados conforme as operações CRUD, para manter consistência com as práticas RESTful.

| Método  | Ação       | Descrição                                                                 |
|---------|------------|---------------------------------------------------------------------------|
| POST    | Criar      | Insere um novo recurso. Exemplo: `POST /operators`.                      |
| GET     | Ler        | Retorna um ou mais recursos. Exemplo: `GET /operators`.                  |
| PUT     | Atualizar  | Atualiza um recurso existente. Exemplo: `PUT /operators/1234`.           |
| DELETE  | Excluir    | Remove um recurso específico. Exemplo: `DELETE /operators/1234`.         |

### Códigos de Status

Use códigos de status HTTP para informar ao cliente o resultado da requisição de forma consistente.

| Código | Descrição                | Contexto de Uso                                                |
|--------|---------------------------|-----------------------------------------------------------------|
| 200    | OK                        | Requisição bem-sucedida.                                        |
| 201    | Created                   | Recurso criado com sucesso.                                     |
| 204    | No Content                | Sucesso sem conteúdo de resposta.                               |
| 400    | Bad Request               | Parâmetros inválidos ou requisição malformada.                  |
| 401    | Unauthorized              | Autenticação necessária.                                        |
| 403    | Forbidden                 | Permissões insuficientes.                                       |
| 404    | Not Found                 | Recurso não encontrado.                                         |
| 500    | Internal Server Error     | Erro interno do servidor.                                       |

### Metadados e Cabeçalhos Personalizados

Inclua cabeçalhos personalizados para metadados e informações adicionais. Esses cabeçalhos facilitam a rastreabilidade e o controle.

- **X-Request-Id**: ID exclusivo da requisição (UUIDv4) para rastreamento.
- **X-Request-Timestamp**: Data e hora da requisição.
- **X-Message**: Mensagem adicional sobre o status da requisição.
- **X-Content-Size**: Número total de itens disponíveis (útil para paginação).
- **X-Content-Limit**: Limite de itens retornados.

### Versionamento

Utilize o versionamento na base da URL ou com o cabeçalho **X-API-Version** para facilitar atualizações sem impactar clientes existentes.

**Exemplos de Versionamento:**
- Na URL:
  ```http
  GET http://api.qbem.net.br/v1/operators
  ```
- No Cabeçalho:
  ```http
  X-API-Version: v1
  ```

[Voltar ao Índice](#índice)

---

## APIs de Search Engine (Typesense)

As APIs de Search Engine utilizam Typesense e são servidas através do domínio `search.qbem.net.br`. Essas APIs permitem buscas rápidas e eficientes em coleções, filtragem avançada e ordenação.

### URLs para Busca

Para busca e manipulação de coleções, utilize as URLs no padrão Typesense, mantendo a estrutura de recursos e identificadores:

1. **Buscar em coleções**: Use `/collections/{collection}/documents/search` para buscar documentos com filtros avançados.
2. **Operações com coleções**: Endpoints para adicionar, atualizar ou excluir documentos dentro de coleções.

**Exemplos de URLs de Busca Typesense:**

- Busca em operadoras com filtro:
  ```http
  GET http://search.qbem.net.br/v1/collections/operators/documents/search?filter_by=active:true
  ```

- Listar todos os documentos de uma coleção:
  ```http
  GET http://search.qbem.net.br/v1/collections/operators/documents
  ```

- Obter um documento específico:
  ```http
  GET http://search.qbem.net.br/v1/collections/operators/documents/1234
  ```

- Adicionar um novo documento a uma coleção:
  ```http
  POST http://search.qbem.net.br/v1/collections/operators/documents
  ```

### Filtros e Parâmetros de Consulta

Typesense permite a aplicação de filtros e parâmetros avançados para busca:

- **filter_by**: Filtro condicional. Exemplo: `filter_by=created_at:>2020`.
- **sort_by**: Define a ordem dos resultados. Exemplo: `sort_by=name:desc`.
- **query_by**: Campos a serem considerados na busca. Exemplo: `query_by=name,description`.

**Exemplo Completo de Busca com Filtros:**
```http
GET http://search.qbem.net.br/v1/collections/operators/documents/search?query_by=name&filter_by=active:true&sort_by=created_at:desc
```

### Cabeçalhos e Boas Práticas

- **X-Search-Request-Id**: ID único para rastreamento de consultas de busca.
- **X-Result-Count**: Quantidade total de documentos correspondentes à consulta.
- **Limitação de Resultados**: Para otimizar o desempenho, limite os resultados retornados por página e evite consultas muito abrangentes.

[Voltar ao Índice](#índice)

---

## Boas Práticas de Segurança

Para garantir segurança, siga estas práticas:

1. **Autenticação e Autorização**: Use OAuth 2.0 ou JWT para autenticação e proteção de dados sensíveis.
2. **Limitação de Taxa (Rate Limiting)**: Limite o número de requisições por IP para evitar abusos.
3. **Sanitização de Entrada**: Sempre valide e sanitize parâmetros de entrada para evitar injeção de código.
4. **HTTPS**: Todas as requisições devem ser feitas via HTTPS para proteger dados

 em trânsito.
5. **Resposta de Erro Genérica**: Evite detalhes excessivos em mensagens de erro para não expor informações internas.

[Voltar ao Índice](#índice)

Aqui está a seção "Documentação da API" revisada para refletir a documentação no formato de uma pasta `docs` no projeto, incluindo um arquivo `requests.http` e uma coleção para o Postman.



## Documentação da API

Toda a documentação das APIs deve estar contida em uma pasta `docs` no projeto. Essa pasta deve incluir:

1. **Arquivo `requests.http`**: Um arquivo `requests.http` que documenta as chamadas HTTP, contendo exemplos detalhados de requisições e respostas. Esse arquivo permite que os desenvolvedores façam chamadas diretamente a partir de seu editor de código, facilitando testes e verificações rápidas.

2. **Coleção do Postman**: Uma coleção JSON para o Postman (`postman_collection.json`) que inclui todos os endpoints documentados com exemplos de parâmetros, cabeçalhos e corpos de requisição. Isso facilita a exploração e a integração dos endpoints da API diretamente no Postman.

### Estrutura da Pasta `docs`

A pasta `docs` deve seguir a estrutura abaixo para garantir uma documentação organizada e fácil de navegar:

```
/docs
 ├── requests.http               # Documentação das requisições HTTP, com exemplos completos
 ├── postman_collection.json      # Coleção para Postman, exportável e pronta para importação
 └── README.md                    # Instruções de uso da documentação
```

### Boas Práticas para a Documentação

- **Consistência**: Mantenha o formato dos endpoints consistente entre o arquivo `requests.http` e a coleção do Postman.
- **Atualização Regular**: Atualize a documentação a cada mudança na API para garantir que os exemplos estejam corretos.
- **Exemplos Completo**: Inclua exemplos detalhados para cada endpoint com descrições dos parâmetros necessários, cabeçalhos e tipos de dados.

Ter essa documentação no próprio projeto facilita o acesso e permite que os desenvolvedores testem diretamente no ambiente de desenvolvimento, promovendo uma experiência de integração mais eficiente.

[Voltar ao Índice](#índice)
