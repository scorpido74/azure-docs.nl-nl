---
title: Sjabloon met afhankelijke resources
description: Leer hoe u een Azure Resource Manager-sjabloon maakt met meerdere resources en hoe u deze via Azure Portal implementeert
author: mumian
ms.date: 04/10/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e589fa8ae5627746ec1f04e2098a7b592f00dc24
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684938"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Zelfstudie: ARM-sjablonen maken met afhankelijke bronnen

Meer informatie over het maken van een ARM-sjabloon (Azure Resource Manager) om meerdere resources te implementeren en de implementatieorder te configureren. Nadat u de sjabloon hebt gemaakt, implementeert u de sjabloon met behulp van de Cloud Shell vanuit de Azure-portal.

In deze zelfstudie hebt u een opslagaccount, een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources gemaakt. Sommige resources kunnen niet worden geïmplementeerd totdat er een andere resource bestaat. U kunt bijvoorbeeld niet een virtuele machine maken totdat het bijbehorende opslagaccount en de bijbehorende netwerkinterface bestaan. U definieert deze relatie door een resource afhankelijk van de andere resources te maken. Resource Manager evalueert de afhankelijkheden tussen resources en implementeert ze in de volgorde van afhankelijkheid. Als resources niet van elkaar afhankelijk zijn, worden deze door Resource Manager parallel geïmplementeerd. Zie [De volgorde definiëren voor het implementeren van resources in ARM-sjablonen](./define-resource-dependency.md)voor meer informatie.

![Resource Manager-sjabloon afhankelijk van het implementatieorderdiagram van resources](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon verkennen
> * De sjabloon implementeren

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](use-vs-code-to-create-template.md)
* Voor een verbeterde beveiliging gebruikt u een gegenereerd wachtwoord voor het beheerdersaccount van de virtuele machine. Hier volgt een voorbeeld voor het genereren van een wachtwoord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault is ontworpen om cryptografische sleutels en andere geheimen te beveiligen. Zie [Zelfstudie: Azure Key Vault integreren in ARM-sjabloonimplementatie](./template-tutorial-use-key-vault.md). We raden u ook aan om uw wachtwoord elke drie maanden te wijzigen.

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

Azure QuickStart-sjablonen is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De sjabloon die in deze zelfstudie wordt gebruikt, heet [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Een eenvoudige Windows-VM implementeren).

1. Selecteer **Bestand**>**openen bestand**in Visual Studio-code .
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**opslaan als** u een kopie van het bestand op uw lokale computer wilt opslaan met de naam **azuredeploy.json**.

## <a name="explore-the-template"></a>De sjabloon verkennen

Wanneer u de sjabloon in deze sectie verkent, probeert u om deze vragen te beantwoorden:

* Hoeveel Azure-resources zijn er in deze sjabloon gedefinieerd?
* Een van de resources is een Azure-opslagaccount.  Ziet de definitie eruit zoals de definitie die is gebruikt in de laatste zelfstudie?
* Kunt u de sjabloonverwijzingen vinden voor de resources die zijn gedefinieerd in deze sjabloon?
* Kunt u de afhankelijkheden van de resources vinden?

1. Vouw in Visual Studio Code de elementen samen totdat u alleen de elementen op het eerste niveau en tweede niveau binnen **resources** ziet:

    ![Azure Resource Manager-sjablonen in Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    De sjabloon heeft zes bronnen gedefinieerd:

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om de sjabloonverwijzing te bekijken voordat u een sjabloon aanwerkt.

1. Vouw de eerste resource uit. Dit is een opslagaccount. Vergelijk de resourcedefinitie met de [sjabloonverwijzing](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Azure Resource Manager-sjablonen in Visual Studio Code: definitie van opslagaccount](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Vouw de tweede resource uit. Het resourcetype is `Microsoft.Network/publicIPAddresses`. Vergelijk de resourcedefinitie met de [sjabloonverwijzing](/azure/templates/microsoft.network/publicipaddresses).

    ![Azure Resource Manager-sjablonen in Visual Studio Code: definitie van openbaar IP-adres](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. De derde resource uitbreiden. Het resourcetype is `Microsoft.Network/networkSecurityGroups`. Vergelijk de resourcedefinitie met de [sjabloonverwijzing](/azure/templates/microsoft.network/networksecuritygroups).

    ![Definitie van de netwerkbeveiligingsgroep van Visual Studio Code Azure Resource Manager-sjablonen](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Vouw de vierde resource uit. Het resourcetype is `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code Azure Resource Manager-sjablonen virtueel netwerk afhankelijk](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Met het element dependsOn kunt u één resource als afhankelijk van een of meer resources definiëren. Deze bron is afhankelijk van een andere bron:

    * `Microsoft.Network/networkSecurityGroups`

1. Vouw de vijfde resource uit. Het resourcetype is `Microsoft.Network/networkInterfaces`. De resource is afhankelijk van twee andere resources:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Breid de zesde resource uit. Deze resource is een virtuele machine. Deze is afhankelijk van twee andere resources:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Het volgende diagram illustreert de resources en de afhankelijkheidsgegevens voor deze sjabloon:

![Azure Resource Manager-sjablonen in Visual Studio Code: afhankelijkheidsdiagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Door de afhankelijkheden op te geven, kan Resource Manager de oplossing efficiënt implementeren. Het implementeert tegelijk het opslagaccount, een openbaar IP-adres en een virtueel netwerk omdat ze geen afhankelijkheden hebben. Nadat het openbare IP-adres en het virtuele netwerk zijn geïmplementeerd, wordt de netwerkinterface gemaakt. Wanneer alle andere resources zijn geïmplementeerd, implementeert Resource Manager de virtuele machine.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Volg de instructies in [De sjabloon implementeren](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) om de Cloud Shell te openen en de herziene sjabloon te uploaden.

1. Voer het volgende PowerShell-script uit om de sjabloon te implementeren.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Voer de volgende PowerShell-opdracht uit om de nieuwe virtuele machine weer te geven:

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $resourceGroupName = "${projectName}rg"
    $vmName = "SimpleWinVM"

    Get-AzVM -Name $vmName -ResourceGroupName $resourceGroupName
    
    Write-Host "Press [ENTER] to continue ..."
    ```

    De naam van de virtuele machine is in code vastgelegd als **SimpleWinVM** in de sjabloon.

1. RDP voor de virtuele machine om te controleren of de virtuele machine is gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon ontwikkeld en geïmplementeerd voor het maken van een virtuele machine, een virtueel netwerk en de afhankelijke resources. Zie voor meer informatie over het gebruik van implementatiescripts voor het uitvoeren van pre/post-implementatiebewerkingen:

> [!div class="nextstepaction"]
> [Implementatiescript gebruiken](./template-tutorial-deployment-script.md)
