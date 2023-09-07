# Potiental Alert Conditions

Alerting is essential in monitoring. It ensures prompt notification of anomalies affecting performance or reliability. This guide has examples of potiental metrics you might consider alerting on in this scernrio.

### S3 Source Metrics
- Increased Error Rates - alert if 4xx or 5xx responses surpass a threshold within a time frame.
- Data Transfer Limits - alert for unexpected spikes or drops in data transfer rates.

### Lambda Processing Metrics
- Invocation Errors - alert for high numbers of failed invocations.
- Throttled Invocations - alert for a rise in throttled invocations.
- Extended Execution Time - alert for increased average execution duration.

### DynamoDB Storage Metrics
- Increased Read/Write Throttle Events - alert for high throttled read/write events.
- High Latency - alert for unexpected latency spikes.

### Pipeline Overall Metrics
- Data Inflow Rate Drop - alert for drops in data inflow rates from S3.
- Extended Processing Time - alert for processing time surpassing expected limits.
- Storage Anomalies - alert for sudden spikes or drops in DynamoDB storage.

Consider typical patterns and behaviors of your workload when setting up alerts. Adjust thresholds and conditions accordingly. Using Grafana's unified alerting allows for complex queries and conditions across multiple data sources.
