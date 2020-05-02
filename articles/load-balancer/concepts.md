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
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 1fc6721ede07100b4a6769eef8893857cbde119d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629992"
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

## <a name="outbound-connections"></a>Uitgaande verbindingen 
Stromen van de back-end-groep naar open bare Ip's worden toegewezen aan de front-end. Azure vertaalt uitgaande verbindingen met het open bare frontend-IP-adres via de uitgaande regel voor taak verdeling. 

Deze configuratie heeft de volgende voor delen:

* Eenvoudige upgrade en nood herstel van services, omdat de front-end dynamisch kan worden toegewezen aan een ander exemplaar van de service.
* Eenvoudig beheer van toegangs beheer lijst (ACL). Acl's die worden weer gegeven als front-end Ip's worden niet gewijzigd wanneer Services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd. Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan computers vermindert de last van het implementeren van lijsten met veilige geadresseerden.

Zie voor meer informatie over het gebruik van bron netwerkadres omzetting (SNAT) en Azure Load Balancer [SNAT en Azure Load Balancer](load-balancer-outbound-connections.md#snat).

## <a name="availability-zones"></a>Beschikbaarheidszones

Standaard load balancer ondersteunt extra mogelijkheden in regio's waar Beschikbaarheidszones beschikbaar zijn. Deze functies zijn incrementeel voor alle standaard load balancer biedt.  Beschikbaarheidszones configuraties zijn beschikbaar voor beide typen standaard load balancer; openbaar en intern.


Een zone-redundante front-front-front-end verkeert de zone storing door de toegewezen infra structuur in alle zones tegelijk te gebruiken. Daarnaast kunt u een frontend garanderen voor een specifieke zone. 

Een zonegebonden-front-end wordt geleverd door een toegewezen infra structuur in één zone. Taak verdeling in meerdere zones is beschikbaar voor de back-end-groep. Alle virtuele-machine bronnen in een virtueel netwerk kunnen deel uitmaken van een back-end-groep.

Basis load balancer biedt geen ondersteuning voor zones.
Raadpleeg de [gedetailleerde beschrijving van Beschikbaarheidszones gerelateerde vaardig heden](load-balancer-standard-availability-zones.md) en [Beschikbaarheidszones overzicht](../availability-zones/az-overview.md) voor meer informatie.

## <a name="ha-ports"></a>HA-poorten

U kunt de regels voor belasting verdeling van HA configureren om de schaal van uw toepassing te schalen en zeer betrouwbaar te maken. 

De taak verdeling per stroom op korte poorten van het interne load balancer frontend-IP-adres wordt door deze regels verschaft.

De functie is nuttig wanneer het niet praktisch is of niet wenselijk is om afzonderlijke poorten op te geven. Met de regel HA-poort kunt u actieve, passieve of actieve n + 1-scenario's maken. Deze scenario's gelden voor virtuele netwerk apparaten en voor alle toepassingen, waarvoor grote bereiken van binnenkomende poorten nodig zijn. Een status test kan worden gebruikt om te bepalen welke back-ends nieuwe stromen moeten ontvangen.  U kunt een netwerk beveiligings groep gebruiken om een scenario met een poort bereik te emuleren. 

Basis load balancer ondersteunt geen HA-poorten. [Gedetailleerde bespreking van ha-poorten](load-balancer-ha-ports-overview.md) bekijken

>[!IMPORTANT]
> Als u van plan bent om een virtueel netwerk apparaat te gebruiken, raadpleegt u uw leverancier voor meer informatie over of het product is getest met HA-poorten en volgt u de specifieke richt lijnen voor de implementatie. 

## <a name="multiple-frontends"></a>Meerdere frontends

De Load Balancer ondersteunt meerdere regels met meerdere frontends.  Standard Load Balancer breidt deze mogelijkheid uit naar uitgaande scenario's. 

Uitgaande regels zijn de inverse van een regel voor binnenkomende verbindingen. Met de regel voor uitgaand verkeer wordt een koppeling voor uitgaande verbindingen gemaakt. Standaard load balancer alle frontends gebruikt die zijn gekoppeld aan een virtuele-machine bron via een taakverdelings regel.

Daarnaast kunt u met een para meter voor de taakverdelings regel een taakverdelings regel voor de doel einden van de uitgaande connectiviteit onderdrukken, waardoor de selectie van specifieke front-ends, inclusief geen, wordt toegestaan.

Voor een vergelijking selecteert basis load balancer één front-end wille keurig. Er is geen mogelijkheid om te bepalen welke frontend is geselecteerd.

## <a name="limitations"></a><a name = "limitations"></a>Beperkingen

- Sku's zijn niet onveranderbaar. U kunt de SKU van een bestaande resource niet wijzigen.
- Een zelfstandige resource voor de virtuele machine, de resource van de beschikbaarheidsset of de virtuele-machine schaalset kan verwijzen naar één SKU, nooit beide.
- Een load balancer regel kan niet twee virtuele netwerken omvatten.  Front-ends en hun bijbehorende back-end-instanties moeten zich in hetzelfde virtuele netwerk bevinden.  
- Het [verplaatsen van abonnements bewerkingen](../azure-resource-manager/management/move-resource-group-and-subscription.md) wordt niet ondersteund voor standaard Load Balancer en open bare IP-resources.
- Web Worker-rollen zonder een virtueel netwerk en andere micro soft-platform Services kunnen toegankelijk zijn vanaf exemplaren achter alleen een interne standaard load balancer. Vertrouw niet op deze toegankelijkheid, omdat de betreffende service zelf of het onderliggende platform zonder kennisgeving kan worden gewijzigd. Als uitgaande connectiviteit vereist is bij het gebruik van een standaard interne load balancer, moet [uitgaande connectiviteit](load-balancer-outbound-connections.md) worden geconfigureerd.
- De Load Balancer biedt taak verdeling en poort door sturen voor specifieke TCP-of UDP-protocollen. Taakverdelings regels en binnenkomende NAT-regels ondersteunen TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.

  De Load Balancer sluit, reageert of anderszins niet met de payload van een UDP-of TCP-stroom. De Load Balancer werkt niet als een proxy. 
  
  Er moet een geslaagde verbinding met een front-end plaatsvinden. Deze verbinding moet overeenkomen met de poort die wordt gebruikt in een taak verdeling of binnenkomende NAT-regel. Als u een reactie van de front-end wilt zien, moet één virtuele machine in de back-end-pool reageren.

  Als een reactie van de front-end niet kan worden ontvangen, betekent dit dat er geen virtuele machines kunnen reageren. Er kan niet worden gereageerd zonder een virtuele machine om te communiceren met een load balancer front-end. 
  
  Dit principe geldt ook voor uitgaande verbindingen waarbij SNAT voor poort masker alleen wordt ondersteund voor TCP en UDP. Andere IP-protocollen, waaronder ICMP, mislukken. 
  
  Wijs een openbaar IP-adres aan de bron toe om dit probleem op te lossen. Zie [Wat is SNAT en Pat](load-balancer-outbound-connections.md#snat)? voor meer informatie.

- Interne load balancers vertalen geen uitgaande oorspronkelijke verbindingen met de front-end van een interne load balancer, omdat beide zich in een privé-IP-adres ruimte bevinden. Open bare load balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen. Voor interne load balancers vermijdt deze benadering de potentiële SNAT-poort uitputting binnen een unieke interne IP-adres ruimte, waarbij omzetting niet vereist is.

  Uitgaande stroom van een back-end-VM naar een front-end van een interne load balancer mislukt. De fout treedt op wanneer de stroom wordt toegewezen aan zichzelf. De twee zijden van de stroom komen niet overeen en de stroom mislukt.
  
  Wanneer de stroom wordt teruggekoppeld aan zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end.

  De stroom slaagt als deze niet is gekoppeld aan dezelfde virtuele machine in de back-end waarmee de stroom is gemaakt.
  
  De binnenkomende en uitgaande delen van de stroom komen niet overeen in de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en het doel komen niet overeen. De virtuele machine kan reageren wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end. De helften voor de stroom overeenkomst en de verbinding kan worden vervolgd.

  Het symptoom voor dit scenario is periodieke verbindingstime-outs. Veelvoorkomende tijdelijke oplossingen omvatten het invoegen van een proxy laag achter de interne load balancer en het gebruik van regels voor het door sturen van direct server return-stijl (DSR). Zie [meerdere front-ends voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.

  U kunt een intern load balancer combi neren met elke proxy van derden. Het gebruik van interne [Application Gateway](../application-gateway/application-gateway-introduction.md) voor proxy SCENARIO'S met http/https is ook beschikbaar. U kunt een open bare load balancer gebruiken om dit probleem te verhelpen, maar het resulterende scenario is gevoelig voor de [uitputting](load-balancer-outbound-connections.md#snat)van de SNAT. Vermijd deze tweede benadering, tenzij u deze zorgvuldig beheert.

- Over het algemeen worden IP-fragmenten die worden doorgestuurd, niet ondersteund in regels voor taak verdeling. IP-fragmentatie van UDP-en TCP-pakketten wordt niet ondersteund in regels voor taak verdeling. De taakverdelings regels voor HA-poorten kunnen worden gebruikt voor het door sturen van bestaande IP-fragmenten. Zie overzicht van poorten met [hoge Beschik baarheid](load-balancer-ha-ports-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer: Maak een virtuele machine met een aangepaste IIS-extensie die is geïnstalleerd en taak verdeling van de web-app tussen de vm's.
- Meer informatie over [Azure Load Balancer uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Standard Load Balancer diagnostische gegevens](load-balancer-standard-diagnostics.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
