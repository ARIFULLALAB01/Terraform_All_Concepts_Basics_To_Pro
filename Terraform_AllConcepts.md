# terraform 

* **terraform plugins**
    - provider
    - module
    - backend
    - provisionars 
### conseptes 
* terraform provider
* terraform resources 
* terraform work flow 
* terraform variables
    - input variables
    - output variables 
    - sensative version --> true
* terraform functions 
* terraform state file
* terraform remote backend
* terraform multi region deploy 
* terraform implicitly and explicitly
* terraform life cycle hooks 
    - create_before_destroy = true
    - prevent_destroy       = true
    - ignore_changes        = [tags, instance_type]
    - replace_triggered_by  = [aws_iam_role.example.name]
* terraform taints
* terrafom graph 
* terraform null_resources 
     - provisioners
     - file 
     - remote-exec
     - local-exec
     - trigger Terraform work-spaces 
* terraform import 
* terraform data sources
* terraform workspace 
* terraform modules 

**terraform most used commands**
```bash
    terraform init
    terraform fmt
    terraform validate
    terraform plan
    terraform apply 
    terraform destroy
    terraform state list
    terraform workspace list
    terraform workspace create
    terraform workspace select
    terraform taint 
    terraform apply --auto-aprove
    terraform apply --var-file='dev.tfvars'
    terraform validate 
    terraform inti --upgrading 
    terraform state rm 
    terraform destroy -target=aws_instance.example
    terraform apply -target=aws_instance.example
    terraform refresh
    terraform untaint <resource_type>.<resource_name>


```

* **terraform provider**
* take that provider version on 

```
- terraform provider version
		- =, !=, >, >= , < , < =, ~> 
  = Only the exact version 2.1.3 {version = 2.1.3}
  != means dont take on specific version take any version 
  > means take version more than {version > 3.28 }
  2.1 >= Any version 2.1 or higher 
  < means take version less than {version < 3.28} 
  2.1.3 <= Any version up to and including 2.1.3
  ~> means take on Allow minor version updates (patch and minor changes).
            Do not allow major version updates (which might introduce breaking changes) like on {version 3.28.0 to 3.28.9}
```
**terraform resources**

**terraform state mv**
    - rename resources the terraform state mean old resources name to new resources name
    - move to state on  one module to another module 
    - Fixing State Issues: Sometimes, you might need to adjust the state file manually to correct mistakes in resource management.
* **terraform state file loos the locally**
    - The state file is permanently deleted if no backup exists.
    - Terraform loses track of the current infrastructure state.
    - Running `terraform plan` or `terraform apply` can cause Terraform to assume all resources need to be re-created, potentially leading to unintended changes or conflicts.
    **Key Takeaway:**
    - For local backends, the loss of the state file requires manual recovery, either from backups or by re-importing resources.
    - For remote backends, the state file can be seamlessly recovered by reinitializing Terraform, making remote backends the safer option for critical infrastructure.



**terraform Implicit vs Explicit Dependencies**

Implicit Dependencies: 	

    Definition	Automatically inferred by Terraform.
    Ease of Use	Requires no extra code.
    Use Cases	Standard resource relationships

Explicit Dependencies:

	Manually declared with depends_on.
	Needs explicit specification.
	Non-inferable or external dependencies.

### **Key Difference Between `terraform import` and Terraform Data Sources**

---

| **Aspect**                 | **`terraform import`**                            | **Terraform Data Sources**                     |
|----------------------------|--------------------------------------------------|------------------------------------------------|
| **Definition**             | Imports an existing resource into the Terraform state. | Reads and references external data/resources dynamically during Terraform runs. |
| **Purpose**                | Adds an already existing resource to Terraform management. | Fetches information about external resources for use in configuration. |
| **State File**             | Updates the state file by associating a resource with its configuration. | Does not create or modify state; only provides data for use in resources or outputs. |
| **Terraform Code**         | Requires pre-existing Terraform configuration.   | Can be used without managing the resource in Terraform. |
| **Impact**                 | Brings external resources under Terraform’s control. | Allows querying external resources without managing them. |
| **Examples**               | Import an existing S3 bucket: <br> `terraform import aws_s3_bucket.example my-bucket`. | Query an existing S3 bucket: <br> ```hcl data "aws_s3_bucket" "example" { bucket = "my-bucket" } ``` |
| **Use Case**               | Manage resources that were created outside Terraform. | Reference attributes or details of external resources without managing them. |
| **Lifecycle Management**   | Enables Terraform to manage the lifecycle (create, update, destroy). | Only retrieves data; lifecycle management is not applicable. |
| **Resource Control**       | Provides full control over the resource post-import. | Read-only access to resource attributes. |



