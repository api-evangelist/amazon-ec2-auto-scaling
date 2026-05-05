---
title: "Building diversified and cost-optimized EC2 server groups in Spinnaker"
url: "https://aws.amazon.com/blogs/compute/building-diversified-and-cost-optimized-ec2-server-groups-in-spinnaker/"
date: "Fri, 24 Mar 2023 16:01:56 +0000"
author: "Sheila Busser"
feed_url: "https://aws.amazon.com/blogs/compute/category/compute/auto-scaling/feed/"
---
<p><em>This blog post is written by Sandeep Palavalasa, Sr. Specialist Containers SA, and Prathibha Datta-Kumar, Software Development Engineer</em></p> 
<p><a href="https://www.spinnaker.io/">Spinnaker</a> is an open source continuous delivery platform created by Netflix for releasing software changes rapidly and reliably. It enables teams to automate deployments into pipelines that are run whenever a new version is released with proven <a href="https://spinnaker.io/docs/concepts/#deployment-strategies">deployment strategies</a> that are faster and more dependable with zero downtime. For many AWS customers, Spinnaker is a critical piece of technology that allows developers to deploy their applications safely and reliably across different AWS managed services.</p> 
<p>Listening to customer requests on the Spinnaker <a href="https://github.com/spinnaker/spinnaker/issues/2866">open source project </a>and in the Amazon <a href="https://github.com/aws/ec2-spot-instances-integrations-roadmap/issues/11">EC2 Spot Instances integrations roadmap</a>, we have further enhanced Spinnaker’s ability to deploy on <a href="https://spinnaker.io/setup/install/providers/aws/aws-ec2/">Amazon Elastic Compute Cloud (Amazon EC2)</a>. The enhancements make it easier to combine Spot Instances with <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-on-demand-instances.html">On-Demand</a>, <a href="https://aws.amazon.com/ec2/pricing/reserved-instances/">Reserved</a>, and <a href="https://aws.amazon.com/savingsplans/">Savings Plans</a> Instances to optimize workload costs with performance. You can improve workload availability when using Spot Instances with features such as <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-fleet-allocation-strategy.html">allocation strategies</a> and proactive <a href="https://aws.amazon.com/blogs/compute/proactively-manage-spot-instance-lifecycle-using-the-new-capacity-rebalancing-feature-for-ec2-auto-scaling/">Spot capacity rebalancing</a>, when you are flexible about Instance types and Availability Zones. Combinations of these features offer the best possible experience when using Amazon EC2 with Spinnaker.</p> 
<p>In this post, we detail the recent enhancements, along with a walkthrough of how you can use them following the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html">best practices</a>.</p> 
<h2>Amazon EC2 Spot Instances</h2> 
<p><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html#spot-features">EC2 Spot Instances</a> are spare compute capacity in the AWS Cloud available at steep discounts of up to 90% when compared to <a href="https://aws.amazon.com/ec2/pricing/on-demand/">On-Demand Instance prices</a>. The primary difference between an On-Demand Instance and a Spot Instance is that a Spot Instance can be <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-interruptions.html">interrupted</a> by Amazon EC2 with a two-minute notification when Amazon EC2 needs the capacity back. Amazon EC2 now sends <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/rebalance-recommendations.html">rebalance recommendation notifications</a> when Spot Instances are at an elevated risk of interruption. This signal can arrive sooner than the two-minute interruption notice. This lets you proactively replace your Spot Instances before it’s interrupted.</p> 
<p>The best way to adhere to Spot <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html">best practices</a> and instance fleet management is by using an <a href="https://aws.amazon.com/ec2/autoscaling/">Amazon EC2 Auto Scaling group</a> When using Spot Instances in Auto Scaling group, enabling <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-capacity-rebalancing.html"><strong>Capacity Rebalancing</strong></a> helps you maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running instance is interrupted by Amazon EC2.</p> 
<h2>Spinnaker concepts</h2> 
<p>Spinnaker uses three key <a href="https://spinnaker.io/docs/concepts/">concepts</a> to describe your services, including <strong>applications</strong>, <strong>clusters</strong>, and <strong>server groups</strong>, and how your services are exposed to users is expressed as Load balancers and firewalls.</p> 
<p>An application is a collection of clusters, a cluster is a collection of server groups, and a server group identifies the deployable artifact and basic configuration settings such as the number of instances, autoscaling policies, metadata, etc. This corresponds to an <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html">Auto Scaling group</a> in AWS. We use Auto Scaling groups and server groups interchangeably in this post.</p> 
<h2><strong>Spinnaker and Amazon EC2 Integration</strong></h2> 
<p>In mid-2020, we started looking into customer requests and gaps in the Amazon EC2 feature set supported in Spinnaker. Around the same time, Spinnaker OSS added support for <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html">Amazon EC2 Launch Templates</a>. Thanks to their effort, we could follow-up and expand the Amazon EC2 feature set supported in Spinnaker. Now that we understand the new features, let’s look at how to use some of them in the following tutorial <a href="https://spinnaker.io/docs/setup/other_config/server-group-launch-settings/aws-ec2/">spinnaker.io</a>.</p> 
<p>Here are some highlights of the features contributed recently:</p> 
<table border=".15" style="height: 387px;" width="802"> 
 <tbody> 
  <tr> 
   <td style="text-align: center;" width="35%"><strong><em>Feature</em></strong></td> 
   <td style="text-align: center;" width="64%"><strong><em>Why use it? (Example use cases)</em></strong></td> 
  </tr> 
  <tr> 
   <td width="35%"><em>&nbsp; Multiple Instance Types</em></td> 
   <td style="background-color: #c9c3c3; width: 64%;" width="64%">&nbsp; Tap into multiple capacity pools to achieve and maintain the desired scale using Spot Instances.</td> 
  </tr> 
  <tr> 
   <td style="text-align: left;" width="35%"><em>&nbsp; Combining On-Demand and Spot Instances</em></td> 
   <td width="64%"> <p>&nbsp; – Control the proportion of On-Demand and Spot Instances launched in your sever group.</p> <p>– Combine Spot Instances with Amazon EC2 Reserved Instances or Savings Plans.</p></td> 
  </tr> 
  <tr> 
   <td width="35%"><em>&nbsp; Amazon EC2 Auto Scaling allocation strategies</em></td> 
   <td style="background-color: #c9c3c3; width: 64%;" width="64%">&nbsp; Reduce overall Spot interruptions by launching from Spot pools that are optimally chosen based on the available Spot capacity, using capacity-optimized Spot allocation strategy.</td> 
  </tr> 
  <tr> 
   <td width="35%"><em>&nbsp; Capacity rebalancing</em></td> 
   <td width="64%">&nbsp; Improve your workload availability by proactively shifting your Spot capacity to optimal pools by enabling capacity rebalancing along with capacity-optimized allocation strategy.</td> 
  </tr> 
  <tr> 
   <td width="35%"><em>&nbsp; Improved support for </em><em><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html">burstable performance instance types</a></em><em> with custom credit specification</em></td> 
   <td style="background-color: #c9c3c3; width: 64%;" width="64%">&nbsp; Reduce costs by preventing wastage of CPU cycles.</td> 
  </tr> 
 </tbody> 
