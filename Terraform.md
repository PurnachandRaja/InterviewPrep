### 1.What is the purpose of Meta Arguments in Terraform?
In Terraform, meta-arguments are a special type of argument that are used to control the behavior of resources, data sources, and other Terraform constructs

Some common meta-arguments include:

depends_on: This meta-argument specifies a dependency relationship between resources, data sources, or other Terraform constructs. If a resource depends on another resource using depends_on, Terraform will ensure that the dependent resource is created or updated before the dependent resource is processed.

count: This meta-argument allows you to create multiple instances of a resource based on a variable or expression. For example, you can use count to create multiple EC2 instances in an Auto Scaling Group.

lifecycle: This meta-argument allows you to configure the lifecycle behavior of a resource. For example, you can use lifecycle to prevent a resource from being destroyed or replaced during a Terraform apply operation.

provider: This meta-argument allows you to specify the provider for a resource, data source, or other Terraform construct. This is useful when you have multiple providers configured in your Terraform project.

### 2.What are the best practices to deploy resources in Multiple environments ?
We can use terraform workspace or reusable modules. 
Terraform workspace – it allows to manage separate statefile for each workspace
Reusable modules – This is an architecture where our config files are stored in a single directory with module block and source that directory pass to that variables

### 3.What is the purpose of Terraform provisioners ?
Provisioners are used for executing scripts or shell commands on a local or remote machine as part of resource creation/deletion. They are similar to “Azure VM user data” scripts that only run once on the creation and if it fails terraform marks it tainted. A tainted resource is one which is planned for destruction on next terraform apply. Terraform doesn’t run these scripts multiple times. If we want more flexibility then we must use a configuration management tool like ansible to properly provision the instance.

The file provisioner copies files or directories from the machine running Terraform to the newly created resource. The file provisioner supports both ssh and winrm type connections.
The local-exec provisioner helps run a script on instance where we are running our terraform code, not on the resource we are creating.
The remote-exec provisioner invokes a script on a remote resource after it is created. This can be used to run a configuration management tool, bootstrap into a cluster, etc. To invoke a local process, see the local-exec provisioner instead. The remote-exec provisioner requires a connection and supports both ssh and winrm.

### 4.What is the purpose of the Data sources in terraform ?
Data sources allow Terraform to use information defined outside of Terraform, defined by another separate Terraform configuration, or modified by functions.

### 5.What is Terraform Backend? Remote Backend?
A backend defines where Terraform stores its state data files.

Terraform uses persisted state data to keep track of the resources it manages. Most non-trivial Terraform configurations use a backend to store state remotely. This lets multiple people access the state data and work together on that collection of infrastructure resources.

### 7.How do you handle secrets in terraform?
Using Environment variables
Declare variables and use sensitive = true
```terraform
# DB Username 
variable "mysql_db_username" {
  description = "Azure MySQL Database Administrator Username"
  type        = string
  sensitive   = true
}
# DB Password - Enable Sensitive flag
variable "mysql_db_password" {
  description = "Azure MySQL Database Administrator Password"
  type        = string
  sensitive   = true
}
```

Next, pass the variables to the Terraform resources that need those secrets:
```terraform
resource "azurerm_mysql_server" "mysql_server" {
  name                = "${local.resource_name_prefix}-${var.mysql_db_name}"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  administrator_login          = var.mysql_db_username
  administrator_login_password = var.mysql_db_password
}
```


### 8.What is terraform state? What is state locking? What is Terraform state rollback? or
terraform state file is used to persist the data of the current state of resources in a cloud. 
State locking is a feature which allows us to work collobaratively in a team it means the state gets locked out when someone in team ran terraform init and will not allow others to make changes at the same time which will create conflicts with the resource changes and will only be released after apply is finished. 
Rolling back to previous state can be achieved by storing state files in form of versions. We can enable versioning on azure blob which will store a version everytime 

### 9.What are modules? Do you know/Have you used any modules?
Terraform modules are reusable units of Terraform configuration that can be used to encapsulate and abstract infrastructure resources.

Use modules to reduce duplication and promote code reuse across your infrastructure. Modules can be published and shared across teams or organizations, and can be versioned and updated independently of other modules or Terraform code.


### 10.Let us say statefile is accidentally deleted? What happens now and how to deal with it or avoid it?
If the Terraform state file is accidentally deleted, Terraform will no longer have access to the current state of the infrastructure. This can cause issues such as not being able to manage or update existing resources or incorrectly creating new resources that conflict with the existing ones. To deal with the situation where the state file is deleted,  you can restore it from a backup.
  

