---
title: Onderdelen en beperkingen
titleSuffix: Azure Load Balancer
description: Overzicht van Azure Load Balancer onderdelen en beperkingen.
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
ms.openlocfilehash: aab6a4de7be57df1f691861533a4528a0bcae571
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241680"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer onderdelen en beperkingen
Azure Load Balancer bevat verschillende belang rijke onderdelen voor de bewerking.  Deze onderdelen kunnen worden geconfigureerd in uw abonnement via de Azure Portal, Azure CLI of Azure PowerShell.  

## <a name="load-balancer-components"></a>Load Balancer onderdelen

* Front **-end-IP-configuraties**: het IP-adres van de Load Balancer. Het is het contact punt voor clients. Deze adressen kunnen een van de volgende zijn: 

    - **[Openbaar IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Privé-IP-adres](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Back-end-pool**: de groep virtuele machines of instanties in de schaalset voor virtuele machines die de inkomende aanvraag gaat verwerken. Om de kosten effectief te schalen om te voldoen aan grote volumes van binnenkomend verkeer, raden computer richtlijnen meestal toe om meer exemplaren toe te voegen aan de back-end-groep. Load Balancer wordt direct opnieuw geconfigureerd via automatische herconfiguratie wanneer u instanties omhoog of omlaag schaalt. Als u Vm's toevoegt aan of verwijdert uit de back-end-groep, worden de Load Balancer opnieuw geconfigureerd zonder dat er extra bewerkingen worden uitgevoerd. Het bereik van de back-end-groep is een virtuele machine in het virtuele netwerk. Een back-end-pool kan Maxi maal 1000 back-end-exemplaren of IP-configuraties hebben.
Basic load balancers hebben een beperkt bereik (beschikbaarheidsset) en kunnen Maxi maal 300 IP-configuraties worden geschaald. Zie [Load Balancer limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)voor meer informatie over limieten. Wanneer u overweegt om uw back-end-pool te ontwerpen, kunt u het minste aantal afzonderlijke back-endservers bronnen ontwerpen om de duur van beheer bewerkingen verder te optimaliseren. Er is geen verschil in de prestaties of schaal van het gegevens vlak.
* **Status tests**: een **[status test](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** wordt gebruikt om de status van de exemplaren in de back-end-pool te bepalen. U kunt de drempel waarde voor de status van uw Health-tests definiëren. Wanneer een test niet reageert, stopt de Load Balancer met het verzenden van nieuwe verbindingen naar de slechte instanties. Een test fout heeft geen invloed op bestaande verbindingen. 
    
    De verbinding wordt vervolgd totdat de toepassing: 
    - Hiermee wordt de stroom beëindigd
    - Time-out voor inactiviteit treedt op
    - De VM wordt afgesloten

    Load Balancer biedt verschillende typen Health probe voor eind punten:
    - TCP
    - HTTP
    - HTTPS (HTTP-test met Transport Layer Security (TLS) wrapper)
     
     De Basic-Load Balancer biedt geen ondersteuning voor HTTPS-tests. Daarnaast worden alle TCP-verbindingen (inclusief tot stand gebrachte verbindingen) door basis Load Balancer beëindigd. 
    Zie [test typen](load-balancer-custom-probe-overview.md#types)voor meer informatie.

* Taakverdelings **regels**: taak verdelings regels zijn het Load Balancer wat moet worden gedaan wanneer. 
* **Binnenkomende NAT-regels**: een binnenkomende NAT-regel stuurt verkeer van een specifieke poort van een specifiek frontend-IP-adres naar een specifieke poort van een specifiek back-end-exemplaar in het virtuele netwerk. Het **[door sturen van poorten](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** wordt uitgevoerd door dezelfde hash-gebaseerde distributie als taak verdeling. Veelvoorkomende scenario's voor deze mogelijkheid zijn Remote Desktop Protocol (RDP) of SSH-sessies (Secure Shell) voor afzonderlijke VM-exemplaren in een Azure Virtual Network. U kunt meerdere interne eind punten toewijzen aan poorten op hetzelfde front-end-IP-adres. U kunt de front-end-IP-adressen gebruiken om uw Vm's extern te beheren zonder een extra Jump box.
* **Uitgaande regels**: met een **[uitgaande regel](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** wordt de uitgaande netwerkadresomzetting (NAT) geconfigureerd voor alle virtuele machines of exemplaren die worden geïdentificeerd door de back-end-pool van uw Standard Load Balancer die moet worden vertaald naar de front-end.
Basic Load Balancer biedt geen ondersteuning voor uitgaande regels.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name = "load-balancer-concepts"></a>Load Balancer concepten

Load Balancer biedt de volgende fundamentele mogelijkheden voor TCP en UDP-toepassingen:

* **Algoritme voor taak verdeling**: met Azure Load Balancer kunt u een taakverdelings regel maken voor het distribueren van verkeer dat binnenkomt bij de frontend van exemplaren van back-endservers. Load Balancer gebruikt een hash-algoritme voor de distributie van binnenkomende stromen (niet bytes) en schrijft de headers van stromen naar back-endadresgroep. Er is een server beschikbaar om nieuwe stromen te ontvangen wanneer een status test een gezonde back-end-eind punt aangeeft.
Load Balancer maakt standaard gebruik van een hash van 5-tuple. 

   De hash bevat: 

   - **IP-adres van bron**
   - **Bron poort**
   - **Doel-IP-adres**
   - **Doel poort**
   - **IP-protocol nummer voor het toewijzen van stromen aan beschik bare servers** 

U kunt affiniteit maken met een IP-bron adres door een twee-of 3-tuple-hash voor een bepaalde regel te gebruiken. Alle pakketten van dezelfde pakketstroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast. Wanneer de client een nieuwe stroom start vanuit hetzelfde bron-IP-adres, wordt de bron poort gewijzigd. Als gevolg hiervan kan de 5-tuple-hash ertoe leiden dat het verkeer naar een ander back-end-eind punt gaat.
Zie [de distributie modus configureren voor Azure Load Balancer](./load-balancer-distribution-mode.md)voor meer informatie. 

Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Hash-distributie">
</p>

  *Afbeelding: hash-distributie*

* **Application onafhankelijkheid en transparantie**: Load Balancer niet rechtstreeks interactie met TCP of UDP of de toepassingslaag. Elk TCP-of UDP-toepassings scenario kan worden ondersteund. Load Balancer geen stromen afbreekt of als deze niet afkomstig zijn, communiceren met de payload van de stroom of een functie voor de gateway van de toepassingslaag. Protocol-Handshakes worden altijd direct uitgevoerd tussen de client en het exemplaar van de back-end-pool. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.
  * Elk eindpunt wordt alleen beantwoord door een virtuele machine. Zo vindt er altijd een TCP-handshake plaats tussen de client en de geselecteerde back-end-VM. Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end hebt gevalideerd, valideert u de end-to-end-connectiviteit met ten minste één virtuele machine van de back-end.
  * Toepassings payloads zijn transparant voor Load Balancer. Elke UDP-of TCP-toepassing kan worden ondersteund.
  * Omdat Load Balancer niet met de TCP-nettolading communiceert en TLS-offload levert, kunt u end-to-end versleutelde scenario's bouwen. Het gebruik van Load Balancer grote hoeveel heden uitschalen voor TLS-toepassingen door de TLS-verbinding op de VM zelf te beëindigen. Uw TLS-sessie sleutel kan bijvoorbeeld alleen worden beperkt door het type en het aantal Vm's dat u toevoegt aan de back-end-pool.

* **Uitgaande verbindingen**: alle uitgaande stromen van privé-IP-adressen in uw virtuele netwerk naar open bare IP-adressen op internet kunnen worden omgezet naar een front-end-IP-adres van de Load Balancer. Wanneer een open bare front-end is gekoppeld aan een back-end-VM met behulp van een taakverdelings regel, vertaalt Azure uitgaande verbindingen met het open bare frontend-IP-adres. Deze configuratie heeft de volgende voor delen:
  * Eenvoudige upgrade en nood herstel van services, omdat de front-end dynamisch kan worden toegewezen aan een ander exemplaar van de service.
  * Eenvoudig beheer van toegangs beheer lijst (ACL). Acl's die worden weer gegeven als front-end Ip's worden niet gewijzigd wanneer Services omhoog of omlaag worden geschaald of opnieuw worden geïmplementeerd. Het omzetten van uitgaande verbindingen naar een kleiner aantal IP-adressen dan computers vermindert de last van het implementeren van lijsten met veilige geadresseerden.

  Standard Load Balancer maakt gebruik van een [robuust, schaalbaar en voorspelbaar algoritme](load-balancer-outbound-connections.md#snat). Dit zijn de belangrijkste grond beginselen die u kunt onthouden wanneer u met Standard Load Balancer werkt:

    - taakverdelings regels bepalen hoe SNAT wordt geprogrammeerd. Taakverdelings regels zijn specifiek voor het protocol. SNAT is protocol-specifiek en configuratie moet weer spie gelen in plaats van een neven effect te maken.

    - **Meerdere frontends** Wanneer er meerdere frontends beschikbaar zijn, worden alle frontends gebruikt en elke frontend vermenigvuldigt het aantal beschik bare SNAT-poorten. Als u meer SNAT-poorten verwacht of als er al een hoge vraag voor uitgaande verbindingen optreedt, kunt u ook incrementele SNAT-poort inventaris toevoegen door extra frontends, regels en back-endservers op dezelfde virtuele machine te configureren. resources.

    - **Bepalen welke frontend wordt gebruikt voor uitgaande verbindingen** U kunt kiezen en bepalen of u niet wilt dat een bepaalde frontend voor uitgaande verbindingen wordt gebruikt. Als u uitgaande verbindingen wilt beperken tot alleen afkomstig van een specifiek frontend-IP-adres, kunt u de uitgaande SNAT uitschakelen op de regel die de uitgaande toewijzing uitdrukt.

    - Uitgaande **connectiviteits scenario's voor uitgaande verbindingen** zijn expliciete en uitgaande connectiviteit bestaat niet totdat deze is opgegeven. Standard Load Balancer bestaat in de context van het virtuele netwerk.  Een virtueel netwerk is een geïsoleerd particulier netwerk.  Tenzij er een koppeling met een openbaar IP-adres bestaat, is de open bare verbinding niet toegestaan.  U kunt de [VNet-service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) bereiken omdat ze zich in en lokaal voor uw virtuele netwerk bevinden.  Als u een uitgaande verbinding wilt maken met een bestemming buiten uw virtuele netwerk, hebt u twee opties:
        - Wijs een openbaar IP-adres van een standaard-SKU toe als openbaar IP-adres op exemplaar niveau voor de virtuele-machine bron of
        - plaats de bron van de virtuele machine in de back-endadresgroep van een open bare Standard Load Balancer.

        Beide zorgt ervoor dat uitgaande verbindingen van het virtuele netwerk naar buiten het virtuele netwerk. 

        Als u _alleen_ een interne Standard Load Balancer hebt gekoppeld aan de back-end-groep waarin de virtuele-machine bron zich bevindt, kan uw virtuele machine alleen virtuele netwerk bronnen en [VNet-service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)bereiken.  U kunt de stappen in de vorige alinea volgen om uitgaande connectiviteit te maken.

        De uitgaande verbinding van een virtuele-machine resource die niet is gekoppeld aan standaard-Sku's, blijft net zo lang.

        Bekijk de [gedetailleerde beschrijving van uitgaande verbindingen](load-balancer-outbound-connections.md).

* **Beschikbaarheidszones**: Standard Load Balancer ondersteunt extra mogelijkheden in regio's waar Beschikbaarheidszones beschikbaar zijn. Deze functies zijn incrementeel voor alle Standard Load Balancer.  Beschikbaarheidszones configuraties zijn beschikbaar voor beide typen, open bare en interne Standard Load Balancer.
 Een zone-redundante front-end bevindt zich in de zone storing en wordt door de toegewezen infra structuur in alle zones tegelijk bediend. 
Daarnaast kunt u een frontend garanderen voor een specifieke zone. Een zonegebonden-front-end-shares worden meegenomen met de betreffende zone en worden alleen geleverd door een toegewezen infra structuur in één zone.
Taak verdeling in meerdere zones is beschikbaar voor de back-end-groep en alle virtuele-machine bronnen in een virtueel netwerk kunnen deel uitmaken van een back-end-groep.
Basis Load Balancer biedt geen ondersteuning voor zones.
Raadpleeg de [gedetailleerde beschrijving van Beschikbaarheidszones gerelateerde vaardig heden](load-balancer-standard-availability-zones.md) en [Beschikbaarheidszones overzicht](../availability-zones/az-overview.md) voor meer informatie.

* **Ha-poorten**: u kunt regels voor taak verdeling configureren om de schaal van uw toepassing te laten opschalen en zeer betrouwbaar te maken. Wanneer u een regel voor taak verdeling van HA-poorten gebruikt, levert Standard Load Balancer per stroom taak verdeling op elke tijdelijke poort van het front-end-IP-adres van een interne Standard Load Balancer.  De functie is handig voor andere scenario's waarbij het niet praktisch is of niet wenselijk is om afzonderlijke poorten op te geven. Met een taakverdelings regel voor HA-poorten kunt u Active-passieve of actief-actief n + 1-scenario's maken voor virtuele netwerk apparaten en voor elke toepassing die grote bereiken aan binnenkomende poorten vereist.  Een status test kan worden gebruikt om te bepalen welke back-ends nieuwe stromen moeten ontvangen.  U kunt een netwerk beveiligings groep gebruiken om een scenario met een poort bereik te emuleren. Basic Load Balancer biedt geen ondersteuning voor HA-poorten.
[Gedetailleerde bespreking van ha-poorten](load-balancer-ha-ports-overview.md) bekijken
>[!IMPORTANT]
> Als u van plan bent om een virtueel netwerk apparaat te gebruiken, raadpleegt u uw leverancier voor meer informatie over of het product is getest met HA-poorten en volgt u de specifieke richt lijnen voor de implementatie. 

* **Meerdere frontends**: Load Balancer ondersteunt meerdere regels met meerdere frontends.  Standard Load Balancer breidt deze uit naar uitgaande scenario's.  Uitgaande scenario's zijn in feite de inverse van een regel voor binnenkomende taak verdeling.  Met de regel voor inkomende taak verdeling wordt ook een koppeling voor uitgaande verbindingen gemaakt. Standard Load Balancer gebruikt alle frontends die zijn gekoppeld aan een virtuele-machine bron via een taakverdelings regel.  Daarnaast kunt u met een para meter voor de taakverdelings regel een taakverdelings regel voor de doel einden van de uitgaande connectiviteit onderdrukken, waardoor de selectie van specifieke front-ends inclusief geen kan worden onderdrukt.

Voor een vergelijking selecteert basis Load Balancer één wille keurige front-end en er is geen mogelijkheid om te bepalen welk item is geselecteerd.
## <a name="load-balancer-types"></a>Load Balancer typen

### <a name = "publicloadbalancer">Openbare Load Balancer</a>

Een openbaar Load Balancer wijst het open bare IP-adres en de poort van binnenkomend verkeer toe aan het privé-IP-adres en de poort van de virtuele machine. Load Balancer wijst verkeer op de andere manier toe voor het antwoord verkeer van de virtuele machine. U kunt specifieke typen verkeer distribueren over meerdere Vm's of services door regels voor taak verdeling toe te passen. U kunt bijvoorbeeld de werkbelasting door webverkeeraanvragen over meerdere webservers spreiden.

>[!NOTE]
>U kunt slechts één openbaar Load Balancer en één interne Load Balancer implementeren per beschikbaarheidsset.

In de volgende afbeelding ziet u een eind punt met gelijke taak verdeling voor Internet verkeer dat wordt gedeeld door drie Vm's voor de open bare en TCP-poort 80. Deze drie VM's maken deel uit van een set met taakverdeling.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Open bare load balancer">
</p>

*Afbeelding: webverkeer balanceren met behulp van een open bare load balancer*

Internetclients verzenden webpagina aanvragen naar het open bare IP-adres van een web-app op TCP-poort 80. Azure Load Balancer distribueert de aanvragen over de drie Vm's in de set met gelijke taak verdeling. Zie [Load Balancer-concepten](concepts-limitations.md#load-balancer-concepts)voor meer informatie over Load Balancer-algoritmen.

Azure Load Balancer netwerk verkeer wordt standaard gelijkmatig verdeeld over meerdere VM-exemplaren. U kunt ook sessieaffiniteit configureren. Zie [de distributie modus configureren voor Azure Load Balancer](load-balancer-distribution-mode.md)voor meer informatie.

### <a name = "internalloadbalancer"></a> Interne Load Balancer

Een interne load balancer stuurt alleen verkeer naar bronnen die zich binnen een virtueel netwerk bevinden of die een VPN gebruiken voor toegang tot de Azure-infra structuur, in tegens telling tot een open bare load balancer. De Azure-infra structuur beperkt de toegang tot de front-end IP-adressen met taak verdeling van een virtueel netwerk. Front-end-IP-adressen en virtuele netwerken worden nooit rechtstreeks blootgesteld aan een Internet-eind punt. Interne Line-Of-Business-toepassingen worden in Azure uitgevoerd en worden vanuit Azure of vanaf on-premises resources benaderd.

Met een interne Load Balancer zijn de volgende typen taakverdeling mogelijk:

* **Binnen een virtueel netwerk**: taak verdeling van virtuele machines in het virtuele netwerk naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor een cross-premises virtueel netwerk**: taak verdeling van on-premises computers naar een set vm's die zich in hetzelfde virtuele netwerk bevinden.
* **Voor toepassingen met meerdere lagen**: taak verdeling voor toepassingen met meerdere lagen op internet waarbij de back-endservers niet op internet zijn gericht. Voor de back-end-lagen is taak verdeling van verkeer van de Internet gerichte laag vereist. Zie de volgende afbeelding.
* **Voor Line-Of-Business-toepassingen**: taakverdeling voor Line-Of-Business-toepassingen die worden gehost in Azure zonder extra load balancer-hardware of -software. Dit scenario bevat on-premises servers die zich in de set computers bevinden waarvan het verkeer wordt verdeeld.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Open bare load balancer">
</p>

*Afbeelding: toepassingen met meerdere lagen verdelen met behulp van zowel open bare als interne Load Balancer*

## <a name="skus"></a> Vergelijking van Load Balancer-SKU's

De Load Balancer ondersteunt zowel de basis-als standaard-Sku's. Deze Sku's wijken af van de schaal, functies en prijzen van het scenario. Elk scenario dat mogelijk is met basis Load Balancer kan worden gemaakt met Standard Load Balancer. De Api's voor beide Sku's zijn vergelijkbaar en worden aangeroepen via de specificatie van een SKU. De API voor de ondersteuning van Sku's voor load balancer en het open bare IP-adres is beschikbaar vanaf de `2017-08-01`-API. Beide Sku's delen dezelfde algemene API en structuur.

De volledige scenario configuratie kan enigszins verschillen, afhankelijk van de SKU. De Load Balancer-documentatie wordt aangeroepen wanneer een artikel alleen van toepassing is op een specifieke SKU. Als u de verschillen met elkaar wilt vergelijken en wilt weten wat ze precies inhouden, kunt u de volgende tabel raadplegen. Zie [overzicht van Azure Standard Load Balancer](load-balancer-standard-overview.md)voor meer informatie.

>[!NOTE]
> Micro soft raadt aan Standard Load Balancer.
Zelfstandige virtuele machines, beschikbaarheidssets en virtuele-machineschaalsets kunnen worden verbonden met slechts één SKU, niet met beide. Load Balancer en de SKU van het open bare IP-adres moeten overeenkomen wanneer u ze gebruikt met open bare IP-adressen. Load Balancer en open bare IP-Sku's zijn niet onveranderbaar.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Zie [limieten voor load balancers](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)voor meer informatie. Zie [Overzicht](load-balancer-standard-overview.md), [Prijzen](https://aka.ms/lbpricing) en [SLA](https://aka.ms/lbsla) voor meer details over Standard Load Balancer.

## <a name = "limitations"></a>Hardwarebeperkingen

- Sku's zijn niet onveranderbaar. U kunt de SKU van een bestaande resource niet wijzigen.
- Een zelfstandige resource voor de virtuele machine, de resource van de beschikbaarheidsset of de virtuele-machine schaalset kan verwijzen naar één SKU, nooit beide.
- Een Load Balancer regel kan niet twee virtuele netwerken omvatten.  Front-end en hun bijbehorende back-end-instanties moeten zich in hetzelfde virtuele netwerk bevinden.  
- Het [verplaatsen van abonnements bewerkingen](../azure-resource-manager/management/move-resource-group-and-subscription.md) wordt niet ondersteund voor Standard lb-en open bare IP-resources.
- Rollen voor webwerkers zonder VNet en andere micro soft-platform Services kunnen toegankelijk zijn vanaf exemplaren achter alleen een interne Standard Load Balancer. U moet er niet op vertrouwen dat u deze kunt gebruiken als de respectieve service zelf of het onderliggende platform kan zonder kennisgeving worden gewijzigd. U moet altijd aannemen dat u een [uitgaande connectiviteit](load-balancer-outbound-connections.md) expliciet moet maken als u alleen een interne Standard Load Balancer wilt gebruiken.

- Load Balancer biedt taak verdeling en poort door sturen voor specifieke TCP-of UDP-protocollen. Taakverdelings regels en binnenkomende NAT-regels ondersteunen TCP en UDP, maar niet voor andere IP-protocollen, waaronder ICMP.

  Load Balancer de payload van een UDP-of TCP-stroom niet beëindigt, beantwoordt of anderszins communiceert. Het is geen proxy. Een geslaagde validatie van de connectiviteit met een front-end moet in-band worden uitgevoerd met hetzelfde protocol dat wordt gebruikt in een taak verdeling of binnenkomende NAT-regel. Ten minste één van de virtuele machines moet een reactie genereren voor een client om een reactie van een front-end te zien.

  Er wordt geen in-band-antwoord ontvangen van de Load Balancer front-end geeft aan dat er geen virtuele machines kunnen reageren. Er kan niet worden gecommuniceerd met een Load Balancer front-end zonder dat de virtuele machine kan reageren. Dit principe geldt ook voor uitgaande verbindingen waarbij SNAT voor poort masker alleen wordt ondersteund voor TCP en UDP. Andere IP-protocollen, waaronder ICMP, mislukken. Wijs een openbaar IP-adres op exemplaar niveau toe om dit probleem te verhelpen. Zie [Wat is SNAT en Pat](load-balancer-outbound-connections.md#snat)? voor meer informatie.

- Interne load balancers vertalen geen uitgaande oorspronkelijke verbindingen met de front-end van een interne Load Balancer, omdat beide zich in een privé-IP-adres ruimte bevinden. Open bare load balancers bieden [uitgaande verbindingen](load-balancer-outbound-connections.md) van privé-IP-adressen in het virtuele netwerk naar open bare IP-adressen. Voor interne load balancers vermijdt deze benadering de potentiële SNAT-poort uitputting binnen een unieke interne IP-adres ruimte, waarbij omzetting niet vereist is.

  Een neven effect is dat als een uitgaande stroom van een virtuele machine in de back-end-pool een stroom naar front-end van de interne Load Balancer in de groep probeert te maken _en_ wordt teruggekoppeld aan zichzelf, de twee zijden van de stroom niet overeenkomen. Omdat ze niet overeenkomen, mislukt de stroom. De stroom slaagt als de stroom niet is gekoppeld aan dezelfde virtuele machine in de back-end-pool die de stroom heeft gemaakt naar de front-end.

  Wanneer de stroom wordt teruggeleid naar zichzelf, lijkt de uitgaande stroom van de virtuele machine naar de front-end en de bijbehorende binnenkomende stroom lijkt afkomstig van de VM naar zichzelf. Vanuit het weergave punt van het gast besturingssysteem komen de inkomende en uitgaande delen van dezelfde stroom niet overeen in de virtuele machine. De TCP-stack herkent deze helften van dezelfde stroom niet als onderdeel van dezelfde stroom. De bron en het doel komen niet overeen. Wanneer de stroom wordt toegewezen aan een andere virtuele machine in de back-end-pool, komen de helften van de stroom overeen en kan de virtuele machine reageren op de stroom.

  Het symptoom voor dit scenario bestaat uit terugkerende verbindingstime-outs wanneer de stroom naar dezelfde back-end terugkeert als de stroom. Veelvoorkomende tijdelijke oplossingen omvatten het invoegen van een proxy laag achter de interne Load Balancer en het gebruik van regels voor het door sturen van direct server return-stijl (DSR). Zie [meerdere front-ends voor Azure Load Balancer](load-balancer-multivip-overview.md)voor meer informatie.

  U kunt een intern Load Balancer combi neren met elke proxy van derden of interne [Application Gateway](../application-gateway/application-gateway-introduction.md) gebruiken voor proxy SCENARIO'S met http/https. U kunt een open bare Load Balancer gebruiken om dit probleem te verhelpen, maar het resulterende scenario is gevoelig voor de [uitputting](load-balancer-outbound-connections.md#snat)van de SNAT. Vermijd deze tweede benadering, tenzij u deze zorgvuldig beheert.

- Over het algemeen worden IP-fragmenten die worden doorgestuurd, niet ondersteund in regels voor taak verdeling. IP-fragmentatie van UDP-en TCP-pakketten wordt niet ondersteund in regels voor taak verdeling. De taakverdelings regels voor poorten met hoge Beschik baarheid kunnen worden gebruikt voor het door sturen van bestaande IP-fragmenten. Zie overzicht van poorten met [hoge Beschik baarheid](load-balancer-ha-ports-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [een open bare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met het gebruik van een Load Balancer: Maak een virtuele machine met een aangepaste IIS-extensie die is geïnstalleerd en taak verdeling van de web-app tussen de vm's.
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over het gebruik van [Standard Load Balancer en Beschikbaarheidszones](load-balancer-standard-availability-zones.md).
- Meer informatie over [status controles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Standard Load Balancer diagnostische gegevens](load-balancer-standard-diagnostics.md).
- Meer informatie over het gebruik [van Load Balancer voor uitgaande verbindingen](load-balancer-outbound-connections.md).
- Meer informatie over [Uitgaande regels](load-balancer-outbound-rules-overview.md).
- Meer informatie over [TCP Reset bij niet-actief](load-balancer-tcp-reset.md).
- Meer informatie over Standard Load Balancer met de taakverdelings [regels van ha-poorten](load-balancer-ha-ports-overview.md).
- Meer informatie over het gebruik van [Load Balancer met meerdere frontends](load-balancer-multivip-overview.md).
- Meer informatie over [netwerk beveiligings groepen](../virtual-network/security-overview.md).
