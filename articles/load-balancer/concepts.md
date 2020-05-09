---
title: Azure Load Balancer concepten
description: Overzicht van Azure Load Balancer concepten
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
ms.openlocfilehash: 2e2e11e1dc16fd99a82bc2fbdc7b72399bc6ae97
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871921"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer concepten

De Load Balancer biedt verschillende mogelijkheden voor UDP-en TCP-toepassingen. 

## <a name="load-balancing-algorithm"></a>Algoritme voor taakverdeling
U kunt een taakverdelings regel maken om verkeer van de front-end naar een back-end-groep te distribueren. Azure Load Balancer gebruikt een hash-algoritme voor de distributie van binnenkomende stromen (niet bytes). De Load Balancer herschrijft de headers van stromen naar back-end-pool instanties. Er is een server beschikbaar om nieuwe stromen te ontvangen wanneer een status test een gezonde back-end-eind punt aangeeft.

Standaard gebruikt Load Balancer een hash van vijf Tuples.

De hash bevat:

- **IP-adres van bron**
- **Bron poort**
- **IP-adres van doel**
- **Doel poort**
- **IP-protocol nummer voor het toewijzen van stromen aan beschik bare servers**

Affiniteit met een IP-bron adres wordt gemaakt met behulp van een hash met twee of drie Tuples. Pakketten met dezelfde stroom arriveren op hetzelfde exemplaar achter de front-end met gelijke taak verdeling. 

De bron poort wordt gewijzigd wanneer een client een nieuwe stroom start van hetzelfde bron-IP-adres. Als gevolg hiervan kan de vijf-tuple-hash ertoe leiden dat het verkeer naar een ander back-end-eind punt gaat.
Zie [de distributie modus configureren voor Azure Load Balancer](./load-balancer-distribution-mode.md)voor meer informatie.

Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

  ![Hash-distributie](./media/load-balancer-overview/load-balancer-distribution.png)

  *Afbeelding: hash-distributie*

## <a name="application-independence-and-transparency"></a>Toepassings onafhankelijkheid en-transparantie

De Load Balancer communiceert niet rechtstreeks met TCP of UDP of met de toepassingslaag. Elk TCP-of UDP-toepassings scenario kan worden ondersteund. De Load Balancer sluit geen stromen af of heeft geen invloed op de nettolading van de stroom. De Load Balancer biedt geen gateway functionaliteit voor de toepassingslaag. Protocol-Handshakes worden altijd direct uitgevoerd tussen de client en het exemplaar van de back-end-pool. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.

* Elk eind punt wordt beantwoord door een virtuele machine. Er wordt bijvoorbeeld een TCP-handshake uitgevoerd tussen de client en de geselecteerde back-end-VM. Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end hebt gevalideerd, valideert u de connectiviteit in ten minste één virtuele machine van de back-end.
* Toepassings payloads zijn transparant voor de load balancer. Elke UDP-of TCP-toepassing kan worden ondersteund.
* Omdat de load balancer geen interactie heeft met de TCP-nettolading en TLS-offload levert, kunt u uitgebreide versleutelde scenario's maken. Het gebruik van load balancer grote hoeveel heden uitschalen voor TLS-toepassingen door de TLS-verbinding op de VM zelf te beëindigen. Uw TLS-sessie sleutel kan bijvoorbeeld alleen worden beperkt door het type en het aantal Vm's dat u toevoegt aan de back-end-pool.

