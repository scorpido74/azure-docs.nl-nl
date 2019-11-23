---
title: Import SQL BACPAC files with templates
description: Meer informatie over het gebruik van SQL Database-extensie om SQL BACPAC-bestanden te importeren met Azure Resource Manager-sjablonen.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 741521551335712400e5f61822d7dda31199d3df
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422168"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Zelfstudie: SQL BACPAC-bestanden importeren met Azure Resource Manager-sjablonen

Meer informatie over het gebruik van Azure SQL Database-extensies om een BACPAC-bestand te importeren met Azure Resource Manager-sjablonen. Deployment artifacts are any files, in addition to the main template files that are needed to complete a deployment. Het BACPAC-bestand is een artefact. In deze zelfstudie maakt u een sjabloon voor het implementeren van een Azure SQL-server, een SQL-database, en importeert u een BACPAC-bestand. Zie [# Zelfstudie: Extensies voor virtuele machines implementeren met Azure Resource Manager-sjablonen](./resource-manager-tutorial-deploy-vm-extensions.md) voor informatie over het implementeren van extensies van virtuele Azure-machines met behulp van Azure Resource Manager-sjablonen.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een BACPAC-bestand voorbereiden
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * De implementatie controleren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van SQL Server om de beveiliging te verbeteren. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

A BACPAC file is shared in [GitHub](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Zie [Een Azure SQL-database exporteren naar een BACPAC-bestand](../sql-database/sql-database-export.md) als u een eigen account wilt maken. Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u de sjabloon later in de zelfstudie bijwerken.

The BACPAC file must be stored in an Azure Storage account before it can be imported using Resource Manager template.

1. Open the [Cloud shell](https://shell.azure.com).
1. Select **Upload/Download files**, and then select **Upload**.
1. Specify the following URL and then select **Open**.

    ```url
    https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac
    ```

1. Copy and paste the following PowerShell script into the shell window.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFile = "$HOME/SQLDatabaseExtension.bacpac"
    $blobName = "SQLDatabaseExtension.bacpac"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    Set-AzStorageBlobContent -File $bacpacFile `
                             -Container $containerName `
                             -Blob $blobName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$blobName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Write down storage account key and the BACPAC file URL. You need these values when you deploy the template.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

The template used in this tutorial is stored in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.

    Er worden drie resources gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

1. Add two more parameters at the end of the **parameters** section to set the storage account key and the BACPAC URL:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Add a comma after **adminPassword**. To format the JSON file from VS Code, press **[SHIFT]+[ALT]+F**.

    See [Prepare a BACPAC file](#prepare-a-bacpac-file) about getting these two values.

1. Voeg twee extra resources aan de sjabloon toe.

    * To allow the SQL database extension to import BACPAC files, you need to allow traffic from Azure services. Add the following firewall rule definition under the SQL server definition:

        ```json
        {
          "type": "firewallrules",
          "apiVersion": "2015-05-01-preview",
          "name": "AllowAllAzureIps",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
          }
        }
        ```

        De sjabloon zal er als volgt uitzien:

        ![Azure Resource Manager sql-extensies importeren BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Voeg met de volgende JSON een SQL Database-extensieresource toe aan de databasedefinitie:

        ```json
        "resources": [
            {
              "type": "extensions",
              "apiVersion": "2014-04-01",
              "name": "Import",
              "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
              ],
              "properties": {
                "storageKeyType": "StorageAccessKey",
                "storageKey": "[parameters('storageAccountKey')]",
                "storageUri": "[parameters('bacpacUrl')]",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import"
              }
            }
        ]
        ```

        De sjabloon zal er als volgt uitzien:

        ![Azure Resource Manager sql-extensies importeren BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Zie de [verwijzing voor SQL Database-extensies](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions) voor meer informatie over de resourcedefinitie. Hier volgen enkele belangrijke elementen:

        * **dependsOn**: de extensieresource moet worden gemaakt nadat de SQL-database is gemaakt.
        * **storageKeyType**: Specify the type of the storage key to use. De waarde kan `StorageAccessKey` of `SharedAccessKey` zijn. Use `StorageAccessKey` in this tutorial.
        * **storageKey**: Specify the key for the storage account where the BACPAC file is stored. If storage key type is SharedAccessKey, it must be preceded with a "?"
        * **storageUri**: Specify the URL of the BACPAC file stored in a storage account.
        * **administratorLoginPassword**: het wachtwoord van de SQL-beheerder. Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).

## <a name="deploy-the-template"></a>De sjabloon implementeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Raadpleeg de sectie [De sjabloon implementeren](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) voor de implementatieprocedure. Gebruik in plaats daarvan het volgende PowerShell-implementatiescript:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Consider using the same project name as you used when you prepared the bacpac file, so that all the resources are stored within the same resource group.  It is easier for managing resource, such as cleaning up the resources. If you use the same project name, you can either remove the **New-AzResourceGroup** command from the script, or answer y or n when you are asked whether you want to update the existing resource group.

Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).

## <a name="verify-the-deployment"></a>De implementatie controleren

To access the SQL server from your client computer, you need to add an additional firewall rule. For more information, see [Create and manage IP firewall rules](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules).

Selecteer in de portal de SQL-database in de geïmplementeerde resourcegroep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. U ziet dat twee tabellen in de database worden geïmporteerd:

![Azure Resource Manager sql-extensies importeren BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een SQL Server en een SQL Database geïmplementeerd en een BACPAC-bestand geïmporteerd. Het BACPAC-bestand wordt opgeslagen in een Azure Storage-account. Iedereen die de URL heeft, kan het bestand openen. Als u wilt weten hoe u het BACPAC-bestand (artefact) wilt beveiligen, raadpleegt u

> [!div class="nextstepaction"]
> [Secure the artifacts](./resource-manager-tutorial-secure-artifacts.md) (Artefacten beveiligen)
