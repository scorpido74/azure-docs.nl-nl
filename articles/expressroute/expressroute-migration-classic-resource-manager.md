---
title: 'Azure ExpressRoute: klassieke VNets migreren naar Resource Manager'
description: Deze pagina wordt beschreven hoe u ExpressRoute gekoppelde virtuele netwerken migreren naar Resource Manager na het verplaatsen van uw circuit.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: cherylmc
ms.openlocfilehash: 2b74523f42a1f57805388aa8c60cf1ad5b1d1331
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74080087"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute-gekoppelde virtuele netwerken migreren van klassiek naar Resource Manager

In dit artikel wordt uitgelegd hoe u virtuele netwerken gekoppeld met een ExpressRoute van het klassieke implementatiemodel migreren naar de Azure Resource Manager-implementatiemodel na het verplaatsen van uw ExpressRoute-circuit. 

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Controleer of u de nieuwste versies van de Azure PowerShell-modules hebt. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie. Zie [de Azure PowerShell service management-module installeren](/powershell/azure/servicemanagement/install-azure-ps)voor het installeren van de Power shell-module voor Service beheer (die nodig is voor het klassieke implementatie model).
* Zorg ervoor dat u hebt bekeken de [vereisten](expressroute-prerequisites.md), [routeringsvereisten](expressroute-routing.md), en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Lees de informatie die is opgegeven onder [een ExpressRoute-circuit verplaatsen van klassiek naar Resource Manager](expressroute-move.md). Zorg ervoor dat u volledig inzicht in limieten en beperkingen.
* Controleer of het circuit volledig operationeel zijn in het klassieke implementatiemodel.
* Zorg ervoor dat u hebt een resourcegroep die is gemaakt in het Resource Manager-implementatiemodel.
* Raadpleeg de volgende documentatie voor de resource-migratie:

    * [Platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Veelgestelde vragen: Platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Bekijk de meest voorkomende migratiefouten en oplossingen](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Ondersteunde en niet-ondersteunde scenario 's

* Een ExpressRoute-circuit kan worden verplaatst van het klassieke naar het Resource Manager-omgeving zonder uitvaltijd. U kunt elk ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-omgeving zonder uitvaltijd. Volg de instructies in [ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatiemodel met behulp van PowerShell](expressroute-howto-move-arm.md). Dit is een vereiste voor het verplaatsen van resources die zijn verbonden met het virtuele netwerk.
* Virtuele netwerken, gateways en bijbehorende implementaties binnen het virtuele netwerk die zijn gekoppeld aan een ExpressRoute-circuit in hetzelfde abonnement kunnen worden gemigreerd naar de Resource Manager-omgeving zonder uitvaltijd. U kunt de stappen verderop beschreven voor het migreren van resources zoals virtuele netwerken, gateways en virtuele machines die binnen het virtuele netwerk is geïmplementeerd. U moet ervoor zorgen dat de virtuele netwerken correct zijn geconfigureerd voordat ze worden gemigreerd. 
* Virtuele netwerken, gateways en bijbehorende implementaties binnen het virtuele netwerk die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit vereist enige uitvaltijd om de migratie te voltooien. De laatste sectie van het document beschrijft de stappen worden gevolgd voor het migreren van resources.
* Een virtueel netwerk met zowel ExpressRoute-Gateway en VPN-Gateway kan niet worden gemigreerd.
* Migratie van ExpressRoute-circuit voor meerdere abonnementen wordt niet ondersteund. Zie [ondersteuning voor micro soft. netwerk verplaatsing](../azure-resource-manager/move-support-resources.md#microsoftnetwork)voor meer informatie.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Een ExpressRoute-circuit van klassiek naar Resource Manager verplaatsen
U moet een ExpressRoute-circuit verplaatsen van de klassieke naar de Resource Manager-omgeving voordat u probeert te migreren van resources die zijn gekoppeld aan het ExpressRoute-circuit. Als u wilt deze taak wordt uitgevoerd, Zie de volgende artikelen:

* Lees de informatie die is opgegeven onder [een ExpressRoute-circuit verplaatsen van klassiek naar Resource Manager](expressroute-move.md).
* [Een circuit verplaatsen van klassiek naar Resource Manager met Azure PowerShell](expressroute-howto-move-arm.md).
* Gebruik de Azure Service Management-portal. U kunt de werkstroom voor het volgen [maken van een nieuwe ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en selecteer de optie importeren. 

Met deze bewerking heeft geen betrekking op uitvaltijd. U kunt blijven gegevens over te dragen tussen uw locatie en Microsoft tijdens de migratie uitgevoerd wordt.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migreren van virtuele netwerken, gateways en bijbehorende implementaties

De stappen die u volgt als u wilt migreren, is afhankelijk van of uw resources bevinden zich in hetzelfde abonnement of verschillende abonnementen.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migreren van virtuele netwerken, gateways en bijbehorende implementaties in hetzelfde abonnement bevinden als het ExpressRoute-circuit
In deze sectie beschrijft de stappen worden gevolgd voor het migreren van een virtueel netwerk, gateway en bijbehorende implementaties in hetzelfde abonnement bevinden als het ExpressRoute-circuit. Er is geen uitvaltijd is gekoppeld aan deze migratie. U kunt echter ook doorgaan met alle resources door het migratieproces. Het beheervlak is vergrendeld terwijl de migratie uitgevoerd wordt. 

1. Zorg ervoor dat het ExpressRoute-circuit is verplaatst van het klassieke naar de Resource Manager-omgeving.
2. Zorg ervoor dat het virtuele netwerk is voorbereid op de juiste wijze voor de migratie.
3. Registreer uw abonnement voor de Resourcemigratie. Gebruik de volgende PowerShell-codefragment voor het registreren van uw abonnement voor de Resourcemigratie:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Valideren, voorbereiden en migreren. Gebruik de volgende PowerShell-codefragment voor het verplaatsen van het virtuele netwerk:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   U kunt ook de migratie afbreken door het uitvoeren van de volgende PowerShell-cmdlet:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Volgende stappen
* [Platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Veelgestelde vragen: Platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Bekijk de meest voorkomende migratiefouten en oplossingen](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
