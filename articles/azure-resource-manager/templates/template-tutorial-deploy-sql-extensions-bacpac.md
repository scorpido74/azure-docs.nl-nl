---
title: SQL BACPAC-bestanden importeren met sjablonen
description: Meer informatie over het gebruik van Azure SQL Database-extensies voor het importeren van SQL BACPAC-bestanden met Azure Resource Manager-sjablonen.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 83108c056035b16d26343d82c721b275ebcad0c5
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754323"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Zelfstudie: SQL BACPAC-bestanden importeren met ARM-sjablonen

Meer informatie over het gebruik van Azure SQL Database-extensies om een BACPAC-bestand te importeren met ARM-sjablonen (Azure Resource Manager). Implementatieartefacten zijn alle bestanden, naast de hoofdsjabloonbestanden, die nodig zijn om een implementatie te voltooien. Het BACPAC-bestand is een artefact.

In deze zelfstudie maakt u een sjabloon om een Azure SQL-server en een SQL-database te implementeren en een BACPAC-bestand te importeren. Zie [Zelfstudie: Virtuele machine-extensies implementeren met ARM-sjablonen](./template-tutorial-deploy-vm-extensions.md)voor informatie over het implementeren van Azure-extensies voor virtuele machine met BEHULP VAN ARM-sjablonen.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Maak een BACPAC-bestand voor.
> * Open een snelstartsjabloon.
> * Bewerk de sjabloon.
> * Implementeer de sjabloon.
> * Controleer de implementatie.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](./use-vs-code-to-create-template.md)
* Als u de beveiliging wilt verhogen, gebruikt u een gegenereerd wachtwoord voor het Azure SQL Server-beheerdersaccount. Hier is een voorbeeld dat u gebruiken om een wachtwoord te genereren:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

Een BACPAC-bestand wordt gedeeld in [GitHub.](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) Zie [Een Azure SQL-database exporteren naar een BACPAC-bestand](../../sql-database/sql-database-export.md) als u een eigen account wilt maken. Als u ervoor kiest het bestand naar uw eigen locatie te publiceren, moet u de sjabloon later in de zelfstudie bijwerken.

Het BACPAC-bestand moet worden opgeslagen in een Azure Storage-account voordat het kan worden geïmporteerd met behulp van een ARM-sjabloon. Met het volgende PowerShell-script wordt het BACPAC-bestand voorbereid met de volgende stappen:

* Het BACPAC-bestand downloaden.
* Een Azure Storage-account maken.
* Maak een blobcontainer voor opslagaccount.
* Het BACPAC-bestand uploaden naar de container.
* Geef de opslagaccountsleutel en de blob-URL weer.

1. Selecteer **Probeer deze** om de cloudshell te openen. Plak vervolgens het volgende PowerShell-script in het shell-venster.

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

1. Noteer de opslagaccountsleutel en de URL van het BACPAC-bestand. U hebt deze waarden nodig wanneer u de sjabloon implementeert.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

De sjabloon die in deze zelfstudie wordt gebruikt, wordt opgeslagen in [GitHub.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)

1. Selecteer **Bestand** > **openen bestand**in Visual Studio-code .
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.

    Er zijn twee bronnen gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
1. Selecteer **Bestand** > **opslaan als** u een kopie van het bestand op uw lokale computer wilt opslaan met de naam *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

1. Voeg nog twee parameters toe aan het einde van de **parameterssectie** om de opslagaccountsleutel en de BACPAC-URL in te stellen.

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

    Voeg een komma toe na **adminPassword**. Als u het JSON-bestand wilt opmaken vanuit Visual Studio Code, selecteert u Shift+Alt+F.

    Zie [Een BACPAC-bestand voorbereiden](#prepare-a-bacpac-file)om deze twee waarden te krijgen.

1. Voeg twee extra resources aan de sjabloon toe.

    * Als u wilt toestaan dat de SQL Database-extensie BACPAC-bestanden importeert, moet u verkeer uit Azure-services toestaan. Voeg de volgende firewallregeldefinitie toe onder de SQL-serverdefinitie:

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

        De sjabloon ziet eruit als volgt:

        ![Sjabloon met firewallregeldefinitie](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

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

        De sjabloon ziet eruit als volgt:

        ![Sjabloon met SQL-database-extensie](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        Zie de [verwijzing voor SQL Database-extensies](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions) voor meer informatie over de resourcedefinitie. Hier volgen enkele belangrijke elementen:

        * **dependsOn**: de extensieresource moet worden gemaakt nadat de SQL-database is gemaakt.
        * **storageKeyType:** geef het type opslagsleutel op dat moet worden gebruikt. De waarde kan `StorageAccessKey` of `SharedAccessKey` zijn. Gebruik `StorageAccessKey` in deze zelfstudie.
        * **storageKey:** Geef de sleutel op voor het opslagaccount waar het BACPAC-bestand is opgeslagen. Als het type `SharedAccessKey`opslagsleutel is, moet deze worden voorafgegaan met een "?".
        * **storageUri:** Geef de URL op van het BACPAC-bestand dat is opgeslagen in een opslagaccount.
        * **administratorLoginPassword**: het wachtwoord van de SQL-beheerder. Gebruik een gegenereerd wachtwoord. Zie [Voorwaarden](#prerequisites).

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

Overweeg dezelfde projectnaam te gebruiken die u hebt gebruikt toen u het BACPAC-bestand voorbereidde, zodat alle bronnen binnen dezelfde brongroep worden opgeslagen. Op deze manier is het eenvoudiger om resourcetaken te beheren, zoals het opschonen van de resources. Als u dezelfde projectnaam gebruikt, kunt `New-AzResourceGroup` u de opdracht uit het script verwijderen of ja (y) of nee (n) antwoorden wanneer u wordt gevraagd of u de bestaande brongroep wilt bijwerken.

Gebruik een gegenereerd wachtwoord. Zie [Voorwaarden](#prerequisites).

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u toegang wilt krijgen tot de SQL-server vanaf uw clientcomputer, moet u een extra firewallregel toevoegen. Zie [IP-firewallregels maken en beheren](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)voor meer informatie.

Selecteer in de Azure-portal de SQL-database uit de nieuw geïmplementeerde brongroep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. Er worden twee tabellen geïmporteerd in de database.

![Queryeditor (voorbeeld)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de Azure-portal in het linkermenu.
1. Voer de naam van de resourcegroep in het veld **Filter by name** in.
1. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resourcegroep.
1. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een SQL-server en een SQL-database geïmplementeerd en een BACPAC-bestand geïmporteerd. Zie voor meer informatie over het oplossen van problemen met de implementatie van sjablonen:

> [!div class="nextstepaction"]
> [Problemen met ARM-sjabloonimplementaties oplossen](./template-tutorial-troubleshoot.md)
