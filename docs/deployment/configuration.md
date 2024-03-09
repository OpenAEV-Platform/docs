# Configuration

The purpose of this section is to learn how to configure OpenBAS to have it tailored for your production and development needs.

OpenBAS is a JAVA process and therefore can be configured manually using the configuration file `application.properties` or environment variables when using Docker.

## Platform

### Basic parameters

| Parameter                      | Environment variable           | Default value         | Description                                                   |
|:-------------------------------|:-------------------------------|:----------------------|:--------------------------------------------------------------|
| server.address                 | SERVER_ADDRESS                 | 0.0.0.0               | Listen address of the application                             |
| server.port                    | SERVER_PORT                    | 8080                  | Listen port of the application                                |
| openbas.base-url                | OPENBAS_BASE-URL                | http://localhost:8080 | Base URL of the application, will be used in some email links |
| server.servlet.session.timeout | SERVER_SERVLET_SESSION_TIMEOUT | 20m                   | Default duration of session (20 minutes)                      |
| openbas.cookie-secure           | OPENBAS_COOKIE-SECURE           | false                 | Turn on if the access is done in HTTPS                        |
| openbas.admin.email             | OPENBAS_ADMIN_EMAIL             | admin@openbas.io       | Default login email of the admin user                         |
| openbas.admin.password          | OPENBAS_ADMIN_PASSWORD          | ChangeMe              | Default password of the admin user                            |
| openbas.admin.token             | OPENBAS_ADMIN_TOKEN             | ChangeMe              | Default token (must be a valid UUIDv4)                        |

### Network and security

| Parameter                     | Environment variable          | Default value           | Description                               |
|:------------------------------|:------------------------------|:------------------------|:------------------------------------------|
| server.ssl.enabled            | SERVER_SSL_ENABLED            | false                   | Turn on to enable SSL on the local server |
| server.ssl.key-store-type     | SERVER_SSL_KEY-STORE-TYPE     | PKCS12                  | Type of SSL keystore                      |
| server.ssl.key-store          | SERVER_SSL_KEY-STORE          | classpath:localhost.p12 | SSL keystore path                         |
| server.ssl.key-store-password | SERVER_SSL_KEY-STORE-PASSWORD | admin                   | SSL keystore password                     |
| server.ssl.key-alias          | SERVER_SSL_KEY-ALIAS          | localhost               | SSL key alias                             |

### Logging

| Parameter               | Environment variable    | Default value     | Description                                   |
|:------------------------|:------------------------|:------------------|:----------------------------------------------|
| logging.level.root      | LOGGING_LEVEL_ROOT      | fatal             | Root log level                                |
| logging.level.io.openbas | LOGGING_LEVEL_IO_OPENBAS | warn              | OpenBAS log level                              |
| logging.file.name       | LOGGING_FILE_NAME       | ./logs/openbas.log | Log file path (in addition to console output) |


## Dependencies

### PostgreSQL

| Parameter                  | Environment variable       | Default value         | Description                                                                    |
|:---------------------------|:---------------------------|:----------------------|:-------------------------------------------------------------------------------|
| spring.datasource.url      | SPRING_DATASOURCE_URL      | jdbc:postgresql://... | URL of the database (ex jdbc:postgresql://postgresql.mydomain.com:5432/openbas) |
| spring.datasource.username | SPRING_DATASOURCE_USERNAME | openbas                | Login for the database                                                         |
| spring.datasource.password | SPRING_DATASOURCE_PASSWORD | password              | Password for the database                                                      |

### S3 bucket / MinIO

| Parameter           | Environment variable | Default value | Description                              |
|:--------------------|:---------------------|:--------------|:-----------------------------------------|
| minio.endpoint      | MINIO_ENDPOINT       | localhost     | Hostname of the S3 instance              |
| minio.bucket        | MINIO_BUCKET         | openbas        | Name of the S3 bucket                    |
| minio.port          | MINIO_PORT           | 9000          | Port of the S3 endpoint                  |
| minio.access-key    | MINIO_ACCESS-KEY     | key           | S3 bucket access key (MinIO user)        |
| minio.access-secret | MINIO_ACCESS-SECRET  | secret        | S3 bucket access secret (MinIO password) |

### Mailbox

