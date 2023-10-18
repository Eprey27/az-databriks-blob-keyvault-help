# Creating a Databricks environment in Azure using Blob storage and Azure Key Vault for secrets

Creating a Databricks environment in Azure using Blob storage and Azure Key Vault for secrets via Terraform involves multiple steps. This guide will provide a **high-level** overview and code snippets to help you set up the environment:

1. **Pre-requisites**:
   - Install Terraform.
   - Azure CLI installed and authenticated.

2. **Define the Terraform Provider and Variables**:

```hcl
provider "azurerm" {
  features {}
}

variable "resource_group_name" {
  description = "The name of the resource group."
  default     = "myResourceGroup"
}

variable "location" {
  description = "The Azure Region in which all resources should be created."
  default     = "East US"
}
```

3. **Create a Resource Group**:

```hcl
resource "azurerm_resource_group" "example" {
  name     = var.resource_group_name
  location = var.location
}
```

4. **Create Blob Storage**:

```hcl
resource "azurerm_storage_account" "example" {
  name                     = "mystorageaccount"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "example" {
  name                  = "content"
  storage_account_name  = azurerm_storage_account.example.name
  container_access_type = "private"
}
```

5. **Set up Azure Key Vault and a Secret**:

```hcl
resource "azurerm_key_vault" "example" {
  name                = "myuniquekeyvault"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
 

sku_name               = "standard"
  tenant_id             = "<YOUR_TENANT_ID>"  # You can get this from `az account show`

  access_policy {
    tenant_id = "<YOUR_TENANT_ID>"
    object_id = "<YOUR_SERVICE_PRINCIPAL_OBJECT_ID>" # You can get this from Azure AD for the service principal

    key_permissions = [
      "create",
      "get"
    ]

    secret_permissions = [
      "set",
      "get",
      "delete"
    ]

    certificate_permissions = [
      "create",
      "get"
    ]
  }
}

resource "azurerm_key_vault_secret" "example" {
  name         = "mysecret"
  value        = "mysecretvalue"
  key_vault_id = azurerm_key_vault.example.id
}
```

6. **Create Databricks Workspace and link to Blob Storage**:

First, you need to grant Databricks the necessary permissions to access the Blob Storage:

```hcl
resource "azurerm_role_assignment" "example" {
  principal_id   = "<DATABRICKS_MSI_PRINCIPAL_ID>"  # Managed Service Identity (MSI) principal ID for Databricks
  role_definition_name = "Storage Blob Data Contributor"
  scope            = azurerm_storage_account.example.id
}
```

Then, you can create the Databricks workspace:

```hcl
resource "azurerm_databricks_workspace" "example" {
  name                        = "mydatabricksworkspace"
  resource_group_name         = azurerm_resource_group.example.name
  location                    = azurerm_resource_group.example.location
  sku                         = "standard"
  managed_resource_group_name = "databricks-resources"

  custom_parameters {
    storage_account_name = azurerm_storage_account.example.name
    storage_account_key  = azurerm_key_vault_secret.example.value
  }
}
```

7. **Apply the Terraform Configuration**:
   - Initialize Terraform:
     ```
     terraform init
     ```
   - Plan the deployment:
     ```
     terraform plan
     ```
   - Apply the plan:
     ```
     terraform apply
     ```

8. **Handling Secrets**:
   For production scenarios, avoid hardcoding sensitive information directly in the Terraform configuration. You can use environment variables, input variables, or integrate with Azure Key Vault directly to handle secrets securely.

9. **Permissions**:
   Make sure your Azure service principal or the account you're using with Terraform has the necessary permissions to create and manage the resources specified in the Terraform configuration.

10. **Clean Up**:
   To remove all the resources created:
   ```
   terraform destroy
   ```

This guide gives you a starting point. In real-world scenarios, you might need to adjust configurations, handle more advanced setups, or integrate with other systems/tools. Always refer to official Terraform and Azure documentation for detailed and updated configurations.

# Terraform Project

To organize your Terraform project for the Databricks environment in Azure, using Blob storage and Azure Key Vault, consider adopting a structure that promotes clarity and maintainability.

Here's a recommended directory and file structure:

```
my_databricks_project/
│
├── main.tf               # Main Terraform configurations
├── variables.tf          # Variables declarations
├── outputs.tf            # Output declarations (if any)
│
├── modules/
│   ├── storage/
│   │   ├── main.tf       # Storage-specific Terraform configurations
│   │   ├── variables.tf  # Variables for the storage module
│   │   └── outputs.tf    # Outputs for the storage module
│   │
│   ├── keyvault/
│   │   ├── main.tf       # KeyVault-specific Terraform configurations
│   │   ├── variables.tf  # Variables for the KeyVault module
│   │   └── outputs.tf    # Outputs for the KeyVault module
│   │
│   └── databricks/
│       ├── main.tf       # Databricks-specific Terraform configurations
│       ├── variables.tf  # Variables for the Databricks module
│       └── outputs.tf    # Outputs for the Databricks module
│
└── terraform.tfvars      # File to define variable values
```

Steps to create the directory and files:

1. Create the main project directory:
```bash
mkdir my_databricks_project
cd my_databricks_project
```

2. Create the main Terraform files:
```bash
touch main.tf variables.tf outputs.tf terraform.tfvars
```

3. Create module directories and their respective files:
```bash
mkdir -p modules/storage modules/keyvault modules/databricks

touch modules/storage/{main.tf,variables.tf,outputs.tf}
touch modules/keyvault/{main.tf,variables.tf,outputs.tf}
touch modules/databricks/{main.tf,variables.tf,outputs.tf}
```

Now, you can start populating these files with the configurations provided in the previous answer, segmenting the code based on the module's purpose.

**Notes**:
- Using modules allows you to encapsulate specific logic, which can help in reuse and clarity.
- The `terraform.tfvars` file is where you can provide values for your variables. It's easier to manage and version your configurations when your variable values are separated from the declarations.
- Always be cautious with the `terraform.tfvars` file if it contains sensitive data. Consider using `.gitignore` to avoid pushing secrets if you're using version control like git.

# Using Windows Power Shell

Here's how you can create the folder and files structure for the Terraform project in Windows using PowerShell:

1. **Open PowerShell**.

2. Navigate to the directory where you want to create your project or start from the current directory.

3. Use the following PowerShell commands to create the suggested directory and file structure:

```powershell
# Create the main project directory and navigate into it
New-Item -ItemType Directory -Name "my_databricks_project"
Set-Location .\my_databricks_project

# Create the main Terraform files
New-Item -Type File -Name "main.tf"
New-Item -Type File -Name "variables.tf"
New-Item -Type File -Name "outputs.tf"
New-Item -Type File -Name "terraform.tfvars"

# Create module directories and their respective files
$modules = @('storage', 'keyvault', 'databricks')
foreach ($module in $modules) {
    New-Item -ItemType Directory -Path ".\modules\$module"
    New-Item -Type File -Path ".\modules\$module\main.tf"
    New-Item -Type File -Path ".\modules\$module\variables.tf"
    New-Item -Type File -Path ".\modules\$module\outputs.tf"
}
```

Run the above commands in PowerShell, and you will have the directory and file structure set up as suggested.

Once your project structure is set up, you can start adding your Terraform configurations to the respective files.
