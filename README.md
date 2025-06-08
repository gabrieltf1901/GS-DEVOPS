# NOME: GABRIEL TORRES FERNANDES - RM 553635

# LINK DO VIDEO --> https://youtu.be/WIbOt2gX23E
# LINK AZURE DEVOPS - PROJETO --> https://dev.azure.com/RM553635/Gestão%20de%20Abrigos%20-%20DevOps
# LINK DO PROJETO JAVA --> https://github.com/gabrieltf1901/GS-JAVA.git 

# Gestão de Abrigos e Recursos em Tempo Real

## Descrição do Projeto

Em cenários de desastres naturais (enchentes, tempestades intensas, terremotos), a coordenação ágil de abrigos temporários e o gerenciamento de estoques de recursos (alimentos, água, colchões, medicamentos etc.) são críticos para salvar vidas e organizar o socorro humanitário. Este projeto oferece:

* Cadastro e consulta de abrigos com informações de localização, capacidade e status.
* Controle de estoque de recursos em cada abrigo, com alertas de níveis críticos.
* Interface web baseada em Spring Boot + Thymeleaf para visualização e gerenciamento.
* Integração com fila RabbitMQ para comunicação assíncrona (ex.: notificações de alerta).
* Autenticação e autorização via Spring Security + OAuth2.
* Suporte a IA generativa com Spring AI para assistentes inteligentes (ex.: chat de suporte).

## Tecnologias Utilizadas

* **Linguagem:** Java 17 (Toolchain Gradle)
* **Build:** Gradle Wrapper (`./gradlew`)
* **Framework:** Spring Boot 3.5.0

  * Spring Data JPA (Oracle)
  * Spring Security OAuth2 Client
  * Spring Boot Starter Web, Thymeleaf, Validation, AMQP
  * Spring AI (OpenAI)
* **Banco de Dados:** Oracle DB (via driver `ojdbc8`)
* **Mensageria:** RabbitMQ
* **Pipeline CI/CD:** Azure DevOps + Azure CLI

  * Provisionamento de infra (Resource Group, SQL Server, DB, App Service)
  * Build e publicação de artefatos
  * Deploy automático no Azure App Service

## Pré-requisitos

1. **JDK 17** instalado localmente ou disponível no agente de build.
2. **Docker** (opcional para rodar instância local de Oracle/RabbitMQ).
3. **Azure CLI** configurado com sua subscription.
4. **Service Connection** no Azure DevOps (tipo Azure Resource Manager) autorizada ao pipeline.
5. **RabbitMQ** rodando localmente ou acesso a um broker remoto.

## Configuração do Projeto

1. Clone este repositório:

   ```bash
   git clone https://<seu-repo-url>/gestao-abrigos.git
   cd gestao-abrigos
   ```

2. Defina as propriedades de conexão em `src/main/resources/application.properties` (ou use variáveis de ambiente):

   ```properties
   # Banco de dados (Oracle)
   spring.datasource.url=jdbc:oracle:thin:@<HOST>:<PORT>/<SERVICE_NAME>
   spring.datasource.username=<USUARIO_DB>
   spring.datasource.password=<SENHA_DB>

   # JPA/Hibernate
   spring.jpa.hibernate.ddl-auto=update
   spring.jpa.show-sql=true

   # RabbitMQ
   spring.rabbitmq.host=<RABBIT_HOST>
   spring.rabbitmq.port=<RABBIT_PORT>
   spring.rabbitmq.username=<RABBIT_USER>
   spring.rabbitmq.password=<RABBIT_PASS>
   ```

3. (Opcional) Para rodar um Oracle leve em Docker:

   ```bash
   docker run --name oracle-xe -p 1521:1521 -e ORACLE_PWD=SenhaForte123 -d gvenzl/oracle-xe
   ```

   Atualize a URL e credenciais no `application.properties` conforme acima.

## Executando Localmente

* **Via Gradle Wrapper:**

  ```bash
  ./gradlew bootRun
  ```

  A aplicação ficará disponível em `http://localhost:8080`.

* **Gerando JAR e rodando manualmente:**

  ```bash
  ./gradlew clean build
  java -jar build/libs/gestaoabrigos-0.0.1-SNAPSHOT.jar
  ```

## Pipeline CI/CD (Azure DevOps)

O arquivo `azure-pipelines.yml` define três stages principais:

1. **Infraestrutura**: usa `AzureCLI@2` para criar:

   * Resource Group
   * SQL Server + Database (Basic, redundância Local)
   * App Service Plan (B1 Linux) + Web App Java17
2. **Build e Artefatos**:

   * Instala Java 17 (`JavaToolInstaller@0`)
   * Executa `gradlew clean build`
   * Publica o diretório `build/libs` como artefato (`PublishPipelineArtifact@1`)
3. **Deploy**:

   * Baixa o artefato publicado (`DownloadPipelineArtifact@2`)
   * Realiza deploy do JAR executável no App Service (`AzureWebApp@1`)

## Acesso à Aplicação

* **Local:** `http://localhost:8080`
* **Na nuvem (Azure):** `https://<nome-do-webapp>.azurewebsites.net`


