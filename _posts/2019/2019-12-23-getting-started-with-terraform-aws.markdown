---
layout: post
title: Beginners Guide - Terraform + AWS
date: '2019-12-23 12:37'
excerpt: >-
  Getting started with using Terraform to quickly configure your infrastructure
  in AWS
comments: true
published: true
---

Back again with another simple and quick tutorial, this time showing you how to include **Terraform** in your project to boot up your AWS machines instantaneously. If you're not familiar with Terraform, here is how HashiCorp describes it on their repo:

> Terraform enables you to safely and predictably create, change, and improve infrastructure. It is an open source tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

So basically, Terraform makes it easier for you to manage your infrastructure by converting all of your configurations to code.

If you have experience spinning up EC2 instances, you can see how useful Terraform is, especially when it comes to configuring and updating multiple instances at a time and keeping track of those configurations for versioning.

That's a high-level look at Terraform, now let's see how we can incorporate that into our AWS environment.


Let's start off by installing Terraform onto your machine by downloading it [here](https://www.terraform.io/downloads.html).

Once installed, head to your root project and now let's make a directory and name it _terraform_. Then, create a _main.tf_ file within your terraform folder - this is where the magic is created.

In order to use AWS resources, you must specify AWS as the provider in your _main.tf_ file:

```
provider "aws" {
    region = "us-east-1"
    shared_credentials_file = "path/to/your/.aws/credentials"
} 
```

The above code is basically configuring your Terraform script to communicate with the AWS API. After specifying AWS as our provider, we must then specify the **region** and the path to your aws credentials file. In a production environment, that path won't be necessary, because it's recommended you utilize IAM users and groups.


Now we must specify the resource we want to configure in the same file; since spinning up EC2 instances are the most popular, we'll start there:

```
resource "aws_instance" "web" {
    ami = "ami-00068cd7555f543d5"
    instance_type = "t2.micro"

    tags = {
        Name = "matt-ec2-tf"
    }
}
```

_aws_instance_ declares an EC2 instance, and _web_ is the local name given to the resource. Next, I chose an us-east-1 Linux AMI and then a free tier instance type. _Name_ inside the **tags** block is the name of your EC2 instance.


Once you're finished, open up terminal and ```cd``` into your _terraform_ directory with your _main.tf_ file and execute:

```terraform init```

This initilizes Terraform so that you can start executing your modules.

When Terraform is done initializing, execute:

```terraform plan```

This command provides you with a preview of your resource configurations before they are executed:
![Screen Shot 2019-12-27 at 3.52.04 PM.png]({{site.baseurl}}/img/Screen Shot 2019-12-27 at 3.52.04 PM.png)

If everything looks good after reviewing the terraform plan, you can proceed with:

```terraform apply``` 

This will apply your changes and launch your EC2 instance in AWS and after a few minutes you should see your instance running in the AWS console. It's that simple.

Of course, when you are launching instances for your production environments, you will require more configurations like a subnet, security groups, etc. Refer to this [link](https://www.terraform.io/docs/providers/aws/r/instance.html) for more EC2 arguments, as well as documentation for using other AWS resources.

Once you're done with this tutorial, learn about using variables for cleaner terraform code and working with other resources.

Hope you found this helpful. Good luck and happy coding!



