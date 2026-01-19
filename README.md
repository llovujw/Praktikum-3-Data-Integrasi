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

### Dasar Teori

Apache Sqoop adalah tool yang digunakan untuk mentransfer data antara database relasional dan Hadoop secara efisien. Sqoop memanfaatkan MapReduce sehingga proses impor dan ekspor data dapat dilakukan secara paralel.

---

### Skenario Praktikum

Mengimpor data tabel `employees` dari database MySQL ke dalam HDFS.

---

### Langkah Praktikum

#### 1️⃣ Persiapan Database MySQL

Masuk ke MySQL:

```bash
mysql -u root -p

Buat Database dan tabel:

'''bash
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

#### 2️⃣ Menjalankan Sqoop Import

./bin/sqoop import \
--connect jdbc:mysql://localhost/company \
--username root \
--password password_anda \
--table employees \
--target-dir /user/hadoop/employees \
-m 1

#### 3️⃣ Verifikasi Data di HDFS

hdfs dfs -ls /user/hadoop/employees
hdfs dfs -cat /user/hadoop/employees/part-m-00000

Hasil:

1,Andi
2,Budi
3,Citra

#### Analisis

1️⃣ Sqoop mempermudah integrasi data terstruktur dari database ke Hadoop.
2️⃣ Proses berjalan paralel menggunakan MapReduce.
3️⃣ Cocok untuk pemindahan data batch skala besar.

## Sesi 2 — Apache Flume (Ingestion Data Log)

### Konfigurasi Flume Agent

File: netcat-logger.conf

# Agent components
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Sink
a1.sinks.k1.type = logger

# Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1

Menjalankan Flume

./bin/flume-ng agent \
--conf conf \
--conf-file conf/netcat-logger.conf \
--name a1 \
-Dflume.root.logger=INFO,console

Mengirim data

telnet localhost 44444

Hello Flume
Ini data log pertama

## Sesi 3 — Apache Kafka (Streaming Data Real-Time)

