# Ops-ing with packer and terraform

[Original URL](http://engineering.sharethrough.com/blog/2015/09/08/ops-ing-with-packer-and-terraform/)

> Most of Sharethrough's infrastructure runs on AWS. For a long time we've relied on chef and knife ec2 to maintain resources, but as we grew, we searched for tools that would continue to...

Most of Sharethrough's infrastructure runs on [AWS](https://aws.amazon.com/). For a long time we've relied on [chef](https://www.chef.io/chef/) and [knife ec2](https://github.com/chef/knife-ec2) to maintain resources, but as we grew, we searched for tools that would continue to improve operation efficiency. Recently we started using two tools from [Hashicorp](https://hashicorp.com/), [Packer](https://www.packer.io/) and [Terraform](https://www.terraform.io/), that appear to deliver what we're looking for.

## What we did

Sharethrough powers much of its platform with microservices. We preserve package and library versions, system parameters, and application configuration for these services using Chef cookbooks, attributes, and environment files. For a long time, services shared customized tasks for changing EC2 instance state using `knife-ec2`:

```
1
2
3
4
5
6
7
8``` | ```
masonleung@Masons-MacBook-Pro $ rake -T
rake chef:resolve # resolve cookbook dependencies
rake chef:vendor # vendor cookbook dependencies
rake instance:chef # run chef on instance
rake instance:delete # delete an instance
rake instance:environment # setup the environment
rake instance:list # list instances
rake instance:new # launch a new ec2 instance```
---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A great approach, but it had some drawbacks:

1. Every time you created an instance, libraries had to be recompiled from source (slow)
2. For service clusters, creating nodes one node at a time was `n` times slow, where `n` is the number of instances in the cluster.
3. Some resources aren't EC2 instances at all (e.g. a new queue in [SQS](https://aws.amazon.com/sqs/)), but we still needed consistent automation to create them.
4. Things like security groups, IAM roles, users, and policies were handled manually, and thus lacked a paper trail.

We decided to take a two-step approach to bringing up a hypothetical service:

1. Create a service's EC2 instance with libraries pre-installed
2. Create any non-EC2 resources required to run the service (e.g. SQS)

## Introducing Packer

Packer creates machine images on multiple platforms using a single configuration file. It contains provisioners that work with common configuration management tools (i.e. Chef). We use it with our Chef cookbooks to create Sharethrough-specific [Amazon Machine Images](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIs.html) (AMI). Packer installs packages and libraries into a reusable image.

Here's a snippet of a sample packer file:

```
1
2
3
4
5
6
7
8
9
10
11
12``` | ```
{"provisioners":[{"type":"chef-solo","cookbook_paths":["ops/chef/berks-cookbooks"],"run_list":["bakery::{{user `role`}}"],"environments_path":"ops/chef/environments","chef_environment":"{{user `environment`}}","staging_directory":"/home/ubuntu/cookbooks"}]}```
--------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In our first try, `staging` and `production` images were created separately (they access different AWS resources). We later discovered Packer has a `staging_directory` attribute to save chef cookbooks onto the image. It also has `environments_path` to store environment specified attributes. The `staging_directory` and `environment_path` combination allowed us to rerun cookbooks. When we created an image, we always built with production attributes. If we need a staging environment, we simply reran the Chef cookbooks with staging attributes during instance instantiation. This approach cuts the number of stored images in half.

## Terraform all the resources

If other AWS resources are required to run a microservice, we want to use Terraform to create all of them. Our Terraform files are organized by components; listing the directory gives us a glance of what resources are needed:

```
1
2
3
4
5
6
7
8
9
10
11``` | ```
masonleung@Masons-MacBook-Pro ~/Projects/code/vaca/ops/terraform/production (master) $ ls -l
total 112
-rw-r--r-- 1 masonleung staff 120 Jun 9 11:50 aws.tf
-rw-r--r-- 1 masonleung staff 1346 Aug 20 13:45 ec2.tf
-rw-r--r-- 1 masonleung staff 116 Aug 20 12:37 iam_instance_profiles.tf
-rw-r--r-- 1 masonleung staff 196 Aug 20 12:37 iam_policy_attachment.tf
-rw-r--r-- 1 masonleung staff 322 Aug 20 12:37 iam_role.tf
-rw-r--r-- 1 masonleung staff 9314 Aug 20 13:45 main.tfstate
-rw-r--r-- 1 masonleung staff 14090 Aug 20 13:45 main.tfstate.backup
-rw-r--r-- 1 masonleung staff 317 Aug 20 12:37 security_group.tf
-rw-r--r-- 1 masonleung staff 680 Aug 20 12:37 variable.tf```
------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Below is a sample terraform file for EC2 instance creation

```
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38``` | ```
resource "aws_instance" "vaca-green" {
 count = 2
 ami = "ami-12345abc"
 instance_type = "m3.large"
 key_name = "master"
 availability_zone = "${lookup(var.zones, concat("zone",count.index))}"
 security_groups = ["office-access", "${aws_security_group.vaca_security_group.name}", "${aws_security_group.vaca_production.name}"]
 iam_instance_profile = "${aws_iam_instance_profile.vaca.name}"

 root_block_device {
 volume_size = "50"
 delete_on_termination = true
 }

 tags {
 Name = "vaca production green ${count.index}"
 Project = "${var.application_name}"
 Roles = "app"
 Stages = "${var.environment}"
 Group = "green"
 }

 lifecycle {
 create_before_destroy = true
 }

 provisioner "remote-exec" {
 inline = [
 "cd /home/ubuntu; sudo chef-solo -c cookbooks/solo.rb -j cookbooks/node.json -E staging",
 "host=$(curl -s [http://169.254.169.254/latest/meta-data/public-hostname)"](http://169.254.169.254/latest/meta-data/public-hostname)&quot);,
 "touch $host"
 ]
 }

 provisioner "local-exec" {
 command = "bundle install && bundle exec cap HOSTFILTER=${self.public_dns} production deploy"
 }
}```
--------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

A few callouts:

1. We perform blue-green deploy to our microservices for breaking changes. Bringing up a new set of EC2 instances requires copy-and-paste of `aws_instance` resources and a rerun of Terraform. When the deploy is completed, we remove the duplicated `aws_instance` section and destroy the extra instances.
2. As mentioned earlier, we only create AMIs with production attributes. Staging instances are built by re-running chef with staging attributes inside the provisioner section.
3. The `root_block_device` section allows us to create [EBS](https://aws.amazon.com/ebs/) volumes of any size, which saves us from forgetting to mount volumes used to, say, store logs.
4. Lastly, we tie IAM role and policy which are defined in other Terraform files to an EC2 instance via the `iam_instance_profile` attribute, preventing us from missing role assignment.

## In Summary

Packer saves us time from having to install libraries from sources every time a new EC2 instance is created. We can simply harden an image and use it as the base image for microservices.

Terraform also solves a common AWS problem: when a developer modifies resources in the AWS console, there is no way of tracing that change to a documented step that could be reversed. With Terraform, changes made to infrastructure is code in Terraform files, so they're recorded and maintained in git. Promoting visibility this way has saved us from having a forgotten one-off step.

Pre-baking an image with Packer saves time, but the proliferation of images is hard to manage without the ability to associate metadata to an image. We're looking at Hashicorp's [Atlas](https://atlas.hashicorp.com/) product as a way to handle images as version-controlled assets.

So far we've only used a subset of Terraform, but we're starting to use it for non-EC2 resources. And although Terraform is great for creating brand new infrastructure, it needs improvements when working on existing nodes. There is a [free tool](https://github.com/dtan4/terraforming) to lessen the work on exporting existing infrastructure, but would be nice if Terraform dealt with it natively.

We're excited about Hashicorp's products and will continue to test-drive them as they mature.
