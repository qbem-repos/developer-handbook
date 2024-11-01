# Diretrizes e Boas Práticas para Webhooks da QBem

Este documento fornece orientações e melhores práticas para a implementação e uso de webhooks em projetos da QBem. Webhooks permitem que sistemas externos sejam notificados automaticamente sobre eventos relevantes, sem necessidade de consulta constante, promovendo integração em tempo real.

## Índice
- [Diretrizes e Boas Práticas para Webhooks da QBem](#diretrizes-e-boas-práticas-para-webhooks-da-qbem)
  - [Índice](#índice)
  - [Visão Geral](#visão-geral)
  - [Estrutura e Payload do Webhook](#estrutura-e-payload-do-webhook)
  - [URL de Webhook](#url-de-webhook)
  - [Métodos HTTP e Códigos de Status](#métodos-http-e-códigos-de-status)
  - [Cabeçalhos Customizados e Segurança](#cabeçalhos-customizados-e-segurança)
    - [Cabeçalhos Customizados](#cabeçalhos-customizados)
    - [Assinatura e Verificação](#assinatura-e-verificação)
  - [Boas Práticas para Webhooks](#boas-práticas-para-webhooks)
  - [Documentação e Exemplo de Payload](#documentação-e-exemplo-de-payload)
    - [Estrutura da Pasta `docs`](#estrutura-da-pasta-docs)
    - [Exemplo de `webhooks.md`](#exemplo-de-webhooksmd)

---

## Visão Geral

Webhooks são usados para enviar dados em tempo real de um sistema para outro. Quando um evento ocorre (como a criação de um novo usuário ou uma atualização de status), o sistema da QBem envia uma notificação para uma URL especificada pelo cliente ou sistema externo. 

Exemplos de eventos que podem acionar webhooks:
- Criação de um novo recurso
- Atualização de um status
- Exclusão de um recurso

[Voltar ao Índice](#índice)

## Estrutura e Payload do Webhook

Os webhooks da QBem enviam **dados estruturados em JSON** com informações sobre o evento que ocorreu. A estrutura do payload deve ser clara e descritiva, facilitando a interpretação dos dados pelo sistema receptor.

**Exemplo de Payload:**
```json
{
  "event": "user.created",
  "timestamp": "2024-10-29T12:34:56Z",
  "data": {
    "user_id": "12345",
    "email": "user@example.com",
    "name": "John Doe",
    "status": "active"
  }
}
```

**Campos recomendados:**
- **event**: Nome único do evento (e.g., `user.created`, `user.updated`).
- **timestamp**: Data e hora do evento em formato ISO 8601.
- **data**: Objeto contendo os detalhes específicos do evento.

[Voltar ao Índice](#índice)

## URL de Webhook

A URL de webhook é fornecida pelo sistema externo que deseja receber notificações. Esta URL deve estar configurada para lidar corretamente com as requisições enviadas pela QBem.

- **HTTPS** é obrigatório para proteger a comunicação.
- As URLs devem ser exclusivas por evento, se necessário, para melhor organização e manuseio.

**Exemplo de URL de Webhook:**
```plaintext
https://webhooks.external-system.com/qbem/user-events
```

[Voltar ao Índice](#índice)

## Métodos HTTP e Códigos de Status

O método **POST** é o padrão para o envio de webhooks. O código de status HTTP da resposta indica se o webhook foi recebido com sucesso.

- **POST**: Envia o payload JSON para a URL do webhook.

**Códigos de Status de Resposta:**
- **200 OK**: Indica que o webhook foi recebido e processado com sucesso.
- **4xx (400, 404)**: Erro do cliente, como URL inválida ou endpoint inexistente.
- **5xx (500)**: Erro no servidor receptor. A QBem deve implementar lógica de reenvio para falhas temporárias.

**Reenvio de Webhook**: Caso o webhook retorne um código 5xx, a QBem deve tentar reenviar a notificação com intervalos exponenciais de tempo, limitando o número de tentativas para evitar sobrecarga.

[Voltar ao Índice](#índice)

## Cabeçalhos Customizados e Segurança

Para garantir a segurança dos webhooks, é recomendado incluir cabeçalhos personalizados e autenticação.

### Cabeçalhos Customizados

1. **X-QBem-Event**: Nome do evento, como `user.created`.
2. **X-QBem-Signature**: Assinatura HMAC do payload para verificar a autenticidade do webhook.
3. **X-QBem-Timestamp**: Timestamp em que o webhook foi enviado, para prevenir ataques de repetição (replay attacks).

**Exemplo de Cabeçalhos Customizados:**
```http
X-QBem-Event: user.created
X-QBem-Signature: sha256=abcdef1234567890
X-QBem-Timestamp: 2024-10-29T12:34:56Z
```

### Assinatura e Verificação

A assinatura (usando HMAC-SHA256) permite que o receptor verifique a autenticidade da requisição. A chave de assinatura é compartilhada apenas entre a QBem e o sistema receptor.

**Verificação de Assinatura no Receptor:**
1. Construa um hash HMAC-SHA256 com a chave secreta e o payload recebido.
2. Compare a assinatura gerada com o valor do cabeçalho `X-QBem-Signature`.

[Voltar ao Índice](#índice)

## Boas Práticas para Webhooks

1. **HTTPS Obrigatório**: Sempre use HTTPS para proteger os dados em trânsito.
2. **Tolerância a Falhas**: Implemente lógica de reenvio com intervalos exponenciais para tentativas, evitando sobrecarga do receptor.
3. **Validação de Assinatura**: Sempre verifique a assinatura do webhook para garantir a autenticidade dos dados recebidos.
4. **Evite Looping de Eventos**: Verifique no sistema receptor se um evento já foi processado para evitar duplicação.
5. **Timeouts e Retries**: Defina um tempo limite para a resposta do webhook (ex.: 5 segundos) e uma lógica de reenvio com limite de tentativas.
6. **Registro de Logs**: Registre os webhooks enviados e as respostas recebidas para auditoria e troubleshooting.
7. **Configuração de Filtros**: Permita que o receptor selecione os eventos que deseja receber, evitando o envio de dados irrelevantes.

[Voltar ao Índice](#índice)

## Documentação e Exemplo de Payload

Toda a documentação dos webhooks deve estar disponível na pasta `docs` do projeto. A documentação deve incluir:

1. **Arquivo `webhooks.md`**: Documento contendo a descrição de todos os eventos suportados e suas estruturas de payload.
2. **Arquivo `webhook_requests.http`**: Exemplo de chamadas HTTP simulando envios de webhook para teste.
3. **Postman Collection**: Uma coleção do Postman com exemplos de payloads e chamadas de webhook para facilitar o teste e validação dos endpoints receptores.

### Estrutura da Pasta `docs`

```
/docs
 ├── webhooks.md               # Documentação dos eventos e estrutura de payloads
 ├── webhook_requests.http      # Exemplo de requisições HTTP de webhooks
 └── postman_collection.json    # Coleção do Postman para testes de webhooks
```

### Exemplo de `webhooks.md`

```markdown
# Documentação de Webhooks da QBem

## Eventos Suportados

1. **user.created** - Evento disparado quando um novo usuário é criado.
   - **Payload**:
     ```json
     {
       "event": "user.created",
       "timestamp": "2024-10-29T12:34:56Z",
       "data": {
         "user_id": "12345",
         "email": "user@example.com",
         "name": "John Doe",
         "status": "active"
       }
     }
     ```

2. **user.updated** - Evento disparado quando um usuário é atualizado.
   - **Payload**:
     ```json
     {
       "event": "user.updated",
       "timestamp": "2024-10-29T13:00:00Z",
       "data": {
         "user_id": "12345",
         "status": "inactive"
       }
     }
     ```

## Cabeçalhos de Segurança

- **X-QBem-Event**
- **X-QBem-Signature**
- **X-QBem-Timestamp**

## Testando Webhooks

Use o arquivo `webhook_requests.http` para simular requisições e validar a implementação.
```

[Voltar ao Índice](#índice)
