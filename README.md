# Learning DevOps  - Build AWS AMI image with Packer and Ansible

###### :page_facing_up: Build AWS AMI image with Packer and Ansible

This is repo 2 - Deploying my project. This is used to aid my learning as a DevOps Engineer. 
This will Demo how to Build AWS AMI image with Packer and Ansible

The Ansible script (Playbook.yml) will provision our AMI image we want to create with the following: 
- Setup Nginx as a Reverse proxy for the React App running on port 3000 
- Clone our React App hosted on Github, Install nodejs required to build and run the App  
- Create a systemd service to run the app in the background

After creating our AMI image successful with Packer and Ansible, we will manaually spin up an AWS EC2 instance and use route 53 to point to our domain
- Add a Domain name of your choice and Leverage AWS Route 53 to create DNS Records to point to your hosted project public IP on AWS EC2

## Description of the Process and Steps to Deploy this Project

---
#### Screencast
Check [here](https://drive.google.com/open?id=1YFSUmPDQ3-kaUOnHEHWuSM0h_yIqy-T5) for a recorded screencast showing how the script works.
NOTE: At 15:31 - there was a typo in creating the systemd service, this was recitifed. Kindly check Ansible playbook.yml file.

---

#### How to build an AWS AMI Image using Packer and Ansible
1. To get started, install Packer and Ansible on your computer. using Homebrew on a mac, run the below commands to install:
```
brew install packer
brew install ansible
``` 
2. Packer template - json file that defines one or more builds by configuring various components of Packer.
Packer has sections like `variables`, `builders`,`provisioners` and `post-processors`, we will focus on the first 3.
The variables section - Set user variables here
```
"variables": {
  "aws_access_key": "{{env `AWS_ACCESS_KEY_ID`}}",
  "aws_secret_key": "{{env `AWS_SECRET_ACCESS_KEY`}}"
},
```
To maintain the security and integrity, it's advised that both credentials are exported as global environment variables or save them in a dot env file like below and  source it to make the variables available everytime you run the project.
```
export ACCESS_KEY="SBSKSFJIHBFJBKBOSFK"
export SECRET_KEY=",nfkxlksknksclzncknsslsjd;ksd"
```
The Builders section contains an array of all the builders that packer should use to generate machine images. We will build for AWS
```
"builders": [{
      "type": "amazon-ebs",
      "access_key": "{{user `aws_access_key`}}",
      "secret_key": "{{user `aws_secret_key`}}",
      "region": "eu-west-2",
      "source_ami_filter": {
        "filters": {
          "virtualization-type": "hvm",
          "name": "ubuntu/images/hvm-ssd/ubuntu-bionic-18.04-amd64-server-*",
          "root-device-type": "ebs"
        },
        "owners": ["099720109477"],
        "most_recent": true
      },
      "instance_type": "t2.micro",
      "ssh_username": "ubuntu",
      "ami_name": "packer-sims-pro {{timestamp}}",
      "ami_description": "EC2 AMI Ubuntu 18.04",
      "tags": {
          "Name": "3Fs web-app",
          "role": "fast-food-fe-web_Server"
      }
    }],
```
The Provisioners section contains an array of all the provisioners that packer should use to install and configure software within running machines prior to turning them into machine images. The provisioner definition below, configuring an ansible provisioner to run within the machine being created.
```
"provisioners": [
        {
            "type": "ansible",
            "playbook_file": "playbook.yml"
        }
    ]
```

3. Build the image, by running
```
packer build name_packer_file.json
```
---

#### Instruction on how to use this repo
1. Clone the repository and cd into `Packer-Ansible` directory
```
git clone https://github.com/sekayasin/Packer-Ansible.git
cd Packer-Ansible
```
2. Contents of Packer-Ansible
```
playbook.yml - Ansible script defined in the provisioners section, it will be used to provision the machine image 
sims-packer-auto.json - Is the packer json file that will be build to create the AMI Image
```
3. To Build the image, make sure you have added the AWS_ACCESS_KEY and SECRET_ACCESS, then run
```
packer build sims-packer-auto.json
```
NOTE: Kindly watch the screencast attached above.

---

#### Tools used
1. AWS   -   Amazon Web Services is a subsidiary of Amazon that provides on-demand cloud computing platforms to individuals, companies, and governments, on a paid subscription basis.
2. Amazon EC2  - Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides resizable compute capacity in the cloud. 
3. Amazon Route 53 - Route 53 is a scalable and highly available Domain Name System (DNS) service. 
4. Freenom -  Freenom is the world’s first and only free domain provider - I used freenom to register my domain name.
5. Packer - Build tool - an open source tool for creating identical machine images.
6. Ansible - Configuration management tool     
---

###### :microscope: NOTE

