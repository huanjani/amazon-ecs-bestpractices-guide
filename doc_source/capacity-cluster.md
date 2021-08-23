# Cluster capacity<a name="capacity-cluster"></a>

Earlier in this topic, we discussed how to scale out the replica account for your by using scaling metrics\. Your tasks also need to run on resources, including CPU and memory resources\. This again relates back the topic of capacity\. In Amazon ECS, capacity is provided through two primary providers: AWS Fargate and Amazon EC2\.

You can provide capacity to an Amazon ECS cluster in several ways\. For example, you can launch Amazon EC2 instances and register them with the cluster at startup using the Amazon ECS container agent\. However, this method can be challenging because you need to manage scaling on your own\. Therefore, we recommend that you use Amazon ECS capacity providers\. They manage resource scaling for you\. There are three kinds of capacity providers: Amazon EC2, Fargate, and Fargate Spot\.

The Fargate and Fargate Spot capacity providers handle the lifecycle of Fargate tasks for you\. Fargate provides on\-demand capacity, and Fargate Spot provides Spot capacity\. When a task is launched, ECS provisions a Fargate resource for you\. This Fargate resource comes with the memory and CPU units that directly correspond to the task\-level limits that you declared in your task definition\. Each task receives its own Fargate resource, making a 1:1 relationship between the task and compute iresources\. 

Tasks that run on Fargate Spot are subject to interruption\. Interruptions come after a two\-minute warning\. These occur during periods of heavy demand\. Fargate Spot works best for interruption\-tolerant workloads such as batch jobs, development or staging environments\. They're also suitable for any other scenario where high availability and low latency isn't a rrequirement\.

You can run Fargate Spot tasks alongside Fargate on\-demand tasks\. By using them together, you receive provision “burst” capacity at a lower cost\.

ECS can also manage the Amazon EC2 instance capacity for your tasks\. Each Amazon EC2 Capacity Provider is associated with an Amazon EC2 Auto Scaling Group that you specify\. When you use the Amazon EC2 Capacity Provider, ECS cluster auto scaling maintains the size of the Amazon EC2 Auto Scaling Group to ensure all scheduled tasks can be placed\.

## Cluster capacity best practices<a name="capacity-cluster-best-practices"></a>

**Add headroom to your service, not to the Capacity Provider\.** Amazon EC2 Capacity Providers offer a target capacity value\. If you set the value lower than 100%, ECS provisions more Amazon EC2 instances than necessary to accommodate your tasks\. Having several Amazon EC2 instances ready to accept tasks can be helpful\. However, when you use Amazon Virtual Private Cloud, launching new tasks require additional time to download the image and attach a network interface\. This added latency could be detrimetal to your bottom line\.

Therefore, we recommend that you do the following\. Instead of reducing the target capacity of your Capacity Provider, increase the number of replicas in your service by modifying the target tracking scaling metric or the step scaling thresholds of the service\. For more information about the related scaling policies, see [Target Tracking Scaling Policies](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-autoscaling-targettracking.html) or [Step Scaling Policies](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-autoscaling-stepscaling.html) in the *Amazon Elastic Container Service Developer Guide*\. The Amazon EC2 Capacity Provider provisions the capacity that's needed for additional tasks by adding additional instances to the Auto Scaling Group\. This helps to ensure that both the compute and application resources are available when you need them\. For example, it can help by doubling the number of tasks in an ECS Service to accommodate an immediate 100% burst in demand\. 