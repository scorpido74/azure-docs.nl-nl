---
title: Gekoppelde sjablonen maken
description: Informatie over het maken van gekoppelde Azure Resource Manager-sjablonen voor het maken van een virtuele machine
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1cce566fb7aab286c57f32d9348e51dd0a7c1ee
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239324"
---
# <a name="tutorial-create-linked-arm-templates"></a>Zelfstudie: Gekoppelde ARM-sjablonen maken

Meer informatie over het maken van gekoppelde ARM-sjablonen (Azure Resource Manager). Met gekoppelde sjablonen kunt u een sjabloon een andere sjabloon laten aanroepen. Dit is handig om sjablonen te modulariseren. In deze zelfstudie gebruikt u dezelfde sjabloon die wordt gebruikt in [Zelfstudie: ARM-sjablonen maken met afhankelijke resources,](./template-tutorial-create-templates-with-dependent-resources.md)waardoor een virtuele machine, een virtueel netwerk en andere afhankelijke bron, waaronder een opslagaccount, worden gemaakt. U verplaatst de gemaakte resource van het opslagaccount naar een gekoppelde sjabloon.

Het aanroepen van een gekoppelde sjabloon is als het maken van een functieaanroep.  U leert ook hoe u parameterwaarden doorgeeft aan de gekoppelde sjabloon en hoe u 'retourwaarden' uit de gekoppelde sjabloon halen.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De gekoppelde sjabloon maken
> * De gekoppelde sjabloon uploaden
> * Koppeling maken met de gekoppelde sjabloon
> * Afhankelijkheid configureren
> * De sjabloon implementeren
> * Aanvullende procedures

Zie [Gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources](./linked-templates.md)voor meer informatie.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](use-vs-code-to-create-template.md)
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure QuickStart-sjablonen is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren). Dit is dezelfde sjabloon die wordt gebruikt in [Zelfstudie: ARM-sjablonen maken met afhankelijke resources.](./template-tutorial-create-templates-with-dependent-resources.md) U slaat twee kopieën op van dezelfde sjabloon. Deze worden gebruikt als:

* **De hoofdsjabloon**: voor het maken van alle resources, met uitzondering van het opslagaccount.
* **De gekoppelde sjabloon**: voor het maken van het opslagaccount.

1. Selecteer **Bestand**>**openen bestand**in Visual Studio-code .
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. De sjabloon heeft zes bronnen gedefinieerd:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Het is handig om een basiskennis van het sjabloonschema te krijgen voordat u de sjabloon aanwerkt.
1. Selecteer **Bestand**>**opslaan als** u een kopie van het bestand op uw lokale computer wilt opslaan met de naam **azuredeploy.json**.
1. Selecteer **Bestand**>**opslaan als** u een andere kopie van het bestand maakt met de naam **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>De gekoppelde sjabloon maken

De gekoppelde sjabloon maakt een opslagaccount. De gekoppelde sjabloon kan worden gebruikt als zelfstandige sjabloon om een opslagaccount te maken. In deze zelfstudie neemt de gekoppelde sjabloon twee parameters en geeft een waarde door aan de hoofdsjabloon. Deze "return"-waarde wordt `outputs` gedefinieerd in het element.

1. Open **linkedTemplate.json** in Visual Studio Code als het bestand niet wordt geopend.
1. Breng de volgende wijzigingen aan:

    * Verwijder alle andere parameters dan **locatie**.
    * Voeg een parameter met de naam **storageAccountName** toe.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      De naam en locatie van het opslagaccount worden van de hoofdsjabloon naar de gekoppelde sjabloon als parameters doorgegeven.

    * Het element **variables** en alle variabeledefinities te verwijderen.
    * Verwijder alle andere bronnen dan het opslagaccount. U moet in totaal vier resources verwijderen.
    * Werk de waarde van het element **name** van de opslagaccount-resource om:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Werk het element **outputs** zo bij dat het er als volgt uitziet:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       **storageUri** is vereist voor de resourcedefinitie van de virtuele machine in de hoofdsjabloon.  U kunt de waarde als een uitvoerwaarde teruggeven aan de hoofdsjabloon.

        Wanneer u klaar bent, ziet de sjabloon er als volgt uit:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. Sla de wijzigingen op.

## <a name="upload-the-linked-template"></a>De gekoppelde sjabloon uploaden

