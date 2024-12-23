# Chapter 10 - Design a database batch auditing service - (Q&A)

### **Introduction to Database Batch Auditing Service**

**Q1. Why is data quality important in system design? Discuss its dimensions with examples.**  
**A1.** Data quality ensures datasets are suitable for their intended purpose. Its dimensions include:

- **Accuracy**: Data reflects real-world values (e.g., a product's price matches the database record).
- **Completeness**: No missing values (e.g., all fields in a customer profile are populated).
- **Consistency**: Data aligns across systems (e.g., identical records in multiple databases).
- **Validity**: Adherence to formats and ranges (e.g., dates are in `YYYY-MM-DD` format).
- **Uniqueness**: Absence of duplicates (e.g., unique transaction IDs).
- **Timeliness**: Data availability when required (e.g., updated stock levels during peak sales).  
  #DataQuality #Accuracy #Completeness

**Q2. What are the two main approaches to data validation, and when would you use each?**  
**A2.**

1. **Anomaly Detection**: Automated, uses historical data to identify outliers (e.g., sales spikes).
2. **Manual Validations**: User-defined conditions to ensure rule adherence (e.g., timestamps within 24 hours).  
    Use anomaly detection for dynamic, large-scale datasets and manual validations for rule-specific, deterministic checks.  
   #DataValidation #AnomalyDetection

---

### **Necessity of Auditing**

**Q3. Why might replication and backups fail to prevent data loss?**
A3.Replication delays or leader node failures can result in data loss before backup completion.

- **Quorum Consistency** ensures a majority of nodes acknowledge writes (e.g., in Cassandra).
- Example: Data is written to an in-memory table and replicated before returning success, reducing loss risks.  
  #DataLoss #QuorumConsistency

**Q4. Why is it insufficient to rely solely on database constraints and application-level validations?**  
**A4.** Database constraints may be inflexible for evolving requirements, and applications may miss errors due to bugs. Batch audits add a safety net, ensuring legacy data is validated irrespective of earlier oversight.  
 #DatabaseConstraints #ApplicationValidation

---

### **Manually Defined Validations**

**Q5. Explain how you can define a validation to ensure data integrity for daily user activity. Provide an example query.**  
**A5.** Use SQL queries to define rules.

- Example: Ensure users don't exceed five transactions/day:
  ```sql
  SELECT user_id, COUNT(*) AS cnt
  FROM Transactions
  WHERE Date(timestamp) = CURDATE() - INTERVAL 1 DAY
  GROUP BY user_id
  ```
  Conditional Statement: `cnt <= 5` ensures integrity by detecting anomalies.  
  #SQLValidation #DailyActivity

**Q6. What types of errors can batch audits detect that application-level validations might miss?**  
**A6.**

- Legacy issues (e.g., a row with a 5-year future date due to initial validation gaps).
- Anomalies requiring historical data (e.g., duplicate entries or missing records).  
  #BatchAudits #ErrorDetection

---

### **Audit Job Design**

**Q7. Outline the workflow of a basic SQL batch auditing job.**  
**A7.**

1. Execute SQL queries.
2. Retrieve and process results.
3. Validate against defined conditions.
4. Log results and generate alerts if conditions fail.  
    Example: Python scripts facilitate this process by integrating query execution and result validation.  
   #AuditWorkflow #SQLAudits

**Q8. How can you optimize batch audits for large files or distributed systems?**  
**A8.**

- Use **LOAD DATA** in MySQL for fast imports before auditing with queries.
- Leverage distributed systems like HDFS, Hive, or Spark for parallel processing.  
  #LargeScaleAudits #DistributedSystems

---

### **System Requirements**

**Q9. What are the functional and non-functional requirements for a batch auditing service?**  
**A9.**

- **Functional**: CRUD operations, scheduled execution, alerts for failures, job status logs.
- **Non-Functional**: Scalability to 10,000 jobs, moderate availability, and secure job access controls.  
  #SystemRequirements

**Q10. How can you ensure audit jobs complete within defined intervals?**  
**A10.** Limit query execution to 10 minutes during configuration and 15 minutes at runtime. Enforce termination and disable the job if thresholds are exceeded.  
 #QueryLimits #ExecutionConstraints

---

### **High-Level Architecture**

**Q11. Describe the components and interactions in a batch auditing service architecture.**  
**A11.**

- **Backend**: Generates configurations and manages interactions.
- **Batch ETL Service**: Executes queries, processes results, and logs outcomes.
- **Alerting Service**: Handles notifications for failures.
- Integrates tools like Airflow for scheduling and distributed systems for scalability.  
  #Architecture #BatchETL

---

### **Constraints and Optimization**

**Q12. How do you prevent resource contention caused by simultaneous queries?**  
**A12.**

- Restrict concurrent queries using thread pools.
- Monitor query latency and trigger alerts if thresholds are exceeded.
- Introduce a query service to centralize execution and manage load.  
  #ConcurrencyControl #ResourceOptimization

**Q13. What pre-submission checks can prevent invalid queries?**  
**A13.**

- Disallow full table scans or unfiltered partitions.
- Provide query execution plans to users with tuning recommendations.  
  #QueryValidation #PreSubmissionChecks

---

### **Logging, Monitoring, and Alerts**

**Q14. What logs and metrics are critical for monitoring audit jobs?**  
**A14.**

- Job statuses (e.g., started, succeeded, failed).
- Query execution durations.
- Backend response times (e.g., P99 latency).
- Alerts for failures and upstream job issues.  
  #Monitoring #AuditLogs

---

### **Advanced Features**

**Q15. How can schema metadata improve query authoring in audits?**  
**A15.** Suggest partition columns for WHERE filters or templates for recent partitions. Automatically update configurations for schema changes (e.g., column renaming).  
 #MetadataUsage #SchemaUpdates

**Q16. Why are cross-data center audits necessary, and how are they implemented?**  
**A16.** Ensure consistency across replicas using sampling tests to compare data between data centers.  
 #DataConsistency #CrossDataCenterAudits

---

### **Pipeline Integration**

**Q17. How should a failed audit in a pipeline impact downstream jobs?**  
**A17.**

1. Disable downstream audits and dependent jobs.
2. Notify owners of all affected jobs.
3. Update metadata to flag problematic tables, preventing bad data propagation.  
   #PipelineAudits #FailureManagement

---
