---
title: Voor waarde gebruiken in sjablonen
description: Leer hoe u Azure-resources implementeert die zijn gebaseerd op voorwaarden. Laat zien hoe u een nieuwe resource implementeert of een bestaande resource gebruikt.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b73598da2b34847a38485db9952302f7c5b33c98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82185027"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Zelf studie: voor waarde in ARM-sjablonen gebruiken

Meer informatie over hoe u Azure-resources implementeert op basis van voor waarden in een Azure Resource Manager ARM-sjabloon.

In de zelfstudie [Resource-implementatievolgorde instellen](./template-tutorial-create-templates-with-dependent-resources.md) maakt u een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources, waaronder een opslagaccount. In plaats van elke keer een nieuw opslagaccount te maken, laat u gebruikers kiezen of ze een nieuw opslagaccount willen maken of een bestaand opslagaccount willen gebruiken. Om dit doel te bereiken, definieert u een extra parameter. Als de waarde van de parameter 'new' is, wordt er een nieuw opslagaccount gemaakt. Anders wordt een bestaand opslag account met de gegeven naam gebruikt.

![Diagram voor condition-gebruik van Resource Manager-sjabloon](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon aanpassen
> * De sjabloon implementeren
> * Resources opschonen

Deze zelf studie geldt alleen voor een basis scenario voor het gebruik van voor waarden. Zie voor meer informatie:

* [Structuur van sjabloon bestand: voor waarde](conditional-resource-deployment.md).
* [Een resource voorwaardelijk implementeren in een arm-sjabloon](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Sjabloon functie: if](./template-functions-logical.md#if).
* [Vergelijkings functies voor ARM-sjablonen](./template-functions-comparison.md)

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio code gebruiken om arm-sjablonen te maken](use-vs-code-to-create-template.md).
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [zelf studie: Azure Key Vault integreren in arm-sjabloon implementatie](./template-tutorial-use-key-vault.md)voor meer informatie. We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Quick Start-sjablonen van Azure is een opslag plaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer **bestand**>**openen**in Visual Studio code.
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. Er zijn zes resources gedefinieerd door de sjabloon:

   * [**Micro soft. Storage/Storage accounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines).

    Het is handig om de sjabloon verwijzing te bekijken voordat u een sjabloon aanpast.

1. Selecteer **bestand**>**Opslaan als** om een kopie van het bestand op te slaan op de lokale computer met de naam **azuredeploy. json**.

## <a name="modify-the-template"></a>De sjabloon aanpassen

Breng de volgende twee wijzigingen aan in de bestaande sjabloon:

* Voeg een parameter voor de opslagaccountnaam toe. Gebruikers kunnen een nieuwe opslagaccountnaam of een bestaande opslagaccountnaam opgeven.
* Voeg een nieuwe parameter toe met de naam **newOrExisting**. Deze para meter wordt gebruikt om te bepalen of u een nieuw opslag account wilt maken of een bestaand opslag account wilt gebruiken.

Hier volgt de procedure waarmee de wijzigingen kunnen worden aangebracht:

1. Open **azuredeploy.json** in Visual Studio Code.
1. Vervang de drie **variabelen (' storageAccountName ')** door **para meters (' storageAccountName ')** in de hele sjabloon.
1. Verwijder de volgende variabeledefinitie:

    ![Diagram voor condition-gebruik van Resource Manager-sjabloon](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Voeg de volgende twee para meters toe aan het begin van de para meters sectie:

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

    Druk op **[Alt] + [SHIFT] + F** om de sjabloon in Visual Studio code in te delen.

    De bijgewerkte parameterdefinitie ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Voeg de volgende regel toe aan het begin van de definitie van het opslagaccount.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    De voorwaarde controleert de waarde van een parameter met de naam **newOrExisting**. Als de parameterwaarde **new** is, wordt er een opslagaccount gemaakt.

    De bijgewerkte definitie van het opslagaccount ziet er als volgt uit:

    ![Voorwaarde gebruiken in Resource Manager](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Werk de eigenschap **storageUri** van de bron definitie van de virtuele machine bij met de volgende waarde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Deze wijziging is nodig als u een bestaand opslagaccount gebruikt onder een andere resourcegroep.

1. Sla de wijzigingen op.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij de [Azure Cloud shell](https://shell.azure.com)

1. Kies uw voorkeurs omgeving door **Power shell** of **bash** (voor CLI) in de linkerbovenhoek te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Azure Portal Cloud Shell bestand uploaden](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u in de vorige sectie hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht **ls** gebruiken en de **Cat** -opdracht om te controleren of het bestand is geüpload.

1. Voer het volgende Power shell-script uit om de sjabloon te implementeren.

    > [!IMPORTANT]
    > De naam van het opslagaccount moet uniek zijn in Azure. De naam mag alleen kleine letters of cijfers bevatten. De waarde mag niet langer zijn dan 24 tekens. De naam van het opslag account is de project naam ' Store ' toegevoegd. Zorg ervoor dat de project naam en de gegenereerde opslag account naam voldoen aan de vereisten voor de opslag account.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > De implementatie mislukt als **newOrExisting** de waarde **new** heeft, maar het opslagaccount met de opgegeven naam al bestaat.

Probeer een andere implementatie uit te voeren met **newOrExisting** ingesteld op ' bestaand ' en geef een bestaand opslag account op. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor informatie over het vooraf maken van een opslagaccount.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen. Als u de resource groep wilt verwijderen, selecteert u **proberen deze** te openen Cloud shell. Als u het Power shell-script wilt plakken, klikt u met de rechter muisknop op het deel venster shell en selecteert u vervolgens **Plakken**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon gemaakt waarmee gebruikers kunnen kiezen tussen het maken van een nieuw opslagaccount en het gebruiken van een bestaand opslagaccount. Zie de volgende zelfstudie voor informatie over het ophalen van geheimen uit Azure Key Vault en hoe u deze geheimen als wachtwoorden gebruikt in de sjabloonimplementatie:

> [!div class="nextstepaction"]
> [Key Vault integreren in sjabloonimplementatie](./template-tutorial-use-key-vault.md)
