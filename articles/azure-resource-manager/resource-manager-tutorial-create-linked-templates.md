---
title: Gekoppelde Azure Resource Manager-sjablonen maken | Microsoft Docs
description: Informatie over het maken van gekoppelde Azure Resource Manager-sjablonen voor het maken van een virtuele machine
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 869e59aea9b78c44b1a920e58ecefab5e0ca4920
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169416"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Zelfstudie: Azure Resource Manager-sjablonen maken

Informatie over het maken van gekoppelde Azure Resource Manager-sjablonen. Met gekoppelde sjablonen kunt u een sjabloon een andere sjabloon laten aanroepen. Dit is handig om sjablonen te modulariseren. In deze zelf studie gebruikt u dezelfde sjabloon die wordt gebruikt in de [zelf studie: maak Azure Resource Manager sjablonen met afhankelijke resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), waarmee u een virtuele machine, een virtueel netwerk en een andere afhankelijke resource met inbegrip van een opslag account maakt. U verplaatst de gemaakte resource van het opslagaccount naar een gekoppelde sjabloon.

Het aanroepen van een gekoppelde sjabloon is vergelijkbaar met het maken van een functie aanroep.  U leert ook hoe u parameter waarden kunt door geven aan de gekoppelde sjabloon en hoe u retour waarden ophaalt uit de gekoppelde sjabloon.

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De gekoppelde sjabloon maken
> * De gekoppelde sjabloon uploaden
> * Koppeling maken met de gekoppelde sjabloon
> * Afhankelijkheid configureren
> * De sjabloon implementeren
> * Aanvullende procedures

