# Documentação de APIs da QBem

Este diretório contém toda a documentação e exemplos necessários para o uso e implementação das APIs da QBem, incluindo **APIs Web RESTful**, **Webhooks** e **APIs Assíncronas**. Cada tipo de API foi projetado para oferecer integração confiável, segura e eficiente com os sistemas da QBem, promovendo a comunicação entre serviços e facilitando a escalabilidade.

## Índice de Documentação

### 1. [APIs Web RESTful](web_apis.md)

Este documento descreve as diretrizes e boas práticas para as APIs Web RESTful da QBem, incluindo:
- Estrutura de URLs RESTful e convenções de nomenclatura.
- Métodos HTTP e seus mapeamentos para operações CRUD.
- Códigos de status padrão e cabeçalhos personalizados.
- Boas práticas de segurança e versionamento.

---

### 2. [APIs de Webhook](webhooks.md)

Este documento detalha as diretrizes para configuração e uso de Webhooks na QBem, incluindo:
- Estrutura do payload JSON e exemplos de eventos suportados (e.g., `user.created`, `order.updated`).
- Configuração de URLs de Webhook e métodos de autenticação.
- Cabeçalhos customizados e segurança com assinatura HMAC.
- Boas práticas para implementação de Webhooks, como lógica de retry, verificação de assinatura e controle de duplicidade.


---

### 3. [APIs Assíncronas (Redis e Kafka)](async_apis.md)

Este documento aborda as diretrizes para implementação de APIs assíncronas da QBem, utilizando Redis e Kafka, incluindo:
- Estrutura e configuração de mensagens para filas de mensagens e tópicos.
- Métodos de comunicação para Redis e Kafka, com exemplos de publicação e consumo.
- Cabeçalhos e metadados para rastreamento e controle de mensagens.
- Boas práticas para APIs assíncronas, como controle de idempotência, retry e segurança.


---

## Boas Práticas Gerais para Todas as APIs

1. **Consistência no Idioma**: Todas as mensagens, respostas e campos de dados devem estar em inglês para garantir acessibilidade internacional.
2. **HTTPS Obrigatório**: Toda comunicação deve ser feita via HTTPS para proteger os dados em trânsito.
3. **Autenticação e Segurança**: Utilize OAuth 2.0 ou JWT para as APIs RESTful e Webhooks, e configure chaves de acesso e permissões para APIs Assíncronas.
4. **Logs e Monitoramento**: Implemente logs detalhados para cada requisição ou evento, facilitando auditorias e troubleshooting.
5. **Idempotência**: Garanta que as chamadas e eventos sejam idempotentes para evitar duplicidade de ações.
6. **Retry e Tolerância a Falhas**: Para Webhooks e APIs Assíncronas, implemente lógica de retry com limites de tentativas para melhorar a resiliência.

## Estrutura da Pasta `docs`

Abaixo está a estrutura recomendada para o diretório `docs`:

```
/docs
 ├── web_api.md                    # Documentação das APIs Web RESTful
 ├── requests.http                 # Exemplo de requisições HTTP para APIs Web
 ├── webhooks.md                   # Documentação para Webhooks
 ├── webhook_requests.http         # Exemplo de requisições para Webhooks
 ├── async_apis.md                 # Documentação para APIs Assíncronas (Redis e Kafka)
 ├── kafka_requests.http           # Exemplo de requisições para APIs Assíncronas Kafka
 ├── postman_collection.json       # Coleção consolidada do Postman com todas as APIs
 └── README.md                     # Índice e visão geral da documentação
```