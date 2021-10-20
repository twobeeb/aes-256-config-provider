# aes-256-config-provider
AES256 ConfigProvider for Apache Kafka Connect.  
Nice to have when using Kafka Connect in a multi-tenant environment.

## Worker config / Admin side

1. Add aes-256-config-provider-1.0.0.jar in ``plugin.path``
2. Edit connect-distributed.properties
    ````properties
    # connect-distributed.properties
    config.providers=aes256
    config.providers.aes256.class=com.michelin.kafka.AES256ConfigProvider
    config.providers.aes256.param.key=0000111122223333
    ````
3. Provide an API to your users so that they can encode their passwords with your key  
    https://github.com/twobeeb/aes-256-vault-api 
5. Keep your key safe !

## Connector config / User side

Encode your password using API
````console
curl -X POST http://admin-api/vault -d '{"password": "mypassword"}'
> mfw43l96122yZiDhu2RevQ==
````

Deploy this
````json
{
  "name": "jdbc_source_mysql_01",
  "config": {
    "connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
    "connection.url": "jdbc:mysql://mysql:3306/test",
    "connection.user": "connect_user",
    "connection.password": "${aes256:mfw43l96122yZiDhu2RevQ==}",
    "topic.prefix": "mysql-01-",
    "poll.interval.ms" : 3600000,
    "table.whitelist" : "test.accounts",
    "mode":"bulk"
  }
}
````

Done.
