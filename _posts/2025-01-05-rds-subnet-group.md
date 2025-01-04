---
title: How to change RDS subnet group on shared account VPC?
tags: aws rds
categories: Cloud
---
This is a problem I encountered while doing a project from a certain client, where we were supposed to do an enhancement of the VPC CIDRs which were excessively assigned from /16 to /24. However, the VPC were shared across another account using AWS Resource Access Manager (RAM) hence in order to perform an enhancement in the VPC CIDR, we need to move the workload of the two account sharing the VPC at the same time. As I moved the resource from the shared account via console, I ran across an issue, perhaps bug that ends up requiring me to explore the workarounds for related issue.

I illustrated the architecture diagram using draw.io below (drawn not for scale - for illustration purposes only) in order to explain the situations a bit more.

![Illustration of the cloud architecture](/assets/img/posts/2025-01-05-image1.jpg)

So both account has RDS Instance attached as its workload (there are other resources such as EC, EFS and such but that's irrelevant to the issue) so in order to change the VPC hosting the RDS, we will need to change the subnet groups of the RDS. However, when an RDS changes VPC, the security group needs to be changed to the new VPC's security group as well. I put the screenshot of basically the issue I found while switching the RDS Subnet Group.

![Screenshot + Explaination of the issue](/assets/img/posts/2025-01-05-image2.jpg)

So when you changes the subnet group into another VPC, the console does not instantly load the security groups of the new target VPC, but it is just stuck with the old target VPC's security groups. When you are changing the main VPC account's RDS subnet group, the AWS console's behaviour is that it will just end up loading the default security group of the VPC, and you can append the changes to the desired security group after you changed the subnet group (apply changes for a second time). While it's honestly quite an annoying workaround, at least it's pretty much an easy fix. However, when you try to attempt this on the other account, things start to get confusing. I put the screenshot of the error I found below on the second AWS account.

![The main issue](/assets/img/posts/2025-01-05-image3.jpg)

It turns out that while you can share VPC across account with AWS Resource Access Manager (RAM), the security groups created are not shared across account, so basically you will find permission issues regarding the default security group when applying this changes via console. It took me some time and an advice from my senior at work (thanks a lot btw) to actually attempt applying the configuration via AWS CLI. Using the command below, I ended up with a successful change of configuration within few seconds after applying the command.

```bash
aws rds modify-db-instance \
  --db-instance-identifier <db-name> \
  --vpc-security-group-ids <db-sg-id> \
  --db-subnet-group-name <db-subnet-group-name>  \
  --apply-immediately 
```

Turns out, if console doesn't work, try the CLI way (or SDK, programmatically, anything).

That's all for now, thank you for reading.