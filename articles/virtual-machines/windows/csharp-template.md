---
title: Een VM implementeren met C# en een resourcemanagersjabloon
description: Meer informatie over het gebruik van C# en een Resource Manager-sjabloon om een Azure VM te implementeren.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: dfcc0c550af9df6c884c8cd864ed90daf5f78e2f
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082914"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>Een Azure Virtual Machine implementeren met C# en een resourcebeheersjabloon

In dit artikel ziet u hoe u een Azure Resource Manager-sjabloon implementeert met C#. De sjabloon die u maakt, implementeert één virtuele machine met Windows Server in een nieuw virtueel netwerk met één subnet.

Zie [Virtuele machines in een Azure Resource Manager-sjabloon](template-description.md)voor een gedetailleerde beschrijving van de bron van de virtuele machine. Zie [Azure Resource Manager-sjabloonwalkthrough voor](../../azure-resource-manager/resource-manager-template-walkthrough.md)meer informatie over alle bronnen in een sjabloon.

Het duurt ongeveer 10 minuten om deze stappen te doen.

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

In deze stap controleert u of Visual Studio is geïnstalleerd en maakt u een consoletoepassing die wordt gebruikt om de sjabloon te implementeren.

1. Als u dit nog niet hebt gedaan, installeert u [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Selecteer **.NET-bureaubladontwikkeling** op de pagina Workloads en klik op **Installeren**. In het overzicht u zien dat **.NET Framework 4 - 4.6 ontwikkeltools** automatisch voor u worden geselecteerd. Als u Visual Studio al hebt geïnstalleerd, u de .NET-werkbelasting toevoegen met behulp van de Visual Studio Launcher.
2. Klik in Visual**New** > Studio op Nieuw**project** **bestand** > .
3. Selecteer **in Sjablonen** > **Visual C#** de optie Console App **(.NET Framework),** voer *myDotnetProject* in voor de naam van het project, selecteer de locatie van het project en klik op **OK**.

## <a name="install-the-packages"></a>De pakketten installeren

NuGet-pakketten zijn de eenvoudigste manier om de bibliotheken te installeren die u nodig hebt om deze stappen te voltooien. Ga als volgt te werk om de bibliotheken te krijgen die u nodig hebt in Visual Studio:

1. Klik **op Extra** > **Nuget Package Manager**en klik vervolgens op Package Manager **Console**.
2. Typ deze opdrachten in de console:

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>De bestanden maken

In deze stap maakt u een sjabloonbestand dat de resources en een parametersbestand implementeert dat parameterwaarden aan de sjabloon levert. U maakt ook een autorisatiebestand dat wordt gebruikt om Azure Resource Manager-bewerkingen uit te voeren.

### <a name="create-the-template-file"></a>Het sjabloonbestand maken

1. Klik in Solution Explorer met de rechtermuisknop op *myDotnetProject* > Nieuw**item****toevoegen** > en selecteer **vervolgens Tekstbestand** in Visual *C# Items*. Geef het bestand *CreateVMTemplate.json*een naam en klik op **Toevoegen**.
2. Voeg deze JSON-code toe aan het bestand dat u hebt gemaakt:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. Sla het bestand CreateVMTemplate.json op.

### <a name="create-the-parameters-file"></a>Het parametersbestand maken

Als u waarden wilt opgeven voor de resourceparameters in de sjabloon, maakt u een parametersbestand dat de waarden bevat.

1. Klik in Solution Explorer met de rechtermuisknop op *myDotnetProject* > Nieuw**item****toevoegen** > en selecteer **vervolgens Tekstbestand** in Visual *C# Items*. Geef het bestand *Parameters.json*een naam en klik op **Toevoegen**.
2. Voeg deze JSON-code toe aan het bestand dat u hebt gemaakt:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Sla het bestand Parameters.json op.

### <a name="create-the-authorization-file"></a>Het autorisatiebestand maken

Voordat u een sjabloon implementeren, moet u ervoor zorgen dat u toegang hebt tot een [active directory-serviceprincipal.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) Vanuit de serviceprincipal krijgt u een token voor het verifiëren van aanvragen naar Azure Resource Manager. U moet ook de toepassings-id, de verificatiesleutel en de tenant-id die u nodig hebt, opnemen in het autorisatiebestand.

1. Klik in Solution Explorer met de rechtermuisknop op *myDotnetProject* > Nieuw**item****toevoegen** > en selecteer **vervolgens Tekstbestand** in Visual *C# Items*. Geef de eigenschappen van bestand *azureauth.en*klik op **Toevoegen**.
2. Voeg deze autorisatie-eigenschappen toe:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Vervang ** &lt;abonnements-id&gt; ** door uw abonnements-id, ** &lt;toepassings-id&gt; ** door de Active ** &lt;Directory-toepassingsid, verificatiesleutel&gt; ** door de toepassingssleutel en ** &lt;tenant-id&gt; ** door de tenant-id.

3. Sla het bestand azureauth.properties op.
4. Stel een omgevingsvariabele in Windows met de naam AZURE_AUTH_LOCATION met het volledige pad naar autorisatiebestand dat u hebt gemaakt, bijvoorbeeld u de volgende PowerShell-opdracht gebruiken:

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>De beheerclient maken

1. Open het Program.cs bestand voor het project dat u hebt gemaakt. Voeg deze vervolgens toe met behulp van instructies aan de bestaande instructies boven aan het bestand:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. Als u de beheerclient wilt maken, voegt u deze code toe aan de hoofdmethode:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Als u waarden voor de toepassing wilt opgeven, voegt u code toe aan de hoofdmethode:

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>Create a storage account

De sjabloon en parameters worden geïmplementeerd vanuit een opslagaccount in Azure. In deze stap maakt u het account aan en uploadt u de bestanden. 

Als u het account wilt maken, voegt u deze code toe aan de hoofdmethode:

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon en parameters van het opslagaccount dat is gemaakt. 

Als u de sjabloon wilt implementeren, voegt u deze code toe aan de hoofdmethode:

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>De bronnen verwijderen

Omdat er kosten in rekening worden gebracht voor resources die in Azure worden gebruikt, is het altijd een goede gewoonte om resources te verwijderen die niet langer nodig zijn. U hoeft niet elke resource afzonderlijk van een resourcegroep te verwijderen. Verwijder de brongroep en alle bronnen worden automatisch verwijderd. 

Als u de brongroep wilt verwijderen, voegt u deze code toe aan de hoofdmethode:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Het duurt ongeveer vijf minuten voordat deze consoletoepassing volledig van begin tot eind wordt uitgevoerd. 

1. Als u de consoletoepassing wilt uitvoeren, klikt u op **Start**.

2. Voordat u op **Enter** drukt om resources te verwijderen, u enkele minuten nodig hebben om de creatie van de resources in de Azure-portal te verifiëren. Klik op de implementatiestatus om informatie over de implementatie te bekijken.

## <a name="next-steps"></a>Volgende stappen

* Als er problemen waren met de implementatie, zou een volgende stap zijn om te kijken naar [het oplossen van veelvoorkomende Azure-implementatiefouten met Azure Resource Manager](../../resource-manager-common-deployment-errors.md).
* Meer informatie over het implementeren van een virtuele machine en de ondersteunende resources door [een Azure Virtual Machine met C# implementeren](csharp.md)te bekijken.