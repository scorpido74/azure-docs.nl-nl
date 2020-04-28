---
title: 'Azure ExpressRoute: werk stromen voor circuit configuratie'
description: Op deze pagina worden de werk stromen weer gegeven voor het configureren van ExpressRoute-circuit en peerings
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.openlocfilehash: e833e20085d7cfd8f727acb394851e96e7e19368
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75864363"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-werkstromen voor circuitinrichting en -statussen
Op deze pagina wordt u begeleid bij het inrichten en routeren van configuratie werk stromen op hoog niveau.

![circuit werk stroom](./media/expressroute-workflows/expressroute-circuit-workflow.png)

In de volgende afbeelding en de bijbehorende stappen worden de taken beschreven voor het inrichten van een ExpressRoute-circuit end-to-end. 

1. Gebruik Power shell om een ExpressRoute-circuit te configureren. Volg de instructies in het artikel [ExpressRoute-circuits maken](expressroute-howto-circuit-classic.md) voor meer informatie.
2. Volg de verbinding van de service provider. Dit proces varieert. Neem contact op met uw connectiviteits provider voor meer informatie over het best Ellen van connectiviteit.
3. Controleer of het circuit is ingericht door de inrichtings status van het ExpressRoute-circuit via Power shell te controleren. 
4. Routerings domeinen configureren. Als uw connectiviteits provider de laag-3 configuratie beheert, wordt route ring voor uw circuit geconfigureerd. Als uw connectiviteits provider alleen Layer 2-Services biedt, moet u route ring configureren volgens de richt lijnen die worden beschreven in de [routerings vereisten](expressroute-routing.md) en [routerings configuratie](expressroute-howto-routing-classic.md) pagina's.
   
   * Persoonlijke Azure-peering inschakelen: Schakel deze peering in om verbinding te maken met Vm's/Cloud Services die zijn geïmplementeerd in virtuele netwerken.

   * Micro soft-peering inschakelen: Schakel dit in om toegang te krijgen tot micro soft onlineservices, zoals Office 365. Alle Azure PaaS-Services zijn toegankelijk via micro soft-peering.
     
     > [!IMPORTANT]
     > U moet ervoor zorgen dat u een afzonderlijke proxy/Edge gebruikt om verbinding te maken met micro soft dan het account dat u voor Internet gebruikt. Het gebruik van dezelfde rand voor zowel ExpressRoute als het internet zorgt voor asymmetrische route ring en veroorzaakt connectiviteits storingen voor uw netwerk.
     > 
     > 
     
     ![werk stromen voor route ring](./media/expressroute-workflows/routing-workflow.png)
5. Virtuele netwerken koppelen aan ExpressRoute-circuits: u kunt virtuele netwerken koppelen aan uw ExpressRoute-circuit. Volg de instructies [om VNets te koppelen](expressroute-howto-linkvnet-arm.md) aan uw circuit. Deze VNets kunnen zich in hetzelfde Azure-abonnement bevindt als het ExpressRoute-circuit of zich in een ander abonnement bevindt.

## <a name="expressroute-circuit-provisioning-states"></a>Inrichtings statussen ExpressRoute-circuit
Elk ExpressRoute-circuit heeft twee statussen:

* Inrichtings status van service provider
* Status

Status staat voor de inrichtings status van micro soft. Deze eigenschap wordt ingesteld op ingeschakeld wanneer u een Expressroute-circuit maakt

De inrichtings status van de connectiviteits provider vertegenwoordigt de status van de zijde van de connectiviteits provider. De naam kan *NotProvisioned*, *Provisioning*of *ingericht*zijn. Het ExpressRoute-circuit moet een ingerichte status hebben in de volg orde voor het configureren van peering.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mogelijke statussen van een ExpressRoute-circuit
In deze sectie wordt een overzicht gegeven van de mogelijke statussen van een ExpressRoute-circuit.

**Tijdstip van aanmaak**

Het ExpressRoute-circuit rapporteert de volgende statussen bij het maken van resources.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Wanneer de connectiviteits provider bezig is met het inrichten van het circuit**

Het ExpressRoute-circuit rapporteert de volgende statussen wanneer de connectiviteits provider bezig is met het inrichten van het circuit.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Wanneer de connectiviteits provider het inrichtings proces heeft voltooid**

Het ExpressRoute-circuit rapporteert de volgende statussen zodra de connectiviteits provider het circuit heeft ingericht.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


**Wanneer de inrichting van de connectiviteits provider het circuit ongedaan maakt**

Als het ExpressRoute-circuit moet worden verwijderd, zal het circuit de volgende statussen rapporteren zodra de service provider het ongedaan maken van de inrichting heeft voltooid.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


U kunt ervoor kiezen om deze indien nodig opnieuw in te scha kelen of Power shell-cmdlets uit te voeren om het circuit te verwijderen.  

> [!IMPORTANT]
> Een circuit kan niet worden verwijderd wanneer de ServiceProviderProvisioningState wordt ingericht of ingericht. De connectiviteits provider moet het circuit ongedaan maken voordat deze kan worden verwijderd. Micro soft blijft het circuit factureren totdat de ExpressRoute-circuit resource in azure wordt verwijderd.
> 

## <a name="routing-session-configuration-state"></a>Configuratie status van routerings sessie
De BGP-inrichtings status rapporten als de BGP-sessie is ingeschakeld op de micro soft Edge. De status moet zijn ingeschakeld om persoonlijke of micro soft-peering te kunnen gebruiken.

Het is belang rijk om de status van de BGP-sessie te controleren met name voor micro soft-peering. Naast de BGP-inrichtings status is er een andere status genaamd *aangekondigde open bare voor voegsels*. De status van de geadverteerde open bare voor voegsels moet in de *geconfigureerde* status zijn, zowel voor de BGP-sessie als voor uw route ring om de eind-tot-eind te kunnen uitvoeren. 

Als de status van de aangekondigde open bare voor voegsel is ingesteld op de status *validatie is vereist* , is de BGP-sessie niet ingeschakeld, omdat de geadverteerde voor voegsels niet overeenkomen met het as-nummer in een van de routerings registers. 

> [!IMPORTANT]
> Als de status aangekondigde open bare voor voegsels zich in *hand matige validatie* status bevindt, moet u een ondersteunings ticket openen met [micro soft-ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en aantonen dat u beschikt over de IP-adressen die zijn geadverteerd samen met het gekoppelde autonome systeem nummer.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Configureer uw ExpressRoute-verbinding.
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een VNet koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

