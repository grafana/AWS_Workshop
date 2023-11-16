# Breakout 2: Leveraging Prometheus Style Telemetry Data Stores with OpenTelemetry

## Introduction

The goal of this lab is to guide you through visualizing and monitoring your systems using Grafana Metrics, Logs, and Traces using the Cloud Watch Integration and Grafana Agent in Grafana Cloud.

As you have just learned there are two primary ways to send your Telemetry data from your AWS account to Grafana backends. One being via the direct installation of an agent on to your server (ie Amazon EC2 or Amazon EKS). The other option being to ship your data via the AWS Integration which lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without having to deploy or install any local agents or configurations.

## Section 1: Shipping from Infrastructure Directly
In order to ship from infrastructure directly you will simply install an agent onto your server. We often find customers installing the agent on their workloads runnning on Amazon Elastic Compute Cloud (EC2), Amazon Elastic Container Service (ECS), and Amazon Elastic Kubernetes Service (EKS) on EC2, AWS Fargate, and AWS Lambda, as well as on-premises.

This allows you to have fine tune control over what telemetry data you are shipping for example you may want to reduce the metrics enabled by default in certain exporters, or you may want to add particualr labels to your metrics and traces that tells you the region and environment the telemetry data is coming from, or you may not want to send logs only at a particualar path. By shipping data directly off of the machine itself you are able to create consistency across account, cloud providers, and onpremise. 

Lets take a look at this in action!

### Hands on - Using, Migrating, and Creating Dashboards in Kubernetes Monitoring
In this first activity we will be referencing data from Amazon Elastic Kubernetes Service (EKS). 

You have alot of options when it comes to deploying the Grafana Agent on Kubernetes. For example you can run the agent as a centralized collection service (K8s StatefulSets), as a host daemon (K8s DaemonSets), or as a container sidecar. 

[Click here](https://grafana.com/docs/agent/latest/flow/setup/deploy-agent/#deploy-grafana-agent) to learn about the pros and cons of each. In this case we installed the agent using Helm. Once the Agent is installed, your telemetry data will be available to query using PromQL, LogQL, and TraceQL.

The Grafana Cloud Integrations bundle Grafana Agent, tailored Grafana dashboards, and best-practice alerting defaults for common observability targets like Linux hosts, databases, and NGINX servers. Currently, there are roughly 100 different technologies that can be observed out of the box with our integrations, and we are and we are continually expanding that catalog.

Grafana Integrations make it possible to achieve a Fast Time to Value
You can think of integrations as the easy-button, it bundles exporters, the Grafana Agent, dashboards, and alerting for common systems such as linux, k8s, sql, etc. Integrations is a cloud only feature however you can leverage the [k8s mixin](https://github.com/kubernetes-monitoring/kubernetes-mixin) in the OSS version to achieve something similiar. You can also recreate these dashboards or export them, all of the basic metrics are available you will just have to copy over the relabels in the Grafana Agent configuration and change the remote_write from being a cloud instance to a self managed instance.

```Step 1:``` Navigate to [https://awsworkshopbreakouts.grafana.net/?orgId=1](https://awsworkshopbreakouts.grafana.net/?orgId=1)


```Step 2:``` In the upper left hand corner open up the **Menu Bar** by clicking on the Icon next to the word **Home**

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/b0fedb38-0f48-4076-a0f5-544c4d71556f/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1053,916&force_format=png&width=1120.0)


```Step 3:``` Click **Dashboards**

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/43befab8-fcde-443e-9235-4f0478080e75/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1146,515&force_format=png&width=1120.0)


```Step 4:``` Click **Integration - Kubernetes**

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/dae3de20-61aa-46d0-88ad-261e76152588/user_cropped_screenshot.jpeg?tl_px=0,93&br_px=1305,1055&force_format=png&width=1120.0)


```Step 5:``` Click **Kubernetes / Compute Resources / Cluster**

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/f7010be8-f22f-454e-8576-ec249011de03/user_cropped_screenshot.jpeg?tl_px=74,294&br_px=1364,1016&force_format=png&width=1120.0)


