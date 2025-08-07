#  Stock Market Kafka Real-Time Data Engineering Project

##  Introduction

This project demonstrates an **end-to-end data engineering pipeline** for streaming **real-time stock market data** using **Apache Kafka** and **Amazon Web Services (AWS)**. The goal is to implement a scalable, production-ready architecture capable of ingesting, storing, and querying live stock data.

Instead of focusing on analytics, this project emphasizes the **operational and architectural aspects** of data engineering such as stream ingestion, cloud storage, schema discovery, and query execution on raw/structured data.

---

##  Architecture

    A[Python Producer] --> B[Apache Kafka Topic]
    B --> C[Kafka Consumer]
    C --> D[Amazon S3 Bucket (Raw JSON)]
    D --> E[Glue Crawler]
    E --> F[Glue Data Catalog]
    F --> G[Athena Query Engine]
````

---

##  Technologies Used

| Technology/Tool      | Purpose                                        |
| -------------------- | ---------------------------------------------- |
| **Python**           | Producer and Consumer scripts                  |
| **Apache Kafka**     | Real-time data ingestion                       |
| **Amazon S3**        | Cloud storage for raw data                     |
| **AWS Glue Crawler** | Automatically crawls S3 and updates catalog    |
| **AWS Glue Catalog** | Maintains schema metadata                      |
| **AWS Athena**       | Run SQL queries on data stored in S3           |
| **AWS EC2**          | Kafka broker deployment and processing scripts |

---

##  Dataset

You may use **any stock market dataset** (e.g., from Kaggle or synthetic data). This project focuses more on the **data pipeline** architecture rather than the dataset itself.

In our example, each record is pushed into Kafka as a **JSON object** representing one snapshot of a stock (e.g., timestamp, symbol, price, volume, etc.).

---

##  Pipeline Workflow

1. Producer (Python):

    Fetches stock data (live or simulated).
    Sends data as JSON to a Kafka topic.

2. Kafka Topic:

    Acts as a buffer for incoming real-time data.

3. Consumer (Python):

    Reads messages from the Kafka topic.
    Stores them as `.json` files in a specific S3 bucket.

4. AWS Glue Crawler:

    Crawls the S3 bucket.
    Detects schema and updates Glue Catalog.

5. AWS Glue Catalog:

    Stores metadata about the S3 data.
    Makes data queryable.

6. AWS Athena:

   Enables SQL querying on the S3 files via the catalog.

---

##  Directory Structure

```
stock-market-kafka-project/
│
├── producer.py               # Kafka producer for stock data
├── consumer.py               # Kafka consumer that writes to S3
├── utils/
│   └── stock_data_fetcher.py # Optional: helper to fetch data from APIs
├── README.md                 # Project overview and instructions
```

---

##  Setup Instructions

> Ensure Python 3.8+, Kafka, and AWS CLI are installed.

###  Prerequisites

* Kafka running locally or on AWS EC2
* AWS credentials configured (`~/.aws/credentials`)
* S3 bucket created (`kafka-stock-market-project-tamanna`)
* Appropriate IAM permissions (see error resolution section below)

### 1. Start Kafka Broker

```bash
# Start Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Start Kafka
bin/kafka-server-start.sh config/server.properties
```

### 2. Run Producer

```bash
python producer.py
```

### 3. Run Consumer (Writing to S3)

```bash
python consumer.py
```

### 4. AWS Glue & Athena

* Create Glue Crawler to crawl the S3 bucket.
* Use Glue Catalog to define schema.
* Query data via Athena using SQL.

---

##  Common AWS Permission Error

If you see:

```
An error occurred (AccessDenied) when calling the PutObject operation
```

Add the following permission to your IAM user or role:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:PutObject"
  ],
  "Resource": "arn:aws:s3:::kafka-stock-market-project-tamanna/*"
}
```

---

##  Sample Athena Query

```sql
SELECT symbol, AVG(price) AS avg_price
FROM "stock_data_db"."stock_table"
WHERE time > current_date - interval '7' day
GROUP BY symbol;
```

---
