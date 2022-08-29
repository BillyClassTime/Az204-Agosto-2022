### Exercise 1-01: Create a static HTML web app by using Azure Cloud Shell

After gathering information about App Service you've decided to create and update a simple web app to try it out. In this exercise you'll deploy a basic HTML+CSS site to Azure App Service by using the Azure CLI az webapp up command. You will then update the code and redeploy it by using the same command.

The ```az webapp up``` command makes it easy to create and update web apps. When executed it performs the following actions:

- Create a default resource group.
- Create a default app service plan.
- Create an app with the specified name.
- Zip deploy files from the current working directory to the web app.

#### Prerequisites

Before you begin make sure you have the following requirements in place:

- An Azure account with an active subscription. If you don't already have one, you can sign up for a free trial at https://azure.com/free.

#### Task 1: Login to Azure and download the sample app

1. Login to the [Azure portal](https://portal.azure.com/) and open the Cloud Shell.

   ![](../Images/01-01.png)

2. After the shell opens be sure to select the **Bash** environment.

   ![](../Images/01-02.png)

3. In the Cloud Shell, create a directory and then navigate to it.

   ```
   mkdir htmlapp 
   cd htmlapp
   ```

4. Run the following git command to clone the sample app repository to your *htmlapp* directory.

   ```
   git clone https://github.com/Azure-Samples/html-docs-hello-world.git
   ```

NOTE: Alternative using PowerShell or Azure Cli

```
$credential = Get-Credential
Connect-AzAccount -Credential $credential
```

```
az login
```



#### Task 2: Create the web app

1. Change to the directory that contains the sample code and run the az webapp up command. In the following example, replace <myAppName> with a unique app name, and <myLocation> with a region near you.

   ```
   cd html-docs-hello-world 
   az webapp up --location <myLocation> --name <myAppName> --html
   ```

   This command may take a few minutes to run. While running, it displays information similar to the example below. Make a note of the resourceGroup value. You need it for the *Clean up resources* section later.
   
   ```json
   { 
    "app_url": "https://<myAppName>.azurewebsites.net", 
    "location": "westeurope", 
    "name": "<app_name>", 
    "os": "Windows", 
    "resourcegroup": "<resource_group_name>", 
    "serverfarm": "appsvc_asp_Windows_westeurope", 
    "sku": "FREE", 
    "src_path": "/home/<username>/demoHTML/html-docs-hello-world ", 
       < JSON data removed for brevity. > 
   }
   ```
   
2. Open a browser and navigate to the app URL (http://<myAppName>.azurewebsites.net) and verify the app is running - take note of the title at the top of the page. Leave the browser open on the app for the next section.

#### Task 3: Update and redeploy the app

1. In the Cloud Shell, type code index.html to open the editor. In the <h1> heading tag, change *Azure App Service - Sample Static HTML Site* to *Azure App Service Updated* - or to anything else that you'd like.

2. Use the commands **ctrl-s** to save and **ctrl-q** to exit.

3. Redeploy the app with the same az webapp up command. Be sure to use the same values for <myLocation> and <myAppName> as you used earlier.

   ```
   az webapp up --location <myLocation> --name <myAppName> --html
   ```

   **Tip:** You can use the up arrow on your keyboard to scroll through previous commands.

4. Once deployment is completed switch back to the browser from step 2 in the “Create the web app” section above and refresh the page.

#### Task 4: Clean up resources

If you no longer need the resources you created in this exercise you can delete the resource group using the az group delete command below. Replace <resource_group> with resource group name you noted in step 1 of the “Create the web app” section above.

```
az group delete --name <resource_group> --no-wait
```