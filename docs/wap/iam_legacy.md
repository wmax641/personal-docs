# IAM Legacy
### Login Flow
Main Org Account, IAM user - `wmax641`
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": [
                "arn:aws:iam::*:role/OrganizationAccountAccessRole"
            ]
        }
    ]
}
```

Then on target accounts, have a role `OrganizationAccountAccessRole`  this which is assumed by the above account with this trust principal:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::xxxxxxxxxxxx:user/wmax641",
                ]
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```


