---
title: 'Azure ExpressRoute: klassieke VNets migreren naar Resource Manager'
description: Op deze pagina wordt beschreven hoe u virtuele netwerken met ExpressRoute-gekoppelde netwerken na het verplaatsen van uw circuit migreren naar Resource Manager.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061307"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migreren met ExpressRoute gekoppelde virtuele netwerken van klassiek naar Resource Manager

In dit artikel wordt uitgelegd hoe u virtuele netwerken met ExpressRoute migreert van het klassieke implementatiemodel naar het Azure Resource Manager-implementatiemodel nadat u uw ExpressRoute-circuit hebt verplaatst. 

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Controleer of u de nieuwste versies van de Azure PowerShell-modules hebt. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie. Zie [De Azure PowerShell Service Management Module installeren](/powershell/azure/servicemanagement/install-azure-ps)als u de PowerShell Service Management-module (die nodig is voor het klassieke implementatiemodel) wilt installeren.
* Zorg ervoor dat u de [vereisten,](expressroute-prerequisites.md) [routeringsvereisten](expressroute-routing.md)en [werkstromen](expressroute-workflows.md) hebt gecontroleerd voordat u met de configuratie begint.
* Bekijk de informatie die wordt verstrekt onder [Het verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md). Zorg ervoor dat u de grenzen en beperkingen volledig begrijpt.
* Controleer of het circuit volledig operationeel is in het klassieke implementatiemodel.
* Controleer of u een resourcegroep hebt die is gemaakt in het implementatiemodel resourcebeheer.
* Bekijk de volgende documentatie over resourcemigratie:

    * [Door platforms ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Veelgestelde vragen: door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [De meest voorkomende migratiefouten en -oplossingen bekijken](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Ondersteunde en niet-ondersteunde scenario's

* Een ExpressRoute-circuit kan zonder downtime van de klassieke naar de Resource Manager-omgeving worden verplaatst. U elk ExpressRoute-circuit verplaatsen van de klassieke naar de Resource Manager-omgeving zonder downtime. Volg de instructies in [het verplaatsen van ExpressRoute-circuits van de klassieke naar het Resource Manager-implementatiemodel met PowerShell.](expressroute-howto-move-arm.md) Dit is een vereiste om resources te verplaatsen die zijn aangesloten op het virtuele netwerk.
* Virtuele netwerken, gateways en bijbehorende implementaties binnen het virtuele netwerk die zijn gekoppeld aan een ExpressRoute-circuit in hetzelfde abonnement, kunnen zonder downtime worden gemigreerd naar de Resource Manager-omgeving. U de later beschreven stappen volgen om bronnen zoals virtuele netwerken, gateways en virtuele machines die binnen het virtuele netwerk worden geïmplementeerd, te migreren. U moet ervoor zorgen dat de virtuele netwerken correct zijn geconfigureerd voordat ze worden gemigreerd. 
* Virtuele netwerken, gateways en bijbehorende implementaties binnen het virtuele netwerk die niet in hetzelfde abonnement zitten als het ExpressRoute-circuit vereisen enige downtime om de migratie te voltooien. In het laatste gedeelte van het document worden de stappen beschreven die moeten worden gevolgd om resources te migreren.
* Een virtueel netwerk met zowel ExpressRoute Gateway als VPN Gateway kan niet worden gemigreerd.
* ExpressRoute circuit cross-abonnement migratie wordt niet ondersteund. Zie [Ondersteuning voor verplaatsen van Microsoft.Network voor](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork)meer informatie .

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Een ExpressRoute-circuit verplaatsen van klassiek naar Resource Manager
U moet een ExpressRoute-circuit verplaatsen van de klassieke naar de Resource Manager-omgeving voordat u probeert resources te migreren die zijn gekoppeld aan het ExpressRoute-circuit. Zie de volgende artikelen om deze taak uit te voeren:

* Bekijk de informatie die wordt verstrekt onder [Het verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md).
* [Verplaats een circuit van klassiek naar Resource beheer met Azure PowerShell](expressroute-howto-move-arm.md).
* Gebruik de Azure Service Management-portal. U de werkstroom volgen om [een nieuw ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) te maken en de importoptie selecteren. 

Deze bewerking houdt geen downtime in. U gegevens blijven overdragen tussen uw lokalen en Microsoft terwijl de migratie aan de gang is.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Virtuele netwerken, gateways en bijbehorende implementaties migreren

De stappen die u volgt om te migreren, zijn afhankelijk van of uw resources in hetzelfde abonnement, verschillende abonnementen of beide zitten.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Virtuele netwerken, gateways en bijbehorende implementaties migreren in hetzelfde abonnement als het ExpressRoute-circuit
In deze sectie worden de stappen beschreven die moeten worden gevolgd om een virtueel netwerk, gateway en bijbehorende implementaties te migreren in hetzelfde abonnement als het ExpressRoute-circuit. Er is geen downtime in verband met deze migratie. U alle resources blijven gebruiken tijdens het migratieproces. Het beheervlak is vergrendeld terwijl de migratie aan de gang is. 

1. Zorg ervoor dat het ExpressRoute-circuit is verplaatst van de klassieke naar de Resource Manager-omgeving.
2. Zorg ervoor dat het virtuele netwerk op de juiste manier is voorbereid op de migratie.
3. Registreer uw abonnement voor bronmigratie. Als u uw abonnement wilt registreren voor bronmigratie, gebruikt u het volgende PowerShell-fragment:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Valideren, voorbereiden en migreren. Als u het virtuele netwerk wilt verplaatsen, gebruikt u het volgende PowerShell-fragment:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   U migratie ook afbreken door de volgende PowerShell-cmdlet uit te voeren:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Volgende stappen
* [Door platforms ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Veelgestelde vragen: door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [De meest voorkomende migratiefouten en -oplossingen bekijken](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
