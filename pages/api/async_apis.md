# Diretrizes e Boas Práticas para APIs Assíncronas da QBem

Este documento fornece orientações e melhores práticas para a implementação e uso de APIs assíncronas na QBem, utilizando **MQ (RabbitMQ)** e **Pub/Sub (Kafka)**. As APIs assíncronas permitem a comunicação entre serviços em tempo real, promovendo um sistema escalável e eficiente.

## Índice
- [Diretrizes e Boas Práticas para APIs Assíncronas da QBem](#diretrizes-e-boas-práticas-para-apis-assíncronas-da-qbem)
  - [Índice](#índice)
  - [Visão Geral](#visão-geral)
    - [Casos de Uso para APIs Assíncronas](#casos-de-uso-para-apis-assíncronas)
  - [Estrutura de Mensagens](#estrutura-de-mensagens)
  - [Configuração de Fila e Tópico](#configuração-de-fila-e-tópico)
    - [RabbitMQ (Message Queue)](#rabbitmq-message-queue)
    - [Kafka (Pub/Sub)](#kafka-pubsub)
  - [Métodos de Comunicação](#métodos-de-comunicação)
    - [RabbitMQ](#rabbitmq)
    - [Kafka](#kafka)
  - [Cabeçalhos e Metadados](#cabeçalhos-e-metadados)
    - [RabbitMQ](#rabbitmq-1)
    - [Kafka](#kafka-1)
  - [Boas Práticas para APIs Assíncronas](#boas-práticas-para-apis-assíncronas)
  - [Documentação e Exemplo de Mensagens](#documentação-e-exemplo-de-mensagens)
    - [Estrutura da Pasta `docs`](#estrutura-da-pasta-docs)
    - [Exemplo de `async_apis.md`](#exemplo-de-async_apismd)

---

## Visão Geral

As APIs assíncronas na QBem utilizam RabbitMQ e Kafka para o envio e recebimento de mensagens entre serviços. O RabbitMQ é utilizado para filas de mensagens (Message Queue), enquanto o Kafka gerencia a comunicação baseada em tópicos (Pub/Sub), sendo ideal para a transmissão de dados em larga escala e baixa latência.

### Casos de Uso para APIs Assíncronas

- Processamento de grandes volumes de dados.
- Notificações em tempo real e atualizações de estado.
- Sistemas que exigem desacoplamento entre produtores e consumidores.

[Voltar ao Índice](#índice)

## Estrutura de Mensagens

As mensagens enviadas devem seguir uma estrutura JSON consistente para garantir que os dados possam ser facilmente consumidos e processados.

**Exemplo de Estrutura de Mensagem:**
```json
{
  "event": "order.created",
  "timestamp": "2024-10-29T12:34:56Z",
  "data": {
    "order_id": "7890",
    "customer_id": "12345",
    "items": [
      {
        "product_id": "9876",
        "quantity": 2
      }
    ],
    "total": 150.00,
    "status": "pending"
  }
}
```

**Campos recomendados:**
- **event**: Nome único do evento (e.g., `order.created`, `order.updated`).
- **timestamp**: Data e hora do evento no formato ISO 8601.
- **data**: Objeto contendo detalhes do evento, específico para cada tipo.

[Voltar ao Índice](#índice)

## Configuração de Fila e Tópico

Para RabbitMQ e Kafka, a configuração de filas e tópicos deve ser consistente e planejada para suportar o escalonamento do sistema.

### RabbitMQ (Message Queue)

- **Nomes de Fila**: Defina nomes de fila que indiquem claramente o propósito. Exemplo: `orders_queue`, `notifications_queue`.
- **Prioridade**: RabbitMQ pode ser configurado para processar mensagens em ordem FIFO (First-In-First-Out).
- **TTL (Time to Live)**: Configure um tempo de vida para cada mensagem, caso necessário, para evitar filas com mensagens obsoletas.

### Kafka (Pub/Sub)

- **Tópicos**: Crie tópicos segmentados por tipo de evento. Exemplo: `orders`, `notifications`.
- **Partições**: Configure múltiplas partições para permitir paralelismo e melhor desempenho.
- **Replicação**: Configure réplicas para tolerância a falhas, garantindo disponibilidade em caso de falha de um nó.

**Exemplo de Tópicos para Kafka:**
- `orders`: Para eventos relacionados a pedidos (e.g., `order.created`, `order.updated`).
- `users`: Para eventos relacionados a usuários (e.g., `user.registered`, `user.deleted`).

[Voltar ao Índice](#índice)

## Métodos de Comunicação

Para APIs assíncronas, diferentes métodos de comunicação são usados dependendo do middleware.

### RabbitMQ

- **LPUSH** e **BRPOP**: Para adicionar mensagens à fila e consumir mensagens de forma síncrona ou assíncrona.
- **PUB/SUB**: RabbitMQ também suporta Pub/Sub básico, mas não mantém mensagens após serem consumidas, sendo ideal para notificações em tempo real.

**Exemplo em RabbitMQ:**
```bash
# Adicionando uma mensagem na fila
LPUSH orders_queue '{"event":"order.created","timestamp":"2024-10-29T12:34:56Z","data":{"order_id":"7890"}}'

# Consumindo uma mensagem da fila
BRPOP orders_queue 0
```

### Kafka

- **Produtores**: Enviam mensagens a um tópico específico.
- **Consumidores**: Consomem mensagens de tópicos de acordo com o grupo ao qual pertencem. Os consumidores em um grupo específico consomem cada mensagem uma vez, evitando duplicações.
- **Offset**: O Kafka permite que consumidores retomem de onde pararam, garantindo que nenhuma mensagem seja perdida.

**Exemplo de Código com Kafka em Python:**
```python
from kafka import KafkaProducer, KafkaConsumer

# Enviar mensagem (produtor)
producer = KafkaProducer(bootstrap_servers='localhost:9092')
producer.send('orders', b'{"event":"order.created","timestamp":"2024-10-29T12:34:56Z","data":{"order_id":"7890"}}')
producer.close()

# Consumir mensagem (consumidor)
consumer = KafkaConsumer('orders', group_id='order_processors', bootstrap_servers='localhost:9092')
for message in consumer:
    print(message.value)
```

[Voltar ao Índice](#índice)

## Cabeçalhos e Metadados

Para rastrear mensagens e controlar o fluxo, inclua metadados e cabeçalhos específicos nas mensagens.

### RabbitMQ

Para RabbitMQ, os metadados são normalmente embutidos no corpo JSON.

### Kafka

Kafka suporta cabeçalhos de mensagem, úteis para rastreamento e validação:

- **X-Request-Id**: ID exclusivo da mensagem para rastreamento.
- **X-Timestamp**: Timestamp da mensagem para auditoria.
- **X-Retry-Count**: Contador de tentativas de processamento para implementar lógica de reenvio ou descarte de mensagens.

[Voltar ao Índice](#índice)

## Boas Práticas para APIs Assíncronas

1. **Padronize Estruturas de Mensagem**: Use uma estrutura JSON padronizada para cada tipo de evento.
2. **Tolerância a Falhas e Retry**: Implemente lógica de retry para mensagens que falharem no processamento, com limite de tentativas.
3. **Configuração de TTL**: Defina um tempo de vida para mensagens (especialmente em RabbitMQ), evitando o acúmulo de mensagens obsoletas.
4. **Controle de Concurrency**: Configure consumidores em grupos para evitar duplicidade e permitir escalabilidade.
5. **Registro e Monitoramento**: Registre todas as mensagens processadas e monitoramento de filas/tópicos para detectar anomalias.
6. **Idempotência**: Garanta que o processamento das mensagens seja idempotente, ou seja, o mesmo evento pode ser processado mais de uma vez sem efeitos indesejados.
7. **Segurança**: Implemente controle de acesso nas filas e tópicos, garantindo que apenas serviços autorizados possam publicar ou consumir mensagens.
8. **Limitação de Tamanho da Mensagem**: Defina um limite de tamanho para mensagens para evitar problemas de desempenho e possíveis rejeições de mensagens muito grandes.

[Voltar ao Índice](#índice)

## Documentação e Exemplo de Mensagens

Toda a documentação das APIs assíncronas deve estar na pasta `docs` do projeto. Essa documentação deve conter:

1. **Arquivo `async_apis.md`**: Descrição dos eventos suportados, estrutura de mensagens e configuração de filas/tópicos.
2. **Exemplos de Mensagens**: Exemplos de mensagens JSON para cada tipo de evento.
3. **Arquivo `kafka_requests.http`** (opcional): Simulação de chamadas para tópicos Kafka para testes.
4. **Postman Collection**: Uma coleção com exemplos de chamadas e mensagens para facilitar testes e validação.

### Estrutura da Pasta `docs`

```
/docs
 ├── async_apis.md               # Documentação das APIs assíncronas
 ├── kafka_requests.http          # Exemplo de requisições para Kafka
 └── postman_collection.json      # Coleção do Postman para testes de APIs assíncronas
```

### Exemplo de `async_apis.md`

```markdown
# Documentação das APIs Assíncronas da QBem

## Eventos Suportados

1. **order.created** - Evento disparado ao criar um novo pedido.
   - **Payload**:
     ```json
     {
       "event": "order.created",
       "timestamp": "2024-10-29T12:34:56Z",
       "data": {
         "order_id": "7890",
         "customer_id": "12345",
         "items": [
           {
             "product_id": "9876",
             "quantity": 2
           }
         ],
         "total":

 150.00,
         "status": "pending"
       }
     }
     ```

2. **order.updated** - Evento disparado ao atualizar um pedido.
   - **Payload**:
     ```json
     {
       "event": "order.updated",
       "timestamp": "2024-10-29T13:00:00Z",
       "data": {
         "order_id": "7890",
         "status": "shipped"
       }
     }
     ```

## Configuração de Fila e Tópicos

- **RabbitMQ**:
  - Fila: `orders_queue`
  - TTL: 1 hora

- **Kafka**:
  - Tópico: `orders`
  - Partições: 3
  - Replicação: 2

## Boas Práticas para Consumidores

- Lógica de retry para mensagens com falha
- Controle de idempotência para evitar duplicidade
```

[Voltar ao Índice](#índice)
