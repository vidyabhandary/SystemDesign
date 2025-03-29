# Chapter 17 - Design a dashboard of top 10 products (Summary)

[Mindmap for designing a dashboard of top 10 products](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/refs/heads/main/imgs/DesignTop10Products.svg)

# Study Notes: Designing a Dashboard for Top Products by Sales Volume

## Introduction to Analytics Dashboards

Analytics plays a crucial role in modern system design. Organizations consistently track network requests and user interactions to gather data for analysis.

The Top K Problem (also known as Heavy Hitters) represents a common dashboard scenario where we identify the most popular (or least popular) items according to specific metrics. #analytics #TopKProblem #HeavyHitters

Common examples of Top K problems include:

- Best/worst-selling products by volume or revenue
- Most/least viewed products on e-commerce platforms
- Most downloaded applications in app stores
- Most popular videos, songs, or posts on media platforms
- Most traded stocks on financial platforms

The insights gained from these dashboards can drive critical business decisions like:

- Product promotion strategies
- Discontinuation of underperforming items
- Resource allocation for marketing

## Requirement Analysis

### Functional Requirements

1. **Ranking mechanism**: Method to break ties between items with equal metrics (any item can be chosen in a tie) #ranking
2. **Time interval flexibility**: System should aggregate data by different time periods:

   - Hourly
   - Daily
   - Weekly
   - Monthly
   - Yearly

3. **Data presentation**: Display both product rankings and actual sales counts
4. **Scope**: Ability to provide at minimum the top 10 products, with flexibility to show more

### Non-Functional Requirements

1. **Accuracy vs Speed tradeoff**:

   - Real-time accurate calculations are resource-intensive
   - Approximate near real-time results are acceptable
   - Accurate historical results (older than a few hours) are required

2. **Latency expectations**:

   - Dashboard generation can take several minutes
   - Low latency is not a priority requirement

3. **Data volume handling**:

   - 10 billion sales events per day (~10 TB/day at 1 KB/event)
   - ~1 million different products
   - Dashboard primarily viewed by employees (low request rate)

4. **Data simplification**:

   - Only initial sales events considered
   - Returns, refunds, exchanges, and recalls not factored into calculations

## Initial Design Approach

A naïve approach might involve:

1. Logging all events to distributed storage (HDFS or Elasticsearch)
2. Running MapReduce, Spark, or Elasticsearch queries to compute Top K lists

However, this approach presents significant challenges:

- Computationally intensive calculations
- Potentially long processing times (hours or days)
- Excessive storage requirements if raw logs are kept for extended periods

### Better Solution Strategy

Pre-process data through periodic aggregation:

1. Count product sales in time-based buckets (hourly, daily, weekly, monthly, yearly)
2. Sum counts from appropriate buckets when a specific period is requested
3. Sort the sums to generate the Top K list

This approach provides several advantages:

- Raw logs can be deleted after aggregation to save storage
- Processing becomes more efficient
- Historical data remains available for analysis

#preprocessing #aggregation #dataStorage

## High-Level Architecture - Lambda Approach

The Lambda architecture combines batch and streaming processing methods to handle large data volumes effectively. #LambdaArchitecture

Key components:

1. **Streaming layer**: Processes events in real-time to compute approximate rankings
2. **Batch layer**: Runs periodically to compute accurate rankings
3. **Serving layer**: Combines results from both pipelines

The process flow includes:

- Sales backend service sends events to Kafka topic
- Parallel processing through streaming and batch pipelines
- Results written to database for dashboard consumption

### Advantages of Lambda Architecture

- Provides both fast approximate results and accurate historical data
- Balances speed and accuracy requirements
- Flexible for various time intervals

## Aggregation Service Design

An aggregation service can optimize the Lambda architecture by:

1. Pre-aggregating sales events before they reach the streaming and batch pipelines
2. Reducing cluster sizes for both pipelines
3. Improving overall system efficiency

### Aggregation by Product ID

Raw sales events typically contain fields like (timestamp, product ID)

Aggregated events can be transformed to: (product_id, start_time, end_time, count, aggregation_host_id)

Time intervals need careful handling to maintain data integrity:

- Ensure (start_time, end_time) pairs stay within the same time bucket
- Example: (0100, 0110) is acceptable, but (0155, 0205) crosses hour boundaries and should be split

### Host and Product ID Mapping

For efficient processing, each host in the aggregation service handles a specific set of product IDs. Implementation options include:

1. Configuration file in source code (requires cluster restart for changes)
2. Configuration file in shared object store (more flexible, no restarts needed)
3. Database table in SQL or Redis

### Timestamp Management

Timestamps should be managed by the sales service, not the analytics service. This maintains:

- Separation of responsibilities
- Freedom to develop analytics pipelines independently
- Clean dependency structures

### Aggregation Process

Each aggregation host maintains:

1. A hash table with product IDs as keys and counts as values
2. Checkpoints of processed Kafka events in Redis

The aggregation workflow:

1. Consume events from Kafka
2. Update the internal hash table
3. Periodically flush aggregated data when:
   - A set time period has passed
   - Host memory is approaching capacity

The flush process:

1. Send aggregated events to a "Flush" Kafka topic
2. Write events to HDFS and mark checkpoint in Redis
3. Write events to streaming pipeline and mark checkpoint

### Failure Handling and Consistency

Fault tolerance is critical as any component might fail:

- Aggregation service hosts
- Redis instances
- HDFS cluster
- Streaming pipeline nodes
- Network connections

Options for maintaining consistency when writing to multiple services:

1. Checkpoint after each write (as described above)
2. Accept inconsistency if requirements permit
3. Implement periodic auditing/supervision
4. Use distributed transaction techniques (2PC, Saga, CDC)

