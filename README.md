# Kafka Producer & Consumer Tasks with Python

This project demonstrates how to use Python with Kafka for producing and consuming messages, managing topics, and handling real-time data processing.

## 🚀 Features
- Produce and consume messages using Python (`confluent_kafka` library)
- Kafka topic creation, deletion, and metadata retrieval
- Group-based message consumption with filtering
- Data processing from `iris.csv` and `regions` dataset

## 📁 Repository Structure
- `producer.py` → Kafka producer for Turkey's geographical regions
- `consumer.py` → Kafka consumer for reading region messages
- `iris_consumer.py` → Consumer for processing `iris.csv`
- `admin.py` → Kafka topic management script
- `setup.md` → Step-by-step guide for setting up Kafka & Python integration
- `solutions.md` → Solutions to the given tasks

## 🔧 Prerequisites
- Apache Kafka installed and running
- Python 3 installed with `confluent_kafka` library
- Zookeeper running (if not using KRaft mode)
- Kafka bin directory added to `PATH`

## 🏗️ Setting up Kafka
1. Start Zookeeper (if required):
   ```bash
   zookeeper-server-start.sh config/zookeeper.properties
   ```

2. Start Kafka broker:
   ```bash
   kafka-server-start.sh config/server.properties
   ```

3. Install dependencies:
   ```bash
   pip install confluent_kafka
   ```

4. Verify Kafka is running:
   ```bash
   kafka-topics.sh --list --bootstrap-server localhost:9092
   ```

## 📌 Tasks and Solutions
Refer to the [solutions.md](solutions.md) file for the detailed commands and explanations.
