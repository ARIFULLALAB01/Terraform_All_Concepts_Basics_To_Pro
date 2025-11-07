# Terraform All Scenario Questions — Must Read Once

*(Full detailed Markdown document combining Q1–Q22 answers in interview-ready, real-world format)*

---

## **1) Can you tell me what are all the daily activities that you do in Terraform?**

Every day, I handle cloud setup or change requests using Terraform.  
I build, update, or fix Azure infrastructure as per application team needs — making sure everything is automated, tested, and version-controlled.

**Part of Azure Practice:**  
I work in an Azure environment, handling Terraform-based infrastructure activities.

**Work Based on Requests (Not Daily Builds):**  
Terraform work happens when there’s a change request — like new application onboarding or infra modification — not daily testing.

**Application Onboarding:**  
When a new app team wants to deploy to Azure, they raise a request.  
➜ We identify which resources (like Function App, Storage, Key Vault, etc.) are needed.  
➜ Then, we create Terraform modules and scripts to deploy them.

**Infrastructure Changes:**  
Handle infra updates (like scaling, adding new resources, or configuration changes).  
These come as change requests or incident tickets.

**Issue Fixes:**  
If the application team faces issues (example: missing resource, wrong config, or access problem),  
➜ we troubleshoot and update Terraform code accordingly.

**Code Updates & Review:**  
Update Terraform variables, backend, and state files when infrastructure changes.  
Make sure all changes are version-controlled in Git.

**Plan → Apply Flow:**  
Before deployment, run `terraform plan` to check what will change.  
Then, after validation, run `terraform apply` to make changes live.

**Environment Maintenance:**  
Maintain Dev / QA / Prod environments with consistent configurations and separate state management.

---

## **2) What are Services that you have Worked with and wrote Terraform files?**

I’ve worked with almost all major Azure services (50–60+ total) through Terraform — mainly compute, networking, storage, database, eventing, and security components.  
I build and automate them based on application team requests.

Recently, one app team had one Function App, and they requested to scale it to three Function Apps with Cosmos DB, Key Vault, and Event Hub integration.  
I modified Terraform modules, handled dependencies and ordering, and deployed everything in Dev, QA, and Prod environments seamlessly.

**Terraform — Services I Worked With:**  
• Resource Group (RG)  
• Virtual Network (VNet) and Subnets  
• Network Interface (NIC) and Public IP  
• Virtual Machines (VMs)  
• Storage Accounts  
• Azure App Services (Web App & Function App)  
• Cosmos DB  
• Azure SQL Database  
• Event Hub / Event Grid  
• Key Vault  
• Azure App Configuration  
• Recovery Services Vault

---

## **3) Tell me a Scenario Where you come Across Provisioners?**

**Requirement:**  
The application team wanted Virtual Machines (VMs) that should have HashiCorp Vault pre-installed and enabled automatically after creation.

**Provisioners Used:**  
- File Provisioner: Copies install script (e.g., `install_vault.sh`) to VM.  
- Remote-Exec Provisioner: Executes that script via SSH.  
- Local-Exec Provisioner: Runs local validation or notifications.

**Alternative in Azure:**  
Use **Azure VM Extensions** — cloud-native and cleaner than provisioners.

---

## **4) What are Plugins and Providers in Terraform?**

**Providers** tell Terraform which cloud platform to use.  
**Plugins** are the executables that make that connection work.

Example:
```hcl
provider "azurerm" {
  features {}
}
```

When you run `terraform init`, Terraform downloads the Azure plugin from the registry.  
Providers act like translators between Terraform and the cloud APIs.

---

## **5) How do you deploy Terraform code, Manually or with Automation?**

Our Terraform code is stored in Git and deployed through **Azure DevOps or GitLab pipelines**, not manually.  
The state file is stored in a **remote backend** with locking enabled.

**CI/CD Pipeline Steps:**  
1. `terraform init`  
2. `terraform plan`  
3. Review/approval  
4. `terraform apply`

**State Locking Example:**  
When a pipeline runs `apply`, Terraform locks the state. Other runs wait until the lock is released — preventing corruption.

---

## **6) When you want to deploy the same Terraform code on Different Environments — what’s the Best Strategy?**

Keep one common Terraform codebase.  
Use separate `.tfvars` files for each environment (dev, uat, prod).

Example:
```bash
terraform apply -var-file="dev.tfvars"
terraform apply -var-file="uat.tfvars"
```

✅ One codebase, multiple environments.  
✅ No duplication, easy maintenance.  
✅ Safe separation via backend & tfvars.

---

## **7) How do you Standardize Terraform Code Across Teams?**

Use **Modules** to make Terraform reusable and standardized.  
Each module (like VM, Key Vault, Network) is used by many teams with different variable inputs.

**Benefits:**  
✅ Reusable code  
✅ Consistent standards  
✅ Easier maintenance

**Example Structure:**
```
/modules/vm/main.tf
/environments/dev/main.tf → calls module.vm
/environments/uat/main.tf → calls module.vm
```

