
𝐇𝐨𝐰 𝐭𝐨 𝐜𝐫𝐞𝐚𝐭𝐞 𝐚 𝐉𝐞𝐧𝐤𝐢𝐧𝐬 𝐬𝐞𝐫𝐯𝐞𝐫 𝐮𝐬𝐢𝐧𝐠 𝐓𝐞𝐫𝐫𝐚𝐟𝐨𝐫𝐦


In this lesson I will be walking you through how to create a Jenkins server using Terraform. One EC2 instance will be deployed in the default VPC. We will bootstrap the instance with a script that will install and start Jenkins as well as Java 11. Next, we will create and assign a security group to the EC2 that will allow traffic on port 22 from the ip and allows traffic from port 8080. A S3 bucket will be created for the Jenkins Artifacts that will not be accessible to the public. In the browser, we will verify that we can reach the Jenkins that was installed on port 8080.We will create code on a main.tf file with hardcoded data and the user data in a shell script file.

What is Jenkins? Jenkins is an automation server that is also open source. It assists with building and testing the parts of software development with its continuous integration and continuous delivery. Many developers love it because it makes it easier to integrate changes to a project thus making it easier for users to obtain a fresh build.

Prerequisites:

Knowledge of Terraform

Knowledge of Jenkins

Official Jenkins Documentation: User Handbook Overview (jenkins.io)

Terraform Documentation: Overview — Configuration Language | Terraform | HashiCorp Developer

AWS Console Account

IDE (I will be using Cloud9)

Step 1: Create the IDE.

Create Environment> Name> New EC2 Instance> t2.micro> Amazon Linux 2> 30 mins > SSM> Default VPC > Create

IDE created successfully.

Step 2: Create two files in the IDE.

Save the first file as main.tf.

The main.tf file contains the main configuration for a Terraform project, as well as the resources that need to be created, updated, or deleted. It also contains the associated settings and dependencies.

Save the second file, which is a shell script, as install_jenkins.sh. This is where we will store our user data and it contains a set of commands to install Jenkins on a Linux system.

Java is required with Jenkins in order for it to work, yet it’s not automatically included with all distributions and some versions are not compatible with Jenkins. In this lesson we are using Linux/Redhat so it’s compatible. If you decide to use another distribution, make sure it’s compatible Java.


Step 3: Cd into your directory and add code to the file.

The provider is Amazon Web Services, and the region is us-east-1.


T2.micro is the instance type that was selected as it provides just enough CPU performance and is a cost-effective way to run workloads in the cloud. You will want to always use the free eligible tier if possible.


Replace “ami-id#” with the ami id of your choice. You don’t have to pretend to spin up an EC2 instance in order to locate the id. You can locate the information you need by going to the AMI catalog on the left navigational panel.



Next, you will define a variable to attach the instance to your default VPC. You will need to locate your default VPC and replace with your ID in that section.



Use the code below to create the EC2 instance.

Name your instance and key pair within the code. Also next to “user_data” enter your shell scripts file name.


Use the code below to create a security group and that allows traffic on port 22 from your Ip address as well as allows traffic from port 8080.

This HashiCorp Configuration Language (HCL) code allows:

inbound traffic on port 8080 from all IP addresses.
inbound traffic on port 22 from a specific IP address.
inbound traffic on port 443 from all IP addresses.


This code creates the S3 bucket in which you will restrict access to the public.

Be sure to use a globally unique bucket name like I did below.



*Don’t run the code until after the you complete the next step. *

Step 4: Use your existing access and secret access keys for the security credentials. You may also create a key pair in the console if you prefer to SSH to connect to your instance in a terminal.

Run export AWS_ACCESS_KEY_ID=“insertkeyhere”

Run export AWS_SECRET_ACCESS_KEY=“insertkeyhere”


Step 5: Build the infrastructure.

terraform fmt: is a good practice to run before committing any changes to your Terraform code. It will properly align the code and ensure that the code is consistent.


terraform init: initializes a Terraform working directory.


terraform validate: validates the configuration files and checks them for errors like syntax errors, invalid resources, and missing required arguments.


terraform plan: allows you to review and approve the changes before they are made thus preventing accidental changes to your infrastructure.


terraform apply: creates or updates resources in your infrastructure to match the Terraform configuration files.

It is important to note that this command cannot be undone. Therefore, it is important to review the executable plan to ensure that you understand and approve of the changes before running terraform apply.

Step 6: Verify Jenkins is running by using sudo systemctl status jenkins.


Step 7: Verify you’re able to see what you created in the console.

Ec2 instance:

The instance “TerraformJenkins” has been created.


Security Group:

“Jenkins-202304…” is the new SG.

Verify the inbound rules match what’s inside the code.


S3 Bucket:

“Jenkins4123luitartifacts” is the name of the newly created S3 bucket.

By default, S3 buckets are private and can only be accessed by the AWS account that initiated them or by IAM users within that account who have been granted permission to access. Please note that any user with the necessary permissions can grant access to other users or roles within the same AWS account or to users or roles in other AWS accounts.


For best practice make sure only users with authority have access to the S3 buckets as that can help to prevent unauthorized access and breaches of data. Hover over the statement “Objects can be public,” and you will find the below message. As it states, in order to block public access, you can enable “Block Public Access.”


Image by Ashley Myrick-Ellis
Step 8: Verify that you're able to reach the Jenkins installation by port 8080 in your web browser.

Copy the public IP address from the EC2 instance.



Paste the IP address in a new window as IPaddress:8080

If everything was done correctly, you should get this:



When complete the best way to dissemble is to use the command “terraform destroy”


