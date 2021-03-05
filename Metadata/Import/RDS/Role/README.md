# Creating a IAM Role

In order to access files in S3 from an RDS instance, the instance must be associated with an IAM Role that has attached to it an IAM Policy that allows access to the iNaturalist Open Dataset bucket. See the [official documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import.ARNRole) for more information.

Go to the [Identity and Access Management (IAM)](https://console.aws.amazon.com/iam/home) dashboard, and click on `Roles` under `Access Management`

Select `AWS Service` as the trusted entity

Scroll down and select `RDS` as the use case

You’ll be provided more RDS options, select `RDS - Add Role to Database` and lick `Next: Permissions`

Choose `Filter Policies -> Customer Managed`

Select the [policy you created](../Policy) named `iNaturalistImportPolicy” from the filtered policy list and click `Next: Tags`

Add tags if you wish and select `Next: Review`

Name the role `iNaturalistImportRole`

Finish by clicking `Create Role`
