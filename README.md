# What is the purpose of this project?

This project currently has two main purposes:

1. Reduce the number of manual steps to deploy a secure&mdash;more secure than the defaults anyway&mdash;OpenVPN Access Server.
2. Utilize and explain in detail often glossed over features of AWS that can protect AWS newbies from themselves. 

# Why would I want to use this AWS CloudFormation template?

This YAML AWS CloudFormation template will help you deploy a more secure OpenVPN Access Server (Community Edition) instance using AWS CloudFormation automation.

Although the fine folks at OpenVPN maintain a somewhat updated [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) there are some "gotchas" for folks that are new to AWS and/or OpenVPN AS.

This AWS CloudFormation template will help you quickly deploy a reasonably secure OpenVPN AS instance and possibly teach you some features of AWS that you don't know are available or useful.

# Which AWS regions does this template support?

The [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) has a list of AMIs to use but not all regions are listed since the document appears to only be updated when there is a new release for OpenVPN AS Community Edition.

---

#### QUICK TIP
If you have AWS CLI installed and access keys configured you can query all regions for the correct AMI. Here is a long but one-liner example of how to query from the OS X terminal:

```bash
for REGION in `aws ec2 describe-regions --output text | cut -f3`; do echo "Listing instances in region: $REGION..." && aws ec2 describe-images --owners aws-marketplace --filters "Name=product-code,Values=f2ew2wrz425a1jagnifd02u5t" --query 'Images[?CreationDate>=`2016-10`].{ID:ImageId,DATE:CreationDate}' --region $REGION --output text; done
```
The `--filter` for `Values=f2ew2wrz425a1jagnifd02u5t` was taken from an image that was launched using one of the OpenVPN recommended AMI IDs. It appears that using AMIs with this value baked into the image will let you deploy instances without having to *manually* subscribe to the OpenVPN AS product in the AWS Marketplace.

The `--query` for `` ?CreationDate>=`2016-10` `` simply uses the year and month listed in the above mentioned quick start guide.

* Instructions on how to install the AWS CLI [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html).
* Online reference for AWS CLI `aws ec2` commands [here](http://docs.aws.amazon.com/cli/latest/reference/ec2/index.html#cli-aws-ec2).

---

Below are the AMIs that were generated with the AWS CLI query outlined above and used in this project's CloudFormation template:

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-cli-query.jpg "Example aws ec2 describe-instances AWS CLI command")

| Friendly Region Name | Region ID | Image ID  |
| --- |---| ---|
| Asia Pacific (Mumbai) | ap-south-1 |ami-066f1b69 |
| EU (London) | eu-west-2 |ami-86c3c9e2 |
| EU (Ireland) | eu-west-1 | ami-07783674 |
| Asia Pacific (Seoul) | ap-northeast-2 | ami-b6e733d8 |
| Asia Pacific (Tokyo) | ap-northeast-1 | ami-2f66c04e |
| South America (São Paulo) | sa-east-1 | ami-283fa244 |
| Canada (Central) | ca-central-1 | ami-24ad1f40 |
| Asia Pacific (Singapore) | ap-southeast-1 | ami-d72a8cb4 |
| Asia Pacific (Sydney) | ap-southeast-2 | ami-5e3b063d |
| EU (Frankfurt) | eu-central-1 | ami-3f788150 |
| US East (N. Virginia) | us-east-1 | ami-44aaf953 |
| US East (Ohio) | us-east-2 | ami-ae3e64cb |
| US West (N. California) | us-west-1 | ami-fa105b9a |
| US West (Oregon) | us-west-2 | ami-e8d67288 |
| China Beijing | cn-north-1 | ami-XXXXXXXX |
| AWS GovCloud | us-gov-west-1 | ami-XXXXXXXX |

# How do I deploy this template?

1. Login to your AWS account and select the region that you want to deploy the OpenVPN AS instance.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-login-oregon.jpg "Example logging into AWS console and selecting a region.")

2. Make sure that you have an EC2 Key Pair configured. A key pair must be configured and selected before launching the CloudFormation template otherwise the launch will fail. You can find detailed instructions on the different ways to create key pairs from AWS [here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html).

3. Click the **Launch Stack** button below to go directly to the CloudFormation service in the selected region of your AWS account.

[![Launch CloudFormation Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png
)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=openvpnas&templateURL=https://s3-us-west-2.amazonaws.com/github.automated-openvpnas/automate-openvpnas.yml)

4. 






