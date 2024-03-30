# Terraform Environment Bootstrap
## S3 Bucket
- Stores the state file. Should be named `tf-<ACCOUNT_ID>`
- Bucket versioning enabled
- Lifecycle policy, 90d deletion of non-current versions

## IAM 
See [IAM and Accounts](./iam_accounts.md)

This first needs to be deployed as admin to set up the `IAMSeedRole` [wmax641/wap-iam-seed-role](https://github.com/wmax641/wap-iam-seed-role)

Once deployed, the `IAMSeedRole` has the below role assumption policy that allows it to be assumed from a Github Actions builds from [wmax641/wap-iam-accounts](https://github.com/wmax641/wap-iam-accounts) repo, depending on its environment

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

