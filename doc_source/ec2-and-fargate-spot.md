# Using Amazon EC2 Spot and FARGATE\_SPOT<a name="ec2-and-fargate-spot"></a>

Spot capacity can provide significant cost savings over on\-demand instances\. Spot capacity is excess capacity that's priced significantly lower than on\-demand or reserved capacity\. Spot capacity is suitable for batch processing and machine\-learning workloads, and development and staging environments\. More generally, it's suitable for any workload that tolerates temporary downtime\. 

Understand that the following consequences because Spot capacity might not be available all the time\.
+ First, during periods of extremely high demand, Spot capacity might be unavailable\. This can cause Fargate Spot task and Amazon EC2 Spot instance launches to be delayed\. In these events, ECS services retry launching tasks, and Amazon EC2 Auto Scaling groups also retry launching instances, until the required capacity becomes available\. Fargate and Amazon EC2 don't replace Spot capacity with on\-demand capacity\. 
+ Second, when the overall demand for capacity increases, Spot instances and tasks might be terminated with only a two\-minute warning\. After the warning is sent, tasks should begin an orderly shutdown if necessary before the instance is fully terminated\. This helps minimize the possibility of errors\. For more information about a graceful shutdown, see [Graceful shutdowns with ECS](https://aws.amazon.com/blogs/containers/graceful-shutdowns-with-ecs/) \.

To help minimize Spot capacity shortages, consider the following recommendations: 
+ **Use multiple Regions and Availability Zones\.** Spot capacity varies by Region and Availability Zone\. You can improve Spot availability by running your workloads in multiple Regions and Availability Zones\. If possible, specify subnets in all the Availibility Zones in the Regions where you run your tasks and instances\. 
+ **Use multiple Amazon EC2 instance types\.** When you use Mixed Instance Policies with Amazon EC2 Auto Scaling, multiple instance types are launched into your Auto Scaling Group\. This ensures that a request for Spot capacity can be fulfilled when needed\. To maximize reliability and minimize complexity, use instance types with roughly the same amount of CPU and memory in your Mixed Instances Policy\. These instances can be from a different generation, or variants of the same base instance type\. Note that they might come with additional features that you might not require\. An example of such a list could include m4\.large, m5\.large, m5a\.large, m5d\.large, m5n\.large, m5dn\.large, and m5ad\.large\. For more information, see [Auto Scaling groups with multiple instance types and purchase options](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ **Use the capacity\-optimized Spot allocation strategy\.** With Amazon EC2 Spot, you can choose between the capacity\- and cost\-optimized allocation strategies\. If you choose the capacity\-optimized strategy when launching a new instance, Amazon EC2 Spot selects the instance type with the greatest availability in the selected Availability Zone\. This helps reduce the possibility that the instance is terminated soon after it launches\. 