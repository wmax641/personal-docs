# Terraform Boilerplate Code

### Makefile (single account)
``` basemake
ACCOUNT ?= $(shell aws sts get-caller-identity --query 'Account' --output text)
BASE_NAME ?= $(shell basename $(CURDIR))
REGION ?= "ap-southeast-2"

init:
	terraform init \
		-backend-config="bucket=tf-${ACCOUNT}" \
		-backend-config="key=${BASE_NAME}" \
		-backend-config="region=${REGION}"

inits3:
	@echo -n "Create s3 bucket: 'tf-${ACCOUNT}'?   [y/N] " && read ans && [ $${ans:-N} = y ]
	aws s3api create-bucket --bucket "tf-${ACCOUNT}" --region "${REGION}" --create-bucket-configuration LocationConstraint="${REGION}"

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
### Makefile (mutliple workspace on same account)
```basemake
ACCOUNT_ID ?= $(shell aws sts get-caller-identity --query 'Account' --output text)
TARGET_ENV ?= dev
PROJECT_NAME ?=$(shell basename $(CURDIR))
REGION ?= "ap-southeast-2"

ifeq ($(shell printf '%s' '$(ACCOUNT_ID)' | wc -m),0)
$(error ACCOUNT_ID is not set)
endif

WORKSPACE_EXISTS := $(shell terraform workspace list | grep -w $(TARGET_ENV) | wc -l)

debug:
	@echo "ACCOUNT_ID = ${ACCOUNT_ID}"
	@echo "TARGET_ENV = ${TARGET_ENV}"
	@echo "PROJECT_NAME = ${PROJECT_NAME}"
	@echo "WORKSPACE_EXISTS = $(WORKSPACE_EXISTS)"

init:
	terraform init \
		-backend-config="bucket=tf-${ACCOUNT_ID}" \
		-backend-config="key=${PROJECT_NAME}" \
		-backend-config="region=${REGION}"
	
ifeq ($(WORKSPACE_EXISTS),0)
	terraform workspace new $(TARGET_ENV)
endif

fmt:
	terraform fmt -write=true -recursive

validate:
	set -e
	terraform fmt -write=false -recursive -check
	terraform validate

switch_workspace:
	terraform workspace select ${TARGET_ENV}
	terraform workspace show

plan: switch_workspace
	terraform plan -input=false -out=tfplan-${TARGET_ENV}-${ACCOUNT_ID} -var-file="vars/${TARGET_ENV}.tfvars"

apply: switch_workspace
	terraform apply -input=false tfplan-${TARGET_ENV}-${ACCOUNT_ID}

deploy: switch_workspace plan apply

destroy: switch_workspace
	terraform plan -destroy -input=false -out=tfplan-${TARGET_ENV}-${ACCOUNT_ID} -var-file="vars/${TARGET_ENV}.tfvars"
	terraform apply -input=false tfplan-${TARGET_ENV}-${ACCOUNT_ID}

clean:
	rm -f  tfplan-*  errored.tfstate 

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

### Lambda
Assumes Python Lambda code in directory `/files/myscript.py`, and entry point is a function called `lambda_handler()`
``` terraform
data "archive_file" "myscript" {
  type             = "zip"
  source_file      = "${path.module}/lambda/myscript.py"
  output_file_mode = "0666"
  output_path      = "${path.module}/lambda/myscript.py.zip"
}

resource "aws_lambda_function" "myscript" {
  filename         = data.archive_file.myscript.output_path
  function_name    = "${var.base_name}-myscript-lambda"
  role             = aws_iam_role.lambda_myscript.arn
  handler          = "myscript.lambda_handler"
  timeout          = 4
  source_code_hash = filebase64sha256(data.archive_file.myscript.output_path)
  runtime          = "python3.9"
  #environment {
  #  variables = {
  #    foo = "bar"
  #  }
  #}
  tags = merge({ "Name" = "${var.base_name}-myscript" }, var.common_tags)
}

# Execution IAM role for lambda
resource "aws_iam_role" "lambda_myscript" {
  name               = "${var.base_name}-LambdaServiceRole"
  path               = "/service/"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      },
    ]
  })

  # inline execution role policy
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = [
          "ec2:Describe*",
        ]
        Effect   = "Allow"
        Resource = "*"
      },
    ]
  })

  managed_policy_arns = [
    aws_iam_policy.lambda_cloudwatch_policy.arn,
  ]
  tags = merge({ "Name" = "${var.base_name}-LambdaServiceRole" }, var.common_tags)
}

data "aws_iam_policy_document" "lambda_cloudwatch_policy_doc" {
  statement {
    actions = ["logs:CreateLogGroup"]
    resources = [
      "arn:aws:logs:${data.aws_region.current.name}:${data.aws_caller_identity.current.account_id}:*"
    ]
  }
  statement {
    actions = [
      "logs:CreateLogStream",
      "logs:PutLogEvents"
    ]
    resources = [
      "arn:aws:logs:${data.aws_region.current.name}:${data.aws_caller_identity.current.account_id}:log-group:/aws/lambda/*"
    ]
  }
}

```