Zie voor meer informatie [gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources](./resource-group-linked-templates.md).

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* [Visual Studio Code](https://code.visualstudio.com/) met de [extensie Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren). Dit is dezelfde sjabloon die wordt gebruikt in de [zelf studie: maak Azure Resource Manager sjablonen met afhankelijke resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md). U slaat twee kopieën op van dezelfde sjabloon. Deze worden gebruikt als:

* **De hoofdsjabloon**: voor het maken van alle resources, met uitzondering van het opslagaccount.
* **De gekoppelde sjabloon**: voor het maken van het opslagaccount.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.
4. Er worden vijf resources gedefinieerd met de sjabloon:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Het is handig om basis informatie over het sjabloon schema op te halen voordat u de sjabloon aanpast.
5. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.
6. Selecteer **Bestand**>**Opslaan als** om nog een exemplaar te maken van het bestand met de naam **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>De gekoppelde sjabloon maken

De gekoppelde sjabloon maakt een opslagaccount. De gekoppelde sjabloon kan worden gebruikt als een zelfstandige sjabloon voor het maken van een opslag account. In deze zelf studie heeft de gekoppelde sjabloon twee para meters en wordt er een waarde weer gegeven in de hoofd sjabloon. Deze return-waarde is gedefinieerd in het element `outputs`.

1. Open **linkedTemplate. json** in Visual Studio code als het bestand niet wordt geopend.
2. Breng de volgende wijzigingen aan:

    * Verwijder alle para meters, met uitzonde ring van de **locatie**.
    * Voeg een parameter met de naam **storageAccountName** toe.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        De naam en locatie van het opslag account worden door gegeven van de hoofd sjabloon naar de gekoppelde sjabloon als para meters.

    * Het element **variables** en alle variabeledefinities te verwijderen.
    * Verwijder alle andere resources dan het opslag account. U moet in totaal vier resources verwijderen.
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
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
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
3. Sla de wijzigingen op.

## <a name="upload-the-linked-template"></a>De gekoppelde sjabloon uploaden

De hoofdsjabloon en de gekoppelde sjabloon moeten toegankelijk zijn vanaf de locatie waar u de implementatie uitvoert. In deze zelf studie gebruikt u de implementatie methode Cloud shell zoals u in de [zelf studie hebt gebruikt: Azure Resource Manager sjablonen met afhankelijke resources maken](./resource-manager-tutorial-create-templates-with-dependent-resources.md). De hoofdsjabloon (azuredeploy.json)-sjabloon is geüpload naar de shell. De gekoppelde sjabloon (linkedTemplate.json) moet ergens veilig worden gedeeld. Met het volgende PowerShell-script wordt een Azure Storage-account gemaakt, de sjabloon geüpload naar het Storage-account, en vervolgens een SAS-token voor beperkte toegang tot het sjabloonbestand gegenereerd. Om de zelf studie te vereenvoudigen, downloadt het script een voltooide gekoppelde sjabloon uit een github-opslag plaats. Als u de gekoppelde sjabloon die u hebt gemaakt, wilt gebruiken, kunt u de gekoppelde sjabloon uploaden met [Cloud Shell](https://shell.azure.com) en vervolgens het script zo aanpassen dat uw eigen gekoppelde sjabloon wordt gebruikt.

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

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Selecteer de groene knop **Uitproberen** om het Azure Cloud Shell-venster te openen.
2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechtermuisknop op een willekeurige plaats in het shellvenster (het marineblauwe deel) en kies vervolgens **Plakken**.
4. Noteer de twee waarden (naam van de resourcegroep en URI van de gekoppelde sjabloon URI) aan het einde van het shellvenster. U hebt deze waarden later in de zelfstudie nodig.
5. Selecteer **Focusmodus sluiten** om het shellvenster te sluiten.

In de praktijk genereert u een SAS-token wanneer u de hoofdsjabloon implementeert en geeft u het SAS-token een kortere verlooptijd om het beter te beveiligen. Zie [SAS-token opgeven tijdens implementatie](./secure-template-with-sas-token.md#provide-sas-token-during-deployment) voor meer informatie.

## <a name="call-the-linked-template"></a>De gekoppelde sjabloon aanroepen

De hoofdsjabloon heet azuredeploy.json.

1. Open **azuredeploy. json** in Visual Studio code als deze niet is geopend.
2. Verwijder de resourcedefinitie van het opslagaccount uit de sjabloon:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Voeg het volgende json-codefragment toe aan de locatie waar u de definitie van het opslagaccount hebt neergezet:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json"
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
4. Zorg ervoor dat u de waarde van het element `uri` hebt gewijzigd in de waarde die u hebt verkregen tijdens het uploaden van de gekoppelde sjabloon (die met een SAS-token). In de praktijk wilt u de URI opgeven met een parameter.
5. De bijgewerkte sjabloon opslaan

## <a name="configure-dependency"></a>Afhankelijkheid configureren

Intrekken uit [zelf studie: maak Azure Resource Manager sjablonen met afhankelijke resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), de virtuele-machine resource is afhankelijk van het opslag account:

![Afhankelijkheidsdiagram van Azure Resource Manager-sjablonen](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

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

    ![Gekoppelde Azure Resource Manager-sjablonen configureren afhankelijkheid](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* is de naam van de implementatieresource.
3. Werk **properties/diagnosticsProfile/bootDiagnostics/storageUri** bij zoals is weergegeven in de vorige schermafbeelding.
4. Sla de bijgewerkte sjabloon op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) voor de implementatieprocedure. Gebruik dezelfde resourcegroepsnaam als het opslagaccount voor het opslaan van de gekoppelde sjabloon. Zo wordt het opschonen van resources in de volgende sectie eenvoudiger. Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Zie [Vereisten](#prerequisites).

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="additional-practice"></a>Aanvullende oefening

U kunt het project verder verbeteren door de volgende aanvullende wijzigingen aan te brengen in het voltooide project:

1. Wijzig de hoofdsjabloon (azuredeploy.json) zo, dat de waarde voor de URI van de gekoppelde sjabloon wordt verkregen via een parameter.
2. Genereer het SAS-token bij het uploaden van de hoofdsjabloon in plaats van bij het uploaden van de gekoppelde sjabloon. Zie [SAS-token opgeven tijdens implementatie](./secure-template-with-sas-token.md#provide-sas-token-during-deployment) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gesplitst in een hoofdsjabloon en een gekoppelde sjabloon. Voor meer informatie over het gebruik van extensies van virtuele machines voor de uitvoering van post-implementatietaken raadpleegt u:

> [!div class="nextstepaction"]
> [Extensies van virtuele machines implementeren](./resource-manager-tutorial-deploy-vm-extensions.md)
