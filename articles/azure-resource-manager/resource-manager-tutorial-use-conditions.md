---
title: Use condition in templates
description: Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden. Shows how to either deploy a new resource or use an existing resource.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7aecc25daed8f73e903b181d782e53c0ea5acd74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325334"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Zelfstudie: Voorwaarde gebruiken in Azure Resource Manager-sjablonen

Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden.

In de zelfstudie [Resource-implementatievolgorde instellen](./resource-manager-tutorial-create-templates-with-dependent-resources.md) maakt u een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources, waaronder een opslagaccount. In plaats van elke keer een nieuw opslagaccount te maken, laat u gebruikers kiezen of ze een nieuw opslagaccount willen maken of een bestaand opslagaccount willen gebruiken. Om dit doel te bereiken, definieert u een extra parameter. Als de waarde van de parameter 'new' is, wordt er een nieuw opslagaccount gemaakt. Otherwise, an existing storage account with the name provided is used.

![Resource Manager template use condition diagram](./media/resource-manager-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon aanpassen
> * De sjabloon implementeren
> * Resources opschonen

This tutorial only covers a basic scenario of using conditions. Zie voor meer informatie:

* [Template file structure: Condition](conditional-resource-deployment.md).
* [Conditionally deploy a resource in an Azure Resource Manager template](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Template function: If](./resource-group-template-functions-logical.md#if).
* [Comparison functions for Azure Resource Manager templates](./resource-group-template-functions-comparison.md)

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure-snelstartsjablonen is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Er worden vijf resources gedefinieerd met de sjabloon:

   * `Microsoft.Storage/storageAccounts`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Zie de [sjabloonverwijzing](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om enige basiskennis te hebben van de sjabloon voordat u deze gaat aanpassen.
5. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="modify-the-template"></a>De sjabloon aanpassen

Breng de volgende twee wijzigingen aan in de bestaande sjabloon:

* Voeg een parameter voor de opslagaccountnaam toe. Gebruikers kunnen een nieuwe opslagaccountnaam of een bestaande opslagaccountnaam opgeven.
* Voeg een nieuwe parameter toe met de naam **newOrExisting**. The deployment uses this parameter to determine whether to create a new storage account or use an existing storage account.

Hier volgt de procedure waarmee de wijzigingen kunnen worden aangebracht:

1. Open **azuredeploy.json** in Visual Studio Code.
2. Replace the three **variables('storageAccountName')** with **parameters('storageAccountName')** in the whole template.
3. Verwijder de volgende variabeledefinitie:

    ![Resource Manager template use condition diagram](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

4. Voeg de volgende twee parameters toe aan de sjabloon:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    De bijgewerkte parameterdefinitie ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Voeg de volgende regel toe aan het begin van de definitie van het opslagaccount.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    De voorwaarde controleert de waarde van een parameter met de naam **newOrExisting**. Als de parameterwaarde **new** is, wordt er een opslagaccount gemaakt.

    De bijgewerkte definitie van het opslagaccount ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Update the **storageUri** property of the virtual machine resource definition with the following value:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Deze wijziging is nodig als u een bestaand opslagaccount gebruikt onder een andere resourcegroep.

7. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Follow the instructions in [Deploy the template](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) to open the Cloud shell and upload the revised template, and then run the following PowerShell script to deploy the template.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"
```

> [!NOTE]
> De implementatie mislukt als **newOrExisting** de waarde **new** heeft, maar het opslagaccount met de opgegeven naam al bestaat.

Try making another deployment with **newOrExisting** set to "existing" and specify an existing storage account. Zie [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) voor informatie over het vooraf maken van een opslagaccount.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. To delete the resource group, select **Try it** to open the Cloud shell. To paste the PowerShell script, right-click the shell pane, and then select **Paste**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gemaakt waarmee gebruikers kunnen kiezen tussen het maken van een nieuw opslagaccount en het gebruiken van een bestaand opslagaccount. Zie de volgende zelfstudie voor informatie over het ophalen van geheimen uit Azure Key Vault en hoe u deze geheimen als wachtwoorden gebruikt in de sjabloonimplementatie:

> [!div class="nextstepaction"]
> [Key Vault integreren in sjabloonimplementatie](./resource-manager-tutorial-use-key-vault.md)
