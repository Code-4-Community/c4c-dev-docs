# AWS Backend Deployment


## AWS Management Console
 - Log in to the [AWS Management Console](https://us-east-2.console.aws.amazon.com/console/home?nc2=h_ct&region=us-east-2&src=header-signin#)
   - If deploying to C4C infrastructure, select `IAM User` and enter account alias `c4cneu` and your *personal* username / password
 - Select region `US East (Ohio)` (also called `us-east-2`)

## RDS Setup
 - Navigate to `RDS` from the `Services` dropdown and click `DB Instances`
 - If creating a new RDS instance:
   - Click `Create database`, then select `Standard create`, and `PostgreSQL`
   - For `Templates`, select `Free tier`
   - Name the RDS instance under `DB instance identifier`
   - Set the `Master username` to `postgres` and enter a unique password
   - Adjust `Maximum storage threshold` to a reasonable value (e.g. `100` GiB)
   - Under `Connectivity`, set `Virtual private cloud (VPC)` to `VPC-Public`
   - For `Subnet group`, select `db-sbg-public`
   - For `Public access`, select `Yes`
   - For `Existing VPC security groups` select `SG-RDS`
   - Under `Database authentication options`, select `Password authentication`
   - Adjust any other default settings on this page, as necessary
   - Click `Create database`
 - Connect to the RDS instance, and create a new database for the project

## Elastic Beanstalk Setup
 - Navigate to `Elastic Beanstalk` from the `Services` dropdown
 - Click `Applications` from the left sidebar
 - Click `c4c-internal` from the list of applications
 - Click `Create a new environment` and select `Web server environment`
 - Name the environment under `Environment name`
 - Under `Platform`, set `Platform` to `Docker`
 - Under `Application code` select `Sample application`
 - Click `Configure more options`
 - Under `Presets`, select `Custom configuration`
 - Under `Network`, click `Edit`
   - Under `Virtual private cloud (VPC)` set `VPC` to `VPC-Public`
   - Under `Load balancer settings`, set `Visibility` to `Public`
   - Under `Load balancer subnets`, select `SN-Public-1` and `SN-Public-2`
   - Under `Instance settings`, check `Public IP address`
   - Under `Instance subnets` select `SN-Public-1` and `SN-Public-2`
   - Click `Save`
 - Under `Load Balancer` click `Edit`
   - Under `Load balancer type` select `Application Load Balancer`
   - Click `+ Add listener`
   - Add a new listener on port `443` for `HTTPS` using the appropriate certificate
 - Under `Instances` click `Edit`
   - Under `EC2 security groups` select `SG-EB-EC2`
   - Click `Save`
 - Under `Capacity` click `Edit`
   - Set `Environment type` to `Load balanced`
   - Set `Min` to `1` and `Max` to `1`
   - Click `Save`
 - Under `Security` click `Edit`
   - Under `EC2 key pair` select an SSH key
 - Click `Create environment`

## Load Balancer Setup
 - Navigate to `EC2` from the `Services` dropdown
 - Click the newly created Load Balancer
 - Click the `Listeners` tab
 - Edit the rule for `HTTP` on port `80`
 - Delete the default action
 - Create a new action to redirect from `HTTP`:`80` to `HTTPS`:`443`

 ## Route 53 Setup
  - Navigate to `Route 53` from the `Services` dropdown
  - Click `Hosted zones` in the left navigation bar
  - Click `c4cneu.com`
  - Create a new `A` record pointing to the Elastic Beanstalk instance

## Update Travis CI
  - Update the `.travis.yml` to point to the Elastic Beanstalk instance