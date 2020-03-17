---
date: "2019-09-20"
title: "How to Deploy bitwarden_rs via AWS Fargate"
tags: ["AWS", "Rust", "Docker", "Security", "Bitwarden"]
draft: false
---

Recently, I ran across a project called [Bitwarden](https://bitwarden.com/), which is an open source password manager. There are a few third-party projects written in other languages that follow the project's API. Further, there is one impressive project [bitwarden_rs](https://github.com/dani-garcia/bitwarden_rs) that re-implements the Bitwarden project in Rust. It features a significantly smaller memory and resource footprint than the original application, which makes it perfect to host on free tier VMs on any of the major cloud providers. Even better, it ships as a public container on Docker Hub, so setting up the application is a breeze.

Since I am most familiar with AWS, I chose to deploy it via Fargate. The Fargate service is _not_ within the typical free tier plan that AWS provides, but as mentioned above, bitwarden_rs does not require a lot of resources. So, a small instance with no auto-scaling or other features should run less than \$20 a month.

### Prerequisites:

- AWS account
- available domain name

Let us get started!

# Launch Fargate service

We are going to leverage Fargate to do most of the heavy lifting for us. Once you have logged into AWS, navigate to ECS. Then, set up a custom image. With the following parameters:

- 1: container name: _whatever you would like_
- 2: image: `bitwardenrs/server` _(ECS will obtain this from Docker Hub)_
- 3: container port: `80`
- 4: container protocol: `tcp`
- 5: task memory: `0.5 GB (512)`
- 6: task CPU: `0.25 vCPU (256)`

Once the image is constructed, Fargate will deploy the image and set up the related infrastructure. This will take a few minutes. Once everything is set up, you will be able to access the front-end of the Bitwarden application by utilizing the DNS provided by AWS. It will be in this format:

```bash
EC2Co-XXXXX-XXXXXXXXXXX-XXXXXXXXX.xx-xxxx-x.elb.amazonaws.com
```

By accessing that URL, you should see the Bitwarden login page. However, do not set up an account and login just yet - the content is being served over HTTP and is not secure. We will now configure our infrastructure to utilize HTTPS.

# HTTPS setup

## DNS configuration

If your domain name was obtained from a Domain Name System (DNS) provider other than AWS, you will need to configure your current DNS provider to use the AWS nameservers. So, let's do that first:

- In the AWS console, navigate to Route 53. Create a new hosted zone with your domain name.
- Once this has been created, AWS will create two records for you - a `NS` record and a `SOA` record. The `NS` record should have 4 values in it - these are the namesevers you will need to point your current DNS provider to.
- Update your current DNS provider to use the AWS nameservers.
- Create a record set to use a subdomain of your intended domain. Point this subdomain to an `alias` target of the ALB previously set up by Fargate.

## HTTPS certificate

Now that we have DNS managed by AWS, we can use the Certificate Manager to generate a SSL/TLS certificate that we can use with the Application Load Balancer (ALB) and Target Group (TG) that Fargate set up for us.

- In the AWS console, navigate to Certificate Manager.
- Select "Request a public certificate"
- Input the domain name. In most cases, you'll want to use the format `*.domain.name` to allow subdomains to be scoped into the certificate. This will give you more flexibility if you intend to utilize your domain name for sites other than the `bitwarden_rs` server we are setting up.
- When your request has been submitted, you will need to add the `CNAME` record to the Route 53 hosted zone that we created previously. You should see a button that says `Create record in Route 53`. Click this and check Route 53 that the record was populated.
- It might take at least 30 minutes, but the certificate should eventually be validated and issued.

## ALB / TG / configuration

Once the HTTPS certificate has been validated, we can now set up our infrastructure to utilize it.

- In the AWS console, navigate to EC2.
- On the left navigation column, select "Load Balancers".
- Select the load balancer created by Fargate. Then click on the "Listeners" tab. By default, Fargate sets up the listener for `HTTP: 80`.
- Click "View/edit rules" on the `HTTP: 80` rule and change the rules to `redirect` to `HTTPS: 443` with a status code of `301`.
- Next, we will add a HTTPS listener. In the Click "Add listener" and set up the rule to `forward` to the target group set up by Fargate. _Note_ - to view your target groups, click on the "Target Groups" link below "Load Balancers" on the left navigation pane within the EC2 module.

## SG configuration

Now, we need to edit our existing Security Groups (SG) to allow HTTPS traffic on port 443.

- In the EC2 module, click on "Security Groups" on the left navigation pane.
- You should see two SGs set up by Fargate - one for ALB and the other for ECS. For both of these, add "Inbound" rules for the following:
  - type: `HTTPS`
  - protocol: `TCP`
  - port range: `443`
  - source: `0.0.0.0/0`

## Test it out!

Now that we have set up our AWS infrastructure to handle traffic to the `bitwarden_rs` container via HTTPS, let us test it out.

In your browser, navigate to the custom URL (subdomain) that you set up in Route 53. The Bitwarden login screen should appear. Success!

You can now use the Bitwarden CLI, browser app and mobile apps to communicate with the Fargate container.
