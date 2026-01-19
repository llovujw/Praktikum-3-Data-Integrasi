# Praktikum-3-Data-Integrasi

**Nama:** Intan Virginia Aulia Putri  
**Modul:** 03 – Data Integrasi  
**Kasus Studi:** Data Ingestion menggunakan Sqoop, Flume, dan Kafka  
**Tools:** Apache Sqoop, Apache Flume, Apache Kafka  

---

## Persiapan Lingkungan

- Sistem Operasi Linux / VM Hadoop  
- Hadoop (HDFS) sudah berjalan  
- Java JDK 8+  
- MySQL Server  
- Apache Sqoop, Apache Flume, Apache Kafka  
- Folder kerja `/data-integrasi` (opsional)  

---

## Sesi 1 — Apache Sqoop (Integrasi Database ke HDFS)

---

### Langkah Praktikum

#### Masuk ke MySQL:

mysql -u root -p

#### Buat database dan tabel:

CREATE DATABASE company;
USE company;

CREATE TABLE employees (
  id INT,
  name VARCHAR(50)
);

INSERT INTO employees VALUES
(1, 'Andi'),
(2, 'Budi'),
(3, 'Citra');

#### Menjalankan Sqoop Import

./bin/sqoop import \
--connect jdbc:mysql://localhost/company \
--username root \
--password password_anda \
--table employees \
--target-dir /user/hadoop/employees \
-m 1

#### Verifikasi Data di HDFS
hdfs dfs -ls /user/hadoop/employees
hdfs dfs -cat /user/hadoop/employees/part-m-00000


- Hasil:

1,Andi
2,Budi
3,Citra

## Sesi 2 — Apache Flume (Ingestion Data Log)

---

### Langkah Praktikum

Konfigurasi Flume Agent

File: netcat-logger.conf

- Agent components
a1.sources = r1
a1.sinks = k1
a1.channels = c1

- Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

- Sink
a1.sinks.k1.type = logger

- Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

- Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

- Menjalankan Flume

./bin/flume-ng agent \
--conf conf \
--conf-file conf/netcat-logger.conf \
--name a1 \
-Dflume.root.logger=INFO,console

- Mengirim data

telnet localhost 44444

- Ketik:

Hello Flume
Ini data log pertama

## Sesi 3 — Apache Kafka (Streaming Data Real-Time)

---

### Langkah Praktikum

- 1️⃣ Menjalankan Zookeeper & Kafka

./bin/zookeeper-server-start.sh config/zookeeper.properties
./bin/kafka-server-start.sh config/server.properties

- 2️⃣ Membuat Topic
./bin/kafka-topics.sh --create \
--topic uji-praktikum \
--bootstrap-server localhost:9092 \
--partitions 1 \
--replication-factor 1

- 3️⃣ Menjalankan Producer
./bin/kafka-console-producer.sh \
--topic uji-praktikum \
--bootstrap-server localhost:9092

Pesan:

Pesan pertama untuk Kafka
Ini adalah tes sistem pesan
Praktikum berhasil

- 4️⃣ Menjalankan Consumer
./bin/kafka-console-consumer.sh \
--topic uji-praktikum \
--from-beginning \
--bootstrap-server localhost:9092
