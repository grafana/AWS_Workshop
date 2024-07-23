# Breakout 2: Leveraging Prometheus Style Telemetry Data Stores with OpenTelemetry

## Introduction

The goal of this lab is to guide you through visualizing and monitoring your systems using Grafana Metrics, Logs, and Traces using the Cloud Watch Integration and Grafana Agent in Grafana Cloud.

As you have just learned, there are two primary ways to send your Telemetry data from your AWS account to Grafana backends. One is via the direct installation of an agent onto your server (i.e., Amazon EC2 or Amazon EKS). The other option is to ship your data via the AWS Integration, which lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without deploying or installing individual agents or configurations.

## Section 1: Shipping from Infrastructure Directly
To ship from infrastructure directly, you will install an agent onto your server. We often find customers installing the agent on their workloads running on Amazon Elastic Compute Cloud (EC2), Amazon Elastic Container Service (ECS), and Amazon Elastic Kubernetes Service (EKS) on EC2, AWS Fargate, and AWS Lambda, as well as on-premises.

This allows you to have fine-tuned control over what telemetry data you are shipping; for example, you may want to reduce the metrics enabled by default in certain exporters, or you may want to add particular labels to your metrics and traces that tell you the region and environment the telemetry data is coming from, or you may not want to send logs only at a particular path. Installing an agent directly on the machine eliminates the variability of naming schemas between vendors, what metrics they surface, etc., allowing you to create consistency across accounts, cloud providers, and on-premise. 

Let's take a look at this in action!

### 1.1: Using, Migrating, and Creating Dashboards in Kubernetes Monitoring
This first activity will reference data from a Kubernetes Service (i.e., Amazon EKS). 

You have a lot of options when it comes to deploying the Grafana Agent on Kubernetes. For example, you can run the agent as a centralized collection service (K8s StatefulSets), host daemon (K8s DaemonSets), or container sidecar.

