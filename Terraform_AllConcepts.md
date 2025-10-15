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


# 🔹 Terraform Modules vs Terraform Workspaces — Detailed Comparison


| **Feature**              | **Terraform Modules**                                                                                             | **Terraform Workspaces**                                                                          |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Purpose**              | Used to **organize and reuse code**                                                                               | Used to **manage multiple environments** (like dev, test, prod) **within the same configuration** |
| **What It Does**         | Groups related Terraform resources into a **reusable unit**                                                       | Creates **isolated state files** for different environments using the same configuration          |
| **Scope**                | Code-level (reusable blocks of infrastructure)                                                                    | Environment-level (isolated states for environments)                                              |
| **Usage Type**           | Promotes **modularity and reusability**                                                                           | Promotes **environment isolation and state separation**                                           |
| **Example Use**          | A single module can create an **EC2 instance**, **VPC**, or **S3 bucket**, and can be reused in multiple projects | Different workspaces like **dev**, **staging**, and **prod** for the same infrastructure code     |
| **Configuration Method** | Defined using a `module` block and sourced from a path, Git repo, or Terraform Registry                           | Created and switched using Terraform CLI commands: `terraform workspace new`, `select`, etc.      |
| **State File Handling**  | Each module shares the **same state file** (unless explicitly separated)                                          | Each workspace has its **own independent state file**                                             |
| **Ideal For**            | Standardizing code and reducing repetition across teams and projects                                              | Managing multiple environments with same code but different variables or data                     |
| **Reusability**          | Highly reusable across multiple projects                                                                          | Not reusable — specific to one Terraform configuration                                            |
| **Dependency Handling**  | Modules can call other modules (nested modules)                                                                   | Workspaces cannot call other workspaces                                                           |
| **Version Control**      | Modules can be versioned and shared (e.g., using Terraform Registry or Git tags)                                  | Workspaces are local or backend-specific, not shared across repos                                 |
| **When to Use**          | When you need to **reuse and maintain consistent infrastructure code**                                            | When you need **different environments (dev, test, prod)** from the same codebase                 |


# 🔹 Terraform Implicit vs Explicit Dependencies — Detailed Comparison

| **Feature**              | **Implicit Dependency**                                                                                                              | **Explicit Dependency**                                                                                                                               |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Definition**           | Terraform automatically understands the **resource dependency** based on references between resources.                               | You **manually define** a dependency using the `depends_on` argument.                                                                                 |
| **How It Works**         | Terraform looks at expressions and variable references inside a resource (like using another resource’s attribute).                  | Terraform waits to create a resource **only after** the explicitly mentioned dependency is created.                                                   |
| **Dependency Type**      | **Automatic / Inferred by Terraform**                                                                                                | **Manual / Defined by User**                                                                                                                          |
| **Configuration Needed** | No need to specify anything manually — Terraform detects dependencies by itself.                                                     | You must specify `depends_on = [resource.name]` to make Terraform aware of the relationship.                                                          |
| **Example**              | If an EC2 instance uses a Security Group ID from another resource, Terraform automatically knows to create the Security Group first. | If a resource doesn’t have a direct attribute reference but still depends on another (like provisioning after a bucket policy), you use `depends_on`. |
| **Terraform Behavior**   | Terraform automatically creates resources in the correct order.                                                                      | Terraform follows your manual order based on the dependency you define.                                                                               |
| **Code Simplicity**      | Cleaner and shorter code — no need for extra dependency blocks.                                                                      | Slightly more verbose but gives **full control** over creation order.                                                                                 |
| **Risk**                 | May fail if Terraform cannot detect indirect dependencies.                                                                           | Safe for complex scenarios where order is critical.                                                                                                   |
| **Ideal For**            | Simple relationships where one resource references another.                                                                          | Complex setups where Terraform cannot infer the dependency automatically.                                                                             |
| **When to Use**          | Use when one resource’s argument directly uses another resource’s output or attribute.                                               | Use when resources are related indirectly (like file uploads, IAM attachments, or null resources).                                                    |


# 🔹 Terraform Commands — Explained Simply

