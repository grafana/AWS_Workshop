# Lab 2: Utilizing Grafana Metrics, Logs, and Traces in Grafana Cloud
The goal of this lab is to guide you through visualizing and monitoring your systems using Grafana Metrics, Logs, and Traces using the Cloud Watch Integration and Grafana Agent in Grafana Cloud.

## Options for sending telemetry data up
To understand whether the integration or plugin best fits your use case, it’s important to understand the different options for sending telemetry data. 
- The Cloudwatch plugin enables you to query metrics and logs straight from the CloudWatch API, but it does not store any data, so queries are always resolved by requesting from CloudWatch directly. 
- The Cloudwatch integration, on the other hand, leverages an open-source exporter to continually pull CloudWatch metrics and store them in a Prometheus format.
- The Grafana Agent deployed directly to ECS, EKS, EC2, etc., to pull metrics and logs and store them in a Prometheus format.


As the integration stores your metrics in Prometheus format, querying them is done using the powerful Prometheus query language (PromQL) , enabling you to run familiar expressions such as `aws_ec2_cpuutilization_maximum{region=“eu-west-2”, scrape_job=”myEC2Job”}`. 
The same can be done with metrics scraped via the Grafana Agent. 
In contrast, the plugin provides its own querying interface for filtering metrics and logs, so there is no need to write your own query expressions.


## Setting up the AWS CloudWatch integration
Let’s take a quick look at how the integration works with a step-by-step tour of the installation flow. 

Navigate to the Integrations Management view from the onboarding menu within Grafana Cloud and select the CloudWatch Metrics integration.

Within the integration, you can create any number of ‘jobs’ — sets of configuration dictating which services, regions, and AWS account to collect from. This enables you to logically split your data into specific jobs and scrape any number of AWS accounts to better organize your data.

As part of creating a job, Grafana needs to be granted access to the CloudWatch data available in your account. To do so, we leverage AWS Account delegation, which enables Grafana to assume a role that can access only your CloudWatch data without the need to share access and secret keys. (If you want to take a further look into how this works, check out this documentation.)

To set up the account delegation, you can leverage one of the provided infrastructure-as-code solutions: CloudFormation and Terraform. We recommend choosing the one that fits into your existing setup, as they not only automate the process but also enable you to keep track of the resources created. If, however, you do not wish to use CloudFormation or Terraform, simply choose ‘manually,’ which provides the details on how to configure account delegation manually.

Once done, test the connection to ensure everything is configured correctly!

Give the job a name and choose from the available services to begin scraping. All resources of the selected services that are tagged will automatically be scraped using tag auto discovery.

Finally, click Create to submit the scrape job. At this point Grafana will now begin importing your CloudWatch metrics into Prometheus!

## Dashboards
The AWS Integration ships with prebuilt dashboards for 6 Services, once you are set-up and you can access those dashboards by clicking on the view dashboards button. 
<screenshot>

Then click the dashboard you would like to see. 
<screenshot>

## Now, let's see how this works with the Grafana Agent
Next, we’ll use the Grafana Agent to collect and send metrics to our Grafana Cloud instance. 
The Grafana Agent is a telemetry collector that allows you to send metrics, logs, and traces to your Grafana stack with just one YAML file. 
There are a number of different ways to collect data from an ECS or EKS cluster with the Grafana Agent. 
When specifically collecting metrics from ECS, it can be deployed as a sidecar, in your ECS instance directly, or as an ECS task. 
Additionally, following the sidecar method, you can add the AWS Distribution for Open Telemetry sidecar container to your task definition in ECS.

Once your Grafana account is running, install the Grafana Agent in a sidecar on your Fargate cluster. 

Once you have configured your Grafana Agent YAML file, the agent will send metrics, logs, and traces to your Grafana stack, so you don’t have to worry about any additional steps or configuration choices.
We have pre-deployed a Grafana Agent with this configuration in this environment.

#Contextual layouts and visualizations
Import Dashboard <>. 

Choose the following Datasources:
- screenshot 
<Cloudwatch>
<Prometheus>

You will now see the metrics we are scraping from Lab2 plus Cloudwatch plugin data from Lab1. 
<screenshot>
When navigating through this dashboard, users can deep dive into specific ECS clusters that may be having issues and correlate that data across other serverless services such as Lambda. 
For example, you can see which pods in the cluster utilize the most CPU and if the linked Lambda function is impacted. 
Allowing you to get to the root cause of an issue quicker with a seamless, correlated view of the environment. 
You can deep dive into service-specific dashboards included as part of the AWS Cloudwatch Integration that we configured as part of Lab2. 


#summary
