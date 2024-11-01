# Pacotes e Toolkits: Boas Práticas

Esta documentação define diretrizes para criar pacotes e toolkits de software que sejam organizados, reutilizáveis e de fácil manutenção. Seguir essas práticas melhora a qualidade dos pacotes, promovendo modularidade e uma estrutura mais robusta para o desenvolvimento.

## Índice
- [Pacotes e Toolkits: Boas Práticas](#pacotes-e-toolkits-boas-práticas)
  - [Índice](#índice)
  - [Boas Práticas](#boas-práticas)
    - [1. Abstração de Processos](#1-abstração-de-processos)
    - [2. Segregação de Subpacotes](#2-segregação-de-subpacotes)
    - [3. Nomenclatura Descritiva](#3-nomenclatura-descritiva)
    - [4. Uso de Interfaces](#4-uso-de-interfaces)
    - [5. Modelo Screaming](#5-modelo-screaming)
    - [6. Repositórios Independentes](#6-repositórios-independentes)
  - [Evite](#evite)

## Boas Práticas

### 1. Abstração de Processos

Os pacotes devem **abstrair processos e serviços internos**, e não frameworks ou SDKs diretamente. A função de um pacote é encapsular a lógica de negócios e não expor dependências específicas que possam limitar a flexibilidade.

**Exemplo:**

Errado:
```csharp
public class KafkaHandler
{
    // Lógica de conexão direta com o SDK do Kafka
}
```

Certo:
```csharp
public interface IMessagePublisher
{
    void PublishMessage(string message);
}

public class InternalMessagePublisher : IMessagePublisher
{
    // Implementação usando Kafka internamente, mas sem expor o SDK
}
```

[Voltar ao Índice](#índice)

### 2. Segregação de Subpacotes

Organize os pacotes de forma segregada em subpacotes, agrupando **Abstrações**, **Exceções** e **Mocks**. Isso torna a estrutura do pacote mais organizada e facilita a localização de componentes específicos.

**Exemplo de Estrutura:**
```
Qbem.Toolkit.Service.Notification
 ├── Abstractions
 ├── Exceptions
 └── Mocks
```

[Voltar ao Índice](#índice)

### 3. Nomenclatura Descritiva

Nomes dos pacotes devem ser **claros e descritivos** para indicar sua função no sistema. Use um padrão consistente de nomenclatura para que a função do pacote seja facilmente reconhecida.

**Exemplo de Nomes:**
```plaintext
Qbem.Toolkit.Service.Notification
Qbem.Toolkit.Data.Publisher
```

[Voltar ao Índice](#índice)

### 4. Uso de Interfaces

Defina configurações e comportamentos principais por meio de **interfaces**. Isso garante que a implementação de configurações seja desacoplada e flexível, permitindo fácil troca ou atualização de dependências.

**Exemplo:**

```csharp
public interface INotificationSettings
{
    string SmtpServer { get; }
    int Port { get; }
}

public class EmailNotificationSettings : INotificationSettings
{
    public string SmtpServer => "smtp.qbem.com";
    public int Port => 587;
}
```

[Voltar ao Índice](#índice)

### 5. Modelo Screaming

No desenvolvimento, prefira o uso do **Modelo Screaming** para nomeação de pastas e pacotes, ou seja, dê nomes que deixem claro o propósito do módulo. Dessa forma, a estrutura dos pacotes indica claramente suas funções.

**Exemplo:**

```plaintext
NotificationService
PublisherConfig
UserManagement
```

[Voltar ao Índice](#índice)

### 6. Repositórios Independentes

Cada pacote deve ter seu **próprio repositório**. Essa prática facilita o versionamento, a manutenção e a distribuição independente dos pacotes, além de permitir que as dependências sejam gerenciadas separadamente.

[Voltar ao Índice](#índice)

## Evite

1. **Usar variáveis de ambiente para configuração**: Opte por configurações encapsuladas em arquivos dedicados ou por meio de interfaces.
   
2. **Usar toolkits para encapsular tecnologias específicas (como Kafka, Banco de Dados, etc.)**: Pacotes devem encapsular funcionalidades, não dependências tecnológicas. Mantenha as tecnologias subjacentes ocultas.

3. **Criar pacotes sem testes**: Todo pacote deve incluir testes de unidade e de integração para garantir a estabilidade e prevenir regressões.

4. **Criar vários pacotes em um único repositório git**: Manter pacotes separados por repositório facilita a manutenção e o controle de versões.

[Voltar ao Índice](#índice)