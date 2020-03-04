---
title: Beveiligde artefacten in sjablonen
description: Meer informatie over het beveiligen van de gebruikte artefacten in uw Azure Resource Manager-sjablonen.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250115"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Zelf studie: veilige artefacten in Azure Resource Manager sjabloon implementaties

Meer informatie over het beveiligen van de artefacten die in uw Azure Resource Manager sjablonen worden gebruikt met behulp van een Azure Storage account met Shared Access signatures (SAS). Implementatieartefacten zijn, naast het hoofdsjabloonbestand, bestanden die vereist zijn om een implementatie te voltooien. In de [zelf studie: SQL BACPAC-bestanden met Azure Resource Manager sjablonen importeren](./template-tutorial-deploy-sql-extensions-bacpac.md), wordt met de hoofd sjabloon een Azure SQL database-exemplaar gemaakt. Er wordt ook een BACPAC-bestand aangeroepen om tabellen te maken en gegevens in te voegen. Het BACPAC-bestand is een artefact en wordt opgeslagen in een Azure Storage-account. Er is een opslag account sleutel gebruikt voor toegang tot het artefact. 

In deze zelfstudie gebruikt u SAS om beperkte toegang tot het BACPAC-bestand in uw eigen Azure Storage-account te verlenen. Zie [Using Shared Access Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Shared Access Signatures (SAS) gebruiken) voor meer informatie.

Zie [zelf studie: gekoppelde Azure Resource Manager sjablonen maken](./template-tutorial-create-linked-templates.md)voor meer informatie over het beveiligen van een gekoppelde sjabloon.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Bereid een BACPAC-bestand voor.
> * Open een bestaande sjabloon.
> * Bewerk de sjabloon.
> * Implementeer de sjabloon.
> * De implementatie controleren.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](./use-vs-code-to-create-template.md).
* Lees [zelf studie: SQL BACPAC-bestanden met Azure Resource Manager sjablonen importeren](./template-tutorial-deploy-sql-extensions-bacpac.md). De sjabloon die in deze zelfstudie wordt gebruikt, is ontwikkeld in die zelfstudie. Dit artikel bevat een koppeling waarmee u de voltooide sjabloon kunt downloaden.
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van SQL Server om de beveiliging te verbeteren. Hier volgt een voor beeld dat u kunt gebruiken om een wacht woord te genereren:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./template-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

In deze sectie bereidt u het BACPAC-bestand voor, zodat het bestand veilig toegankelijk is wanneer u de Resource Manager-sjabloon implementeert. Deze sectie bevat vijf procedures:

* Het BACPAC-bestand downloaden.
* Een Azure Storage-account maken.
* Maak een BLOB-container voor het opslag account.
* Het BACPAC-bestand uploaden naar de container.
* Het SAS-token van het BACPAC-bestand ophalen.

1. Selecteer **proberen** om de Cloud shell te openen. Plak het volgende Power shell-script in het shell-venster.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName `
        -Location $location `
        -SkuName "Standard_LRS"

    $context = $storageAccount.Context

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $context

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Noteer de URL van het BACPAC-bestand en het SAS-token. U hebt deze waarden nodig wanneer u de sjabloon implementeert.

## <a name="open-an-existing-template"></a>Een bestaande sjabloon openen

In deze sessie wijzigt u de sjabloon die u hebt gemaakt in de [zelf studie: IMPORTEER SQL BACPAC-bestanden met Azure Resource Manager sjablonen](./template-tutorial-deploy-sql-extensions-bacpac.md) om het BACPAC-bestand aan te roepen met een SAS-token. De sjabloon die is ontwikkeld in de zelf studie over de SQL-uitbrei ding wordt gedeeld in [github](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Selecteer in Visual Studio Code **Bestand** > **Bestand openen**.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Selecteer **Openen** om het bestand te openen.

    Er zijn vier resources gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
1. Selecteer **Bestand** > **Opslaan als** om het bestand op uw lokale computer op te slaan als *azuredeploy.json*.

## <a name="edit-the-template"></a>De sjabloon bewerken

1. Vervang de definitie van de storageAccountKey-para meter door de volgende parameter definitie: 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Parameters voor beveiligde artefacten in Resource Manager-zelfstudie](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Werk de waarde van de volgende drie elementen van de SQL-extensie bron bij:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

De voltooide sjabloon ziet er als volgt uit:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>De sjabloon implementeren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Zie de sectie [de sjabloon implementeren](./template-tutorial-create-multiple-instances.md#deploy-the-template) voor de implementatie procedure. Gebruik in plaats daarvan het volgende Power shell-implementatie script.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).
Zie [een BACPAC-bestand voorbereiden](#prepare-a-bacpac-file)voor de waarden van _artifactsLocation, _ArtifactsLocationSasToken en bacpacFileName.

## <a name="verify-the-deployment"></a>De implementatie controleren

Selecteer in de portal de SQL-database in de geïmplementeerde resourcegroep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. U ziet twee tabellen die zijn geïmporteerd in de-data base.

![Query-Editor (preview-versie)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in het Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resource groep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een SQL-Server en een SQL database geïmplementeerd en een BACPAC-bestand met behulp van een SAS-token geïmporteerd. Zie voor meer informatie over het maken van een Azure-pijp lijn om voortdurend Resource Manager-sjablonen te ontwikkelen en te implementeren:

> [!div class="nextstepaction"]
> [Continue integratie met Azure-pijp lijn](./template-tutorial-use-azure-pipelines.md)
