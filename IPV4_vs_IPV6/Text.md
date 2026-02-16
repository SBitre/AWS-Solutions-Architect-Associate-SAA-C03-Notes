Placement Groups

Cluster

PRos: Great network
Cons: If the AZ fails, all instances fail at the same time

USE CASE : big data jobs that need to complete fast
Application that needs extremely low latency and high network throughput.

Spread

PROS : Can spread across AZs

Reduced risk of complete failure

EC2 instances are on different physical hardware

CONS: limited to 7 instances per AZ per placement group

USE CASE: application that needs to maximize high availability

Critical applications where each instance must be isolated from failure from each other.

PARTITION

up to 7 partitions per AZ

can span across multiple AZs in the same region

up to 100s of EC2 instances

The instances in a partition do not share racks with the instances in the other partitions

A partition failure can effect many EC2 but wont affect pther partitions

EC2 instances get access to the partition information as metadata

USE case : HDFS, Hbase, cassandra and kafka



ENI ELASTIC NETWORK INTERFACE:
Logical component in a VPC that represents a virtual network card
The ENI can have the following attributes:
Primary private ipv4, one more secondary ipv4
one elastic ip(ipv4) per private ipv4
one public ipv4
one or more security groups
a mac address
you can create ENI independantly and attach a then on the fly (move them) on EC@ instances for failover
Bound to a specific availaibility zone



WHY DO YOU NEED ENI?? BECAUSE IT GIVE ENABLES YOU TO HAVE ANOTHER SECONDARY PRIVATE IPV4 ADRRESS FOR A INSTANCE WHICH YOU CAN VIEW FROM NETWORK INTERFACES AND IT IS DONE SO THAT IF THE SAME APPLICATION IN RUNNING ON TWO DIFFERENT INSTANCES YOU CAN ATTACH ONE OF THE PRIVATE IPV4 ADDRESS TO THAT INSTANCE AND ACCESS IT. THIS PREVENTS NETWORK FAILOVER.