[Click here](https://grafana.com/docs/agent/latest/flow/setup/deploy-agent/#deploy-grafana-agent) to learn about the pros and cons of each. In this case, we installed the agent using Helm. Once the agent is installed, your telemetry data will be available to query using PromQL, LogQL, and TraceQL.

The Grafana Cloud Integrations bundle Grafana Agent, tailored Grafana dashboard experiences, and best-practice alerting defaults for common observability targets like Linux hosts, databases, and NGINX servers. Today, around 100 different technologies can be observed using integrations, and we are continually expanding that catalog.

Grafana Integrations make it possible to achieve a fast time to value. Integrations are the easy button; it bundles exporters, the Grafana Agent, dashboards, and alerts for common systems such as Linux, k8s, SQL, etc. Integrations are a cloud-only feature; however, you can leverage the [k8s mixin](https://github.com/kubernetes-monitoring/kubernetes-mixin) in the OSS version to achieve something similar.

In the first part of this lab, we'll dive into the Kubernetes app experience, which is designed to streamline the management of your Kubernetes environments. This app is meant to help with both reactive problem solving and proactive management, ensuring smoother operations and better resource optimization.

1.  Navigate to the Grafana Cloud instance you were provided with at the start of the workshop.

1.  In the upper left-hand corner, open the Menu Bar by clicking on the Menu/Burger icon next to the word **Home**. In the main menu, expand **Infrastructure**, then click **Kubernetes**.

    The Kubernetes Overview page will be shown:

    ![](./images/k8s_overview.png)

    Kubernetes Overview acts as your starting point for understanding the operational & infrastructure health of your Kubernetes environments. The most critical issues and alerts from all of your clusters are bubbled up into this view. 

    You can see the graphed counts for Clusters, Nodes, Pods, and containers, as well as CPU usage, memory usage, pod and container alerts.

1.  Now let's dive into a single cluster. From the left hand menu click **Infrastructure &rarr; Kubernetes &rarr; Clusters**.

1.  From here, click on the single **cluster** in the list:

    ![](./images/k8s_cluster.png)

    The cluster view is displayed. From here, you can see all of the details of this Kubernetes cluster, which is an Elastic Kubernetes Service (EKS) cluster on AWS. 
    
    You can see cluster details like:

    - provider (AWS)
    - nodes
    - CPU and memory usage
    - disk

1.  Scroll down to the nodes list and choose a **node** in the cluster:

    ![](./images/k8s_node.png)

1.  Now we see the Node view, which shows us all the details of this node in the cluster, some aggregated metrics, and all of the Pods running on the Node:

    ![](./images/k8s_node2.png)

1.  Now let's take a look at some of the workloads running on our cluster. From the sidebar, click on **Infrastructure &rarr; Kubernetes &rarr; Workloads**

    The Workloads view will be displayed. We can see all our workloads, across all clusters.

1.  From the Workloads view, click on the workload that starts with the name `accountingservice`:

    ![](./images/k8s_workloads.png)

1.  From the `accountingservice` workload view, we can see aggregated metrics of this workload, from the infrastructure layer. We can see CPU, memory usage and cost.

    Scroll down to the list of Pods at the bottom of this page and click on the Pod name to drill into it:

    ![](./images/k8s_workload_pod.png)

1.  In the Pod view, you can see the CPU and memory usage of this Pod. 

    You can also view performance over time, such as usage over the last year, six months, 90 days, etc.

    ![](./images/k8s_pod.png)

1.  At the top right-hand corner, change the timeframe from `Last 1 hour` to the `Last 2 days`.

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/2312d9ff-4065-41f1-8484-4417704814f3/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1232,633&force_format=png&width=500.0)

    Observe how we can see the health of this pod over the last 2 days.

[Click here](https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/navigate-k8s-monitoring/) if you want to learn more about what is available with the Kubernetes Integration.

Next we'll look at how we can observe the application layer in our Kubernetes cluster.

### 1.2: Application Observability

In our demonstration EKS cluster, we have also instrumented our applications to send traces to Grafana Cloud. This completes our observability picture by allowing us to see health at the application layer, through metrics, logs and traces.

1.  From the side menu in Grafana, click on **Application** to navigate to Application Observability.

    The Application Observability home page is displayed. This gives us a complete picture of our application health, with metrics derived from OpenTelemetry traces, which gives us information on:

    - Request duration trends
    - Error rate
    - Request rate

1.  From here, click on _checkoutservice_ to drill into it:

    ![](./images/appo11y_home.png)

1.  Now, we can explore application health for our Checkout service. On this page, you will see the RED metrics (rate, error, duration) associated with this service. 

    Further down the page, you can also see the automatically discovered operations of this service, and associated services.

    ![](./images/appo11y.png)

1.  Return to the top of the page and click on the Service Map tab. This depicts the relationship between our services, as well as allowing us to dive into service-specific logs and traces.

    ![](./images/servicemap.png)

The advantage of using Grafana backends in hybrid environments becomes apparent when using these types of dashboards; this is because you can leverage the same dashboards, alerts, and flows for similarly hosted workloads regardless of whether they are in the cloud or on-premise since you are emitting consistent telemetry data from each environment.

## Section 2: Shipping Data from CloudWatch
Sometimes, installing an agent on AWS services is not possible. This is true for many AWS Managed Services. The idea is that they are managed so your burden of responsibility shifts when observing your applications. 

This is where the AWS integration comes in. The AWS integration type lets users connect and pull their AWS CloudWatch metrics into Grafana Cloud without deploying or installing any local agents or configurations. Users only need to connect to their AWS account via the Grafana Cloud UI. 

Once you set up the integration, data flows from that point forward into your Grafana Cloud telemetry databases.

**In this lab environment, we have already configured Grafana Cloud's AWS integration for you. If you want to see how it is done, jump to the appendix at the end of this document.**

### Exploring data collected from CloudWatch
A common use case to leverage the AWS Integrations is for Managed AWS Services. Often, those do not have a way to ship your telemetry directly to other backends. In this lab part, we will look at one of the prebuilt dashboards that comes out of the box.

1.  In the upper left-hand corner, open up the Menu Bar by clicking on the Menu/Burger icon next to the word **Home** -> **Dashboards**

    ![Grafana Menu Navigation](images/menu_nav.png)

1.  Select "Integration - CloudWatch Metrics"

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/7ce00cc7-4167-4ad2-b300-e233aa56dfb6/user_cropped_screenshot.jpeg?tl_px=0,19&br_px=601,452&force_format=png&width=500)

1.  Select "AWS Lambda"

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/f5a4cb96-3066-417d-9eb0-a0ea98c352f3/user_cropped_screenshot.jpeg?tl_px=0,146&br_px=565,627&force_format=png&width=500)

1.  Click on the dropdown that is labeled 'job'. You will notice that directly corresponds to the name given to the scrape job we setup (previous steps). Select **prod-ecommerce** for the job value. You will also see filters for _region_ and _function name_.

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/2caf81a7-09f2-4bca-ac03-6b4edc23407b/user_cropped_screenshot.jpeg?tl_px=130,0&br_px=2423,380&force_format=png&width=1120.0)

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/3aac486b-18c4-4fc4-bb4d-cda59c7a5fc3/user_cropped_screenshot.jpeg?tl_px=239,0&br_px=2532,547&force_format=png&width=1120.0)

    While these dashboards are less comprehensive than the Kubernetes integration we saw earlier, they give you a starting point for basic monitoring. 
    
    The real power comes when we create tailored dashboards. To start this, you can easily examine the underlying query and add it to your custom dashboards.

