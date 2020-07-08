---
title: 'Azure ExpressRoute-sjabloon: een ExpressRoute-circuit maken'
description: Een ExpressRoute-circuit maken, inrichten, verwijderen en de inrichting ervan opheffen.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: charwen
ms.openlocfilehash: 336337c0860ba19095665310d2c797cf10ba183f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736302"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Een ExpressRoute-circuit maken met behulp van Azure Resource Manager sjabloon

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video-Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [Power shell (klassiek)](expressroute-howto-circuit-classic.md)
>

Meer informatie over het maken van een ExpressRoute-circuit door een Azure Resource Manager-sjabloon te implementeren met behulp van Azure PowerShell. Zie de [documentatie van Resource Manager](/azure/azure-resource-manager/) en de [sjabloon verwijzing](/azure/templates/microsoft.network/expressroutecircuits)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de [vereisten](expressroute-prerequisites.md) en [werk stromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Zorg ervoor dat u gemachtigd bent om nieuwe netwerk bronnen te maken. Neem contact op met uw account beheerder als u niet over de juiste machtigingen beschikt.
* U kunt [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

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

   * **SKU-laag** bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [Premium](expressroute-faqs.md#expressroute-premium)is. U kunt *lokaal*, * standaard of *Premium*opgeven.
   * De **SKU-familie** bepaalt het facturerings type. U kunt *Metereddata* opgeven voor een data-abonnement met data limiet en *Unlimiteddata* voor een onbeperkt data-abonnement. U kunt het facturerings type wijzigen van *Metereddata* in *Unlimiteddata*, maar u kunt het type niet wijzigen van *Unlimiteddata* in *Metereddata*. Een *lokaal* circuit is alleen *Unlimiteddata* .
   * De **locatie van peering** is de fysieke locatie waar u met micro soft bent gepeerd.

     > [!IMPORTANT]
     > De locatie van de peering geeft de [fysieke locatie](expressroute-locations.md) aan waar u met micro soft bent gepeerd. Deze is **niet** gekoppeld aan de eigenschap Location, die verwijst naar de geografische locatie waar de Azure-netwerk resource provider zich bevindt. Hoewel ze niet gerelateerd zijn, is het een goed idee om een netwerk resource provider geografisch dicht bij de locatie van het circuit te kiezen.

    De naam van de resource groep is de naam van de service bus-naam ruimte waaraan **RG** is toegevoegd.

2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechter muisknop op de shell-console en selecteer vervolgens **Plakken**.

Het duurt enkele minuten om een Event Hub te maken.

Azure PowerShell wordt gebruikt voor het implementeren van de sjabloon in deze zelf studie. Zie voor andere implementatie methoden voor sjablonen:

* Met [behulp van de Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Met [behulp van Azure cli](../azure-resource-manager/templates/deploy-cli.md).
* Met [behulp van rest API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

U kunt het ExpressRoute-circuit verwijderen door het pictogram **verwijderen** te selecteren. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de inrichtings status van de ExpressRoute-circuit service provider is **ingericht** of **ingericht** , moet u samen met uw service provider de inrichting van het circuit aan hun zijde opheffen. We blijven resources reserveren en u factureren totdat de service provider de inrichting van het circuit ongedaan heeft gemaakt en ons op de hoogte stelt.
* Als de service provider het circuit heeft ontsteld (de inrichtings status van de service provider is ingesteld op **niet ingericht**), kunt u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U kunt uw ExpressRoute-circuit verwijderen door de volgende Power shell-opdracht uit te voeren:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt, gaat u door met de volgende stappen:

* [Route ring voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Uw virtuele netwerk koppelen aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
