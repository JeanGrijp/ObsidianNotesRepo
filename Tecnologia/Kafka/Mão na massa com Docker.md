Prática, prática, prática!

#### Ferramentas:

- Docker + Docker Compose
    
- Kafka CLI
    
- Kafka UI (como o [Kowl](https://github.com/redpanda-data/kowl) ou [Kafka UI da Provectus](https://github.com/provectus/kafka-ui))
    

🚀 Tarefa:

- Subir um ambiente Kafka com Docker Compose
    
- Criar tópicos, produzir e consumir mensagens via terminal

version: '3.7'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.0.1
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.0.1
    depends_on:
      - zookeeper
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
