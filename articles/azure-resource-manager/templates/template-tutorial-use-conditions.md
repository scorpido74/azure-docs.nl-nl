---
title: Voor waarde gebruiken in sjablonen
description: Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden. Laat zien hoe u een nieuwe resource implementeert of een bestaande resource gebruikt.
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 189d54454a1259d08400e3762b3fbf1c633474bd
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250052"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Zelfstudie: Voorwaarde gebruiken in Azure Resource Manager-sjablonen

Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden.

In de zelfstudie [Resource-implementatievolgorde instellen](./template-tutorial-create-templates-with-dependent-resources.md) maakt u een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources, waaronder een opslagaccount. In plaats van elke keer een nieuw opslagaccount te maken, laat u gebruikers kiezen of ze een nieuw opslagaccount willen maken of een bestaand opslagaccount willen gebruiken. Om dit doel te bereiken, definieert u een extra parameter. Als de waarde van de parameter 'new' is, wordt er een nieuw opslagaccount gemaakt. Anders wordt een bestaand opslag account met de gegeven naam gebruikt.

![Diagram voor condition-gebruik van Resource Manager-sjabloon](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een quickstartsjabloon openen
> * De sjabloon aanpassen
> * De sjabloon implementeren
> * Resources opschonen

Deze zelf studie geldt alleen voor een basis scenario voor het gebruik van voor waarden. Ga voor meer informatie naar:

* [Structuur van sjabloon bestand: voor waarde](conditional-resource-deployment.md).
* [Een resource voorwaardelijk implementeren in een Azure Resource Manager sjabloon](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Sjabloon functie: if](./template-functions-logical.md#if).
* [Vergelijkings functies voor Azure Resource Manager sjablonen](./template-functions-comparison.md)

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio code met de extensie Resource Manager-Hulpprogram Ma's. Zie [Visual Studio code gebruiken om Azure Resource Manager sjablonen te maken](use-vs-code-to-create-template.md).
* Gebruik een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine om de beveiliging te verhogen. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in de Resource Manager-sjabloonimplementatie](./template-tutorial-use-key-vault.md) voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

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
* Voeg een nieuwe parameter toe met de naam **newOrExisting**. Deze para meter wordt gebruikt om te bepalen of u een nieuw opslag account wilt maken of een bestaand opslag account wilt gebruiken.

Hier volgt de procedure waarmee de wijzigingen kunnen worden aangebracht:

1. Open **azuredeploy.json** in Visual Studio Code.
2. Vervang de drie **variabelen (' storageAccountName ')** door **para meters (' storageAccountName ')** in de hele sjabloon.
3. Verwijder de volgende variabeledefinitie:

    ![Diagram voor condition-gebruik van Resource Manager-sjabloon](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

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

    ![Voorwaarde gebruiken in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Voeg de volgende regel toe aan het begin van de definitie van het opslagaccount.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    De voorwaarde controleert de waarde van een parameter met de naam **newOrExisting**. Als de parameterwaarde **new** is, wordt er een opslagaccount gemaakt.

    De bijgewerkte definitie van het opslagaccount ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
6. Werk de eigenschap **storageUri** van de bron definitie van de virtuele machine bij met de volgende waarde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Deze wijziging is nodig als u een bestaand opslagaccount gebruikt onder een andere resourcegroep.

7. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Volg de instructies in [de sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) om de Cloud shell te openen en de gereviseerde sjabloon te uploaden, en voer vervolgens het volgende Power shell-script uit om de sjabloon te implementeren.

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

Probeer een andere implementatie uit te voeren met **newOrExisting** ingesteld op ' bestaand ' en geef een bestaand opslag account op. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor informatie over het vooraf maken van een opslagaccount.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als u de resource groep wilt verwijderen, selecteert u **proberen** om de Cloud shell te openen. Als u het Power shell-script wilt plakken, klikt u met de rechter muisknop op het deel venster shell en selecteert u vervolgens **Plakken**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the same resource group name you used in the last procedure"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gemaakt waarmee gebruikers kunnen kiezen tussen het maken van een nieuw opslagaccount en het gebruiken van een bestaand opslagaccount. Zie de volgende zelfstudie voor informatie over het ophalen van geheimen uit Azure Key Vault en hoe u deze geheimen als wachtwoorden gebruikt in de sjabloonimplementatie:

> [!div class="nextstepaction"]
> [Key Vault integreren in sjabloonimplementatie](./template-tutorial-use-key-vault.md)