---

## **8) How do you call output of one Module in Another?**

Define outputs in one module and reference them in another.

```hcl
output "keyvault_id" {
  value = azurerm_key_vault.example.id
}

module "functionapp" {
  source = "./modules/functionapp"
  kv_id  = module.keyvault.keyvault_id
}
```

✅ Avoids duplication  
✅ Maintains dependencies automatically

---

## **9) How to delete only one resource via Terraform?**

Use:
```bash
terraform destroy -target=<resource_name>
```
→ Deletes resource from Azure & state.

Or remove from tracking only:
```bash
terraform state rm <resource_name>
```
→ Resource remains but Terraform stops managing it.

---

## **10) Can we Merge Two Different State Files?**

❌ No, direct merging isn’t supported.  
Each `.tfstate` tracks its own resources.

✅ Use `terraform import` to bring resources into one state.  
✅ Use `data` sources to reference existing ones.

---

## **11) Few Challenges faced while working with Terraform?**

**Main Challenges:**
1. Manual portal changes → Drift issues.  
2. Dependency conflicts → Cascading failures.  
3. Importing existing resources → Tedious.  
4. State lock conflicts → Solved via remote backend.  
5. Provider version mismatch → Solved via version pinning.

**Fixes:**  
- Enforced no-manual-change policy.  
- Used `depends_on` & `prevent_destroy`.  
- Adopted remote backend with locking.  
- Automated drift detection via `plan -refresh-only`.

---

## **12) Best Way to Authenticate Terraform to Azure?**

Use **Service Principal (SPN)** instead of user credentials.  
It’s secure, programmatic, and works in pipelines.

```hcl
provider "azurerm" {
  features {}
  subscription_id = var.subscription_id
  client_id       = var.client_id
  client_secret   = var.client_secret
  tenant_id       = var.tenant_id
}
```

✅ Secure  
✅ Least-privilege RBAC  
✅ Works in CI/CD

---

## **13) How to ensure one resource is created before another?**

Use `depends_on`.

```hcl
resource "azurerm_key_vault" "example" { ... }

resource "azurerm_function_app" "example" {
  depends_on = [azurerm_key_vault.example]
}
```

✅ Guarantees order of creation  
✅ Avoids race conditions

---

## **14) What is Null Resource in Terraform?**

A **null_resource** doesn’t create real infra.  
Used for running commands or scripts post-deployment.

Example:
```hcl
resource "null_resource" "post_vm_script" {
  provisioner "remote-exec" {
    inline = ["bash /tmp/setup.sh"]
  }
}
```

✅ Used for automation steps  
✅ Not for real resources

---

## **15) What happens if State File is Deleted?**

Terraform loses its memory.  
It forgets all created resources and may try to recreate them.

✅ Always store state in **remote backend**.  
✅ Enable **versioning** for recovery.  
✅ Never keep local `.tfstate` in shared teams.

---

## **16) Can Terraform Be Used for On-Prem Infrastructure Automation?**

✅ Yes — Terraform can automate VMware, OpenStack, Cisco UCS, Nutanix, etc.  
It uses provider plugins to communicate with on-prem APIs.

**Example:**
```hcl
provider "vsphere" {
  user           = var.vsphere_user
  password       = var.vsphere_password
  vsphere_server = var.vsphere_server
}
```

✅ Works for both cloud & on-prem infra.

---

## **18) How to call Existing Resources without Hardcoding or Import?**

Use `data` blocks to read existing infra dynamically.

```hcl
data "azurerm_resource_group" "existing" {
  name = "rg-prod"
}

resource "azurerm_storage_account" "sa" {
  resource_group_name = data.azurerm_resource_group.existing.name
}
```

✅ No import needed  
✅ Avoids duplication

---

## **19) If we give count = 0 in a Resource?**

Terraform skips that resource.  
If it already exists → Terraform destroys it.

✅ Used for **conditional creation**  
Example:  
```hcl
count = var.create_vm ? 1 : 0
```

---

## **20) What is Dynamic Block in Terraform?**

Used to create multiple nested blocks dynamically.

Example:
```hcl
dynamic "container" {
  for_each = var.containers
  content {
    name = each.value
  }
}
```

✅ Reduces repetition  
✅ Useful for lists or maps

---

## **21) Best Practices in Terraform**

✅ Use Modules for Reusability  
✅ Store State Remotely  
✅ Lock Versions  
✅ Validate Code (`fmt`, `validate`, `plan`)  
✅ Secure Secrets in Key Vault  
✅ Enforce Approvals in CI/CD

---

## **22) What CI/CD tool used to deploy Terraform and version used?**

Used **Azure DevOps** & **GitLab CI/CD**.  
Pipeline flow: init → validate → plan → apply.  
Used **Terraform v1.3.x** across all environments for consistency.

---

**✅ End of Complete Terraform Scenario Questions (Q1–Q22)**  
**Perfect for Real-World & Interview Prep (Azure, AWS, CI/CD, GitLab, ADO)**

