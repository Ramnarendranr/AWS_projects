create a Terraform work directory

create a provider.tf file: 
to define what env you want to connect to(AWS/AZURE/etc..,)
go to terraform provider documentation and follow the syntax. I used:

            terraform {
            required_providers {
                aws = {
                source = "hashicorp/aws"
                version = "5.36.0"
                }
            }
            }

            provider "aws" {
                region = "ca-central-1"
            }

Now you should've installed Terraform on your pc

run **terraform init** command to initialize the terraform environment

Now create main.tf file:
This file is used to create any instance on AWS. If you want to create vpc instamce, goto terraform aws vpc documentation and follow the syntax.

Variable.tf:
contains all the values that needs to be used in main.tf

Complete the main.tf and variable.tf

1. to check everything is perfect and syntax is right:
**Terraform validate**

2. to check what's going to create:
**Terraform Plan**

3. Now to create any instance:
**Terraform apply**
you can also run
**Terraform apply -auto-approve**

4. to format the file to look nice
**terraform fmt**