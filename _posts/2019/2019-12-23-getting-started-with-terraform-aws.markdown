---
layout: post
title: Beginners Guide - Terraform + AWS
date: '2019-12-23 12:37'
excerpt: Getting started with using Terraform to quickly configure your infrastructure in AWS
comments: true
published: true
---

Back again with another simple and quick tutorial, this time showing you how to include **Terraform** in your project to boot up your AWS machines instantaneously. If you're not familiar with Terraform, here is how HashiCorp describes it:

> Terraform enables you to safely and predictably create, change, and improve infrastructure. It is an open source >tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

So basically, Terraform makes it easier for you to manage your infrastructure by converting all of your configurations to code.

If you have experience spinning up EC2 instances, you can see how useful Terraform is, especially when it comes to configuring and updating multiple instances at a time and keeping track of those configurations for versioning.

That's a high-level look at Terraform, now let's see how we can incorporate that into our AWS environment.


Let's start off by installing Terraform onto your machine by downloading it [here](https://www.terraform.io/downloads.html).



