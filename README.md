# Spark for Java: Resilient Distributed DataSet (RDD)

## 1. Introduction

**Resilient Distributed Dataset (RDD)** is the fundamental data structure in Apache Spark

It represents an **immutable distributed collection** of objects that can be processed in parallel across a cluster. Let's break down the key aspects of RDDs in Spark:

**Resilient**: RDDs are resilient because they can automatically recover from failures

Spark keeps track of the lineage of each RDD (i.e., the sequence of transformations that were applied to create it), which allows it to reconstruct lost data partitions due to failures

**Distributed**: RDDs are distributed across multiple nodes in a cluster. Spark automatically distributes the data and computations across the nodes, enabling parallel processing

**Dataset**: RDDs are collections of objects. These objects could be anything - integers, strings, custom objects, etc

RDDs can be created from various data sources like HDFS, S3, local file systems, databases, etc

**Creating RDDs**:

```java
// Creating an RDD from a list
List<Integer> data = Arrays.asList(1, 2, 3, 4, 5);
JavaRDD<Integer> rdd = sc.parallelize(data);
```

**Transforming RDDs**:

```java
// Applying a transformation (map) to double each element of the RDD
JavaRDD<Integer> doubledRDD = rdd.map(x -> x * 2);
```

**Actions on RDDs**:

```java
// Performing an action (collect) to retrieve all elements of the RDD
List<Integer> doubledData = doubledRDD.collect();
```

**Caching RDDs**:
```java
// Caching an RDD in memory for faster access
doubledRDD.cache();
```

**Repartitioning RDDs**:

```java
// Repartitioning an RDD into a specified number of partitions
JavaRDD<Integer> repartitionedRDD = doubledRDD.repartition(4);
```

**Persisting RDDs**:

```java
// Persisting an RDD in memory only
doubledRDD.persist(StorageLevel.MEMORY_ONLY());
```

**Reading from External Data Sources**:

```java
// Creating an RDD by reading from an external data source (e.g., a text file)
JavaRDD<String> lines = sc.textFile("hdfs://path/to/your/file.txt");
```

RDDs in Spark are resilient, distributed, and dataset-like structures that enable parallel processing and fault tolerance

They serve as the building blocks for all Spark operations, providing a high-level abstraction for distributed data manipulation

## 2. 

