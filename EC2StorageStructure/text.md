WHAT IS EBS VOLUME?

AN EBS (Elastic Block Volume) Volume is a network drive you can attach to your instance while they run
It allows your instances to persist data, even after their termination
They can only be mounted to one instance at a time at the CCP level
They are bound to a specific availability zone
ANALOGY ; Think of them as a NETWORK USB STICK



AMI OVERVIEW

AMI = Amazon Machine lmage

AMI are customization of an EC2 instance

you add your own software, configuration, operating system, monitoring

faster boot / configuration time because all your software is pre-packaged

AMI are built for a specific region ( and can be copied across regions)

You can launch EC2 instances from:

A public AMI; AWS provided

Your own : you make and maintain them yourself

an AWS marketplace AMI; AN ami someone else made and potentially sells