---
title: 'Azure ExpressRoute: configuratiewerkstromen voor circuit'
description: Op deze pagina worden de werkstromen weergegeven voor het configureren van ExpressRoute-circuits en peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864363"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-werkstromen voor circuitinrichting en -statussen
Deze pagina leidt u door de werkstromen voor het inrichten en routeren van configuratieop hoog niveau.

![circuitwerkstroom](./media/expressroute-workflows/expressroute-circuit-workflow.png)

In de volgende cijfers en bijbehorende stappen worden de taken beschreven die een ExpressRoute-circuit end-to-end moeten inrichten. 

1. Gebruik PowerShell om een ExpressRoute-circuit te configureren. Volg de instructies in het artikel [ExpressRoute-circuits maken](expressroute-howto-circuit-classic.md) voor meer informatie.
2. Bestel connectiviteit bij de serviceprovider. Dit proces varieert. Neem contact op met uw connectiviteitsprovider voor meer informatie over het bestellen van connectiviteit.
3. Zorg ervoor dat het circuit met succes is ingericht door de inrichtingsstatus van het ExpressRoute-circuit via PowerShell te verifiëren. 
4. Routeringsdomeinen configureren. Als uw connectiviteitsprovider de Layer 3-configuratie beheert, configureert deze routering voor uw circuit. Als uw connectiviteitsprovider alleen Layer 2-services aanbiedt, moet u routering configureren volgens de richtlijnen die zijn beschreven in de [routeringsvereisten](expressroute-routing.md) en [routeringsconfiguratiepagina's.](expressroute-howto-routing-classic.md)
   
   * Azure private peering inschakelen - Schakel deze peering in om verbinding te maken met VM's / cloudservices die zijn geïmplementeerd binnen virtuele netwerken.

   * Microsoft-peering inschakelen - Schakel dit in om toegang te krijgen tot onlineservices van Microsoft, zoals Office 365. Alle Azure PaaS-services zijn toegankelijk via Microsoft-peering.
     
     > [!IMPORTANT]
     > U moet ervoor zorgen dat u een aparte proxy / rand gebruikt om verbinding te maken met Microsoft dan degene die u gebruikt voor het internet. Het gebruik van dezelfde rand voor zowel ExpressRoute als internet zal leiden tot asymmetrische routering en verbindingsonderbrekingen voor uw netwerk veroorzaken.
     > 
     > 
     
     ![routeringswerkstromen](./media/expressroute-workflows/routing-workflow.png)
5. Virtuele netwerken koppelen aan ExpressRoute-circuits - U virtuele netwerken koppelen aan uw ExpressRoute-circuit. Volg instructies [om VNets](expressroute-howto-linkvnet-arm.md) aan uw circuit te koppelen. Deze VNets kunnen zich in hetzelfde Azure-abonnement bevinden als het ExpressRoute-circuit, of kunnen een ander abonnement hebben.

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute circuit provisioning staten
Elk ExpressRoute circuit heeft twee staten:

* Status van de voorziening van dienstverleners
* Status

Status vertegenwoordigt de inrichtingsstatus van Microsoft. Deze eigenschap is ingesteld op Ingeschakeld wanneer u een Expressroute-circuit maakt

De status van de connectiviteitsprovider vertegenwoordigt de status aan de kant van de connectiviteitsprovider. Het kan *notprovisioneerd*, *provisioning*, of *ingericht*. Het expressroute-circuit moet in een ingerichte status staan om peering te configureren.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mogelijke toestanden van een ExpressRoute circuit
In deze sectie worden de mogelijke toestanden van een ExpressRoute-circuit weergegeven.

**Op creatietijd**

Het ExpressRoute-circuit rapporteert de volgende toestanden bij het maken van resources.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Wanneer connectiviteitsprovider bezig is met het inrichten van het circuit**

Het ExpressRoute-circuit rapporteert de volgende statussen terwijl de connectiviteitsprovider werkt aan de inrichting van het circuit.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Wanneer de connectiviteitsprovider het inrichtingsproces heeft voltooid**

Het ExpressRoute-circuit rapporteert de volgende staten zodra de connectiviteitsprovider het circuit met succes heeft ingericht.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Wanneer connectiviteitsprovider het circuit deprovisionert**

Als het ExpressRoute-circuit moet worden gedeprovisioneerd, rapporteert het circuit de volgende statussen zodra de serviceprovider het deprovisioningproces heeft voltooid.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


U ervoor kiezen om het indien nodig opnieuw in te schakelen of PowerShell-cmdlets uit te voeren om het circuit te verwijderen.  

> [!IMPORTANT]
> Een circuit kan niet worden verwijderd wanneer de ServiceProviderProvisioningState is provisioning of provisioned. De connectiviteitsprovider moet het circuit deprovisioneren voordat het kan worden verwijderd. Microsoft blijft het circuit factureren totdat de ExpressRoute-circuitbron is verwijderd in Azure.
> 

## <a name="routing-session-configuration-state"></a>Status van configuratie van de routeringssessie
De BGP-status rapporteert als de BGP-sessie is ingeschakeld op de Microsoft-rand. De status moet zijn ingeschakeld om privé- of Microsoft-peering te gebruiken.

Het is belangrijk om de Status van de BGP-sessie te controleren, vooral voor Microsoft-peering. Naast de BGP-provisioningstatus is er een andere status die *geadverteerde openbare voorvoegsels staat*. De geadverteerde openbare voorvoegsels status moet in de *geconfigureerde* status, zowel voor de BGP-sessie te worden en voor uw routing te werken end-to-end. 

Als de geadverteerde openbare voorvoegselstatus is ingesteld op een *validatiestatus* die nodig is, is de BGP-sessie niet ingeschakeld, omdat de geadverteerde voorvoegsels niet overeenkomen met het AS-nummer in een van de routeringsregisters. 

> [!IMPORTANT]
> Als de geadverteerde openbare voorvoegselsstaat in *handmatige validatiestatus* is, moet u een ondersteuningsticket openen met [Microsoft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en het bewijs leveren dat u eigenaar bent van de IP-adressen die worden geadverteerd, samen met het bijbehorende autonome systeemnummer.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

