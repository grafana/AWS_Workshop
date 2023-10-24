# Potiental Alert Conditions

Alerting is essential in monitoring. It ensures prompt notification of anomalies affecting performance or reliability. This guide has examples of potiental metrics you might consider alerting on in this scernrio.

1. **API Gateway:**
   - **High Latency:** Alert when the response time exceeds a certain threshold, indicating potential performance issues.
   - **Spike in 4XX Errors:** Alert on a sudden increase in client-side errors, which could indicate issues with request formatting or authentication.
   - **Spike in 5XX Errors:** Alert on an increase in server-side errors, indicating issues with the backend Lambda function or other internal issues.

2. **Lambda:**
   - **High Duration:** Alert when function execution time is close to the timeout limit, which could lead to incomplete executions.
   - **Increase in Error Rate:** Alert when there is a sudden spike in function errors, indicating potential issues in the code or with external dependencies.
   - **Low Success Rate:** Alert when the ratio of successful invocations to total invocations drops below a certain threshold.

3. **DynamoDB:**
   - **Read/Write Throttling:** Alert when the number of throttled read or write requests increases, indicating capacity issues.
   - **High Latency:** Alert on increased latency for read or write operations.
   - **High Provisioned Throughput Consumption:** Alert when usage is close to the provisioned capacity limits.

4. **Third-party Recommendations ML Model:**
   - **High Latency:** Alert if the ML model’s response time increases, which could impact user experience.
   - **Error Rate:** Alert on increased error rates in calls to the ML model, which might indicate issues with the model or the data being sent to it.
   - **Low Accuracy:** If possible, alert on detected drops in the accuracy or effectiveness of the recommendations.

5. **General Monitoring:**
   - **High Cost:** Alert when AWS costs exceed expected ranges.
   - **Unusual Activity:** Alert on any unexpected spikes in usage or activity, which could indicate a security issue or misconfiguration.
   - **Resource Limit Approaching:** Alert when usage is approaching any service limits to prevent service disruptions.