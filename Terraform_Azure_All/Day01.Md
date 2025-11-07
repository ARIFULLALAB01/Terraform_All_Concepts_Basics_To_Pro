# Day01:
============

Terraform:
==============

## Day01.Md

* Terraform Installation:
    /Tools:
 * set Environmental Variable path:
 * Azure Authentication Setup
 * Terraform Files Creations
 * Terraform Workflow:
 * Terraform Commands:
    - learn New Commands Use Case
* Manually Deleted from Portal What will Happened
* Management Groups ANd Moved other Environment Setups


*) Companies are moved to Terraform Instead of Azure ARM Templates.

*) Terraform vs ARM Templtes

1.Terrafrom can be used with any cloud, infact they have 4222 providers as of Aug15th
Providers is nothing but Applications
2) understanding code is much easily when compared to ARM Templates
3) we can distribute the code across multiple file for each resource
4) workspaces for identical environments.
5) Modules to avoid code duplication.

*) Azure features usecases in Terraform


azurerm resource group..!

terraform are binaries

add environmental variable

========

Terraform Workflow

terraform init
terraform fmt
terraform validate
terraform plan
terraform apply


Ctrld

azure terraform credentials using environmental varibles 


Different ways to authenticate:


terraform --refresh-only

terraform state-list

terraform destroy -target <Particale rresource we need to taget then we use this>

terraform destroy --auto-approve




resource "azure_virtual_network" "vnet1"

resource "aws_vpc" "vpc1" {}
 