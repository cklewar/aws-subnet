# AWS-SUBNET

This repository consists of Terraform templates to bring up a AWS Subnet attached to VPC.

## Usage

- Clone this repo with: `git clone --recurse-submodules https://github.com/cklewar/aws-subnet`
- Enter repository directory with: `cd aws-subnet`
- Export AWS `access_key` and `aws_secrect_key` environment variables
- Pick and choose from below examples and add mandatory input data and copy data into file `main.tf.example`
- Rename file __main.tf.example__ to __main.tf__ with: `rename main.tf.example main.tf`
- Initialize with: `terraform init`
- Apply with: `terraform apply -auto-approve` or destroy with: `terraform destroy -auto-approve`

### Example Output

```bash

```

## AWS VPC with subnets

````hcl
variable "project_prefix" {
  type        = string
  description = "prefix string put in front of string"
  default     = "f5xc"
}

variable "project_suffix" {
  type        = string
  description = "prefix string put at the end of string"
  default     = "01"
}

module "aws_vpc" {
  source             = "./modules/aws/vpc"
  aws_az_name        = "us-east-2a"
  aws_region         = "us-east-2"
  aws_vpc_cidr_block = "172.16.40.0/21"
  aws_vpc_name       = format("%s-aws-vpc-%s", var.project_prefix, var.project_suffix)
  custom_tags        = {
    Name  = format("%s-aws-vpc-%s", var.project_prefix, var.project_suffix)
    Owner = "c.klewar@f5.com"
  }
}

provider "aws" {
  region = "us-east-2"
  alias  = "us-east-2"
}

module "aws_subnet" {
  source          = "./modules/aws/subnet"
  aws_vpc_id      = module.aws_vpc.aws_vpc["id"]
  aws_vpc_subnets = [
    {
      name                    = format("%s-aws-subnet-a-%s", var.project_prefix, var.project_suffix)
      map_public_ip_on_launch = true
      cidr_block              = "172.16.40.0/24"
      availability_zone       = "us-west-2a"
      custom_tags             = {
        Name  = format("%s-aws-subnet-a-%s", var.project_prefix, var.project_suffix)
        Owner = "c.klewar@f5.com"
      }
    },
    {
      name                    = format("%s-aws-subnet-b-%s", var.project_prefix, var.project_suffix)
      map_public_ip_on_launch = true
      cidr_block              = "172.16.41.0/24"
      availability_zone       = "us-west-2a"
      custom_tags             = {
        Name  = format("%s-aws-subnet-b-%s", var.project_prefix, var.project_suffix)
        Owner = "c.klewar@f5.com"
      }
    }
  ]
  custom_tags = {
    Owner = "c.klewar@f5.com"
  }

  providers = {
    aws = aws.us-east-2
  }
}````

