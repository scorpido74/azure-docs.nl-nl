---
title: 'Snelstart: een load balancer maken - Azure-sjabloon'
titleSuffix: Azure Load Balancer
description: In deze snelstart ziet u hoe u een load balancer maakt met behulp van de sjabloon Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: 175c5a36c873d16d50d5192a489133a01018e335
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474594"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snelstart: een load balancer maken om vm's te laden met Azure Resource Manager-sjabloon

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines (VM's). In deze snelstart ziet u hoe u een Azure Resource Manager-sjabloon implementeert waarmee een standaardloadbalancer wordt gemaakt om VM's voor het laden van balansteladen te implementeren. Met behulp van resourcebeheersjabloon neemt u minder stappen in vergelijking met andere implementatiemethoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-load-balancer"></a>Een load balancer maken

Load Balancer en Public IP SKU's moeten overeenkomen. Wanneer u een Standaard Load Balancer maakt, moet u ook een nieuw standaard IP-adres maken dat is geconfigureerd als frontend voor de standaardloadbalancer. Als u een basislastbalans wilt maken, gebruikt u [deze sjabloon](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Microsoft raadt aan om Standaard SKU te gebruiken voor productieworkloads.

### <a name="review-the-template"></a>De sjabloon bekijken

De sjabloon die in deze quickstart wordt gebruikt, is afkomstig van [Azure Quickstart-sjablonen.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

Er zijn meerdere Azure-bronnen gedefinieerd in de sjabloon:

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses:**](/azure/templates/microsoft.network/publicipaddresses)voor de load balancer en voor elk van de drie virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 van hen)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 van hen)
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3 van hen): gebruiken om het IIS en de webpagina's te configureren

Zie [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)voor meer sjablonen die gerelateerd zijn aan Azure Load Balancer.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **Probeer het** in het volgende codeblok om Azure Cloud Shell te openen en volg de instructies om u aan te melden bij Azure.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   Wacht tot u de prompt van de console ziet.

1. Selecteer **Kopiëren** uit het vorige codeblok om het PowerShell-script te kopiëren.

1. Klik met de rechtermuisknop op het deelvenster shell-console en selecteer **Plakken**.

1. Voer de waarden in.

   De sjabloonimplementatie maakt drie beschikbaarheidszones. Beschikbaarheidszones worden alleen in [bepaalde regio's](../availability-zones/az-overview.md)ondersteund. Gebruik een van de ondersteunde regio's. Als u niet zeker bent, voer **centralus**.

   De naam van de resourcegroep is de projectnaam **met rg** toegevoegd. U hebt de naam van de resourcegroep in de volgende sectie nodig.

Het implementeren van de sjabloon duurt ongeveer 10 minuten. Wanneer de uitvoer is voltooid, is vergelijkbaar met:

![Azure Standard Load Balancer Resource Manager-sjabloon PowerShell-implementatieuitvoer](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell wordt gebruikt om de sjabloon te implementeren. Naast Azure PowerShell u ook de Azure-portal, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md)voor meer informatie over andere implementatiemethoden.

## <a name="test-the-load-balancer"></a>Load balancer testen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Resourcegroepen** in het linkerdeelvenster.

1. Selecteer de resourcegroep die u in de vorige sectie hebt gemaakt. De standaardnaam van de resourcegroep is de projectnaam **met rg** toegevoegd.

1. Selecteer de load balancer. De standaardnaam is de projectnaam met **-lb** toegevoegd.

1. Kopieer alleen het IP-adresgedeelte van het openbare IP-adres en plak het vervolgens in de adresbalk van uw browser.

   ![Openbaar IP-sjabloon Azure-standaardloadbalancer Resource Manager](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    De browser geeft de standaardpagina van de IIS-webserver (Internet Information Services) weer.

   ![IIS-webserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Als u wilt zien dat de load balancer het verkeer over alle drie de VM's verdeelt, u een vernieuwing van uw webbrowser vanaf de clientmachine forceren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ze niet meer nodig hebt, verwijdert u de brongroep, de load balancer en alle gerelateerde resources. Ga hiervoor naar de Azure-portal, selecteert u de resourcegroep die de load balancer bevat en selecteer vervolgens **Brongroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een standaardloadbalancer gemaakt, VM's aan gekoppeld, de regel voor het verkeer van de load-balancer geconfigureerd, een statussonde uitgevoerd en vervolgens de load balancer getest.

Ga voor meer informatie verder naar de tutorials voor Load Balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
