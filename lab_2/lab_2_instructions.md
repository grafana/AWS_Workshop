# Breakout 2: Leveraging Prometheus Style Telemetry Data Stores with OpenTelemetry

## Introduction

The goal of this lab is to guide you through visualizing and monitoring your systems using Grafana Metrics, Logs, and Traces using the Cloud Watch Integration and Grafana Agent in Grafana Cloud.

As you have just learned, there are two primary ways to send your Telemetry data from your AWS account to Grafana backends. One is via the direct installation of an agent onto your server (i.e., Amazon EC2 or Amazon EKS). The other option is to ship your data via the AWS Integration, which lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without deploying or installing individual agents or configurations.

## Section 1: Shipping from Infrastructure Directly
To ship from infrastructure directly, you will install an agent onto your server. We often find customers installing the agent on their workloads running on Amazon Elastic Compute Cloud (EC2), Amazon Elastic Container Service (ECS), and Amazon Elastic Kubernetes Service (EKS) on EC2, AWS Fargate, and AWS Lambda, as well as on-premises.

This allows you to have fine-tuned control over what telemetry data you are shipping; for example, you may want to reduce the metrics enabled by default in certain exporters, or you may want to add particular labels to your metrics and traces that tell you the region and environment the telemetry data is coming from, or you may not want to send logs only at a particular path. Installing an agent directly on the machine eliminates the variability of naming schemas between vendors, what metrics they surface, etc., allowing you to create consistency across accounts, cloud providers, and on-premise. 

Let's take a look at this in action!

### Hands on - Using, Migrating, and Creating Dashboards in Kubernetes Monitoring
This first activity will reference data from a Kubernetes Service (i.e., Amazon EKS). 

You have a lot of options when it comes to deploying the Grafana Agent on Kubernetes. For example, you can run the agent as a centralized collection service (K8s StatefulSets), host daemon (K8s DaemonSets), or container sidecar.

[Click here](https://grafana.com/docs/agent/latest/flow/setup/deploy-agent/#deploy-grafana-agent) to learn about the pros and cons of each. In this case, we installed the agent using Helm. Once the agent is installed, your telemetry data will be available to query using PromQL, LogQL, and TraceQL.

The Grafana Cloud Integrations bundle Grafana Agent, tailored Grafana dashboards, and best-practice alerting defaults for common observability targets like Linux hosts, databases, and NGINX servers. Today, around 100 different technologies can be observed using integrations, and we are continually expanding that catalog.

Grafana Integrations make it possible to achieve a fast time to value. Integrations are the easy button; it bundles exporters, the Grafana Agent, dashboards, and alerts for common systems such as Linux, k8s, SQL, etc. Integrations are a cloud-only feature; however, you can leverage the [k8s mixin](https://github.com/kubernetes-monitoring/kubernetes-mixin) in the OSS version to achieve something similar. You can also recreate these dashboards or export them. Note that you will have to copy over the relabels in the Grafana Agent configuration and change the remote_write from a cloud instance to a self-managed one.

```Step 1:``` Navigate to [https://awsworkshopbreakouts.grafana.net/?orgId=1](https://awsworkshopbreakouts.grafana.net/?orgId=1)


```Step 2:``` In the upper left-hand corner, open up the Menu Bar by clicking on the Icon next to the word **Home**. In the main menu, expand **Infrastructure**, then click **Kubernetes**.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/d0ea0787-354a-40b6-a7a0-1956a9a0e9bf/user_cropped_screenshot.jpeg?tl_px=0,24&br_px=487,890&force_format=png&width=200)

The main Kubernetes page displays a snapshot of issues that exceed specific thresholds (and any associated alerts) for the data source chosen in the drop-down menu.

You can see the graphed counts for Clusters, Nodes, Pods, and containers, as well as:

- Pods that have been in a non-running state for 15 minutes or more
- Node issues with CPU and memory usage over 90% for over 5 minutes, and disks exceeding capacity of over 90%
- Persistent Volumes that have been using over 90% of their capacity

```Step 3:``` Now lets dive into our Clusters. On the left hand side click **Cluster navigation**.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/6b7f002d-fe2e-4523-8273-72404da2d851/user_cropped_screenshot.jpeg?tl_px=0,22&br_px=742,887&force_format=png&width=200)

Here you can use seemlessly navigate from Clusters, namespaces, workloads, and Nodes through to containers.

Additionally you can analyze the cost estimates for running each of the cluster/workloads/nodes.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/b1e15808-b3b9-4177-ab59-220abb8a760d/user_cropped_screenshot.jpeg?tl_px=6,0&br_px=1726,579&force_format=png&width=500.0)