### **Key Takeaway**:
- **`terraform import`**: Used to manage existing resources through Terraform by adding them to the state.  
- **Data Sources**: Used to reference external resources dynamically without managing them.

**terraform Null resources**
    * **Common Use Cases**
        - Running Scripts:
            * Execute custom commands or scripts that are not tied to a specific resource.
        - Handling Dependencies:
            * Create explicit dependencies between resources when implicit dependencies are insufficient.
        - Triggering on Changes:
            * Re-run a resource or command based on changes in input values.

## terraform workspace

```
terraform workspace --help
Usage: terraform [global options] workspace

  new, list, show, select and delete Terraform workspaces.

Subcommands:
    delete    Delete a workspace
    list      List Workspaces
    new       Create a new workspace
    select    Select a workspace
    show      Show the name of the current workspace
```

## Terraform life_cycle hooks 

* In Terraform, lifecycle hooks are used to control the behavior of resource creation, modification, or deletion during a terraform apply. These hooks provide fine-grained control over how Terraform manages resources and can help you address situations like preserving resources, preventing automatic replacements, or handling dependencies in a specific way.

**1.create_before_destroy**

* This ensures that Terraform creates the new resource before destroying the old one, avoiding downtime during replacements.
* Useful for situations like rolling updates where you want to ensure that the new resource is available before removing the old one.
  
**2.prevent_destroy**

* This prevents a resource from being destroyed. If you attempt to terraform destroy a resource with this argument, Terraform will refuse to destroy it and throw an error.
* Useful for critical resources that should not be deleted accidentally.

**3.ignore_changes**

* This allows you to specify resource attributes that Terraform should ignore during the apply. It can be helpful when you want Terraform to manage certain aspects of a resource but not others.
* For example, ignoring the tags field if you’re manually updating it outside of Terraform.

**Summary of Lifecycle Arguments:**
    - **create_before_destroy:** Create the new resource before destroying the old one.
    - **prevent_destroy:** Prevent the resource from being destroyed.
    - **ignore_changes:** Ignore changes to specific resource attributes.
    - **replace_triggered_by:** Trigger replacement of a resource when certain other resources change.


## terraform state

* terraform state maintaining actvally state and desired state 
* actvally state means is `.tf` files 
* desired state means `terraform.state` file 

* store state file to remote backend 
* don not update/delete the file 
* state locking
* isolation of state file 
* regular backup
---
    s3 bucket
    azure blob storage 
    GCP storing A/C

# 🔹 Terraform Lifecycle Arguments — Detailed Comparison

| **Lifecycle Argument**    | **Purpose**                                                            | **Behavior**                                                                                | **Real-Time Use Case (Example)**                                                                                       | **When to Use**                                                                                  |
| ------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **create_before_destroy** | Ensures new resource is created **before** destroying the existing one | Terraform first creates the new resource → then destroys the old one                        | **Example:** Updating an **AWS Load Balancer**, **VM**, or **S3 Bucket** where downtime must be avoided                | When zero-downtime replacement is required (e.g., updating production servers or load balancers) |
| **prevent_destroy**       | Protects critical resources from being accidentally destroyed          | Terraform will **fail the plan/apply** if a destroy operation is attempted                  | **Example:** Protecting **production databases (RDS)** or **stateful storage (EBS volumes)**                           | When resource deletion could cause **data loss** or **service outage**                           |
| **ignore_changes**        | Ignores changes to specific attributes in a resource                   | Terraform will not trigger a plan/apply for ignored attributes even if they change manually | **Example:** **Auto-scaling groups** where instance count changes automatically, or **tags** modified by other systems | When external processes modify resources and you want to **avoid unnecessary Terraform drift**   |
| **replace_triggered_by**  | Triggers resource replacement when a dependent resource changes        | Automatically replaces the resource if another resource changes                             | **Example:** Replace an **EC2 instance** when a related **security group** or **AMI** changes                          | When a resource must always be replaced in sync with another resource change                     |
