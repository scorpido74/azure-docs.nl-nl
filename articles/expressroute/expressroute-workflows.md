---
title: 'Circuit configuratie werk stromen-ExpressRoute: Azure| Microsoft Docs'
description: Deze pagina bevat de werkstromen voor het configureren van ExpressRoute-circuit en -peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 1e0f4d356bb6addf735bf148a80e6be3ca550c8f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123385"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-werkstromen voor circuitinrichting en -statussen
Deze pagina vindt u de inrichting en configuratie van werkstromen op hoog niveau routering-service.

![circuit-werkstroom](./media/expressroute-workflows/expressroute-circuit-workflow.png)

De volgende afbeelding en de bijbehorende stappen wordt de taken die u volgen moet om een ExpressRoute-circuit dat is ingericht end-to-end weergeven. 

1. PowerShell gebruiken voor het configureren van een ExpressRoute-circuit. Volg de instructies in de [maken ExpressRoute-circuits](expressroute-howto-circuit-classic.md) artikel voor meer informatie.
2. De connectiviteit van de volgorde van de serviceprovider. Dit proces is. Neem contact op met uw connectiviteitsprovider voor meer informatie over het bestellen van connectiviteit.
3. Zorg ervoor dat het circuit is ingericht met succes door te controleren of het ExpressRoute-circuit Inrichtingsstatus via PowerShell. 
4. Routeringsdomeinen configureren. Als uw connectiviteitsprovider laag-3 voor u beheert, wordt deze de routering voor uw circuit configureren. Als uw connectiviteitsprovider alleen Layer 2-services biedt, moet u configureren routering per richtlijnen die worden beschreven in de [routeringsvereisten](expressroute-routing.md) en [routeringsconfiguratie](expressroute-howto-routing-classic.md) pagina's.
   
   * Inschakelen van persoonlijke Azure-peering - inschakelen van deze peering voor het verbinding maken met virtuele machines / services die zijn geïmplementeerd in virtuele netwerken in de cloud.

   * Schakel micro soft-peering in om toegang te krijgen tot Office 365. Alle Azure PaaS-services zijn ook toegankelijk via Microsoft-peering.
     
     > [!IMPORTANT]
     > U moet ervoor zorgen dat u gebruikmaakt van een afzonderlijke proxyserver / edge verbinding maken met Microsoft dan de versie die u gebruikt voor het Internet. Met behulp van de rand voor ExpressRoute- en Internet veroorzaakt asymmetrische Routering en connectiviteit onderbrekingen voor uw netwerk veroorzaken.
     > 
     > 
     
     ![Routering werkstromen](./media/expressroute-workflows/routing-workflow.png)
5. Virtuele netwerken koppelen aan ExpressRoute-circuits - kunt u virtuele netwerken koppelen aan uw ExpressRoute-circuit. Volg de instructies [VNets koppelen](expressroute-howto-linkvnet-arm.md) aan uw circuit. Deze vnet's in hetzelfde Azure-abonnement als het ExpressRoute-circuit kan zijn of kunnen zich in een ander abonnement.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute-circuit inrichtingsstatussen
Elk ExpressRoute-circuit heeft twee statussen:

* Serviceprovider-Inrichtingsstatus
* Status

Status vertegenwoordigt de Inrichtingsstatus van Microsoft. Deze eigenschap is ingesteld op ingeschakeld wanneer u een Expressroute-circuit maken

De Inrichtingsstatus van connectiviteit provider vertegenwoordigt de status aan van de connectiviteitsprovider. Het kan zijn *NotProvisioned*, *Provisioning*, of *ingerichte*. Het ExpressRoute-circuit moet zijn ingericht voor u te kunnen gebruiken.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mogelijke statussen van een ExpressRoute-circuit
In deze sectie geeft een lijst van de mogelijke statussen voor een ExpressRoute-circuit.

**Tijdens het maken**

U ziet het ExpressRoute-circuit in de volgende status hebben als u de PowerShell-cmdlet voor het maken van het ExpressRoute-circuit worden uitgevoerd.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Wanneer de connectiviteitsprovider bij het inrichten van het circuit is**

U ziet het ExpressRoute-circuit in de volgende status zodra u de servicesleutel aan de connectiviteitsprovider doorgeven en ze het inrichtingsproces hebt gestart.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Wanneer de connectiviteitsprovider het inrichtingsproces is voltooid**

U ziet het ExpressRoute-circuit in de volgende status hebben, zodra het inrichtingsproces is voltooid door de connectiviteitsprovider.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Ingericht en ingeschakeld is dat de enige staat het circuit, kan zich in te kunnen gebruiken. Als u een Layer 2-provider gebruikt, kunt u de routering voor uw circuit alleen wanneer deze zich in deze status kunt configureren.

**Als connectiviteitsprovider het circuit is inrichting**

Als u de serviceprovider de inrichting van het ExpressRoute-circuit ongedaan maken is aangevraagd, ziet u het circuit ingesteld op de volgende status hebben wanneer de service-provider het opheffen van inrichtingen proces is voltooid.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


U kunt deze opnieuw inschakelen als nodig is en voer de PowerShell-cmdlets voor het verwijderen van het circuit.  

> [!IMPORTANT]
> Als u de PowerShell-cmdlet als u wilt verwijderen van het circuit bij het inrichten van de ServiceProviderProvisioningState of ingericht, de bewerking mislukt uitvoert. Neem contact op met uw connectiviteitsprovider om na te eerst de ExpressRoute-circuit de inrichting en verwijder vervolgens het circuit. Microsoft blijft het circuit factureren totdat u de PowerShell-cmdlet als u wilt verwijderen van het circuit uitvoert.
> 
> 

## <a name="routing-session-configuration-state"></a>Configuratie van routering sessiestatus
De Inrichtingsstatus BGP laat u weten dat als de BGP-sessie is ingeschakeld op de Microsoft edge. De status moet worden ingeschakeld voor u mogelijk gebruik van de peering.

Het is belangrijk om te controleren of de status van de BGP-sessie met name voor Microsoft-peering. Naast het BGP Inrichtingsstatus, er is een andere status met de naam *staat openbare voorvoegsels geadverteerd*. De status van de aangekondigde openbare voorvoegsels moet zich in *geconfigureerd* staat, zowel voor de BGP-sessie zodat u en uw routering om te werken van end-to-end. 

Als de status van de aangekondigde openbare voorvoegsel is ingesteld op een *validatie vereist* status, de BGP-sessie niet is ingeschakeld, zoals de geadverteerde voorvoegsels komt niet overeen met de AS-nummer in een van de routeringsregisters. 

> [!IMPORTANT]
> Als de status aangekondigde openbare voorvoegsels *handmatig worden gevalideerd* staat, moet u een ondersteuningsticket met openen [Microsoft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en bewijzen dat u eigenaar van de IP-adressen die zijn geadverteerd samen met de gekoppelde autonoom systeemnummer.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

