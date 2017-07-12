# What is the purpose of this project?

This project currently has two main purposes:

1. Reduce the number of manual steps to deploy a secure&mdash;more secure than the defaults anyway&mdash;OpenVPN Access Server.
2. Utilize and explain in detail often glossed over features of AWS that can protect AWS newbies from themselves. 

# Why would I want to use this AWS CloudFormation template?

This AWS CloudFormation template will help you deploy a more secure OpenVPN Access Server (Community Edition) instance using AWS CloudFormation automation.

Although the fine folks at OpenVPN maintain a somewhat updated [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) there are some "gotchas" for folks that are new to AWS and/or OpenVPN AS.

This AWS CloudFormation template will help you quickly deploy a reasonably secure OpenVPN AS instance and possibly teach you some features of AWS that you don't know are available such as AWS EC2 Systems Manager.

# Which AWS regions does this template support?

All regions listed in the [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) are included.

# How do I deploy this template?

1. Login to your AWS account and select the region that you want to deploy the OpenVPN AS instance.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-login-oregon.jpg "Example logging into AWS console and selecting a region.")

2. Make sure that you have an EC2 Key Pair configured. A key pair must be configured and selected before launching the CloudFormation template otherwise the launch will fail. You can find detailed instructions on the different ways to create key pairs from AWS [here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html). If you want to spice up your security a bit more generate your own key pairs and password protect them. Then store the private key separately from the password.

3. Click the **Launch Stack** button below to go directly to the CloudFormation service in the selected region of your AWS account.

[![Launch CloudFormation Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png
)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=openvpnas&templateURL=https://s3-us-west-2.amazonaws.com/github.automated-openvpnas/automate-openvpnas.yml)

4. You will now see the **Create Stack** section of CloudFormation. The most important thing to confirm on this screen is the region. Click **Next**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-create-stack.jpg "Create Stack in CloudFormation")

5. You can stick with the defaults here but try and enter a hostname for the instance. If you plan on access the web console using a DNS name and/or using custom TLS certificates (eg. Let's Encrypt) you will need a proper hostname FQDN.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-specify-details-p1.jpg "Specify details - Part 1")

6. These highlighted parameters are important so make sure to set them. If you don't select an **EC2 Key Pair** the stack will fail. For **OpenVPN AS Management IP** please specify a source IP instead of 0.0.0.0/0. For **OpenVPN AS Admin Username** set a unique username and avoid the default. Finally, you have a choice to set a password using the AWS Parameter Store or after logging into the OpenVPN AS web Admin Login screen. Click **Next**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-specify-details-p2.jpg "Specify details - Part 2")

7. There isn't much to do at the **Options** screen. Feel free to add some custom tags (good practice by the way) otherwise click **Next**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-create-stack-options.jpg "Create Stack Options")

8. This is the final confirmation screen before launching the stack. You can confirm the parameter settings that you chose. Also note that the stack will rollback on failure so you won't have any orphaned resources if something goes whacky. Make sure to check the acknowledgement check box and click **Create**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-create-stack-final.jpg "Create Stack - Create")

9. Hopefully nothing borked out and after about 5 minutes you should see a nice green status of **CREATE_COMPLETE**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-create-complete.jpg "Create Stack - Complete")

10. Click on the **Overview** dropdown and select **Outputs**.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-stack-outputs.jpg "Get Stack Outputs")

11. Click on the hyperlink to have a new tab open that will take you to the OpenVPN AS web Admin Login page. Remember that at this point, even though you might have entered a custom hostname parameter you can only access the Admin Login page by IP address since DNS is not configured yet. 

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-stack-outputs-login-url.jpg "Get Web Admin Login URL")

12. In the example below Chrome will spit out a warning&mdash;this is because the instance is using a self-signed certificate. Go ahead and proceed or whitelist depending on which browser you are using.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-connection-not-private.jpg "Get Your Connection Is Not Private Example")

13. Congratulations if you see the Admin Login page! Enter the username and either the default password of *ChangeMePlease* or the secret string you configured if using SSM Parameter Store.

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-admin-login.jpg "OpenVPN AS Admin Login")

14. Read the EULA and click **Agree** you filthy liar. 

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-eula-agree.jpg "OpenVPN AS EULA")

15. You now have a functioning OpenVPN AS server running on AWS to start playing around with!

![alt text](https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/automated-openvpnas-readme-login-success.jpg "OpenVPN AS Successful Login")



