# Creating an IAM Policy

In order to access files in S3 from an RDS instance, the instance must be associated with an IAM Role that has attached to it an IAM Policy that allows access to the iNaturalist Open Dataset bucket. See the [official documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import.ARNRole) for more information.

Go to the [Identity and Access Management (IAM)](https://console.aws.amazon.com/iam/home) dashboard, and click on `Policies` under `Access Management`.

Click `Create` Policy

Click the JSON tab and paste in the following:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::inaturalist-open-data",
                "arn:aws:s3:::inaturalist-open-data/*"
            ]
        }
    ]
}
```

Click `Next` twice (skipping the optional “Add Tags” section)

In the `Review Policy` Section enter `iNaturalistImportPolicy` in the `Name` field and click `Create Policy`
