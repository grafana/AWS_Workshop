# Breakout 2: Leveraging Prometheus Style Telemetry Data Stores with OpenTelemetry

## Introduction

Building on the concepts thus far, this lab provides a hands-on experience to explore various options for shipping telemetry data out of AWS to Grafana Cloud.

In lab 1 we discussed the CloudWatch plugin, which enables you to query both metrics and logs straight from the CloudWatch API. If you're using AWS, you're almost certainly using Amazon CloudWatch to collect and analyze observability data from your workloads. And while AWS remains the most broadly adopted cloud platform, not every company uses it exclusively, which means you need a tool that can standarized and give you a centralized view across all your environments.

In this lab, we will explore two popular options for shipping telemetry data out of AWS to Grafana. One being a fully managed option the other being through an open source agent you deploy and manage.

## Objectives
By the end of this lab, you will be able to:

- Select and implement the optimal telemetry data shipping method tailored to your specific requirements.
- Achieve enhanced observability, leading to more effective monitoring and quicker, data-driven decision-making.

## Prerequisites
- Ensure you have received your Grafana Cloud login details via email. Contact a Grafana Labs team member if you haven't.

## Emitting and Collecting Telemetry Data Primer

Instrumenting your applications to emit telemetry is crucial for gaining insights into their performance and behavior. 

