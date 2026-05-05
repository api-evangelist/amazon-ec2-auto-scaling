---
title: "How to create custom health checks for your Amazon EC2 Auto Scaling Fleet"
url: "https://aws.amazon.com/blogs/compute/how-to-create-custom-health-checks-for-your-amazon-ec2-auto-scaling-fleet/"
date: "Mon, 13 Feb 2023 18:03:19 +0000"
author: "Sheila Busser"
feed_url: "https://aws.amazon.com/blogs/compute/category/compute/auto-scaling/feed/"
---
<p><em>This blog post is written by Gaurav Verma, Cloud Infrastructure Architect, Professional Services AWS.</em></p> 
<p><a href="https://aws.amazon.com/ec2/autoscaling/getting-started/">Amazon EC2 Auto Scaling</a> helps you maintain application availability and lets you automatically add or remove <a href="https://docs.aws.amazon.com/ec2/index.html">Amazon Elastic Compute Cloud (Amazon EC2</a>) instances according to the conditions that you define. You can use dynamic and predictive scaling to scale-out and scale-in EC2 instances. Auto Scaling helps to maintain the self-healing Amazon EC2 environment for an application where Auto Scaling can use the status of Amazon EC2 health checks to determine if an instance is faulty and needs replacement. Amazon EC2 Auto Scaling provides three types of health checks, which are discussed below.</p> 
<p><strong>EC2 Status check</strong>: AWS provides two types of health checks for EC2 instances: System status check and instance status check. System status checks monitor the AWS system on which an instance is running. If the problem is with underlying system, AWS will fix the problem. Instance status check monitors the software and network configuration of an instance. If the instance status check fails, then you can fix the problem by following the steps in the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html">troubleshoot instances with failed status checks documentation</a>.</p> 
<p><strong>Elastic Load Balancer Health Check</strong>: Auto Scaling groups are generally connected to <a href="https://aws.amazon.com/elasticloadbalancing/">Elastic Load Balancers (ELB).</a> ELB provides the application-level health check by monitoring the endpoint (a webpage, or a health page in a web application) of an application. ELB health check monitors the application and marks the instance unhealthy if there is no response from an instance in the configured time.</p> 
<p><strong>Custom Health Check</strong>: You can use custom health checks to mark any instance as unhealthy if the instance fails for the check you define. Custom health checks can be used to implement various user requirements, such as the presence of instance tags added upon completion of a required workflow. The user data script is executed at instance boot time, and it can perform additional investigation into whether or not the user requirements are met before confirming that the instance is ready to accept load. For example, this approach could be used to confirm that the instance was successfully integrated with other parts of a complex application stack.</p> 
<p>In some cases, a customer may add multiple checks either in the Amazon EC2 AMI or in the boot sequence to keep the instance secure and compliant. These checks can increase the boot time for the EC2 instance, and they can reboot the EC2 instance multiple times before an instance can be marked as compliant. Therefore, in some cases, an EC2 instance boot period can take forty to fifty minutes or longer.</p> 
<p>If an EC2 instance isn’t marked as healthy within a defined time, Auto Scaling will mark an instance unhealthy, even though the instance wasn’t yet ready for evaluation. Custom health checks can help manage these situations. You can write the <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html">Amazon EC2 user data script</a> to perform the custom health check and force Auto Scaling to wait until the instance is truly healthy (i.e., functional, secure, and compliant).</p> 
<p>This blog describes a method to write a custom health check. We write an Amazon EC2 user data script to perform the custom health check and automate it for future EC2 instances in the Auto Scaling group. This script can wait for an instance to successfully complete the boot process and then mark the instance as healthy.</p> 
<h2>Prerequisites</h2> 
<p>You must have an AWS Identity and Access Management (<a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create.html">IAM) role</a> for Amazon EC2 with an Auto Scaling policy, which has these two actions allowed for the Auto Scaling group:</p> 
<p>autoscaling:CompleteLifecycleAction</p> 
<p>autoscaling:RecordLifecycleActionHeartbeat</p> 
<p>Furthermore, we use the Amazon EC2 Auto Scaling <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/lifecycle-hooks.html">lifecycle hooks</a>. Lifecycle hooks let you create a solutions that are aware of events in Auto Scaling instance lifecycle, and then perform a custom action on instances when the lifecycle event occurs. As mentioned previously, typically a custom health check is needed when determining the workload readiness of an instance would be longer than the usual boot time for an EC2 instance that Auto Scaling assumes. Therefore, we utilize lifecycle hooks to keep the checks running until the instance is marked healthy.</p> 
<h2>Create custom health check</h2> 
<p>Let’s look at an example where an instance can only be marked as healthy if the instance has a tag with the key “Compliance-Check” and value “Successful”. Until this tag is both (a) present and (b) carries the value “Successful”, the instance shouldn’t be marked as “InService”.</p> 
<ol> 
 <li>Create the Auto Scaling <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-launch-template.html">launch template</a> for Amazon EC2 Auto Scaling. Name your Launch template “test”. In the additional configuration for user data, use this shell script as text.</li> 
</ol> 
<p>The Following script will install the <a href="https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html">AWS Command Line Interface (AWS CLI</a>) to interact with the AWS tagging and Auto Scaling APIs. Then, the script will run the while loop until the instance has a tag with the key “Compliance-Check” and value “Successful”. Once the instance has a tag, it will mark the instance as healthy and the instance will move into the “InService” state.</p> 
<pre><code class="lang-powershell">#!/bin/bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
#get instance id
instance=$(curl http://169.254.169.254/latest/meta-data/instance-id)

#Checking instance status
while true
do
readystatus=$(aws ec2 describe-instances --instance-ids $instance --filters "Name=tag: Compliance-Check,Values= Successful" |grep -i $instance)
if [[ $readystatus = *"InstanceId"* ]]; then
        echo $readystatus &gt;&gt; /home/ec2-user/user-script-output.txt
        aws autoscaling set-instance-health --instance-id $instance --health-status Healthy
        aws autoscaling complete-lifecycle-action --lifecycle-action-result CONTINUE --instance-id $instance --lifecycle-hook-name test --auto-scaling-group-name my-asg
        break 
else
	aws autoscaling set-instance-health --instance-id $instance --health-status Unhealthy
	sleep 5  
fi

done
</code></pre> 
<ol start="2"> 
 <li>Create an Amazon EC2 Auto Scaling group using the AWS CLI with the “test” launch template that you just created and a predefined lifecycle hook. First, create a JSON file “config.json” in a system where you will run the AWS command to create the Auto Scaling group.</li> 
</ol> 
<pre><code class="lang-json">{
    "AutoScalingGroupName": "my-asg",
    "LaunchTemplate": {"LaunchTemplateId": "lt-1234567890abcde12"} ,
    "MinSize": 2,
    "MaxSize": 4,
    "DesiredCapacity": 2,
    "VPCZoneIdentifier": "subnet-12345678, subnet-90123456",
    "NewInstancesProtectedFromScaleIn": true,
    "LifecycleHookSpecificationList": [
        {
            "LifecycleHookName": "test",
            "LifecycleTransition": "autoscaling:EC2_INSTANCE_LAUNCHING",
            "HeartbeatTimeout": 300,
            "DefaultResult": "ABANDON"
        }
    ],
    "Tags": [
        {
            "ResourceId": "my-asg",
            "ResourceType": "auto-scaling-group",
            "Key": "Compliance-Check",
            "Value": "UnSuccessful",
            "PropagateAtLaunch": true
        }
    ]
}
</code></pre> 
<p>To create the Auto Scaling group with the AWS CLI, you must run the following command at the same location where you saved the preceding JSON file. Make sure to replace the relevant subnets that you intend to use in the VPCZoneIdentifier.</p> 
<p><em>&gt;&gt; aws autoscaling create-auto-scaling-group </em><em>–cli-input-json</em><em> file://config.json</em></p> 
<p>This command will create the Auto Scaling group with a configuration defined in the JSON file. This Auto Scaling group should have two instances and a lifecycle hook called “test” with a 300 second wait period at the time of launch of an instance.</p> 
<h2>Tests</h2> 
<p>Now is the time to test the newly-created instances with a custom health check. Instances in Auto Scaling should be in the “Pending:Wait” stage, not the “InService” stage. Instances will be in this stage for approximately five minutes because we have a lifecycle hook time of 300 seconds in the config.json file.</p> 
<p>If the workload readiness evaluation takes more than 300 seconds in your environment, then you can increase the lifecycle hook period to as long as 7200 seconds.</p> 
<p><a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html#Using_Tags_Console">Change the tag value</a> for one instance from “UnSuccessful” to “Successful”. If you’ve changed the tag within the five minutes of instances creation, then the instance should be in the “InService” state and marked as healthy.</p> 
<p><a href="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/02/13/Figure-1.jpg"><img alt="Change Compliance-Check value from &quot;UnSuccessful&quot; to &quot;Successful&quot;. " class="aligncenter size-full wp-image-19962" height="429" src="https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2023/02/13/Figure-1.jpg" width="820" /></a></p> 
<p>This test is a simulation of the situation where the health check of an instance depends on the tag values, and the tag values are only updated if the instance passes all of the checks as per the organization standards. Here we change the tag value manually, but in a real use case scenario, this value would be changed by the booting process when instances are marked as compliant.</p> 
<p>Another test case could be that an instance should be marked as healthy if it’s added to the configuration management database, but not before that. For these checks, you can use the API with the curl command and look for the desired result. An example to call an API is in the above script, where it calls the AWS API to get the instance ID.</p> 
<p>In case your custom health check script needs more than 7200 seconds, you can use this command to increase the lifecycle hook time:</p> 
<p>&gt;&gt; aws autoscaling record-lifecycle-action-heartbeat –lifecycle-hook-name &lt;lh_<br /> name&gt; –auto-scaling-group-name &lt;asg_name&gt; –instance-id &lt;instance_id&gt;</p> 
<p>This command will give you the extra time equal to the time that you have configured in the life cycle hook.</p> 
<h2>Cleanup</h2> 
<p>Once you successfully test the solution, to avoid ongoing charges for resources you created, you should delete the resources.</p> 
<p>To delete the Amazon EC2 Auto Scaling group, run the following command:</p> 
<pre>aws autoscaling delete-auto-scaling-group --auto-scaling-group-name my-asg</pre> 
<p>To delete the launch template, run the following command:</p> 
<pre>aws ec2 delete-launch-template --launch-template-id lt-1234567890abcde12</pre> 
<p>Delete the role and policy as well if you no longer need it.</p> 
<h2>Conclusion</h2> 
<p>EC2 Auto Scaling custom health checks are useful when system or instance health is insufficient, and you want instances to be marked as healthy only after additional checks. Typically, because of these different checks, the Amazon EC2 boot period can be longer than usual, and this may impact the scale-out process when an application needs more resources.</p> 
<p>You can start by exploring EC2 Auto Scaling <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-warm-pools.html">warm pools</a> for these environments. You can keep the healthy marked instances in the warm pool in the Stopped stage. Then, these instances can be brought into the main pool at the time of scale-out without spending time on the boot process and lengthy health check. If you enable <a href="https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-instance-protection.html">scale-in protection</a>, then these healthy instances can move back to the warm pool at the time of scale-in rather than being terminated altogether.</p>
