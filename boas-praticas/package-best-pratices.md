# Package & Toolkit: Boas Práticas

## Boas praticas

2) Os pacotes devem abstrair processos e serviços internos e não frameworks ou SDKs;
3) Os pacotes devem oferecer de forma segregada(subpacotes) de: Abstrações, Exceções e Mocks;
4) Os nomes dos pacotes devem ser descritivos: *Qbem.Toolkit.Service.Notification; Qbem.Toolkit.Data.Publisher*.
5) Use interfaces para definir implementações de configurações;
6) No desenvolvimento, prefira usar o modelo Screamming para nomeclatura das pastas e pacotes.
7) Os pacotes devem ter os seu próprio repositórios

## Evite

- Usar variáveis de ambiente para configuração
- Usar toolkits para encapsular tecnologia como Kafka, Banco de dados, etc.
- Criar pacotes sem testes
- Criar vários pacotes em um mesmo repositório git
