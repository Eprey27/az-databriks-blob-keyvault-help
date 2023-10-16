# **Azure Databricks and Blob Storage Integration Guide**

This guide outlines the steps to install the Databricks CLI locally and then configure a connection between Azure Databricks and Azure Blob Storage securely using secrets.

## **Prerequisites**:
- **Python**: Ensure you have a version of Python installed ([Download Python here](https://www.python.org/downloads/)). Python 3 is recommended.
- **pip**: This is the package installer for Python. It's usually included by default with Python installations. If not, [see here for pip installation](https://pip.pypa.io/en/stable/installation/).
- **Azure Account**: You'll need access to an Azure subscription and permissions to create or manage resources.
- **Azure Databricks Workspace**: Ensure you have an Azure Databricks workspace set up in your Azure subscription. If not, follow [this guide](https://docs.databricks.com/getting-started) to set it up.
- **Azure Storage Account**: Make sure you have an Azure Storage account and a Blob container where you intend to store your data. [Learn how to create one here](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create).

## **1. Installing the Databricks CLI**:
Prerequisites: You should have Python and pip installed on your machine.

Install the Databricks CLI using pip:
```
pip install databricks-cli
```

## **2. Configuring the Databricks CLI**:
After installation, set up the CLI to connect to your Azure Databricks workspace:
```
databricks configure --token
```
You will be prompted to provide the URL of your Azure Databricks workspace (e.g., `https://westeurope.azuredatabricks.net`) and a personal access token.

To obtain a personal access token:
- Sign in to your Azure Databricks workspace.
- Click on your profile icon in the top-right corner and select User Settings.
- Navigate to the Access Tokens tab.
- Click Generate New Token, provide a descriptive name for the token, and optionally set an expiration date.
- Copy the generated token and use it when setting up the CLI.

**Quick Test**: To ensure the CLI is configured properly, try executing `databricks workspace ls`. This should list the top-level items in your workspace.

## **3. Creating and Configuring Azure Storage Account**:
- Sign in to the [Azure portal](https://portal.azure.com/).
- Create a new storage account if you don't already have one. If unfamiliar, use [this guide](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create).
- Within that storage account, navigate to the Blob service section and create a new container.
- Go to the Access keys section in the settings menu.
- Copy one of the Connection strings you find there (either key1 or key2).

## **4. Creating and Configuring Secret Scope in Databricks**:
Use the Databricks CLI to create a new secret scope:
```
databricks secrets create-scope --scope <scope_name>
```
Store the Shared Key you copied earlier in the secret scope you created:
```
databricks secrets put --scope <scope_name> --key <secret_name> --string-value "<Copied_Connection_String>"
```

## **5. Accessing Azure Blob Storage from a Databricks Notebook**:
In your Databricks notebook, access the secret and set up access to Azure Blob Storage:
```python
connection_string = dbutils.secrets.get(scope="<scope_name>", key="<secret_name>")
spark.conf.set("spark.hadoop.fs.azure", "org.apache.hadoop.fs.azure.NativeAzureFileSystem")
spark.conf.set("spark.hadoop.fs.azure.account.key.<your_account_name>.blob.core.windows.net", connection_string)
```
You can now access your data in Azure Blob Storage using Spark:
```python
df = spark.read.text("wasbs://<your_container>@<your_account_name>.blob.core.windows.net/<file_path>")
```

## **6. Troubleshooting**:
- Ensure your connection string is correct.
- Check if the secret scope and key names in Databricks match with what you've provided in your notebook.
- Verify that you've granted the necessary permissions on your Blob storage.

With these steps, you should be able to set up the Databricks CLI locally and then connect Azure Databricks with Azure Blob Storage securely using secrets.