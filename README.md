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

## 2. More advance samples 

These examples in Java demonstrate various RDD operations, including joins, aggregations, filtering, custom partitioning, and working with external data sources, 

highlighting the flexibility and power of Spark's RDDs for distributed data processing

**Example 1: Joining Two RDDs in Java**

Join operations combine two datasets based on a common key

```java
JavaPairRDD<String, Integer> rdd1 = sc.parallelizePairs(Arrays.asList(
    new Tuple2<>("apple", 2),
    new Tuple2<>("banana", 1),
    new Tuple2<>("orange", 3)
));

JavaPairRDD<String, String> rdd2 = sc.parallelizePairs(Arrays.asList(
    new Tuple2<>("apple", "green"),
    new Tuple2<>("banana", "yellow"),
    new Tuple2<>("grape", "purple")
));

// Perform an inner join on rdd1 and rdd2
JavaPairRDD<String, Tuple2<Integer, String>> joinedRDD = rdd1.join(rdd2);

// Collect and print the result
joinedRDD.collect().forEach(System.out::println);
```

**Example 2: Aggregating Data in Java**

Aggregating data can summarize information in a distributed dataset

```java
JavaPairRDD<String, Integer> data = sc.parallelizePairs(Arrays.asList(
    new Tuple2<>("math", 55),
    new Tuple2<>("math", 56),
    new Tuple2<>("english", 57),
    new Tuple2<>("english", 58)
));

// Using reduceByKey to calculate total marks per subject
JavaPairRDD<String, Integer> totalMarks = data.reduceByKey(Integer::sum);

totalMarks.collect().forEach(System.out::println);
```

**Example 3: Advanced Filtering and Map-Reduce in Java**

Demonstrates a pipeline involving filtering, mapping, and reducing

```java
JavaRDD<String> logData = sc.textFile("hdfs://path/to/log.txt");

// Filtering error logs
JavaRDD<String> errorLogs = logData.filter(line -> line.contains("ERROR"));

// Extracting error messages and counting occurrences
JavaPairRDD<String, Integer> errorCount = errorLogs.mapToPair(line -> new Tuple2<>(line.split("\t")[2], 1))
                                                    .reduceByKey(Integer::sum);

errorCount.collect().forEach(System.out::println);
```

**Example 4: Custom Partitioning in Java**

Custom partitioning can optimize data distribution across the cluster

```java
class DomainNamePartitioner extends Partitioner {
    private final int numParts;

    public DomainNamePartitioner(int numParts) {
        this.numParts = numParts;
    }

    @Override
    public int numPartitions() {
        return numParts;
    }

    @Override
    public int getPartition(Object key) {
        String domain = key.toString().split("@")[1];
        return Math.abs(domain.hashCode()) % numParts;
    }
}

// Assuming emailRDD is a JavaRDD<String> of email addresses
JavaPairRDD<String, String> pairRDD = emailRDD.mapToPair(email -> new Tuple2<>(email, email));
JavaPairRDD<String, String> partitionedRDD = pairRDD.partitionBy(new DomainNamePartitioner(3));
```

**Example 5: Working with External Datasets in Java**

Integrating RDDs with external data sources like databases or data files

```java
// Reading a large dataset from HDFS
JavaRDD<String> userData = sc.textFile("hdfs://path/to/user/data.txt");

// Processing user data
JavaRDD<Tuple2<String, Integer>> processedData = userData.map(line -> {
    String[] fields = line.split(",");
    // Example processing: parse fields and apply some operations
    return new Tuple2<>(fields[0], Integer.parseInt(fields[1]) * 2);
});

processedData.saveAsTextFile("hdfs://path/to/output/");
```


