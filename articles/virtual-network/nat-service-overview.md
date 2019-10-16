---
title: Wat is de Azure NAT-service?
titlesuffix: Azure NAT
description: Overzicht van de functies, architectuur en implementatie van de Azure NAT-service. Meer informatie over de werking van de NAT-service en hoe u deze kunt gebruiken in de Cloud.
services: nat
documentationcenter: na
author: asudbring
ms.service: nat
Customer intent: As an IT administrator, I want to learn more about the Azure NAT service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2019
ms.author: allensu
ms.openlocfilehash: 04464716384c1700571096443963499b0ca1b261
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376502"
---
# <a name="what-is-azure-nat-service-public-preview"></a>Wat is Azure NAT-service (open bare preview)

U kunt Azure NAT-service gebruiken om uitgaande verbindingen te bieden voor open bare IP-adres doelen voor alle virtuele machines in uw virtuele netwerk. De NAT-service kan verschillende soorten werk belastingen bevatten door vertalingen op aanvraag te leveren zonder voorplanning per exemplaar vraag.

U kunt definiëren welke subnetten van een virtueel netwerk NAT-service moet gebruiken. U kunt een uitgaande verbinding met een of meer IP-adressen schalen. En u kunt de time-out voor inactiviteit aanpassen van 4 tot 120 minuten. De NAT-service verzendt ook altijd TCP-opnieuw instellen wanneer inactieve verbindingen opnieuw worden gebruikt. Dit is het geval nadat de time-out voor inactiviteit is bereikt. 

U kunt [Beschikbaarheidszones](../availability-zones/az-overview.md) scenario's maken door de NAT-service in een specifieke beschikbaarheids zone te plaatsen.

NAT-service is een beheerde, flexibele service voor uitgaande verbindingen. U kunt de NAT-service combi neren met standaard resources met een openbaar IP-adres en standaard load balancer voor het maken van extra inkomende scenario's.

