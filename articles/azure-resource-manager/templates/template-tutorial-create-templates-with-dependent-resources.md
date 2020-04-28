---
title: Sjabloon met afhankelijke resources
description: Leer hoe u een Azure Resource Manager-sjabloon maakt met meerdere resources en hoe u deze via Azure Portal implementeert
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: cf876d3c7c100f001ba81082d792e81a777c7315
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193034"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Zelf studie: ARM-sjablonen maken met afhankelijke resources

Meer informatie over het maken van een Azure Resource Manager-sjabloon (ARM) voor het implementeren van meerdere resources en het configureren van de implementatie volgorde. Nadat u de sjabloon hebt gemaakt, implementeert u de sjabloon met behulp van de Cloud Shell van de Azure Portal.

In deze zelfstudie hebt u een opslagaccount, een virtuele machine, een virtueel netwerk en enkele andere afhankelijke resources gemaakt. Sommige resources kunnen niet worden geïmplementeerd totdat er een andere resource bestaat. U kunt bijvoorbeeld niet een virtuele machine maken totdat het bijbehorende opslagaccount en de bijbehorende netwerkinterface bestaan. U definieert deze relatie door een resource afhankelijk van de andere resources te maken. Resource Manager evalueert de afhankelijkheden tussen resources en implementeert ze in de volgorde van afhankelijkheid. Als resources niet van elkaar afhankelijk zijn, worden deze door Resource Manager parallel geïmplementeerd. Zie [de order voor het implementeren van resources in arm-sjablonen definiëren](./define-resource-dependency.md)voor meer informatie.

![Volgorde diagram voor de implementatie van resources van Resource Manager-sjabloon](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * De sjabloon verkennen
> * De sjabloon implementeren

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
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **bestand**>**Opslaan als** om een kopie van het bestand op te slaan op de lokale computer met de naam **azuredeploy. json**.

## <a name="explore-the-template"></a>De sjabloon verkennen

Wanneer u de sjabloon in deze sectie verkent, probeert u om deze vragen te beantwoorden:

* Hoeveel Azure-resources zijn er in deze sjabloon gedefinieerd?
* Een van de resources is een Azure-opslagaccount.  Ziet de definitie eruit zoals de definitie die is gebruikt in de laatste zelfstudie?
* Kunt u de sjabloonverwijzingen vinden voor de resources die zijn gedefinieerd in deze sjabloon?
* Kunt u de afhankelijkheden van de resources vinden?

1. Vouw in Visual Studio Code de elementen samen totdat u alleen de elementen op het eerste niveau en tweede niveau binnen **resources** ziet:

    ![Azure Resource Manager-sjablonen in Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Er zijn zes resources gedefinieerd door de sjabloon:

   * [**Micro soft. Storage/Storage accounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Micro soft. Compute/informatie**](/azure/templates/microsoft.compute/virtualmachines).

     Het is handig om de sjabloon verwijzing te bekijken voordat u een sjabloon aanpast.

1. Vouw de eerste resource uit. Dit is een opslagaccount. Vergelijk de resource definitie met de [sjabloon verwijzing](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Azure Resource Manager-sjablonen in Visual Studio Code: definitie van opslagaccount](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Vouw de tweede resource uit. Het resourcetype is `Microsoft.Network/publicIPAddresses`. Vergelijk de resource definitie met de [sjabloon verwijzing](/azure/templates/microsoft.network/publicipaddresses).

    ![Azure Resource Manager-sjablonen in Visual Studio Code: definitie van openbaar IP-adres](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Vouw de derde resource uit. Het resourcetype is `Microsoft.Network/networkSecurityGroups`. Vergelijk de resource definitie met de [sjabloon verwijzing](/azure/templates/microsoft.network/networksecuritygroups).

    ![Definitie van de netwerk beveiligings groep van Visual Studio code Azure Resource Manager sjablonen](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Vouw de vierde resource uit. Het resourcetype is `Microsoft.Network/virtualNetworks`:

    ![DependsOn voor Visual Studio code Azure Resource Manager-sjablonen van virtueel netwerk](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Met het element dependsOn kunt u één resource als afhankelijk van een of meer resources definiëren. Deze resource is afhankelijk van één andere resource:

    * `Microsoft.Network/networkSecurityGroups`

1. Vouw de vijfde resource uit. Het resourcetype is `Microsoft.Network/networkInterfaces`. De resource is afhankelijk van twee andere resources:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Vouw de zesde resource uit. Deze resource is een virtuele machine. Deze is afhankelijk van twee andere resources:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Het volgende diagram illustreert de resources en de afhankelijkheidsgegevens voor deze sjabloon:

![Azure Resource Manager-sjablonen in Visual Studio Code: afhankelijkheidsdiagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Door de afhankelijkheden op te geven, kan Resource Manager de oplossing efficiënt implementeren. Het implementeert tegelijk het opslagaccount, een openbaar IP-adres en een virtueel netwerk omdat ze geen afhankelijkheden hebben. Nadat het openbare IP-adres en het virtuele netwerk zijn geïmplementeerd, wordt de netwerkinterface gemaakt. Wanneer alle andere resources zijn geïmplementeerd, implementeert Resource Manager de virtuele machine.

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Meld u aan bij de [Azure Cloud shell](https://shell.azure.com)

1. Kies uw voorkeurs omgeving door **Power shell** of **bash** (voor CLI) in de linkerbovenhoek te selecteren.  U moet de shell opnieuw starten wanneer u overschakelt.

    ![Azure Portal Cloud Shell bestand uploaden](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Selecteer **Upload/download files** en selecteer **Uploaden**. Zie de vorige schermafbeelding. Selecteer het bestand dat u eerder hebt opgeslagen. Nadat het bestand is geüpload, kunt u de opdracht **ls** gebruiken en de **Cat** -opdracht om te controleren of het bestand is geüpload.

1. Voer het volgende Power shell-script uit om de sjabloon te implementeren.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

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

    ---

1. RDP voor de virtuele machine om te controleren of de virtuele machine is gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in de Azure Portal **resource groep** in het menu links.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep. U ziet in totaal zes resources in de resource groep.
4. Selecteer **resource groep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een sjabloon ontwikkeld en geïmplementeerd voor het maken van een virtuele machine, een virtueel netwerk en de afhankelijke resources. Zie voor meer informatie over het gebruik van implementatie scripts voor het uitvoeren van pre/na de implementatie-bewerkingen:

> [!div class="nextstepaction"]
> [Implementatiescript gebruiken](./template-tutorial-deployment-script.md)
