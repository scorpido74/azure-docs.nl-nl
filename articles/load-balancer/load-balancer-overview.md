---
title: Wat is Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer-functies, -architectuur en -implementatie. Informatie over hoe de Load Balancer werkt en hou u deze kunt gebruiken in de cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: allensu
ms.openlocfilehash: 335549f4ccae01fa36921e0e4668fa15e8b33835
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423903"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

Met Azure Load Balancer kunt u uw toepassingen schalen en hoge beschikbaarheid voor uw services realiseren. Load Balancer biedt ondersteuning voor scenario's met inkomend en uitgaand verkeer, biedt lage latentie en hoge doorvoer en kan omhoog worden geschaald tot miljoenen stromen voor alle TCP- en UDP-toepassingen.

Load Balancer distribueert nieuwe inkomende stromen die aankomen op de front-end van de Load Balancer voor een back-end-pool, volgens opgegeven regels en status controles.

Een open bare Load Balancer kan uitgaande verbindingen bieden voor virtuele machines (Vm's) in uw virtuele netwerk door hun privé-IP-adressen te vertalen naar open bare IP-adressen.

Azure Load Balancer is beschikbaar in twee prijs categorieën of *sku's*: Basic en Standard. Er zijn verschillen in schaal, functies en prijzen. Elk scenario dat mogelijk is met basis Load Balancer, kan ook worden gemaakt met Standard Load Balancer, hoewel de benaderingen enigszins verschillen. Lees voor meer informatie over Load Balancer de basis principes en de SKU-specifieke verschillen.

## <a name="why-use-load-balancer"></a>Waarom zou ik Load Balancer moeten gebruiken?

U kunt Azure Load Balancer voor het volgende gebruiken:

* Taak verdeling van binnenkomend Internet verkeer naar uw Vm's. Deze configuratie wordt een [open bare Load Balancer](#publicloadbalancer)genoemd.
* Taak verdeling van verkeer tussen virtuele machines in een virtueel netwerk. In een hybride scenario kunt u een front-end van een Load Balancer ook bereiken vanaf een on-premises netwerk. In beide scenario's wordt een configuratie gebruikt die een [interne Load Balancer](#internalloadbalancer)wordt genoemd.
* Voor Port Forwarding-verkeer naar een specifieke poort op specifieke virtuele machines met regels voor binnenkomende NAT-regels (Network Address Translation).
* Voor het leveren van [uitgaande connectiviteit](load-balancer-outbound-connections.md) voor VM's binnen uw virtuele netwerk door gebruik te maken van een openbare Load Balancer.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Zie [Wat is Azure-toepassing gateway?](../application-gateway/overview.md) als u op zoek bent naar Transport Layer Security (TLS) Termination (' SSL offload ') of per HTTP/HTTPS-aanvraag, de verwerking van de toepassingslaag. Als u op zoek bent naar globale DNS-taak verdeling, raadpleegt u [Wat is Traffic Manager?](../traffic-manager/traffic-manager-overview.md) Uw end-to-end-scenario's kunnen van pas komen bij het combi neren van deze oplossingen.
>
> Zie [overzicht van opties voor taak verdeling in azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)voor een vergelijking van Azure-opties voor taak verdeling.

## <a name="what-are-load-balancer-resources"></a>Wat zijn Load Balancer-resources?

Load Balancer resources zijn objecten die aangeven hoe Azure de multi tenant-infra structuur moet Program meren om het scenario te verhalen dat u wilt maken. Er is geen rechtstreekse relatie tussen Load Balancer resources en de werkelijke infra structuur. Als een Load Balancer wordt gemaakt, wordt er geen instantie gemaakt en capaciteit is altijd beschikbaar.

Een Load Balancer resource kan een open bare Load Balancer of een interne Load Balancer zijn. De functies van de Load Balancer resource worden gedefinieerd door een front-end, een regel, een status test en een back-end-pool definitie. U kunt virtuele machines in de back-end-pool plaatsen door de back-end-pool van de virtuele machine op te geven.

## <a name="fundamental-load-balancer-features"></a>Fundamentele functies van Load Balancer

Load Balancer biedt de volgende fundamentele mogelijkheden voor TCP en UDP-toepassingen:

* **Taakverdeling**

  Met Azure Load Balancer kunt u een taakverdelings regel maken voor het distribueren van verkeer dat binnenkomt bij de front-end naar exemplaren van een back-end-pool. Load Balancer gebruikt een hash-algoritme voor de distributie van binnenkomende stromen en herschrijft de headers van stromen naar back-end-pool exemplaren. Er is een server beschikbaar om nieuwe stromen te ontvangen wanneer een status test een gezonde back-end-eind punt aangeeft.

  Load Balancer maakt standaard gebruik van een hash van 5-tuple. De hash omvat het bron-IP-adres, de bron poort, het doel-IP-adres, de doel poort en het IP-protocol nummer voor het toewijzen van stromen aan beschik bare servers. U kunt affiniteit maken met een IP-bron adres door een twee-of 3-tuple-hash voor een bepaalde regel te gebruiken. Alle pakketten van dezelfde pakketstroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast. Wanneer de client een nieuwe stroom initieert van hetzelfde bron-IP-adres, wordt de bron poort gewijzigd. Als gevolg hiervan kan de 5-tuple-hash ertoe leiden dat het verkeer naar een ander back-end-eind punt gaat.

  Zie [de distributie modus configureren voor Azure Load Balancer](load-balancer-distribution-mode.md)voor meer informatie. Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

  ![Hash-distributie](./media/load-balancer-overview/load-balancer-distribution.png)

  *Afbeelding: hash-distributie*

* **Port Forwarding**

  Met Load Balancer kunt u een binnenkomende NAT-regel maken. Met deze NAT-regel wordt verkeer doorgestuurd van een specifieke poort van een specifiek front-end-IP-adres naar een specifieke poort van een specifiek back-end-exemplaar in het virtuele netwerk. Deze doorstuur bewerking wordt uitgevoerd door dezelfde hash-gebaseerde distributie als taak verdeling. Veelvoorkomende scenario's voor deze mogelijkheid zijn Remote Desktop Protocol (RDP) of SSH-sessies (Secure Shell) voor afzonderlijke VM-exemplaren in een Azure Virtual Network.
  
  U kunt meerdere interne eind punten toewijzen aan poorten op hetzelfde front-end-IP-adres. U kunt de front-end-IP-adressen gebruiken om uw Vm's extern te beheren zonder een extra Jump box.

* **Toepassings onafhankelijkheid en-transparantie**

  Load Balancer communiceert niet rechtstreeks met TCP of UDP of met de toepassingslaag. Elk TCP-of UDP-toepassings scenario kan worden ondersteund. Load Balancer geen stromen afbreekt of als deze niet afkomstig zijn, communiceren met de payload van de stroom of een functie voor de gateway van de toepassingslaag. Protocol-Handshakes worden altijd direct uitgevoerd tussen de client en het exemplaar van de back-end-pool. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.

  * Elk eindpunt wordt alleen beantwoord door een virtuele machine. Zo vindt er altijd een TCP-handshake plaats tussen de client en de geselecteerde back-end-VM. Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end hebt gevalideerd, valideert u de end-to-end-connectiviteit met ten minste één virtuele machine van de back-end.
  * Toepassings payloads zijn transparant voor Load Balancer. Elke UDP-of TCP-toepassing kan worden ondersteund. Voor workloads die per HTTP-aanvraag verwerking of manipulatie van payloads van toepassingslaag vereisen, zoals het parseren van HTTP-Url's, moet u een laag 7-load balancer gebruiken, zoals [Application Gateway](https://azure.microsoft.com/services/application-gateway).
  * Omdat Load Balancer niet met de TCP-nettolading communiceert en TLS-offload levert, kunt u end-to-end versleutelde scenario's bouwen. Het gebruik van Load Balancer grote hoeveel heden uitschalen voor TLS-toepassingen door de TLS-verbinding op de VM zelf te beëindigen. Uw TLS-sessie sleutel kan bijvoorbeeld alleen worden beperkt door het type en het aantal Vm's dat u toevoegt aan de back-end-pool. Als u SSL-offload, verwerking van toepassingslaag of certificaat beheer wilt delegeren naar Azure, gebruikt u in plaats daarvan de Azure Layer 7-load balancer [Application Gateway](https://azure.microsoft.com/services/application-gateway) .

* **Automatische herconfiguratie**

  Load Balancer herconfigureert zichzelf onmiddellijk wanneer u instanties omhoog of omlaag schaalt. Door Vm's toe te voegen aan of te verwijderen uit de back-end-groep, worden de Load Balancer opnieuw geconfigureerd zonder dat er extra bewerkingen op de Load Balancer bron worden uitgevoerd.

* **Statuscontroles**

  Als u de status van exemplaren in de back-end-pool wilt bepalen, gebruikt Load Balancer status controles die u definieert. Wanneer een test niet reageert, stopt de Load Balancer met het verzenden van nieuwe verbindingen naar de slechte instanties. Een test fout heeft geen invloed op bestaande verbindingen. De verbinding wordt vervolgd totdat de toepassing de stroom beëindigt, er een time-out voor inactiviteit optreedt of de virtuele machine wordt uitgeschakeld.

  Load Balancer biedt verschillende typen Health probe voor TCP-, HTTP-en HTTPS-eind punten. Zie [test typen](load-balancer-custom-probe-overview.md#types)voor meer informatie.

  Wanneer u klassieke Cloud Services gebruikt, is een extra type toegestaan: [gast agent](load-balancer-custom-probe-overview.md#guestagent). Een gast agent moet als een status test van de laatste redmiddel worden beschouwd. Micro soft raadt hen niet aan als er andere opties beschikbaar zijn.

* **Uitgaande verbindingen (SNAT)**

  Alle uitgaande stromen van privé-IP-adressen in uw virtuele netwerk naar open bare IP-adressen op internet kunnen worden omgezet naar een front-end-IP-adres van de Load Balancer. Wanneer een open bare front-end is gekoppeld aan een back-end-VM met behulp van een taakverdelings regel, vertaalt Azure uitgaande verbindingen met het open bare front-end-IP-adres. Deze configuratie heeft de volgende voor delen:

  * Eenvoudige upgrade en nood herstel van services, omdat de front-end dynamisch kan worden toegewezen aan een ander exemplaar van de service.
  * Eenvoudig beheer van toegangs beheer lijst (ACL). Acl's die worden weer gegeven als front-end Ip's worden niet gewijzigd wanneer Services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd. Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan computers vermindert de last van het implementeren van lijsten met veilige geadresseerden.

  Zie voor meer informatie [uitgaande verbindingen in azure](load-balancer-outbound-connections.md).

Standard Load Balancer heeft nog meer SKU-specifieke mogelijkheden, zoals hieronder wordt beschreven.

## <a name="skus"></a> Vergelijking van Load Balancer-SKU's

Load Balancer ondersteunt zowel de Basic-als standaard-Sku's. Deze Sku's wijken af van de schaal, functies en prijzen van het scenario. Elk scenario dat mogelijk is met basis Load Balancer kan worden gemaakt met Standard Load Balancer. De Api's voor beide Sku's zijn vergelijkbaar en worden aangeroepen via de specificatie van een SKU. De API voor de ondersteuning van Sku's voor Load Balancer en het open bare IP-adres is beschikbaar vanaf de `2017-08-01`-API. Beide Sku's delen dezelfde algemene API en structuur.

De volledige scenario configuratie kan enigszins verschillen, afhankelijk van de SKU. In de documentatie voor Load Balancer is duidelijk aangegeven wanneer een artikel alleen voor een specifieke SKU geldt. Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [overzicht van Azure Standard Load Balancer](load-balancer-standard-overview.md)voor meer informatie.

>[!NOTE]
> Nieuwe ontwerpen moeten Standard Load Balancer kunnen faciliteren.

Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. Load Balancer en de SKU van het open bare IP-adres moeten overeenkomen wanneer u ze gebruikt met open bare IP-adressen. Load Balancer en open bare IP-Sku's zijn niet onveranderbaar.

De best practice is om de Sku's expliciet op te geven. Op dit moment worden vereiste wijzigingen tot een minimum beperkt. Als er geen SKU is opgegeven, is de standaard waarde de `2017-08-01` API-versie van de basis-SKU.

>[!IMPORTANT]
>Standard Load Balancer is een nieuw Load Balancer product. Het is grotendeels een superset van basis Load Balancer, maar er zijn belang rijke verschillen tussen de twee producten. Elk end-to-end scenario dat mogelijk is met Basic Load Balancer kan worden gemaakt met Standard Load Balancer. Als u al een basis Load Balancer gebruikt, vergelijkt u deze met Standard Load Balancer om inzicht te krijgen in de meest recente wijzigingen in het gedrag.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Zie [limieten voor load balancers](https://aka.ms/lblimits)voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="concepts"></a>Concepten

### <a name = "publicloadbalancer">Openbare Load Balancer</a>

Een openbaar Load Balancer wijst het open bare IP-adres en de poort van binnenkomend verkeer toe aan het privé-IP-adres en de poort van de virtuele machine. Load Balancer wijst verkeer op de andere manier toe voor het antwoord verkeer van de virtuele machine. U kunt specifieke typen verkeer distribueren over meerdere Vm's of services door regels voor taak verdeling toe te passen. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.

>[!NOTE]
>U kunt slechts één openbaar Load Balancer en één interne Load Balancer implementeren per beschikbaarheidsset.

In de volgende afbeelding ziet u een eind punt met gelijke taak verdeling voor Internet verkeer dat wordt gedeeld door drie Vm's voor de open bare en TCP-poort 80. Deze drie VM's maken deel uit van een set met taakverdeling.

![Voorbeeld van een openbare Load Balancer](./media/load-balancer-overview/IC727496.png)

*Afbeelding: webverkeer balanceren met behulp van een open bare Load Balancer*

Internetclients verzenden webpagina aanvragen naar het open bare IP-adres van een web-app op TCP-poort 80. Azure Load Balancer distribueert de aanvragen over de drie Vm's in de set met gelijke taak verdeling. Zie [basis Load Balancer functies](load-balancer-overview.md##fundamental-load-balancer-features)voor meer informatie over Load Balancer algoritmen.

Azure Load Balancer netwerk verkeer wordt standaard gelijkmatig verdeeld over meerdere VM-exemplaren. U kunt ook sessieaffiniteit configureren. Zie [de distributie modus configureren voor Azure Load Balancer](load-balancer-distribution-mode.md)voor meer informatie.

### <a name = "internalloadbalancer"></a> Interne Load Balancer

Een interne Load Balancer stuurt alleen verkeer naar bronnen die zich binnen een virtueel netwerk bevinden of die een VPN gebruiken voor toegang tot de Azure-infra structuur, in tegens telling tot een open bare Load Balancer. De Azure-infra structuur beperkt de toegang tot de front-end IP-adressen met taak verdeling van een virtueel netwerk. Front-end-IP-adressen en virtuele netwerken worden nooit rechtstreeks blootgesteld aan een Internet-eind punt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd.

Met een interne Load Balancer zijn de volgende typen taakverdeling mogelijk:

* **Binnen een virtueel netwerk**: taak verdeling van virtuele machines in het virtuele netwerk naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor een cross-premises virtueel netwerk**: taak verdeling van on-premises computers naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor toepassingen met meerdere lagen**: taak verdeling voor toepassingen met meerdere lagen op internet waarbij de back-endservers niet op internet zijn gericht. Voor de back-end-lagen is taak verdeling van verkeer van de Internet gerichte laag vereist. Zie de volgende afbeelding.
* **Voor Line-Of-Business-toepassingen**: taakverdeling voor Line-Of-Business-toepassingen die worden gehost in Azure zonder extra load balancer-hardware of -software. Dit scenario bevat on-premises servers die zich in de set computers bevinden waarvan het verkeer wordt verdeeld.

![Voorbeeld van een interne Load Balancer](./media/load-balancer-overview/IC744147.png)

*Afbeelding: toepassingen met meerdere lagen verdelen met behulp van zowel open bare als interne Load Balancer*

## <a name="pricing"></a>Prijzen

Gebruik van Standard Load Balancer wordt in rekening gebracht.

* Aantal geconfigureerde taakverdelings-en uitgaande regels. Inkomende NAT-regels tellen niet in het totale aantal regels.
* De hoeveelheid gegevens die binnenkomend en uitgaand is verwerkt, onafhankelijk van regels.

Zie [Load Balancer prijzen](https://azure.microsoft.com/pricing/details/load-balancer/)voor Standard Load Balancer prijs informatie.

Basic Load Balancer wordt gratis aangeboden.

## <a name="sla"></a>SLA

Zie [Sla voor Load Balancer](https://aka.ms/lbsla)voor meer informatie over de Standard Load Balancer sla.

## <a name="limitations"></a>Beperkingen

* Load Balancer biedt taak verdeling en poort door sturen voor specifieke TCP-of UDP-protocollen. Taakverdelings regels en binnenkomende NAT-regels ondersteunen TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.

  Load Balancer de payload van een UDP-of TCP-stroom niet beëindigt, beantwoordt of anderszins communiceert. Het is geen proxy. Een geslaagde validatie van de connectiviteit met een front-end moet in-band worden uitgevoerd met hetzelfde protocol dat wordt gebruikt in een taak verdeling of binnenkomende NAT-regel. Ten minste één van de virtuele machines moet een reactie genereren voor een client om een reactie van een front-end te zien.

  Er wordt geen in-band-antwoord ontvangen van de Load Balancer front-end geeft aan dat er geen virtuele machines kunnen reageren. Er kan niet worden gecommuniceerd met een Load Balancer front-end zonder dat de virtuele machine kan reageren. Dit principe geldt ook voor uitgaande verbindingen waarbij SNAT voor poort masker alleen wordt ondersteund voor TCP en UDP. Andere IP-protocollen, waaronder ICMP, mislukken. Wijs een openbaar IP-adres op exemplaar niveau toe om dit probleem te verhelpen. Zie [Wat is SNAT en Pat](load-balancer-outbound-connections.md#snat)? voor meer informatie.

* Interne load balancers vertalen geen uitgaande oorspronkelijke verbindingen met de front-end van een interne Load Balancer, omdat beide zich in een privé-IP-adres ruimte bevinden. Open bare load balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen. Voor interne load balancers vermijdt deze benadering de potentiële SNAT-poort uitputting binnen een unieke interne IP-adres ruimte, waarbij omzetting niet vereist is.

  Een neven effect is dat als een uitgaande stroom van een virtuele machine in de back-end-pool een stroom naar front-end van de interne Load Balancer in de groep probeert te maken _en_ wordt teruggekoppeld aan zichzelf, de twee zijden van de stroom niet overeenkomen. Omdat ze niet overeenkomen, mislukt de stroom. De stroom slaagt als de stroom niet is gekoppeld aan dezelfde virtuele machine in de back-end-pool die de stroom heeft gemaakt naar de front-end.

  Wanneer de stroom wordt teruggeleid naar zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end en de bijbehorende binnenkomende stroom lijkt afkomstig van de VM naar zichzelf. Vanuit het weergave punt van het gast besturingssysteem komen de inkomende en uitgaande delen van dezelfde stroom niet overeen in de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en het doel komen niet overeen. Wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end-pool, komen de helften van de stroom overeen en kan de virtuele machine reageren op de stroom.

  Het symptoom voor dit scenario bestaat uit terugkerende verbindingstime-outs wanneer de stroom naar dezelfde back-end wordt geretourneerd als de stroom afkomstig is. Veelvoorkomende tijdelijke oplossingen omvatten het invoegen van een proxy laag achter de interne Load Balancer en het gebruik van regels voor het door sturen van direct server return-stijl (DSR). Zie [meerdere front-ends voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.

  U kunt een intern Load Balancer combi neren met elke proxy van derden of interne [Application Gateway](../application-gateway/application-gateway-introduction.md) gebruiken voor proxy SCENARIO'S met http/https. U kunt een open bare Load Balancer gebruiken om dit probleem te verhelpen, maar het resulterende scenario is gevoelig voor de [uitputting](load-balancer-outbound-connections.md#snat)van de SNAT. Vermijd deze tweede benadering, tenzij u deze zorgvuldig beheert.

* Over het algemeen worden IP-fragmenten die worden doorgestuurd, niet ondersteund in regels voor taak verdeling. IP-fragmentatie van UDP-en TCP-pakketten wordt niet ondersteund in regels voor taak verdeling. De taakverdelings regels voor poorten met hoge Beschik baarheid kunnen worden gebruikt voor het door sturen van bestaande IP-fragmenten. Zie overzicht van poorten met [hoge Beschik baarheid](load-balancer-ha-ports-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [een basis Load Balancer maken](quickstart-create-basic-load-balancer-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer: Maak een virtuele machine met een aangepaste IIS-extensie die is geïnstalleerd en taak verdeling van de web-app tussen de vm's.
