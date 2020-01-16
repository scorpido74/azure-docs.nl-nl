---
title: 'Snelstartgids: een standaard load balancer maken-Azure-sjabloon'
titleSuffix: Azure Load Balancer
description: In deze Quick start ziet u hoe u een standaard load balancer maakt met behulp van de Azure Resource Manager sjabloon.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard load balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 28c4ca59b20fcdd8ab0249392dc0f13cbabf1a97
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970636"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-by-using-azure-resource-manager-template"></a>Quick Start: een standaard load balancer maken om taken van Vm's te verdelen met behulp van Azure Resource Manager sjabloon

Taakverdeling zorgt voor een hogere beschikbaarheid en betere schaalbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines (VM's). In deze Quick start ziet u hoe u een Azure Resource Manager sjabloon kunt implementeren waarmee een standaard-load balancer wordt gemaakt voor de taak verdeling van Vm's. Het gebruik van Resource Manager-sjablonen neemt minder stappen in vergelijking met andere implementatie methoden.

[Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md) is een JavaScript object Notation-bestand (JSON) waarmee de infra structuur en configuratie voor uw project worden gedefinieerd. De sjabloon maakt gebruik van declaratieve syntaxis, waarmee u kunt aangeven wat u wilt implementeren zonder dat u de volg orde van de programmeer opdrachten hoeft te schrijven om deze te maken. Als u meer wilt weten over het ontwikkelen van Resource Manager-sjablonen, raadpleegt u de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.network/loadbalancers).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Standard Load Balancer ondersteunt alleen een standaard openbaar IP-adres. Wanneer u een standaard load balancer maakt, moet u ook een nieuw standaard openbaar IP-adres maken dat is geconfigureerd als de front-end voor de standaard load balancer.

De sjabloon die in deze Quick Start wordt gebruikt, is een [Quick Start sjabloon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-load-balancer-standard-create/azuredeploy.json).

[!code-json[<Azure Resource Manager template create standard load balancer>](~/quickstart-templates/101-load-balancer-standard-create/azuredeploy.json)]

Er zijn meerdere Azure-resources gedefinieerd in de sjabloon:

- **Microsoft.Network/loadBalancers**
- **Micro soft. Network/publicIPAddresses**: voor de Load Balancer.
- **Microsoft.Network/networkSecurityGroups**
- **Microsoft.Network/virtualNetworks**
- **Micro soft. Compute/virutalMachines** (3 van deze)
- **Micro soft. Network/publicIPAddresses** (3): voor elk van de drie virtuele machines.
- **Micro soft. Network/networkInterfaces** (3 van deze)
- **Micro soft. Compute/virtualMachine/Extensions** (3): gebruik om de IIS-en de webpagina's te configureren

Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)start-sjablonen voor meer informatie over de sjablonen die zijn gerelateerd aan Azure Load Balancer.

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

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

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