</table> 
<p>We recommend using Spinnaker stable release 1.28.x for API users and 1.29.x for UI users. Here is the <a href="https://github.com/spinnaker/spinnaker/issues/5989">Git issue</a> for related PRs and feature releases.</p> 
<p>Now that we understand the new features, let’s look at how to use some of them in the following tutorial.</p> 
<h2><strong>Example tutorial: Deploy a demo web application on an Auto Scaling group with On-Demand and Spot Instances</strong></h2> 
<p>In this example tutorial, we setup Spinnaker to deploy to Amazon EC2, create an <a href="https://aws.amazon.com/elasticloadbalancing/application-load-balancer/">Application Load Balancer</a>, and deploy a demo application on a server group diversified across multiple instance types and purchase options – this case On-Demand and Spot Instances.</p> 
<p>We leverage Spinnaker’s API throughout the tutorial to create new resources, along with a quick guide on how to deploy the same using Spinnaker UI (Deck) and leverage UI to view them.</p> 
<h3>Prerequisites</h3> 
<p>As a prerequisite to complete this tutorial, you must have an <a href="https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/">AWS Account</a> with an <a href="https://aws.amazon.com/iam/">AWS Identity and Access Management (IAM)</a> User that has the AdministratorAccess configured to use with <a href="https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html">AWS Command Line Interface (AWS CLI</a>).</p> 
<p><strong>1. Spinnaker setup</strong></p> 
<p>We will use the <a href="https://aws.amazon.com/cloudformation/">AWS CloudFormation</a> template&nbsp;<a href="https://raw.githubusercontent.com/awslabs/ec2-spot-labs/master/ec2-spot-spinnaker/setup-spinnaker-with-deployment-vpc.yml">setup-spinnaker-with-deployment-vpc.yml</a> to setup Spinnaker and the <a href="https://github.com/awslabs/ec2-spot-labs/tree/master/ec2-spot-spinnaker#spinnaker-setup">required resources</a>.</p> 
<p style="padding-left: 40px;">1.1 Create an Secure Shell(SSH) keypair used to connect to Spinnaker and EC2 instances launched by Spinnaker.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">AWS_REGION=us-west-2 # Change the region where you want Spinnaker deployed
EC2_KEYPAIR_NAME=spinnaker-blog-${AWS_REGION}
aws ec2 create-key-pair --key-name ${EC2_KEYPAIR_NAME} --region ${AWS_REGION} --query KeyMaterial --output text &gt; ~/${EC2_KEYPAIR_NAME}.pem
chmod 600 ~/${EC2_KEYPAIR_NAME}.pem
</code></pre> 
<p style="padding-left: 40px;">1.2 Deploy the Cloudformation stack.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">STACK_NAME=spinnaker-blog
SPINNAKER_VERSION=1.29.1 # Change the version if newer versions are available
NUMBER_OF_AZS=3
AVAILABILITY_ZONES=${AWS_REGION}a,${AWS_REGION}b,${AWS_REGION}c
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
S3_BUCKET_NAME=spin-persitent-store-${ACCOUNT_ID}

