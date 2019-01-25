---
layout: post
title: S3 - Temporary Access Key
categories: [server]
tags: [server]
published: false
fullview: true
---

https://docs.aws.amazon.com/AmazonS3/latest/dev/RESTAuthentication.html

The Amazon S3 REST API uses the standard HTTP Authorization header to pass authentication information.

`Authorization: AWS AWSAccessKeyId:Signature`

https://forums.aws.amazon.com/thread.jspa?threadID=112435

https://www.npmjs.com/package/aws-cloudfront-sign

https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-signed-urls.html

https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html


khang:7@j&uJSC!e*yz&76

khang:"}J5sFWwaxb2qbU]



### Step:

- Create private s3 bucket
- Create cloudfront distribution pointing to this bucket
- Creating CloudFront Key Pairs for Your Trusted Signers (can only be done by root account)
- Specifying the AWS Accounts That Can Create Signed URLs and Signed Cookies (Trusted Signers)