Notice you can adjust what renders on the page with the dropdown variables, this is great if you are looking to compare different clusters or namespaces. 
![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7f54c9fd-332d-4bc6-8987-6560f79ffe5f/user_cropped_screenshot.jpeg?tl_px=90,0&br_px=2384,1074&force_format=png&width=1120.0&wat=1&wat_opacity=1&wat_gravity=northwest&wat_url=https://colony-recorder.s3.amazonaws.com/images/watermarks/0EA5E9_standard.png&wat_pad=1226,20)


Additionally, you can change the timeframe for which the dashboard renders data. Try changing it to the last 12 hours. 

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/1cebd87b-96fd-463b-b830-1dee35f7ed8d/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1526,770&force_format=png&width=1120.0)


You can click and drag on the graph to zoom back into particular timeframes that look of interest

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-14/ec885a75-ae9e-4dd7-aa81-6d8737374913/user_cropped_screenshot.jpeg?tl_px=262,0&br_px=2556,519&force_format=png&width=1120.0)


```Step 6:``` Next we will drill down into one of our Namespaces. Under CPU Quota click on the Namespace = "otel-demo-obscon"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/b3709b76-fb8c-46cc-a956-750a09223749/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2055,1103&force_format=png&width=1120.0)


In the Pod overview dashboard feel free to further drill down into any of the pods showing

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/efce4b3c-0057-4dc5-bb54-8c587517d749/user_cropped_screenshot.jpeg?tl_px=233,0&br_px=2527,1064&force_format=png&width=1120.0)

The advantage of using Grafana backends in hybrid environments becomes apparent when using these types of dashboards, this is because you can leverage the same dashboards, alerts, and flows for similiarly hosted workloads regardless of if they are in the cloud or onpremise.

If you want to learn more about what is all avaiable with the Kubernetes Integration [click here](https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/navigate-k8s-monitoring/).

## Section 2: Shipping Data from CloudWatch
Sometimes installing an agent on AWS services is not possible. This is true for many AWS Managed Services. The idea is that they are managed so your burden of responsibliyt shifts when it comes to observing your applications. 

This is where the AWS integration comes in. The AWS integration type lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without having to deploy or install any local agents or configurations. Users only need to connect to their AWS account via the Grafana Cloud UI. 

*****Note:** *we have already done these step so data would be available for you to query. Once you setup the integration data begins to flow from that point forward into your Grafana Cloud telemetry databases. To set up this integration, it only takes three steps after you select what type of telemetry you would like to gather. *

```Step 1:``` Create a new role in AWS. This can be done automatically via CloudFormation or you can do so manually.

![Grafana Login Page](images/step1.png)

```Step 2:``` Input the connection details to your AWS account. These will be outputs in the CloudFormation stack or you can find them in the AWS console.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/57931064-947c-49d3-86d8-e3cbed6ba1b5/user_cropped_screenshot.jpeg?tl_px=25,0&br_px=1745,844&force_format=png&width=1120.0)

*****Note:** *you can optionally include your AWS Resource Tags in the data sent to Grafana Cloud (ex, env, application). Tags will appear as labels on the exported metric with a tag_ prefix. [Click here](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging.html) to learn more about the benefits of tagging your AWS resources if you do not already.*

```Step 3:``` Next you will configure which services you want to pull metrics from, as well as what metrics/aggregates you want to pull.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/6141c887-229f-4bf1-9939-9aac04610277/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2058,1182&force_format=png&width=1120.0)

```Step 4:``` Once you have figured configuring what metrics to scrape, you press 'Create Scrape Job' and Grafana will begin pulling in your metrics... it's that easy!

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/01c09c6d-c047-46ea-b96b-338dee1f9653/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1368,555&force_format=png&width=1120.0)

You can create any number of job configurations dictating which services, regions, and AWS accounts to collect metrics/logs from. 

