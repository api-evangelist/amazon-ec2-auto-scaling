---
title: "Faster scaling with Amazon EC2 Auto Scaling Target Tracking"
url: "https://aws.amazon.com/blogs/compute/faster-scaling-with-amazon-ec2-auto-scaling-target-tracking/"
date: "Fri, 29 Nov 2024 18:45:09 +0000"
author: "aostan"
feed_url: "https://aws.amazon.com/blogs/compute/category/compute/auto-scaling/feed/"
---
<p><em>This post is written by Shahad Choudhury, Senior Cloud Support Engineer and Tiago Souza, Solutions Architect</em></p> 
<h2>Introduction</h2> 
<p>One of the key benefits of the AWS cloud is elasticity. It enables our users to provision and pay only for resources they need. To fully use the elasticity benefits, users needed a mechanism that is automated and can be widely operated with ease. <a href="https://aws.amazon.com/ec2/autoscaling/" rel="noopener" target="_blank">Amazon EC2 Auto Scaling</a> solves these challenges by helping our users automatically scale the number of <a href="https://aws.amazon.com/ec2/" rel="noopener" target="_blank">Amazon Elastic Compute Cloud (Amazon EC2)</a> instances to meet the changing workload demands, and it offers a wide suite of capabilities to manage the instance’s lifecycle.</p> 
<p>To scale their Auto Scaling groups (ASG), users need to create scaling policies. Scaling policies provide ASGs with guidelines for adjusting Amazon EC2 capacity to match the workload demand. There are different types of scaling policies, with each having a different approach to manage capacity. One type of policy is <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html" rel="noopener" target="_blank">Target Tracking</a>, which offers a simpler yet effective way to scale automatically. To use it, users need to define a <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html#target-tracking-choose-metrics" rel="noopener" target="_blank">utilization metric</a> and set a target value to maintain. For example, setting a 60% Average CPU Utilization policy causes the ASG to keep the metric as close to that value as possible across its fleet of EC2 instances.</p> 
<p>In this post, we describe the recently <a href="https://aws.amazon.com/about-aws/whats-new/2024/11/amazon-ec2-auto-scaling-highly-responsive-scaling-policies/" rel="noopener" target="_blank">released updates</a> to Target Tracking. We also walk through the steps to create a Target Tracking policy that uses the new feature, and highlight the improvements and benefits users can expect from this new feature.</p> 
<h2>What’s new with Target Tracking policy</h2> 
<p>As users modernized their applications, we learned from them that a dynamic Auto Scaling solution must expand beyond our original implementation of the Target Tracking policy.</p> 
<p>First, users found that the few minutes Target Tracking took to respond to a demand spike could lead to short-term performance degradation. We’ve seen many users mitigate this challenge by buffering their running capacity, leading to increased costs. Second, different workloads have different scaling requirements. This leads to users having to create tailored scaling policies for each workload, which is a time consuming, error prone, and operationally expensive activity for performance and cost optimizations.</p> 
<p>To address these user challenges, we released an intelligent and highly responsive Target Tracking scaling policy. Target Tracking now automatically tunes its responsiveness to the unique usage patterns of individual applications and closely monitors application demand for faster scaling decisions. Automatic tuning allows users to enhance their application performance and maintain high usage for their Amazon EC2 resources to save costs without having to create tailored scaling policies for each workload. Users must specify a target utilization they want to maintain, and Target Tracking scales without any further input needed from users.</p> 
<p>For faster auto scaling decisions, users can configure Target Tracking policies using high-resolution metrics in <a href="https://aws.amazon.com/pm/cloudwatch/" rel="noopener" target="_blank">Amazon CloudWatch</a>. This fine-grained monitoring allows Target Tracking to detect and respond to changing demand, not in minutes, but in seconds. This capability is ideal for applications that have volatile demand patterns, such as client-serving APIs, live streaming services, e-commerce websites, and on-demand data processing.</p> 
<h2>Getting started with the new Target Tracking policy</h2> 
<p>If you’re already using Target Tracking policies, then no action is necessary for you to upgrade to Target Tracking that automatically tunes itself. Target Tracking policies regularly analyze targeted metric history and determine the appropriate level of sensitivity to initiate scale-outs and scale-ins. Furthermore, it determines the amount of capacity that must be added or removed to optimize both availability and lower cost. These decisions depend on the unique characteristics of the application’s demand patterns, such as the range and frequency of demand changes, and whether spikes in usage are long or short-lived. Target Tracking continues to learn on an ongoing basis, and reevaluates itself to automatically adapt for your specific application and demand patterns.</p> 
<h2>Enabling faster scaling response from Target Tracking</h2> 
<p>Moreover, to enable the fastest response from Target Tracking policies, users can track metrics published at sub-minute granularity to CloudWatch (also known as high-resolution CloudWatch metrics). Users can update an existing Target Tracking policy or create a new one with a high-resolution metric as part of a CustomizedMetricSpecification. Users must describe the same metric namespace, metric name, and any dimension(s) and/or unit created when publishing the metric to CloudWatch. They must also define the metric period to indicate the metric granularity at which target tracking should evaluate the metric. The following steps walk you through how to get started on the <a href="https://aws.amazon.com/console/" rel="noopener" target="_blank">AWS Management Console</a> for ASG:</p> 
<p><u>Step 1: Choose the ASG</u></p> 
<p>In the console, choose the name of the ASG. This takes you to the <strong>Details</strong> page, as shown in the following figure.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/1-7.png"><img alt="List of ASGs in the Amazon EC2 console Auto Scaling section" class="aligncenter wp-image-23180 size-full" height="403" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/1-7.png" width="1488" /></a></p> 
<p style="text-align: center;"><em>Figure 1: In the Amazon EC2 console, choose the ASG that you want to scale</em></p> 
<p>Choose the <strong>Automatic scaling</strong> tab that gives you the option to <strong>Create a dynamic scaling policy, </strong>as shown in the following figure.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/2-7.png"><img alt="Step 2: Create dynamic scaling policy" class="size-full wp-image-23179 aligncenter" height="629" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/2-7.png" width="1215" /></a></p> 
<p><u>Step 2: Create dynamic scaling policy</u></p> 
<p>Choose the target tracking policy as the policy type. For <strong>Metric Type</strong>, choose <strong>Custom CloudWatch metric</strong>. This shows a prefilled JSON snippet that you can edit to specify the metric name, namespace, and dimensions of the metric that you want to scale using the Target Tracking policy that you used to publish the CloudWatch metric, as shown in the following figure.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/3-4.png"><img alt="Figure 3: Updated CustomizedMetricSpecification section added to the Auto Scaling Console" class="size-full wp-image-23178 aligncenter" height="798" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/3-4.png" width="870" /></a></p> 
<p style="text-align: center;"><em>Figure 3: Updated CustomizedMetricSpecification section added to the Auto Scaling Console</em></p> 
<p>The minimum Period supported is ten seconds. To use the ten second metric periods, your metric should be published at a ten second or higher resolution, for example at one second. However, publishing at one second intervals can substantially increase your CloudWatch cost. We discuss the cost considerations later in this post. Auto Scaling imposes a limit of 60 seconds to make sure that Target Tracking can observe and respond to usage spikes quickly.</p> 
<p>These two steps allow you to enable target tracking to scale on a high resolution metric.</p> 
<p><u>Enabling faster scaling impact:</u></p> 
<p>The preceding steps allow the ASG to detect changes in your utilization faster, thus it can add more instances when demand spikes.</p> 
<p>In the following diagram, we see the results of running identical load tests against an environment with a default target tracking policy of a 60 second period and a target tracking policy configured with a ten second period. Each policy has a target value of 60% CPU Utilization. The load test ramps up to 20 threads over three minutes each sending http requests to simulate a spike in demand. We can see that, in the 60 second period case (the left diagram) there were three minutes where the application was above the CPU Utilization target of 60% (blue line). The capacity (green line) increased only after the system had reached a peak of 100% CPU Utilization. This may lead to application performance issues and, to avoid that, users would have to aim for lower utilization level so that more capacity can be provisioned, which would increase their cost. However, with the ten second periods (the right diagram), scaling happened rapidly to avoid application impact. The capacity increased after one minute, during which CPU Utilization remained closer to 60% and didn’t hit the peak 100% level. This allows users to reach a higher utilization level, saving the cost without impacting the application performance.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/4-6.png"><img alt="Two graphs of CPU Utilization and Auto Scaling InService Capacity comparing scaling based on 60 seconds period as opposed to 10 seconds period. In the first case, the CPU approaches 100% for multiple minutes before scaling occurs to bring CPU down. Comparatively, when scaling with a 10 second period, the CPU increases over two minutes but remained closer to the 60% target throughout. " class="size-full wp-image-23177 aligncenter" height="404" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/4-6.png" width="1344" /></a></p> 
<p style="text-align: center;"><em>Figure 4: Target tracking policy with 60 second periods as opposed to 10 seconds</em></p> 
<h2>Considerations</h2> 
<p>Before applying high resolution custom metrics, we recommend that you consider the following factors as they may impact your costs.</p> 
<p><strong>Metric types</strong>: Target Tracking assumes that metrics change proportionally to the number of instances in the ASG. Selecting the right metric is key for successful Target Tracking policies. Refer to <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html#target-tracking-considerations" rel="noopener" target="_blank">the Target Tracking public documentation</a> for more details.</p> 
<p><strong>Pricing: </strong>There is no further charge for EC2 Auto Scaling, including these new features. Users pay only for the AWS resources needed to run their applications and CloudWatch monitoring fees. However, you must understand the three CloudWatch billing items relevant to these features:</p> 
<p>1) High-resolution alarms</p> 
<p>2) API calls</p> 
<p>3) Custom metrics</p> 
<p>Target Tracking creates at least two alarms, one each to track high and low usage with a buffer in between their thresholds to reduce oscillation. If the metric period is less than sixty seconds, these alarms are billed as high-resolution alarms. As of this writing, the price for high-resolution alarm for the AWS US East (Ohio) <a href="https://aws.amazon.com/about-aws/global-infrastructure/regions_az/" rel="noopener" target="_blank">Region</a> is $0.30 per alarm metric as compared to $0.10 per alarm metric for standard resolution alarms.</p> 
<p>If you’re using CloudWatch Agent, it sends API calls from each instance based on the metrics_collection_interval setting in the CloudWatch Agent config. Each instance sends an API call once per interval to CloudWatch. In CloudWatch, a metric is defined as a unique combination of a Namespace, MetricName, Dimension(s) (optional), and Unit (optional). Every unique combination of dimensions pushed from the CloudWatch Agent is billed as its custom metric.</p> 
<p>The following is an example of expected monthly charges in USD using us-east-2 for an account that has passed the free tier, but is still in the first tier of paid usage (the price reduction for bulk usage). This example assumes an average of ten instances running over the month in an ASG with one target tracking policy where metrics and alarms are configured for ten second intervals.</p> 
<p>1) High-resolution alarms:</p> 
<p>2 alarms @ $0.30 each = $0.60/month</p> 
<p>2) API calls:</p> 
<p>10 instances * 30 days * 24 hours * 3600 seconds / 10 second_intervals = 2.592 million API calls</p> 
<p>2.592 million API calls * $0.01 per 1,000 requests = $25.92/month</p> 
<p>3) Custom metrics:</p> 
<p>1 ASG aggregate metric @ $0.30/month = $0.30/month</p> 
<p>Total estimate: $26.82/month for a 10 instance ASG</p> 
<p>Multiple metrics can be pushed in a single PutMetricData API call. If you decide to configure the CloudWatch Agent to publish more than the single aggregate AutoScalingGroupName metric, then the API charges stay the same until the PutMetricData size limit is hit, and only the Custom metrics charge increases.</p> 
<p>For example, if the ASG is running c8g.xlarge instances, then by running one fewer instance due to the higher utilization unlocked by these features, then the monthly cost saving in us-east-2 would be:</p> 
<p>1 c8g.xlarge @ $0.15896/hour * 30 days * 24 hours = $114.45/month</p> 
<p>Taking away the $26.82/month in estimated CloudWatch costs means a savings of $87.63/month per ASG. This is nearly 8% saving on the EC2 cost in this example.</p> 
<h2>Template to publish metrics and updating your scaling policies</h2> 
<p>To help you start publishing high resolution metrics, we have created a sample <a href="https://aws.amazon.com/cloudformation/" rel="noopener" target="_blank">AWS CloudFormation </a>template. The template provides the scaffolding to demonstrate the new faster scaling period for an existing ASG. It includes installing a CloudWatch agent and publishing the CPU Utilization of the ASG instances to CloudWatch at high resolution. The template also includes a Target Tracking policy, as described in this post.</p> 
<p>Instructions on deployment and customization requirements can be found in the <a href="https://github.com/aws-samples/amazon-ec2-auto-scaling-group-examples/tree/main/features/faster-target-tracking" rel="noopener" target="_blank">AWS Samples Repo for Faster Target Tracking</a>. However, there are a few code snippets in the template that we want to highlight.</p> 
<p>First, to install the <a href="https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html" rel="noopener" target="_blank">CloudWatch agent</a>, the template updates the UserData of the Launch Template used with the ASG.</p> 
<div class="hide-language"> 
 <pre><code class="lang-ts">UserData: 
          Fn::Base64: 
            !Sub |
              #!/bin/bash
              yum install amazon-cloudwatch-agent -y
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/cw-agent-asg-aggregate-cpu -s</code></pre> 
</div> 
<p>This command refers to an <a href="https://aws.amazon.com/systems-manager/" rel="noopener" target="_blank">AWS Systems Manager</a> parameter holding the Cloudwatch Agent configuration.</p> 
<p>The following snippet of the Systems Manager parameter reports the CPU Utilization metric at a 10 second interval to a custom namespace called FasterScalingDemo. The metric is also aggregated with the name of the ASG as a dimension so that you can easily refer to it in CloudWatch.</p> 
<div class="hide-language"> 
 <pre><code class="lang-ts">CloudWatchMetricsSSMParameter:
    Type: AWS::SSM::Parameter
    Properties:
      Name: cw-agent-asg-aggregate-cpu
      Type: String
      Value: '{"agent":{"metrics_collection_interval":10,"run_as_user":"cwagent"},"metrics":{"force_flush_interval":10,"aggregation_dimensions":[["AutoScalingGroupName"]],"append_dimensions":{"AutoScalingGroupName":"${aws:AutoScalingGroupName}"},"namespace":"FasterScalingDemo","metrics_collected":{"cpu":{"drop_original_metrics":["cpu_usage_active"],"measurement":[{"name":"cpu_usage_active","rename":"CPUUtilization"}]}}}}'
      Tier: Intelligent-Tiering
      Description: Custom metric specification for CloudWatch Agent</code></pre> 
