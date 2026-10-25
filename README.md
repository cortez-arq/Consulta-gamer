# SaaS App

Aplicação web desenvolvida em Java para consulta e cadastro de clientes, com persistência em MySQL e execução no Apache Tomcat.

## Tecnologias

- Java 17
- Jakarta/Java Servlet API compatível com Apache Tomcat 9 (`javax.servlet`)
- Apache Tomcat 9
- Maven
- MySQL 8.4
- JDBC
- MySQL Connector/J

## Funcionalidades atuais

- Consultar e exibir clientes cadastrados.
- Cadastrar clientes por requisição HTTP POST.
- Persistir os dados no banco de dados MySQL.

## Arquitetura

A aplicação é empacotada como WAR pelo Maven e implantada no Apache Tomcat. A classe `App.java` implementa o servlet de clientes, enquanto `Conexao.java` gerencia a conexão JDBC com o banco.

```text
Navegador
   |
   v
Apache Tomcat 9
   |
   v
Servlet Java (App.java)
   |
   v
Conexao.java / JDBC
   |
   v
MySQL (saas_db)
```

## Pré-requisitos

- JDK 17 instalado.
- Maven instalado.
- Apache Tomcat 9 instalado e em execução.
- MySQL 8.4 em execução.
- Banco `saas_db`, tabela `clientes` e usuário de banco configurados.

## Configuração do banco

Crie o banco e configure a tabela conforme os campos utilizados pela aplicação. Exemplo de criação do banco:

```sql
CREATE DATABASE saas_db;
```

A tabela `clientes` deve conter as colunas esperadas pelo código de `App.java`. Confira o código-fonte antes de executar um `CREATE TABLE`, para que os tipos e nomes correspondam exatamente ao servlet.

Configure o usuário `app_user` com acesso somente ao banco necessário. Não reutilize a conta administrativa do MySQL pela aplicação.

## Configuração da conexão

A classe `Conexao.java` utiliza atualmente `DriverManager`. Verifique a URL JDBC, o usuário e a senha configurados nessa classe antes de executar a aplicação.

**Segurança:** não publique senhas reais no Git. Para um ambiente compartilhado ou de produção, prefira variáveis de ambiente ou um mecanismo seguro de gestão de segredos.

O projeto também possui uma configuração `context.xml` de exemplo para um `DataSource` JNDI. Esse recurso só será usado depois que `Conexao.java` for adaptada para consultar `java:comp/env/jdbc/saasDB`. Não mantenha duas configurações contraditórias.

## Compilar e empacotar

Na raiz do projeto:

```bash
mvn clean package
```

O arquivo WAR será gerado em `target/`.

## Implantar no Tomcat

Copie o WAR gerado para o diretório `webapps` do Tomcat. Exemplo, ajustando o caminho conforme a instalação:

```bash
sudo cp target/saas-app.war /opt/tomcat/webapps/
sudo systemctl restart tomcat
```

O nome do arquivo WAR determina o caminho padrão da aplicação. Se o arquivo for `saas-app.war`, o endereço será semelhante a:

```text
http://IP_DO_SERVIDOR:8080/saas-app/clientes
```

Substitua `IP_DO_SERVIDOR` pelo endereço IP do servidor. A porta e o caminho dependem da configuração efetiva do Tomcat.

## Estrutura resumida

```text
saas-app/
├── src/
│   └── main/
│       ├── java/
│       │   └── br/com/saas/
│       │       ├── App.java
│       │       └── Conexao.java
│       └── webapp/
│           └── META-INF/
│               └── context.xml
├── docs/
│   └── adr/
│       └── 001-arquitetura-da-aplicacao.md
├── pom.xml
└── README.md
```

A estrutura acima é uma referência de organização; confirme os caminhos reais do repositório antes de mover arquivos.

## Diagnóstico básico

- Verifique o estado do Tomcat: `sudo systemctl status tomcat`
- Consulte os logs: `sudo tail -n 100 /opt/tomcat/logs/catalina.out`
- Confirme que o MySQL está ativo e que o usuário da aplicação consegue acessar `saas_db`.
- Se a aplicação não abrir, confira o IP do servidor, a porta 8080, o nome do WAR e o contexto implantado.

## Decisões de arquitetura

Consulte [`docs/adr/001-arquitetura-da-aplicacao.md`](docs/adr/001-arquitetura-da-aplicacao.md) para ver o registro da decisão arquitetural, as justificativas, as alternativas e as consequências.

## Status

Projeto em desenvolvimento. As instruções devem ser ajustadas conforme a evolução do código e do ambiente de implantação.
