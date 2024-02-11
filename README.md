## Healthcare Application Architecture on AWS
![alt text]()


The above diagram shows the cloud architecture of how the healthcare application would be deployed over
the AWS cloud. In the proposed healthcare application architecture, we intend to have 3 types of users/tenants. The following
are the types :


1. End users/patients
2. Hospital staff/administrator
3. New Healthcare tenant


Any user/tenant will access the application via an IAM role associated with him/her. Even when considering a new healthcare tenant trying to onboard to the application or accessing it via APIs, the API gateway will
authenticate using Identity and Access Management (IAM) roles for secure and fast authentication. The entire architecture in this case is shown to be deployed over 2 availability zones, with a Virtual Private
CLoud (VPC) spanning over all the AZs. 

The end user or a hospital staff will access the application via a DNS which will be provided by the AWS Route53 service. Route53 spans over multiple AWS regions and Availability zones and routes the traffic
based on the user’s geographical location and the health of the available resources using the Anycast routing algorithm. We have Web application firewall (WAF) attached to Route53 to protect the web application
from common web exploits and attacks. WAF contains a component called Web ACL (Access Control List) which is a set of rules and conditions that define how WAF should inspect and filter incoming web traffic.
These rules and conditions enforce the security policies that protect the application. The WAF upon successful traffic filtering, passes the request to the Application Load Balancer (ALB), responsible for Layer 7
(Application level) load balancing. ALB does content-based routing, allowing to route requests to different backend services based on the content of the request. The ALB forwards the request to the Elastic Kuber-
netes Service (EKS) where the actual workload is running. EKS has certain nodegroups attached to it which contain multiple Elastic cloud compute (EC2) instances. These EC2 instances are provisioned dynamically
as per the varying workload. Depending on a threshold of CPU utilization, network throughput, etc, the autoscaling policy determines how many EC2 nodes are to be provisioned and we can also define the scale-down
policies in the same way. In the first place, EKS gets the application image from Elastic Container Repository(ECR), which contains all the successfully built images of the healthcare application. The application uses
AWS Relational Database Service (S3) for storing relational data. the architecture also uses S3 to store any kind of object storage needs, like, flat files, binaries, data dumps, etc. All the data across the RDS and S3
storage is cached in AWS Elasticache, via managed Redis servers. Redundant read replicas of Redis servers are maintained to fetch data at high speeds, ensuring high performance and low latency. Elasticache provides
multi-availability zone replication ensuring high availability and also scales automatically as per the varying workload requirements.


In the case of a new Healthcare provider who wants to use the already existing APIs before on-boarding
on the application, would require IAM roles to be granted to access the APIs. This flow would be a little
bit different from the general one. This flow would first go through an API Gateway checking the security
and authentication and granting access to the authorized user to be forwarded to the Network Load Balancer
(NLB). The request would then be forwarded to the workload running on the EKS cluster.


From the point of security, we have included IAM for Role-based Access Control to the application and
the APIs. We also have included AWS Cloudtrail, which enables governance, compliance, operational au-
diting, and risk auditing of your AWS account, especially enforcing HIPAA compliance. Since this application
will have an enormous amount of sensitive patient data, it’s very important to have air-tight security for the
application and the data involved. The architecture includes Key Management System (KMS), to create
and manage cryptographic keys for use with AWS services. It supports both symmetric and asymmetric
keys. AWS KMS supports automatic key rotation for customer-managed keys. This helps improve security
by regularly replacing old keys with new ones.


We have used AWS Elastic Disaster Recovery (EDR) service for replicating the architecture into a region of interest where we want to have the application up and running when it fails in the primary region.
It saves costs by removing idle recovery site resources and paying for a full disaster recovery site only when
needed. For the observability and monitoring stack, we have implemented AWS Cloudwatch. This helps
us generate metrics for every resource that we use check how the resources are performing and visualize the
trends of the resource performance. Also, we set alerts and triggers based on a certain event.


For cost calculation and budgeting, we have used the AWS Cost Calculator. This provides us with an
insight into how much the architecture created will cost us on average and that will help us in budgeting our
resources accordingly. AWS Cost and Usage Reporting tool (CUR), will help us to generate detailed reports
about AWS spending and usage. These reports will provide insights into how resources are being used, which
services are contributing to costs, and how changes to your environment impact spending.
