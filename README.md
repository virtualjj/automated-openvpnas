# Automated OpenVPN Access Server on AWS

This AWS CloudFormation template attempts to automate the steps outlined at the [OpenVPN EC2 AMI Quick Start Guide.] (https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/)

In addition, a generic user of `router` and associated .ovpn file will be generated to an S3 bucket for quicker configuration on a router like DD-WRT.

Currently the supported regions are:

1. us-east-1 (N. Virginia)
2. us-west-1 (N. California)
3. us-west-2 (Oregon)
4. eu-west-1 (Ireland)
5. eu-central-1 (Frankfurt)
6. ap-northeast-1 (Tokyo)
7. ap-southeast-2 (Sydney)

## Disclaimer

This CloudFormation template will create resources (i.e. S3, EIP, EC2) that will possibly incur charges on your AWS bill. Make sure to understand the template before deploying.

## Deployment Steps

1. Review and download this template.
2. Login to your AWS console and go to the region that you want to deploy OpenVPN AS.
3. (Optional) Create your own S3 bucket and upload the template there. Remember that bucket names have to be unique to AWS S3.
4. Open the CloudFormation service in the region you would like to deploy OpenVPN AS.
5. Press the blue **Create New Stack** button.
6. (Optional) If you created your own S3 bucket select the **Specify an Amazon S3 template URL** and enter the URL of the template. Click **Next** to continue.
7. Choose the **Upload a template to Amazon S3** radio button and upload this template then click **Next**
8. Create a **Stack Name** and then complete the Parameters. PLEASE, PLEASE, PLEASE change the password!!!
9. At the **Options** screen click **Next** unless you know what you are doing.
10. At the **Review** screen make sure to check the box next to **I acknowledge that this template might cause AWS CloudFormation to create IAM resources.** at the bottom and then click on the **Create** button.
11. The outputs section of the completed template will display the public IP (Elastic IP) and S3 bucket. Use the public IP to connect to the instance using the custom admin name and password combination that you set. (i.e. https://elasticip/admin) The S3 bucket will contain the .ovpn file for user `router` that you can use to configure your router's OpenVPN client. Note that this user only requires certificate authenticaion and not password + certificate authentication.

## Deletion Steps

1. Delete the .ovpn file from the S3 bucket that was generated.
2. In CloudFormation click the checkbox next to the **Stack Name**.
3. From the **Actions** dropdown select **Delete Stack**.

## Notes About the Parameters

`KeyPairName` A Key pair is always required to start an AWS instance. If you don't have a keypair the template will fail.

`MobileUse` If you plan on using the OpenVPN mobile app (Yes there is one for iOS and Android) you will need to open up a security group to 0.0.0.0/0. When you select **Yes** for this parameter another security group will be created and applied to the OpenVPN AS instance.

`OpenVPNASAccessIP` Chances are you will want to use the OpenVPN AS with your home router especially if you are running DD-WRT or Tomato firmware. To prevent opening your OpenVPN AS to the world use your public IP address here.

`OpenVPNASInstanceType` If you plan on taking advantage of the AWS Free Tier make sure keep the default of **t2.micro**. If you manually try to setup an OpenVPN AS instance you cannot select t2.nano however, you can change the instance size after it has been created. Note that t2.nano does NOT quality for AWS's free tier.

`OpenVPNASAdminUser` Choose a unique admin username here.

`OpenVPNASAdminPW` The template requires a password and is defaulted to Ck6#*3a9. Don't be an idiot and keep this password - set a strong password please. Note that I have opted to require only three special characters because some characters are not supported for passwords in OpenVPN AS for some reason.


## Other Notes

- This is a work in progress and there are many opportunities for further customization.

- An Elastic IP (EIP) will be generated and assigned to the OpenVPN AS instance. If you shutdown or terminate the instance you will be charged for the unused EIP.
 
- Delete the .ovpn file before deleting the stack otherwise stack deletion will fail since the bucket is not empty.

- Make sure to delete the stack to delete all resources created instead of individually removing resources to avoid unexpected AWS charges.

- This template sets the minimum OpenVPN client version to TLS 1.2

- `userdata` script parameters were taken from `/usr/local/openvpn_as/scripts/README.txt` on the OpenVPN AS appliance.