</div> 
<p>Second, the template also includes an updated <a href="https://aws.amazon.com/iam/" rel="noopener" target="_blank">AWS Identity and Access Management (IAM)</a> Role and corresponding IAM Instance Profile with permissions to PutMetricData to CloudWatch, and to retrieve Systems Manager parameters that we created previously to configure the agent.</p> 
<div class="hide-language"> 
 <pre><code class="lang-ts">IAMInstanceRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - ec2.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      Policies:
        - PolicyName: FasterScalingDemo
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              - Effect: Allow
                Action:
                  - cloudwatch:PutMetricData
                  - ec2:DescribeTags
                  - ssm:GetParameter
                Resource: '*'
      Tags:
        - Key: Name
          Value: !Sub ${EnvironmentName}_IAMROLE</code></pre> 
</div> 
<p>Finally, the following image depicts the architecture deployed by the CloudFormation template.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/5-4.png"><img alt="Amazon VPC with an ASG launching instances in three Availability Zones, publishing high-resolution metrics to CloudWatch" class="size-full wp-image-23176 aligncenter" height="500" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2024/11/28/5-4.png" width="805" /></a></p> 
<p style="text-align: center;"><em>Figure 4: AWS resources created in the CloudFormation example</em></p> 
<p>When the template is deployed with your chosen ASG, you should be ready to test Target Tracking set with high resolution metrics. You can perform a load test to see Target Tracking in action. The closer the load test mimics your application usage pattern, the more conclusive the test would be in determining the benefits of these features.</p> 
<h2>Conclusion</h2> 
<p>This post provides an overview of the updates we have made to the Target Tracking policy that deliver higher precision in matching your demand with Amazon EC2 capacity. Specifically, this post demonstrated the value of using high resolution CloudWatch metrics with Target Tracking to increase the Auto Scaling rate to match demand, improve availability, and open possibilities for better resource utilization. We encourage you to test the feature and apply the consideration factors outlined in this post before opting for high-resolution metric scaling. You can find more details about these new features <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scaling-target-tracking.html" rel="noopener" target="_blank">in the Target Tracking documentation</a>.</p>
