---
title: SQL BACPAC-bestanden met sjablonen importeren
description: Meer informatie over het gebruik van Azure SQL Database extensies om SQL BACPAC-bestanden te importeren met Azure Resource Manager-sjablonen.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 83108c056035b16d26343d82c721b275ebcad0c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80754323"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Zelf studie: SQL BACPAC-bestanden met ARM-sjablonen importeren

Meer informatie over het gebruik van Azure SQL Database-extensies voor het importeren van een BACPAC-bestand met Azure Resource Manager-sjablonen (ARM). Implementatie artefacten zijn alle bestanden, naast de hoofd sjabloon bestanden, die nodig zijn om een implementatie te volt ooien. Het BACPAC-bestand is een artefact.

In deze zelf studie maakt u een sjabloon voor het implementeren van een Azure SQL-Server en een SQL database en het importeren van een BACPAC-bestand. Zie [zelf studie: extensies voor virtuele machines implementeren met arm-sjablonen](./template-tutorial-deploy-vm-extensions.md)voor meer informatie over het implementeren van extensies van virtuele Azure-machines met behulp van arm-sjablonen.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Bereid een BACPAC-bestand voor.
> * Open een Quick Start-sjabloon.
> * Bewerk de sjabloon.
> * Implementeer de sjabloon.
> * Controleer de implementatie.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio code gebruiken om arm-sjablonen te maken](./use-vs-code-to-create-template.md).
* Gebruik een gegenereerd wacht woord voor het beheerders account van Azure SQL Server om de beveiliging te verbeteren. Hier volgt een voor beeld dat u kunt gebruiken om een wacht woord te genereren:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [zelf studie: Azure Key Vault integreren in arm-sjabloon implementatie](./template-tutorial-use-key-vault.md)voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

Een BACPAC-bestand wordt gedeeld in [github](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Zie [Een Azure SQL-database exporteren naar een BACPAC-bestand](../../sql-database/sql-database-export.md) als u een eigen account wilt maken. Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u de sjabloon later in de zelfstudie bijwerken.

Het BACPAC-bestand moet worden opgeslagen in een Azure Storage-account voordat het kan worden geïmporteerd met behulp van een ARM-sjabloon. Het volgende Power shell-script bereidt het BACPAC-bestand voor met de volgende stappen:

* Het BACPAC-bestand downloaden.
* Een Azure Storage-account maken.
* Maak een BLOB-container voor het opslag account.
* Het BACPAC-bestand uploaden naar de container.
* De sleutel van het opslag account en de URL van de BLOB weer geven.

1. Selecteer **proberen** om de Cloud shell te openen. Plak het volgende Power shell-script in het shell-venster.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Noteer de sleutel van het opslag account en de URL van het BACPAC-bestand. U hebt deze waarden nodig wanneer u de sjabloon implementeert.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

De sjabloon die in deze zelf studie wordt gebruikt, wordt opgeslagen in [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Selecteer **bestand** > **openen**in Visual Studio code.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.

    Er zijn twee resources gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
1. Selecteer **bestand** > **Opslaan als** om een kopie van het bestand op te slaan op de lokale computer met de naam *azuredeploy. json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

1. Voeg twee extra para meters aan het einde van de sectie **para meters** toe om de sleutel van het opslag account en de BACPAC-URL in te stellen.

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

    Voeg een komma toe na **adminPassword**. Als u het JSON-bestand wilt Format teren vanuit Visual Studio code, selecteert u SHIFT + ALT + F.

    Zie [een BACPAC-bestand voorbereiden](#prepare-a-bacpac-file)om deze twee waarden op te halen.

1. Voeg twee extra resources aan de sjabloon toe.

    * Als u de extensie SQL Database wilt toestaan om BACPAC-bestanden te importeren, moet u verkeer van Azure-Services toestaan. Voeg de volgende firewall regel definitie toe onder de SQL Server-definitie:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        De sjabloon ziet er als volgt uit:

        ![Sjabloon met definitie van firewall regel](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Voeg met de volgende JSON een SQL Database-extensieresource toe aan de databasedefinitie:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        De sjabloon ziet er als volgt uit:

        ![Sjabloon met SQL Database extensie](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Zie de [verwijzing voor SQL Database-extensies](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions) voor meer informatie over de resourcedefinitie. Hier volgen enkele belangrijke elementen:

        * **dependsOn**: de extensieresource moet worden gemaakt nadat de SQL-database is gemaakt.
        * **storageKeyType**: Geef het type van de opslag sleutel op die moet worden gebruikt. De waarde kan `StorageAccessKey` of `SharedAccessKey` zijn. Gebruik `StorageAccessKey` in deze zelf studie.
        * **storageKey**: Geef de sleutel op voor het opslag account waarin het BACPAC-bestand wordt opgeslagen. Als het type opslag sleutel is `SharedAccessKey`, moet het worden voorafgegaan door een '? '.
        * **storageUri**: Geef de URL op van het BACPAC-bestand dat is opgeslagen in een opslag account.
        * **administratorLoginPassword**: het wachtwoord van de SQL-beheerder. Gebruik een gegenereerd wachtwoord. Zie [vereisten](#prerequisites).

De voltooide sjabloon ziet er als volgt uit:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>De sjabloon implementeren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Raadpleeg de sectie [De sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) voor de implementatieprocedure. Gebruik in plaats daarvan het volgende PowerShell-implementatiescript:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

U kunt dezelfde project naam gebruiken die u hebt gebruikt bij het voorbereiden van het BACPAC-bestand, zodat alle resources worden opgeslagen in dezelfde resource groep. Op deze manier is het eenvoudiger om resource taken te beheren, zoals het opschonen van de resources. Als u dezelfde project naam gebruikt, kunt u de `New-AzResourceGroup` opdracht uit het script verwijderen of antwoord geven op Ja (y) of Nee (n) wanneer u wordt gevraagd of u de bestaande resource groep wilt bijwerken.

Gebruik een gegenereerd wachtwoord. Zie [vereisten](#prerequisites).

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u toegang wilt krijgen tot de SQL-Server vanaf uw client computer, moet u een extra firewall regel toevoegen. Zie [IP-firewall regels maken en beheren](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)voor meer informatie.

Selecteer in de Azure Portal de SQL database van de zojuist geïmplementeerde resource groep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. U ziet twee tabellen die zijn geïmporteerd in de-data base.

![Query-Editor (preview-versie)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in het Azure Portal **resource groep** in het menu links.
1. Voer de naam van de resourcegroep in het veld **Filter by name** in.
1. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resource groep.
1. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een SQL-Server en een SQL database geïmplementeerd en een BACPAC-bestand geïmporteerd. Zie voor meer informatie over het oplossen van problemen met sjabloon implementatie:

> [!div class="nextstepaction"]
> [Problemen met ARM-sjabloon implementaties oplossen](./template-tutorial-troubleshoot.md)
