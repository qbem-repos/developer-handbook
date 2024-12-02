# 🚀 **Diretrizes e Boas Práticas para Webhooks da QBem**

Este documento descreve diretrizes e práticas recomendadas para a implementação e uso de webhooks em projetos da QBem. Webhooks notificam sistemas externos sobre eventos relevantes em tempo real, eliminando a necessidade de polling e promovendo integração eficiente e segura.

---

## **Índice**

1. [Visão Geral](#visão-geral)
2. [Estrutura e Payload do Webhook](#estrutura-e-payload-do-webhook)
3. [URL de Webhook](#url-de-webhook)
4. [Métodos HTTP e Códigos de Status](#métodos-http-e-códigos-de-status)
5. [Cabeçalhos Customizados e Segurança](#cabeçalhos-customizados-e-segurança)
   - [Cabeçalhos Customizados](#cabeçalhos-customizados)
   - [Assinatura e Verificação](#assinatura-e-verificação)
6. [Boas Práticas para Webhooks](#boas-práticas-para-webhooks)
7. [Documentação e Exemplos](#documentação-e-exemplos)
   - [Estrutura da Pasta `docs`](#estrutura-da-pasta-docs)
   - [Exemplo de `webhooks.md`](#exemplo-de-webhooksmd)

---

## 🌍 **Visão Geral**

Webhooks enviam dados estruturados para sistemas externos sempre que um evento ocorre no sistema da QBem. Eles suportam comunicação em tempo real, permitindo que clientes e serviços sejam notificados automaticamente sobre mudanças importantes.

**Exemplos de Eventos:**
- Criação de novos usuários.
- Atualizações de status em pedidos.
- Exclusão de recursos específicos.

[Voltar ao Índice](#índice)

---

## 🛠️ **Estrutura e Payload do Webhook**

Webhooks enviam dados em **JSON** com detalhes do evento que ocorreu. O payload deve ser claro, descritivo e padronizado para garantir facilidade de integração.

### **Exemplo de Payload**
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

### **Campos Recomendados**
- **event**: Identificador único do evento (e.g., `user.created`, `order.updated`).
- **timestamp**: Data e hora do evento em formato ISO 8601.
- **data**: Detalhes específicos do evento (customizáveis por evento).

**Nota:** Evite incluir dados sensíveis no payload.

[Voltar ao Índice](#índice)

---

## 🌐 **URL de Webhook**

A URL de webhook é fornecida pelo sistema receptor e deve atender aos seguintes requisitos:

1. **HTTPS obrigatório**: Todas as comunicações devem ser criptografadas para proteger os dados.
2. **Resiliência**: A URL deve estar preparada para lidar com falhas temporárias.
3. **Compatibilidade**: Deve aceitar requisições HTTP `POST` com payload JSON.

**Exemplo de URL de Webhook**:
```plaintext
https://webhooks.external-system.com/qbem/events
```

### **Melhores Práticas**
- Utilize URLs exclusivas por tipo de evento para simplificar o processamento.
- Realize verificações de permissões e autenticação na URL para garantir que apenas webhooks autorizados sejam aceitos.

[Voltar ao Índice](#índice)

---

## 🔄 **Métodos HTTP e Códigos de Status**

### **Método HTTP**
- **POST**: O webhook envia o payload JSON para a URL configurada.

### **Códigos de Status**
- **200 OK**: Indica que o webhook foi recebido e processado com sucesso.
- **400 Bad Request**: Problemas no payload ou URL malformada.
- **404 Not Found**: Endpoint não encontrado no receptor.
- **5xx (500, 503)**: Erro temporário no servidor receptor.

### **Reenvio de Webhook**
- Implementar **retry automático** com intervalos exponenciais em caso de erros temporários (5xx). 
- Definir um número máximo de tentativas (e.g., 5 tentativas).

**Estratégia de Retry:**
1. Tentar novamente após 10 segundos.
2. Dobrar o intervalo em cada tentativa subsequente.
3. Encerrar após o número máximo de tentativas.

[Voltar ao Índice](#índice)

---

## 🔒 **Cabeçalhos Customizados e Segurança**

### **Cabeçalhos Customizados**

Adicione cabeçalhos específicos para rastreamento e autenticação do webhook:

- **X-QBem-Event**: Nome do evento enviado.
- **X-QBem-Signature**: Assinatura HMAC para validar a integridade do payload.
- **X-QBem-Timestamp**: Hora do envio, usado para prevenir ataques de repetição.

### **Assinatura e Verificação**

A assinatura HMAC permite que o receptor valide a origem do webhook.

#### **Como Funciona**
1. QBem gera uma assinatura usando HMAC-SHA256.
2. A chave secreta é compartilhada entre QBem e o receptor.
3. O receptor recria a assinatura localmente e a compara com o valor recebido.

#### **Exemplo de Verificação em Python**
```python
import hmac
import hashlib

# Dados para verificação
secret = b'shared-secret'
payload = b'{"event":"user.created", ...}'  # Corpo recebido
signature = "sha256=abcdef1234567890"

# Gerar assinatura localmente
local_signature = 'sha256=' + hmac.new(secret, payload, hashlib.sha256).hexdigest()

# Validar assinatura
if hmac.compare_digest(local_signature, signature):
    print("Assinatura válida!")
else:
    print("Assinatura inválida!")
```

[Voltar ao Índice](#índice)

---

## ✅ **Boas Práticas para Webhooks**

1. **HTTPS Obrigatório**: Nunca use URLs não criptografadas.
2. **Timeouts e Retries**: Configure um timeout (e.g., 5 segundos) e implemente lógica de retries com backoff exponencial.
3. **Validação de Assinatura**: Sempre valide a assinatura para evitar acesso não autorizado.
4. **Idempotência**: Certifique-se de que eventos repetidos não causem efeitos indesejados.
5. **Registre Logs**: Mantenha registros detalhados dos webhooks enviados, respostas recebidas e falhas.
6. **Controle de Eventos**: Permita que os receptores escolham quais eventos desejam receber.
7. **Envio em Lote (Opcional)**: Para alta frequência de eventos, considere agrupar notificações para reduzir tráfego.

[Voltar ao Índice](#índice)

---

## 📚 **Documentação e Exemplos**

Mantenha uma documentação clara e acessível sobre os webhooks disponíveis.

### **Estrutura da Pasta `docs`**

```
/docs
 ├── webhooks.md               # Descrição dos eventos e estrutura de payloads
 ├── webhook_requests.http      # Exemplos de requisições HTTP de webhooks
 └── postman_collection.json    # Coleção do Postman para testes de webhooks
```

### **Exemplo de `webhooks.md`**

```markdown
# Documentação de Webhooks da QBem

## Eventos Suportados

1. **user.created**
   - **Descrição**: Disparado quando um novo usuário é criado.
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

2. **user.updated**
   - **Descrição**: Disparado quando um usuário é atualizado.
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

## Testando Webhooks

- Use o arquivo `webhook_requests.http` para simular chamadas.
- A coleção `postman_collection.json` fornece exemplos prontos para testes no Postman.
```

[Voltar ao Índice](#índice)