```Step 4:``` Click **Namespaces** on the navigation bar. 

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/67e3553d-f772-4c29-81bd-b39640428e0f/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1488,601&force_format=png&width=500.0)

```Step 5:``` Click on the **otel-demo** namespace. 

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/70521fa3-4f16-4671-bf9a-ed0f0052087f/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2622,959&force_format=png&width=500.0)

On this page you can see the usage patterns associated with the workloads running in this particular namespace.

You can also view performance over time.

```Step 6:``` At the top right hand corner change the timeframe from `Last 1 hour` to the `Last 2 days`.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/2312d9ff-4065-41f1-8484-4417704814f3/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1232,633&force_format=png&width=500.0)

Lets drill even further down into a particular workload.

```Step 7:``` Scroll down to the list of workloads and select **my-otel-demo-checkoutservice**.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/da189308-d8ec-45d9-9c61-73a24205f53c/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2719,735&force_format=png&width=1000.0)

Here we can see the infrastructure usage associated with this workload, but what if we are interested in seeing the application level details. 

```Step 8:``` At the top click on the **View application layer** button.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/b2d88944-d5d4-4ada-a0e5-aac249fa79e7/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1764,539&force_format=png&width=500.0)

Now we can seemlessly go into our application layer details. One this page you will see the RED metrics associated with this service. From here you can explore things such as the Service Map, which depicts the intra relationship between our services, as well as dive into service specific logs and traces.

The advantage of using Grafana backends in hybrid environments becomes apparent when using these types of dashboards; this is because you can leverage the same dashboards, alerts, and flows for similarly hosted workloads regardless of whether they are in the cloud or on-premise.

[Click here](https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/navigate-k8s-monitoring/) if you want to learn more about what is available with the Kubernetes Integration.

## Section 2: Shipping Data from CloudWatch
Sometimes, installing an agent on AWS services is not possible. This is true for many AWS Managed Services. The idea is that they are managed so your burden of responsibility shifts when observing your applications. 

This is where the AWS integration comes in. The AWS integration type lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without deploying or installing any local agents or configurations. Users only need to connect to their AWS account via the Grafana Cloud UI. 

*****Note:** *we have already done these steps, so data will be available for you to query. Once you set up the integration, data flows from that point forward into your Grafana Cloud telemetry databases.*

```Step 1:``` Create a new role in AWS. This can be done automatically via CloudFormation, or you can do so manually.

![Grafana Login Page](images/step1.png)

```Step 2:``` Input the connection details to your AWS account. These will be outputs in the CloudFormation stack or you can find them in the AWS console.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/57931064-947c-49d3-86d8-e3cbed6ba1b5/user_cropped_screenshot.jpeg?tl_px=25,0&br_px=1745,844&force_format=png&width=1120.0)

*****Note:** *you can optionally include your AWS Resource Tags in the data sent to Grafana Cloud (ex, env, application). Tags will appear as labels on the exported metric with a tag_ prefix. [Click here](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging.html) to learn more about the benefits of tagging your AWS resources if you do not already.*

```Step 3:``` Next, you will configure which services you want to pull metrics from and what metrics/aggregates you want to ingest.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/6141c887-229f-4bf1-9939-9aac04610277/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2058,1182&force_format=png&width=1120.0)

```Step 4:``` Once you have configured what metrics to scrape, press 'Create Scrape Job' and Grafana will begin pulling in your metrics... it's that easy!

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/01c09c6d-c047-46ea-b96b-338dee1f9653/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1368,555&force_format=png&width=1120.0)

You can create any number of job configurations dictating which services, regions, and AWS accounts to collect metrics/logs from. 

