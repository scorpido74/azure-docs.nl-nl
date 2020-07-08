---
title: 'ExpressRoute: circuits van het klassieke naar het Azure Resource Manager verplaatsen'
description: Overzicht van het overbruggen van het klassieke en het Resource Manager-implementatie model.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: how-to
ms.date: 12/07/2018
ms.author: ganesr
ms.openlocfilehash: b6fb087e0faecc51b44b5b796fed29756356e669
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84738816"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatiemodel
Dit artikel bevat een overzicht van wat het betekent om een Azure ExpressRoute-circuit te verplaatsen van het klassieke naar het Azure Resource Manager-implementatiemodel.

U kunt één ExpressRoute-circuit gebruiken om verbinding te maken met virtuele netwerken die zijn geïmplementeerd in het klassieke en het Resource Manager-implementatiemodel. Een ExpressRoute-circuit kan nu, ongeacht de manier waarop deze is gemaakt, via beide implementatiemodellen worden gekoppeld aan virtuele netwerken.

![Een ExpressRoute-circuit dat via beide implementatiemodellen wordt gekoppeld aan virtuele netwerken](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>ExpressRoute-circuits die zijn gemaakt in het klassieke implementatiemodel
ExpressRoute-circuits die zijn gemaakt in het klassieke implementatiemodel moeten eerst naar het Resource Manager-implementatiemodel worden verplaatst om connectiviteit met zowel het klassieke als het Resource Manager-implementatiemodel in te schakelen. Er is geen connectiviteitsonderbreking of -verlies wanneer een verbinding wordt verplaatst. Alle koppelingen van circuit naar virtueel netwerk in het klassieke implementatiemodel (binnen hetzelfde abonnement en overlappend abonnement) blijven behouden.

Wanneer de verbinding is verplaatst, werkt het ExpressRoute-circuit exact zoals een ExpressRoute-circuit dat is gemaakt in het Resource Manager-implementatiemodel. Nu kunt u verbinding maken met virtuele netwerken in het Resource Manager- implementatiemodel.

Wanneer een ExpressRoute-circuit is verplaatst naar het Resource Manager-implementatiemodel, kunt u de levenscyclus van het ExpressRoute-circuit alleen beheren met het Resource Manager-implementatiemodel. Dit betekent dat u handelingen, zoals het toevoegen, bijwerken, verwijderen van peerings, het bijwerken van circuiteigenschappen (zoals bandbreedte, SKU en factureringstype) en het bijwerken en verwijderen van circuits alleen kunt uitvoeren in het Resource Manager-implementatiemodel. Raadpleeg onderstaande sectie over circuits die zijn gemaakt in het Resource Manager-implementatiemodel, voor meer informatie over hoe u de toegang tot beide implementatiemodellen kunt beheren.

U hebt uw connectiviteitsprovider niet nodig om de verplaatsing uit te voeren.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatiemodel
U kunt ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatiemodel inschakelen voor toegang vanuit beide implementatiemodellen. Elk ExpressRoute-circuit in uw abonnement kan worden ingeschakeld voor toegang vanuit beide implementatiemodellen.

* ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatiemodel hebben standaard geen toegang tot het klassieke implementatiemodel.
* ExpressRoute-circuits die van het klassieke implementatiemodel zijn verplaatst naar Resource Manager-implementatiemodel zijn standaard toegankelijk vanuit beide implementatiemodellen.
* Een ExpressRoute-circuit heeft altijd toegang tot het Resource Manager-implementatiemodel, ongeacht of het is gemaakt in het Resource Manager- of klassieke implementatiemodel. Dat betekent dat u virtuele netwerken die in het Resource Manager-implementatiemodel zijn gemaakt, kunt koppelen door de instructies te volgen in [how to link virtual networks](expressroute-howto-linkvnet-arm.md) (Virtuele netwerken koppelen).
* De toegang tot het klassieke implementatiemodel wordt gecontroleerd met de parameter **allowClassicOperations** in het ExpressRoute-circuit.

> [!IMPORTANT]
> Alle quota die zijn beschreven op de pagina [service limits](../azure-resource-manager/management/azure-subscription-service-limits.md) (Servicelimieten) zijn van toepassing. Zo kan een standaardcircuit maximaal 10 virtuele netwerkkoppelingen/-verbindingen tussen zowel het klassieke als het Resource Manager-implementatiemodel hebben.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Toegang tot het klassieke implementatiemodel beheren
U kunt één ExpressRoute-circuit inschakelen om te worden gekoppeld aan virtuele netwerken in beide implementatiemodellen, door de parameter **allowClassicOperations** van het ExpressRoute-circuit in te stellen.

Wanneer u **allowClassicOperations** instelt op TRUE, kunt u virtuele netwerken vanuit beide implementatiemodellen koppelen aan het ExpressRoute-circuit. U kunt een koppeling maken met een virtueel netwerk in het klassieke implementatiemodel door de richtlijnen te volgen in [how to link virtual networks in the classic deployment model](expressroute-howto-linkvnet-classic.md) (Een virtueel netwerk koppelen in het klassieke implementatiemodel). U kunt een koppeling maken met een virtueel netwerk in het Resource Manager-implementatiemodel door de richtlijnen te volgen in [how to link virtual networks in the Resource Manager deployment model](expressroute-howto-linkvnet-arm.md) (Een virtueel netwerk koppelen in het Resource Manager-implementatiemodel).

Wanneer u **allowClassicOperations** instelt op FALSE, wordt de toegang tot het circuit vanuit het klassieke implementatiemodel geblokkeerd. Alle koppelingen met virtuele netwerken in het klassieke implementatiemodel blijven echter behouden. In dit geval is het ExpressRoute-circuit niet zichtbaar in het klassieke implementatiemodel.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Ondersteunde bewerkingen in het klassieke implementatiemodel
De volgende klassieke bewerkingen worden ondersteund in een ExpressRoute-circuit als **allowClassicOperations** is ingesteld op TRUE:

* ExpressRoute-circuitgegevens verkrijgen
* Koppelingen tussen virtuele netwerken en klassieke virtuele netwerken maken, bijwerken, verkrijgen en verwijderen
* Autorisaties voor koppelingen met virtuele netwerken voor abonnementoverschrijdende connectiviteit maken, bijwerken, verkrijgen en verwijderen

Als **allowClassicOperations** echter is ingesteld op True, kunt u de volgende klassieke bewerkingen niet uitvoeren:

* BGP-peerings (Border Gateway Protocol) maken, bijwerken, verkrijgen of verwijderen voor persoonlijke of openbare Azure-peerings en Microsoft-peerings
* ExpressRoute-circuits verwijderen

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Communicatie tussen het klassieke en het Resource Manager-implementatiemodel
Het ExpressRoute-circuit fungeert als een brug tussen het klassieke en het Resource Manager-implementatiemodel. Verkeer tussen virtuele machines in virtuele netwerken in het klassieke implementatiemodel en die in virtuele netwerken in het Resource Manager-implementatiemodel stroomt via ExpressRoute als beide virtuele netwerken zijn gekoppeld aan hetzelfde ExpressRoute-circuit.

Cumulatieve doorvoer wordt beperkt door de doorvoercapaciteit van de gateway van het virtueel netwerk. Verkeer komt de netwerken van de connectiviteitsprovider of uw netwerken in dergelijke gevallen niet binnen. De verkeersstroom tussen de virtuele netwerken vindt volledig plaats in het Microsoft-netwerk.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Toegang tot resources in openbare Azure-peering en Microsoft-peering
U blijft zonder onderbreking toegang houden tot resources die doorgaans toegankelijk zijn via openbare Azure-peering en Microsoft-peering.  

## <a name="whats-supported"></a>Wat wordt er ondersteund
In deze sectie wordt beschreven wat er wordt ondersteund voor ExpressRoute-circuits:

* U kunt één ExpressRoute-circuit gebruiken voor toegang tot virtuele netwerken die zijn geïmplementeerd in het klassieke en het Resource Manager-implementatiemodel.
* U kunt een ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-implementatiemodel Wanneer het is verplaatst, werkt het ExpressRoute-circuit net als elk ander ExpressRoute-circuit dat is gemaakt in het Resource Manager-implementatiemodel.
* U kunt alleen het ExpressRoute-circuit verplaatsen. Met deze bewerking kunt u geen circuitkoppelingen, virtuele netwerken of VPN-gateways verplaatsen.
* Wanneer een ExpressRoute-circuit is verplaatst naar het Resource Manager-implementatiemodel, kunt u de levenscyclus van het ExpressRoute-circuit alleen beheren met het Resource Manager-implementatiemodel. Dit betekent dat u handelingen, zoals het toevoegen, bijwerken, verwijderen van peerings, het bijwerken van circuiteigenschappen (zoals bandbreedte, SKU en factureringstype) en het bijwerken en verwijderen van circuits alleen kunt uitvoeren in het Resource Manager-implementatiemodel.
* Het ExpressRoute-circuit fungeert als een brug tussen het klassieke en het Resource Manager-implementatiemodel. Verkeer tussen virtuele machines in virtuele netwerken in het klassieke implementatiemodel en die in virtuele netwerken in het Resource Manager-implementatiemodel stroomt via ExpressRoute als beide virtuele netwerken zijn gekoppeld aan hetzelfde ExpressRoute-circuit.
* Abonnementoverschrijdende connectiviteit wordt ondersteund in zowel het klassieke als het Resource Manager-implementatiemodel.
* Nadat u een ExpressRoute-circuit uit het klassieke model naar het model van Azure Resource Manager hebt verplaatst, kunt u [de virtuele netwerken die zijn gekoppeld aan het ExpressRoute-circuit migreren](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Wat wordt er niet ondersteund
In deze sectie wordt beschreven wat er niet wordt ondersteund voor ExpressRoute-circuits:

* Het beheer van de levenscyclus van een ExpressRoute-circuit vanuit het klassieke implementatiemodel.
* RBAC-ondersteuning (Role-Based Access Control - op rollen gebaseerd toegangsbeheer) voor het klassieke implementatiemodel. U kunt geen RBAC-besturing uitvoeren voor een circuit in het klassieke implementatiemodel. Een beheerder/co-beheerder van het abonnement kan virtuele netwerken koppelen aan of loskoppelen van het circuit.

## <a name="configuration"></a>Configuratie
Volg de instructies in [Een ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-implementatiemodel](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Volgende stappen
* [De virtuele netwerken die zijn gekoppeld aan het ExpressRoute-circuit uit het klassieke model migreren naar het model van Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Voor informatie over werkstromen raadpleegt u [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Werkstromen voor de inrichting van ExpressRoute-circuits en circuittoestanden).
* Ga als volgt te werk om uw ExpressRoute-verbinding te configureren:
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

