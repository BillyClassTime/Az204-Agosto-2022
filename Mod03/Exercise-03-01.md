### Exercise 3-01: Create a block blob storage account

The block blob storage account type lets you create block blobs with premium performance characteristics. This type of storage account is optimized for workloads with high transactions rates or that require very fast access times.

In this exercise you will create a block blob storage account by using the Azure portal, and in the Cloud Shell using the Azure CLI.

#### Prerequisites

Before you begin make sure you have the following requirements in place:

- An Azure account with an active subscription. If you don't already have one, you can sign up for a free trial at https://azure.com/free.

#### Task 1: Create account in the Azure portal

To create a block blob storage account in the Azure portal, follow these steps:

1. In the Azure portal, select **All services >** the **Storage** category > **Storage accounts**.

2. Under **Storage accounts**, select **+ Create**.

3. In the **Subscription** field, select the subscription in which to create the storage account.

4. In the **Resource group** field, select **Create new** and enter *az204-blob-rg* as the name for the new resource group.

5. In the **Storage account name** field, enter a name for the account. Note the following guidelines:

   - The name must be unique across Azure.
   - The name must be between three and 24 characters long.
   - The name can include only numbers and lowercase letters.

6. In the **Location** field, select a location for the storage account, or use the default location.

7. For the rest of the settings, configure the following:

   | Field                    | Value                                                        |
   | ------------------------ | ------------------------------------------------------------ |
   | **Performance**          | Select **Premium**.                                          |
   | **Premium account type** | Select **Block blobs**.                                      |
   | **Replication**          | Leave the default setting of **Locally-redundant storage (LRS)**. |

8. Select **Review + create** to review the storage account settings.

9. Select **Create**.

#### Task 2: Create account by using Azure Cloud Shell

1. Login to the [Azure portal](https://portal.azure.com/) and open the Cloud Shell.

   - You can also login to the [Azure Cloud Shell](https://shell.azure.com/) directly.

2. Create a new resource group. Replace <myLocation> with a region near you.

   **Note:** Skip this step if you created a resource group in the *Create account in the Azure portal* section above.

   `az group create --name az204-blob-rg --location <myLocation>`

3. Create the block blob storage account. See Step 5 in the *Create account in the Azure portal* instructions above for the storage account name requirements. Replace <myLocation> with a region near you.

   ```
   az storage account create --name <myStorageAcct> --resource-group az204-blob-rg  --location <myLocation> --sku Premium_LRS --kind BlockBlobStorage                     
   ```

**NOTE:** Alternative using PowerShell

```
New-AzResourceGroup -Name az204-blob-rg -Location <myLocation>

New-AzStorageAccount -Name <myStorageAcct> -ResourceGroupName az204-blob-rg -Location <myLocation> -SkuName Premium_LRS -Kind BlockBlobStorage
```



#### Task 3: Clean up resources

When you no longer need the resources in this walkthrough use the following command to delete the resource group and associated resources.

```
az group delete --name az204-blob-rg --no-wait
```

**NOTE:** Alternative using PowerShell

```
Remove-azResourceGroup -Name az204-blob-rg -Force -AsJob
```