Keep in mind what you are configuring here is pulling in CloudWatch data into Grafana Mimir (Prometheus) and Grafana Loki (Prometheus but for logs) – allowing users to interact with and explore this data using the same query language, dashboards, alerts, etc. This provides a consistent way for end users to query their data and reduces the cost variability with querying/surfacing metrics and logs from the [CloudWatch API](https://aws.amazon.com/cloudwatch/pricing/) via the Plugin (lab 1).

Once the data is in the Grafana Cloud telemetry backend, there are no additional fees associated with querying, using dashboards, or running alerts against the data that’s been collected. The CloudWatch metrics integration installs prebuilt dashboards in your Grafana Cloud instance to help monitor your system. 

### Exploring the Collected Data
A common use case to leverage the AWS Integrations is for Managed AWS Services. Often, those do not have a way to ship your telemetry directly to other backends. In this lab part, we will look at one of the prebuilt dashboards that comes out of the box.

```Step 1:``` In the upper left-hand corner, open up the Menu Bar by clicking on the Icon next to the word **Home** -> **Dashboards**

![Grafana Menu Navigation](images/menu_nav.png)

```Step 2:``` Select "Integration - CloudWatch Metrics"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7ce00cc7-4167-4ad2-b300-e233aa56dfb6/user_cropped_screenshot.jpeg?tl_px=0,19&br_px=601,452&force_format=png&width=500)

```Step 3:``` Select "AWS Lambda"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/f5a4cb96-3066-417d-9eb0-a0ea98c352f3/user_cropped_screenshot.jpeg?tl_px=0,146&br_px=565,627&force_format=png&width=500)

```Step 4:``` Click on the dropdown that is labeled 'job'. You will notice that directly corresponds to the name given to the scrape job we setup (previous steps). You will also see filters for region and function name.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/2caf81a7-09f2-4bca-ac03-6b4edc23407b/user_cropped_screenshot.jpeg?tl_px=130,0&br_px=2423,380&force_format=png&width=500.0)

While these dashboards are less comprehensive than the Kubernetes integration we saw earlier. These dashboards are simply meant to give you a starting point for basic monitoring; the real power comes when we create tailored dashboards. To start this, you can easily examine the underlying query and add it to your custom dashboards.

```Step 5:``` To do so, hover over the upper right-hand side of one of the dashboard visualizations. Then click on the **three dots** and select **Explore**.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/3aac486b-18c4-4fc4-bb4d-cda59c7a5fc3/user_cropped_screenshot.jpeg?tl_px=239,0&br_px=2532,547&force_format=png&width=500.0)

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/8a0a5715-23f3-48d9-b371-85d71387bf0e/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1816,910&force_format=png&width=500.0)


You should now be able to see the underlying query that makes up this panel - this is PromQL! You could do the same thing with the Kubernetes dashboards we explored earlier.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/191763ae-a6ec-487b-accc-fdce8abc7f31/user_cropped_screenshot.jpeg?tl_px=154,51&br_px=1701,917&force_format=png&width=500.0)

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/eb2b9dc5-9f43-4b65-9b3a-a58e1357d66f/user_cropped_screenshot.jpeg?tl_px=228,24&br_px=2521,1305&force_format=png&width=500.0)

```Step 6:``` Now, if we want to add this to a dashboard, we could do it straight from this screen by selecting **Add** -> **Add to dashboard**. Then, you choose which dashboard to which you want to add this query, and voila.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/1ecb53ae-2b63-4f97-9592-646d12e84a3d/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1096,807&force_format=png&width=500.0)


### Part 3: Unified Monitoring with Grafana - The First Pane of Glass
Grafana can query multiple data sources simultaneously. This means you can combine data from various AWS services (like CloudWatch, Elasticsearch, and AWS X-Ray) and other sources (like Prometheus or SQL databases) in a single dashboard for a unified view of your infrastructure and applications.

By providing a centralized and unified view of your AWS observability data, Grafana telemetry backends can help you improve your team's operational efficiency. By centralizing observability data and providing pre-built dashboards and alerts, Grafana telemetry backends can help you reduce the time it takes to troubleshoot problems in your AWS environment.

See [this blog](https://grafana.com/blog/2022/06/06/grafana-dashboards-a-complete-guide-to-all-the-different-types-you-can-build/) to learn more about the different types of dashboards you can build with Grafana.

# STOP HERE... END OF LAB 2