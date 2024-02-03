**WARNING:** The files may be outdated as of today (June 2019) . Kindly use it with precaution.

# Terraform-ansible-setup
Setting up your complete infrastructure on cloud premises using Infrastructure as a Code

## Prerequisites

* Ansible
* Terraform

| Cloud         | Requirements                           | Operating System                                 |    Region      |
| ------------- |:--------------------------------------:|:------------------------------------------------:|----------------|
| AWS           | *Nil*                                  | Ubuntuv14.04(ami-21766642)/Rhelv7(ami-9a3322f9)  | ap-southeast-2 |
| GCP           | gcloud cli, Apache-Libcloud(==1.2.0)   | Ubuntu 14.04 (ubuntu-1404-trusty-v20170703)      | us-east1-b     |
| Azure         | *Coming Soon*                          |                                                  |                |

## Design

The idea of this project is to make easy deployment of our infrastructure using the concept of Infrastructure as a Code.For this purpose,I have used 2 tier architecture setup consisting of two different tools, Terraform and Ansible.Terraform is used in order to provision the required instances on Cloud.Whereas, ansible is used to configure our application.

![arch2](https://user-images.githubusercontent.com/8342133/28283464-6c37d870-6b4b-11e7-9cf0-ac46aed9c594.png)


## Setup

* [Terraform](#terraform)
* [Ansible](#ansible)

## Sample Video Demonstrations

Sample video output can be found out for Google Cloud Platform [here](https://youtu.be/EE1Z_9F98vU) :

<a href="http://www.youtube.com/watch?feature=player_embedded&v=EE1Z_9F98vU" target="_blank"><img src="http://img.youtube.com/vi/EE1Z_9F98vU/0.jpg" alt="IMAGE ALT TEXT HERE" width="530" height="360" /></a>

### Terraform

For getting started with ansible,choose your cloud premise:

* [AWS-Terraform](#aws-terraform)
* [GCP-Terraform](#gcp-terraform)



#### [AWS-Terraform](#aws-terraform)

ami-9a3322f9 - RedHat 7.3 Image


For AWS usage you can create a universal Access and Secret Key ID at

**IAM Console -> Access Keys (Access Key ID and Secret Access Key)**

Replace your Access ID and Secret Key ID [here](https://github.com/ramitsurana/terraform-ansible-setup/blob/master/terraform/aws/variables.tf)

The main.tf file contains the provisioning of ec2 instance including creation of security group.

##### [GCP-Terraform](#gcp-terraform)

1. Like in AWS,we have security credentials for accessing unlimited resources,in gcp we can use a security key in JSON format in order to use the resources.This can be generated by visiting 

**Google Cloud Dashboard -> IAM & Admin -> Service Accounts -> Choose a Service Account -> Options -> Create Key**

2. Download this json key and keep it under 

**terraform-ansible-setup -> GCP -> YOUR-ACCOUNT-ID.JSON**

*For my reference,I have name it as account.json in my variables.tf file*
 
3. Connect your terminal with gcp via ssh using the following command:

````
$ sudo cat ~/.ssh/id_rsa.pub
````
4. Copy and Paste the above output at 

**Google Cloud Dashboard -> Compute Engine -> Metadata -> SSH Keys -> Add New Key**

5. Install the gcloud cli using :

````
$ curl https://sdk.cloud.google.com | bash
````

5. Make sure to authorize permissions:

````
$ sudo chown -R ${USER} /home/${USER}/.config/gcloud
````

7. Use the below command to verify gcloud cli with your account:

````
$ sudo gcloud auth login
````
You can now check gcloud cli working by running any of the gcloud available commands such as gcloud compute machine-types list etc.

8. Now you can run your commands to kickstart 3 vm instaces (sample1,sample2,sample3) using 

````
$ terraform get
$ terraform plan
$ terraform apply
````

### Ansible
 
In order to use ansible I am using the config at dynamic inventory located at /etc/ansible/ansible.cfg and /etc/ansible/hosts.Here are the changes I made after configuration:

````
[defaults]

# some basic default values...

inventory      = /etc/ansible/hosts
library        = /usr/share/my_modules/
remote_tmp     = $HOME/.ansible/tmp
local_tmp      = $HOME/.ansible/tmp
forks          = 5
poll_interval  = 15
sudo_user      = root
#ask_sudo_pass = True
#ask_pass      = True
#transport      = smart
remote_port    = 22
#module_lang    = C
#module_set_locale = True

# uncomment this to disable SSH key host checking
host_key_checking = False

# if True, make ansible use scp if the connection type is ssh
# (default is sftp)
scp_if_ssh = True

[selinux]
# file systems that require special treatment when dealing with security context
# the default behaviour that copies the existing context or uses the user default
# needs to be changed to use the file system dependent context.
#special_context_filesystems=nfs,vboxsf,fuse,ramfs

# Set this to yes to allow libvirt_lxc connections to work without SELinux.
libvirt_lxc_noseclabel = yes
````

/etc/ansible/hosts file:

````
[local]
127.0.0.1 ansible_connection=local

[ec2]
XX.XX.XX.XX ansible_user=ubuntu

[gce]
XX.XX.XX.XX ansible_ssh_user=ubuntu
XX.XX.XX.XX ansible_ssh_user=ubuntu
XX.XX.XX.XX ansible_ssh_user=ubuntu

[gce1]
XX.XX.XX.XX ansible_ssh_user=ubuntu
````
#### Ansible Playbooks Manual Configurations

[Ref](https://github.com/ansible/ansible/issues/19584) 

````
ssh-agent bash
ssh-add <path to private key>
````

Set *hosts:* parameters according to the cloud provider you want,for example:

````
hosts: aws
hosts: gce
hosts: azure
````

| Files         | AWS                               | GCP                                        |    Azure                                |
| ------------- |:---------------------------------:|:------------------------------------------:|-----------------------------------------|
| consul.yml    | *Nil     *                        | *Nil*                                      |  *Nil*                                  |
| k8s.yml       | *export KUBERNETES_PROVIDER=aws*  | *export KUBERNETES_PROVIDER=gce*           | *export KUBERNETES_PROVIDER=azure*      |

  * [AWS](aws)
  
  You can start by setting up your aws enviornment EC2 instance using ec2-configure.yml playbook present in playbooks directory,using the below command:

```` 
$ ansible all -m ping --ask-pass --ask-sudo-pass
````
  
````
$ sudo ansible-playbook ec2-configure.yml -vv --private-key  <path-to-keypair>
````
  
  * [GCP](gcp)

For running ansible via local machine:

```` 
$ ansible all -m ping --ask-pass --ask-sudo-pass
````

````
sudo ansible-playbook <NAME>.yml --private-key = <NAME-OF-FILE>
````
## License

MIT License