The tradeoff with aggregation is that real-time results are delayed by the aggregation and flushing time, making it unsuitable for low-latency requirements.

#faultTolerance #consistency #checkpointing

## Batch Pipeline Design

The batch pipeline performs aggregation operations on increasing time intervals:

1. Rollup by hour
2. Rollup by day
3. Rollup by week
4. Rollup by month and year

### Batch Pipeline Workflow

For each rollup task:

1. **Verification**: Ensure dependent rollups have completed
2. **Counting**: Run Hive/SQL query to sum counts and write to batch_table
3. **Cleanup**: Delete corresponding rows from speed_table
4. **List Generation**: Create Top K lists using batch_table data

Dependencies between rollups:

- Daily rollup depends on 24 hourly rollups
- Weekly rollup depends on 7 daily rollups
- Monthly rollup depends on 28-31 daily rollups

Tools like Airflow's ExternalTaskSensor can verify dependent job completion.

#batchProcessing #ETL #dataAggregation

## Streaming Pipeline Design

The streaming pipeline provides approximate counts and rankings while batch jobs are still processing. This addresses the delay issue where batch processing can take significant time.

Challenges addressed by streaming:

- Hourly batch jobs may take 30+ minutes to complete
- This delays Top K lists for the hour, day, week, and month containing that hour

### Hash Table and Max-Heap Approach

A basic approach for a single host uses:

1. A hash table tracking frequencies of each item
2. A max-heap of size K to track the top items

This approach can be run in parallel for different time buckets (hour, day, week, month, year), with heap contents stored at the end of each period.

### Horizontal Scaling

To scale to multiple hosts, the system implements:

1. **Multi-tier aggregation**:

   - First tier: Initial count aggregation by product ID
   - Middle tier: Sum counts from upstream hosts
   - Final tier: Max-heaps that aggregate from middle tier

2. **Partitioning**:

   - By product ID
   - Potentially by timestamp

The system aggregates by combination of product ID and timestamp to ensure events are counted in the correct time ranges.

#streamProcessing #realTimeAnalytics #horizontalScaling

## Dashboard Implementation with Lambda Architecture

The dashboard consists of:

1. **Browser application**: Presents data to users
2. **Backend service**: Processes requests and queries database
3. **SQL database**: Stores batch_table and speed_table data

For optimal performance:

- Create a top_1000 view combining data from batch_table and speed_table
- Include column indicating source (batch or speed)
- Backend queries this view to prioritize batch data and fill gaps with speed data
- Caching can further improve response times

#dashboardDesign #viewOptimization #caching

## Kappa Architecture Alternative

Kappa architecture simplifies the Lambda approach by using a single technology stack for both batch and streaming processing. #KappaArchitecture

### Lambda vs. Kappa Comparison

| Aspect                | Lambda                                                                          | Kappa                                                                 |
| --------------------- | ------------------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Structure             | Separate batch and streaming pipelines with different code bases and frameworks | Single pipeline, cluster, code base, and processing framework         |
| Processing efficiency | Batch pipelines offer better performance for large data volumes                 | Processing large data is slower but occurs immediately upon ingestion |
| Error handling        | Errors in batch jobs may require complete reprocessing                          | Errors only affect specific data points                               |
| Complexity            | Higher operational overhead with multiple systems                               | Simpler to maintain and develop                                       |
| Storage requirements  | Can leverage HDFS for large volumes                                             | Storing large volumes in Kafka is costly and less scalable            |

### Kappa Implementation for Top K Dashboard

A Kappa implementation would:

1. Aggregate each sales event by product ID and time range
2. Use multi-tier aggregation to handle high event volumes
3. Monitor error rates closely and stop pipeline if critical threshold exceeded
4. Eliminate batch pipeline and aggregation service from the Lambda architecture

#architectureComparison #streamingProcessing

## System Monitoring and Management

Important monitoring aspects include:

1. **Batch ETL platform integration** with logging, monitoring, and alerting systems

   - Track unusually long run times
   - Detect and alert on task failures

2. **Data quality monitoring** for HDFS tables

   - Detect invalid data points
   - Raise alerts for potential issues

3. **Error rate tracking** in streaming pipelines

   - Define critical error thresholds
   - Implement automatic pipeline pausing when thresholds exceeded

#monitoring #alerting #dataQuality

## Advanced Considerations

Additional aspects that could be incorporated:

1. **Enhanced partitioning**:

   - By geography (country/city)
   - By revenue instead of sales volume
   - By change in metrics over time

2. **Search capabilities** for finding products by name or description patterns

#advancedFeatures #futureEnhancements

## Summary of Key Concepts

1. When accurate large-scale aggregation takes too much time, parallel streaming with approximation techniques can trade accuracy for speed (Lambda architecture)
2. Effective large-scale aggregation requires partitioning by aggregation keys
3. Maintain separation of concerns by storing non-aggregation data in separate services
4. Checkpointing provides a technique for distributed transactions involving systems with different read operation costs
5. Multi-tier horizontal scaling with heaps enables approximate large-scale aggregation
6. Count-min sketch offers an efficient approximation technique for frequency counting
7. Both Kappa and Lambda architectures provide viable approaches for processing large data streams, with different tradeoffs in complexity, accuracy, and resource usage

#keyConcepts #systemDesign #dataProcessing

References:

1. [Count - min sketch](https://www.youtube.com/watch?v=lGoCslwItiU)

2. [Top K Problem - Heavy Hitters](https://www.youtube.com/watch?v=kx-XDoPjoHw)

3. [Lambda architecture vs Kappa architecture](https://www.youtube.com/watch?v=waDJcSCXz_Y)

4. [Spine Leaf Architecture](https://www.youtube.com/watch?v=xjc7WLBb-nI)
