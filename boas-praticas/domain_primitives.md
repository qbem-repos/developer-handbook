# Domain Primitives

**Domain Primitives** são tipos primitivos encapsulados em classes que representam valores de domínio específicos, como `GUID`, `Email`, `Moeda`, `URL`, entre outros. Esses tipos encapsulam pequenas regras de negócio diretamente relacionadas ao valor que representam. Esse padrão ajuda a evitar o anti-padrão conhecido como **primitives-hell**, onde tipos primitivos comuns (como `string` ou `decimal`) são utilizados sem contexto ou validação específica, o que pode levar a inconsistências e erros.

O uso de Domain Primitives oferece várias vantagens:
- Simplifica o código nas classes clientes.
- Centraliza e reforça as regras de validação.
- Melhora a legibilidade, pois o tipo comunica a intenção diretamente.

## Estrutura de um Domain Primitive

Abaixo está um exemplo de implementação de um **Domain Primitive** para o tipo `Email`, com regras de validação para garantir que o valor esteja em um formato de email válido.

```csharp
using System;
using System.Text.RegularExpressions;

public struct Email 
{
    private string Value { get; }

    private Email(string value)
    {
        Value = value;
        ThrowExceptionIfIsInvalid(value);
    }

    public static implicit operator string(Email email) 
        => email.Value;

    public static implicit operator Email(string email) 
        => new Email(email);

    private static void ThrowExceptionIfIsInvalid(string value) 
    {
        var pattern = @"[a-z0-9\-\.]{3,}@[a-z0-9]{3,}\.[a-z]{2,3}(\.[a-z]{2})?$";
        var reg = new Regex(pattern, RegexOptions.IgnoreCase);
        if (!reg.IsMatch(value))
        {
            throw new FormatException("The email should be in a valid format.");
        }
    }
}
```

### Explicação da Estrutura
1. **Atributo `Value`**: Atributo `private` que armazena o valor do email, garantindo que seja imutável após a criação.
2. **Construtor `private`**: O construtor privado inicializa o `Value` e chama a validação.
3. **Operadores Implícitos**: 
   - Permitem conversão implícita para e de `string`, simplificando o uso do tipo `Email`.
4. **Validação de Formato**: O método `ThrowExceptionIfIsInvalid` verifica se o email está no formato correto e lança uma exceção `FormatException` em caso de formato inválido.

### Exemplo de Uso

Com essa estrutura, podemos facilmente criar e utilizar `Email` com validação integrada:

```csharp
// Criação e conversão implícita
Email email = "fulano@domain.com.br";

// Conversão explícita
var emailString = (string)email; // "fulano@domain.com.br"

// Exceção de formato inválido
Email invalidEmail = "fulano@te.s"; // Lança FormatException
```

## Boas Práticas para Domain Primitives

1. **Encapsulamento de Regras de Negócio**: Regras de validação e restrições devem estar encapsuladas dentro do Domain Primitive, evitando a repetição dessas lógicas em várias partes do código.
2. **Operadores Implícitos**: Utilize operadores implícitos para simplificar a conversão entre o tipo primitivo e o Domain Primitive.
3. **Imutabilidade**: Implemente Domain Primitives como `struct` ou com atributos `readonly` para garantir que os valores sejam imutáveis após a criação.
4. **Exceções Específicas**: Lance exceções específicas, como `FormatException`, para facilitar o tratamento de erros em casos de validação.
5. **Reutilização**: Utilize Domain Primitives em toda a aplicação onde valores de domínio específicos (como email, moeda, etc.) sejam usados para reforçar as regras e evitar validações redundantes.

### Benefícios do Padrão

Utilizar Domain Primitives traz benefícios importantes para o código, como:
- **Redução de Erros**: Com validações centralizadas, o risco de inconsistências no formato e na lógica é reduzido.
- **Melhor Legibilidade**: O código se torna mais expressivo, indicando claramente a intenção dos valores utilizados.
- **Reforço de Regras de Negócio**: Garantia de que os dados obedecem às regras de negócio sempre que são manipulados.

Esse padrão contribui para um código mais confiável, seguro e fácil de manter.