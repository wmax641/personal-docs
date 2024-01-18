# IAM and Accounts for WAP
Identity and Access Management, and Account access for the wmax641 app platform (WAP)

## Architecture and Design

## Operation

Use [wmax641/wap-iam-accounts](https://github.com/wmax641/wap-iam-accounts) module

### Create Roles

### Initial Setup and Seed Role

This first needs to be deployed as admin to set up the `IAMSeedRole` [wmax641/wap-iam-seed-role](https://github.com/wmax641/wap-iam-seed-role)

Once deployed, the `IAMSeedRole` has a role assumption policy like below that allows it to be assumed from a Github Actions deployment from the [wmax641/wap-iam-accounts](https://github.com/wmax641/wap-iam-accounts) repo

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::071440211637:oidc-provider/token.actions.githubusercontent.com"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringEquals": {
                    "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                },
                "StringLike": {
                    "token.actions.githubusercontent.com:sub": [
                        "repo:wmax641/wap-iam-accounts:environment:production",
                        "repo:wmax641/wap-iam-accounts:environment:development"
                    ]
                }
            }
        }
    ]
}
```
