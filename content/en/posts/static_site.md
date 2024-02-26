+++
title = 'Deploying a static site on AWS S3'
date = 2024-02-23T22:12:55-08:00
draft = false
toc = true
tocOpen = true
+++

# The beginning
In my software engineering class, I have been tasked with building a portfolio website. Though the focus of the project is how I implement software engineering best practices, I have taken this opportunity to learn about the cloud. 

***

## Register a domain with Route 53

<p>AWS [Route 53](https://aws.amazon.com/route53/) is a scalable Domain Name System (DNS). 
First, register a custom domain through Route 53. I was able to register my custom domain for $13 in a matter of minutes.</P>

![Registering domain](/posts/images/static_site/domain_registration.png "Registering domain")

<br>

***

## What is AWS S3
AWS [S3](https://aws.amazon.com/s3/) is an object storage service used to store data for a variety of different use on AWS. In this case, I am using S3 to store and host my static website.

### 1. Create a bucket
Create two buckets, one for the root domain <code>(yoursite.com)</code> and subdomain <code>\(www.yoursite.com)</code>.

- Access <b>Amazon S3</b> > <b>Buckets</b>, select <b>Create buckets</b>. Select a region close to your location to minimize cost and latency. Name your buckets appropriately to the examples above.
![Bucket 1](/posts/images/static_site/s3_bucket_1.png "Bucket 1")
- Leave <b>Public Access settings</b> to <b>Block</b> <i>all</i> <b>public access</b> for security reasons so that your files are not available for the public to access.
![Bucket 2](/posts/images/static_site/s3_bucket_2.png "Bucket 2")
- Feel free to add a tag to your bucket so that you can manage and organize your buckets.
![Bucket 3](/posts/images/static_site/s3_bucket_3.png "Bucket 3")
- Select the encryption that you prefer and hit <b>Create bucket</b> to create your new bucket.
![Bucket 4](/posts/images/static_site/s3_bucket_4.png "Bucket 4")


### 2. Configure root domain bucket and subdomain bucket
<p>First configure the root domain bucket.</p>

- Access <b>Amazon S3</b> > <b>Buckets</b> > <i>Bucket Name</i>, select <b>Properties</b>.
- Under <b>Static website hosting</b>, select <b>Edit</b>.
- Enable <b>Static website hosting</b>.
- Specify the home or default page of the website in <b>Index document</b> by entering the name of the index document, typically <code>index.html</code>.
-  Save changes.
![Root Domain](/posts/images/static_site/root_domain_1.png "Root Domain 1")
- Under <b>Static website hosting</b>, verify the <b>Endpoint</b>. It should have generated a link for your static website.
![Root Domain](/posts/images/static_site/root_domain_2.png "Root Domain 2")
<p>Next, configure the root domain bucket.</p>

- Access <b>Amazon S3</b> > <b>Buckets</b> > <i>Bucket Name</i>, select <b>Properties</b>.
- Under <b>Static website hosting</b>, select <b>Edit</b>.
- Enable <b>Static website hosting</b>.
- Select <b>Redirect request for an object</b>
- Enter the root domain (yoursite.com) under <b>Host name</b>.
- Select <b>https</b> and save changes.
![Subdomain](/posts/images/static_site/subdomain.png "Subdomain")

### 3. Upload website content to S3
Now it's time to upload the content of your website. For my site, I will be uploading a static website that I have built using HTML and CSS.

- Acess <b>Amazon S3</b> > <b>Buckets</b> > <i>Bucket Name</i>, select <b>Upload</b>.
- Upload the content from your local director to S3.
![Upload](/posts/images/static_site/upload_to_bucket.png "Upload")
- When you have successfully uploaded your content, you should see a success notification.
![Upload Status](/posts/images/static_site/upload_status.png "Upload Status")

### 4. Update Public Access and attach a bucket policy
<p>To allow for users to view the contents of your bucket, you will first need to update <b>public access</b> and provide a bucket policy.</p>

- Acess <b>Amazon S3</b> > <b>Buckets</b> > <i>Bucket Name</i>, select <b>Permissions</b>.
- Under <b>Block public access (bucket settings)</b>, select Edit and uncheck <b>Block</b> <i>all</i> <b>public access</b>.
- Save changes.
![Public Access](/posts/images/static_site/unblock_public_access.png "Public Access")
<p>Next is attaching a bucket policy that will allow public <i>read access</i>.</p>

- Under <b>Bucket policy</b>, select <b>Edit</b>.
- Copy the following code into the JSON, changing <code>Your-Bucket-Name</code> to your bucket name.
```
{
   "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::Your-Bucket-Name/*"
            ]
        }
    ]
}
```
- Save changes.
![Bucket Policy](/posts/images/static_site/bucket_policy.png "Bucket Policy")

### 5. Connect the custom domain name to root domain and subdomain
<p>You are almost done. The final step is to create alias records that you add to the hosted zone of your domain maps. This is done through Route 53, where the mapping between the alias records and the IP addresses where the Amazon S3 buckets reside.</p>

- Acess the Route 53 console. Choose <b>Hosted zones</b>. Select the hosted zone that matches your domain name.
- Select <b>Create record</b>. Then select <b>Switch to wizard</b>.
![Route 53 0](/posts/images/static_site/route_53_0.png "Route 53 0")
- Select <b>Simple routing</b>.
<br>
![Route 53 1](/posts/images/static_site/route_53_1.png "Route 53 1")

First, you must create an alias record for your root domain.
- Select <b>Define simple record</b>. For <b>Record name</b> of the root domain, leave blank. 
- In <b>Record type</b>, select <b> A - Routes traffic to an IPv4 address and some AWS resource</b>.
- In <b>Value/Route traffic to</b>, select <b>Alias to S3 website endpoint</b>. Select the region. Select the S3 bucket where your site is stored.
- Select <b>No</b> for <b>Evaluate target health</b>.
- Select <b>Define simple record</b> when you are done.
![Route 53 2](/posts/images/static_site/route_53_2.png "Route 53 2")

Now create an alias record for your subdomain.
- Select <b>Define simple record</b>. For <b>Record name</b> of the subdomain, type <code>www</code>.
- Configure the rest of <b>Define simple record</b> the same as you would your root domain.
![Route 53 3](/posts/images/static_site/route_53_3.png "Route 53 3")

### 6. Test the website
<p>Go to your web browser and test your custom domain and subdomain by entering the appropriate URLs. Congratulations on deploying a static website to AWS.</p><br>

***

## My architecture

<p>Here is the architecture of my portfolio site.</p>

![My architecture](/posts/images/static_site/architecture_portfolio.png "My architecture")

In the future, I will improve upon this architecture by using CodePipePlines, Certificate Manager, and CloudFront. AWS [CodePipeline](https://aws.amazon.com/codepipeline/) is a CI/CD service. It will automate continuous delivery from GitHub to update my S3 bucket. AWS [Cloud Manager](https://aws.amazon.com/certificate-manager/) provisions and manages SSL/TLS certificates for AWS services and connected resources. It will enable <code>https://</code> traffic for my site. AWS [CloudFront](https://aws.amazon.com/cloudfront/) is a content delivery network (CDN) serice. It will speed up my site by making my site files available from data centers around the world as well as provide security by setting up an SSL certificate to enable my site to use HTTPS.

I hope you enjoyed this guide as much as I enjoyed learning more about AWS.