>[!NOTE] 
>De Azure NAT-service is op dit moment beschikbaar als open bare preview. Het is momenteel alleen beschikbaar in een beperkt aantal [regio's](#regions). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.comsupport/legal/preview-supplemental-terms).

## <a name="nat-gateways"></a>NAT-gateways

Azure NAT-service biedt een NAT-gateway resource. Deze bron kan worden geconfigureerd met een of meer open bare IP-adressen met behulp van een [openbaar IP-adres](../virtual-network/virtual-network-ip-addresses-overview-arm.md#standard) of [standaard open bare IP-prefix](../virtual-network/public-ip-address-prefix.md) bronnen of beide. U gebruikt de NAT-service door subnetten van een virtuele netwerk bron te configureren voor het gebruik van een specifieke NAT-gateway. Een NAT-gateway kan worden gebruikt op meerdere subnetten van hetzelfde virtuele netwerk. Verschillende virtuele netwerken hebben afzonderlijke NAT-gateways nodig.

Een subnet begint met het gebruik van de NAT-gateway voor uitgaande verbindingen wanneer deze is geconfigureerd voor een subnet van een virtueel netwerk. De NAT-gateway vervangt de standaard Internet bestemming van een subnet en de configuratie van een UDR is niet nodig. Uitgaande vertalingen worden op aanvraag gegeven als virtuele machines maken.

>[!IMPORTANT]
>Uitgaande stromen worden pas uitgevoerd als u ook ten minste één openbaar IP-adres aan de NAT-gateway toewijst.

>[!NOTE]
> Als u een gegarandeerde virtuele machine toewijzing nodig hebt of granulariteit nodig hebt voor het definiëren van uitgaande verbindingen op een instantie niveau of groepen van exemplaren of het garanderen van bron Network Address Translation (SNAT)-poorten naar een specifiek exemplaar, gebruikt u [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) met [Uitgaande regels](../load-balancer/load-balancer-outbound-rules-overview.md) voor het definiëren van Load Balancer op groep gebaseerde uitgaande bron Network Address Translation (SNAT).   Deze flexibiliteit biedt aanzienlijk meer complexiteit en meer planning als verschillende werk belastingen worden gecombineerd of als de uitgaande verbinding van de werk belastingen niet nauw keurig kan worden aangepast aan een afzonderlijk exemplaar.

## <a name="combination-of-inbound-and-outbound-scenarios-and-skus"></a>Combi natie van inkomende en uitgaande scenario's en Sku's

U kunt inkomend en uitgaand scenario's maken op dezelfde subnetten met NAT-gateway door NAT-gateway te combi neren met een openbaar IP-adres om toegang te krijgen tot virtuele en standaard open bare load balancer-eind punten, inclusief services op basis van deze. De NAT-gateway en het standaard open bare IP-adres en het standaard open bare load balancer zijn stroom richting. Voor uitgaande stromen worden alle andere uitgaande scenario's door de NAT-gateway vervangen door [Load Balancer uitgaande](../load-balancer/load-balancer-outbound-connections.md) en [open bare IP-adressen op exemplaar niveau op virtuele machines](../load-balancer/load-balancer-outbound-connections.md) . Voor binnenkomende stromen kunnen open bare IP-adressen op exemplaar niveau of open bare eind punten op een load balancer tegelijk met de NAT-gateway worden gebruikt.  Als dit scenario is voltooid, worden inkomende/uitgaande stromen uitgevoerd op dezelfde subnetten.  Het resulterende scenario is standaard ' veilig ', omdat er een NSG is vereist en dat binnenkomend verkeer expliciet mag worden toegestaan.

U kunt uw implementaties in een virtueel netwerk opsplitsen in subnetten om het uitgaande gedrag te beïnvloeden. U kunt ook open bare IP-adressen gebruiken als open bare IP-adressen op exemplaar niveau op virtuele machines en load balancer uitgaande bron Network Address Translation (SNAT) voor uitgaande verbindingen op één subnet en NAT-gateway op een ander subnet van de hetzelfde virtuele netwerk. U kunt geen NAT-gateways combi neren met resources met behulp van basis-open bare IP-adres bronnen of basis load balancer resources in hetzelfde subnet. U kunt de basis-SKU-resources implementeren op een ander subnet van hetzelfde virtuele netwerk.

## <a name="network-address-translation"></a>Netwerkadresomzetting

Zodra de configuratie is voltooid, biedt de NAT-gateway een poort maskering bron Network Address Translation (SNAT) voor alle TCP-en UDP-toepassingen door het privé IP-adres van een exemplaar van een virtuele machine te vertalen naar open bare IP-adressen.

Stromen van privé-IP-adressen worden omgezet naar een of meer open bare IP-adressen op basis van uw configuratie. De vertaling treedt op omdat de bron van de privé-IP-adres bron Network Address Translation (SNAT) is gewijzigd in een openbaar IP-adres. Poort masker betekent dat de bron poort opnieuw wordt geschreven om stromen na de omzetting te onderscheiden en een veel-op-veel-toewijzing te vereenvoudigen. Het bron poort nummer voor de resulterende stroom in open bare IP-adres ruimte wordt op aanvraag toegewezen als virtuele bron machines stromen starten. Meerdere verbindingen met dezelfde bestemming voor het open bare IP-adres, het IP-protocol en de doel poort gebruiken een extra poort per stroom.

Wanneer virtuele machines uitgaande verbindingen maken op deze subnetten, worden bron poorten op aanvraag gegeven en vrijgegeven nadat de stroom is voltooid of wanneer de time-out voor inactiviteit is bereikt. Elke open bare IP biedt 55.000 SNAT-poorten die moeten worden gebruikt en u kunt uitschalen met behulp van meerdere open bare IP-adressen. De NAT-gateway probeert poorten opnieuw te gebruiken voor stromen naar verschillende locaties om de uitgaande verbindings schaal verder te verhogen. 

U kunt eenvoudig meerdere werk belastingen binnen hetzelfde subnet adresseren.  In tegens telling tot de uitgaande connectiviteit van Azure Load Balancer hoeft u het ergste scenario voor het verbruik van SNAT-poorten van een virtuele machine niet vooraf toe te wijzen of voor te bereiden. In plaats daarvan plant u het totale volume van uitgaande verbindingen voor alle virtuele machines in geconfigureerde subnetten. Elke beschik bare poort wordt zo nodig gedeeld voor alle exemplaren.

## <a name="timeout-and-tcp-resets"></a>Time-out en TCP opnieuw instellen

De NAT-gateway implementeert time-outs voor inactiviteit en verzendt TCP-opnieuw ingestelde (RST) voor stromen die niet bestaan.  Time-outs kunnen worden geconfigureerd van 4 minuten (standaard) tot 120 minuten. Een eerste TCP-pakket wordt geretourneerd naar de bron wanneer deze arriveert op de NAT-gateway en er bestaat geen overeenkomende verbinding. Wanneer een stroom een time-out voor inactiviteit heeft bereikt, wordt de stroom verwijderd van de NAT-gateway en wordt de poort beschikbaar voor de volgende stroom die tot stand is gebracht. Als een TCP-verbinding bijvoorbeeld een time-out voor inactiviteit heeft bereikt en er extra pakketten zijn gezien voor een time-out van een verbinding, wordt een eerste TCP-bericht naar de bron verzonden.  Uw toepassing kan TCP-keepalives gebruiken om te Signa leren en zo nodig synchronisatie van de eindpunt status op te geven.

## <a name="availability-zones"></a>Beschikbaarheidszones

NAT-gateways kunnen, indien nodig, in een specifieke beschikbaarheids zone worden geplaatst.  

Een openbaar IP-adres van zonegebonden moet overeenkomen met dezelfde beschikbaarheids zone als de NAT-gateway. Open bare IP-voor voegsel biedt geen ondersteuning voor beschikbaarheids zones en kan niet worden gebruikt.

Wanneer een zonegebonden NAT-gateway is geconfigureerd, wordt het gegevens vlak van de NAT-gateway uitgelijnd en geïsoleerd naar de aangevraagde beschikbaarheids zone.

U kunt het gegevens vlak van de NAT-gateway uitlijnen met een virtuele machine in een specifieke zone.  U kunt deze uitlijning doen door een regionaal subnet te maken dat alleen virtuele machines in dezelfde zone bevat als de NAT-gateway die voor het subnet is geconfigureerd.

De plaatsing van de beschikbaarheids zone van een NAT-gateway kan niet worden gewijzigd en u kunt een NAT-gateway niet converteren van regionaal naar zonegebonden of zonegebonden naar regionaal.

## <a name="outbound-connection-service-comparison"></a>Vergelijking van uitgaande verbindings Services

De NAT-gateway en Load Balancer uitgaande connectiviteit zijn bedoeld voor verschillende scenario's. Eenvoud, toewijzing op aanvraag en schaal van virtueel netwerk op niveau versus groeps gerichte, per exemplaar, en complexere scenario's zijn de belangrijkste zaken die u moet overwegen.  Er zijn ook een aantal andere gedrags verschillen en plannings overwegingen.

De NAT-service is een eenvoudigere manier om uitgaande connectiviteit voor virtuele netwerken te configureren in plaats van op groep gebaseerde configuratie voor [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) met behulp van [Uitgaande regels](../load-balancer/load-balancer-outbound-rules-overview.md).  U kunt de uitgaande connectiviteit eenvoudig configureren en schalen voor een subnet en scenario's die niet eerder mogelijk zijn, bereiken.

De NAT-service heeft een hogere prioriteit dan load balancer uitgaande SNAT, maar beide kunnen worden gecombineerd voor een inkomend en uitgaand scenario. Als u inkomende scenario's combineert met open bare IP-adressen en load balancer-eind punten, kunt u ook gebruikmaken van ' standaard beveiligd ' voor het standaard open bare IP-adres en standaard load balancers.  Alleen wanneer er een NSG bestaat en verkeer expliciet is toegestaan, kan binnenkomend verkeer worden toegestaan om te stromen.

De NAT-service kan voorspel bare, uitgaande, alleen-Verlaag bare connectiviteit voor uw virtuele netwerk maken. Als u alleen een uitgaande verbinding nodig hebt, hoeft u geen load balancer met uitgaande regels te configureren, machines aan de groep toe te voegen, de toewijzing van de SNAT-poort te berekenen en te definiëren, of om open bare IP-adressen toe te wijzen aan afzonderlijke machines. U kunt de verantwoordelijkheid voor uitgaande connectiviteit overdragen aan de beheerder van de NAT-gateway bron in plaats van de beheerder van de load balancer.

Met NAT-service kunt u uitgaande connectiviteit plannen voor de schaal van piek werkbelastingen van een virtueel netwerk of subnetten binnen, in plaats van de schaal van het virtuele machine-exemplaar te schalen. U kunt extra vraag gemak bieden door het aantal open bare IP-adressen dynamisch te verhogen voor alle subnetten van het hele virtuele netwerk. In plaats van load balancer de toewijzing van de SNAT-poort te berekenen en opnieuw te plannen, deelt NAT-service de SNAT-poorten die beschikbaar zijn voor uitgaande verbindingen en kunnen ze op aanvraag worden voorzien van burstie of niet eenvoudig voorspel bare werk belastingen.  Met Load Balancer wordt een specifiek aantal SNAT-poorten voor een bepaald exemplaar van een virtuele machine toegewezen.  

NAT-service retourneert altijd TCP Reset-pakketten (RST) naar de afzender voor niet-bestaande stromen (bijvoorbeeld vanwege een time-out voor inactiviteit). Standaard load balancer kan worden geconfigureerd om [TCP reset in](../load-balancer/load-balancer-tcp-reset.md)te scha kelen op niet-actief, waardoor TCP eerste pakketten worden gemaakt voor beide eind punten van een verbinding op het moment van de time-out voor inactiviteit.

## <a name="regions"></a>Regio's

De NAT-service is beschikbaar in deze regio's
- US - oost 2
- VS - west-centraal

## <a name="limitations"></a>Beperkingen

- De NAT-service is niet compatibel met basis-open bare IP-of Basic load balancers in hetzelfde subnet.  Ze moeten zich bevinden in een subnet dat niet wordt geleverd door een NAT-gateway.
- TCP-en UDP-toepassings protocollen worden ondersteund.
- NAT-gateways kunnen worden geconfigureerd op een of meer subnetten van een virtueel netwerk.
- IPv6 wordt niet ondersteund.
- Het voor voegsel openbaar IP-adres biedt geen ondersteuning voor zonegebonden-plaatsing en kan niet worden gebruikt met een zonegebonden NAT-gateway.

## <a name="next-steps"></a>Volgende stappen

- [Feedback geven over het NAT-service schema](https://aka.ms/natuservoice) .