For the associated mailbox, for the moment the platform only relies on IMAP / SMTP protocols, we are actively developing integrations with APIs such as O365 and Google Apps.

#### IMAP

| Parameter                 | Environment variable      | Default value     | Description                                                                         |
|:--------------------------|:--------------------------|:------------------|:------------------------------------------------------------------------------------|
| openbas.mail.imap.enabled  | OPENBAS_MAIL_IMAP_ENABLED  | false             | Turn on to enable IMAP mail synchronization. Injector email must be well configured |
| openbas.mail.imap.host     | OPENBAS_MAIL_IMAP_HOST     | imap.mail.com     | IMAP Server hostname                                                                |
| openbas.mail.imap.port     | OPENBAS_MAIL_IMAP_PORT     | 993               | IMAP Server port                                                                    |
| openbas.mail.imap.username | OPENBAS_MAIL_IMAP_USERNAME | username@mail.com | IMAP Server username                                                                |
| openbas.mail.imap.password | OPENBAS_MAIL_IMAP_PASSWORD | password          | IMAP Server password                                                                |
| openbas.mail.imap.inbox    | OPENBAS_MAIL_IMAP_INBOX    | INBOX             | IMAP inbox directory to synchronize from                                            |
| openbas.mail.imap.sent     | OPENBAS_MAIL_IMAP_SENT     | Sent              | IMAP sent directory to synchronize from                                             |

| Parameter                        | Environment variable             | Default value | Description                   |
|:---------------------------------|:---------------------------------|:--------------|:------------------------------|
| openbas.mail.imap.ssl.enable      | OPENBAS_MAIL_IMAP_SSL_ENABLE      | true          | Turn on IMAP SSL mode         |
| openbas.mail.imap.ssl.trust       | OPENBAS_MAIL_IMAP_SSL_TRUST       | *             | Trust unverified certificates |
| openbas.mail.imap.auth            | OPENBAS_MAIL_IMAP_AUTH            | true          | Turn on IMAP authentication   |
| openbas.mail.imap.starttls.enable | OPENBAS_MAIL_IMAP_STARTTLS_ENABLE | true          | Turn on IMAP STARTTLS         |

#### SMTP

| Parameter             | Environment variable | Default value     | Description          |
|:----------------------|:---------------------|:------------------|:---------------------|
| spring.mail.host      | SPRING_MAIL_HOST     | smtp.mail.com     | SMTP Server hostname |
| spring.mail.port      | SPRING_MAIL_PORT     | 587               | SMTP Server port     |
| spring.mail.username  | SPRING_MAIL_USERNAME | username@mail.com | SMTP Server username |
| spring.mail.password  | SPRING_MAIL_PASSWORD | password          | SMTP Server password |

| Parameter                                        | Environment variable                             | Default value | Description                   |
|:-------------------------------------------------|:-------------------------------------------------|:--------------|:------------------------------|
| spring.mail.properties.mail.smtp.ssl.enable      | SPRING_MAIL_PROPERTIES_MAIL_SMTP_SSL_ENABLE      | true          | Turn on SMTP SSL mode         |
| spring.mail.properties.mail.smtp.ssl.trust       | SPRING_MAIL_PROPERTIES_MAIL_SMTP_SSL_TRUST       | *             | Trust unverified certificates |
| spring.mail.properties.mail.smtp.auth            | SPRING_MAIL_PROPERTIES_MAIL_SMTP_AUTH            | true          | Turn on SMTP authentication   |
| spring.mail.properties.mail.smtp.starttls.enable | SPRING_MAIL_PROPERTIES_MAIL_SMTP_STARTTLS_ENABLE | true          | Turn on SMTP STARTTLS         |

!!! note "Default file"

    It is possible to check all default parameters implemented in the platform in the [`application.properties` file](https://github.com/OpenBAS-Platform/openbas/blob/master/openbas-api/src/main/resources/application.properties).

## Injectors (integrations)

For specific injector configuration, you need to check each injector behavior.

!!! question "Injectors list"

    If you are looking to the list of OpenBAS injectors or native integration, please check the [OpenBAS Ecosystem](https://filigran.notion.site/OpenBAS-Ecosystem-30d8eb73d7d04611843e758ddef8941b).