### 11.How to do changes in the configuration of already created resources using terraform? 
To make changes to an already created resource using Terraform, you can update the configuration file to reflect the changes you want to make and then run the "terraform apply" command. Terraform will compare the current state of the resource with the desired state defined in the configuration file, and apply the necessary changes to bring the resource into the desired state.

### 12. Explain terraform lifecycle Meta Argument?
The Terraform lifecycle Meta Argument is a way to control the behavior of resources in Terraform during certain lifecycle events, such as creation, updates, or deletions. The meta-argument is used to set various lifecycle options for a resource.

Some of the commonly used lifecycle Meta Arguments are:

create_before_destroy: When set to true, this option ensures that the new resource is created before the old one is destroyed during an update. This can help avoid downtime by ensuring that the new resource is up and running before the old one is removed.

prevent_destroy: When set to true, this option prevents the resource from being destroyed. This can be useful for critical resources that should not be deleted accidentally.

ignore_changes: When set to a list of attributes, this option tells Terraform to ignore changes to those attributes during an update. This can be useful for attributes that may change frequently but are not important to the overall configuration.

delete_before_replace: When set to true, this option ensures that the old resource is deleted before the new one is created during an update. This can be useful for resources that cannot be updated in-place, but must be replaced entirely.

### 13. Let’s say we have 20 resources, everything is running fine on Azure/AWS/GCP but we have to destroy a resource can you do that? 
Yes, in Terraform you can destroy a specific resource by running the terraform destroy command and specifying the resource to destroy

```
terraform destroy -target=resourcetype.resourcename
terraform destroy -target=azurerm_virtual_network.example
```

### 14. How do you preserve the key that you have used which is created using terraform?


### 15. How to pass variables in terraform at runtime?
We can store variables in a varfile and pass it while running plan and apply 

```
terraform plan -var-file=./stage.tfvars
```
### 16. Drawbacks of terraform that you have faced in your career?

### 17.Explain Core Terraform end-to-end workflow to deploy and delete resources in Azure/AWS/GCP?
Write: Writing IaaC in tf config files
init: Initializes working directory containing tf config files (creates .terraform folder ic which providers & modules will be downloaded)
validate: Validates tf config files for syntactical and internal consitent
plan: Preview changes before applying
apply: Provisions reproducible infratructure on cloud
destroy: Delete/Destroy Terraform managed infrastructure on cloud

fmt command us used to rewrite Terraform configuration to a canonical format

### 18. We have existing Terraform Infrastructure created in Azure/AWS/GCP cloud, now one particular resource needs to be re-created whenever we do the next apply?
We can achieve this using a concept terraform tainting
Terraform Taint:
- Terraform taint command informs Terraform that a particular object has become degraded or damaged and needed to be replaced in next Apply.

```
terraform state list
terraform taint "resource_name" (#check resource status in .tfstate file it will change to tainted)
terraform apply
```

To untaint
```
terraform untaint "resource_name"
```

Terraform taint command is deprecated use -replace option with terraform apply instead
```
terraform apply -replace="resource_name"
```

### 19.Explain or walk-through step by step process to secure .tfstate file and by making it redily available for other developers within team?
Terraform Backend:
- Backends define where terraform's state snapshots are stored

```
terraform{
  backend "azurerm" {
    resource_group_name  = "StorageAccount-ResourceGroup"
    storage_account_name = "chandu8978"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
```

### 20. Who creates the "terraform.tfstate.backup" file and under which scenario it is created?
- Backup state file is automatically created, when terraform destroy command is executed.
- The above is done to restore infrastructure to the same state, prior running terraform destroy command.

### 21.Difference between child and parent module?
In Terraform, a child module is a module that is called by another module and provides resources that are used in the parent module. A parent module is the module that calls one or more child modules and uses their resources to create infrastructure.

Use child modules to encapsulate and abstract infrastructure resources that are used in multiple parent modules. Use parent modules to compose and orchestrate infrastructure resources from multiple child modules into a cohesive whole.

### 22. What is terraform import? Can we use it to recover lost or deleted statefile?
Terraform import is useful when you have already created infrastructure outside of Terraform and want to start managing it with Terraform. By importing the existing resources into Terraform state, you can apply changes to them using Terraform, and Terraform will track the current state of the resources. However, it is not designed to recover or restore a lost or deleted state file.
