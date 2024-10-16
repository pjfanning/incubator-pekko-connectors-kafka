---
project.description: Consider these areas when using Apache Pekko Connectors Kafka in production.
---
# Production considerations


## Apache Pekko Connectors Kafka API

1. Do not use `Consumer.atMostOnceSource` in production as it internally commits the offset after every element.
1. If you create `Producer` sinks in "inner flows", be sure to @ref:[share the `Producer` instance](producer.md#sharing-the-kafkaproducer-instance). This avoids the expensive creation of `KafkaProducer` instances.

@@@ note

This is just a start, please add your experiences to this list by [opening a Pull Request](https://github.com/apache/incubator-pekko-connectors-kafka/pulls).

@@@


## Security setup

The different security setups offered by Kafka brokers are described in the @extref[Apache Kafka documentation](kafka:/documentation.html#security).


### SSL

The properties described in Kafka's @extref[Configuring Kafka Clients for SSL](kafka:/documentation.html#security_configclients) go in the
`pekko.kafka.consumer.kafka-clients` and `pekko.kafka.producer.kafka-clients` sections of the configuration, or can be added programmatically via
`ProducerSettings.withProperties` and `ConsumerSettings.withProperties`. The necessary property name constants are available in @javadoc[SslConfigs](org.apache.kafka.common.config.SslConfigs).

```hocon
pekko.kafka.producer { # and pekko.kafka.consumer respectively
  kafka-clients {
    security.protocol=SSL
    ssl.truststore.location=/var/private/ssl/kafka.client.truststore.jks
    ssl.truststore.password=test1234
    ssl.keystore.location=/var/private/ssl/kafka.client.keystore.jks
    ssl.keystore.password=test1234
    ssl.key.password=test1234
  }
}
```

The truststore and keystore locations may specify URLs, absolute paths or relative paths (starting with `./`).

You have the option to pass the passwords as command line parameters or environment values via the support in [Config](https://github.com/lightbend/config#optional-system-or-env-variable-overrides).


### Kerberos

The properties described in Kafka's @extref[Configuring Kafka Clients for Kerberos](kafka:/documentation.html#security_sasl_kerberos_clientconfig) go in the
`pekko.kafka.consumer.kafka-clients` and `pekko.kafka.producer.kafka-clients` sections of the configuration, or can be added programmatically via
`ProducerSettings.withProperties` and `ConsumerSettings.withProperties`.

```hocon
pekko.kafka.producer { # and pekko.kafka.consumer respectively
  kafka-clients {
    security.protocol=SASL_PLAINTEXT # (or SASL_SSL)
    sasl.mechanism=GSSAPI
    sasl.kerberos.service.name=kafka
  }
}
```
