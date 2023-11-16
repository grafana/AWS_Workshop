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


```Step 2:``` In the upper left-hand corner, open up the Menu Bar by clicking on the Icon next to the word **Home** -> **Dashboards**

![Grafana Menu Navigation](images/menu_nav.png)

```Step 3:``` Click **Integration - Kubernetes**


![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/dae3de20-61aa-46d0-88ad-261e76152588/user_cropped_screenshot.jpeg?tl_px=0,93&br_px=1305,1055&force_format=png&width=1120.0)


```Step 4:``` Click **Kubernetes / Compute Resources / Cluster**


![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/f7010be8-f22f-454e-8576-ec249011de03/user_cropped_screenshot.jpeg?tl_px=74,294&br_px=1364,1016&force_format=png&width=1120.0)


Notice you can adjust what renders on the page with the dropdown variables; this is great if you are looking to compare different clusters or namespaces. 


![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7f54c9fd-332d-4bc6-8987-6560f79ffe5f/user_cropped_screenshot.jpeg?tl_px=90,0&br_px=2384,1074&force_format=png&width=1120.0&wat=1&wat_opacity=1&wat_gravity=northwest&wat_url=https://colony-recorder.s3.amazonaws.com/images/watermarks/0EA5E9_standard.png&wat_pad=1226,20)


Additionally, you can change the timeframe for which the dashboard renders data. Try changing it to the last 12 hours. 

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/1cebd87b-96fd-463b-b830-1dee35f7ed8d/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1526,770&force_format=png&width=1120.0)


You can click and drag on the graph to zoom into interesting timeframes.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/ec885a75-ae9e-4dd7-aa81-6d8737374913/user_cropped_screenshot.jpeg?tl_px=262,0&br_px=2556,519&force_format=png&width=1120.0)


```Step 5:``` Next, we will drill down into one of our Namespaces. Under CPU Quota, click on the Namespace = "otel-demo-obscon"


![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/b3709b76-fb8c-46cc-a956-750a09223749/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2055,1103&force_format=png&width=1120.0)


Feel free to drill down further into any pods showing in the Pod overview dashboard.


![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/efce4b3c-0057-4dc5-bb54-8c587517d749/user_cropped_screenshot.jpeg?tl_px=233,0&br_px=2527,1064&force_format=png&width=1120.0)


The advantage of using Grafana backends in hybrid environments becomes apparent when using these types of dashboards; this is because you can leverage the same dashboards, alerts, and flows for similarly hosted workloads regardless of whether they are in the cloud or on-premise.

[Click here](https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/navigate-k8s-monitoring/) if you want to learn more about what is available with the Kubernetes Integration.

## Section 2: Shipping Data from CloudWatch
Sometimes, installing an agent on AWS services is not possible. This is true for many AWS Managed Services. The idea is that they are managed so your burden of responsibility shifts when observing your applications. 

This is where the AWS integration comes in. The AWS integration type lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without deploying or installing any local agents or configurations. Users only need to connect to their AWS account via the Grafana Cloud UI. 

*****Note:** *we have already done these steps, so data will be available for you to query. Once you set up the integration, data flows from that point forward into your Grafana Cloud telemetry databases. *

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

### Hands on with the Collected Data
A common use case to leverage the AWS Integrations is for Managed AWS Services. Often, those do not have a way to ship your telemetry directly to other backends. In this lab part, we will look at one of the prebuilt dashboards that comes out of the box.

```Step 1:``` In the upper left-hand corner, open up the Menu Bar by clicking on the Icon next to the word **Home** -> **Dashboards**

![Grafana Menu Navigation](images/menu_nav.png)

```Step 2:``` Select "Integration - CloudWatch Metrics"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7ce00cc7-4167-4ad2-b300-e233aa56dfb6/user_cropped_screenshot.jpeg?tl_px=0,19&br_px=601,452&force_format=png&width=774)

```Step 3:``` Select "AWS Lambda"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/f5a4cb96-3066-417d-9eb0-a0ea98c352f3/user_cropped_screenshot.jpeg?tl_px=0,146&br_px=565,627&force_format=png&width=860)

```Step 4:``` Click on the dropdown that is labeled 'job'. You will notice that directly corresponds to the name given to the scrape job we setup (previous steps). You will also see filters for region and function name.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/0daa42b0-a69b-4189-8b92-3c27327e50bf/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=609,368&force_format=png&width=774)

While these dashboards are less comprehensive than the Kubernetes integration we saw earlier. These dashboards are simply meant to give you a starting point for basic monitoring; the real power comes when we create tailored dashboards. To start this, you can easily examine the underlying query and add it to your custom dashboards.

```Step 5:``` To do so, hover over the upper right-hand side of one of the dashboard visualizations. Then click on the **three dots** and select **Explore**.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/9db42a0d-b2fe-4021-a879-7a4422be2463/user_cropped_screenshot.jpeg?tl_px=55,23&br_px=1774,984&force_format=png&width=1120.0)

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/a36b0bc4-7b25-4331-a2bc-9331745a4991/user_cropped_screenshot.jpeg?tl_px=76,59&br_px=1624,925&force_format=png&width=1120.0)


You should now be able to see the underlying query that makes up this panel - this is PromQL! You could do the same thing with the Kubernetes dashboards we explored earlier.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/5f3c4d2d-f2dd-4694-88a4-e5fdc03b059a/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=3325,1317&force_format=png&width=1120.0)


```Step 6: `` Now, if we want to add this to a dashboard, we could do it straight from this screen by selecting **Add** -> **Add to dashboard**. Then, you choose which dashboard to which you want to add this query, and voila.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/1ecb53ae-2b63-4f97-9592-646d12e84a3d/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1096,807&force_format=png&width=1120.0)


### Part 3: Unified Monitoring with Grafana - The First Pane of Glass
Grafana can query multiple data sources simultaneously. This means you can combine data from various AWS services (like CloudWatch, Elasticsearch, and AWS X-Ray) and other sources (like Prometheus or SQL databases) in a single dashboard for a unified view of your infrastructure and applications.

By providing a centralized and unified view of your AWS observability data, Grafana telemetry backends can help you improve your team's operational efficiency. By centralizing observability data and providing pre-built dashboards and alerts, Grafana telemetry backends can help you reduce the time it takes to troubleshoot problems in your AWS environment.

See [this blog](https://grafana.com/blog/2022/06/06/grafana-dashboards-a-complete-guide-to-all-the-different-types-you-can-build/) to learn more about the different types of dashboards you can build with Grafana.

# STOP HERE... END OF LAB 2