```
| **Command**                               | **Purpose / Use Case (Simple Explanation)**                                                                                                               |
| ----------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `terraform init`                          | Initializes the working directory, downloads provider plugins, and sets up backend for the state file. **(Always the first command to run in a project)** |
| `terraform fmt`                           | Formats Terraform files to maintain a clean and consistent code style.                                                                                    |
| `terraform validate`                      | Checks your configuration for syntax errors and verifies if the code is valid.                                                                            |
| `terraform plan`                          | Shows what changes Terraform will make before actually applying them. **(Like a preview)**                                                                |
| `terraform apply`                         | Applies the planned changes and creates/updates infrastructure.                                                                                           |
| `terraform destroy`                       | Destroys all resources defined in your configuration. **(Used to tear down environments)**                                                                |
| `terraform state list`                    | Lists all resources currently tracked in the Terraform state file.                                                                                        |
| `terraform workspace list`                | Lists all existing Terraform workspaces.                                                                                                                  |
| `terraform workspace create <name>`       | Creates a new workspace (e.g., dev, test, prod).                                                                                                          |
| `terraform workspace select <name>`       | Switches between different workspaces.                                                                                                                    |
| `terraform taint <resource>`              | Marks a resource for recreation during the next apply.                                                                                                    |
| `terraform untaint <resource>`            | Removes the taint mark, so Terraform won’t recreate that resource.                                                                                        |
| `terraform apply --auto-approve`          | Runs apply automatically without asking for confirmation.                                                                                                 |
| `terraform apply --var-file="dev.tfvars"` | Applies configuration using variables from the given `.tfvars` file.                                                                                      |
| `terraform init --upgrade`                | Upgrades Terraform providers to the latest compatible versions.                                                                                           |
| `terraform state rm <resource>`           | Removes a resource from the state file (useful when managing resources manually).                                                                         |
| `terraform destroy -target=<resource>`    | Destroys only the specified resource instead of the whole infrastructure.                                                                                 |
| `terraform apply -target=<resource>`      | Applies only the specified resource instead of the entire configuration.                                                                                  |
| `terraform refresh`                       | Updates the state file with the latest real-world resource information from the cloud provider.                                                           |
| `terraform show`                          | Displays detailed information about resources in the state file.                                                                                          |
| `terraform output`                        | Shows the values of outputs defined in your configuration.                                                                                                |
| `terraform graph`                         | Generates a dependency graph of all resources (helps visualize relationships).                                                                            |
| `terraform import <resource> <id>`        | Brings an existing resource (created manually) under Terraform management.                                                                                |
| `terraform state show <resource>`         | Displays detailed info about a specific resource from the state file.                                                                                     |
| `terraform providers`                     | Lists all providers used in your configuration.                                                                                                           |
| `terraform version`                       | Displays the installed Terraform version.                                                                                                                 |
| `terraform console`                       | Opens an interactive console to test and evaluate Terraform expressions.                                                                                  |
| `terraform lock` / `terraform unlock`     | Manages the state lock to prevent concurrent modifications. *(Useful in team setups)*                                                                     |
| `terraform output -json`                  | Exports Terraform output values in JSON format — useful for automation scripts.                                                                           |

```


## 🔹 Terraform Commands — Complete Reference Table

| **Command** | **Purpose / Use Case (Simple Explanation)** |
|--------------|---------------------------------------------|
| `terraform init` | Initializes the working directory, downloads provider plugins, and sets up backend for the state file. **(Always the first command to run in a project)** |
| `terraform fmt` | Formats Terraform files to maintain a clean and consistent code style. |
| `terraform validate` | Checks your configuration for syntax errors and verifies if the code is valid. |
| `terraform plan` | Shows what changes Terraform will make before actually applying them. **(Like a preview)** |
| `terraform apply` | Applies the planned changes and creates/updates infrastructure. |
| `terraform destroy` | Destroys all resources defined in your configuration. **(Used to tear down environments)** |
| `terraform state list` | Lists all resources currently tracked in the Terraform state file. |
| `terraform workspace list` | Lists all existing Terraform workspaces. |
| `terraform workspace create <name>` | Creates a new workspace (e.g., dev, test, prod). |
| `terraform workspace select <name>` | Switches between different workspaces. |
| `terraform taint <resource>` | Marks a resource for recreation during the next apply. |
| `terraform untaint <resource>` | Removes the taint mark, so Terraform won’t recreate that resource. |
| `terraform apply --auto-approve` | Runs apply automatically without asking for confirmation. |
| `terraform apply --var-file="dev.tfvars"` | Applies configuration using variables from the given `.tfvars` file. |
| `terraform init --upgrade` | Upgrades Terraform providers to the latest compatible versions. |
| `terraform state rm <resource>` | Removes a resource from the state file (useful when managing resources manually). |
| `terraform destroy -target=<resource>` | Destroys only the specified resource instead of the whole infrastructure. |
| `terraform apply -target=<resource>` | Applies only the specified resource instead of the entire configuration. |
| `terraform refresh` | Updates the state file with the latest real-world resource information from the cloud provider. |
| `terraform show` | Displays detailed information about resources in the state file. |
| `terraform output` | Shows the values of outputs defined in your configuration. |
| `terraform graph` | Generates a dependency graph of all resources (helps visualize relationships). |
| `terraform import <resource> <id>` | Brings an existing resource (created manually) under Terraform management. |
| `terraform state show <resource>` | Displays detailed info about a specific resource from the state file. |
| `terraform providers` | Lists all providers used in your configuration. |
| `terraform version` | Displays the installed Terraform version. |
| `terraform console` | Opens an interactive console to test and evaluate Terraform expressions. |
| `terraform lock` / `terraform unlock` | Manages the state lock to prevent concurrent modifications. *(Useful in team setups)* |
| `terraform output -json` | Exports Terraform output values in JSON format — useful for automation scripts. |




