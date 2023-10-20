# Lab 2: Utilizing Cloudwatch Metrics, Logs, and Traces in Grafana Cloud
The goal of this lab is to guide you through visualizing and monitoring your systems using Grafana Metrics, Logs, and Traces using the Cloud Watch Integration and Grafana Agent in Grafana Cloud.

## Setting up the AWS CloudWatch integration
Let’s take a quick look at how the integration works with a step-by-step tour of the installation flow. 
1. In your Grafana Cloud stack, click Connections in the left-hand menu.
<img width="328" alt="Screenshot 2023-10-20 at 8 14 30 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/0a7a403c-0bfa-436f-aba7-b579e8f0e01a">
2. Find AWS and click its tile to open the AWS integration.
<img width="1416" alt="Screenshot 2023-10-20 at 8 14 50 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/3d2e4af3-d70f-403e-be60-31e08050e442">
3. Find CloudWatch metrics and click its tile to open the integration
<img width="1384" alt="Screenshot 2023-10-20 at 8 14 41 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/8500b7c2-2702-461e-9b0d-ec8dd99005f5">
4._ For the purpose of this lab, we have already done this step_. Click on the Configuration Details to begin the setup process. Within the integration, you can create any number of ‘jobs’ — configurations dictating which services, regions, and AWS accounts to collect from. This enables you to logically split your data into specific jobs and scrape any number of AWS accounts to better organize your data.

**Dashboards**
The CloudWatch metrics integration installs prebuilt dashboards in your Grafana Cloud instance to help monitor your system.
1. Click the VIEW DASHBOARDS button to see available dashboards.
   <img width="1405" alt="Screenshot 2023-10-20 at 8 08 24 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/1727f156-3e84-4cd4-876a-1528b8f9f331">
2. Choose a dashboard, lets start with EC2.
   <img width="1377" alt="Screenshot 2023-10-20 at 8 09 30 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/86f55394-0fbe-4c21-b786-9960658695f1">

This is what your EC2 dashboard should look like. 
<img width="1649" alt="Screenshot 2023-10-20 at 8 14 09 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/992fa689-b4bd-4839-9f4e-c28f440bd8ff">

3. Take one of the panels from your dashboard into explore mode by clicking the three dots in the upper right-hand corner.
<img width="858" alt="Screenshot 2023-10-20 at 8 22 50 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/6f20f34e-af48-4185-9c9b-d6862bf2b24a">
   
4. Now you can see the underlying query that makes up this panel - this is PromQL! 
<img width="1664" alt="Screenshot 2023-10-20 at 8 23 00 AM" src="https://github.com/grafana/AWS_Workshop/assets/104938954/c6ef9520-f5fd-44df-ac05-7c1e7bed8328">
