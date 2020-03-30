---
title: 'Azure ExpressRoute-sjabloon: een ExpressRoute-circuit maken'
description: Maak, indik, verwijder en deprovisioneer een ExpressRoute-circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 11/13/2019
ms.author: cherylmc
ms.reviewer: ganesr
ms.openlocfilehash: 78da84a462566cca1a2800174849159ace8dd6dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981136"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Een ExpressRoute-circuit maken met azure resource manager-sjabloon

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure-CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sjabloon](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-circuit-classic.md)
>

Meer informatie over het maken van een ExpressRoute-circuit door een Azure Resource Manager-sjabloon te implementeren met Azure PowerShell. Zie [Resourcemanager-documentatie](/azure/azure-resource-manager/) en de [sjabloonverwijzing](/azure/templates/microsoft.network/expressroutecircuits)voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.

## <a name="before-you-begin"></a>Voordat u begint

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u met de configuratie begint.
* Zorg ervoor dat u machtigingen hebt om nieuwe netwerkbronnen te maken. Neem contact op met uw accountbeheerder als u niet over de juiste machtigingen beschikt.
* U [een video bekijken](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) voordat u begint om de stappen beter te begrijpen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten

[Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) hebben een goede verzameling resourcebeheersjabloon. U gebruikt een van de [bestaande sjablonen](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) om een ExpressRoute-circuit te maken.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Als u meer gerelateerde sjablonen wilt bekijken, selecteert u [hier](https://azure.microsoft.com/resources/templates/?term=expressroute).

Een ExpressRoute-circuit maken door een sjabloon te implementeren:

1. Selecteer **Probeer het** in het volgende codeblok en volg de instructies om u aan te melden bij de Azure Cloud-shell.

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

   * **De SKU-laag** bepaalt of een ExpressRoute-circuit [lokaal](expressroute-faqs.md#expressroute-local), standaard of [premium](expressroute-faqs.md#expressroute-premium)is. U *Lokaal,* *Standaard* of *Premium*opgeven.
   * **De SKU-familie** bepaalt het factureringstype. U *Datadata* opgeven voor een data-abonnement met data met data met data en *Unlimiteddata* voor een onbeperkt data-abonnement. U het factureringstype wijzigen van *Gemetendata* naar *Unlimiteddata,* maar u het type niet wijzigen van *Unlimiteddata* naar *Gemetendata.* Een *lokaal* circuit is alleen *Unlimiteddata.*
   * **Peering-locatie** is de fysieke locatie waar u peering met Microsoft.

     > [!IMPORTANT]
     > De peeringlocatie geeft de [fysieke locatie](expressroute-locations.md) aan waar u peert met Microsoft. Dit is **niet** gekoppeld aan de eigenschap Locatie, die verwijst naar de geografie waar de Azure Network Resource Provider zich bevindt. Hoewel ze niet gerelateerd zijn, is het een goede gewoonte om een Network Resource Provider te kiezen die geografisch dicht bij de peeringlocatie van het circuit ligt.

    De naam van de resourcegroep is de naamruimtenaam servicebus met **rg** toegevoegd.

2. Selecteer **Kopiëren** om het PowerShell-script te kopiëren.
3. Klik met de rechtermuisknop op de shell console en selecteer **Plakken**.

Het duurt even om een gebeurtenishub te maken.

Azure PowerShell wordt gebruikt om de sjabloon in deze zelfstudie te implementeren. Zie voor andere methoden voor het implementeren van sjablonen:

* [Door gebruik te maken van de Azure-portal](../azure-resource-manager/templates/deploy-portal.md).
* [Door Azure CLI te gebruiken](../azure-resource-manager/templates/deploy-cli.md).
* [Door rest API te gebruiken.](../azure-resource-manager/templates/deploy-rest.md)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Deprovisioning and deleting an ExpressRoute circuit (Inrichting van een ExpressRoute-circuit ongedaan maken en het circuit verwijderen)

U uw ExpressRoute-circuit verwijderen door het **pictogram Verwijderen te** selecteren. Let op de volgende informatie:

* U moet alle virtuele netwerken loskoppelen van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of virtuele netwerken zijn gekoppeld aan het circuit.
* Als de ExpressRoute-dienstverlener provisioning state is **Provisioning** or **Provisioned,** moet u samenwerken met uw serviceprovider om het circuit aan hun kant te deprovisioneren. We blijven resources reserveren en u factureren totdat de serviceprovider het circuit heeft voltooid en ons hiervan op de hoogte stelt.
* Als de serviceprovider het circuit heeft gedeprovisioneerd (de status van de serviceprovider is ingesteld op **Niet-ingericht),** u het circuit verwijderen. Hiermee wordt ook de facturering voor het circuit gestopt.

U uw ExpressRoute-circuit verwijderen door de volgende PowerShell-opdracht uit te voeren:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw circuit hebt gemaakt, gaat u verder met de volgende stappen:

* [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-portal-resource-manager.md)
* [Koppel uw virtuele netwerk aan uw ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)
