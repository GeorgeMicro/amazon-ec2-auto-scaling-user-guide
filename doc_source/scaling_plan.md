# Scaling the Size of Your Auto Scaling Group<a name="scaling_plan"></a>

*Scaling* is the ability to increase or decrease the compute capacity of your application\. Scaling starts with an event, or scaling action, which instructs an Auto Scaling group to either launch or terminate Amazon EC2 instances\.

Amazon EC2 Auto Scaling provides a number of ways to adjust scaling to best meet the needs of your applications\. As a result, it's important that you have a good understanding of your application\. Keep the following considerations in mind:
+ What role should Amazon EC2 Auto Scaling to play in your application's architecture? It's common to think about automatic scaling as a way to increase and decrease capacity, but it's also useful for maintaining a steady number of servers\.
+ What cost constraints are important to you? Because Amazon EC2 Auto Scaling uses EC2 instances, you only pay for the resources that you use\. Knowing your cost constraints helps you decide when to scale your applications, and by how much\.
+ What metrics are important to your application? Amazon CloudWatch supports a number of different metrics that you can use with your Auto Scaling group\. We recommend reviewing them to see which of these metrics are the most relevant to your application\.

**Topics**
+ [Scaling Options](#scaling_typesof)
+ [Multiple Scaling Policies](#multiple-scaling-policy-resolution)
+ [Maintaining the Size of Your Auto Scaling Group](as-maintain-instance-levels.md)
+ [Manual Scaling](as-manual-scaling.md)
+ [Scheduled Scaling](schedule_time.md)
+ [Dynamic Scaling](as-scale-based-on-demand.md)
+ [Scaling Cooldowns](Cooldown.md)
+ [Auto Scaling Instance Termination](as-instance-termination.md)
+ [Lifecycle Hooks](lifecycle-hooks.md)
+ [Temporarily Removing Instances](as-enter-exit-standby.md)
+ [Suspending and Resuming Scaling Processes](as-suspend-resume-processes.md)

## Scaling Options<a name="scaling_typesof"></a>

Amazon EC2 Auto Scaling provides several ways for you to scale your Auto Scaling group\.

**Maintain current instance levels at all times**  
You can configure your Auto Scaling group to maintain a minimum or specified number of running instances at all times\. To maintain the current instance levels, Amazon EC2 Auto Scaling performs a periodic health check on running instances within an Auto Scaling group\. When Amazon EC2 Auto Scaling finds an unhealthy instance, it terminates that instance and launches a new one\. For information about configuring your Auto Scaling group to maintain the current instance levels, see [Maintaining the Number of Instances in Your Auto Scaling Group](as-maintain-instance-levels.md)\.

**Manual scaling**  
Manual scaling is the most basic way to scale your resources\. Specify only the change in the maximum, minimum, or desired capacity of your Auto Scaling group\. Amazon EC2 Auto Scaling manages the process of creating or terminating instances to maintain the updated capacity\. For more information, see [Manual Scaling](as-manual-scaling.md)\.

**Scale based on a schedule**  
Sometimes you know exactly when you will need to increase or decrease the number of instances in your group, simply because that need arises on a predictable schedule\. Scaling by schedule means that scaling actions are performed automatically as a function of time and date\. For more information, see [Scheduled Scaling for Amazon EC2 Auto Scaling](schedule_time.md)\.

**Scale based on demand**  
A more advanced way to scale your resources, scaling by policy, lets you define parameters that control the scaling process\. For example, you can create a policy that calls for enlarging your fleet of EC2 instances whenever the average CPU utilization rate stays above ninety percent for fifteen minutes\. This is useful for scaling in response to changing conditions, when you don't know when those conditions will change\. You can set up Amazon EC2 Auto Scaling to respond for you\. For more information, see [Dynamic Scaling for Amazon EC2 Auto Scaling](as-scale-based-on-demand.md)\.

## Multiple Scaling Policies<a name="multiple-scaling-policy-resolution"></a>

An Auto Scaling group can have more than one scaling policy attached to it\. You can sometimes combine scaling policies to maximize the performance of an Auto Scaling group\.

To illustrate how multiple policies work together, consider an application that uses an Auto Scaling group and an Amazon SQS queue to send requests to the group EC2 instances\. To help ensure that the application performs at optimum levels, there are two policies that control when the Auto Scaling group should scale out\. One policy uses the Amazon CloudWatch `CPUUtilization` metric to detect when an instance is at 90% of capacity\. The other uses a custom metric to detect when the SQS queue is becoming overwhelmed with messages\. For more information about scaling based on message activity in your SQS queue, see [Scaling Based on Amazon SQS](as-using-sqs-queue.md)\.

When you have multiple policies attached to an Auto Scaling group, there's a chance that policies could instruct it to scale out \(or in\) at the same time\. For example, it's possible that the EC2 instance could trigger the CloudWatch alarm for the `CPUUtilization` metric at the same time that the SQS queue triggers the alarm for the custom metric\.

When these situations occur, Amazon EC2 Auto Scaling chooses the policy that has the greatest impact on the Auto Scaling group\. For example, suppose that the policy for CPU utilization launches one instance, while the policy for the SQS queue launches two instances\. If the scale\-out criteria for both policies are met at the same time, Amazon EC2 Auto Scaling gives precedence to the SQS queue policy\. It has the greatest impact on the Auto Scaling group\. This results in the Auto Scaling group launching two instances\. This precedence applies even when the policies use different criteria for scaling out\. For instance, if one policy launches three instances, and another increases capacity by 25 percent, Amazon EC2 Auto Scaling give precedence to whichever policy has the greatest impact on the group at that time\.