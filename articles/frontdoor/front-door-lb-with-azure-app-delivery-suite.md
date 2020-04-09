---
title: Azure Front Door - Load Balancing met Azure's application delivery suite | Microsoft Documenten
description: In dit artikel vindt u meer informatie over de manier waarop Azure het balanceren van de toepassing met de toepassingsleveringssuite aanbeveelt
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 44af14a01e7b045b7abb6a84db89a67f3dd22445
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875279"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Taakverdeling realiseren met de Azure-suite voor toepassingslevering

## <a name="introduction"></a>Inleiding
Microsoft Azure biedt meerdere globale en regionale services voor het beheren van de manier waarop uw netwerkverkeer wordt gedistribueerd en in balans wordt gebracht: Traffic Manager, Front Door, Application Gateway en Load Balancer.  Samen met de vele regio's en zonale architectuur van Azure u met behulp van deze services robuuste, schaalbare krachtige toepassingen bouwen.

![Application Delivery Suite ][1]
 
Deze services zijn onderverdeeld in twee categorieën:
1. **Wereldwijde load-balancing services** zoals Traffic Manager en Front Door verdelen het verkeer van uw eindgebruikers over uw regionale backends, over clouds of zelfs uw hybride on-premises services. Wereldwijde taakverdeling leidt uw verkeer naar de dichtstbijzijnde backend van de service en reageert op veranderingen in de betrouwbaarheid of prestaties van de service om de maximale prestaties voor uw gebruikers te behouden. 
2. **Regionale load-balancing services** zoals Standard Load Balancer of Application Gateway bieden de mogelijkheid om verkeer binnen virtuele netwerken (VNET's) te distribueren over uw virtuele machines (VM's) of zonale service eindpunten binnen een regio.

Het combineren van wereldwijde en regionale diensten in uw toepassing biedt een end-to-end betrouwbare, performante en veilige manier om verkeer van en naar uw gebruikers naar uw IaaS-, PaaS- of on-premises services te leiden. In het volgende gedeelte beschrijven we elk van deze services.

## <a name="global-load-balancing"></a>Wereldwijde taakverdeling
**Traffic Manager** biedt globale DNS-taakverdeling. Het kijkt naar inkomende DNS-verzoeken en reageert met een gezonde backend, in overeenstemming met het routeringsbeleid dat de klant heeft geselecteerd. Opties voor routeringsmethoden zijn:
- Prestatieroutering om de aanvrager naar de dichtstbijzijnde backend te sturen in termen van latentie.
- Prioriteitsroutering om al het verkeer naar een backend te leiden, met andere backends als back-up.
- Gewogen round-robin routing, die verkeer verdeelt op basis van de weging die is toegewezen aan elke backend.
- Geografische routering om ervoor te zorgen dat aanvragers in specifieke geografische regio's worden gericht op de backends die naar die regio's zijn toegewezen (bijvoorbeeld alle aanvragen uit Spanje moeten worden gericht op de Regio Frankrijk Centraal Azure)
- Subnetrouting waarmee u IP-adresbereiken toewijzen aan backends, zodat aanvragen die afkomstig zijn van deze worden verzonden naar de opgegeven backend (bijvoorbeeld alle gebruikers die verbinding maken vanuit het IP-adresbereik van uw hoofdkantoor van uw bedrijf, moeten andere webinhoud krijgen dan de algemene gebruikers)

De client maakt rechtstreeks verbinding met die backend. Azure Traffic Manager detecteert wanneer een backend niet in orde is en leidt de clients vervolgens door naar een andere gezonde instantie. Raadpleeg [azure traffic manager-documentatie](../traffic-manager/traffic-manager-overview.md) voor meer informatie over de service.

**Azure Front Door** biedt dynamische websiteversnelling (DSA) inclusief wereldwijde HTTP-taakverdeling.  Er wordt gekeken naar binnenkomende HTTP-aanvragen routes naar de dichtstbijzijnde service backend / regio voor de opgegeven hostname, URL-pad en geconfigureerde regels.  
Front Door beëindigt HTTP-aanvragen aan de rand van het netwerk van Microsoft en sondes actief om wijzigingen in de status of latentie van toepassingen of infrastructuur te detecteren.  Front Door leidt dan altijd het verkeer naar de snelste en beschikbare (gezonde) backend. Raadpleeg de [routeringsarchitectuurdetails](front-door-routing-architecture.md) en [verkeersrouteringsmethoden](front-door-routing-methods.md) van Front Door voor meer informatie over de service.

## <a name="regional-load-balancing"></a>Regionale load balancing
Application Gateway biedt application delivery controller (ADC) als een service, met verschillende Layer 7 load-balancing mogelijkheden voor uw toepassing. Het stelt klanten in staat om de productiviteit van webfarmen te optimaliseren door CPU-intensieve TLS-beëindiging te ontladen naar de toepassingsgateway. Andere Routeringsmogelijkheden van Layer 7 zijn round-robin-distributie van binnenkomend verkeer, sessieaffiniteit op basis van cookies, URL-padgebaseerde routering en de mogelijkheid om meerdere websites achter één toepassingsgateway te hosten. Application Gateway kan worden geconfigureerd als een naar internet gerichte gateway, een interne gateway of een combinatie van beide. Application Gateway is volledig azure beheerd, schaalbaar en zeer beschikbaar. Het biedt een uitgebreide verzameling diagnostische gegevens en functies voor logboekregistratie voor betere beheersbaarheid.
Load Balancer is een integraal onderdeel van de Azure SDN-stack en biedt krachtige, low-latency Layer 4 load-balancing services voor alle UDP- en TCP-protocollen. Het beheert inkomende en uitgaande verbindingen. U kunt openbare en interne load-balancedeindpunten configureren en regels definiëren om binnenkomende verbindingen toe te wijzen aan back-end-poolbestemmingen met behulp van TCP- en HTTP-opties voor statustesten om de beschikbaarheid van de service te beheren.


