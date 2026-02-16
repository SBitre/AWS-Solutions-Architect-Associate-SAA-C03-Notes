EBS Volumes come in 6 types

gp2 / gp3 (SSD) : General purpose SSD volume that balances price and performance for a wide variety of workloads.
io1 / io2 Block Express (SSD) : Highest-performance SSD volume for mission-critical low-latency or high-throughput workloads.
st 1 (HDD) : low cost HDD volume designed for frequently accessed, throughput-intensive workloads.
sc 1 (HDD) : lowest cost HDD volume designed for less frequently accessed workloads.



EBS Volumes are characterized in size | throughput | IOPS (i/o OPs Per Sec)
When in doubt always consult the aws documentation - its good!
only gp2/gp3 and io1/io2 Block Express can be used as boot volumes



GENERAL PURPOSE SSD

Cost effective storage, low latency
System boot volumes, virtual desktops, Development and test environments
1 GiB - 16 TiB
gp3:
Base line of 3,000 IOPS and throughput of 125 MiB/s
Can increase IOPS up to 16,000 and throughput up to 1000 MiB/s independently
gp2:
Small gp2 volumes can burst IOPS to 3,000
Size of the volume and IOPS are linked, max IOPS is 16000
3 IOPS per GiB, means at 5,334 GiB we are at the max IOPS

EBS Multi attach

ONLY FOR io1 and io2 family

It attaches the same EBS volume to multiple EC@ instances in the same AZ.
each instance has full read and write permissions to high-performance volume.

Use Case:

Achieve higher application availability in clustered Linux applications
Applications must manage concurrent write operations

UP TO 16 EC2 INSTANCES AT A TIME

must use a file system that's cluster-aware

AMAZON EFS - Elastic File System

Managed NFS (Network File system) that can be mounted on many EC2

EFS works with EC2 instances in multi-AZ

Highly available, scalable, expensive (3x gp2), pay per use

