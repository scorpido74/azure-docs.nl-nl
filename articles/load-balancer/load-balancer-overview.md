---
title: Wat is Azure Load Balancer?
titlesuffix: Azure Load Balancer
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
ms.date: 01/11/2019
ms.author: allensu
ms.openlocfilehash: 349d8afd46a06455edcd25e2a7ea48f407d285ef
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130419"
---
# <a name="what-is-azure-load-balancer"></a>Wat is Azure Load Balancer?

Met Azure Load Balancer kunt u uw toepassingen schalen en hoge beschikbaarheid voor uw services realiseren. Load Balancer biedt ondersteuning voor scenario's met inkomend en uitgaand verkeer, biedt lage latentie en hoge doorvoer en kan omhoog worden geschaald tot miljoenen stromen voor alle TCP- en UDP-toepassingen.  

Load Balancer verdeelt nieuwe inkomende stromen die aankomen op de front-end-naar-back-endpoolinstanties van de Load Balancer, overeenkomstig regels en statuscontroles. 

Daarnaast biedt een openbare Load Balancer uitgaande verbindingen voor virtuele machines (VM's) binnen uw netwerk door de privé-IP-adressen ervan om te zetten in openbare IP-adressen.

Azure Load Balancer is beschikbaar in twee SKU's: Basic en Standard. Er zijn verschillen in schaal, functies en prijzen. Elk scenario dat mogelijk is met de Basic-versie van Load Balancer kan ook worden gemaakt met de Standard-versie ervan, hoewel de manier waarop bepaalde dingen worden benaderd, enigszins verschillen. Naarmate u meer te weten komt over Load Balancer, is het belangrijk dat u zich vertrouwd maakt met de basisprincipes en de verschillen die specifiek zijn voor elke SKU.

## <a name="why-use-load-balancer"></a>Waarom zou ik Load Balancer moeten gebruiken? 

U kunt Azure Load Balancer voor het volgende gebruiken:

* Het verdelen van inkomend internetverkeer over uw virtuele machines (VM's). Deze configuratie wordt ook wel een [openbare Load Balancer](#publicloadbalancer) genoemd.
* Het verdelen van verkeer over VM's binnen een virtueel netwerk. In een hybride scenario kunt u een front-end van een Load Balancer ook bereiken vanaf een on-premises netwerk. Beide scenario's gebruiken een configuratie die bekend staat als een [interne Load Balancer](#internalloadbalancer).
* Voor Port Forwarding-verkeer naar een specifieke poort op specifieke virtuele machines met regels voor binnenkomende NAT-regels (Network Address Translation).
* Voor het leveren van [uitgaande connectiviteit](load-balancer-outbound-connections.md) voor VM's binnen uw virtuele netwerk door gebruik te maken van een openbare Load Balancer.


>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. Als u op zoek bent naar beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Application Gateway](../application-gateway/application-gateway-introduction.md). Raadpleeg [Traffic Manager](../traffic-manager/traffic-manager-overview.md) als u op zoek bent naar wereldwijde DNS-taakverdeling. Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.

## <a name="what-are-load-balancer-resources"></a>Wat zijn Load Balancer-resources?

Een Load Balancer-resource kan voorkomen als een openbare Load Balancer of een interne Load Balancer. De functies van een Load Balancer-resource worden uitgedrukt als een gedefinieerde front-end, regel, statuscontrole of back-endpool. U plaatst virtuele machines in de back-endpool door de back-endpool van de virtuele machine op te geven.

Load Balancer-resources zijn objecten waarmee u kunt uitdrukken hoe Azure de infrastructuur met meerdere tenants moet programmeren om het scenario te realiseren dat u wilt maken. Er is geen directe relatie tussen Load Balancer-resources en de daadwerkelijke infrastructuur. Als een Load Balancer wordt gemaakt, wordt er geen instantie gemaakt en capaciteit is altijd beschikbaar. 

## <a name="fundamental-load-balancer-features"></a>Fundamentele functies van Load Balancer

Load Balancer biedt de volgende fundamentele mogelijkheden voor TCP en UDP-toepassingen:

* **Taakverdeling**

    Met Azure Load Balancer kunt u een taakverdelingsregel maken voor het verdelen van verkeer dat op instanties van front-end-naar-back-endpools binnenkomt. Load Balancer maakt gebruik van een op hash gebaseerd algoritme voor de verdeling van inkomende stromen en herschrijft de headers van stromen naar instanties van back-end-pools dienovereenkomstig. Er is een server is beschikbaar voor het ontvangen van nieuwe stromen als een statustest aangeeft dat het back-endeindpunt in orde is.
    
    Load Balancer gebruikt standaard een hash met vijf tuples, die is samengesteld uit bron-IP-adres, bronpoort, doel-IP-adres, doelpoort en nummer van het IP-protocol, om stromen toe te wijzen aan de beschikbare servers. U kunt kiezen voor affiniteit met een specifiek bron-IP-adres door te kiezen voor een bepaalde regel bij een hash met twee of drie tuples. Alle pakketten van dezelfde pakketstroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast. Als de client een nieuwe stroom initieert vanaf hetzelfde bron-IP, wordt de bronpoort gewijzigd. Als gevolg hiervan kunnen de vijf tuples ertoe leiden dat het verkeer naar een ander back-endeindpunt gaat.

    Zie [Distributiemodus voor Load Balancer](load-balancer-distribution-mode.md) voor meer informatie. Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

    ![Hash-distributie](./media/load-balancer-overview/load-balancer-distribution.png)

    *Afbeelding: Hash-distributie*

* **Port Forwarding**

    Met Load Balancer kunt u een inkomende NAT-regel maken om verkeer met Port Forwarding vanaf een specifieke poort van een specifiek front-end-IP-adres door te sturen naar een specifieke back-endinstantie binnen het virtuele netwerk. Dit wordt ook bereikt door de dezelfde hash-distributie als taakverdeling. Veelvoorkomende scenario's voor deze mogelijkheid zijn RDP- (Remote Desktop Protocol) of SSH-sessies (Secure Shell) op afzonderlijke VM-instanties binnen Azure Virtual Network. U kunt meerdere interne eindpunten toewijzen aan de verschillende poorten op hetzelfde front-end-IP-adres. U kunt de front-end-IP-adressen gebruiken voor het extern beheren van uw VM's via internet zonder dat u een extra jump-box nodig hebt.

* **Niet toepassingsgebonden en transparant**

    Load Balancer communiceert niet rechtstreeks met TCP of UDP of met de toepassingslaag, en elk scenario met TCP- of UDP-toepassingen kan worden ondersteund.  Load Balancer beëindigt geen stromen en start deze niet, heeft geen interactie met de nettolading van de stroom, biedt geen gatewayfunctie voor de toepassingslaag, en protocol-handshakes vinden altijd rechtstreeks plaats de tussen de client en de instantie van de back-endpool.  Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine.  Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.  Een aantal voorbeelden die transparantie verder illustreren:
    - Elk eindpunt wordt alleen beantwoord door een virtuele machine.  Zo treedt een TCP-handshake bijvoorbeeld op tussen de client en de geselecteerde back-end-VM.  Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end valideert, valideert u de end-to-end-connectiviteit met ten minste één back-end-VM.
    - Nettoladingen van toepassingen zijn transparant voor de Load Balancer en alle UDP- of TCP-toepassingen kunnen worden ondersteund. Voor workloads waarvoor een verwerking per HTTP-aanvraag of manipulatie van nettoladingen van de toepassingslaag is vereist (bijvoorbeeld bij het parseren van HTTP-URL's), moet u een Layer 7 Load Balancer gebruiken, zoals [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Omdat Load Balancer onafhankelijk is van de TCP-nettolading en TLS-offload ('SSL') niet is opgegeven, kunt u end-to-end versleutelde scenario's maken met behulp van Load Balancer, en een grote uitschaling voor TLS-toepassingen realiseren door de TLS-verbinding op de virtuele machine zelf te beëindigen.  De sleutelcapaciteit van de TLS-sessie wordt bijvoorbeeld alleen beperkt door het type en het aantal VM's dat u aan de back-endpool toevoegt.  Als u 'SSL-offloading', de behandeling als toepassingslaag, vereist of het certificaatbeheer wilt delegeren aan Azure, moet u in plaats daarvan de Layer 7 Load Balancer [Application Gateway](https://azure.microsoft.com/services/application-gateway) gebruiken.
        

* **Automatische herconfiguratie**

    Load Balancer herconfigureert zichzelf onmiddellijk wanneer u instanties omhoog of omlaag schaalt. Als u virtuele machines toevoegt aan de back-endpool of eruit verwijdert, wordt de Load Balancer opnieuw geconfigureerd zonder dat er aanvullende bewerkingen op de Load Balancer-resource worden uitgevoerd.

* **Statuscontroles**

    Load Balancer maakt gebruik van statuscontroles die door u zijn gedefinieerd, om de status van instanties in de back-endpool te bepalen. Wanneer een test niet reageert, stopt de Load Balancer met het verzenden van nieuwe verbindingen naar de slechte instanties. Bestaande verbindingen worden niet beïnvloed, en zij blijven intact totdat de stroom door de toepassing wordt beëindigd, er een time-out wegens inactiviteit optreedt of de virtuele machine wordt afgesloten.
     
    Load Balancer biedt [verschillende typen statuscontroles](load-balancer-custom-probe-overview.md#types) voor TCP-, HTTP- en HTTPS-eindpunten.

    Daarnaast is er een aanvullend type toegestaan wanneer u klassieke cloudservices gebruikt:  [Gastagent](load-balancer-custom-probe-overview.md#guestagent).  Dit moet worden gezien als een statuscontrole die echter pas in laatste instantie moet worden gebruikt, en die niet wordt aanbevolen als andere opties voorhanden zijn.
    
* **Uitgaande verbindingen (SNAT)**

    Alle uitgaande stromen van privé-IP-adressen in uw virtuele netwerk naar openbare IP-adressen op internet, kunnen worden omgezet in een front-end-IP-adres van de Load Balancer. Wanneer een openbare front-end aan een back-end-VM is gekoppeld via een taakverdelingsregel, worden in Azure uitgaande verbindingen geprogrammeerd om automatisch te worden omgezet in het openbare front-end-IP-adres.

  * Eenvoudig upgraden en herstellen na noodgevallen van services is mogelijk, omdat de front-end dynamisch kan worden toegewezen aan een andere instantie van de service.
  * Gemakkelijker beheer van toegangsbeheerlijsten( ACL). ACL's die worden uitgedrukt in de vorm van front-end-IP-adressen, veranderen niet als services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd.  Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan er machines zijn, kan de belasting die door opname in whitelists optreedt, reduceren.

    Zie [Uitgaande verbindingen](load-balancer-outbound-connections.md) voor meer informatie.

Standaard Load Balancer beschikt over extra mogelijkheden die specifiek zijn voor SKU's buiten deze basismogelijkheden. Lees de rest van dit artikel voor meer informatie.

## <a name="skus"></a> Vergelijking van Load Balancer-SKU's

Load Balancer biedt ondersteuning voor Basic- en Standard-SKU's, die van elkaar verschillen wat betreft de schaal van het scenario, functies en prijzen. Elk scenario dat mogelijk is met de Basic-versie van Load Balancer kan worden gemaakt met de Standard-versie ervan. De API's voor beide SKU's zijn in feite vergelijkbaar en worden aangeroepen via de specificatie van een SKU. De API voor de ondersteuning van SKU's voor de Load Balancer en het openbare IP-adres is beschikbaar met ingang van de API-versie 2017-08-01. Beide SKU's hebben de dezelfde algemene API en structuur.

Echter, afhankelijk van welke SKU u kiest, de configuratie van het volledige scenario is mogelijk enigszins anders. In de documentatie voor Load Balancer is duidelijk aangegeven wanneer een artikel alleen voor een specifieke SKU geldt. Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [Overzicht van Standard Load Balancer](load-balancer-standard-overview.md) voor meer informatie.

>[!NOTE]
> Nieuwe ontwerpen moeten Standard Load Balancer kunnen faciliteren. 

Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. Als u ze met openbare IP-adressen gebruikt, moeten zowel Load Balancer als het openbare IP-adres SKU overeenkomen. Load Balancer en openbare IP SKU's zijn niet veranderlijk.

_Het is een aanbevolen procedure om de SKU's expliciet op te geven, zelfs als dit nog niet is vereist._  Op dit moment worden vereiste wijzigingen tot een minimum beperkt. Als een SKU niet wordt opgegeven, wordt dit geïnterpreteerd als een intentie om de API-versie 2017-08-01-API-van de Basic-SKU te gebruiken.

>[!IMPORTANT]
>Standard Load Balancer is een nieuw Load Balancer-product en is grotendeels een superset van Basic Load Balancer. Er zijn belangrijke en doelbewuste verschillen tussen de twee producten. Elk end-to-end scenario dat mogelijk is met Basic Load Balancer kan worden gemaakt met Standard Load Balancer. Als u al vertrouwd bent met Basic Load Balancer, moet u zich vertrouwd maken met Standard Load Balancer om de meest recente wijzigingen in werking tussen Standard en Basic en hun invloed te kunnen begrijpen. Lees deze sectie aandachtig.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Zie [Servicelimieten voor Load Balancer](https://aka.ms/lblimits) voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name="concepts"></a>Concepten

### <a name = "publicloadbalancer"></a>Openbare Load Balancer

Een openbare Load Balancer wijst het openbare IP-adres en poortnummer van het inkomende verkeer toe aan het privé-IP-adres en poortnummer van de virtuele machine, en vice versa voor het antwoordverkeer van de virtuele machine. Door het toepassen van regels voor taakverdeling, kunt u specifieke soorten verkeer verdelen voor meerdere virtuele machines of services. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.

Op de volgende afbeelding ziet u een eindpunt met taakverdeling voor webverkeer dat wordt gedeeld door drie virtuele machines voor de openbare en de TCP-poort 80. Deze drie VM's maken deel uit van een set met taakverdeling.

![Voorbeeld van een openbare Load Balancer](./media/load-balancer-overview/IC727496.png)

*Afbeelding: Taakverdeling toepassen op webverkeer met behulp van een openbare load balancer*

Als internetclients aanvragen voor webpagina's verzenden naar het openbare IP-adres van een web-app op TCP-poort 80, worden de aanvragen in Azure Load Balancer verspreid over de drie virtuele machines in de set met taakverdeling. Zie de sectie [Load Balancer-functies](load-balancer-overview.md##fundamental-load-balancer-features) van dit artikel voor meer informatie over Load Balancer-algoritmen.

Standaard verdeelt Azure Load Balancer netwerkverkeer evenredig over meerdere VM-instanties. U kunt ook sessieaffiniteit configureren. Zie [Distributiemodus voor Load Balancer](load-balancer-distribution-mode.md) voor meer informatie.

### <a name = "internalloadbalancer"></a> Interne Load Balancer

Een interne Load Balancer zorgt ervoor dat verkeer alleen naar resources wordt geleid die zich binnen een virtueel netwerk bevinden, of die een VPN-verbinding gebruiken om toegang tot Azure-infrastructuur te krijgen. In dat opzicht wijkt een interne Load Balancer af van een openbare Load Balancer. Azure-infrastructuur beperkt de toegang tot de front-end-IP-adressen met taakverdeling van een virtueel netwerk. Front-end-IP-adressen en virtuele netwerken communiceren nooit rechtstreeks met een interneteindpunt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd.

Met een interne Load Balancer zijn de volgende typen taakverdeling mogelijk:

* **In een virtueel netwerk**: Taakverdeling vanaf virtuele machines in het virtuele netwerk voor een verzameling virtuele machines die zich in hetzelfde virtuele netwerk bevindt.
* **Voor een virtueel netwerk met verschillende locaties**: Taakverdeling vanaf on-premises computers voor een verzameling virtuele machines die zich in hetzelfde virtuele netwerk bevindt. 
* **Voor toepassingen met meerdere lagen**: Taakverdeling voor internetgerichte toepassingen met meerdere lagen waarin de back-endlagen niet internetgericht zijn. De back-end-lagen vereisen verkeer met taakverdeling vanuit de internetgerichte laag (zie de volgende afbeelding).
* **Voor Line-Of-Business-toepassingen**: Taakverdeling voor Line-Of-Business-toepassingen die worden gehost in Azure zonder extra load balancer-hardware of -software. Dit scenario geldt ook voor on-premises servers die deel uitmaken van de set computers waarvan taakverdeling op het verkeer is toegepast.

![Voorbeeld van een interne Load Balancer](./media/load-balancer-overview/IC744147.png)

*Afbeelding: Taakverdeling die wordt toegepast op toepassingen met meerdere lagen waarvoor zowel een openbare als een interne load balancer wordt gebruikt*

## <a name="pricing"></a>Prijzen

Gebruik van Standard Load Balancer wordt in rekening gebracht.

- Aantal geconfigureerde load-balancing- en uitgaande regels (inkomende NAT-regels worden niet meegeteld in het totale aantal regels)
- Hoeveelheid verwerkte inkomende en uitgaande gegevens, ongeacht de regel. 

Ga naar de pagina [Prijs van Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/) voor informatie over de prijs van Standard Load Balancer.

Basic Load Balancer wordt gratis aangeboden.

## <a name="sla"></a>SLA

Ga naar de pagina [Load Balancer-SLA](https://aka.ms/lbsla) voor informatie over de Standard Load Balancer-SLA. 

## <a name="limitations"></a>Beperkingen

- Load Balancer is een TCP- of UDP-product voor taakverdeling en Port Forwarding voor deze specifieke IP-protocollen.  Taakverdelingsregels en inkomende NAT-regels worden ondersteund voor TCP en UDP en worden niet ondersteund voor andere IP-protocollen, met inbegrip van ICMP. Load Balancer beëindigt de nettolading van een UDP- of TCP-stroom niet, reageert er niet op of communiceert er op geen enkele andere ander manier mee. Het is geen proxy. Succesvolle validatie van connectiviteit naar een front-end moet in-band worden uitgevoerd met hetzelfde protocol (TCP of UDP) dat is gebruikt in een NAT-regel met taakverdeling of een inkomende NAT-regel, _en_ ten minste één van uw virtuele machines moet een antwoord genereren voor een client om een antwoord van een front-end te kunnen zien.  Als er geen in-band-reactie van de Load Balancer-front-end wordt ontvangen, geeft dat aan dat geen virtuele machine heeft kunnen antwoorden.  Het is niet mogelijk om te communiceren met een Load Balancer-front-end als een virtuele machine niet kan antwoorden.  Dit geldt ook voor uitgaande verbindingen waar [poortmaskering SNAT](load-balancer-outbound-connections.md#snat) alleen wordt ondersteund voor TCP en UDP; andere IP-protocollen, waaronder ICMP, werken ook niet.  Wijs een openbaar IP-adres op instantieniveau toe om dit op te lossen.
- In tegenstelling tot openbare Load Balancers die [uitgaande verbindingen](load-balancer-outbound-connections.md) bieden bij het overstappen van privé-IP-adressen binnen het virtuele netwerk naar openbare IP-adressen, zetten interne Load Balancers uitgaande verbinding niet om in de front-end van een interne Load Balancer omdat beide zich in privé-IP-adresruimte bevinden.  Hiermee voorkomt u mogelijke SNAT-poortuitputting binnen unieke interne IP-adresruimte waarvoor omzetting niet vereist is.  Het neveneffect hiervan is dat, als een uitgaande stroom vanaf een virtuele machine in de back-endpool een stroom genereert naar de front-end van de interne Load Balancer in wiens groep deze zich bevindt _en_ terug wordt toegewezen naar zichzelf, beide vertakkingen van de stroom niet overeenkomen en de stroom mislukt.  Als de stroom niet terugverwijst naar dezelfde virtuele machine in de back-endpool waar de stroom naar de front-end is gemaakt, lukt de stroom wel.   Wanneer de stroom is toegewezen aan zichzelf, lijkt het of de uitgaande stroom afkomstig is van de virtuele machine aan de front-end en lijkt de bijbehorende inkomende stroom afkomstig te zijn van de virtuele machine naar zichzelf. Vanuit het oogpunt van het gastbesturingssysteem gezien, komen de inkomende en uitgaande delen van dezelfde flow in de virtuele machine niet overeen. De TCP-stack herkent deze niet als twee helften van dezelfde stroom, omdat de bron en de bestemming niet overeenkomen.  Wanneer de stroom wordt toegewezen aan een andere VM in de back-endpool, komen de helften van de stroom overeen en kan de virtuele machine reageren.  Dit scenario manifesteert zich als onregelmatige time-outs van de verbinding wanneer de stroom terugkeert naar dezelfde back-end waarvan de stroom afkomstig was. Er zijn enkele tijdelijke oplossingen om op een betrouwbare manier tot dit scenario te komen (waarbij stromen worden gegenereerd vanaf een back-endpool naar de back-endpools of respectievelijke interne Load Balancer-front-end), waaronder het invoegen van een proxylaag achter de interne Load Balancer of door [gebruik te maken van de DSR-stijlregels](load-balancer-multivip-overview.md).  Klanten kunnen een interne Load Balancer combineren met een proxy van een externe partij of met vervanging voor de interne [Application Gateway](../application-gateway/application-gateway-introduction.md) voor scenario's met proxy's die zijn beperkt tot HTTP/HTTPS. Als oplossing hiervoor zou u een openbare Load Balancer kunnen gebruiken, maar het scenario dat daar het resultaat van is, is gevoelig voor [SNAT-uitputting](load-balancer-outbound-connections.md#snat) en moet worden vermeden, tenzij dit nauwkeurig wordt beheerd.
- Over het algemeen wordt het door sturen van IP-fragmenten of het uitvoeren van IP-fragmentatie van UDP-en TCP-pakketten niet ondersteund op taakverdelings regels.  De taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md) vormen een uitzonde ring op deze algemene instructie en kunnen worden gebruikt voor het door sturen van bestaande IP-fragmenten.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een algemeen beeld van Azure Load Balancer. Om een Load Balancer te kunnen gebruiken, moet u er eerst een maken, maakt u vervolgens virtuele machines met daarop een aangepaste IIS-extensie geïnstalleerd, en past u taakverdeling toe op de web-app tussen de VM's. Zie de snelstart[Een Basic Load Balancer maken](quickstart-create-basic-load-balancer-portal.md) voor meer informatie.
