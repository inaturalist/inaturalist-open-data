# Setting up a database on AWS using Amazon RDS

**Step 1**: Create an [AWS account](https://aws.amazon.com/), or log in

**Step 2**: Create an [RDS database](https://console.aws.amazon.com/rds). While this database can be stopped and restarted at any time, the estimated monthly costs are $121.82 USD for the type of database generated following these steps

- In the `Dashboard` section of the [Amazon RDS page](https://console.aws.amazon.com/rds), find the `Create database` box and click `Create database`

- Configure the new database. These are the settings we tried and know work, but configure as desired. Leave all of the default settings except for the following:
  - Under `Engine Options` under `Engine Type` select `Postgresql` and under `Version` select `PostgreSQL 13.x-R1` (e.g. `PostgreSQL 13.1-R1`)
  - Under `Templates` select `Dev/Test`
  - Under `Settings` create a password and enter it in the `Master password` and `Confirm password` fields
  - Under `Storage` set `Allocated Storage` to 50GB and under `Storage autoscaling` uncheck `Enable storage autoscaling`
  - Under `Availability and Durability` select `Do not create a standby instance`
  - Under `Connectivity` set `Public access` to `yes`
  - Under the `Additional Configuration -> Database options` section create and enter a database name in the `initial database name` field
  - Under the `Additional Configuration -> Backup` section, uncheck `Enable automatic backups`
  - Under the `Additional Configuration -> Performance Insights` section, uncheck `Enable Performance Insights`
  - Under the `Additional Configuration -> Monitoring` section, uncheck `Enable Enhanced monitoring`
  - Under the `Additional Configuration -> Maintenance` section, uncheck `Enable auto minor version update`
  - Click the orange `Create database` button

- Confirm your new database is available. In the `Databases` section of the Amazon RDS page in the `Databases` box you should see a row representing your new database with status `Creating`. After a few minutes, refresh the page and the status should be `Available`.

**Step 3**: Configure the network to give your IP address access to the database

- If you have not previously configured the VPC, add the IP address of your local machine to the `Security Group Rules` for the new database so you can connect from your local PostgreSQL client
  - In the `Databases` section of the Amazon RDS page in the `Databases` box, click on your newly created database
  - In the `Connectivity & security` tab write down the `Endpoint` in the `Endpoint and port` section. You’ll use this to remotely connect to the database
  - Also in the `Connectivity & security` tab, scroll down to the `Security group rules` box and click the `Security group` row with type `CIDR/IP - Inbound` This will load a new page showing the `Security Groups` table
    - In the `Actions` menu click `Edit inbound rules` which will load a new `Edit inbound rules` page
    - In the `Source` menu click `My IP` which should populate the IP address of your local machine. If it does not, click `Custom` and enter your IP address manually. If you don’t know it you can search the web for `what is my IP`
    - Return to the database page by clicking on it from the the `Databases` section of the `Amazon RDS` page in the `Databases` box


**Step 4**: Connect to the RDS database from PostgreSQL on your local machine

- Make sure a PostgreSQL client is installed
- Navigate to a place where you can interact with PostgreSQL, for example on a Mac or Linux machine open a terminal
- Using the Endpoint of your Amazon RDS database that you created and wrote down in Step 3, connect to the database. Note that you’ll have to enter your proper endpoint (e.g. dbname.XXXXXX.rds.amazonaws.com) as well as your database name (e.g. dbname) and your user name if you changed the default (e.g. postgres)
  - `psql -h dbname.XXXXXX.rds.amazonaws.com -U postgres dbname`
- If the database is running and you can connect to it, you should be in a normal PostgreSQL prompt and can interact with it like a typical PostgreSQL database

**Step 5**: Connect an [IAM Policy](../Policy) to be attached to an [IAM Role](../Role)

**Step 6**: Connect an [IAM Role](../Role) and attach the policy

**Step 7**: Associate the role with the RDS database

- Return to your RDS database page by clicking on it from the the `Databases` section of the [Amazon RDS dashboard](https://console.aws.amazon.com/rds/home) in the `Databases` box
- In the `Connectivity and Security` tab, scroll down to `Manage IAM roles`
- Select `iNaturalistImportRole` from the `Add IAM roles to this instance` select dropdown and `s3Import` from the `Feature` dropdown, then click `Add role`
- You should see [the role you created](../Role) named `iNaturalistImportRole` represented in the `Current IAM roles for this instance` table
- Wait for the status to become `Active`, refreshing the page if necessary
- Now reboot the instance. Under the `Actions` menu at the top of the same database page click `Reboot`
- Wait for the database to become `Available`. Be sure to close any open PostgreSQL connections you have to the database. Until you close your connections and reconnect, the `s3Import` feature may not be enabled, and you may not be able to import from the iNaturalist Open Dataset
