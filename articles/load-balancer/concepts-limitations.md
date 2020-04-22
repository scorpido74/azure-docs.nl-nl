---
title: Componenten en beperkingen
titleSuffix: Azure Load Balancer
description: Overzicht van azure load balancer-componenten en -beperkingen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: 7a7210915920ed9ab2a5ddc47a27c2587895a57a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768267"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer componenten en beperkingen
Azure Load Balancer bevat verschillende belangrijke onderdelen voor de werking ervan.  Deze onderdelen kunnen in uw abonnement worden geconfigureerd via de Azure-portal, Azure CLI of Azure PowerShell.  

## <a name="load-balancer-components"></a>Load Balancer-componenten

* **Frontend IP-configuraties**: Het IP-adres van de load balancer. Het is het aanspreekpunt voor klanten. Deze adressen kunnen: 

    - **[Openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privé-IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Backend pool**: De groep virtuele machines of instanties in de virtuele machineschaalset die de binnenkomende aanvraag gaan dienen. Als u kosteneffectief wilt schalen om aan grote volumes binnenkomend verkeer te voldoen, raden computerrichtlijnen over het algemeen aan om meer exemplaren toe te voegen aan de backendpool. Load Balancer configureert zichzelf direct via automatische herconfiguratie wanneer u instanties omhoog of omlaag schaalt. Het toevoegen of verwijderen van VM's uit de backend pool configureert de Load Balancer zonder extra bewerkingen. De scope van de backend pool is een virtuele machine in het virtuele netwerk. Een backendpool kan maximaal 1000 backend-exemplaren of IP-configuraties hebben.
Basic Load Balancers hebben een beperkt bereik (beschikbaarheidset) kunnen alleen maximaal 300 IP-configuraties opschalen. Zie [Limieten voor load balancer voor](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)meer informatie over limieten. Wanneer u overweegt hoe u uw backendpool ontwerpen, u het minste aantal afzonderlijke backendpoolbronnen ontwerpen om de duur van beheerbewerkingen verder te optimaliseren. Er is geen verschil in prestaties van het datavlak of de schaal.
* **Statussondes:** Een **[statussonde](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** wordt gebruikt om de status van de instanties in de backendpool te bepalen. U de ongezonde drempelwaarde voor uw gezondheidssondes definiëren. Wanneer een test niet reageert, stopt de Load Balancer met het verzenden van nieuwe verbindingen naar de slechte instanties. Een sondestoring heeft geen invloed op bestaande verbindingen. 
    
    De verbinding gaat door tot de toepassing: 
    - Beëindigt de stroom
    - Idle time-out vindt plaats
    - De VM wordt afgesloten

    Load Balancer biedt verschillende typen health probe voor eindpunten:
    - TCP
    - HTTP
    - HTTPS (HTTP-sonde met TLS-wrapper (Transport Layer Security)
     
     Basic Load Balancer ondersteunt geen HTTPS-sondes. Daarnaast beëindigt Basic Load Balancer alle TCP-verbindingen (inclusief gevestigde verbindingen). 
    Zie [Sondetypen voor](load-balancer-custom-probe-overview.md#types)meer informatie.

* **Regels voor het balanceren van**de lasten: load-balancing regels zijn degenen die de Load Balancer vertellen wat er gedaan moet worden wanneer. 
* **Inkomende NAT-regels**: Een inkomende NAT-regel stuurt verkeer door van een specifieke poort van een specifiek ip-adres aan de voorzijde naar een specifieke poort van een specifieke backend-instantie binnen het virtuele netwerk. **[Port forwarding](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** gebeurt door dezelfde hash-gebaseerde distributie als load balancing. Veelvoorkomende scenario's voor deze mogelijkheid zijn RDP- of Secure Shell-sessies (Secure Shell) voor afzonderlijke VM-exemplaren in een Azure Virtual Network. U meerdere interne eindpunten toewijzen aan poorten op hetzelfde front-end IP-adres. U de front-end IP-adressen gebruiken om uw VM's op afstand te beheren zonder een extra jump box.
* **Uitgaande regels:** een **[uitgaande regel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** configureert uitgaande Netwerkadresvertaling (NAT) voor alle virtuele machines of exemplaren die zijn geïdentificeerd door de backendpool van uw standaardloadbalansr die naar de frontend moet worden vertaald.

  Basic Load Balancer ondersteunt geen uitgaande regels.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Transportprotocollen**: Load Balancer ondersteunt geen ICMP; ICMP pings naar een publiek gerichte load balancer zal time-out. Als u uw openbare load balancer wilt pingen, gebruikt u TCP Ping

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>Load Balancer-concepten

Load Balancer biedt de volgende fundamentele mogelijkheden voor TCP en UDP-toepassingen:

* **Algoritme voor taakverdeling:** met Azure Load Balancer u een regel voor het balanceren van de lastenom te distribueren om verkeer te distribueren dat aan de frontend aan de backend pool-exemplaren komt. Load Balancer gebruikt een hashing-algoritme voor de verdeling van binnenkomende stromen (geen bytes) en herschrijft de kopteksten van stromen naar backendpoolexemplaren. Er is een server beschikbaar om nieuwe stromen te ontvangen wanneer een statussonde een gezond eindpunt voor het einde van het eindpunt aangeeft.
Load Balancer gebruikt standaard een 5-tuple hash. 

   De hash bevat: 

   - **IP-adres van bron**
   - **Bronpoort**
   - **IP-adres van doel**
   - **Bestemmingshaven**
   - **IP-protocolnummer om stromen naar beschikbare servers toe te leiden** 

U affiniteit maken met een bron-IP-adres met behulp van een 2- of 3-tuple hash voor een bepaalde regel. Alle pakketten van dezelfde pakketstroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast. Wanneer de client een nieuwe stroom van hetzelfde bron-IP start, wordt de bronpoort gewijzigd. Als gevolg hiervan kan de 5-tuple hash ervoor zorgen dat het verkeer naar een ander backend endpoint gaat.
Zie [De distributiemodus configureren voor Azure Load Balancer voor](./load-balancer-distribution-mode.md)meer informatie. 

Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Hash-distributie">
</p>

  *Afbeelding: hash-distributie*

* **Onafhankelijkheid en transparantie van**toepassingen: Load Balancer werkt niet rechtstreeks met TCP of UDP of de toepassingslaag. Elk TCP- of UDP-toepassingsscenario kan worden ondersteund. Load Balancer beëindigt of ontkomt geen stromen, werkt niet samen met de lading van de stroom of biedt geen gatewayfunctie voor toepassingslagen. Protocolhandshakes vinden altijd direct plaats tussen de client en de back-end pool-instantie. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.
  * Elk eindpunt wordt alleen beantwoord door een virtuele machine. Er vindt bijvoorbeeld altijd een TCP-handshake plaats tussen de client en de geselecteerde back-end-vm. Een antwoord op een verzoek aan een front-end is een antwoord gegenereerd door een back-end VM. Wanneer u de connectiviteit met een front-end met succes valideert, valideert u de end-to-end-connectiviteit tot ten minste één back-end virtuele machine.
  * Toepassingspayloads zijn transparant voor Load Balancer. Elke UDP- of TCP-toepassing kan worden ondersteund.
  * Omdat Load Balancer geen interactie heeft met de TCP-payload en TLS offload biedt, u end-to-end versleutelde scenario's bouwen. Het gebruik van Load Balancer krijgt een grote scale-out voor TLS-toepassingen door de TLS-verbinding op de VM zelf te beëindigen. Uw TLS-sessiesleutelcapaciteit wordt bijvoorbeeld alleen beperkt door het type en het aantal VM's dat u toevoegt aan de back-endpool.

* **Uitgaande verbindingen**: Alle uitgaande stromen van privé-IP-adressen binnen uw virtuele netwerk naar openbare IP-adressen op het internet kunnen worden vertaald naar een frontend IP-adres van de Load Balancer. Wanneer een openbare front-end is gekoppeld aan een backend-vm door middel van een load-balancing-regel, vertaalt Azure uitgaande verbindingen naar het openbare FRONTEnd IP-adres. Deze configuratie heeft de volgende voordelen:
  * Eenvoudige upgrade en disaster recovery van services, omdat de front-end dynamisch kan worden toegewezen aan een ander exemplaar van de service.
  * Beheer van de toegangscontrolelijst (ACL). ACL's uitgedrukt als front-end IP's veranderen niet als services omhoog of omlaag schalen of opnieuw worden geïmplementeerd. Het vertalen van uitgaande verbindingen naar een kleiner aantal IP-adressen dan machines vermindert de last van het implementeren van veilige ontvangerslijsten.

  Standard Load Balancer maakt gebruik van een [robuust, schaalbaar en voorspelbaar SNAT-algoritme.](load-balancer-outbound-connections.md#snat) Dit zijn de belangrijkste principes om te onthouden bij het werken met Standard Load Balancer:

    - load-balancing regels concluderen hoe SNAT is geprogrammeerd. Regels voor taakverdeling zijn protocolspecifiek. SNAT is protocolspecifiek en de configuratie moet dit weerspiegelen in plaats van een bijwerking te creëren.

    - **Meerdere frontends** Wanneer er meerdere frontends beschikbaar zijn, worden alle frontends gebruikt en vermenigvuldigt elke frontend het aantal beschikbare SNAT-poorten. Als u meer SNAT-poorten wilt omdat u verwacht of al een grote vraag ondervindt naar uitgaande verbindingen, u ook incrementele SNAT-poortvoorraad toevoegen door extra frontends, regels en backendpools te configureren aan dezelfde bronnen voor virtuele machines.

    - **Bepalen welke frontend wordt gebruikt voor uitgaande** U kiezen en controleren als u niet wilt dat een bepaalde frontend wordt gebruikt voor uitgaande verbindingen. Als u uitgaande verbindingen wilt beperken tot alleen afkomstig zijn van een specifiek IP-adres aan de voorzijde, u optioneel uitgaande SNAT uitschakelen op de regel die de uitgaande toewijzing uitdrukt.

    - **Uitgaande** uitgaande scenario's voor uitgaande connectiviteit beheren zijn expliciet en uitgaande connectiviteit bestaat pas nadat deze is opgegeven. Standard Load Balancer bestaat binnen de context van het virtuele netwerk.  Een virtueel netwerk is een geïsoleerd, privénetwerk.  Tenzij er een koppeling met een openbaar IP-adres bestaat, is openbare connectiviteit niet toegestaan.  U [eindpunten van de Virtual Network Service](../virtual-network/virtual-network-service-endpoints-overview.md) bereiken omdat ze zich binnen en lokaal bevinden in uw virtuele netwerk.  Als u uitgaande connectiviteit met een bestemming buiten uw virtuele netwerk wilt tot stand brengen, hebt u twee opties:
        - een openbaar IP-adres op standaardSKU toewijzen als openbaar IP-adres op instantieniveau aan de bron van de virtuele machine of
        - plaats de virtuele machinebron in de backendpool van een openbare StandaardloadBalancer.

        Beide zullen uitgaande connectiviteit van het virtuele netwerk tot buiten het virtuele netwerk mogelijk maken. 

        Als u _alleen_ een interne Standaardload Balancer hebt die is gekoppeld aan de backendpool waarin uw virtuele machinebron zich bevindt, kan uw virtuele machine alleen virtuele netwerkbronnen en [Virtual Network Service-eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md)bereiken.  U de stappen volgen die in de vorige alinea zijn beschreven om uitgaande connectiviteit te maken.

        Uitgaande connectiviteit van een virtuele machinebron die niet is gekoppeld aan standaard SKU's, blijft zoals voorheen.

        Bekijk [de gedetailleerde bespreking van uitgaande verbindingen](load-balancer-outbound-connections.md).

* **Beschikbaarheidszones**: Standard Load Balancer ondersteunt extra vaardigheden in regio's waar beschikbaarheidszones beschikbaar zijn. Deze functies zijn incrementeel naar alle Standaard Load Balancer biedt.  Beschikbaarheidszones configuraties zijn beschikbaar voor zowel typen, openbare als interne Standard Load Balancer.
 Een zone-redundante frontend overleeft zone storing en wordt bediend door speciale infrastructuur in alle zones tegelijk. 
Daarnaast u een frontend garanderen voor een specifieke zone. Een zonale frontend deelt het lot met de betreffende zone en wordt alleen bediend door speciale infrastructuur in één zone.
Cross-zone load balancing is beschikbaar voor de backend pool en elke virtuele machinebron in een virtueel netwerk kan deel uitmaken van een backend pool.
Basic Load Balancer ondersteunt geen zones.
Bekijk [gedetailleerde bespreking van beschikbaarheidzones gerelateerde vaardigheden](load-balancer-standard-availability-zones.md) en [beschikbaarheidzones overzicht](../availability-zones/az-overview.md) voor meer informatie.

* **HA-poorten:** U load-balancing regels configureren om uw applicatie schaal te maken en zeer betrouwbaar te zijn. Wanneer u een HA-poortloadbalancing-regel gebruikt, biedt Standard Load Balancer per flow load balancing op elke kortstondige poort van het ip-adres van een interne StandaardLoad Balancer.  De functie is handig voor andere scenario's waarbij het onpraktisch of ongewenst is om afzonderlijke poorten op te geven. Met een HA-poorttaakverdelingsregel u actief-passieve of actieve n+1-scenario's maken voor virtuele netwerkapparaten en elke toepassing, waarvoor grote reeksen binnenkomende poorten nodig zijn.  Een health probe kan worden gebruikt om te bepalen welke backends nieuwe stromen moeten ontvangen.  U een netwerkbeveiligingsgroep gebruiken om een scenario voor het bereik van een poort te emuleren. Basic Load Balancer ondersteunt geen HA-poorten.
Gedetailleerde [bespreking van HA-havens bekijken](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Als u van plan bent een netwerkvirtueel toestel te gebruiken, raadpleegt u uw leverancier voor informatie over de vraag of hun product is getest met HA-poorten en volgt u de specifieke richtlijnen voor implementatie. 

* **Meerdere frontends**: Load Balancer ondersteunt meerdere regels met meerdere frontends.  Standard Load Balancer breidt dit uit naar uitgaande scenario's.  Uitgaande scenario's zijn in wezen het omgekeerde van een inkomende regel voor het balanceren van de lasten.  De inkomende load-balancing-regel maakt ook een partner voor uitgaande verbindingen. Standard Load Balancer gebruikt alle frontends die zijn gekoppeld aan een virtuele machinebron via een load-balancing rule.  Bovendien u met een parameter op de regel voor het balanceren van de lastenafweging een regel voor het balanceren van de lasten onderdrukken ten behoeve van uitgaande connectiviteit, waarmee specifieke frontends kunnen worden geselecteerd, waaronder geen.

Ter vergelijking, Basic Load Balancer selecteert een enkele frontend willekeurig en er is geen mogelijkheid om te bepalen welke is geselecteerd.
## <a name="load-balancer-types"></a>Load Balancer-typen

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Openbare Load Balancer

Een openbare Load Balancer brengt het openbare IP-adres en de poort van binnenkomend verkeer in kaart naar het privé-IP-adres en de poort van de VM. Load Balancer brengt verkeer andersom in kaart voor het reactieverkeer van de VM. U specifieke typen verkeer distribueren over meerdere VM's of services door regels voor het balanceren van de lasten toe te passen. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.

>[!NOTE]
>U slechts één openbare load balancer en één interne load balancer per beschikbaarheidsset implementeren.

De volgende afbeelding toont een load-balanced endpoint voor webverkeer dat wordt gedeeld tussen drie VM's voor het publiek en TCP-poort 80. Deze drie VM's maken deel uit van een set met taakverdeling.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Public load balancer">
</p>

*Figuur: Webverkeer in evenwicht brengen met behulp van een openbare load balancer*

Internetclients sturen webpaginaverzoeken naar het openbare IP-adres van een web-app op TCP-poort 80. Azure Load Balancer verdeelt de aanvragen over de drie VM's in de load-balanced set. Zie [Load Balancer-concepten](concepts-limitations.md#load-balancer-concepts)voor meer informatie over load balancer-algoritmen .

Azure Load Balancer verdeelt netwerkverkeer standaard gelijk over meerdere VM-exemplaren. U kunt ook sessieaffiniteit configureren. Zie [De distributiemodus configureren voor Azure Load Balancer voor](load-balancer-distribution-mode.md)meer informatie.

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a> Interne Load Balancer

Een interne load balancer stuurt verkeer alleen naar resources die zich binnen een virtueel netwerk bevinden of die een VPN gebruiken om toegang te krijgen tot Azure-infrastructuur, in tegenstelling tot een public load balancer. Azure-infrastructuur beperkt de toegang tot de load-balanced front-end IP-adressen van een virtueel netwerk. Front-end IP-adressen en virtuele netwerken worden nooit direct blootgesteld aan een interneteindpunt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd.

Met een interne Load Balancer zijn de volgende typen taakverdeling mogelijk:

* **Binnen een virtueel netwerk**: Load balancing van VM's in het virtuele netwerk naar een set VM's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor een cross-premises virtueel netwerk:** Load balancing van on-premises computers naar een set VM's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor toepassingen met meerdere lagen:** taakverdeling voor multi-tier toepassingen met een internetgericht internet waarbij de back-endlagen niet naar internet zijn gericht. De back-endlagen vereisen verkeersverdeling vanaf de laag die naar internet gericht is. Zie de volgende figuur.
* **Voor Line-Of-Business-toepassingen**: taakverdeling voor Line-Of-Business-toepassingen die worden gehost in Azure zonder extra load balancer-hardware of -software. Dit scenario omvat on-premises servers die zich in de set van computers waarvan het verkeer wordt geladen in evenwicht.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Public load balancer">
</p>

*Figuur: Multi-tier toepassingen in evenwicht brengen met zowel openbare als interne load balancer*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Vergelijking van Load Balancer-SKU's

Load balancer ondersteunt zowel Basic als Standard SKU's. Deze SKU's verschillen in scenarioschaal, functies en prijzen. Elk scenario dat mogelijk is met Basic Load Balancer kan worden gemaakt met Standard Load Balancer. De API's voor beide SKU's zijn vergelijkbaar en worden aangeroepen via de specificatie van een SKU. De API voor het ondersteunen van SKU's voor load `2017-08-01` balancer en het openbare IP is beschikbaar vanaf de API. Beide SKU's delen dezelfde algemene API en structuur.

De volledige scenarioconfiguratie kan enigszins verschillen, afhankelijk van De SKU. Load balancer documentatie roept wanneer een artikel alleen van toepassing is op een specifieke SKU. Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie overzicht [van Azure Standard Load Balancer](load-balancer-standard-overview.md)voor meer informatie.

Zie [Een basisbalans voor basislastenafweging upgraden](upgrade-basic-standard.md)als u Basic Load Balancer wilt upgraden naar Standard.

>[!NOTE]
> Microsoft raadt Standard Load Balancer aan.
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. Load Balancer en het openbare IP-adres SKU moeten overeenkomen wanneer u ze gebruikt met openbare IP-adressen. Load Balancer en openbare IP-SKU's zijn niet mutable.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Zie [Limieten voor load balancer voor](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)meer informatie . Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="limitations"></a><a name = "limitations"></a>Beperkingen

- SKU's zijn niet veranderlijk. U mag de SKU van een bestaande resource niet wijzigen.
- Een zelfstandige virtuele machine bron, beschikbaarheid set resource, of virtuele machine schaal set bron kan verwijzen naar een SKU, nooit beide.
- Een load balancer-regel kan niet twee virtuele netwerken omvatten.  Frontends en de bijbehorende backend-exemplaren moeten zich in hetzelfde virtuele netwerk bevinden.  
- [Verhuisabonnementsbewerkingen](../azure-resource-manager/management/move-resource-group-and-subscription.md) worden niet ondersteund voor standaard LB- en openbare IP-bronnen.
- Webworkerrollen zonder VNet en andere Microsoft-platformservices kunnen toegankelijk zijn vanuit instanties achter alleen een interne Standaardload Balancer. U mag hier niet op vertrouwen, omdat de betreffende service zelf of het onderliggende platform zonder kennisgeving kan veranderen. U moet er altijd van uitgaan dáár desgewenst uitgaande [connectiviteit](load-balancer-outbound-connections.md) moeten maken wanneer u alleen een interne standaardloadbalancer gebruikt.

- Load Balancer biedt taakverdeling en poortforwarding voor specifieke TCP- of UDP-protocollen. Regels voor taakverdeling en binnenkomende NAT-regels ondersteunen TCP en UDP, maar geen andere IP-protocollen, waaronder ICMP.

  Load Balancer beëindigt, reageert of werkt niet anders met de payload van een UDP- of TCP-stroom. Het is geen proxy. Succesvolle validatie van connectiviteit met een front-end moet plaatsvinden in-band met hetzelfde protocol dat wordt gebruikt in een load balancing of inbound NAT-regel. Ten minste één van uw virtuele machines moet een antwoord genereren voor een client om een antwoord van een front-end te zien.

  Als u geen in-band-respons ontvangt van de front-end Load Balancer, geeft dit aan dat er geen virtuele machines kunnen reageren. Niets kan communiceren met een Load Balancer front-end zonder een virtuele machine in staat om te reageren. Dit principe is ook van toepassing op uitgaande verbindingen waarbij poortmaskerade SNAT alleen wordt ondersteund voor TCP en UDP. Alle andere IP-protocollen, waaronder ICMP, mislukken. Wijs een openbaar IP-adres op instantieniveau toe om dit probleem te beperken. Zie [Understanding SNAT en PAT](load-balancer-outbound-connections.md#snat)voor meer informatie.

- Interne load balancers vertalen geen uitgaande verbindingen naar de voorkant van een interne load balancer omdat beide zich in de privé-IP-adresruimte bevinden. Public Load Balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van particuliere IP-adressen binnen het virtuele netwerk naar openbare IP-adressen. Voor interne load balancers voorkomt deze aanpak mogelijke SNAT-poortuitputting in een unieke interne IP-adresruimte, waar vertaling niet nodig is.

  Een neveneffect is dat als een uitgaande stroom van een VM in de back-end pool een stroom naar de voorkant van de interne load balancer in de pool probeert _en_ terug naar zichzelf wordt toegewezen, de twee poten van de stroom niet overeenkomen. Omdat ze niet overeenkomen, faalt de stroom. De stroom slaagt als de stroom niet is teruggeleid naar dezelfde VM in de back-endpool die de stroom naar de front-end heeft gemaakt.

  Wanneer de stroom weer naar zichzelf wordt toegewezen, lijkt de uitgaande stroom afkomstig te zijn van de VM naar de front-end en lijkt de bijbehorende inkomende stroom afkomstig te zijn van de VM naar zichzelf. Vanuit het oogpunt van het gastbesturingssysteem komen de inkomende en uitgaande delen van dezelfde stroom niet overeen met de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en bestemming komen niet overeen. Wanneer de stroom wordt toegewezen aan een andere VM in de back-endpool, komen de helften van de stroom overeen en kan de VM reageren op de stroom.

  Het symptoom voor dit scenario is intermitterende verbindingstime-outs wanneer de stroom terugkeert naar dezelfde backend die de stroom is ontstaan. Veelvoorkomende oplossingen zijn het invoegen van een proxylaag achter de interne Load Balancer en het gebruik van DSR-stijlregels (Direct Server Return). Zie [Meerdere fronteinden voor Azure Load Balancer voor](load-balancer-multivip-overview.md)meer informatie.

  U een interne load balancer combineren met elke proxy van derden of interne [Application Gateway](../application-gateway/application-gateway-introduction.md) gebruiken voor proxyscenario's met HTTP/HTTPS. Hoewel u een openbare load balancer gebruiken om dit probleem te beperken, is het resulterende scenario gevoelig voor [SNAT-uitputting.](load-balancer-outbound-connections.md#snat) Vermijd deze tweede aanpak, tenzij zorgvuldig beheerd.

- In het algemeen wordt het doorsturen van IP-fragmenten niet ondersteund op regels voor het balanceren van de belasting. IP-fragmentatie van UDP- en TCP-pakketten wordt niet ondersteund op regels voor het balanceren van de lasten. Load-balancing rules voor poorten met hoge beschikbaarheid kunnen worden gebruikt om bestaande IP-fragmenten door te sturen. Zie [overzicht van poorten met hoge beschikbaarheid](load-balancer-ha-ports-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een basisbalans upgrade om](upgrade-basic-standard.md) Basic Load Balancer te upgraden naar Standard Load Balancer.
- Zie [Een openbare standaardbalanservoor maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een Load Balancer: maak er een, maak VM's met een aangepaste IIS-extensie geïnstalleerd en laadbalans de web-app tussen de VM's.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over het gebruik van [standaardbalans- en beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [gezondheidssondes.](load-balancer-custom-probe-overview.md)
- Meer informatie over [standaard load balancer diagnostics](load-balancer-standard-diagnostics.md).
- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [uitgaande regels](load-balancer-outbound-rules-overview.md).
- Meer informatie over [TCP Reset in Idle](load-balancer-tcp-reset.md).
- Meer informatie over [standaardloadbalancer met HA-poorts regels voor taakverdeling](load-balancer-ha-ports-overview.md).
- Meer informatie over het gebruik [van Load Balancer met meerdere frontends](load-balancer-multivip-overview.md).
- Meer informatie over [netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
