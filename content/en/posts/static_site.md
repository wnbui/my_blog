+++
title = 'Deploying a static site on AWS S3'
date = 2024-02-22T22:12:55-08:00
draft = true
toc = true
tocOpen = true
+++

# The beginning
 In 2019, while on deployment in the Western Pacific, 

[contracts](https://www.nytimes.com/2022/12/07/business/pentagon-cloud-contracts-jwcc.html)
***

## Register a domain with Route 53

AWS [Route 53](https://aws.amazon.com/route53/) is a scalable Domain Name System (DNS). 
First, register a custom domain through Route 53. I was able to register my custom domain for $13 in a matter of minutes.

![Registering domain](/posts/images/domain_registration.png "Registering domain")

***

## What is AWS S3
AWS [S3](https://aws.amazon.com/s3/) is an object storage service used to store data for a variety of different use on AWS. In this case, I am using S3 to store and host my static website.

### 1. Create a bucket

Create two buckets, one for the root domain (yoursite.com) and subdomain \(www.yoursite.com). Pick the region where your endpoint will be, preferably some place close to you to minimize latency and costs
![Bucket 1](/posts/images/s3_bucket_1.png "Bucket 1")

Leave <b>Public Access settings</b> to <b>Block</b> <i>all</i> <b>public access</b> for security reasons so that your files are not available for the public to access.
![Bucket 2](/posts/images/s3_bucket_2.png "Bucket 2")

Feel free to add a tag to your bucket so that you can manage and organize your buckets.
![Bucket 3](/posts/images/s3_bucket_3.png "Bucket 3")

Select the encryption that you prefer and hit <b>Create bucket</b> to create your new bucket.
![Bucket 4](/posts/images/s3_bucket_4.png "Bucket 4")

### 2. Configure root domain bucket and subdomain bucket


***
## My architecture

Here is the architecture of my portfolio site.
![My architecture](/posts/images/architecture_portfolio.png "My architecture")

In the future, I will improve upon this architecture by using CodePipePlines and CloudFront. AWS [CodePipeline](https://aws.amazon.com/codepipeline/) is a CI/CD service. It will automate continuous delivery from GitHub to update my S3 bucket. AWS [CloudFront](https://aws.amazon.com/cloudfront/) is a content delivery network (CDN) serice. It will speed up my site by making my site files available from data centers around the world.

I hope you enjoyed this guide as much as I enjoyed learning more AWS.