# Expanding on the Grafana Dashboard for Monitoring a Data Processing Pipeline

This guide provides ideas of additional metrics that may be useful to include on your dashboard in this scernerio.

## S3 Source Metrics (via AWS CloudWatch)
- Total number of objects and storage size - singlestat showing object count and storage size
  
- PUT/GET/DELETE request rates - time series or graph with individual series/lines for each request type
  
- Errors (like 4xx or 5xx responses) - time series or graph with different colored lines for 4xx and 5xx errors
  
- Data transfer in/out rates - time series or graph using a color differentiation for data in vs. data out

## Lambda Processing Metrics (via AWS CloudWatch)
- Invocation count (success vs. failures) - bar graph or stacked bar graph using contrasting colors for success and failures
  
- Average execution duration - time series or graph
  
- Error count and types - pie chart or bar graph using different colors for each error type
  
- Throttled invocations - time series or graph

## DynamoDB Storage Metrics (via Amazon CloudWatch)
- Read/write capacity vs. consumed - time series or graph showing metrics for both capacity and consumption
  
- Throttled read/write events - time series or graph
  
- Latency metrics - time series or graph
  
- Number of items and table size - singlestat for items and time series or graph for table size.

## Pipeline Overall Metrics
- Data inflow rate from S3 - time series or graph
  
- Data processing time - time series or graph
  
- Number of results stored in DynamoDB over time - time series or graph
