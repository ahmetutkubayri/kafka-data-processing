# Solutions to Kafka Tasks

This file contains solutions to the given Kafka-related tasks.

## Q-1: Produce Turkey's Geographical Regions to `regions` Topic

### Producer (producer.py)
```python
from confluent_kafka import Producer
import time

producer_config = {
    'bootstrap.servers': 'localhost:9092'
}
producer = Producer(producer_config)

regions = {
    1: "Marmara",
    2: "Ege",
    3: "Akdeniz",
    4: "İç Anadolu",
    5: "Karadeniz",
    6: "Doğu Anadolu",
    7: "Güneydoğu Anadolu"
}

def delivery_report(err, msg):
    if err is not None:
        print(f"Message delivery failed: {err}")
    else:
        print(f"Message delivered to {msg.topic()} [{msg.partition()}]")

for key, value in regions.items():
    producer.produce('regions', key=str(key), value=value, callback=delivery_report)
    time.sleep(0.5)

producer.flush()
```

### Consumer (consumer.py)
```python
from confluent_kafka import Consumer, KafkaException
import time

consumer_config = {
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'region-consumer-group',
    'auto.offset.reset': 'earliest'
}
consumer = Consumer(consumer_config)

consumer.subscribe(['regions'])

try:
    while True:
        msg = consumer.poll(timeout=1.0)
        if msg is None:
            continue
        if msg.error():
            raise KafkaException(msg.error())

        key = msg.key().decode('utf-8')
        value = msg.value().decode('utf-8')
        print(f"Key: {key}, Value: {value}, Partition: {msg.partition()}, TS: {msg.timestamp()[1]}")
        time.sleep(0.5)
except KeyboardInterrupt:
    print("Consumer stopped.")
finally:
    consumer.close()
```

## Q-2: Process `iris.csv` and Categorize Messages

### Consumer (iris_consumer.py)
```python
from confluent_kafka import Consumer, KafkaException
import os

consumer_config = {
    'bootstrap.servers': 'localhost:9092',
    'group.id': 'iris-consumer-group',
    'auto.offset.reset': 'earliest'
}

consumer = Consumer(consumer_config)
consumer.subscribe(['topic1'])

output_dir = '/tmp/kafka_out/'
species_files = {
    'Iris-setosa': 'setosa_out.txt',
    'Iris-versicolor': 'versicolor_out.txt',
    'Iris-virginica': 'virginica_out.txt'
}
other_file = 'other_out.txt'

if not os.path.exists(output_dir):
    os.makedirs(output_dir)

species_writers = {species: open(os.path.join(output_dir, fname), 'a') for species, fname in species_files.items()}
other_writer = open(os.path.join(output_dir, other_file), 'a')

try:
    while True:
        msg = consumer.poll(timeout=1.0)
        if msg is None:
            continue
        if msg.error():
            raise KafkaException(msg.error())

        value = msg.value().decode('utf-8')
        parts = value.split(',')
        species = parts[-1].strip()
        output_line = f"{msg.topic()}|{msg.partition()}|{msg.offset()}|{msg.timestamp()[1]}|{value}
"
        
        if species in species_writers:
            species_writers[species].write(output_line)
        else:
            other_writer.write(output_line)
except KeyboardInterrupt:
    print("Consumer stopped.")
finally:
    consumer.close()

for writer in species_writers.values():
    writer.close()
other_writer.close()
```

## Q-3: Create a Kafka Topic If Not Exists

### Admin (admin.py)
```python
from confluent_kafka.admin import (AdminClient, NewTopic)

config = {
    'bootstrap.servers': 'localhost:9092'
}

def topic_exists(admin, topic):
    metadata = admin.list_topics(timeout=10)
    for t in iter(metadata.topics.values()):
        if t.topic == topic:
            return True
    return False

def create_a_new_topic_if_not_exists(admin, topic="example-topic", num_partitions=1, replication_factor=1):
    if topic_exists(admin, topic):
        print(f"Topic '{topic}' already exists. No action taken.")
        return

    new_topic = NewTopic(topic, num_partitions=num_partitions, replication_factor=replication_factor)
    result_dict = admin.create_topics([new_topic])
    for topic, future in result_dict.items():
        try:
            future.result()
            print(f"Topic '{topic}' created successfully.")
        except Exception as e:
            print(f"Failed to create topic '{topic}': {e}")

if __name__ == '__main__':
    admin = AdminClient(config)
    topic = 'example-topic'
    create_a_new_topic_if_not_exists(admin, topic)
```

