# **Documentação de APIs da QBem**

Este diretório contém toda a documentação e exemplos necessários para o uso e implementação das APIs da QBem, abrangendo **APIs Web RESTful**, **Webhooks** e **APIs Assíncronas**. Todas as APIs foram projetadas para oferecer integração confiável, segura e eficiente, promovendo a escalabilidade e a interoperabilidade entre os sistemas QBem e seus parceiros.

---

## **Índice de Documentação**

1. [APIs Web RESTful](#apis-web-restful)
2. [APIs de Webhook](#apis-de-webhook)
3. [APIs Assíncronas (Redis e Kafka)](#apis-assíncronas-redis-e-kafka)
4. [Boas Práticas Gerais](#boas-práticas-gerais-para-todas-as-apis)
5. [Estrutura do Diretório `docs`](#estrutura-da-pasta-docs)

---

## **1. APIs Web RESTful**

A documentação das APIs Web RESTful inclui:

- **Estrutura de URLs e Convenções**:
  - Use recursos no plural (e.g., `/users`, `/orders`).
  - Estruture URLs hierárquicas para refletir relações (e.g., `/users/{user_id}/orders`).
  - Não inclua verbos nas URLs; utilize os métodos HTTP para expressar ações.

- **Métodos HTTP e Operações CRUD**:
  - `GET`: Leitura de recursos.
  - `POST`: Criação de recursos.
  - `PUT`: Atualização completa de recursos.
  - `PATCH`: Atualização parcial de recursos.
  - `DELETE`: Exclusão de recursos.

- **Códigos de Status**:
  - Use os códigos de status adequados:
    - `200 OK`: Requisição bem-sucedida.
    - `201 Created`: Recurso criado.
    - `400 Bad Request`: Dados inválidos.
    - `401 Unauthorized`: Credenciais inválidas.
    - `404 Not Found`: Recurso não encontrado.
    - `500 Internal Server Error`: Erro no servidor.

- **Boas Práticas**:
  - **Versionamento**: Utilize versões na URL (e.g., `/v1/users`).
  - **Paginação**: Adote paginação para grandes volumes de dados, seguindo o padrão `limit` e `offset`.
  - **Cabeçalhos Personalizados**: Use cabeçalhos customizados, como `X-QBem-Request-Id` para rastreamento.

---

## **2. APIs de Webhook**

A documentação de Webhooks cobre:

- **Estrutura do Payload JSON**:
  - Cada evento é enviado como um objeto JSON. Exemplo para `user.created`:
    ```json
    {
      "event": "user.created",
      "data": {
        "user_id": "12345",
        "email": "user@example.com"
      },
      "timestamp": "2024-12-02T12:00:00Z"
    }
    ```

- **Configuração e Autenticação**:
  - URLs de Webhook devem suportar HTTPS.
  - Autentique usando HMAC com assinatura no cabeçalho `X-QBem-Signature`.

- **Boas Práticas**:
  - **Retry Automático**: Reenvie eventos em caso de falha com backoff exponencial.
  - **Controle de Duplicidade**: Use IDs exclusivos para identificar eventos.
  - **Verificação de Assinatura**: Valide a assinatura HMAC para garantir a autenticidade.

---

## **3. APIs Assíncronas (Redis e Kafka)**

A documentação para APIs assíncronas inclui:

- **Estrutura de Mensagens**:
  - Mensagens devem conter metadados como `event_id`, `timestamp` e `correlation_id`.

- **Exemplo de Mensagem Kafka**:
  ```json
  {
    "event_id": "abcd1234",
    "event_type": "order.updated",
    "data": {
      "order_id": "5678",
      "status": "processed"
    },
    "timestamp": "2024-12-02T12:00:00Z"
  }
  ```

- **Boas Práticas**:
  - **Idempotência**: Utilize `event_id` para garantir que eventos sejam processados uma única vez.
  - **Monitoramento**: Implante ferramentas como Prometheus para rastreamento de mensagens.
  - **Resiliência**: Configure retries automáticos em caso de falhas de consumo.

---

## **4. Boas Práticas Gerais para Todas as APIs**

1. **Consistência no Idioma**:
   - Use o inglês como idioma padrão para nomes de campos, mensagens de erro e logs.

2. **HTTPS Obrigatório**:
   - Todas as APIs devem exigir HTTPS para proteger os dados em trânsito.

3. **Autenticação e Autorização**:
   - Use OAuth 2.0 ou JWT para autenticação segura.
   - Configure escopos de autorização para restringir acesso a recursos específicos.

4. **Versionamento de APIs**:
   - Sempre inclua o número da versão na URL, começando com `/v1`.

5. **Documentação Clara**:
   - Forneça exemplos de requisição/resposta para cada endpoint ou evento.

6. **Paginação**:
   - Utilize padrões de paginação como `limit`, `offset` e inclua metadados para facilitar a navegação.

7. **Retry e Tolerância a Falhas**:
   - Webhooks e APIs assíncronas devem implementar lógica de retry com backoff exponencial.

---

## **5. Estrutura da Pasta `docs`**

A estrutura recomendada para o diretório `docs`:

```
/docs
 ├── web_api.md                    # Documentação das APIs Web RESTful
 ├── requests.http                 # Exemplos de requisições HTTP para APIs Web
 ├── webhooks.md                   # Documentação para Webhooks
 ├── webhook_requests.http         # Exemplos de requisições para Webhooks
 ├── async_apis.md                 # Documentação para APIs Assíncronas (Redis e Kafka)
 ├── kafka_requests.http           # Exemplos de mensagens Kafka
 ├── postman_collection.json       # Coleção consolidada do Postman com todas as APIs
 └── README.md                     # Índice e visão geral da documentação
```