Keep in mind what you are configuring here is pulling in CloudWatch data into Grafana Mimir (Prometheus) and Grafana Loki (Prometheus but for logs) – allowing users interact with and explore this data using the same query language, dashboards, alerts, etc. This provides a consistent way for your end users to query their data as well  reduces the cost variablity with querying/surfacing metrics and logs from the CloudWatch API via the Plugin (lab 1).

Once the data is in the Grafana Cloud telemetry backend there are no additional fees associated with querying, using dashboards, or running alerts against the data that’s been. The CloudWatch metrics integration installs prebuilt dashboards in your Grafana Cloud instance to help monitor your system. 

### Hands on with the Collected Data
A very common use case to leverage the AWS Integrations for is Managed AWS Services. Often times those do not have any way to directly ship your telemetry to other backends. In this part of the lab we will be looking at one of prebuilt dashboards that comes out of the box.

```Step 1:``` In the upper left hand corner open up the Menu Bar by clicking on the Icon next to the word **Home** -> **Dashboards**

![Grafana Menu Navigation](images/menu_nav.png)

```Step 2:``` Select "Integration - CloudWatch Metrics"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7ce00cc7-4167-4ad2-b300-e233aa56dfb6/user_cropped_screenshot.jpeg?tl_px=0,19&br_px=601,452&force_format=png&width=774)

```Step 3:``` Select "AWS Lambda"

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/f5a4cb96-3066-417d-9eb0-a0ea98c352f3/user_cropped_screenshot.jpeg?tl_px=0,146&br_px=565,627&force_format=png&width=860)

```Step 4:``` Click on the dropdown that is labeled 'job'. You will notice that directly corresponds to the name given to the scrape job we setup (previous steps). You will also see filters for region and function name.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/0daa42b0-a69b-4189-8b92-3c27327e50bf/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=609,368&force_format=png&width=774)

While these dashboards are less comphrensive than that of the Kubernetes integration we saw earilier. These dashboards are simply meant to give you a starting point to have some basic monitoring the real power comes in when we start creating our own tailorted dashboards with this. You can examine the underlying query and add it to your own dashboards very easily.

```Step 5:``` To do so, hoover over the upper righthand side of on of the dashboard visualizations. Then click on the three dots and select Explore.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/9db42a0d-b2fe-4021-a879-7a4422be2463/user_cropped_screenshot.jpeg?tl_px=55,23&br_px=1774,984&force_format=png&width=1120.0)

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/a36b0bc4-7b25-4331-a2bc-9331745a4991/user_cropped_screenshot.jpeg?tl_px=76,59&br_px=1624,925&force_format=png&width=1120.0)

   
You should now be able to see the underlying query that makes up this panel - this is PromQL! You could do the same thing with the Kubernetes dashboards we explored earlier.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/5f3c4d2d-f2dd-4694-88a4-e5fdc03b059a/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=3325,1317&force_format=png&width=1120.0)


```Step 6:``` Now if we wanted to add this to a dashboard we could do it straight from this screen by selecting **Add** -> **Add to dashboard**. Then you simply select which dashboard you want to add this query to.

![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-16/1ecb53ae-2b63-4f97-9592-646d12e84a3d/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1096,807&force_format=png&width=1120.0)


### Part 3: Unified Monitoring with Grafana - The First Pane of Glass
Grafana can query multiple data sources simultaneously. This means you can combine data from various AWS services (like CloudWatch, Elasticsearch, and AWS X-Ray) and other sources (like Prometheus or SQL databases) in a single dashboard for a unified view of your infrastructure and applications.

By providing a centralized and unified view of your AWS observability data, Grafana telemetry backends can help you to improve the operational efficiency of your team. By centralizing observability data and providing pre-built dashboards and alerts, Grafana telemetry backends can help you to reduce the time it takes to troubleshoot problems in your AWS environment.

See [this blog](https://grafana.com/blog/2022/06/06/grafana-dashboards-a-complete-guide-to-all-the-different-types-you-can-build/) to learn more about the different types of dashboards you can build with Grafana.

# STOP HERE... END OF LAB 2