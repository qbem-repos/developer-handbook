# **Diretrizes e Boas Práticas para APIs Assíncronas da QBem**

Este documento fornece orientações detalhadas e melhores práticas para a implementação de APIs assíncronas na QBem, utilizando **Message Queues (RabbitMQ)** e **Pub/Sub (Kafka)**. Estas APIs permitem comunicação eficiente entre serviços em tempo real, garantindo escalabilidade, resiliência e desempenho.

---

## **Índice**

1. [Visão Geral](#visão-geral)
   - [Casos de Uso para APIs Assíncronas](#casos-de-uso-para-apis-assíncronas)
2. [Estrutura de Mensagens](#estrutura-de-mensagens)
3. [Configuração de Filas e Tópicos](#configuração-de-filas-e-tópicos)
   - [RabbitMQ (Message Queue)](#rabbitmq-message-queue)
   - [Kafka (Pub/Sub)](#kafka-pubsub)
4. [Métodos de Comunicação](#métodos-de-comunicação)
   - [RabbitMQ](#rabbitmq)
   - [Kafka](#kafka)
5. [Cabeçalhos e Metadados](#cabeçalhos-e-metadados)
   - [RabbitMQ](#rabbitmq-1)
   - [Kafka](#kafka-1)
6. [Boas Práticas para APIs Assíncronas](#boas-práticas-para-apis-assíncronas)
7. [Documentação e Exemplo de Mensagens](#documentação-e-exemplo-de-mensagens)
   - [Estrutura da Pasta `docs`](#estrutura-da-pasta-docs)
   - [Exemplo de Arquivo `async_apis.md`](#exemplo-de-arquivo-async_apismd)

---

## **1. Visão Geral**

As APIs assíncronas da QBem utilizam **RabbitMQ** para processamento baseado em filas e **Kafka** para comunicação baseada em tópicos. Esses sistemas garantem uma comunicação robusta, permitindo que os serviços sejam escalados e desacoplados.

### **Casos de Uso para APIs Assíncronas**

- **Processamento de Dados em Massa**: Ideal para serviços que processam grandes volumes de eventos, como logs ou atualizações em lote.
- **Notificações em Tempo Real**: Envio de notificações e atualizações de status entre serviços ou para clientes finais.
- **Desacoplamento de Serviços**: Isola produtores e consumidores para maior resiliência e flexibilidade na arquitetura.

[Voltar ao Índice](#índice)

---

## **2. Estrutura de Mensagens**

Todas as mensagens devem ser padronizadas no formato JSON para facilitar a integração entre serviços.

### **Exemplo de Estrutura de Mensagem**

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

### **Campos Recomendados**

- **event**: Identificador único do tipo de evento (e.g., `order.created`, `user.updated`).
- **timestamp**: Data e hora do evento no formato ISO 8601.
- **data**: Objeto JSON com os detalhes específicos do evento.

[Voltar ao Índice](#índice)

---

## **3. Configuração de Filas e Tópicos**

Configurações claras de filas e tópicos garantem melhor gerenciamento e desempenho do sistema.

### **RabbitMQ (Message Queue)**

- **Nomes de Fila**:
  - Utilize nomes descritivos, como `orders_queue` ou `notifications_queue`.
- **Prioridade**:
  - Configure filas com base em prioridade para mensagens críticas.
- **TTL (Time to Live)**:
  - Estabeleça tempos de vida para mensagens, reduzindo acúmulo de dados desnecessários.

### **Kafka (Pub/Sub)**

- **Tópicos**:
  - Crie tópicos específicos por domínio de eventos (e.g., `orders`, `users`).
- **Partições**:
  - Aumente o paralelismo configurando múltiplas partições.
- **Replicação**:
  - Defina réplicas para tolerância a falhas.

[Voltar ao Índice](#índice)

---

## **4. Métodos de Comunicação**

### **RabbitMQ**

- **LPUSH/BRPOP**: Inserção e consumo de mensagens síncronas ou assíncronas.
- **Pub/Sub**: Suporte básico para notificações em tempo real.

**Exemplo de Inserção e Consumo:**

```bash
# Adicionar mensagem na fila
LPUSH orders_queue '{"event":"order.created","timestamp":"2024-10-29T12:34:56Z"}'

# Consumir mensagem da fila
BRPOP orders_queue 0
```

### **Kafka**

- **Produtores**: Enviam mensagens para tópicos específicos.
- **Consumidores**: Consomem mensagens de acordo com o grupo de consumidores.
- **Offsets**: Rastreiam a posição de leitura de cada consumidor.

**Exemplo em Python:**

```python
from kafka import KafkaProducer, KafkaConsumer

# Produção
producer = KafkaProducer(bootstrap_servers='localhost:9092')
producer.send('orders', b'{"event":"order.created"}')
producer.close()

# Consumo
consumer = KafkaConsumer('orders', group_id='order_processors', bootstrap_servers='localhost:9092')
for message in consumer:
    print(message.value)
```

[Voltar ao Índice](#índice)

---

## **5. Cabeçalhos e Metadados**

### **RabbitMQ**

- Metadados devem ser incluídos no corpo JSON das mensagens.

### **Kafka**

- **Cabeçalhos Sugeridos**:
  - `X-Request-Id`: ID único da mensagem para rastreamento.
  - `X-Retry-Count`: Contador de tentativas para lógica de retry.
  - `X-Timestamp`: Data/hora de criação da mensagem.

[Voltar ao Índice](#índice)

---

## **6. Boas Práticas para APIs Assíncronas**

1. **Padronização**:
   - Mantenha estruturas consistentes para todas as mensagens.
2. **Tolerância a Falhas**:
   - Configure lógica de retry com limites de tentativas.
3. **Monitoramento**:
   - Implante ferramentas como Prometheus e Grafana para rastrear mensagens.
4. **Idempotência**:
   - Garanta que mensagens processadas mais de uma vez não causem inconsistências.
5. **Segurança**:
   - Controle acesso a filas e tópicos com ACLs e autenticação.
6. **Compactação**:
   - Comprima mensagens maiores para reduzir tráfego de rede.
7. **Auditoria**:
   - Registre todas as mensagens processadas para rastreamento.

[Voltar ao Índice](#índice)

---

## **7. Documentação e Exemplo de Mensagens**

### **Estrutura da Pasta `docs`**

```
/docs
 ├── async_apis.md                # Documentação detalhada de APIs assíncronas
 ├── kafka_requests.http          # Exemplos de requisições Kafka
 ├── rabbitmq_requests.http       # Exemplos de requisições RabbitMQ
 └── postman_collection.json      # Coleção do Postman para APIs assíncronas
```

### **Exemplo de Arquivo `async_apis.md`**

```markdown
# Documentação das APIs Assíncronas da QBem

## Eventos Suportados

### **order.created**
- **Descrição**: Evento disparado ao criar um novo pedido.
- **Payload**:
  ```json
  {
    "event": "order.created",
    "timestamp": "2024-10-29T12:34:56Z",
    "data": {
      "order_id": "7890",
      "customer_id": "12345",
      "total": 150.00,
      "status": "pending"
    }
  }
  ```

### **order.updated**
- **Descrição**: Evento disparado ao atualizar um pedido.
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

[Voltar ao Índice](#índice)
