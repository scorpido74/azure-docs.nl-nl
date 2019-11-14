---
title: 'Azure ExpressRoute-sjabloon: een ExpressRoute-circuit maken'
description: Een ExpressRoute-circuit maken, inrichten, verwijderen en de inrichting ervan opheffen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 07/05/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: d6d9a3b472e2a40bfacf25a0b8fe0accb7d62cb8
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031974"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Een ExpressRoute-circuit maken met behulp van Azure Resource Manager sjabloon

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Meer informatie over het maken van een ExpressRoute-circuit door een Azure Resource Manager-sjabloon te implementeren met behulp van Azure PowerShell. Zie de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.network/expressroutecircuits)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Zorg ervoor dat u gemachtigd bent om te maken van nieuwe netwerkresources. Neem contact op met uw accountbeheerder als u nog niet de juiste machtigingen hebt.
* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om beter te begrijpen van de stappen.

## <a name="create"></a>Maken en inrichten van een ExpressRoute-circuit

[Sjablonen voor Azure Quick](https://azure.microsoft.com/resources/templates/) start hebben een goede verzameling van de Resource Manager-sjabloon. U gebruikt een van de [bestaande sjablonen](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) voor het maken van een ExpressRoute-circuit.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Selecteer [hier](https://azure.microsoft.com/resources/templates/?term=expressroute)om meer gerelateerde sjablonen weer te geven.

Een ExpressRoute-circuit maken door een sjabloon te implementeren:

1. Selecteer **Probeer het** uit het volgende code blok en volg de instructies om u aan te melden bij de Azure Cloud shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **Laag** bepaalt of een ExpressRoute-standard- of een premium-invoegtoepassing voor ExpressRoute is ingeschakeld. U kunt opgeven **Standard** om op te halen van de standaard-SKU of **Premium** voor de premium-invoegtoepassing.

   * **Peeringlocatie** is de fysieke locatie waar u peering met Microsoft.

     > [!IMPORTANT]
     > De Peeringlocatie geeft aan dat de [fysieke locatie](expressroute-locations.md) waar u peering met Microsoft. Dit is **niet** gekoppeld aan "Locatie" eigenschap, die verwijst naar de geografische locatie waar de Resourceprovider van de Azure-netwerk zich bevindt. Hoewel ze niet zijn gerelateerd, is het raadzaam een Netwerkresourceprovider geografisch dicht bij de locatie-Peering van het circuit kiezen.

    De naam van de resource groep is de naam van de service bus-naam ruimte waaraan **RG** is toegevoegd.

2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechter muisknop op de shell-console en selecteer vervolgens **Plakken**.

Het duurt enkele minuten om een Event Hub te maken.

Azure PowerShell wordt gebruikt voor het implementeren van de sjabloon in deze zelf studie. Zie voor andere implementatie methoden voor sjablonen:

* Met [behulp van de Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* Met [behulp van Azure cli](../azure-resource-manager/resource-group-template-deploy-cli.md).
* Met [behulp van rest API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="delete"></a>Opheffen van inrichting en het verwijderen van een ExpressRoute-circuit

U kunt uw ExpressRoute-circuit verwijderen door het selecteren van de **verwijderen** pictogram. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking is mislukt, controleert u of alle virtuele netwerken zijn gekoppeld aan het circuit.
* Als het ExpressRoute-circuit serviceprovider-Inrichtingsstatus **Provisioning** of **ingerichte** moet u werken met uw serviceprovider inrichting ongedaan maken van het circuit aan hun kant. We blijven reserveer resources en factureren u totdat de service-provider is voltooid ongedaan maken van inrichting van het circuit en ons wordt geïnformeerd.
* Als de service-provider de inrichting van het circuit is beëindigd (de serviceprovider Inrichtingsstatus is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U kunt uw ExpressRoute-circuit verwijderen door de volgende Power shell-opdracht uit te voeren:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, kunt u doorgaan met de volgende stappen uitvoeren:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtueel netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
