# kafka-ecommerce-alura
E-commerce application developed while studying Kafka (Alura)

## Installing Kafka (WSL)

### Update packages
```sh
sudo apt update
sudo apt upgrade
```

### Install Java
```
sudo apt install openjdk-11-jdk
```

### Install Kafka
```
wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz
tar -xzvf kafka_2.13-3.4.0.tgz
cd kafka_2.13-3.4.0/
```

### Configure proxy (WSL only!)

#### Retrieve IP -> 172.x.y.z
```sh
ip addr | grep "eth0" | grep -oEi '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' | head -1
```

#### Configure listeners

Note: Replace the retrieved IP above

```sh
vi config/server.properties
```

Add the following line:

> advertised.listeners=PLAINTEXT://172.x.y.z:9092

#### Create proxy in Windows

In an administrator terminal

```shell
netsh interface portproxy add v4tov4 listenport=9092 listenaddress=0.0.0.0 connectport=9092 connectaddress=172.x.y.z
```

### Configure Kafka and Zookeeper directories (optional)

By default, all Kafka runtime data is saved in the system's temporary directory (e.g., `/tmp/`) and can be lost at any time. To resolve this, we can create and configure a new directory within the Kafka folder itself.

First, create the new directories:

```sh
mkdir -p data/kafka data/zookeeper
```

Open the Kafka configuration file and edit the value of `log.dirs` to point to `<...>/data/kafka`:

```sh
vi config/server.properties
```

Open the Zookeeper configuration file and edit the value of `dataDir` to point to `<...>/data/zookeeper`:

```sh
vi config/zookeeper.properties
```

## Running

First, start Zookeeper

```sh
bin/zookeeper-server-start.sh config/zookeeper.properties
```
Then, **in another terminal**:

```sh
bin/kafka-server-start.sh config/server.properties
```

## Quickstart

### Creating a topic
```sh
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic LOJA_NOVO_PEDIDO
```

### Listing topics
```sh
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

### Describing topics
```sh
bin/kafka-topics.sh --describe --bootstrap-server localhost:9092
```

### Creating a console producer
Note: Each input line enqueues a new message
```sh
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic LOJA_NOVO_PEDIDO
```

### Creating a console consumer
```sh
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic LOJA_NOVO_PEDIDO # --from-beginning
```

### Describing consumer groups
```sh
bin/kafka-consumer-groups.sh --all-groups --bootstrap-server localhost:9092 --describe
```

## Useful Commands

### Changing the number of partitions for a topic

```sh
bin/kafka-topics.sh --alter --bootstrap-server localhost:9092 --topic ECOMMERCE_NEW_ORDER --partitions 3
```