# Chapter 11 - Design a autocomplete/typeahead service (Summary)

[Mindmap for designing a autocomplete/typeahead service](https://github.com/vidyabhandary/SystemDesign/blob/44122103358a6b182690172060b564e1cc07f69a/imgs/DesignAutocomplete.svg)

### **System Design Architecture for Autocomplete**

#### **1. High-Level Architecture**

The autocomplete system is composed of three major components:

1. **Data Ingestion System**: Handles the logging and capture of user inputs.
2. **Data Processing Pipeline**: Converts raw data into a structured format, such as a weighted trie.
3. **Query System**: Interfaces with the user, processes input, and retrieves suggestions.

---

#### **2. Component Details**

##### **2.1. Data Ingestion**

- **Purpose**: Collect and store user queries at high scale.
- **Implementation Options**:
  - **Shared Logging Service**: Handles logs for multiple services, not just autocomplete.
    - Examples: Kafka, Elasticsearch, or Logstash.
  - **Data Storage**: Retain raw logs in HDFS or similar distributed systems.
- **Scalability Features**:
  - Partitioning: Logs partitioned by topic (e.g., search) and timestamp.
  - Horizontal Scaling: Allows handling spikes in user input.

**Key Design Pattern**:

- **Command Query Responsibility Segregation (CQRS)**: Separate the ingestion (write-heavy) system from the query (read-heavy) system.

#DataIngestion #CQRS #Scalability

---

##### **2.2. Data Processing**

- **Goal**: Transform raw logs into structured, meaningful data for autocomplete.
- **Key Stages**:
  1. **Log Fetching**: Retrieve logs from sources like Kafka or Elasticsearch.
  2. **Preprocessing**:
     - Split search strings into words.
     - Filter inappropriate content using blacklists.
     - Normalize data (e.g., convert to lowercase).
  3. **Word Count**:
     - Aggregate word frequencies using MapReduce or Count-Min Sketch algorithms.
  4. **Weighted Trie Generation**:
     - Use word counts to assign weights to trie nodes.
     - Serialize the trie into JSON for storage and querying.

**Design Pattern**:

- **Batch ETL (Extract, Transform, Load)**:
  - Independent pipeline stages ensure modularity and easier debugging.
  - Example Frameworks: Apache Spark, Hive.

**Optimization**:

- Sampling: Process only a subset of data for lower resource usage.
- Rollup: Aggregate logs by hour, day, or month to reduce storage needs.

#DataProcessing #BatchETL #WeightedTrie

---

##### **2.3. Query System**

- **Purpose**: Serve user requests by providing autocomplete suggestions in real time.
- **Key Operations**:
  1. Receive partial input from the user (via a frontend interface).
  2. Match input against the weighted trie.
  3. Retrieve the top suggestions based on weight and relevance.
- **Performance Requirements**:
  - P99 Latency: ~100ms.
  - Ensure low-latency trie lookups using in-memory storage.

**Deployment**:

- Host trie in memory on backend servers (or distribute via CDNs for faster access).
- Update trie periodically (e.g., daily) to reflect recent trends.

#QuerySystem #LowLatency #RealTime

---

#### **3. Architectural Patterns**

##### **3.1. Weighted Trie**

- **Functionality**: Efficiently stores strings and retrieves suggestions based on input prefixes.
- **Advantages**:
  - Fast lookups for user input.
  - Compact storage due to shared prefixes.
- **Trie Node Structure**:
  ```text
  TrieNode:
    - Children: Links to child nodes.
    - Weight: Frequency or relevance score.
  ```

##### **3.2. Batch Processing Pipeline**

- **Purpose**: Separate processing from querying to reduce complexity and increase scalability.
- **Implementation**:
  - Use tools like Apache Spark or Hadoop for parallel processing.
  - Modularity: Each processing step (e.g., filtering, counting) is implemented as an independent task.

##### **3.3. Content Moderation**

- **Objective**: Prevent inappropriate suggestions.
- **Approaches**:
  - Maintain blacklists/whitelists in a database.
  - Use distributed joins to filter content during the word processing stage.
  - Incorporate machine learning for advanced filtering.

##### **3.4. Sampling**

- **Goal**: Reduce resource consumption without compromising accuracy.
- **Stages for Sampling**:
  1. Raw log entries.
  2. Processed words post-filtering.
  3. Popular words after frequency analysis.

#WeightedTrie #ContentModeration #Sampling

---

#### **4. Advanced Features**

- **Personalization**:
  - Enhance user experience by tailoring suggestions based on user-specific history.
  - Requires integration of user profiles and demographics.
- **Handling Phrases**:
  - Extend trie to store and suggest multi-word phrases.
  - Keep trie size manageable by including only top phrases.
- **Real-Time Updates**:
  - Introduce a Lambda Architecture with separate batch and streaming pipelines for real-time trie updates.

#Personalization #RealTimeUpdates #PhraseHandling

---

### **5. Key Takeaways**

- Separate ingestion, processing, and querying to maintain modularity.
- Leverage batch processing for scalability and maintainability.
- Optimize for low-latency, high-throughput user interactions.
- Use weighted tries for efficient and compact storage.
- Design for future extensions (personalization, phrase handling, real-time updates).

---

References:

1. [Trie](https://www.youtube.com/watch?v=qA8l8TAMyig)
2. [Bloom Filters](https://www.youtube.com/watch?v=heEDL9usFgs)
3. [Bloom Filter Code](https://github.com/vidyabhandary/algorithms/tree/master/src/bloomfilter)
4. [count-min sketch](https://www.youtube.com/watch?v=lGoCslwItiU)
5. [Map Reduce - Filter, Map, Reduce explained in less than 2 minutes](https://www.youtube.com/watch?v=PZvHZJVeYdw)
6. [A/B testing and multi-armed bandit](https://www.youtube.com/watch?v=HPl0ZtXqppM)
