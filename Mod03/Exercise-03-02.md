### Exercise: Create blob storage resources by using the .NET client library

This exercise uses the Azure Blob storage client library to show you how to perform the following actions on Azure Blob storage in a console app:

- Create a container
- Upload blobs to a container
- List the blobs in a container
- Download blobs
- Delete a container

#### Prerequisites

- An Azure account with an active subscription. If you don't already have one, you can sign up for a free trial at https://azure.com/free.
- **Visual Studio Code**: You can install Visual Studio Code from [https://code.visualstudio.com](https://code.visualstudio.com/).
- **Azure CLI**: You can install the Azure CLI from https://docs.microsoft.com/cli/azure/install-azure-cli.
- The **.NET Core 3.1 SDK**, or **.NET 5.0 SDK**. You can install from https://dotnet.microsoft.com/download.

#### Task 1: Setting up

Perform the following actions to prepare Azure, and your local environment, for the exercise.

1. Start Visual Studio Code and open a terminal window by selecting **Terminal** from the top application bar, then selecting **New Terminal**.

2. Login to Azure by using the command below. A browser window should open letting you choose which account to login with.

   ```
   az login
   ```

   ``` 
   Connect-AzAccount
   ```

   

3. Create a resource group for the resources needed for this exercise. Replace <myLocation> with a region near you.

   ```
   az group create --location <myLocation> --name az204-blob-rg
   ```

   ```
   New-AzResourceGroup -Name az204-blob-rg -Location <myLocation>
   ```

   

4. Create a storage account. We need a storage account created to use in the application. Replace <myLocation> with the same region you used for the resource group. Replace <myStorageAcct> with a unique name.

   ```
   az storage account create --resource-group az204-blob-rg --name <myStorageAcct> --location <myLocation> --sku Standard_LRS
   ```

   

   ```
   New-AzStorageAccount -Name <myStorageAcct> -ResourceGroupName az204-blob-rg -Location <myLocation> -SkuName Standard_LRS
   ```

   

   **Note:** Storage account names must be between 3 and 24 characters in length and may contain numbers and lowercase letters only. Your storage account name must be unique within Azure. No two storage accounts can have the same name.

   

5. Get credentials for the storage account.

   - Navigate to the [Azure portal](https://portal.azure.com/).
   - Locate the storage account created.
   - Select **Access keys** in the **Security + networking** section of the navigation pane. Here, you can view your account access keys and the complete connection string for each key.
   - Find the **Connection string** value under **key1**, and select the **Copy** button to copy the connection string. You will add the connection string value to the code in the next section.
   - In the **Blob** section of the storage account overview, select **Containers**. Leave the windows open so you can view changes made to the storage as you progress through the exercise.

Note: Using PowerShell or Azure Cli to get Storage Account Key

```
Get-AzStorageAccountKey -ResourceGroupName az204-blob-rg -Name <myStorageAcct>
```

```
az storage account keys list -g az204-blob-rg -n <myStorageAcct>
```

```
DefaultEndpointsProtocol=https;AccountName=<myStorageAcct>;AccountKey=<key1 or Key2>;EndpointSuffix=core.windows.net
```

Note: Using PowerShell or Azure CLI to get Connection String directly

```
az storage account show-connection-string --resource-group az204-blob-rg --name <myStorageAcct>
```

```
$rgName = az204-blob-rg
$accountName = <myStorageAcct>

(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).Context.ConnectionString
```



#### Task 2: Prepare the .NET project

In this section we'll create project named *az204-blob* and install the Azure Blob Storage client library.

1. In the VS Code terminal navigate to a directory where you want to store your project.

2. In the terminal, use the dotnet new command to create a new console app. This command creates a simple “Hello World” C# project with a single source file: *Program.cs*.

   ```
   dotnet new console -f netcoreapp3.1 -n az204-blob

3. Use the following commands to switch to the newly created *az204-blob* folder and build the app to verify that all is well.

   `cd az204-blob `

   `dotnet build`

4. Inside the *az204-blob* folder, create another folder named *data*. This is where the blob data files will be created and stored.

   `mkdir data`

5. While still in the application directory, install the Azure Blob Storage client library for .NET package by using the dotnet add package command.

   `dotnet add package Azure.Storage.Blobs`

   **Tip:** Leave the console window open so you can use it to build and run the app later in the exercise.

6. Open the *Program.cs* file in your editor, and replace the contents with the following code.

   ```c#
   using Azure.Storage.Blobs;
   using Azure.Storage.Blobs.Models;
   using System;
   using System.IO;
   using System.Threading.Tasks;
   using static System.Console;
   
   namespace az204_blob
   {
       class Program
       {
           public static void Main()
           {
               WriteLine("Azure Blob Storage exercise\n");
   
               // Run the examples asynchronously, wait for the results before proceeding
               ProcessAsync().GetAwaiter().GetResult();
   
               WriteLine("Press enter to exit the sample application.");
               ReadLine();
   
           }
   
           private static async Task ProcessAsync()
           {
               // Copy the connection string from the portal in the variable below.
               // Format: DefaultEndpointsProtocol=https;AccountName=<myStorageAcct>;AccountKey=<key1 or Key2>;EndpointSuffix=core.windows.net
               string storageConnectionString = "CONNECTION STRING";
   
               // Create a client that can authenticate with a connection string
               BlobServiceClient blobServiceClient = new BlobServiceClient(storageConnectionString);
   
               // EXAMPLE CODE STARTS BELOW HERE
   
   
           }
       }
   }
   ```

   

7. Set the storageConnectionString variable to the value you copied from the portal.

#### Task 3: Build the full app

For each of the following sections below you'll find a brief description of the action being taken as well as the code snippet you'll add to the project. Each new snippet is appended to the one before it, and we'll build and run the console app at the end.

For each example below copy the code and append it to the previous snippet in the example code section of the *Program.cs* file.

##### Create a container

To create the container first create an instance of the BlobServiceClient class, then call the CreateBlobContainerAsync method to create the container in your storage account. A GUID value is appended to the container name to ensure that it is unique. The CreateBlobContainerAsync method will fail if the container already exists.

```c#
//Create a unique name for the container
string containerName = "wtblob" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
WriteLine("A container named '" + containerName + "' has been created. " +
    "\nTake a minute and verify in the portal." +
    "\nNext a file will be created and uploaded to the container.");
WriteLine("Press 'Enter' to continue.");
ReadLine();
```

##### Upload blobs to a container

The following code snippet gets a reference to a BlobClient object by calling the GetBlobClient method on the container created in the previous section. It then uploads the selected local file to the blob by calling the UploadAsync method. This method creates the blob if it doesn't already exist, and overwrites it if it does.

```c#
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "wtfile" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to the blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();

WriteLine("\nThe file was uploaded. We'll verify by listing" +
        " the blobs next.");
WriteLine("Press 'Enter' to continue.");
ReadLine();
```

##### List the blobs in a container

List the blobs in the container by using the GetBlobsAsync method. In this case, only one blob has been added to the container, so the listing operation returns just that one blob.

```c#
// List blobs in the container
WriteLine("Listing blobs...");
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    WriteLine("\t" + blobItem.Name);
}

WriteLine("\nYou can also verify by looking inside the " +
        "container in the portal." +
        "\nNext the blob will be downloaded with an altered file name.");
WriteLine("Press 'Enter' to continue.");
ReadLine();
```

##### Download blobs

Download the blob created previously to your local file system by using the DownloadAsync method. The example code adds a suffix of “DOWNLOADED” to the blob name so that you can see both files in local file system.

```c#
// Download the blob to a local file
// Append the string "DOWNLOADED" before the .txt extension
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
WriteLine("\nLocate the local file to verify it was downloaded.");
WriteLine("The next step is to delete the container and local files.");
WriteLine("Press 'Enter' to continue.");
ReadLine();
```

##### Delete a container

The following code cleans up the resources the app created by deleting the entire container using DeleteAsync. It also deletes the local files created by the app.

```c#
// Delete the container and clean up local files created
WriteLine("\n\nDeleting blob container...");
await containerClient.DeleteAsync();

WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

WriteLine("Finished cleaning up.");
```

#### Task 4: Run the code

Now that the app is complete it's time to build and run it. Ensure you are in your application directory and run the following commands:

```
dotnet build 
dotnet run
```

There are many prompts in the app to allow you to take the time to see what's happening in the portal after each step.

#### Task 5: Clean up other resources

The app deleted the resources it created. You can delete all of the resources created for this exercise by using the command below. You will need to confirm that you want to delete the resources.

```
az group delete --name az204-blob-rg --no-wait
```

```
Remove-azResourceGroup -Name az204-blob-rg
```

