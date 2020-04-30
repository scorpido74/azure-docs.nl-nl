---
title: 'Snelstartgids: een Load Balancer-Azure-sjabloon maken'
titleSuffix: Azure Load Balancer
description: In deze Quick start ziet u hoe u een load balancer maakt met behulp van de Azure Resource Manager sjabloon.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80474594"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Snelstartgids: een Load Balancer maken om taken van Vm's te verdelen met behulp van Azure Resource Manager sjabloon

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines (VM's). In deze Quick start ziet u hoe u een Azure Resource Manager sjabloon kunt implementeren waarmee een standaard-load balancer wordt gemaakt voor de taak verdeling van Vm's. Het gebruik van Resource Manager-sjablonen neemt minder stappen in vergelijking met andere implementatie methoden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-load-balancer"></a>Een load balancer maken

Load Balancer en open bare IP-Sku's moeten overeenkomen. Wanneer u een Standard Load Balancer maakt, moet u ook een nieuw standaard openbaar IP-adres maken dat is geconfigureerd als de front-end voor de standaard load balancer. Als u een basis Load Balancer wilt maken, gebruikt u [deze sjabloon](https://azure.microsoft.com/resources/templates/201-2-vms-loadbalancer-natrules/). Micro soft raadt aan om standaard-SKU te gebruiken voor werk belastingen voor productie.

### <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze Quick Start wordt gebruikt, is afkomstig uit [Azure Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json)start-sjablonen.

:::code language="json" source="~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json" range="1-324" highlight="58-122":::

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Micro soft. Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Micro soft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses): voor de Load Balancer en voor elk van de drie virtuele machines.
- [**Micro soft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Micro soft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Micro soft. Compute/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines) (3 van deze)
- [**Micro soft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) (3 van deze)
- [**Micro soft. Compute/virtualMachine/Extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3): gebruik om de IIS-en de webpagina's te configureren

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)start-sjablonen voor meer informatie over de sjablonen die zijn gerelateerd aan Azure Load Balancer.

### <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer **deze** in het volgende code blok proberen om Azure Cloud shell te openen en volg de instructies om u aan te melden bij Azure.

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

   Wacht totdat u de prompt van de-console ziet.

1. Selecteer **kopiëren** in het vorige code blok om het Power shell-script te kopiëren.

1. Klik met de rechter muisknop op het deel venster shell console en selecteer vervolgens **Plakken**.

1. Voer de waarden in.

   Met de sjabloon implementatie worden drie beschikbaarheids zones gemaakt. Beschikbaarheids zones worden alleen ondersteund in [bepaalde regio's](../availability-zones/az-overview.md). Gebruik een van de ondersteunde regio's. Als u dat niet zeker weet, voert u **centralus**in.

   De naam van de resource groep is de naam van het project waaraan **RG** is toegevoegd. U hebt de naam van de resource groep nodig in de volgende sectie.

Het duurt ongeveer 10 minuten om de sjabloon te implementeren. Wanneer dit is voltooid, is de uitvoer vergelijkbaar met:

![Uitvoer van Azure Standard Load Balancer Resource Manager-sjabloon Power shell-implementatie](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

Azure PowerShell wordt gebruikt voor het implementeren van de sjabloon. Naast Azure PowerShell, kunt u ook de Azure Portal, Azure CLI en REST API gebruiken. Zie voor meer informatie over andere implementatie methoden [sjablonen implementeren](../azure-resource-manager/templates/deploy-portal.md).

## <a name="test-the-load-balancer"></a>Load balancer testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **resource groepen** in het linkerdeel venster.

1. Selecteer de resource groep die u in de vorige sectie hebt gemaakt. De standaard naam van de resource groep is de naam van het project waaraan **RG** is toegevoegd.

1. Selecteer de load balancer. De standaard naam is de project naam met **-lb** toevoegen.

1. Kopieer alleen het IP-adres gedeelte van het open bare IP-adres en plak het in de adres balk van uw browser.

   ![Open bare IP van Azure Standard load balancer Resource Manager-sjabloon](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    In de browser wordt de standaard pagina van de Internet Information Services-webserver (IIS) weer gegeven.

   ![IIS-webserver](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

Als u de load balancer distributie van verkeer over alle drie Vm's wilt zien, kunt u het vernieuwen van uw webbrowser afdwingen van de client computer.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de resource groep, de load balancer en alle gerelateerde resources. Als u dit wilt doen, gaat u naar de Azure Portal, selecteert u de resource groep die de load balancer bevat en selecteert u **resource groep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een standaard load balancer, gekoppelde virtuele machines, gemaakt, de regel voor de Load Balancer-verkeer geconfigureerd, een status test en vervolgens de load balancer getest.

Ga verder met de zelf studies voor Load Balancer voor meer informatie.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
