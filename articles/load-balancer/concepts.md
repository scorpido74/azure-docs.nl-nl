---
title: Azure Load Balancer-concepten
description: Een overzicht van Azure Load Balancer-concepten
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: cb8b3b58f1029a722121f491d202e245300d1aee
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801009"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer-concepten

Load Balancer biedt verschillende mogelijkheden voor zowel UDP- als TCP-toepassingen. 

## <a name="load-balancing-algorithm"></a>Algoritme voor taakverdeling
U kunt een taakverdelingsregel maken om verkeer van de front-end naar een back-endpool te distribueren. Azure Load Balancer gebruikt een hash-algoritme voor de distributie van binnenkomende stromen (niet bytes). Load Balancer herschrijft de headers van stromen naar instanties van back-endpools. Er is een server beschikbaar voor het ontvangen van nieuwe stromen als een statustest aangeeft dat het back-endeindpunt in orde is.

Standaard gebruikt Load Balancer een hash van vijf tuples.

De hash bevat:

- **IP-adres van bron**
- **Bronpoort**
- **IP-adres van doel**
- **Doelpoort**
- **IP-protocolnummer voor het toewijzen van stromen aan beschikbare servers**

Affiniteit met een IP-bronadres wordt gemaakt met behulp van een hash met twee of drie tuples. Pakketten van dezelfde stroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast. 

Wanneer de client een nieuwe stroom initieert vanaf hetzelfde bron-IP, wordt de bronpoort gewijzigd. Als gevolg hiervan kan de hash van vijf tuples ertoe leiden dat het verkeer naar een ander back-endeindpunt gaat.
Zie [De distributiemodus configureren voor Azure Load Balancer](./load-balancer-distribution-mode.md) voor meer informatie.

Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

  ![Hash-distributie](./media/load-balancer-overview/load-balancer-distribution.png)

  *Afbeelding: Hash-distributie*

## <a name="application-independence-and-transparency"></a>Toepassingsonafhankelijkheid en -transparantie

Load Balancer communiceert niet rechtstreeks met TCP of UDP of met de toepassingslaag. Elk TCP- of UDP-toepassingsscenario kan worden ondersteund. Load Balancer sluit of start geen stromen en communiceert ook niet met de nettolading van de stroom. Load Balancer biedt geen gatewayfunctionaliteit voor de toepassingslaag. Protocol-handshakes worden altijd direct uitgevoerd tussen de client en de instantie van de back-endpool. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.

* Elk eindpunt wordt beantwoord door een virtuele machine. Zo treedt een TCP-handshake bijvoorbeeld op tussen de client en de geselecteerde back-end-VM. Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end valideert, valideert u de algehele connectiviteit met ten minste één back-end-VM.
* Toepassingsnettoladingen zijn transparant voor de load balancer. Elke UDP- of TCP-toepassing kan worden ondersteund.
* Omdat de load balancer geen interactie heeft met de TCP-nettolading en TLS-offload levert, kunt u uitgebreide versleutelde scenario's maken. Door load balancer te gebruiken, wordt grote scale-out voor TLS-toepassingen mogelijk door de TLS-verbinding op de VM zelf te beëindigen. De sleutelcapaciteit van de TLS-sessie wordt bijvoorbeeld alleen beperkt door het type en het aantal VM's dat u aan de back-endpool toevoegt.

