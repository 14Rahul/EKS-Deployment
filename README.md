# DevOps Assignment- EKS

## Deliverables

**A highly available and scalable containerized "Hello World" application deployed on Amazon EKS accessible from the internet through an Application Load Balancer. The application configured to autoscale based on CPU utilisation(70% CPU). Below is the Link.**
-  http://k8s-hellowor-ingress-db08b070a4-1429673963.us-east-2.elb.amazonaws.com/

**Suggestions to make application cost-effective.**
- Use AWS Farget serverless cluster deployment option.
- Use Autoscaling Group to scale up and scale down cluster nodes based on Utilization.
- Use Cheapest Region to deploy EKS Cluster.
- Use Spot instance to if the application can tolerate interruption or use reserved instance.

**Suggestions to make the application more secure.**
- Host the EKS cluster in Private Subnet and use ALB to make it accessible to the external world.
- Use WAF in front of ALB to secure applications from attacks.
- Use SSL to encrypt data in transit.
- Use KMS to encrypt data at rest
- Properly configure firewall settings, including Security Groups and Network Access Control Lists (NACL).
- Provide access to users and services following the least privilege policy.
- Establish robust monitoring (ELK, Prometheus and Grafana etc.) and alerting mechanism for your Amazon EKS cluster.
- Perform Regular Patching of EKS Cluster Nodes
- Use VPC Endpoints to access other services privately.
- Use AWS Gaurd Duty to monitor VPC flow logs.
- Implement EKS namespaces and ACLs (Access Control Lists) to restrict access within the cluster.
- Use secret and config maps for securely storing sensitive data and configurations.