De hoofdsjabloon en de gekoppelde sjabloon moeten toegankelijk zijn vanaf de locatie waar u de implementatie uitvoert. In deze zelfstudie gebruikt u de implementatiemethode voor de cloudshell zoals u die gebruikt in [Zelfstudie: ARM-sjablonen maken met afhankelijke resources.](./template-tutorial-create-templates-with-dependent-resources.md) De hoofdsjabloon (azuredeploy.json)-sjabloon is geüpload naar de shell. De gekoppelde sjabloon (linkedTemplate.json) moet ergens veilig worden gedeeld. Met het volgende PowerShell-script wordt een Azure Storage-account gemaakt, de sjabloon geüpload naar het Storage-account, en vervolgens een SAS-token voor beperkte toegang tot het sjabloonbestand gegenereerd. Om de zelfstudie te vereenvoudigen, downloadt het script een voltooide gekoppelde sjabloon uit een GitHub-repository. Als u de gekoppelde sjabloon die u hebt gemaakt, wilt gebruiken, kunt u de gekoppelde sjabloon uploaden met [Cloud Shell](https://shell.azure.com) en vervolgens het script zo aanpassen dat uw eigen gekoppelde sjabloon wordt gebruikt.

> [!NOTE]
> De geldigheidsduur van het SAS-token wordt met het script beperkt tot acht uur. Als u meer tijd nodig hebt om deze zelfstudie te voltooien, verhoogt u de verlooptijd.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Selecteer de groene knop **Uitproberen** om het Azure Cloud Shell-venster te openen.
2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechtermuisknop op een willekeurige plaats in het shellvenster (het marineblauwe deel) en kies vervolgens **Plakken**.
4. Noteer de twee waarden (naam van de resourcegroep en URI van de gekoppelde sjabloon URI) aan het einde van het shellvenster. U hebt deze waarden later in de zelfstudie nodig.
5. Selecteer **Focusmodus sluiten** om het shellvenster te sluiten.

In de praktijk genereert u een SAS-token wanneer u de hoofdsjabloon implementeert en geeft u het SAS-token een kortere verlooptijd om het beter te beveiligen. Zie [SAS-token opgeven tijdens implementatie](./secure-template-with-sas-token.md#provide-sas-token-during-deployment) voor meer informatie.

## <a name="call-the-linked-template"></a>De gekoppelde sjabloon aanroepen

De hoofdsjabloon heet azuredeploy.json.

1. Open **azuredeploy.json** in Visual Studio Code als deze niet wordt geopend.
1. Vervang de definitie van opslagaccountbronnen door het volgende json-fragment:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Let op de volgende gegevens:

    * Een `Microsoft.Resources/deployments`-resource in de hoofdsjabloon wordt gebruikt om de sjabloon te koppelen aan een andere sjabloon.
    * De `deployments`-resource heet `linkedTemplate`. Deze naam wordt gebruikt voor [het configureren van afhankelijkheden](#configure-dependency).
    * U kunt alleen de [incrementele](./deployment-modes.md) implementatiemodus gebruiken bij het aanroepen van gekoppelde sjablonen.
    * `templateLink/uri` bevat de URI van de gekoppelde sjabloon. Wijzig de waarde in de URI die u hebt verkregen tijdens het uploaden van de gekoppelde sjabloon (die met een SAS-token).
    * Gebruik `parameters` om waarden door te geven van de hoofdsjabloon naar de gekoppelde sjabloon.
1. Zorg ervoor dat u de waarde van het element `uri` hebt gewijzigd in de waarde die u hebt verkregen tijdens het uploaden van de gekoppelde sjabloon (die met een SAS-token). In de praktijk wilt u de URI opgeven met een parameter.
1. De bijgewerkte sjabloon opslaan

## <a name="configure-dependency"></a>Afhankelijkheid configureren

Terugroepen uit [zelfstudie: maak ARM-sjablonen met afhankelijke resources,](./template-tutorial-create-templates-with-dependent-resources.md)de bron van de virtuele machine is afhankelijk van het opslagaccount:

![Afhankelijkheidsdiagram van Azure Resource Manager-sjablonen](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Omdat het opslagaccount nu is gedefinieerd in de gekoppelde sjabloon, moet u de volgende twee elementen van de `Microsoft.Compute/virtualMachines`-resource bijwerken.

* Configureer het `dependsOn`-element opnieuw. De definitie van het opslagaccount is verplaatst naar de gekoppelde sjabloon.
* Configureer het `properties/diagnosticsProfile/bootDiagnostics/storageUri`-element opnieuw. In [De gekoppelde sjabloon maken](#create-the-linked-template) hebt u een uitvoerwaarde toegevoegd:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    Deze waarde heeft de hoofdsjabloon nodig.

1. Open azuredeploy.json in Visual Studio Code als het bestand nog niet is geopend.
2. Vouw de definitie van de VM-resource uit en werk **dependsOn** bij zoals wordt weergegeven in de volgende schermafbeelding:

    ![Gekoppelde Azure Resource Manager-sjablonen configureren afhankelijkheid](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* is de naam van de implementatiebron.
3. Werk **properties/diagnosticsProfile/bootDiagnostics/storageUri** bij zoals is weergegeven in de vorige schermafbeelding.
4. Sla de bijgewerkte sjabloon op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) voor de implementatieprocedure. Gebruik dezelfde resourcegroepsnaam als het opslagaccount voor het opslaan van de gekoppelde sjabloon. Zo wordt het opschonen van resources in de volgende sectie eenvoudiger. Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Zie [Voorwaarden](#prerequisites).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="additional-practice"></a>Aanvullende oefening

U kunt het project verder verbeteren door de volgende aanvullende wijzigingen aan te brengen in het voltooide project:

1. Wijzig de hoofdsjabloon (azuredeploy.json) zo, dat de waarde voor de URI van de gekoppelde sjabloon wordt verkregen via een parameter.
2. Genereer het SAS-token bij het uploaden van de hoofdsjabloon in plaats van bij het uploaden van de gekoppelde sjabloon. Zie [SAS-token opgeven tijdens implementatie](./secure-template-with-sas-token.md#provide-sas-token-during-deployment) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gesplitst in een hoofdsjabloon en een gekoppelde sjabloon. Voor meer informatie over het gebruik van extensies van virtuele machines voor de uitvoering van post-implementatietaken raadpleegt u:

> [!div class="nextstepaction"]
> [Virtuele machine-extensies implementeren](./template-tutorial-deploy-vm-extensions.md)