## <a name="load-balancer-terminology"></a>Load Balancer-terminologie
| Concept | Wat betekent dit? | Gedetailleerd document |
| ---------- | ---------- | ----------|
Uitgaande verbindingen | Stromen van de back-endpool naar openbare IP's worden toegewezen aan de front-end. Azure vertaalt uitgaande verbindingen naar het openbare front-end-IP-adres via de uitgaande regel voor taakverdeling. Deze configuratie heeft de volgende voordelen. Eenvoudig upgraden en herstellen na noodgevallen van services, omdat de front-end dynamisch kan worden toegewezen aan een andere instantie van de service. Gemakkelijker beheer van toegangsbeheerlijst (ACL). ACL's die worden uitgedrukt als front-end-IP-adressen, veranderen niet als services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd. Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan er machines zijn, vermindert de belasting van het implementeren van lijsten met veilige ontvangers.| Zie [SNAT en Azure Load Balancer](load-balancer-outbound-connections.md) voor meer informatie over het gebruik van Source Network Address Translation (SNAT) en Azure Load Balancer.
Beschikbaarheidszones | Standard Load Balancer ondersteunt extra mogelijkheden in regio's waar beschikbaarheidszones beschikbaar zijn. Deze functies zijn incrementeel voor alles wat Standard Load Balancer biedt.  Configuraties van beschikbaarheidszones zijn beschikbaar voor beide typen Standard Load Balancer; openbaar en intern. Een zone-redundante front-end overleeft zonestoringen door de toegewezen infrastructuur in alle zones tegelijk te gebruiken. Daarnaast kunt u een front-end garanderen voor een specifieke zone. Een zonegebonden front-end wordt geleverd door een toegewezen infrastructuur in één zone. Taakverdeling in meerdere zones is beschikbaar voor de back-endpool. Elke VM-resource in een virtueel netwerk kan deel uitmaken van een back-endpool. Basic Load Balancer biedt geen ondersteuning voor zones.| Bekijk [gedetailleerde bespreking van aan beschikbaarheidszones gerelateerde vaardigheden](load-balancer-standard-availability-zones.md) en [Overzicht beschikbaarheidszones](../availability-zones/az-overview.md) voor meer informatie.
| HA-poorten | U kunt de regels voor belastingverdeling van HA-poorten configureren om de toepassing te laten schalen en zeer betrouwbaar te maken. Door deze regels wordt taakverdeling per stroom op kortstondige poorten van het front-end-IP-adres van de interne load balancer verschaft. De functie is nuttig wanneer het niet praktisch of niet wenselijk is om afzonderlijke poorten op te geven. Met de regel voor HA-poorten kunt u actief-passieve of actief-actieve n+1-scenario's maken. Deze scenario's gelden voor virtuele netwerkapparaten en voor alle toepassingen waarvoor grote bereiken van binnenkomende poorten nodig zijn. Een statustest kan worden gebruikt om te bepalen welke back-ends nieuwe stromen moeten ontvangen.  U kunt een netwerkbeveiligingsgroep gebruiken om een scenario met een poortbereik te emuleren. Basic Load Balancer biedt geen ondersteuning voor HA-poorten. | Bestudeer [gedetailleerde bespreking van HA-poorten](load-balancer-ha-ports-overview.md)
| Meerdere frontends | Load Balancer ondersteunt meerdere regels met meerdere front-ends.  Standard Load Balancer breidt deze mogelijkheid uit naar uitgaande scenario's. Uitgaande regels zijn het tegenovergestelde van een regel voor binnenkomende verbindingen. De uitgaande regel maakt een koppeling voor uitgaande verbindingen. Standard Load Balancer gebruikt alle front-ends die zijn gekoppeld aan een VM-resource via een taakverdelingsregel. Daarnaast kunt u met een parameter op de taakverdelingsregel een taakverdelingsregel onderdrukken voor uitgaande connectiviteit, wat de selectie van specifieke front-ends, inclusief geen, mogelijk maakt. Ter vergelijking: Basic Load Balancer selecteert willekeurig één front-end. Er is geen mogelijkheid om te bepalen welke front-end wordt geselecteerd.|


## <a name="limitations"></a><a name = "limitations"></a>Beperkingen

- Een regel voor een load balancer kan niet twee virtuele netwerken omvatten.  Front-ends en de bijbehorende back-endinstanties moeten zich in hetzelfde virtuele netwerk bevinden.  

- Webwerkrollen zonder een virtueel netwerk en andere Microsoft-platformservices kunnen toegankelijk zijn vanaf instanties die alleen een interne Standard Load Balancer hebben. Vertrouw niet op deze toegankelijkheid, omdat de betreffende service zelf of het onderliggende platform zonder kennisgeving kan worden gewijzigd. Als uitgaande connectiviteit vereist is bij het gebruik van een interne Standard Load Balancer, moet [uitgaande connectiviteit](load-balancer-outbound-connections.md) worden geconfigureerd.

- Voor deze specifieke TCP- of UDP-protocollen biedt Load Balancer taakverdeling en port forwarding. Taakverdelingsregels en inkomende NAT-regels bieden ondersteuning voor TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.

- Uitgaande stroom van een back-end-VM naar een front-end van een interne Load Balancer mislukt.

- Het doorsturen van IP-fragmenten wordt niet ondersteund voor taakverdelingsregels. IP-fragmentatie van UDP- en TCP-pakketten wordt niet ondersteund in taakverdelingsregels. De taakverdelingsregels voor HA-poorten kunnen worden gebruikt voor het doorsturen van bestaande IP-fragmenten. Zie [Overzicht van poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om een Load balancer te gaan gebruiken: maak eerst een, maak VM's met daarop een aangepaste IIS-extensie geïnstalleerd en pas taakverdeling toe op de web-app tussen de VM's.
- Meer informatie over [Uitgaande verbindingen voor Azure Load Balancer](load-balancer-outbound-connections.md).
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [Statuscontroles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
