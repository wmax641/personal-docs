# Terraform Boilerplate Code

### Makefile
```
IDENTITY=$(shell aws sts get-caller-identity | jq -r '.Arn' | cut -d':' -f5-)
ACCOUNT=$(shell echo "$(IDENTITY)" | cut -d":" -f1)
BASE_NAME=$(shell basename $(CURDIR))
REGION="ap-southeast-2"

init:
	terraform init \
		-backend-config="bucket=tf-${ACCOUNT}" \
		-backend-config="key=${BASE_NAME}" \
		-backend-config="region=${REGION}"

fmtchk:
	terraform fmt -write=false -diff=true -check=true

fmtfix:
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

### Common Variables
```
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
```
data "archive_file" "myscript" {
  type             = "zip"
  source_file      = "${path.module}/lambda/myscript.py"
  output_file_mode = "0666"
  output_path      = "${path.module}/lambda/myscript.py.zip"
}

resource "aws_lambda_function" "myscript" {
  filename         = data.archive_file.myscript.output_path
  function_name    = "${var.base_name}-myscript-lambda"
  role             = aws_iam_role.lambda_logs_role.arn
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
resource "aws_iam_role" "lambda_logs_role" {
  name               = "${var.base_name}-LambdamyscriptRole"
  path               = "/service/"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Sid    = ""
        Principal = {
          Service = "lambda.amazonaws.com"
        }
      },
    ]
  })
  managed_policy_arns = [
    aws_iam_policy.lambda_cloudwatch_policy.arn,
  ]
  tags = merge({ "Name" = "${var.base_name}-LambdamyscriptRole" }, var.common_tags)
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
