# Clean Code

"Clean Code" refere-se a práticas recomendadas na escrita de software para garantir maior legibilidade, manutenibilidade e qualidade no código. Adotar essas práticas melhora a comunicação entre desenvolvedores, facilita a manutenção e reduz a incidência de erros.

## Índice
- [Clean Code](#clean-code)
  - [Índice](#índice)
  - [1. Nomes São Importantes](#1-nomes-são-importantes)
  - [2. Regra do Escoteiro](#2-regra-do-escoteiro)
  - [3. Seja o Verdadeiro Autor do Código](#3-seja-o-verdadeiro-autor-do-código)
  - [4. DRY (Don’t Repeat Yourself)](#4-dry-dont-repeat-yourself)
  - [5. Comente Apenas o Necessário](#5-comente-apenas-o-necessário)
  - [6. Tratamento de Erros](#6-tratamento-de-erros)
  - [7. Testes Limpos](#7-testes-limpos)

## 1. Nomes São Importantes

Definir nomes claros é essencial para o entendimento de um código. Isso inclui variáveis, funções, parâmetros, classes e métodos. Ao nomear algo, tenha em mente dois pontos principais:

1. O nome deve ser preciso e direto, expressando a ideia central.
2. Não hesite em usar nomes longos, caso necessário, para garantir clareza.

**Exemplo em .NET 8:**

```csharp
// Ruim
var a = new List<Product>();

// Bom
var productList = new List<Product>();

// Melhor
var availableProducts = new List<Product>();
```

[Voltar ao Índice](#índice)

## 2. Regra do Escoteiro

A regra do escoteiro sugere que devemos **deixar o código melhor do que estava ao encontrá-lo**. Ao fazer uma alteração, aproveite para melhorar trechos que possam estar confusos, simplificar lógicas e remover duplicações.

**Exemplo em .NET 8:**

```csharp
// Antes da limpeza
public void ProcessOrder(Order order)
{
    if (order.Status == "Pending") { /*...*/ }
    if (order.Status == "Shipped") { /*...*/ }
    if (order.Status == "Delivered") { /*...*/ }
}

// Depois da limpeza
public void ProcessOrder(Order order)
{
    switch (order.Status)
    {
        case OrderStatus.Pending: /*...*/ break;
        case OrderStatus.Shipped: /*...*/ break;
        case OrderStatus.Delivered: /*...*/ break;
    }
}
```

[Voltar ao Índice](#índice)

## 3. Seja o Verdadeiro Autor do Código

Escrever código é contar uma história, e a clareza é essencial. Para manter essa clareza, funções devem ser **simples, claras e pequenas**. Uncle Bob sugere duas regras para funções:

- Funções precisam ser pequenas.
- Funções devem ser ainda menores.

**Exemplo em .NET 8:**

```csharp
// Função complexa e grande
public void UpdateCustomerInfo(Customer customer)
{
    ValidateCustomer(customer);
    UpdateDatabase(customer);
    SendUpdateEmail(customer);
    LogCustomerUpdate(customer);
}

// Funções menores e mais claras
public void UpdateCustomerInfo(Customer customer)
{
    ValidateCustomer(customer);
    UpdateCustomerRecord(customer);
    NotifyCustomer(customer);
    LogUpdate(customer);
}

private void ValidateCustomer(Customer customer) { /*...*/ }
private void UpdateCustomerRecord(Customer customer) { /*...*/ }
private void NotifyCustomer(Customer customer) { /*...*/ }
private void LogUpdate(Customer customer) { /*...*/ }
```

[Voltar ao Índice](#índice)

## 4. DRY (Don’t Repeat Yourself)

O princípio DRY (não se repita) sugere que devemos evitar duplicação de código. Isso reduz inconsistências e facilita a manutenção. Cada pedaço de conhecimento deve ter uma única, inequívoca e bem definida representação.

**Exemplo em .NET 8:**

```csharp
// Código repetitivo
public void AddCustomer(Customer customer)
{
    if (IsValidCustomer(customer))
    {
        // Adicionar cliente à base de dados
    }
}

public void UpdateCustomer(Customer customer)
{
    if (IsValidCustomer(customer))
    {
        // Atualizar cliente na base de dados
    }
}

// Código aplicando DRY
public void AddOrUpdateCustomer(Customer customer)
{
    if (IsValidCustomer(customer))
    {
        // Inserir ou atualizar cliente na base de dados
    }
}
```

[Voltar ao Índice](#índice)

## 5. Comente Apenas o Necessário

Comentários são úteis, mas devem ser usados com cautela. Uncle Bob afirma que **comentários mentem**, pois enquanto o código é atualizado, os comentários muitas vezes ficam desatualizados, criando confusão. Prefira **código autoexplicativo** e comente apenas onde necessário.

**Exemplo em .NET 8:**

```csharp
// Ruim: Comentário desnecessário
// Este método adiciona um produto ao inventário
public void AddProductToInventory(Product product)
{
    inventory.Add(product);
}

// Bom: Comentário somente quando necessário
public void UpdateCustomerStatus(Customer customer)
{
    // Atualizar status para "VIP" apenas se o cliente tiver comprado mais de 10 vezes
    if (customer.PurchaseCount > 10)
    {
        customer.Status = CustomerStatus.VIP;
    }
}
```

[Voltar ao Índice](#índice)

## 6. Tratamento de Erros

Tratar erros adequadamente é fundamental. Um bom tratamento de erros garante que o sistema continue a funcionar e que informações sobre o problema estejam acessíveis.

**Exemplo em .NET 8:**

```csharp
// Tratamento de erro adequado
public void ProcessOrder(Order order)
{
    try
    {
        // Código de processamento do pedido
    }
    catch (OrderProcessingException ex)
    {
        // Log do erro para futura análise
        LogError(ex);
        throw; // Propaga a exceção para ser tratada em outro lugar
    }
}
```

[Voltar ao Índice](#índice)

## 7. Testes Limpos

Testes são essenciais para garantir que o código funciona como esperado. Um código só é considerado limpo quando coberto por testes bem estruturados. Boas práticas para testes incluem:

- **Fast (Rápido)**: O teste deve ser rápido para execuções frequentes.
- **Independent (Independente)**: Cada teste deve ser isolado para evitar falhas em cascata.
- **Repeatable (Repetível)**: Testes devem poder ser executados em qualquer ambiente.
- **Self-Validating (Auto-Validante)**: Testes retornam verdadeiro ou falso.
- **Timely (Pontual)**: Escreva os testes no momento certo, de preferência antes do código.

**Exemplo em .NET 8 usando xUnit:**

```csharp
public class OrderTests
{
    [Fact] // Fast
    public void Order_TotalAmount_ShouldBeCorrect()
    {
        // Arrange
        var order = new Order();
        order.AddItem(new Product { Price = 10.0 }, quantity: 2);

        // Act
        var total = order.GetTotalAmount();

        // Assert
        Assert.Equal(20.0, total); // Self-Validating
    }

    [Fact] // Independent
    public void Order_ShouldBeEmpty_Initially()
    {
        var order = new Order();
        Assert.Empty(order.Items); // Self-Validating
    }
}
```

[Voltar ao Índice](#índice)