## <a name="load-balancer-terminology"></a>Load Balancer terminologie
| Concept | Wat betekent dit? | Gedetailleerd document |
| ---------- | ---------- | ----------|
Uitgaande verbindingen | Stromen van de back-end-groep naar open bare Ip's worden toegewezen aan de front-end. Azure vertaalt uitgaande verbindingen met het open bare frontend-IP-adres via de uitgaande regel voor taak verdeling. Deze configuratie heeft de volgende voor delen. Eenvoudige upgrade en nood herstel van services, omdat de front-end dynamisch kan worden toegewezen aan een ander exemplaar van de service. Eenvoudig beheer van toegangs beheer lijst (ACL). Acl's die worden weer gegeven als front-end Ip's worden niet gewijzigd wanneer Services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd. Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan computers vermindert de last van het implementeren van lijsten met veilige geadresseerden.| Zie voor meer informatie over het gebruik van bron netwerkadres omzetting (SNAT) en Azure Load Balancer [SNAT en Azure Load Balancer](load-balancer-outbound-connections.md#snat).
Beschikbaarheidszones | Standaard load balancer ondersteunt extra mogelijkheden in regio's waar Beschikbaarheidszones beschikbaar zijn. Deze functies zijn incrementeel voor alle standaard load balancer biedt.  Beschikbaarheidszones configuraties zijn beschikbaar voor beide typen standaard load balancer; openbaar en intern. Een zone-redundante front-front-front-end verkeert de zone storing door de toegewezen infra structuur in alle zones tegelijk te gebruiken. Daarnaast kunt u een frontend garanderen voor een specifieke zone. Een zonegebonden-front-end wordt geleverd door een toegewezen infra structuur in één zone. Taak verdeling in meerdere zones is beschikbaar voor de back-end-groep. Alle virtuele-machine bronnen in een virtueel netwerk kunnen deel uitmaken van een back-end-groep. Basis load balancer biedt geen ondersteuning voor zones.| Raadpleeg de [gedetailleerde beschrijving van Beschikbaarheidszones gerelateerde vaardig heden](load-balancer-standard-availability-zones.md) en [Beschikbaarheidszones overzicht](../availability-zones/az-overview.md) voor meer informatie.
| HA-poorten | U kunt de regels voor belasting verdeling van HA configureren om de schaal van uw toepassing te schalen en zeer betrouwbaar te maken. De taak verdeling per stroom op korte poorten van het interne load balancer frontend-IP-adres wordt door deze regels verschaft. De functie is nuttig wanneer het niet praktisch is of niet wenselijk is om afzonderlijke poorten op te geven. Met de regel HA-poort kunt u actieve, passieve of actieve n + 1-scenario's maken. Deze scenario's gelden voor virtuele netwerk apparaten en voor alle toepassingen, waarvoor grote bereiken van binnenkomende poorten nodig zijn. Een status test kan worden gebruikt om te bepalen welke back-ends nieuwe stromen moeten ontvangen.  U kunt een netwerk beveiligings groep gebruiken om een scenario met een poort bereik te emuleren. Basis load balancer ondersteunt geen HA-poorten. | [Gedetailleerde bespreking van ha-poorten](load-balancer-ha-ports-overview.md) bekijken
| Meerdere frontends | De Load Balancer ondersteunt meerdere regels met meerdere frontends.  Standard Load Balancer breidt deze mogelijkheid uit naar uitgaande scenario's. Uitgaande regels zijn de inverse van een regel voor binnenkomende verbindingen. Met de regel voor uitgaand verkeer wordt een koppeling voor uitgaande verbindingen gemaakt. Standaard load balancer alle frontends gebruikt die zijn gekoppeld aan een virtuele-machine bron via een taakverdelings regel. Daarnaast kunt u met een para meter voor de taakverdelings regel een taakverdelings regel voor de doel einden van de uitgaande connectiviteit onderdrukken, waardoor de selectie van specifieke front-ends, inclusief geen, wordt toegestaan. Voor een vergelijking selecteert basis load balancer één front-end wille keurig. Er is geen mogelijkheid om te bepalen welke frontend is geselecteerd.|


## <a name="limitations"></a><a name = "limitations"></a>Beperkingen

- Een load balancer regel kan niet twee virtuele netwerken omvatten.  Front-end-en back-end-exemplaren moeten zich in hetzelfde virtuele netwerk bevinden.  

- Webwerkers zonder een virtueel netwerk en andere micro soft-platform Services kunnen toegankelijk zijn vanaf exemplaren die alleen een standaard interne Load Balancer hebben. Vertrouw niet op deze toegankelijkheid, omdat de betreffende service zelf of het onderliggende platform zonder kennisgeving kan worden gewijzigd. Als uitgaande connectiviteit vereist is bij het gebruik van een standaard interne load balancer, moet [uitgaande connectiviteit](load-balancer-outbound-connections.md) worden geconfigureerd.

- De Load Balancer biedt taak verdeling en poort door sturen voor specifieke TCP-of UDP-protocollen. Taakverdelings regels en binnenkomende NAT-regels ondersteunen TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.

- Uitgaande stroom van een back-end-VM naar een front-end van een interne Load Balancer mislukt.

- Het door sturen van IP-fragmenten wordt niet ondersteund voor taakverdelings regels. IP-fragmentatie van UDP-en TCP-pakketten wordt niet ondersteund in regels voor taak verdeling. De taakverdelings regels voor HA-poorten kunnen worden gebruikt voor het door sturen van bestaande IP-fragmenten. Zie overzicht van poorten met [hoge Beschik baarheid](load-balancer-ha-ports-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer: Maak een virtuele machine met een aangepaste IIS-extensie die is geïnstalleerd en taak verdeling van de web-app tussen de vm's.
- Meer informatie over [Azure Load Balancer uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Standard Load Balancer diagnostische gegevens](load-balancer-standard-diagnostics.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