Prometheus exporters, are known for their simplicity, efficiency, and vendor-agnostic nature, offer a straightforward way to collect metrics from various services. Meanwhile, OpenTelemetry, a collaborative project under the Cloud Native Computing Foundation (CNCF), offers a vendor-neutral and community-driven framework for instrumenting your applications, making it easier to achieve seamless observability across diverse environments and languages. To learn more about emitting telemetry data [click here](https://opentelemetry.io/docs/concepts/observability-primer/).

Once you have your applications exposing telemetry data you can collect it. Telemetry often relies on software agents running on the source systems to gather the data. These agents then forward the data to a centralized telemetry backend, where it is stored and analyzed.

There are many different ways you can choose to ship your telemetry data to our backends, but two popular option we see our customers choosing from are the [Grafana Agent](https://grafana.com/oss/agent/) and the [OpenTelemetry Collector](https://opentelemetry.io/docs/collector/). 

The Grafana Agent is an open-source telemetry collector designed to collect metrics, logs, and traces. It's compatible with Prometheus, Loki, and Tempo telemetry stack, ensuring reliability. The Grafana Agent can forward metrics to Prometheus-compatible endpoints, logs to Loki-compatible endpoints, and traces to OpenTelemetry-compatible endpoints.

This approach is advantageous because a single agent can efficiently capture multiple telemetry data types—metrics, logs, traces, and profiles—and route them to various telemetry backends. This unified method is particularly beneficial for maintaining a cohesive observability strategy across all infrastructure components, whether located on-premises or in the cloud.

When thinking about the biggest differentiation aspect between the Grafana Agent and the OpenTelemetry collector it is support. In terms of support, the Grafana Agent offers direct assistance, enabling faster issue resolution and feature development. This level of support is not available with the OpenTelemetry Collector, which relies on upstream maintainers for issue handling without guaranteed timelines. There are many other advantages of using the Grafana Agent specifically such as native Prometheus pipelines, enabling consolidating OTel and Prometheus pipelines on one single data collection solution.

For the purposes of this lab we will be focused on the Grafana Agent, but you can achieve the same results shown in the lab with the Open Telemetry collector, which will be out of scope for this lab.

## Lab Steps

### Part 1: Shipping Telemetry Data via Open Source Agent
The install guides [found here](https://grafana.com/docs/agent/latest/flow/setup/install/). 

We will be referencing data from ECS Fargate in this lab, and there are several ways to install the Grafana Agent on ECS Fargate. This can be achieved as a sidecar, in your Fargate instance directly, or as an ECS task. Once the Agent is installed your telemetry data will be avaiable to query.

Let’s take a quick look at the data we are gathering from our Fargate cluster. 
1. On your side menu, navigate to explore. 

<img width="339" alt="Screenshot 2023-10-23 at 1 42 31 PM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/39fb8306-5d38-4b6e-bd9a-96790130b104">

2. Set the data source to grafanacloud-awsworkshop-logs and paste the following query into the editor:
{job="varlogs", filename="/var/log/ecs/ecs-agent.log"} |= `` | logfmt

This query will return some of the ecs logs

<img width="1387" alt="Screenshot 2023-10-23 at 1 46 18 PM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/4434aa4b-bf17-4241-acbd-3bf0655a82a0">

3. Next, Set the data source to grafanacloud-awsworkshop-prom. 
Choose the label "instance" from the query builder, and choose a corresponding IP to go along with it. 
Next choose a metric that is available for this label. All of these  metrics are what we are collecting for our ECS Fargate Clusters.

<img width="776" alt="Screenshot 2023-10-23 at 1 50 17 PM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/de2405bc-7462-4ec9-9e66-6548545cdaba">

### Part 2: Grafana Cloud Integrations 

Grafana Cloud is designed to be user-friendly, making it accessible even for those who are new to observability practices. The Grafana Cloud Integrations bundle Grafana Agent, tailored Grafana dashboards, and best-practice alerting defaults for common observability targets like Linux hosts, databases, and NGINX servers. Currently, there are roughly 100 different technologies that can be observed out of the box with our integrations, and we are and we are continually expanding that catalog. In the case of monitoring specific infrastructure changing the location that the agent sends to is very simple. Lets say you wanted to change the configuration file to write to your OSS Tempo. For most integrations such as the Kubernetes monitoring or Linux you would simply change the remote_write destination.

Now, you can use Grafana Cloud to connect over 60 of the most popular AWS services, including EC2, Lambda, EBS, RDS, S3, ECS, ELB, and Billing. We’ve also improved how you interact with those services in our cloud by giving you one portal to set up and manage your AWS observability strategy. 

To setup the integration you simply follow a few steps in the Grafana Cloud UI no need to install anything directly on any EC2 instances, kubernetes clusters, etc. 

*** Note: for the purpose of this lab, we have already done these step so data would be available for you to query. Once you setup the integration data begins to flow from that point forward into your Grafana Cloud telemetry databases.

To setup this integration it only takes 3 steps after you select what type of telemetry you would like to gather
![Grafana Login Page](images/options.png)

Step 1: Create a new role in AWS. This can be done automatically via cloudformation or you can do so manually.
![Grafana Login Page](images/step1.png)

Step 2: Input the connection details to your AWS account
![Grafana Login Page](images/step2.png)

Step 3: Create scrape job
![Grafana Login Page](images/step3.png)

Within the integration, you can create any number of job configurations dictating which services, regions, and AWS accounts to collect from. This enables you to logically split your data into specific jobs and scrape any number of AWS accounts to better organize your data. [Click here](https://grafana.com/blog/2023/08/29/centralize-aws-observability-with-grafana-cloud/) to learn more about this integration.

**** Note: today the AWS integration only supports metrics and logs, you would have to have another method to ingest traces, as well all jobs have a scrape interval of 5m minutes and is currently not configurable. This is not the case for all other integrations. 

Thats it! Once we have this installed the telemetry data will be shipped to Grafana Cloud and you will be able to query it with PromQL as well as have various out of the box dashboards avaiable to you.

### Part 3: Pulling it all Together
OK but when do we use each? Let say we want to add on to our example from before 
k8s app that calls this service... 

1. Navigate to Dashboards on your side menu.

<img width="340" alt="Screenshot 2023-10-23 at 1 52 06 PM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/95f31048-de13-4459-a62e-04c595a04254">


2. Open the dashboard named "Serverless" . This dashboard provides an overview of the serverless infrastructure, including the health, addition of new services, pods, clusters, lambda functions, an instances.

<img width="1407" alt="Screenshot 2023-10-23 at 1 56 19 PM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/24e9f1d9-7cf4-451d-a657-c674bb5b088b">

3. The Fargate portion of this dashboard includes data from the Grafana Agent and the lambda portion of the dashboard brings in data from the Cloudwatch plugin that we learned about earlier in the lab. Take a moment to explore the various panels in the dashboard!

#Debug Workflow
When reviewing the serverless dashboard, did you notice issues with our services?
We are experiencing high errors in our lambda functions and a sudden spike in Pods running within our Fargate Clusters. 
Lets step through and debug this. 

1. Since we are seeing a high amount of errors with our lambda functions, let's take the "Errors per function [count/sec] into explore mode.
<img width="863" alt="Screenshot 2023-10-27 at 8 13 55 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/c50fa19f-fc09-45b6-bec2-f91b07a98742">


2. Next, split the screen. Our data source will still be Cloudwatch, but we want to change from Cloudwatch metrics to Cloudwatch logs as shown in the screenshot below.
<img width="600" alt="Screenshot 2023-10-27 at 8 14 50 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/986c1b7c-1347-4034-a95b-e78fc3e6e5ce">

 <img width="1384" alt="Screenshot 2023-10-27 at 8 18 41 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/7deb5534-9cd4-4096-b836-f73b35780a1b">

4. Now we need to choose our log groups. Click the "select log groups" button. Then type "/aws/lambda" in the search bar. 
You will want to choose the following log groups:
/aws/lambda/items /aws/lambda/batch-migration-function /aws/lambda/aws_config_bucket_events
When complete click the "Add Log Groups Button" 

<img width="404" alt="Screenshot 2023-10-27 at 8 24 17 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/b073a536-3090-43fe-a47d-b195f74c8fef">

<img width="698" alt="Screenshot 2023-10-27 at 8 24 32 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/9355d04f-b338-4a71-9730-90bce8c2ee7b">


5. Paste the following query into the Cloudwatch logs query interface in Grafana:
fields @timestamp, @message |
 sort @timestamp desc |
 limit 200

<img width="688" alt="Screenshot 2023-10-27 at 8 25 29 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/3bba380b-6057-4ab7-bbd0-9dac973ba94c">

6. Next, let's be sure our views across both Cloudwatch Metrics and logs are Synced across the same time range by clicking the sync button.


7. Then, zoom into a time range when you see a spike in errors in your metrics, the logs will automatically adjust to the same time range as you update your metrics.

<img width="1876" alt="Screenshot 2023-10-27 at 8 34 13 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/6ec40b54-ff49-4521-8eca-0bce71403bb2">

8. Now, we can have a look at our logs to see if there are any errors and what specifically is causing those errors.

   
10. Next, we should be sure to look at our Fargate cluster to ensure it is not impacted. Lets close the CloudWatch Logs Split pane.

11. Now Split the explore view again. and this time, choose the datasource "grafanacloud-awsworkshop-prom"

<img width="488" alt="Screenshot 2023-10-27 at 8 41 29 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/280bbe23-d733-471c-8fdf-5daa543abeeb">

12. Paste the following PromQL query into the Query Editor. This Query provides a count of the label "event" in our fargate cluster.
count by(event) (prometheus_sd_kubernetes_events_total{agent_hostname="ip-10-0-1-213.ec2.internal"})

<img width="913" alt="Screenshot 2023-10-27 at 8 47 06 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/6b1f9eec-9a43-4c59-b378-321743cc6ed5">

14. After pasting the query, run the query.

<img width="422" alt="Screenshot 2023-10-27 at 8 47 44 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/880d15b5-60fb-4c2f-9c3c-034c59d6c28b">

14. Next, we will want to change our graph type since the query returns multiple values. Let's use Stacked Bars.

   <img width="937" alt="Screenshot 2023-10-27 at 8 47 19 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/041d28f4-accf-4bc1-91bf-7107aa3c9aa5">
   
16. When digging into this, it does look like we do have some delete actions happening in our cluster as well!

17. Zooming out to the main dashboard, it looks like those delete actions are correlated with what is happening in our application!

    <img width="1876" alt="Screenshot 2023-10-27 at 8 57 34 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/4412b393-30cd-4a80-965b-0a61337ff2c3">
