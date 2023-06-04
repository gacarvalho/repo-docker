Operações básicas com KAFKA + DOCKER COMPOSE

---


1. Em uma pasta separada, crie o arquivo ``` docker-compose.yaml``` e como conteúdo você poderá colar o seguinte conteúdo:

```
---
version: '3'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.3.2
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000

  broker:
    image: confluentinc/cp-kafka:7.3.2
    container_name: broker
    ports:
      - "9092:9092"
    depends_on:
      - zookeeper
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper:2181'
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_INTERNAL:PLAINTEXT
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://broker:9092,PLAINTEXT_INTERNAL://broker:29092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
```

2. Finalizando a etapa 1, você poderá subir a imagem com o comando ``` docker-compose up -d ```
3. Espere um momento até o docker subir as imagens.
4. Com a etapa 2 e 3 finalizada, vamos entrar no bash do container pelo o comando ```docker-compose exec broker bash```
5. Agora já estamos com o nosso container no ar e a partir deste momento podemos consultar os tópicos pelo comando: 
``` kafka-topics --bootstrap-server broker:9092 --list ```
6. Agora, vamos criar o nosso tópico com o comando: 
``` ka-topics --bootstrap-server=broker:9092 --topic=teste --create --partitions=3 --replication-factor=1```
7. Com o nosso tópico criado, vamos analisar o nosso tópico com o comando: 
```ka-topics --bootstrap-server=broker:9092 --topic=test --describe```

Você pode perceber que estavamos utilizando o kafka topics, agora vamos utilizar o **kafka consumer**!

1. Para analisar o tópico (apenas os eventos recebidos - novos) você pode executar o comando ```ka-console-consumer --bootstrap-server broker:9092 --topic teste``` e para analisar os eventos (historico), você poderá executar o comando ```ka-console-consumer --bootstrap-server broker:9092 --topic teste --from-beginning```

---

Para executar o PRODUCER do tópico=teste: ```ka-console-producer --bootstrap-server=broker:9092 --topic=test```

Para executar o CONSUMER do tópico=teste (com historico): ```ka-console-consumer --bootstrap-server broker:9092 --topic teste --from-beginning```

