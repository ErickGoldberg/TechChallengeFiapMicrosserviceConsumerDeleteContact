# TCFiap Consumer Delete Contact API

Este projeto é um serviço .NET que consome mensagens para remoção de contatos através do MassTransit e RabbitMQ. Quando uma mensagem do tipo `RemoveContactMessage` é recebida, o serviço busca o contato no banco de dados e o remove.

## Sumário

- [Visão Geral](#visão-geral)
- [Recursos](#recursos)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Pré-requisitos](#pré-requisitos)
- [Como Executar o Projeto](#como-executar-o-projeto)
  - [Executando Localmente](#executando-localmente)
  - [Utilizando Docker](#utilizando-docker)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Testes](#testes)
- [Configuração](#configuração)
- [Licença](#licença)

## Visão Geral

O TCFiap Consumer Delete Contact é responsável por processar mensagens de remoção de contatos, integrando-se com:

- **MassTransit** para gerenciamento e consumo de mensagens via RabbitMQ;
- **TechChallenge.SDK** para operações de persistência e funcionalidades específicas do domínio;
- **Worker Service** que mantém a execução do consumo de mensagens de forma contínua.

O serviço implementa mecanismos de log para monitoramento e utiliza uma fila específica (`delete-contact-queue`) com suporte a Dead Letter Exchange para tratamento de mensagens que não podem ser processadas corretamente.

## Recursos

- **Consumo de mensagens:** Recebe e processa mensagens para remoção de contatos.
- **Integração com RabbitMQ:** Configuração de endpoint e Dead Letter Exchange para gerenciamento de mensagens com falha.
- **Persistência:** Utiliza um repositório para acesso ao banco de dados.
- **Logs:** Informações detalhadas de operações e tratamento de exceções.
- **Testes:** Casos unitários e testes de integração utilizando NUnit e Moq.

## Tecnologias Utilizadas

- .NET 8
- MassTransit
- RabbitMQ
- Docker
- NUnit
- Moq
- TechChallenge.SDK (repositório interno)

## Pré-requisitos

- .NET SDK 8.0
- RabbitMQ (disponível localmente ou via container)
- Variável de ambiente ou configuração de connection string para o banco de dados:
  - `CONNECTION_DATABASE`: string de conexão para o banco de dados.
- Caso utilize o Docker, é necessário ter o Docker instalado.

## Como Executar o Projeto

### Executando Localmente

Clone o repositório:

```
git clone https://seurepositorio.com/TCFiapConsumerDeleteContact.git
cd TCFiapConsumerDeleteContact
```

Configure a connection string:

Defina a variável de ambiente `CONNECTION_DATABASE` ou configure o arquivo de configuração (`appsettings.json`) com a connection string do banco de dados.

Restaure os pacotes e compile o projeto:

```
dotnet restore
dotnet build
```

Execute a aplicação:

```
dotnet run --project TCFiapConsumerDeleteContact.API
```

### Utilizando Docker

O projeto contém um Dockerfile que implementa um processo de build multi-stage.

Defina o argumento de senha do NuGet (caso necessário):

Certifique-se de definir a variável de ambiente ou passar o argumento `ARG_SECRET_NUGET_PACKAGES` para acessar os pacotes privados.

Construa a imagem Docker:

```
docker build --build-arg ARG_SECRET_NUGET_PACKAGES=SuaSenhaAqui -t tcfiap-consumer-delete-contact .
```
   
Execute o container:

```
docker run -d -p 8080:8080 --env CONNECTION_DATABASE="SuaConnectionString" tcfiap-consumer-delete-contact
```


## Estrutura do Projeto

- **TCFiapConsumerDeleteContact.API:** Projeto principal contendo o serviço de consumo, configuração do MassTransit e Worker Service.
- **Program.cs:** Configuração do host, serviços, MassTransit e registro do SDK.
- **RemoveContactConsumer.cs:** Implementação do consumidor responsável por processar a mensagem de remoção.
- **Worker.cs:** Serviço de background que gerencia o ciclo de vida do bus.
- **Dockerfile:** Configuração para build e publicação da aplicação.

### Testes

- **TCFiapConsumerDeleteContact.Tests.UnitTests:** Casos de teste unitários para o consumidor.
- **TCFiapConsumerDeleteContact.Tests.IntegrationTests:** Testes de integração utilizando o InMemoryTestHarness do MassTransit.

## Testes

O projeto inclui testes unitários e de integração utilizando o framework NUnit e Moq para simular comportamentos.

- **Testes Unitários:** Focados na lógica de remoção de contatos e verificação dos logs gerados.
- **Testes de Integração:** Validação do fluxo completo de consumo de mensagens utilizando o MassTransit In-Memory Test Harness.

## Configuração

**MassTransit & RabbitMQ:**

- **Fila de recebimento:** `delete-contact-queue`
- **Dead Letter Exchange:** `delete-contact-dlx-exchange`
- **Dead Letter Routing Key:** `delete-contact-dlx`

**SDK:** O projeto utiliza o método `RegisterSdkModule` para registrar o módulo do TechChallenge.SDK com a connection string do banco de dados.

**Variáveis de Ambiente:**

- `CONNECTION_DATABASE`: String de conexão com o banco de dados.