# Download template
curl -o setup-spinnaker-with-deployment-vpc.yml https://raw.githubusercontent.com/awslabs/ec2-spot-labs/master/ec2-spot-spinnaker/setup-spinnaker-with-deployment-vpc.yml

# deploy stack
aws cloudformation deploy --template-file setup-spinnaker-with-deployment-vpc.yml \
    --stack-name ${STACK_NAME} \
    --parameter-overrides NumberOfAZs=${NUMBER_OF_AZS} \
    AvailabilityZones=${AVAILABILITY_ZONES} \
    EC2KeyPairName=${EC2_KEYPAIR_NAME} \
    SpinnakerVersion=${SPINNAKER_VERSION} \
    SpinnakerS3BucketName=${S3_BUCKET_NAME} \
    --capabilities CAPABILITY_NAMED_IAM --region ${AWS_REGION}
</code></pre> 
<p style="padding-left: 40px;">1.3 Connecting to Spinnaker</p> 
<p style="padding-left: 40px;">1.3.1 Get the SSH command to port forwarding for Deck – the browser-based UI (9000) and Gate – the API Gateway (8084) to access the Spinnaker UI and API.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">SPINNAKER_INSTANCE_DNS_NAME=$(aws cloudformation describe-stacks --stack-name ${STACK_NAME} --region ${AWS_REGION} --query "Stacks[].Outputs[?OutputKey=='SpinnakerInstance'].OutputValue" --output text)
echo 'ssh -A -L 9000:localhost:9000 -L 8084:localhost:8084 -L 8087:localhost:8087 -i ~/'${EC2_KEYPAIR_NAME}' ubuntu@$'{SPINNAKER_INSTANCE_DNS_NAME}''
</code></pre> 
<p style="padding-left: 40px;">1.3.2 Open a new terminal and use the SSH command (output from the previous command) to connect to the Spinnaker instance. After you successfully connect to the Spinnaker instance via SSH, access the Spinnaker UI <a href="http://localhost:9000/">here</a> and API <a href="http://localhost:8084/">here</a>.</p> 
<p><strong>2. Deploy a demo web application</strong></p> 
<p style="padding-left: 40px;">Let’s make sure that we have the environment variables required in the shell before proceeding. If you’re using the same terminal window as before, then you might already have these variables.</p> 
<pre style="padding-left: 40px;">STACK_NAME=spinnaker-blog
AWS_REGION=us-west-2 # use the same region as before
EC2_KEYPAIR_NAME=spinnaker-blog-${AWS_REGION}
VPC_ID=$(aws cloudformation describe-stacks --stack-name ${STACK_NAME} --region ${AWS_REGION} --query "Stacks[].Outputs[?OutputKey=='VPCID'].OutputValue" --output text)</pre> 
<p style="padding-left: 40px;">2.1 Create a Spinnaker Application</p> 
<p style="padding-left: 40px;">We start by creating an application in Spinnaker, a placeholder for the service that we deploy.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">curl 'http://localhost:8084/tasks' \
-H 'Content-Type: application/json;charset=utf-8' \
--data-raw \
'{
   "job":[
      {
         "type":"createApplication",
         "application":{
            "cloudProviders":"aws",
            "instancePort":80,
            "name":"demoapp",
            "email":"test@test.com",
            "providerSettings":{
               "aws":{
                  "useAmiBlockDeviceMappings":true
               }
            }
         }
      }
   ],
   "application":"demoapp",
   "description":"Create Application: demoapp"
}'
</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/23/Spin_Create_ServerGroup.gif"><img alt="Spin Create Server Group" class="aligncenter wp-image-20180 size-full" height="724" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/23/Spin_Create_ServerGroup.gif" width="1292" /></a></p> 
<p style="text-align: left; padding-left: 40px;">2.2 Create an Application Load Balancer</p> 
<p style="padding-left: 40px;">Let’s create an Application Load Balanacer and a target group for port 80, spanning the three availability zones in our public subnet. We use the <strong>Demo-ALB-SecurityGroup</strong> for Firewalls to allow public access to the ALB on port 80.</p> 
<p style="padding-left: 40px;">As Spot Instances are interrupted with a two minute warning, you must adjust the Target Group’s <a href="https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html#deregistration-delay">deregistration delay</a> to a slightly lower time. Recommended values are <strong>90 seconds </strong>or less. This allows time for in-flight requests to complete and gracefully close existing connections before the instance is interrupted.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">curl 'http://localhost:8084/tasks' \
-H 'Content-Type: application/json;charset=utf-8' \
--data-binary \
'{
   "application":"demoapp",
   "description":"Create Load Balancer: demoapp",
   "job":[
      {
         "type":"upsertLoadBalancer",
         "name":"demoapp-lb",
         "loadBalancerType":"application",
         "cloudProvider":"aws",
         "credentials":"my-aws-account",
         "region":"'"${AWS_REGION}"'",
         "vpcId":"'"${VPC_ID}"'",
         "subnetType":"public-subnet",
         "idleTimeout":60,
         "targetGroups":[
            {
               "name":"demoapp-targetgroup",
               "protocol":"HTTP",
               "port":80,
               "targetType":"instance",
               "healthCheckProtocol":"HTTP",
               "healthCheckPort":"traffic-port",
               "healthCheckPath":"/",
               "attributes":{
                  "deregistrationDelay":90
               }
            }
         ],
         "regionZones":[
            "'"${AWS_REGION}"'a",
            "'"${AWS_REGION}"'b",
            "'"${AWS_REGION}"'c"
         ],
         "securityGroups":[
            "Demo-ALB-SecurityGroup"
         ],
         "listeners":[
            {
               "protocol":"HTTP",
               "port":80,
               "defaultActions":[
                  {
                     "type":"forward",
                     "targetGroupName":"demoapp-targetgroup"
                 }
               ]
            }
         ]
      }
   ]
}'
</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/16/Spin_Create_ALB.gif"><img alt="Spin Create ALB" class="aligncenter wp-image-20159 size-full" height="724" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/16/Spin_Create_ALB.gif" width="1294" /></a></p> 
<p style="padding-left: 40px;">2.3 Create a server group</p> 
<p style="padding-left: 40px;">Before creating a server group (Auto Scaling group), here is a brief overview of the <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html">features</a> used in the example:</p> 
<ul> 
 <li> 
  <ul> 
   <li> 
    <ul> 
     <li><strong>onDemandBaseCapacity</strong><em> (default 0):</em> The minimum amount of your ASG’s capacity that must be fulfilled by On-Demand instances (can also be applied toward Reserved Instances or Savings Plans). The example uses an <em>onDemandBaseCapacity</em> of three.</li> 
     <li><strong>onDemandPercentageAboveBaseCapacity </strong>(<em>default 100)</em>: The percentages of On-Demand and Spot Instances for additional capacity beyond&nbsp;<em>OnDemandBaseCapacity</em>. The example uses <em>onDemandPercentageAboveBaseCapacity</em> of 10% (i.e. 90% Spot).</li> 
     <li><strong>spotAllocationStrategy</strong>: This indicates how you want to allocate instances across Spot Instance pools in each Availability Zone. The example uses the recommended <strong>Capacity Optimized</strong> strategy. Instances are launched from optimal Spot pools that are chosen based on the available Spot capacity for the number of instances that are launching.</li> 
     <li><strong>launchTemplateOverridesForInstanceType</strong>: The list of instance types that are acceptable for your workload. Specifying multiple instance types enables tapping into multiple instance pools in multiple Availability Zones, designed to enhance your service’s availability. You can use the <a href="https://github.com/aws/amazon-ec2-instance-selector"><em>ec2-instance-selector</em></a>, an open source AWS Command Line Interface(CLI) tool to narrow down the instance types based on resource criteria like vcpus and memory.</li> 
    </ul> </li> 
  </ul> </li> 
