---
title: Secure artifacts in templates
description: Meer informatie over het beveiligen van de gebruikte artefacten in uw Azure Resource Manager-sjablonen.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326428"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Secure artifacts in Azure Resource Manager template deployments

Meer informatie over het beveiligen van de gebruikte artefacten in uw Azure Resource Manager-sjablonen met behulp van een Azure Storage-account met Shared Access Signatures (SAS). Implementatieartefacten zijn, naast het hoofdsjabloonbestand, bestanden die vereist zijn om een implementatie te voltooien. For example, in [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), the main template creates an Azure SQL Database; it also calls a BACPAC file to create tables and insert data. Het BACPAC-bestand is een artefact. Het artefact wordt opgeslagen in een Azure Storage-account met openbare toegang. In deze zelfstudie gebruikt u SAS om beperkte toegang tot het BACPAC-bestand in uw eigen Azure Storage-account te verlenen. Zie [Using Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (Shared Access Signatures (SAS) gebruiken) voor meer informatie.

To learn how to secure linked template, see [Tutorial: Create linked Azure Resource Manager templates](./resource-manager-tutorial-create-linked-templates.md).

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een BACPAC-bestand voorbereiden
> * Een bestaande sjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren
> * De implementatie controleren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Review [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). De sjabloon die in deze zelfstudie wordt gebruikt, is ontwikkeld in die zelfstudie. Dit artikel bevat een koppeling waarmee u de voltooide sjabloon kunt downloaden.
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van SQL Server om de beveiliging te verbeteren. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="prepare-a-bacpac-file"></a>Een BACPAC-bestand voorbereiden

In dit gedeelte bereidt u het BACPAC-bestand voor zodat het bestand veilig toegankelijk is wanneer u de Resource Manager-sjabloon implementeert. Deze sectie bevat vijf procedures:

* Het BACPAC-bestand downloaden.
* Een Azure Storage-account maken.
* Een blobcontainer in een Storage-account maken.
* Het BACPAC-bestand uploaden naar de container.
* Het SAS-token van het BACPAC-bestand ophalen.

Als u deze stappen wilt automatiseren met behulp van een PowerShell-script, raadpleegt u het script van [De gekoppelde sjabloon uploaden](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Het BACPAC-bestand downloaden

Download het [BACPAC-bestand](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) en sla het bestand op uw lokale computer op onder dezelfde naam, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Maak een opslagaccount

1. Selecteer de volgende afbeelding om een Resource Manager-sjabloon in Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Voer de volgende eigenschappen in:

    * **Abonnement**: selecteer uw Azure-abonnement.
    * **Resource Group**: Select **Create new** and give it a name. Een resourcegroep is een container voor Azure-resources voor beheerdoeleinden. In deze zelfstudie, kunt u dezelfde resourcegroep voor het opslagaccount en de Azure SQL Database gebruiken. Noteer de naam van deze resourcegroep. U hebt deze nodig wanneer u de Azure SQL Database later in de zelfstudies maakt.
    * **Location**: Select a region. Bijvoorbeeld **US - centraal**.
    * **Type opslagaccount**: Gebruik de standaardwaarde, **Standard_LRS**.
    * **Location**: Use the default value, which is **[resourceGroup().location]** . Dit betekent dat u de locatie van de resourcegroep gebruikt voor het opslagaccount.
    * **Ik ga akkoord met de bovenstaande voorwaarden**: (geselecteerd)
3. Selecteer **Aankoop**.
4. Selecteer het meldingspictogram (het belpictogram) in de rechterbovenhoek van de portal om de implementatiestatus te bekijken.

    ![Het deelvenster Meldingen in de portal voor de Resource Manager-zelfstudie](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Als het opslagaccount met succes is geïmplementeerd, selecteert u **Ga naar resourcegroep** in het deelvenster Meldingen om de resourcegroep te openen.

### <a name="create-a-blob-container"></a>Een blobcontainer maken

Een blobcontainer is vereist voordat u bestanden kunt uploaden.

1. Selecteer de opslag om deze te openen. U ziet slechts één opslagaccount in de resourcegroep. De naam van uw opslagaccount wijkt af van die van het opslagaccount in de volgende schermafbeelding.

    ![Opslagaccount in Resource Manager-zelfstudie](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Selecteer de tegel **Blobs**.

    ![Blobs in Resource Manager-zelfstudie](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Selecteer **+ Container** bovenaan om een nieuwe container te maken.
4. Voer de volgende waarden in:

    * **Naam**: voer **sqlbacpac** in.
    * **Openbaar toegangsniveau**: gebruik de standaardwaarde, **Privé (geen anonieme toegang)** .
5. Selecteer **OK**.
6. Selecteer **sqlbacpac** om de zojuist gemaakte container te openen.

### <a name="upload-the-bacpac-file-to-the-container"></a>Het BACPAC-bestand uploaden naar de container

1. Selecteer **Uploaden**.
2. Voer de volgende waarden in:

    * **Files**: Following the instructions to select the BACPAC file you downloaded earlier. De standaardnaam is **SQLDatabaseExtension.bacpac**.
    * **Authentication type**: Select **SAS**.  *SAS* is de standaardwaarde.
3. Selecteer **Uploaden**.  Zodra het bestand is geüpload, wordt de bestandsnaam vermeld in de container.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Een SAS-token genereren

1. Klik met de rechtermuisknop op **SQLDatabaseExtension.bacpac** in de container en selecteer vervolgens **SAS genereren**.
2. Voer de volgende waarden in:

    * **Permission**: Use the default, **Read**.
    * **Start and expiry date/time**: The default value gives you eight hours to use the SAS token. Als u meer tijd nodig hebt om deze zelfstudie te voltooien, verhoogt u de **verlooptijd**.
    * **Allowed IP addresses**: Leave this field blank.
    * **Allowed protocols**: use the default value: **HTTPS**.
    * **Signing key**: use the default value: **Key 1**.
3. Selecteer **Blob-SAS-token en -URL genereren**.
4. Kopieer de **Blob-SAS-URL**. In het midden van de URL staat de bestandsnaam **SQLDatabaseExtension.bacpac**.  De bestandsnaam verdeelt de URL in drie delen:

   - **Locatie van het artefact**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Zorg ervoor dat de locatie eindigt op een '/'.
   - **BACPAC file name**: SQLDatabaseExtension.bacpac.
   - **Artifact location SAS token**: Make sure the token precedes with a "?."

     U hebt deze drie waarden nodig voor het [implementeren van de sjabloon](#deploy-the-template).

## <a name="open-an-existing-template"></a>Een bestaande sjabloon openen

In this session, you modify the template you created in [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) to call the BACPAC file with a SAS token.  The template developed in the SQL extension tutorial is shared in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.

    Er zijn vijf resources gedefinieerd in de sjabloon:

   * `Microsoft.Sql/servers`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg de volgende aanvullende parameters toe:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Parameters voor beveiligde artefacten in Resource Manager-zelfstudie](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Werk de waarde van de volgende twee elementen bij:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Raadpleeg de sectie [De sjabloon implementeren](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) voor de implementatieprocedure. Gebruik in plaats daarvan het volgende PowerShell-implementatiescript:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Gebruik een gegenereerd wachtwoord. Zie [Vereisten](#prerequisites).
Zie [Een SAS-token genereren](#generate-a-sas-token) voor de waarden van _artifactsLocationSasToken en bacpacFileName.

## <a name="verify-the-deployment"></a>De implementatie controleren

Selecteer in de portal de SQL-database in de geïmplementeerde resourcegroep. Selecteer **Query-editor (preview)** en voer vervolgens de beheerdersreferenties in. U ziet dat twee tabellen in de database worden geïmporteerd:

![Azure Resource Manager sql-extensies importeren BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een SQL Server en een SQL Database geïmplementeerd en een BACPAC-bestand geïmporteerd met behulp van een SAS-token. To learn how to create an Azure Pipeline to continuously develop and deploy Resource Manager templates, see

> [!div class="nextstepaction"]
> [Continuous integration with Azure Pipeline](./resource-manager-tutorial-use-azure-pipelines.md)
