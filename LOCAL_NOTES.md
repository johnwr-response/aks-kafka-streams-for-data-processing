# Log
### Section 1: Kafka Streams - First Look
- Running your first Kafka Streams Application: WordCount
    - Downloading and extracting Kafka binaries
        - i.e: `C:\usr\local\kafka_2.13-2.7.0>`
    - Change directory into the kafka folder
    - Start Zookeeper:
      ```
      bin\windows\zookeeper-server-start.bat config\zookeeper.properties
      ```
    - Start Kafka:
      ```
      bin\windows\zookeeper-server-start.bat config\zookeeper.properties
      ```
    - Create topics:
      ```
      bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic streams-plaintext-input
      bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic streams-wordcount-output
      ```
    - Start a producer and add some content:
      ```
      bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic streams-plaintext-input
      
      kafka streams udemy
      kafka data processing
      kafka streams course
      ```
    - Verify the content:
      ```
      bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic streams-plaintext-input --from-beginning
      ```
    - Start a consumer on the output topic:
      ```
      bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic streams-wordcount-output --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property print.value=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
      ```
    - Start the Streams application and verify the data:
      ```
      bin\windows\kafka-run-class.bat org.apache.kafka.streams.examples.wordcount.WordCountDemo
      ```
- Kafka Streams vs other stream processing libraries (Spark Streaming, NiFI, Flink
  - [What are the differences and similarities between Kafka and Spark Streaming?](https://www.quora.com/What-are-the-differences-and-similarities-between-Kafka-and-Spark-Streaming)
  - `Micro batch` vs `per data streaming`
  - `Cluster required` vs `no cluster required`
  - `At least once` vs `Exactly Once` semantics
  - `Kafka Streams` scales easily by just adding java processes (no re-configuration required)
  - All code-based

### Section 2: Code Download
- [Code download](https://courses.datacumulus.com/downloads/kafka-streams-sn2) 

### Section 3: End to End Kafka Streams Application - Word Count
- Section Objective
  - Fundamentals of Kafka Streams
  - IDE Setup
  - Basic Kafka Streams configurations
  - Kafka Streams App internal Topics 
  - Run Kafka Streams applications in development environment and package them as jars
  - Scale Kafka Streams applications
- Kafka Streams Core Concepts
  - A **Stream** is a `sequence` of immutable `data` records, that fully `ordered`, can be `replayed`, and is `fault tolerant`
  - A **Stream Processor** is a `node` in the processor topology. It `transforms` incoming **streams**, `record by record`, and may `create a new` **stream** from it.
  - A **Topology** is a graph of **processors** `chained together` by **streams**
  - A **Source processor** is a `special` **processor** that `takes` the streamed data directly `from` a **Kafka Topic**. It has `no predecessors` in a **Kafka Topology** and `does not transform` the data.
  - A **Sink processor** is a `special` **processor** that `sends` the streamed data directly `to` a **Kafka Topic**. It has `no children` in a **Kafka Topology** and `does not transform` the data.
  - `High Level DSL`
    - Used most of the time
    - Simple and descriptive
    - Has all the operations needed to perform most transformation tasks
    - Contains a lot of syntax helpers
  - `Low Level Processor API`
    - Rarely needed
    - Imperative API
    - Used to implement the most complex logic
- Environment and IDE Setup: Java 8, Maven, IntelliJ IDEA
- Starter Project Setup
- Kafka Streams Application Properties
  - A stream application is using the `Consumer` and the `Producer` APIs when communication with Kafka. 
  - Configurations:
    - `bootstrap.servers` used to communicate with Kafka (Usually port 9092)
    - `auto.offset.reset.config` set to `earliest` to consume the topic from start
    - `application.id` is specific for Streams applications, will be used for:
      - Consumer `group.id` = `application.id` (important!)
      - Default `client.id` prefix
      - Prefix to internal changelog topics
    - `default.[key|value].serde` (for serialization and Deserialization of the data)
- Java 8 Lambda Functions - quick overview
  - [Learn more about Java 8 Expressions](https://www.tutorialspoint.com/java8/java8_lambda_expressions.htm)
  - Example: This anonymous lambda function will filter away all streams with value not greater than 0
    ```
    stream.filter((key, value) -> value > 0)
    ```
- Word Count Application Topology
  1. `Stream` from Kafka
  1. `MapValues` lowercase
  1. `FlatMapValues` split by space
  1. `SelectKey` to apply a key
  1. `GroupByKey` before aggregation
  1. `Count` occurrences in each group
  1. `To` in order to write the results back to Kafka
- Printing the Kafka Streams Topology
- Kafka Streams Graceful Shutdown
- Running Application from IntelliJ IDEA
  - Create topic
    ```
    bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 2 --topic word-count-input
    bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 2 --topic word-count-output
    ```
  - Start a consumer on the output topic:
    ```
    bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic word-count-output --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property print.value=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
- Debugging Application from IntelliJ IDEA
- Internal Topics for our Kafka Streams Application
- Packaging the application as Fat Jar & Running the Fat Jar
- Scaling our Application
- Section Wrap-Up

### Section 4: KStreams and KTables Simple Operations (Stateless)
- Section Objectives
  - [Duality of Streams and Tables](https://docs.confluent.io/platform/current/streams/concepts.html#duality-of-streams-and-tables "Duality of Streams and Tables in Confluent documentation")
  - [KStream](https://docs.confluent.io/platform/current/streams/concepts.html#kstream "KStream in Confluent documentation")
  - [KTable](https://docs.confluent.io/platform/current/streams/concepts.html#ktable "KTable in Confluent documentation")
- KStream & KTables
  - KStreams (See figure in video at 1:19)
    - All inserts
    - Similar to a log
    - Infinite
    - Unbounded data streams
  - KTables (See figure in video at 2:55)
    - All `upserts` on non null value
    - All `deletes` on null value
    - Similar to row in a database table (when using an id)
    - Strong correlation with `log compacted` topics
- Stateless vs Stateful Operations
  - `Stateless` means that the result of a transformation only depends on the data you process 
  - `Stateful` means that the result of a transformation depends on both the data you process and some external information (the state)
- MapValues / Map
  - Takes one record and produces one record
    - `MapValues` 
      - is only affecting values
      - does not change keys
      - does not trigger a re-partition
      - for KStreams and KTables
      - Java 8 example:
        ```
        KStream<byte[], String> uppercased = stream.mapValues(value -> value.toUpperCase());
        ```
    - `Map` 
      - affects both keys and values
      - does trigger a re-partition
      - for KStreams only
- Filter / FilterNot
  - Takes one record and produces zero or one record
    - `Filter`
      - does not change keys or values
      - does not trigger a re-partition
      - for KStreams and KTables
    - `FilterNot`
      - inverse of filter
      - Java 8 example:
        ```
        KStream<String, Long> onlyPositives = stream.filter((key,value) -> value > 0);
        ```
- FlatMapValues / FlatMap
  - Takes one record and produces zero, one or more records
    - `FlatMapValues`
      - does not change keys
      - does not trigger a re-partition
      - for KStreams only
    - `FlatMap`
      - does change keys
      - does trigger a re-partition
      - for KStreams only
      - Java 8 example:
        ```
        words = sentences.FlatMapValues(value -> Arrays.asList(value.split("\\s+")));
        ```
- Branch
  - Branch (split) a KStream based on one or more predicates
  - Predicates are evaluated in order, if no matches, records are dropped
  - The result will be multiple KStreams
    - Java 8 example:
      ```
      KStream<String, Long>[] branches = stream.branch(
        (key, value) -> value > 100,
        (key, value) -> value > 10,
        (key, value) -> value > 0,
      );
      ```
- SelectKey
  - Assigns a new key to the record
  - does trigger a re-partition
  - Best practice is to isolate that transformation (to know exactly where the re-partitioning happens) 
    - Java 8 example:
      ```
      rekeyed = stream.selectKey((key, value) -> key.substring(0,1));
      ```
- Reading from Kafka
  - A Topic can be read as a KStream, a KTable or a GlobalKTable
    - Java 8 examples:
      ```
      KStream<String,Long> wordCounts = builder.stream(
             Serdes.String(), /* key serde */
             Serdes.Long(), /* value serde */
             "word-count-input-topic" /* input topic */
      );
      ```
      ```
      KTable<String,Long> wordCounts = builder.table(
             Serdes.String(), /* key serde */
             Serdes.Long(), /* value serde */
             "word-count-input-topic" /* input topic */
      );
      ```
      ```
      GlobalKTable<String,Long> wordCounts = builder.globalTable(
             Serdes.String(), /* key serde */
             Serdes.Long(), /* value serde */
             "word-count-input-topic" /* input topic */
      );
      ```
- Writing to Kafka
  - Any KStream or KTable can be written back to Kafka
  - For writing KTables back to Kafka, think about creating a log compacted topic
  - To: Terminal operation write the records to a topic
    - Java 8 examples:
      ```
      stream.to("my-stream-output-topic");
      table.to("my-table-output-topic");
      ```
  - Through: write to a topic and get a stream/table from the topic
    - Java 8 examples:
      ```
      KStream<String,Long> newStream = stream.through("my-stream-output-topic");
      KTable<String,Long> newTable = table.through("my-table-output-topic");
      ```
- Streams Marked for Re-Partition
  - As soon as an operation can possibly change the key, the stream will be marked for re-partition:
    - `Map`
    - `FlatMap`
    - `SelectKey`
  - Only use these APIs if you need to change the key, otherwise use their counterparts:
    - `MapValues`
    - `FlatMapValues`
  - Re-partitioning is done seamlessly behind the scenes but will incur a performance cost (read and write to Kafka)


# Links
 - [Confluent documentation](https://docs.confluent.io/home/overview.html#transform-a-stream)
 - [Kafka Streams Overview](https://docs.confluent.io/platform/current/streams/index.html)
 - [Kafka 101](https://docs.confluent.io/platform/current/streams/concepts.html#ak-101)

# Github setup
```
git remote add origin https://github.com/johnwr-response/aks-kafka-streams-for-data-processing.git
```