1.  Let's add a panel to a custom dashboard.

    Hover over the upper right-hand side of one of the dashboard visualizations. Then click on the **three dots** and select **Explore**.

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/8a0a5715-23f3-48d9-b371-85d71387bf0e/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1816,910&force_format=png&width=1120.0)

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2024-04-07/191763ae-a6ec-487b-accc-fdce8abc7f31/user_cropped_screenshot.jpeg?tl_px=154,51&br_px=1701,917&force_format=png&width=1120.0)

    You should now be able to see the underlying query that makes up this panel - this is PromQL! You could do the same thing with the Kubernetes dashboards we explored earlier.

    ![](./images/cw_lambda_query.png)

1.  Now, if we want to add this to a dashboard, we could do it straight from this screen by selecting **Add** -> **Add to dashboard**. Then, you choose which dashboard to which you want to add this query, and voila!

    ![](./images/cw_lambda_addtodash.png)


### Part 3: Unified Monitoring with Grafana - The First Pane of Glass
Grafana can query multiple data sources simultaneously. This means you can combine data from various AWS services (like CloudWatch, Elasticsearch, and AWS X-Ray) and other sources (like Prometheus or SQL databases) in a single dashboard for a unified view of your infrastructure and applications.

By providing a centralized and unified view of your AWS observability data, Grafana telemetry backends can help you improve your team's operational efficiency. By centralizing observability data and providing pre-built dashboards and alerts, Grafana telemetry backends can help you reduce the time it takes to troubleshoot problems in your AWS environment.

See [this blog](https://grafana.com/blog/2022/06/06/grafana-dashboards-a-complete-guide-to-all-the-different-types-you-can-build/) to learn more about the different types of dashboards you can build with Grafana.

## STOP HERE... END OF LAB 2

This is the end of the lab! Read on to find out how to set up the AWS integration in own environment with your own AWS account.

## Appendix: Setting up the AWS integration

**Note: The configuration of the AWS integration has been done for you already. You don't need to complete the following section. Read on to find out how to set up the CloudWatch integration in your own environment.**

AWS integration setup (for information only):

1.  Go to AWS &rarr; Configuration. 

1.  Create a new role in AWS. This can be done automatically via CloudFormation, or you can do so manually.

    ![Grafana Login Page](images/step1.png)

1.  Input the connection details to your AWS account. These will be outputs in the CloudFormation stack or you can find them in the AWS console.

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/57931064-947c-49d3-86d8-e3cbed6ba1b5/user_cropped_screenshot.jpeg?tl_px=25,0&br_px=1745,844&force_format=png&width=1120.0)

    *****Note:** *you can optionally include your AWS Resource Tags in the data sent to Grafana Cloud (e.g. env, application). Tags will appear as labels on the exported metric with a tag_ prefix. [Click here](https://docs.aws.amazon.com/tag-editor/latest/userguide/tagging.html) to learn more about the benefits of tagging your AWS resources if you do not already.*

1.  Next, you will configure which services you want to pull metrics from and what metrics/aggregates you want to ingest.

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/6141c887-229f-4bf1-9939-9aac04610277/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=2058,1182&force_format=png&width=1120.0)

1.  Once you have configured what metrics to scrape, press 'Create Scrape Job' and Grafana will begin pulling in your metrics... it's that easy!

    ![](https://ajeuwbhvhr.cloudimg.io/colony-recorder.s3.amazonaws.com/files/2023-11-15/01c09c6d-c047-46ea-b96b-338dee1f9653/user_cropped_screenshot.jpeg?tl_px=0,0&br_px=1368,555&force_format=png&width=1120.0)

    You can create any number of job configurations dictating which services, regions, and AWS accounts to collect metrics/logs from. 

### How it works

Keep in mind what you are configuring here is pulling in CloudWatch data into Grafana Mimir (Prometheus) and Grafana Loki (Prometheus but for logs) – allowing users to interact with and explore this data using the same query language, dashboards, alerts, etc. This provides a consistent way for end users to query their data and reduces the cost variability with querying/surfacing metrics and logs from the [CloudWatch API](https://aws.amazon.com/cloudwatch/pricing/) via the Plugin (lab 1).

Once the data is in the Grafana Cloud telemetry backend, there are no additional fees associated with querying, using dashboards, or running alerts against the data that’s been collected. The CloudWatch metrics integration installs prebuilt dashboards in your Grafana Cloud instance to help monitor your system. 

**That's it - thank you for participating!**

For more information about AWS observability in Grafana Cloud, visit: https://grafana.com/solutions/cloud-monitoring-aws/

