AWS Bastion Terraform module
===========================================

[![Open Source Helpers](https://www.codetriage.com/guimove/terraform-aws-bastion/badges/users.svg)](https://www.codetriage.com/guimove/terraform-aws-bastion)

Terraform module which creates a secure SSH bastion on AWS.

Mainly inspired by [Securely Connect to Linux Instances Running in a Private Amazon VPC](https://aws.amazon.com/blogs/security/securely-connect-to-linux-instances-running-in-a-private-amazon-vpc/)

Features
--------

This module will create an SSH bastion to securely connect in SSH  to your private instances.
![Bastion Infrastrucutre](https://raw.githubusercontent.com/Guimove/terraform-aws-bastion/master/_docs/terraformawsbastion.png)
All SSH  commands are logged on an S3 bucket for security compliance, in the /logs path.

SSH  users are managed by their public key, simply drop the SSH key of the user in  the /public_keys path of the bucket.
Keys should be named like 'username.pub', this will create the user 'username' on the bastion server.

Then after you'll be able to connect to the server with : 

```
ssh [-i path_to_the_private_key] username@bastion-dns-name
```

From this bastion server, you'll able to connect to all instances on the private subnet. 

If there is a missing feature or a bug - [open an issue](https://github.com/Guimove/terraform-aws-bastion/issues/new).

Usage
-----

```hcl
module "bastion" {
  "source" = "terraform-aws-modules/bastion/aws"
  "bucket_name" = "my_famous_bucket_name"
  "region" = "eu-west-1"
  "vpc_id" = "my_vpc_id"
  "is_lb_private" = "true|false"
  "bastion_host_key_pair" = "my_key_pair"
  "hosted_zone_name" = "my.hosted.zone.name."
  "bastion_record_name" = "bastion.my.hosted.zone.name."
  "elb_subnets" = [
    "subnet-id1a",
    "subnet-id1b"
  ]
  "auto_scaling_group_subnets" = [
    "subnet-id1a",
    "subnet-id1b"
  ]
  tags = {
    "name" = "my_bastion_name",
    "description" = "my_bastion_description"
  }
}
```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| auto_scaling_group_subnets | List of subnet were the Auto Scalling Group will deploy the instances | list | - | yes |
| bastion_amis |  | map | `<map>` | no |
| bastion_host_key_pair | Select the key pair to use to launch the bastion host | string | - | yes |
| bastion_instance_count |  | string | `1` | no |
| bastion_record_name | DNS record name to use for the bastion | string | `` | no |
| bucket_name | Bucket name were the bastion will store the logs | string | - | yes |
| cidrs | List of CIDRs than can access to the bastion. Default : 0.0.0.0/0 | list | `<list>` | no |
| create_dns_record | Choose if you want to create a record name for the bastion (LB). If true 'hosted_zone_name' and 'bastion_record_name' are mandatory | string | - | yes |
| elb_subnets | List of subnet were the ELB will be deployed | list | - | yes |
| hosted_zone_name | Name of the hosted zone were we'll register the bastion DNS name | string | `` | no |
| is_lb_private | If TRUE the load balancer scheme will be "internal" else "internet-facing" | string | - | yes |
| log_auto_clean | Enable or not the lifecycle | string | `false` | no |
| log_expiry_days | Number of days before logs expiration | string | `90` | no |
| log_glacier_days | Number of days before moving logs to Glacier | string | `60` | no |
| log_standard_ia_days | Number of days before moving logs to IA Storage | string | `30` | no |
| region |  | string | - | yes |
| tags | A mapping of tags to assign | map | `<map>` | no |
| vpc_id | VPC id were we'll deploy the bastion | string | - | yes |

## Outputs

| Name | Description |
|------|-------------|
| bucket_name |  |
| elb_ip |  |


Known issues
------------

Tags are not applied to the instances generated by the auto scaling group.
Known terraform issue : terraform-providers/terraform-provider-aws#290


Authors
-------

Module managed by [Guimove](https://github.com/Guimove).

License
-------

Apache 2 Licensed. See LICENSE for full details.
