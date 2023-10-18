# Terraform (Aliyun) Boilerplate

### Makefile
``` basemake
ACCOUNT=$(shell aliyun sts GetCallerIdentity | jq -r '.AccountId')
BASE_NAME=$(shell basename $(CURDIR))
REGION="cn-hongkong-b"

init:
	terraform init \
		-backend-config="bucket=tf-${ACCOUNT}" \
		-backend-config="key=${BASE_NAME}" \
		-backend-config="region=${REGION}"

inits3:
	@echo -n "Create OSS bucket: 'tf-${ACCOUNT}'?   [y/N] " && read ans && [ $${ans:-N} = y ]
	aliyun oss mb "oss://tf-${ACCOUNT}"

fmt:
	terraform fmt -write=true --recursive

validate:
	terraform validate

plan:
	terraform plan -input=false -out=tfplan-${ACCOUNT}

apply:
	terraform apply -input=false tfplan-${ACCOUNT}

destroy:
	terraform plan -destroy -input=false -out=tfplan-${ACCOUNT}
	terraform apply -input=false tfplan-${ACCOUNT}

```

### Common
#### main.tf
``` terraform
terraform {
  backend "s3" {
  }

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.4.0"
    }
  }
}

provider "aws" {
  region = "ap-southeast-2"
}
```
#### data.tf
``` terraform
data "aws_region" "current" {}
data "aws_caller_identity" "current" {}
```
#### variables.tf
``` terraform
variable "base_name" {
  description = "Common prefix used for naming resources of this project"
  default     = "tf-project-name"
}
variable "common_tags" {
  description = "Common tags used in resources of this project"
  default     = {
    "project" = "tf-project-name"
  }
}
```
