### Exercise: Set and retrieve a secret from Azure Key Vault by using Azure CLI

In this exercise you'll learn how to perform the following actions by using the Azure CLI:

- Create a Key Vault
- Add and retrieve a secret

#### Prerequisites

- 
  - An **Azure account** with an active subscription. If you don't already have one, you can sign up for a free trial at https://azure.com/free

#### Login to Azure and start the Cloud Shell

1. Login to the [Azure portal](https://portal.azure.com/) and open the Cloud Shell.

   ![The location of Cloud Shell launch button.](../Images/01-01.png)

2. After the shell opens be sure to select the **Bash** environment.

   ![Selecting the Bash environment.](../Images/01-02.png)

#### Create a Key Vault

1. Let's set some variables for the CLI commands to use to reduce the amount of retyping. Replace the <myLocation> variable string below with a region that makes sense for you. The Key Vault name needs to be a globally unique name, and the script below generates a random string.

   ```
   myKeyVault=az204vault-$RANDOM 
   myLocation=<myLocation>
   ```

   

2. Create a resource group.

   ```
   az group create --name az204-vault-rg --location $myLocation
   ```

   

3. Create a Key Vault by using the az keyvault create command.

   ```
   az keyvault create --name $myKeyVault --resource-group az204-vault-rg --location $myLocation
   ```

   **Note:** This can take a few minutes to run.

#### Add and retrieve a secret

To add a secret to the vault, you just need to take a couple of additional steps.

1. Create a secret. Let's add a password that could be used by an app. The password will be called **ExamplePassword** and will store the value of **hVFkk965BuUv** in it.

   ```
   az keyvault secret set --vault-name $myKeyVault --name "ExamplePassword" --value "hVFkk965BuUv"
   ```

2. Use the az keyvault secret show command to retrieve the secret.

   ```
   az keyvault secret show --name "ExamplePassword" --vault-name $myKeyVault
   ```

   This command will return some JSON. The last line will contain the password in plain text.

   `"value": "hVFkk965BuUv"`

You have created a Key Vault, stored a secret, and retrieved it.

#### Clean up resources

When you no longer need the resources in this exercise use the following command to delete the resource group and associated Key Vault.

```
az group delete --name az204-vault-rg --no-wait 
```

