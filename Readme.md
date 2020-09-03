## Deploying Wordpress on AWS Infrastructure with Ansible & Terraform

**Create an EC2 T2.micro instance**  
**T2.micro instance of CentOS 7 in ap-south-1 Availability zone.**  
**Get the terminal access of CentOS 7 server, Also name it as GAT-Primary** 
 
---------------------------------------------------------------------------------
**Install pre-requisites for the setup.**

- `sudo yum install epel-release -y`
- `sudo yum install git python python-devel python-pip openssl -y`
- `sudo yum install wget nano unzip -y`
---------------------------------------------------------------------------------
**Perform a Full Update Check.**

- `sudo yum update -y`

---------------------------------------------------------------------------------
**Install Terraform**

- `sudo curl -O https://releases.hashicorp.com/terraform/0.13.1/terraform_0.13.1_linux_amd64.zip`
- `sudo unzip terraform_0.13.1_linux_amd64.zip -d /usr/local/bin`
- `terraform -v`
---------------------------------------------------------------------------------
 **Install Ansible, Also create a backup of Original Hosts file**

- `sudo yum install ansible -y`
- `ansible --version`
- `sudo cp /etc/ansible/hosts /etc/ansible/hosts.bkp`
---------------------------------------------------------------------------------
**Install Python-pip module & upgrade the same**

- `sudo yum install git python-pip `   
- `sudo pip install --upgrade pip`
---------------------------------------------------------------------------------
**Install AWS CLI Tool for Auto-Deployment on AWS infra.**

- `sudo pip install awscli --upgrade`

---------------------------------------------------------------------------------
**Go to IAM Service in your AWS Account** 

[IAM Service for AP-South-1](https://console.aws.amazon.com/iam/home?region=ap-south-1)  

1. Add a User, with a name awsadmin, with an access type of Programmatic access only.
2. Set permissions via "Attach Existing Policies Directly" Tab, with an Administrator Access
3. Ignore the Tags & Create the User
4. Note down the Username, Access Key ID & Secret Access Key
                  

| AWS Fields  | Values |
| ------------- | ------------- |
| User  | **awsadmin**  |
| Access Key ID  | **KeyID**  |
| Secret Access Key  | **Secret Key**  |
---------------------------------------------------------------------------------
**Configure AWS CLI Tool for Connection**

- `aws configure`

**Enter the Access Key ID & Secret Access key when asked
Enter preferred region & format. - ie. ap-south-1 & json**

---------------------------------------------------------------------------------
**Uncomment "host_key_checking = false"**

- `sudo nano /etc/ansible/ansible.cfg`

---------------------------------------------------------------------------------
**Install the following roles (use sudo if required)**

- ` ansible-galaxy install geerlingguy.php-versions geerlingguy.php geerlingguy.php-mysql geerlingguy.repo-remi `

---------------------------------------------------------------------------------
**SSH Agent Setup**

- `ssh-keygen # Accept Defaults`
- `ssh-agent bash`
- `ssh-add ~/.ssh/id_rsa`
- `ssh-add -l # Verify the Status`
---------------------------------------------------------------------------------
**Let us start with the actual case study**  
**Note down the Git Hub repo for Terraform, & ansible files**  
GitHub Repo -: [https://github.com/PratikVasagadekar/awsansibleterraform.git](https://github.com/PratikVasagadekar/awsansibleterraform.git "https://github.com/PratikVasagadekar/awsansibleterraform.git")

- `mkdir awscasestudy`
- `cd awscasestudy`
- `git clone https://github.com/PratikVasagadekar/AWSAnsibleTerraformWordpress.git`

**Now edit the .tfvars file with aws specific values.**

- `cd awsansibleterraform/scripts/`

**dev_ami can be found in any aws instances by the name AMI ID**  
**Change the User in public_key_path depending upon the OS.**  

		###### Sample terraform.tfvars file

		aws_profile       = "ProfileName"
		aws_region        = "ap-south-1"
		db_instance_class = "db.t2.micro"
		dbname		  = "wordpressdb"
		dbuser		  = "username"
		dbpassword	  = "username123"
		key_name          = "id_rsa"
		public_key_path   = "/home/centos/.ssh/id_rsa.pub"
		dev_instance_type = "t2.micro"
		dev_ami		  = "AMI ID"
		cidrs             = {
		  public1	  = "10.1.1.0/24"
		  public2	  = "10.1.2.0/24"
		  private1	  = "10.1.3.0/24"
		  private2	  = "10.1.4.0/24"
		  rds1		  = "10.1.5.0/24"
		  rds2		  = "10.1.6.0/24"
		  rds3		  = "10.1.7.0/24"
		}
---------------------------------------------------------------------------------
**Start Initializing Terraform & Plan the setup**

- `terraform init`

- `terraform plan -out firstsetup`

**Input var.localip - 0.0.0.0/0**

- `terraform apply firstsetup`

**If you wish to destroy the plan**

- `terraform destroy`

**Write down the output after applying the plan**

Outputs:  

| Wordpress Fields  | Values |
| ------------- | ------------- |
| Database_Hostname  | **FQDN:3306**  |
| Database_Name  | **wordpressdb**  |
| Database_Password  | **username**  |
| Database_Username  | **username123**  |
| Wordpress_Address  | **http://xx.xx.xx.xx**  |

---------------------------------------------------------------------------------
**Visit the Wordpress IP Address**  
**Enter the Output Details in Wordpress Init Screen**  
