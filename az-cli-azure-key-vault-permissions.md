Cheat sheet for managing Azure Key Vault permissions using the `az` CLI:

1. **Login to Azure**:
   ```bash
   az login
   ```

2. **List Key Vaults in a Subscription**:
   ```bash
   az keyvault list --resource-group 'YourResourceGroupName'
   ```

3. **Set a Key Vault Access Policy for a User**:
   ```bash
   az keyvault set-policy --name 'YourKeyVaultName' --upn user@example.com --key-permissions get list --secret-permissions get list
   ```

4. **Set a Key Vault Access Policy for a Service Principal/Application**:
   ```bash
   az keyvault set-policy --name 'YourKeyVaultName' --object-id 'YourObjectId' --key-permissions get list --secret-permissions get list
   ```

5. **List Access Policies for a Key Vault**:
   ```bash
   az keyvault show --name 'YourKeyVaultName' --query "properties.accessPolicies"
   ```

6. **Remove an Access Policy from a Key Vault for a User**:
   ```bash
   az keyvault delete-policy --name 'YourKeyVaultName' --upn user@example.com
   ```

7. **Remove an Access Policy from a Key Vault for a Service Principal/Application**:
   ```bash
   az keyvault delete-policy --name 'YourKeyVaultName' --object-id 'YourObjectId'
   ```

8. **List Secrets in a Key Vault**:
   ```bash
   az keyvault secret list --vault-name 'YourKeyVaultName'
   ```

9. **List Keys in a Key Vault**:
   ```bash
   az keyvault key list --vault-name 'YourKeyVaultName'
   ```

10. **List Certificates in a Key Vault**:
    ```bash
    az keyvault certificate list --vault-name 'YourKeyVaultName'
    ```

11. **Backup a Secret**:
    ```bash
    az keyvault secret backup --vault-name 'YourKeyVaultName' --name 'YourSecretName' --file backup-file-name
    ```

12. **Restore a Secret**:
    ```bash
    az keyvault secret restore --vault-name 'YourKeyVaultName' --file backup-file-name
    ```

Permissions (like `get`, `list`, `set`, `delete`, etc.) can be set specifically for keys, secrets, and certificates. Make sure to review and grant only the required permissions, following the principle of least privilege.

Remember, always replace placeholders like `'YourKeyVaultName'`, `'YourResourceGroupName'`, `user@example.com`, etc., with your actual values.
