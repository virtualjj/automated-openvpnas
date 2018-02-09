# AWS AUTOMATED OpenVPN AS (ACCESS SERVER)

- [SUPPORTED REGIONS](#supported-regions)
- [CAVEATS](#caveats)
- [PREPARATION](#preparation)
- [STACK DEPLOYMENT](#stack-deployment)
- [LICENSE](#license)

## PURPOSE

This project takes the basic steps maintained at [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) at bit further by automating the entire deployment with a few security tweaks using AWS CloudFormation. This stack also configures VPC Flow Logs and EC2 Systems Manager

## SUPPORTED REGIONS

All regions listed in the [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/) are supported.

## CAVEATS

While this CloudFormation template will get you started a bit quicker than OpenVPN's [Amazon Web Services EC2 Community Appliance Quick Start Guide](https://docs.openvpn.net/how-to-tutorialsguides/virtual-platforms/amazon-ec2-appliance-ami-quick-start-guide/), depending on what you are trying to accomplish further configuration will probably be required.

Also, I haven't included any examples of how to connect to the OpenVPN AS instance after it is deployed so the assumption is that if you are deploying an OpenVPN AS instance you know how to configure and connect the VPN client.

Finally, if you are looking to use Let's Encrypt take a look at my other Github project [here.](https://github.com/virtualjj/automated-openvpnas-cloudflare-letsencrypt)

### PREPARATION

You will need an EC2 Key Pair in order to successfully deploy this CloudFormation stack. Basic insructions on how to do this can be found [here.](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair) A more secure method would be to create your own, password protect the private key, store the keys separately from the password (eg. passphrase in [KeePass](http://keepass.info/), key pairs stored locally with backups), and upload the public key to AWS. Basic instructions to get you started with creating your own key pairs can be found [here.](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)

This stack configures EC2 Systems Manager so you can use the *Secure String* feature of [AWS Parameter Store](http://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html) to pass your OpenVPN AS administrator password relatively securely during deployment of the instance. You don't have to use Parameter Store but in case you didn't know, when you pass parameters in **Userdata** all commands are passed as plaintext. This is generally a concern since anyone that has access to the instance can see decrypted passwords by either issuing the `curl http://169.254.169.254/latest/user-data` command or looking in the logs. I recommend that you store your OpenVPN AS password in AWS Parameter Store at least for the initial deployment.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/prepstep-000-parameter-store-example.jpg" alt="Parameter Store secure string example." height="75%" width="75%">
</p>

### STACK DEPLOYMENT

1. Login to your AWS account and select the region that you want to deploy the OpenVPN AS instance. This is very important as its easy to accidentally open tabs in other regions.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-000-login-region-check.jpg" alt="Make sure in the intended AWS region." height="75%" width="75%">
</p>

2. Click the **Launch Stack** button below to go directly to the CloudFormation service in the selected region of your AWS account.

[![Launch CloudFormation Stack](https://s3.amazonaws.com/cloudformation-examples/cloudformation-launch-stack.png
)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=openvpnas&templateURL=https://s3-us-west-2.amazonaws.com/github-automated-openvpnas/automated-openvpnas.yml)

3. You will now see the **Create Stack** section of CloudFormation. The most important thing to confirm on this screen is the region. Click **Next**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-003-region-check-again.jpg" alt="Double-check in the intended AWS region." height="75%" width="75%">
</p>

4. You can stick with the defaults here but try and enter a hostname for the instance. If you plan on accessing the web console using a DNS name and/or using custom TLS certificates (eg. Let's Encrypt) you will need a proper hostname FQDN anyway so this will save you a step in the future.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-004-enter-fqdn.jpg" alt="Double-check in the intended AWS region." height="75%" width="75%">
</p>

5. The parameters highlighted in red are important so make sure to set them. If you don't select an **EC2 Key Pair** the stack will fail. For **OpenVPN AS Management IP** please specify a source IP (i.e. YOUR IP). If you absolutely want to open this to the world you will need to modify the template directly. For **OpenVPN AS Admin Username** set a unique username and avoid the default. Finally, as mentioned in the **Preparation** section above you have a choice to set a password using the AWS Parameter Store or after logging into the OpenVPN AS web Admin Login screen. Click **Next**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-005-basic-setup.jpg" alt="Choose a key pair, source IP, and admin username/password." height="75%" width="75%">
</p>

6. There isn't much to do at the **Options** screen. Feel free to add some custom tags (good practice by the way) otherwise click **Next**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-006-options-tags.jpg" alt="Options screen, not much to do here." height="75%" width="75%">
</p>

7. This is the final confirmation screen before launching the stack. You can confirm the parameter settings that you chose. Also note that the stack will rollback on failure so you won't have any orphaned resources if something goes whacky. Make sure to check the acknowledgement check box and click **Create**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-007-confirm-before-launch.jpg" alt="Final confirmation before lauching the CloudFormation stack." height="75%" width="75%">
</p>

8. Hopefully nothing borked out and after about 5 minutes you should see a nice green status of **CREATE_COMPLETE**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-008-stack-launch-success.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

9. It will be tempting to try logging into the instance and start doing "stuff". Be patient and wait for the instance status checks to complete. Also, if you try logging into the instance too early you'll break the initial ***ovpn-init --ec2*** script which is responsible for unattended setup.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-009-not-ready-status.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-009.5-checks-passed.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>


10. Head back to CloudFormation and click on the **Overview** dropdown and select **Outputs**. Click on the hyperlink to have a new tab open that will take you to the OpenVPN AS web admin Login page. Remember that at this point, even though you might have entered a custom hostname parameter you can only access the Admin Login page by IP address since DNS is not configured yet.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-010-stack-outputs.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

11. In the example below Chrome will spit out a warning&mdash;this is because the instance is using a self-signed certificate. Go ahead and proceed or whitelist depending on which browser you are using.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-011-cert-warning.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

12. Congratulations if you see the **Admin Login** page! Enter the username and either the default password of ***ChangeMePlease*** or the password that you saved in the Parameter Store secure string.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-012-initial-login.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

13. Read the EULA and click **Agree** you filthy liar (apologies if you actually do read EULAs).

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-013-accept-eula.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

14. You now have a fully functional OpenVPN AS server. Have a look around and confirm some of the settings such as the **Server Name**.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-014-working-openvpnas.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

15. Not that it matters with a self-signed TLS certificate but notice that only TLS 1.2 is enabled.

<p align="center">
<img src="https://github.com/virtualjj/automated-openvpnas/blob/master/images/readme/deploystep-015-only-tls-1.2-.jpg" alt="Confirm successful stack launch." height="75%" width="75%">
</p>

## LICENSE

This project is licensed under the [MIT License](https://opensource.org/licenses/MIT) - see the [LICENSE.md](LICENSE.md) file for details
