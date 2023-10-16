# Azure Databricks and Blob Storage Integration Guide

This guide outlines the steps to install the Databricks CLI locally and then configure a connection between Azure Databricks and Azure Blob Storage securely using secrets.

## Prerequisites:

- **Python**: Ensure you have a version of Python installed (Python 3 is recommended).
- **pip**: This is the package installer for Python. It's usually included by default with Python installations.
- **Azure Account**: You'll need access to an Azure subscription and permissions to create or manage resources.
- **Azure Databricks Workspace**: Ensure you have an Azure Databricks workspace set up in your Azure subscription.
- **Azure Storage Account**: Make sure you have an Azure Storage account and a Blob container where you intend to store your data.


## 1. Installing the Databricks CLI:

**Prerequisites**: You should have Python and `pip` installed on your machine.

- Install the Databricks CLI using `pip`:

  ```bash
  pip install databricks-cli
  ```

## 2. Configuring the Databricks CLI:

1. After installation, set up the CLI to connect to your Azure Databricks workspace:
   
   ```bash
   databricks configure --token
   ```

2. You will be prompted to provide the URL of your Azure Databricks workspace (e.g., `https://westeurope.azuredatabricks.net`) and a personal access token.

**To obtain a personal access token**:

- Sign in to your Azure Databricks workspace.
- Click on your profile icon in the top-right corner and select `User Settings`.
- Navigate to the `Access Tokens` tab.
- Click `Generate New Token`, provide a descriptive name for the token, and optionally set an expiration date.
- Copy the generated token and use it when setting up the CLI.

## 3. Creating and Configuring Azure Storage Account:

1. Sign in to the Azure portal.
2. Create a new storage account if you don't already have one.
3. Within that storage account, navigate to the `Blob service` section and create a new container.
4. Go to the `Access keys` section in the settings menu.
5. Copy one of the `Connection strings` you find there (either key1 or key2).

## 4. Creating and Configuring Secret Scope in Databricks:

1. Use the Databricks CLI to create a new `secret scope`:
   
   ```bash
   databricks secrets create-scope --scope <scope_name>
   ```

2. Store the Shared Key you copied earlier in the `secret scope` you created:

   ```bash
   databricks secrets put --scope <scope_name> --key <secret_name> --string-value "<Copied_Connection_String>"
   ```

## 5. Accessing Azure Blob Storage from a Databricks Notebook:

1. In your Databricks notebook, access the secret and set up access to Azure Blob Storage:

   ```python
   connection_string = dbutils.secrets.get(scope="<scope_name>", key="<secret_name>")
   spark.conf.set("spark.hadoop.fs.azure", "org.apache.hadoop.fs.azure.NativeAzureFileSystem")
   spark.conf.set("spark.hadoop.fs.azure.account.key.<your_account_name>.blob.core.windows.net", connection_string)
   ```

2. You can now access your data in Azure Blob Storage using Spark:

   ```python
   df = spark.read.text("wasbs://<your_container>@<your_account_name>.blob.core.windows.net/<file_path>")
   ```

With these steps, you should be able to set up the Databricks CLI locally and then connect Azure Databricks with Azure Blob Storage securely using secrets.