## <a name="choosing-a-global-load-balancer"></a>Een globale load balancer kiezen
Wanneer u een globale load balancer kiest tussen Traffic Manager en Azure Front Door voor globale routering, moet u overwegen wat vergelijkbaar is en wat er anders is aan de twee services.   Beide diensten bieden
- **Multigeo redundantie:** Als één regio uitvalt, leidt het verkeer naadloos naar de dichtstbijzijnde regio zonder tussenkomst van de eigenaar van de toepassing.
- **Dichtstbijzijnde regioroutering:** Verkeer wordt automatisch doorgestuurd naar de dichtstbijzijnde regio

</br>In de volgende tabel worden de verschillen tussen Traffic Manager en Azure Front Door beschreven:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Elk protocol:** Omdat Traffic Manager op de DNS-laag werkt, u elk type netwerkverkeer routeren. HTTP, TCP, UDP, etc. | **HTTP-versnelling:** Met Front Door verkeer is proxied aan de rand van het netwerk van Microsoft.  Hierdoor worden http(s)-aanvragen latentie- en doorvoerverbeteringen weergegeven die de latentie voor TLS-onderhandeling verminderen en het gebruik van hot-verbindingen van AFD naar uw toepassing.|
|**On-premises routing:** Met routering op een DNS-laag gaat het verkeer altijd van punt naar punt.  Routering van uw filiaal naar uw on-premises datacenter kan een direct pad bewandelen; zelfs op uw eigen netwerk met behulp van Traffic Manager. | **Onafhankelijke schaalbaarheid:** Omdat Front Door werkt met de HTTP-aanvraag, kunnen aanvragen naar verschillende URL-paden worden doorgestuurd naar verschillende backend/ regionale servicepools (microservices) op basis van regels en de status van elke toepassingsmicroservice.|
|**Factureringsindeling:** DNS-gebaseerde facturering schalen met uw gebruikers en voor diensten met meer gebruikers, plateaus om de kosten te verlagen tegen hoger gebruik. |**Inline beveiliging:** Front Door maakt regels zoals tariefbeperking en IP ACL-ing mogelijk om u uw backends te laten beschermen voordat het verkeer uw toepassing bereikt. 

</br>Vanwege de prestaties, operabiliteit en beveiligingsvoordelen voor HTTP-workloads met Front Door, raden we klanten aan Front Door te gebruiken voor hun HTTP-workloads.    Traffic Manager en Front Door kunnen parallel worden gebruikt om al het verkeer voor uw toepassing te bedienen. 

## <a name="building-with-azures-application-delivery-suite"></a>Bouwen met azure's application delivery suite 
We raden aan dat alle websites, API's, services geografisch redundant zijn en waar mogelijk verkeer aan de gebruikers leveren vanaf de dichtstbijzijnde (laagste latentie) locatie.  Door services van Traffic Manager, Front Door, Application Gateway en Load Balancer te combineren, u geografisch en zonig redundant bouwen om de betrouwbaarheid, schaal en prestaties te maximaliseren.

In het volgende diagram beschrijven we een voorbeeldservice die een combinatie van al deze services gebruikt om een wereldwijde webservice te bouwen.   In dit geval heeft de architect Traffic Manager gebruikt om naar globale backends te routeren voor bestands- en objectweergave, terwijl de url-paden van Front Door worden gebruikt om URL-paden wereldwijd te routeren die overeenkomen met het patroon /store/* naar de service die ze naar App Service hebben gemigreerd terwijl alle andere aanvragen worden routerd naar regionale toepassingsgateways.

In de regio heeft de applicatieontwikkelaar voor hun IaaS-service besloten dat alle URL's die overeenkomen met het patroon /afbeeldingen/* worden geserveerd vanuit een speciale groep VM's die verschillen van de rest van de webfarm.

Bovendien moet de standaard VM-groep met de dynamische inhoud praten met een back-enddatabase die wordt gehost op een cluster met hoge beschikbaarheid. De volledige implementatie wordt ingesteld via Azure Resource Manager.

In het volgende diagram ziet u de architectuur van dit scenario:

![Gedetailleerde architectuur voor toepassingsleveringssuite][2] 

> [!NOTE]
> Dit voorbeeld is slechts een van de vele mogelijke configuraties van de load-balancing services die Azure biedt. Traffic Manager, Front Door, Application Gateway en Load Balancer kunnen worden gemengd en afgestemd op uw behoeften op het gebied van taakverdeling. Als TLS/SSL-offload of Layer 7-verwerking bijvoorbeeld niet nodig is, kan Load Balancer worden gebruikt in plaats van Application Gateway.


## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