</ul> 
<ul> 
 <li> 
  <ul> 
   <li> 
    <ul> 
     <li><strong>capacityRebalance</strong>: When enabled, this feature proactively manages the EC2 Spot Instance lifecycle leveraging the new <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/rebalance-recommendations.html">EC2 Instance rebalance recommendation</a>. This increases the emphasis on availability by automatically attempting to replace Spot Instances in an ASG before they are interrupted by Amazon EC2. We enable this feature in this example.</li> 
    </ul> </li> 
  </ul> </li> 
</ul> 
<p style="padding-left: 40px;">Learn more on <a href="https://spinnaker.io/docs/setup/other_config/server-group-launch-settings/aws-ec2/">spinnaker.io</a>: <a href="https://spinnaker.io/docs/setup/other_config/server-group-launch-settings/aws-ec2/launch-templates/#feature-configuration">feature descriptions</a> and <a href="https://spinnaker.io/docs/setup/other_config/server-group-launch-settings/aws-ec2/launch-templates/#use-cases--sample-api-requests">use cases and sample API requests.</a></p> 
<p style="padding-left: 40px;">Let’s create a server group with a desired capacity of 12 instances diversified across current and previous generation instance types, attach the previously created ALB, use <strong>Demo-EC2-SecurityGroup</strong> for the Firewalls which allows http traffic only from the ALB, use the following bash script for <strong>UserData</strong> to install httpd, and add instance metadata into the index.html.</p> 
<p style="padding-left: 40px;">2.3.1 Save the userdata bash script into a file <em>user-date.sh</em>.</p> 
<p style="padding-left: 40px;">Note that Spinnaker only support base64 encoded userdata. We use <em>base64</em> bash command to encode the file contents in the next step.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">cat &lt;&lt; "EOF" &gt; user-data.sh
#!/bin/bash
yum update -y
yum install httpd -y
echo "&lt;html&gt;
    &lt;head&gt;
        &lt;title&gt;Demo Application&lt;/title&gt;
        &lt;style&gt;body {margin-top: 40px; background-color: #Gray;} &lt;/style&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;h2&gt;You have reached a Demo Application running on&lt;/h2&gt;
        &lt;ul&gt;
            &lt;li&gt;instance-id: &lt;b&gt; `curl http://169.254.169.254/latest/meta-data/instance-id` &lt;/b&gt;&lt;/li&gt;
            &lt;li&gt;instance-type: &lt;b&gt; `curl http://169.254.169.254/latest/meta-data/instance-type` &lt;/b&gt;&lt;/li&gt;
            &lt;li&gt;instance-life-cycle: &lt;b&gt; `curl http://169.254.169.254/latest/meta-data/instance-life-cycle` &lt;/b&gt;&lt;/li&gt;
            &lt;li&gt;availability-zone: &lt;b&gt; `curl http://169.254.169.254/latest/meta-data/placement/availability-zone` &lt;/b&gt;&lt;/li&gt;
        &lt;/ul&gt;
    &lt;/body&gt;
&lt;/html&gt;" &gt; /var/www/html/index.html
systemctl start httpd
systemctl enable httpd
EOF</code></pre> 
<p style="padding-left: 40px;">2.3.2 Create the server group by running the following command. Note we use the KeyPairName that we created as part of the prerequisites.</p> 
<pre style="padding-left: 40px;"><code class="lang-bash">curl 'http://localhost:8084/tasks' \
-H 'Content-Type: application/json;charset=utf-8' \
-d \
'{
   "job":[
      {
         "type":"createServerGroup",
         "cloudProvider":"aws",
         "account":"my-aws-account",
         "application":"demoapp",
         "stack":"",
         "credentials":"my-aws-account",
	"healthCheckType": "ELB",
	"healthCheckGracePeriod":600,
	"capacityRebalance": true,
         "onDemandBaseCapacity":3, 
         "onDemandPercentageAboveBaseCapacity":10,
         "spotAllocationStrategy":"capacity-optimized",
         "setLaunchTemplate":true,
         "launchTemplateOverridesForInstanceType":[
            {
               "instanceType":"m4.large"
            },
            {
               "instanceType":"m5.large"
            },
            {
               "instanceType":"m5a.large"
            },
            {
               "instanceType":"m5ad.large"
            },
            {
               "instanceType":"m5d.large"
            },
            {
               "instanceType":"m5dn.large"
            },
            {
               "instanceType":"m5n.large"
            }

         ],
         "capacity":{
            "min":6,
            "max":21,
            "desired":12
         },
         "subnetType":"private-subnet",
         "availabilityZones":{
            "'"${AWS_REGION}"'":[
               "'"${AWS_REGION}"'a",
               "'"${AWS_REGION}"'b",
               "'"${AWS_REGION}"'c"
            ]
         },
         "keyPair":"'"${EC2_KEYPAIR_NAME}"'",
         "securityGroups":[
            "Demo-EC2-SecurityGroup"
         ],
         "instanceType":"m5.large",
         "virtualizationType":"hvm",
         "amiName":"'"$(aws ec2 describe-images --owners amazon --filters "Name=name,Values=amzn2-ami-hvm-2*x86_64-gp2" --query 'reverse(sort_by(Images, &amp;CreationDate))[0].Name' --region ${AWS_REGION} --output text)"'",
         "targetGroups":[
            "demoapp-targetgroup"
         ],
         "base64UserData":"'"$(base64 user-data.sh)"'",,
        "associatePublicIpAddress":false,
         "instanceMonitoring":false
      }
   ],
   "application":"demoapp",
   "description":"Create New server group in cluster demoapp"
}'
</code></pre> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_Create_ServerGroup.gif"><img alt="Spin Create ServerGroup" class="aligncenter size-full wp-image-20164" height="724" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_Create_ServerGroup.gif" width="1292" /></a></p> 
<p>Spinnaker creates an Amazon EC2 <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html">Launch Template</a> and an ASG with specified parameters and waits until the ALB health check passes before sending traffic to the EC2 Instances.</p> 
<p>The server group and launch template that we just created will look like this in Spinnaker UI:</p> 
<p><em> <a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup.png"><img alt="Spin View ServerGroup" class="aligncenter size-full wp-image-20165" height="1542" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup.png" width="2868" /></a></em></p> 
<p>The UI also displays capacity type, such as the purchase option for each instance type in the <em>Instance Information </em>section:</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup_Purchase_Options_1.png"><img alt="Spin View ServerGroup Purchase Options 1" class="aligncenter wp-image-20166 size-full" height="738" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup_Purchase_Options_1.png" width="2850" /></a><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup_Purchase_Options_2.png"><img alt="Spin View ServerGroup Purchase Options 2" class="aligncenter size-full wp-image-20167" height="742" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_View_ServerGroup_Purchase_Options_2.png" width="2854" /></a></p> 
<h3>3. Access the application</h3> 
<p>Copy the Application Load Balancer URL by selecting the tree icon in the right top corner of the server group, and access it in a browser. You can refresh multiple times to see that the requests are going to different instances every time.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/02/14/Spin_Access_App.png"><img alt="Spin Access App" class="aligncenter size-full wp-image-19976" height="300" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/02/14/Spin_Access_App.png" width="902" /></a></p> 
<p>Congratulations! You successfully deployed the demo application on an Amazon EC2 server group diversified across multiple instance types and purchase options.</p> 
<p>Moreover, you can clone, modify, disable, and destroy these server groups, as well as use them with Spinnaker <a href="https://spinnaker.io/docs/concepts/pipelines/">pipelines</a> to effectively release new versions of your application.</p> 
<h2>Cost savings</h2> 
<p>Check the savings you realized by deploying your demo application on EC2 Spot Instances by going to <strong>EC2 console &gt; Spot Requests &gt; Saving Summary.</strong></p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_Spot_Savings.png"><img alt="Spin Spot Savings" class="aligncenter size-full wp-image-20168" height="583" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/03/17/Spin_Spot_Savings.png" width="902" /></a></p> 
<h2>Cleanup</h2> 
<p>To avoid incurring any additional charges, clean up the resources created in the tutorial.</p> 
<p>Frist, delete the server group, application load balancer and application in Spinnaker.</p> 
<pre><code class="lang-bash">curl 'http://localhost:8084/tasks' \
-H 'Content-Type: application/json;charset=utf-8' \
--data-raw \
'{
   "job":[
      {
         "reason":"Cleanup",
         "asgName":"demoapp-v000",
         "moniker":{
            "app":"demoapp",
            "cluster":"demoapp",
            "sequence":0
         },
         "serverGroupName":"demoapp-v000",
         "type":"destroyServerGroup",
         "region":"'"${AWS_REGION}"'",
         "credentials":"my-aws-account",
         "cloudProvider":"aws"
      },
      {
         "cloudProvider":"aws",
         "loadBalancerName":"demoapp-lb",
         "loadBalancerType":"application",
         "regions":[
            "'"${AWS_REGION}"'"
         ],
         "credentials":"my-aws-account",
         "vpcId":"'"${VPC_ID}"'",
         "type":"deleteLoadBalancer"
      },
      {
         "type":"deleteApplication",
         "application":{
            "name":"demoapp",
            "cloudProviders":"aws"
         }
      }
   ],
   "application":"demoapp",
   "description":"Deleting ServerGroup, ALB and Application: demoapp"
}'
</code></pre> 
<p>Wait for Spinnaker to delete all of the resources before proceeding further. You can confirm this either on the Spinnaker UI or <a href="https://aws.amazon.com/console/">AWS Management Console</a>.</p> 
<p>Then delete the Spinnaker infrastructure by running the following command:</p> 
<pre>aws ec2 delete-key-pair --key-name ${EC2_KEYPAIR_NAME} --region ${AWS_REGION}
rm ~/${EC2_KEYPAIR_NAME}.pem
aws s3api delete-objects \
--bucket ${S3_BUCKET_NAME} \
--delete "$(aws s3api list-object-versions \
--bucket ${S3_BUCKET_NAME} \
--query='{Objects: Versions[].{Key:Key,VersionId:VersionId}}')" #If error occurs, there are no Versions and is OK
aws s3api delete-objects \
--bucket ${S3_BUCKET_NAME} \
--delete "$(aws s3api list-object-versions \
--bucket ${S3_BUCKET_NAME} \
--query='{Objects: DeleteMarkers[].{Key:Key,VersionId:VersionId}}')" #If error occurs, there are no DeleteMarkers and is OK
aws s3 rb s3://${S3_BUCKET_NAME} --force #Delete Bucket
aws cloudformation delete-stack --region ${AWS_REGION} --stack-name ${STACK_NAME}</pre> 
<h2>Conclusion</h2> 
<p>In this post, we learned about the new Amazon EC2 features recently added to Spinnaker, and how to use them to build diversified and optimized Auto Scaling Groups. We also discussed <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html">recommended best practices</a> for EC2 Spot and how they can improve your experience with it.</p> 
<p>We would love to hear from you! Tell us about other Continuous Integration/Continuous Delivery (CI/CD) platforms that you want to use with EC2 Spot and/or Auto Scaling Groups by adding an issue on the <a href="https://github.com/aws/ec2-spot-instances-integrations-roadmap/projects/1">Spot integrations roadmap</a>.</p>
