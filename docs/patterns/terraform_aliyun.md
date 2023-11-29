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
  backend "oss" {
  }

  required_providers {
    alicloud = {
      source  = "aliyun/alicloud"
      version = "1.211.1"
    }
  }
}

provider "alicloud" {
  region = "cn-hongkong"
}

```
#### data.tf
``` terraform
data "alicloud_regions" "current_region_ds" {
  current = true
}
data "alicloud_account" "current" {}
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
