---
title: Meerdere resource-exemplaren maken
description: Leer hoe u een Azure Resource Manager-sjabloon maakt om meerdere resource-instanties te maken.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b62cca48323d4e12a92c89d64ab67bf5b783c36f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183825"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>Zelf studie: meerdere bron exemplaren maken met ARM-sjablonen

Meer informatie over hoe u uw Azure Resource Manager (ARM)-sjabloon kunt herhalen om meerdere exemplaren van een Azure-resource te maken. In deze zelfstudie wijzigt u een sjabloon om drie instanties van het opslagaccount te maken.

![Azure Resource Manager maakt meerdere exemplaren diagram](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio code gebruiken om arm-sjablonen te maken](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Quick](https://azure.microsoft.com/resources/templates/) start-sjablonen van Azure is een opslag plaats voor arm-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer **bestand**>**openen**in Visual Studio code.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.
4. De resource Microsoft.Storage/storageAccounts is in de sjabloon gedefinieerd. Vergelijk de sjabloon met de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
5. Selecteer **bestand**>**Opslaan als** om het bestand op te slaan als **azuredeploy. json** op de lokale computer.

## <a name="edit-the-template"></a>De sjabloon bewerken

Met de bestaande sjabloon wordt één opslagaccount gemaakt. U past de sjabloon aan om drie opslagaccounts te maken.

Breng vanuit Visual Studio Code de volgende vier wijzigingen aan:

![Azure Resource Manager maakt meerdere instanties](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. Voeg een `copy`-element toe aan de resourcedefinitie van het opslagaccount. In het copy-element geeft u het aantal iteraties en een variabele voor deze lus op. Het aantal iteraties moet een positief geheel getal zijn en mag niet hoger zijn dan 800.
2. De functie `copyIndex()` retourneert de huidige iteratie in de lus. U gebruikt de index als het voorvoegsel van de naam. `copyIndex()` is gebaseerd op nul. Als u de indexwaarde wilt verschuiven, kunt u een waarde doorgeven in de functie copyIndex(). Bijvoorbeeld *copyIndex(1)*.
3. Verwijder het element **variables**, want dit wordt niet meer gebruikt.
4. Verwijder het element **outputs**. Dit is niet langer nodig.

De voltooide sjabloon ziet er als volgt uit:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
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
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

Zie [meerdere exemplaren van een resource of eigenschap implementeren in arm-sjablonen](./copy-resources.md) voor meer informatie over het maken van meerdere exemplaren.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij de [Azure Cloud shell](https://shell.azure.com)

1. Kies uw voorkeurs omgeving door **Power shell** of **bash** (voor CLI) in de linkerbovenhoek te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Azure Portal Cloud Shell bestand uploaden](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht **ls** gebruiken en de **Cat** -opdracht om te controleren of het bestand is geüpload.

1. Voer de volgende opdrachten uit op de Cloud Shell. Selecteer het tabblad om de PowerShell-code of de CLI-code weer te geven.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```

    ---

Als u alle drie de opslagaccounts wilt weergeven, laat u de parameter --name weg:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"

Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

Vergelijk de namen van de opslagaccounts met de naamdefinitie in de sjabloon.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  In totaal worden drie resources in de resource groep weer geven.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u meerdere instanties van opslagaccounts maakt.  In de volgende zelfstudie ontwikkelt u een sjabloon met meerdere resources en meerdere resourcetypen. Sommige resources hebben afhankelijke resources.

> [!div class="nextstepaction"]
> [Afhankelijke resources maken](./template-tutorial-create-templates-with-dependent-resources.md)
