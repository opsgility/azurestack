# Deploy MySQL into Azure Stack with PowerShell

Instead of using the Azure Stack Portal, you can use PowerShell to deploy the DevOps tools through Azure Resource Manager templates, to the Azure Stack Development Kit. Azure Resource Manager templates deploy and provision all resources for your application in a single, coordinated operation.

## Run AzureRM PowerShell cmdlets
In this example, you run a script to deploy a virtual machine to Azure Stack Development Kit using a Resource Manager template.  Before proceeding, ensure you have [configured PowerShell](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-admin)  

1. Go to the [MySQL template folder](<AzureStack.MySQL/DeploymentTemplates>) and grab the mainTemplate.json, saving it to the following location: c:\\templates\\mySQLTemplate.json.
2. In PowerShell, run the following deployment script. Replace *username*, *password* and *mySQlPassword* with your username and password. On subsequent uses, increment the value for the *$myNum* parameter to prevent overwriting your deployment.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "mySQLRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy MySQL Template
       New-AzureRmResourceGroupDeployment `
           -Name mySQLDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\mySQLTemplate.json `
           -vmName mySQLVM$myNum `
           -vmSize "Standard_A3" `
           -storageAccountNewOrExisting new `
           -storageAccountName mysqlstor$myNum `
           -storageAccountType standard_lrs `
           -adminUsername <username> `
           -adminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -mySQLPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -allowRemoteConnections "No" `
           -authenticationType password `
           -virtualNetworkName mysql_vnet$myNum `
           -virtualNetworkAddressPrefix 10.0.0.0/16 `
           -virtualNetworkNewOrExisting new `
           -virtualNetworkSubnetName mysql_subnet$myNum `
           -virtualNetworkSubnetAddressPrefix 10.0.0.0/24 `
           -publicIPAddressName mysql_ip$myNum `
           -publicIPAddressDomainNameLabel mysql$myNum `
           -publicIPAddressNewOrExisting new `
           -scriptBaseUrl "https://raw.githubusercontent.com/mattmcspirit/azurestack/master/deployment/scripts/" `
           -templateBaseUrl "https://raw.githubusercontent.com/mattmcspirit/azurestack/master/deployment/packages/MySQL/AzureStack.MySQL/DeploymentTemplates/"
   ```
3. Open the Azure Stack portal, click **Browse**, click **Virtual machines**, and look for your new virtual machine (*mySQLDeployment001*).

Feel free to modify the above information to suit your needs!