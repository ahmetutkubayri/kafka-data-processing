# Kafka Setup Guide

This document provides a step-by-step guide to setting up Kafka for the given tasks.

## Step 1: Install and Start Kafka
Ensure Kafka is installed and running on your system.

- Start Zookeeper (if required):
  ```bash
  zookeeper-server-start.sh config/zookeeper.properties
  ```

- Start Kafka broker:
  ```bash
  kafka-server-start.sh config/server.properties
  ```

- Install `confluent_kafka` Python library:
  ```bash
  pip install confluent_kafka
  ```

- Verify Kafka is running:
  ```bash
  kafka-topics.sh --list --bootstrap-server localhost:9092
  ```

## Step 2: Kafka Consumer Groups
For tasks that require consumer groups, use multiple terminal sessions to run multiple consumers.

