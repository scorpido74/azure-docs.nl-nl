---
title: Azure front-deur-taak verdeling met de leverings suite van Azure. Microsoft Docs
description: Dit artikel helpt u bij het aanwijzen van de belasting verdeling met de Application Delivery Suite van Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875279"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Taakverdeling realiseren met de Azure-suite voor toepassingslevering

## <a name="introduction"></a>Inleiding
Microsoft Azure biedt meerdere globale en regionale Services voor het beheren van de manier waarop uw netwerk verkeer wordt gedistribueerd en taak verdeling: Traffic Manager, front deur, Application Gateway en Load Balancer.  Samen met de vele regio's en zonegebonden-architectuur van Azure kunt u met deze services tegelijk robuuste, schaal bare toepassingen met hoge prestaties bouwen.

![Application Delivery Suite ][1]
 
Deze services zijn onderverdeeld in twee categorieën:
1. **Wereld wijde taakverdelings Services** zoals Traffic Manager en de voor deur distribueren verkeer van uw eind gebruikers over uw regionale back-ends, in verschillende Clouds of zelfs uw hybride on-premises Services. Met globale taak verdeling wordt uw verkeer naar uw dichtstbijzijnde service back-end gerouteerd en worden de wijzigingen in de betrouw baarheid of prestaties van de service gecommuniceerd om permanente en maximale prestaties voor uw gebruikers te behouden. 
2. **Regionale taakverdelings Services** zoals Standard Load Balancer of Application Gateway bieden de mogelijkheid om verkeer in virtuele netwerken (VNETs) over uw virtuele machines (vm's) of zonegebonden service-eind punten binnen een regio te distribueren.

Het combi neren van wereld wijde en regionale Services in uw toepassing biedt een end-to-end betrouw bare, krachtige en veilige manier om verkeer van en naar uw gebruikers te routeren naar uw IaaS-, PaaS-of on-premises Services. In de volgende sectie worden deze services beschreven.

## <a name="global-load-balancing"></a>Globale taak verdeling
**Traffic Manager** voorziet in globale DNS-taak verdeling. Er wordt gekeken naar inkomende DNS-aanvragen en reageert met een gezonde back-end, in overeenstemming met het routerings beleid dat de klant heeft geselecteerd. Opties voor routerings methoden zijn:
- Prestatie routering voor het verzenden van de aanvrager naar de dichtstbijzijnde back-end in het kader van de latentie.
- Prioriteits routering om al het verkeer door te sturen naar een back-end, met andere back-ends als back-ups.
- Gewogen Round Robin-route ring, waarmee verkeer wordt gedistribueerd op basis van de weging die is toegewezen aan elke back-end.
- Geografische route ring om ervoor te zorgen dat aanvragers in specifieke geografische regio's worden omgeleid naar de back-ends die aan deze regio's zijn toegewezen (bijvoorbeeld alle aanvragen van Spanje moeten worden omgeleid naar de Frankrijk-centraal Azure-regio)
- Met subnet routering waarmee u IP-adresbereiken kunt toewijzen aan back-endservers zodat aanvragen die afkomstig zijn van deze, worden verzonden naar de opgegeven back-end (bijvoorbeeld alle gebruikers die verbinding maken vanuit het IP-adres bereik van uw bedrijfs hoofd kantoor)

De client maakt rechtstreeks verbinding met die back-end. Azure Traffic Manager detecteert wanneer een back-end beschadigd is en stuurt de clients vervolgens om naar een ander ongezonde exemplaar. Raadpleeg de documentatie van [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) voor meer informatie over de service.

De **Azure front-deur** biedt dynamische website versnelling (DSA), inclusief algemene http-taak verdeling.  Er wordt gekeken naar binnenkomende HTTP-aanvragen routes naar de dichtstbijzijnde service back-end/regio voor de opgegeven hostnaam, het URL-pad en de geconfigureerde regels.  
De voor deur beëindigt HTTP-aanvragen aan de rand van het netwerk van micro soft en controleert actief om de status van de toepassing of infra structuur te detecteren.  Met de voor deur wordt verkeer altijd gerouteerd naar de snelste en beschik bare back-end. Raadpleeg de voor deur [routerings architectuur](front-door-routing-architecture.md) Details en [methoden voor verkeers routering](front-door-routing-methods.md) voor meer informatie over de service.

## <a name="regional-load-balancing"></a>Regionale taak verdeling
Application Gateway biedt ADC (Application Delivery controller) als een service met verschillende mogelijkheden voor taak verdeling voor laag 7 voor uw toepassing. Hiermee kunnen klanten de productiviteit van webfarms optimaliseren door CPU-intensieve TLS-beëindiging naar de Application Gateway te offloaden. Andere routerings mogelijkheden voor laag 7 zijn onder andere round-robin distributie van inkomend verkeer, sessie affiniteit op basis van cookies, route ring op basis van URL-pad en de mogelijkheid om meerdere websites achter één toepassings gateway te hosten. Application Gateway kunnen worden geconfigureerd als een Internet gerichte gateway, een alleen-interne gateway of een combi natie van beide. Application Gateway is volledig beheerde, schaal bare en Maxi maal beschik bare Azure. Het biedt een uitgebreide verzameling diagnostische gegevens en functies voor logboekregistratie voor betere beheersbaarheid.
Load Balancer is een integraal onderdeel van de Azure SDN-stack, met hoge prestaties en lage latentie Services voor taak verdeling voor alle UDP-en TCP-protocollen. Het beheert binnenkomende en uitgaande verbindingen. U kunt openbare en interne load-balancedeindpunten configureren en regels definiëren om binnenkomende verbindingen toe te wijzen aan back-end-poolbestemmingen met behulp van TCP- en HTTP-opties voor statustesten om de beschikbaarheid van de service te beheren.


## <a name="choosing-a-global-load-balancer"></a>Een globale load balancer kiezen
Bij het kiezen van een globale load balancer tussen Traffic Manager en Azure front deur voor wereld wijde route ring, moet u rekening houden met wat vergelijkbaar is en wat de twee services verschillen.   Beide services bieden
- **Multi-geo-redundantie:** Als een regio uitvalt, wordt verkeer naadloos gerouteerd naar de dichtstbijzijnde regio zonder tussen komst van de eigenaar van de toepassing.
- **Dichtstbijzijnde regio routering:** Verkeer wordt automatisch doorgestuurd naar de dichtstbijzijnde regio

</br>In de volgende tabel worden de verschillen tussen Traffic Manager en Azure front-deur beschreven:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Elk protocol:** Omdat Traffic Manager werkt op de DNS-laag, kunt u elk type netwerk verkeer routeren. HTTP, TCP, UDP, etc. | **Http-versnelling:** Via het verkeer van de voor deur wordt een proxy aan de rand van het netwerk van micro soft verzonden.  Als gevolg hiervan zien HTTP (S)-aanvragen de latentie en doorvoer verbeteringen de latentie voor TLS-onderhandeling verminderen en het gebruik van warme verbindingen van AFD naar uw toepassing.|
|**On-premises route ring:** Met route ring op een DNS-laag loopt verkeer altijd van punt naar punt.  Route ring van uw filiaal naar uw on-premises Data Center kan een direct pad hebben. zelfs op uw eigen netwerk met behulp van Traffic Manager. | **Onafhankelijke schaal baarheid:** Omdat front-deur werkt met de HTTP-aanvraag, kunnen aanvragen voor verschillende URL-paden worden doorgestuurd naar verschillende back-end/regionale service groepen (micro Services) op basis van regels en de status van elke toepassings micro service.|
|**Facturerings indeling:** Facturering op basis van DNS kan worden geschaald met uw gebruikers en voor services met meer gebruikers en voor het verlagen van de kosten bij een hoger gebruik. |**Inline beveiliging:** Met de voor deur kunt u regels, zoals frequentie limiet en IP-ACL, zodanig instellen dat u uw back-ends kunt beveiligen voordat het verkeer uw toepassing bereikt. 

</br>Vanwege de prestaties, operability en beveiligings voordelen voor HTTP-workloads met de voor deur, raden we klanten aan om de voor deur van hun HTTP-workloads te gebruiken.    Traffic Manager en de voor deur kunnen parallel worden gebruikt om al het verkeer voor uw toepassing te leveren. 

## <a name="building-with-azures-application-delivery-suite"></a>Voortbouwend met de leverings suite van Azure Application 
We raden aan dat alle websites, Api's, services geografisch redundant zijn en het verkeer naar de gebruikers van de dichtstbijzijnde (laagste latentie) locatie waar mogelijk naar hen worden overgebracht.  Door services te combi neren van Traffic Manager, front-deur, Application Gateway en Load Balancer kunt u geografisch en zonally redundant bouwen om de betrouw baarheid, schaal en prestaties te maximaliseren.

In het volgende diagram beschrijven we een voorbeeld service die gebruikmaakt van een combi natie van al deze services voor het bouwen van een wereld wijde webservice.   In dit geval heeft de architect Traffic Manager gebruikt voor het routeren van globale back-ends voor bestands-en object levering, terwijl front-deur wordt gebruikt voor het globaal routeren van URL-paden die overeenkomen met het patroon/Store/* naar de service die ze hebben gemigreerd naar App Service terwijl alle andere aanvragen naar regionale toepassings gateways worden gerouteerd.

In de regio voor hun IaaS-service heeft de ontwikkelaar van de toepassing besloten dat alle Url's die overeenkomen met het patroon/images/* worden geleverd vanuit een specifieke groep virtuele machines die verschillen van de rest van de webfarm.

Daarnaast moet de standaard-VM-groep met de dynamische inhoud communiceren met een back-end-data base die wordt gehost op een cluster met hoge Beschik baarheid. De volledige implementatie wordt ingesteld via Azure Resource Manager.

In het volgende diagram ziet u de architectuur van dit scenario:

![Gedetailleerde architectuur van Application Delivery Suite][2] 

> [!NOTE]
> Dit voor beeld is slechts een van de vele mogelijke configuraties van de taakverdelings services die Azure biedt. Traffic Manager, front deur, Application Gateway en Load Balancer kunnen worden gecombineerd en afgestemd op uw behoeften voor taak verdeling. Als bijvoorbeeld TLS/SSL-offload of Layer 7-verwerking niet nodig is, kan Load Balancer worden gebruikt in plaats van Application Gateway.


## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
