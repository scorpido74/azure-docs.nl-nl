---
title: 'Azure ExpressRoute: werk stromen voor circuit configuratie'
description: Deze pagina bevat de werkstromen voor het configureren van ExpressRoute-circuit en -peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864363"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-werkstromen voor circuitinrichting en circuittoestanden
Deze pagina vindt u de inrichting en configuratie van werkstromen op hoog niveau routering-service.

![circuit-werkstroom](./media/expressroute-workflows/expressroute-circuit-workflow.png)

In de volgende afbeelding en de bijbehorende stappen worden de taken beschreven voor het inrichten van een ExpressRoute-circuit end-to-end. 

1. PowerShell gebruiken voor het configureren van een ExpressRoute-circuit. Volg de instructies in de [maken ExpressRoute-circuits](expressroute-howto-circuit-classic.md) artikel voor meer informatie.
2. De connectiviteit van de volgorde van de serviceprovider. Dit proces is. Neem contact op met uw connectiviteitsprovider voor meer informatie over het bestellen van connectiviteit.
3. Zorg ervoor dat het circuit is ingericht met succes door te controleren of het ExpressRoute-circuit Inrichtingsstatus via PowerShell. 
4. Routeringsdomeinen configureren. Als uw connectiviteits provider de laag-3 configuratie beheert, wordt route ring voor uw circuit geconfigureerd. Als uw connectiviteits provider alleen Layer 2-Services biedt, moet u route ring configureren volgens de richt lijnen die worden beschreven in de [routerings vereisten](expressroute-routing.md) en [routerings configuratie](expressroute-howto-routing-classic.md) pagina's.
   
   * Inschakelen van persoonlijke Azure-peering - inschakelen van deze peering voor het verbinding maken met virtuele machines / services die zijn geïmplementeerd in virtuele netwerken in de cloud.

   * Micro soft-peering inschakelen: Schakel dit in om toegang te krijgen tot micro soft onlineservices, zoals Office 365. Alle Azure PaaS-services zijn toegankelijk via het Microsoft-peering.
     
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

De Inrichtingsstatus van connectiviteit provider vertegenwoordigt de status aan van de connectiviteitsprovider. Het kan zijn *NotProvisioned*, *Provisioning*, of *ingerichte*. Het ExpressRoute-circuit moet een ingerichte status hebben in de volg orde voor het configureren van peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mogelijke statussen van een ExpressRoute-circuit
In deze sectie wordt een overzicht gegeven van de mogelijke statussen van een ExpressRoute-circuit.

**Tijdens het maken**

Het ExpressRoute-circuit rapporteert de volgende statussen bij het maken van resources.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Wanneer de connectiviteitsprovider bij het inrichten van het circuit is**

Het ExpressRoute-circuit rapporteert de volgende statussen wanneer de connectiviteits provider bezig is met het inrichten van het circuit.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Wanneer de connectiviteitsprovider het inrichtingsproces is voltooid**

Het ExpressRoute-circuit rapporteert de volgende statussen zodra de connectiviteits provider het circuit heeft ingericht.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Als connectiviteitsprovider het circuit is inrichting**

Als het ExpressRoute-circuit moet worden verwijderd, zal het circuit de volgende statussen rapporteren zodra de service provider het ongedaan maken van de inrichting heeft voltooid.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


U kunt deze opnieuw inschakelen als nodig is en voer de PowerShell-cmdlets voor het verwijderen van het circuit.  

> [!IMPORTANT]
> Een circuit kan niet worden verwijderd wanneer de ServiceProviderProvisioningState wordt ingericht of ingericht. De connectiviteits provider moet het circuit ongedaan maken voordat deze kan worden verwijderd. Micro soft blijft het circuit factureren totdat de ExpressRoute-circuit resource in azure wordt verwijderd.
> 

## <a name="routing-session-configuration-state"></a>Configuratie van routering sessiestatus
De BGP-inrichtings status rapporten als de BGP-sessie is ingeschakeld op de micro soft Edge. De status moet zijn ingeschakeld om persoonlijke of micro soft-peering te kunnen gebruiken.

Het is belangrijk om te controleren of de status van de BGP-sessie met name voor Microsoft-peering. Naast het BGP Inrichtingsstatus, er is een andere status met de naam *staat openbare voorvoegsels geadverteerd*. De status van de geadverteerde open bare voor voegsels moet in de *geconfigureerde* status zijn, zowel voor de BGP-sessie als voor uw route ring om de eind-tot-eind te kunnen uitvoeren. 

Als de status van de aangekondigde openbare voorvoegsel is ingesteld op een *validatie vereist* status, de BGP-sessie niet is ingeschakeld, zoals de geadverteerde voorvoegsels komt niet overeen met de AS-nummer in een van de routeringsregisters. 

> [!IMPORTANT]
> Als de status aangekondigde open bare voor voegsels zich in *hand matige validatie* status bevindt, moet u een ondersteunings ticket openen met [micro soft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en aantonen dat u beschikt over de IP-adressen die zijn geadverteerd samen met het gekoppelde autonome systeem nummer.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