# 🔹 Terraform Meta-Arguments – Detailed Real-Time Use Cases

| **Meta-Argument** | **Function** | **Detailed Real-Time Use Case (Project-Style Explanation)** |
|-------------------|--------------|--------------------------------------------------------------|
| **depends_on** | Force resource creation order | In an Azure multi-tier setup, ensure the **subnet and NSG** are created before provisioning the VM NICs. Similarly, in AWS, make sure an **S3 bucket (for logs)** is created before EC2 instances that need to push application logs. Prevents dependency errors and race conditions during `terraform apply`. |
| **count** | Create multiple identical resources | Used to provision **a fixed number of web servers or NICs** for scaling within a VM scale set or auto-scaling group. For instance, `count = 3` can spin up three identical EC2 or Azure VMs for a front-end tier. Makes environment replication easy by changing a single variable. |
| **for_each** | Loop with key-value mapping | Deployed multiple **environment-specific resource groups or storage accounts** (Dev, QA, Prod) from one Terraform block. Each environment key (e.g., `"dev"`, `"prod"`) has its own independent state and name, making management cleaner than `count`. |
| **provider** | Choose provider alias | When a project uses **multiple Azure subscriptions** (Dev, QA, Prod) or **multi-region AWS deployments**, define multiple providers with aliases and assign specific provider blocks to each resource. Ensures isolation between accounts or regions in one Terraform plan. |
| **lifecycle** | Control creation, update, or destroy | Used in production to **protect critical resources** (like databases, load balancers, or vaults) by setting `prevent_destroy = true`. Also use `ignore_changes` for auto-updated fields like tags or version numbers that change outside Terraform. Prevents unwanted re-deployments or outages. |
| **locals** | Define reusable expressions or constants | Define **standard tags, region names, or naming conventions** once and reuse them across modules. Example: `local.common_tags = { Project = "BankingApp", Owner = "DevOps" }`. Reduces duplication and ensures naming consistency across 50+ resources. |
| **dynamic** | Generate nested blocks dynamically | Automatically creates **security group ingress rules** from a variable list of ports `[22, 80, 443]` or adds multiple **backend pools** to an Azure load balancer. Avoids hard-coding multiple identical `ingress` blocks. Perfect for infra modules with flexible inputs. |
| **provisioner** | Execute post-creation scripts | After creating a VM, use `remote-exec` or `file` provisioners to **install Docker, configure monitoring agents (Datadog, Splunk), or deploy initial app code**. Common in hybrid setups where configuration tools (like Ansible) aren’t yet integrated. |
| **create_before_destroy** *(lifecycle sub-option)* | Ensure zero-downtime replacement | When updating infrastructure components like **NICs, load balancers, or ASG launch configs**, Terraform first creates the new one before destroying the old one. Guarantees continuous service availability during redeployment or blue-green releases. |
| **time_sleep** | Introduce wait or delay in workflow | Some resources (like **DNS records, IAM roles, or network gateways**) need propagation time before dependent resources can be created. Using `time_sleep` waits e.g., 30s–60s, preventing errors like “resource not found” due to async cloud API behavior. |
| **module count / for_each** | Reuse modules dynamically for multiple environments | Used to deploy **same infra pattern (VNet, Storage, AKS, Key Vault)** across multiple environments by looping modules. Example: `for_each = toset(["dev","qa","prod"])` — each environment gets its own isolated module instance and state. Enables scalable infra-as-code. |
