---
layout: post
title: AMI Automation using Packer
tags : packer hashicorp devops
---

[Packer](https://www.packer.io/) is yet another awesome tool from [HashiCorp](https://www.hashicorp.com/) to automate your AMI build process. You simply write a json config file in HCL (HashiCorp configuration language) describing all your requirements and Packer will go and launch a server, apply all your changes by running provisioner scripts and give you a final ready to use AMI Id. It also takes care of cleaning up all the resources that it creates on your cloud infrastructure during the build process. From [the product's intro](https://www.packer.io/intro/index.html):

  > Packer is an open source tool for creating identical machine images for multiple platforms from a single source configuration. Packer does not replace configuration management like Chef or Puppet. In fact, when building images, Packer is able to use tools like Chef or Puppet to install software onto the image.

### Sample Config:

{% raw %}
```ruby
{
  "variables": {
    "aws_region": "ap-south-1",
    "aws_credentials_profile": "default",
    "aws_instance_type": "t2.nano",
    "ssh_username": "ec2-user",
    "name": "my-base-image",
    "description": "My Base Image",
    "ansible_playbook": "playbook.yml"
  },
  "builders": [{
    "type": "amazon-ebs",
    "region": "{{user `aws_region`}}",
    "profile": "{{user `aws_credentials_profile`}}",
    "instance_type": "{{user `aws_instance_type`}}",
    "ssh_username": "{{user `ssh_username`}}",
    "ami_name": "{{user `name`}}",
    "tags": {
      "Name": "{{user `name`}}",
      "Created": "{{isotime \"2006-01-02\"}}",
      "Packer": true,
      "OS": "AmazonLinux",
      "Release": "Latest",
      "Description": "{{user `description`}}"
    },
    "run_tags": {
      "Name": "packer-instance-{{isotime \"2006-01-02\"}}",
      "Packer": true
    },
    "run_volume_tags": {
      "Name": "packer-instance-{{isotime \"2006-01-02\"}}",
      "Packer": true
    },
    "launch_block_device_mappings": [{
      "device_name": "/dev/xvda",
      "volume_size": 8,
      "volume_type": "standard",
      "delete_on_termination": true
    }],
    "ami_block_device_mappings": [{
      "device_name": "/dev/xvda",
      "volume_size": 8,
      "volume_type": "standard",
      "delete_on_termination": true
    }],
    "source_ami_filter": {
      "filters": {
        "virtualization-type": "hvm",
        "name": "*amzn-ami-hvm-2017.09*",
        "root-device-type": "ebs"
      },
      "most_recent": true
    }
  }],
  "provisioners": [{
      "type": "shell",
      "script": "custom-script.sh"
    },
    {
      "type": "ansible",
      "playbook_file": "{{user `ansible_playbook`}}"
    }
  ]
}
```
{% endraw %}

## Builders

Builders are responsible for creating machines and generating images from them for various platforms. For example, there are separate builders for EC2, Google Cloud, Docker, VMware, VirtualBox, etc. Packer comes with many builders by default, and can also be extended to add new builders.

{% raw %}
```ruby
"builders": [{
    "type": "amazon-ebs",
    "region": "ap-south-1",
    "instance_type": "t2.micro",
    "ami_name": "ami-11111",
    "ssh_username": "{{user `name`}}"
 }]
 ```

```ruby
"builders": [{
    "type": "googlecompute",
    "zone": "ap-south1-a",
    "machine_type": "f1-micro",
    "image_family": "ubuntu-1604",
    "ssh_username": "{{user `name`}}"
   }]
```
{% endraw %}

## Provisioners

Provisioners use builtin and third-party software to install and configure the machine image after booting. Provisioners prepare the system for use, so common use cases for provisioners include:

* installing packages
* patching the kernel
* creating users
* downloading application code

These are just a few examples, and the possibilities for provisioners are endless.

{% raw %}
```ruby
"provisioners": [{
    "type": "shell",
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo {{ .Path }} {{user `name`}}",
    "script": "custom-script.sh"
  },
  {
    "type": "ansible",
    "playbook_file": "{{user `ansible_playbook`}}",
    "user": "ec2-user",
    "extra_arguments": "-vv",
    "ansible_env_vars": ["ANSIBLE_HOST_KEY_CHECKING=False"]
  }
]
```
{% endraw %}

## Building the Image

{% raw %}
```
$  packer build base-ami.json
==> amazon-ebs: Force Deregister flag found, skipping prevalidating AMI Name
    amazon-ebs: Found Image ID: ami-28e8dd48
==> amazon-ebs: Creating temporary keypair: packer_5a23a50e-8a7e-90e8-d7fc-c9ab76a748d1
==> amazon-ebs: Creating temporary security group for this instance: packer_5a23a512-4985-fe84-3bb8-61cecea90b51
==> amazon-ebs: Authorizing access to port 22 from 0.0.0.0/0 in the temporary security group...
==> amazon-ebs: Launching a source AWS instance...
    amazon-ebs: Instance ID: i-0f990949480ce893e
==> amazon-ebs: Waiting for instance (i-0f990949480ce893e) to become ready...
==> amazon-ebs: Waiting for SSH to become available...
==> amazon-ebs: Connected to SSH!
==> amazon-ebs: Provisioning with shell script: user_data.sh
...
...
==> amazon-ebs: Provisioning with Ansible…
...
...
==> amazon-ebs: Creating the AMI: my-base-image
    amazon-ebs: AMI: ami-3e003a5e
==> amazon-ebs: Waiting for AMI to become ready...
==> amazon-ebs: Adding tags to AMI (ami-3e003a5e)...
==> amazon-ebs: Tagging snapshot: snap-0cb6ad8e0b1d322af
==> amazon-ebs: Creating snapshot tags
==> amazon-ebs: Terminating the source AWS instance...
==> amazon-ebs: Cleaning up any extra volumes...
==> amazon-ebs: No volumes to clean up, skipping
==> amazon-ebs: Deleting temporary security group...
==> amazon-ebs: Deleting temporary keypair...
Build 'amazon-ebs' finished.
==> Builds finished. The artifacts of successful builds are:
--> amazon-ebs: AMIs were created:
us-west-1: ami-3e003a5e
```
{% endraw %}

It's a great tool which is must for implementing Infrastructure as Code and goes really good with [Terraform](https://www.terraform.io/)

For the above code [visit the project on GitHub](https://github.com/nitnagpal/packer/).
