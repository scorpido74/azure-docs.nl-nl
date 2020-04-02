---
title: Veerkrachtige oplossingen ontwerpen met Azure DDoS Protection
description: Meer informatie over hoe u logboekregistratiegegevens gebruiken om diepgaande inzichten over uw toepassing te verkrijgen.
services: security
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: terrylan
ms.openlocfilehash: 8be1f1161ac1c4611ddb2a5ec61592394014c488
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548683"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection - Veerkrachtige oplossingen ontwerpen

Dit artikel is voor IT-beslissers en beveiligingspersoneel. Het verwacht dat u bekend bent met Azure, netwerken en beveiliging.
DDoS is een type aanval dat probeert om toepassingsbronnen uit te putten. Het doel is om de beschikbaarheid van de toepassing en de mogelijkheid om legitieme verzoeken te behandelen beïnvloeden. Aanvallen worden steeds geavanceerder en groter in omvang en impact. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet. Het ontwerpen voor ddos-tolerantie (distributed denial of service) vereist planning en ontwerp voor verschillende foutmodi. Azure biedt continue bescherming tegen DDoS-aanvallen. Deze beveiliging is standaard en zonder extra kosten geïntegreerd in het Azure-platform.

Naast de belangrijkste DDoS-beveiliging in het platform biedt [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) geavanceerde DDoS-mitigatiemogelijkheden tegen netwerkaanvallen. Het wordt automatisch afgestemd om uw specifieke Azure-bronnen te beschermen. Bescherming is eenvoudig mogelijk te maken tijdens het creëren van nieuwe virtuele netwerken. Het kan ook worden gedaan na het maken en vereist geen toepassing of resource wijzigingen.

![De rol van Azure DDoS Protection bij het beschermen van klanten en een virtueel netwerk tegen een aanvaller](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Fundamentele best practices

In de volgende secties worden prescriptieve richtlijnen gegeven voor het bouwen van DDoS-bestendige services op Azure.

### <a name="design-for-security"></a>Ontwerpen voor beveiliging

Zorg ervoor dat beveiliging een prioriteit is gedurende de gehele levenscyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen bugs hebben die een relatief laag aantal aanvragen mogelijk maken om een buitensporige hoeveelheid resources te gebruiken, wat resulteert in een servicestoring. 

Om een service die op Microsoft Azure draait te beschermen, moet u een goed begrip hebben van uw toepassingsarchitectuur en zich richten op de [vijf pijlers van softwarekwaliteit.](/azure/architecture/guide/pillars)
U moet typische verkeersvolumes kennen, het connectiviteitsmodel tussen de toepassing en andere toepassingen en de serviceeindpunten die worden blootgesteld aan het openbare internet.

Ervoor zorgen dat een toepassing veerkrachtig genoeg is om een denial of service te verwerken die is gericht op de toepassing zelf, is het belangrijkst. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met de [Security Development Lifecycle (SDL).](https://www.microsoft.com/sdl/default.aspx) De SDL richt zich op beveiliging in elke ontwikkelingsfase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

### <a name="design-for-scalability"></a>Ontwerp voor schaalbaarheid

Schaalbaarheid is hoe goed een systeem kan omgaan met verhoogde belasting. Ontwerp uw toepassingen om horizontaal te [schalen](/azure/architecture/guide/design-principles/scale-out) om te voldoen aan de vraag naar een versterkte belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt één storingspunt veroorzaakt. Door meerdere exemplaren in te richten, wordt uw systeem veerkrachtiger en schaalbaarder.

Selecteer voor [Azure App Service](/azure/app-service/app-service-value-prop-what-is)een App [Service-abonnement](/azure/app-service/overview-hosting-plans) dat meerdere exemplaren biedt. Configureer voor Azure Cloud Services elk van uw rollen om [meerdere exemplaren](/azure/cloud-services/cloud-services-choose-me)te gebruiken. Voor [Azure Virtual Machines](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)moet u ervoor zorgen dat uw VM-architectuur (Virtual Machine) meer dan één VM bevat en dat elke vm is opgenomen in een [beschikbaarheidsset.](/azure/virtual-machines/virtual-machines-windows-manage-availability) We raden u aan [virtuele machineschaalsets te](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) gebruiken voor automatische schaling.

### <a name="defense-in-depth"></a>Diepgaande verdediging

Het idee achter de verdediging in de diepte is om risico's te beheren met behulp van diverse defensieve strategieën. Gelaagdheid beveiliging in een toepassing vermindert de kans op een succesvolle aanval. We raden u aan veilige ontwerpen voor uw toepassingen te implementeren met behulp van de ingebouwde mogelijkheden van het Azure-platform.

Bijvoorbeeld, het risico van aanval toeneemt met de grootte *(oppervlakte)* van de toepassing. U het oppervlak verkleinen door whitelisting te gebruiken om de blootgestelde IP-adresruimte en luisterpoorten te sluiten die niet nodig zijn op de load balancers[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) en [Azure Application Gateway).](/azure/application-gateway/application-gateway-create-probe-portal) [Netwerkbeveiligingsgroepen (NSG's)](/azure/virtual-network/security-overview) zijn een andere manier om het aanvalsoppervlak te verminderen.
U [servicetags](/azure/virtual-network/security-overview#service-tags) en [toepassingsbeveiligingsgroepen](/azure/virtual-network/security-overview#application-security-groups) gebruiken om de complexiteit voor het maken van beveiligingsregels en het configureren van netwerkbeveiliging te minimaliseren, als een natuurlijke uitbreiding van de structuur van een toepassing.

U moet Azure-services waar mogelijk implementeren in een [virtueel netwerk.](/azure/virtual-network/virtual-networks-overview) Deze praktijk stelt serviceresources in staat om te communiceren via privé IP-adressen. Azure-serviceverkeer vanuit een virtueel netwerk gebruikt standaard openbare IP-adressen als bron-IP-adressen. Als u [serviceeindpunten gebruikt,](/azure/virtual-network/virtual-network-service-endpoints-overview) schakelt u serviceverkeer om privéadressen van virtuele netwerken te gebruiken als bron-IP-adressen wanneer ze toegang hebben tot de Azure-service vanuit een virtueel netwerk.

We zien vaak dat de on-premises bronnen van klanten worden aangevallen, samen met hun resources in Azure. Als u een on-premises omgeving verbindt met Azure, raden we u aan de blootstelling van on-premises bronnen op het openbare internet te minimaliseren. U de schaal- en geavanceerde DDoS-beveiligingsmogelijkheden van Azure gebruiken door uw bekende openbare entiteiten in Azure te implementeren. Omdat deze openbaar toegankelijke entiteiten vaak een doelwit zijn voor DDoS-aanvallen, vermindert het plaatsen ervan in Azure de impact op uw on-premises resources.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-aanbiedingen voor DDoS-bescherming

Azure heeft twee DDoS-serviceaanbiedingen die bescherming bieden tegen netwerkaanvallen (Layer 3 en 4): DDoS Protection Basic en DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS Protection Basic

Basisbeveiliging is standaard geïntegreerd in de Azure zonder extra kosten. De schaal en capaciteit van het wereldwijd geïmplementeerde Azure-netwerk biedt verdediging tegen veelvoorkomende netwerklaagaanvallen door middel van always-on verkeersbewaking en real-time mitigatie. DDoS Protection Basic vereist geen gebruikersconfiguratie of toepassingswijzigingen. DDoS Protection Basic helpt bij het beschermen van alle Azure-services, waaronder PaaS-services zoals Azure DNS.

![Vertegenwoordiging van het Azure-netwerk toewijzen met de tekst 'Wereldwijde DDoS-mitigatieaanwezigheid' en 'Toonaangevende DDoS-mitigatiecapaciteit'](./media/ddos-best-practices/image3.png)

BasisDDoS-beveiliging in Azure bestaat uit zowel software- als hardwarecomponenten. Een software controle vlak bepaalt wanneer, waar en wat voor soort verkeer moet worden gestuurd door middel van hardware-apparaten die analyseren en verwijderen aanval verkeer. Het controlevlak neemt deze beslissing op basis van een infrastructuurbreed DDoS *Protection-beleid.* Dit beleid is statisch ingesteld en universeel toegepast op alle Azure-klanten.

Het DDoS-beveiligingsbeleid geeft bijvoorbeeld aan op welk verkeersvolume de beveiliging moet worden *geactiveerd.* (Dat wil zeggen, het verkeer van de huurder moet worden gerouteerd door middel van schrobben apparaten.) Het beleid geeft vervolgens aan hoe de schrobapparaten de aanval moeten *beperken.*

De Azure DDoS Protection Basic-service is gericht op de bescherming van de infrastructuur en bescherming van het Azure-platform. Het vermindert het verkeer wanneer het een tarief overschrijdt dat zo belangrijk is dat het meerdere klanten in een multitenant-omgeving kan beïnvloeden. Het biedt geen waarschuwingen of aangepaste beleidsregels per klant.

### <a name="ddos-protection-standard"></a>DDoS-beveiligingsstandaard

Standaardbescherming biedt verbeterde DDoS-mitigatiefuncties. Het wordt automatisch afgestemd om uw specifieke Azure-bronnen in een virtueel netwerk te beschermen. Beveiliging is eenvoudig in te schakelen op elk nieuw of bestaand virtueel netwerk en vereist geen wijzigingen in toepassingen of bronnen. Het heeft verschillende voordelen ten opzichte van de basisservice, waaronder logboekregistratie, waarschuwingen en telemetrie. In de volgende secties worden de belangrijkste functies van de Azure DDoS Protection Standard-service beschreven.

#### <a name="adaptive-real-time-tuning"></a>Adaptieve real-time tuning

De Azure DDoS Protection Basic-service helpt klanten te beschermen en effecten voor andere klanten te voorkomen. Als een service bijvoorbeeld is ingericht voor een typisch volume legitiem binnenkomend verkeer dat kleiner is dan de *triggersnelheid* van het infrastructuurbrede DDoS-beveiligingsbeleid, kan een DDoS-aanval op de resources van die klant onopgemerkt blijven. Meer in het algemeen vragen de complexiteit van recente aanvallen (bijvoorbeeld multi-vector DDoS) en het toepassingsspecifieke gedrag van tenants om een aangepast beveiligingsbeleid per klant. De service bereikt deze aanpassing door gebruik te maken van twee inzichten:

- Automatisch leren van verkeerspatronen per klant (per IP) voor Laag 3 en 4.

- Het minimaliseren van false positives, gezien het feit dat de schaal van Azure het mogelijk maakt om een aanzienlijke hoeveelheid verkeer te absorberen.

![Diagram van hoe DDoS Protection Standard werkt, met "Policy Generation" omcirkeld](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetrie, bewaking en waarschuwing van DDoS Protection

DDoS Protection Standard legt uitgebreide telemetrie via [Azure Monitor](/azure/azure-monitor/overview) bloot voor de duur van een DDoS-aanval. U waarschuwingen configureren voor een van de Azure Monitor-statistieken die DDoS-beveiliging gebruikt. U logboekregistratie integreren met Splunk (Azure Event Hubs), Azure Monitor-logboeken en Azure Storage voor geavanceerde analyse via de Azure Monitor Diagnostics-interface.

##### <a name="ddos-mitigation-policies"></a>DDoS-mitigatiebeleid

Selecteer in de Azure-portal De optie > **Monitorstatistieken**. **Monitor** Selecteer **in** het deelvenster Statistieken de brongroep, selecteer een brontype **openbaar IP-adres**en selecteer uw openbaar IP-adres in Azure. DDoS-statistieken zijn zichtbaar in het deelvenster **Beschikbare statistieken.**

DDoS Protection Standard past drie automatisch afgestemdmitigatiebeleid (TCP SYN, TCP en UDP) toe voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarvoor DDoS is ingeschakeld. U de beleidsdrempels bekijken door de metrische binnenkomende pakketten te selecteren **om DDoS-mitigatie te activeren.**

![Grafiek beschikbare statistieken en statistieken](./media/ddos-best-practices/image7.png)

De beleidsdrempels worden automatisch geconfigureerd via machine learning-gebaseerde netwerkverkeerprofilering. DDoS-mitigatie treedt alleen op voor een IP-adres dat wordt aangevallen wanneer de beleidsdrempel wordt overschreden.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrische gegevens voor een IP-adres onder DDoS-aanval

Als het openbare IP-adres wordt aangevallen, wordt de waarde voor de metrische **DDoS-aanval al dan niet** gewijzigd in 1 omdat DDoS Protection mitigatie uitvoert op het aanvalsverkeer.

![Statistiek en grafiek onder DDoS-aanval of niet](./media/ddos-best-practices/image8.png)

We raden u aan een waarschuwing op deze statistiek te configureren. U ontvangt dan een melding wanneer er een actieve DDoS-beperking wordt uitgevoerd op uw openbare IP-adres.

Zie [Azure DDoS Protection Standard beheren met de Azure-portal](/azure/virtual-network/ddos-protection-manage-portal)voor meer informatie.

#### <a name="web-application-firewall-for-resource-attacks"></a>Firewall voor webtoepassingen voor bronaanvallen

Specifiek voor resourceaanvallen op de toepassingslaag, moet u een webtoepassingsfirewall (WAF) configureren om webtoepassingen te beveiligen. Een WAF inspecteert binnenkomend webverkeer om SQL-injecties, cross-site scripting, DDoS en andere Layer 7-aanvallen te blokkeren. Azure biedt [WAF als een functie van Application Gateway](/azure/application-gateway/application-gateway-web-application-firewall-overview) voor gecentraliseerde bescherming van uw webtoepassingen tegen veelvoorkomende exploits en kwetsbaarheden. Er zijn andere WAF-aanbiedingen beschikbaar van Azure-partners die mogelijk beter geschikt zijn voor uw behoeften via de [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)

Zelfs webapplicatie firewalls zijn gevoelig voor volumetrische en staat uitputting aanvallen. We raden u ten zeerste aan de DDoS Protection Standard op het virtuele WAF-netwerk in te schakelen om te helpen beschermen tegen volumeaanvallen en protocolaanvallen. Zie de sectie [Referentiearchitecturen voor DDoS Protection](#ddos-protection-reference-architectures) voor meer informatie.

### <a name="protection-planning"></a>Beschermingsplanning

Planning en voorbereiding zijn cruciaal om te begrijpen hoe een systeem zal presteren tijdens een DDoS-aanval. Het ontwerpen van een incident management response plan is onderdeel van deze inspanning.

Als u DDoS Protection Standard hebt, moet u ervoor zorgen dat deze is ingeschakeld op het virtuele netwerk van naar internet gerichte eindpunten. Door DDoS-waarschuwingen te configureren, u voortdurend op leten op mogelijke aanvallen op uw infrastructuur. 

Controleer uw toepassingen onafhankelijk. Begrijp het normale gedrag van een toepassing. Bereid je voor om te handelen als de toepassing zich niet gedraagt zoals verwacht tijdens een DDoS-aanval.

#### <a name="testing-through-simulations"></a>Testen door middel van simulaties

Het is een goede gewoonte om uw veronderstellingen over hoe uw diensten zullen reageren op een aanval te testen door het uitvoeren van periodieke simulaties. Controleer tijdens het testen of uw services of toepassingen blijven functioneren zoals verwacht en dat de gebruikerservaring niet wordt verstoord. Identificeer hiaten vanuit zowel technologie als processtandpunt en neem ze op in de DDoS-responsstrategie. We raden u aan dergelijke tests uit te voeren in faseringsomgevingen of tijdens niet-piekuren om de impact op de productieomgeving te minimaliseren.

We werken samen met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) om een interface te bouwen waar Azure-klanten verkeer kunnen genereren tegen openbare eindpunten met DDoS Protection voor simulaties. U de [BreakingPoint Cloud-simulatie](https://www.ixiacom.com/products/breakingpoint-cloud) gebruiken om:

- Valideer hoe Azure DDoS Protection uw Azure-bronnen beschermt tegen DDoS-aanvallen.

- Optimaliseer uw incidentresponsproces terwijl u onder ddos-aanval staat.

- DDoS-naleving documenteren.

- Train uw netwerkbeveiligingsteams.

Cybersecurity vereist constante innovatie in defensie. Azure DDoS Standard-beveiliging is een state-of-the-art aanbod met een effectieve oplossing om steeds complexere DDoS-aanvallen te beperken.

## <a name="components-of-a-ddos-response-strategy"></a>Onderdelen van een DDoS-responsstrategie

Een DDoS-aanval die azure-bronnen target, vereist meestal minimale tussenkomst vanuit het oogpunt van de gebruiker. Toch helpt het opnemen van DDoS-mitigatie als onderdeel van een incidentresponsestrategie de impact op de bedrijfscontinuïteit te minimaliseren.

### <a name="microsoft-threat-intelligence"></a>Microsoft threat intelligence

Microsoft heeft een uitgebreid threat intelligence-netwerk. Dit netwerk maakt gebruik van de collectieve kennis van een uitgebreide beveiligingscommunity die onlineservices van Microsoft, Microsoft-partners en relaties binnen de internetbeveiligingscommunity ondersteunt. 

Als leverancier van kritieke infrastructuur ontvangt Microsoft vroege waarschuwingen over bedreigingen. Microsoft verzamelt threat intelligence van zijn online services en uit haar wereldwijde klantenbestand. Microsoft integreert al deze bedreigingsinformatie terug in de Azure DDoS Protection-producten.

Ook de Microsoft Digital Crimes Unit (DCU) voert offensieve strategieën tegen botnets. Botnets zijn een veel voorkomende bron van commando en controle voor DDoS-aanvallen.

### <a name="risk-evaluation-of-your-azure-resources"></a>Risico-evaluatie van uw Azure-resources

Het is absoluut noodzakelijk om de omvang van uw risico van een DDoS-aanval te begrijpen op een permanente basis. Vraag jezelf regelmatig af:

- Welke nieuwe openbaar beschikbare Azure-bronnen hebben bescherming nodig?

- Is er één storingspunt in de service? 

- Hoe kunnen services worden geïsoleerd om de impact van een aanval te beperken en tegelijkertijd services beschikbaar te stellen aan geldige klanten?

- Zijn er virtuele netwerken waar DDoS Protection Standard moet worden ingeschakeld, maar is het niet? 

- Zijn mijn services actief/actief met failover in meerdere regio's?

### <a name="customer-ddos-response-team"></a>Klant DDoS-responsteam

Het creëren van een DDoS-responsteam is een belangrijke stap om snel en effectief op een aanval te reageren. Identificeer contactpersonen in uw organisatie die toezicht houden op zowel planning als uitvoering. Dit DDoS-antwoordteam moet de Azure DDoS Protection Standard-service grondig begrijpen. Zorg ervoor dat het team een aanval kan identificeren en beperken door te coördineren met interne en externe klanten, waaronder het Microsoft-ondersteuningsteam.

Voor uw DDoS-responsteam raden we u aan simulatieoefeningen te gebruiken als een normaal onderdeel van uw servicebeschikbaarheid en continuïteitsplanning. Deze oefeningen moeten schaaltesten omvatten.

### <a name="alerts-during-an-attack"></a>Waarschuwingen tijdens een aanval

Azure DDoS Protection Standard identificeert en vermindert DDoS-aanvallen zonder tussenkomst van de gebruiker. Als u een melding wilt ontvangen wanneer er een actieve beperking is voor een beveiligd openbaar IP-adres, u [een waarschuwing configureren](/azure/virtual-network/ddos-protection-manage-portal) voor de metrische **DDoS-aanval of niet**. U ervoor kiezen om waarschuwingen te maken voor de andere DDoS-statistieken om inzicht te krijgen in de schaal van de aanval, het verkeer dat wordt verwijderd en andere details.

#### <a name="when-to-contact-microsoft-support"></a>Wanneer u contact opnemen met microsoft-ondersteuning

- Tijdens een DDoS-aanval vindt u dat de prestaties van de beveiligde bron ernstig zijn aangetast of dat de resource niet beschikbaar is.

- U denkt dat de DDoS Protection-service zich niet gedraagt zoals verwacht. 

  De DDoS Protection-service wordt alleen beperkt als het **metrische waardebeleid voor het activeren van DDoS-mitigatie (TCP/TCP SYN/UDP)** lager is dan het verkeer dat is ontvangen op de beveiligde openbare IP-bron.

- U plant een virale gebeurtenis die uw netwerkverkeer aanzienlijk zal verhogen.

- Een acteur heeft gedreigd om een DDoS-aanval te lanceren tegen uw middelen.

- Als u de lijst een IP- of IP-bereik van Azure DDoS Protection Standard wilt toestaan. Een veelvoorkomend scenario is het toestaan van lijst-IP als het verkeer wordt doorgestuurd van een externe cloud WAF naar Azure. 

Voor aanvallen die een kritieke zakelijke impact hebben, maakt u een ticket voor de [ernst-A-ondersteuning.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="post-attack-steps"></a>Stappen na de aanval

Het is altijd een goede strategie om een postmortem te doen na een aanval en de DDoS-responsstrategie aan te passen als dat nodig is. Dingen om te overwegen:

- Was er een verstoring van de service of gebruikerservaring door gebrek aan schaalbare architectuur?

- Welke toepassingen of diensten hebben het meest geleden?

- Hoe effectief was de DDoS-responsstrategie en hoe kan deze worden verbeterd?

Als u vermoedt dat u onder een DDoS-aanval staat, escaleert u via uw normale Azure Support-kanalen.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection-referentiearchitecturen

DDoS Protection Standard is ontworpen [voor services die worden geïmplementeerd in een virtueel netwerk.](/azure/virtual-network/virtual-network-for-azure-services) Voor andere services is de standaard DDoS Protection Basic-service van toepassing. De volgende referentiearchitecturen worden gerangschikt op scenario's, waarbij architectuurpatronen zijn gegroepeerd.

### <a name="virtual-machine-windowslinux-workloads"></a>Virtual machine (Windows/Linux) workloads

#### <a name="application-running-on-load-balanced-vms"></a>Toepassing die wordt uitgevoerd op vm's met belastingsbalans

Deze referentiearchitectuur toont een reeks beproefde praktijken voor het uitvoeren van meerdere Windows VM's in een schaal die achter een load balancer is ingesteld, om de beschikbaarheid en schaalbaarheid te verbeteren. Deze architectuur kan worden gebruikt voor elke stateless workload, zoals een webserver.

![Diagram van de referentiearchitectuur voor een toepassing die wordt uitgevoerd op vm's met belastingsbalans](./media/ddos-best-practices/image9.png)

In deze architectuur wordt een werkbelasting verdeeld over meerdere VM-exemplaren. Er is één openbaar IP-adres en internetverkeer wordt via een load balancer naar de VM's gedistribueerd. DDoS Protection Standard is ingeschakeld op het virtuele netwerk van de Azure (internet) load balancer met het openbare IP-ip dat eraan is gekoppeld.

De load balancer distribueert binnenkomende internetaanvragen naar de VM-exemplaren. Met virtuele machineschaalsets kan het aantal VM's handmatig in- of uitschalen of automatisch op basis van vooraf gedefinieerde regels. Dit is belangrijk als de bron onder DDoS-aanval staat. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)voor meer informatie over deze referentiearchitectuur.

#### <a name="application-running-on-windows-n-tier"></a>Toepassing die wordt uitgevoerd op Windows N-tier

Er zijn veel manieren om een architectuur met meerdere lagen te implementeren. In het volgende diagram ziet u een typische drielaagse webtoepassing. Deze architectuur bouwt voort op het artikel [Load-balanced VM's uitvoeren voor schaalbaarheid en beschikbaarheid.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) De web- en bedrijfslagen gebruiken VM's met taakverdeling.

![Diagram van de referentiearchitectuur voor een toepassing die wordt uitgevoerd op Windows N-tier](./media/ddos-best-practices/image10.png)

In deze architectuur is DDoS Protection Standard ingeschakeld op het virtuele netwerk. Alle openbare IP's in het virtuele netwerk krijgen DDoS-bescherming voor Layer 3 en 4. Implementeer Application Gateway voor laag 7-beveiliging in de WAF SKU. Zie [dit artikel](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)voor meer informatie over deze referentiearchitectuur.

#### <a name="paas-web-application"></a>PaaS-webtoepassing

Deze referentiearchitectuur toont het uitvoeren van een Azure App Service-toepassing in één regio. Deze architectuur toont een reeks beproefde praktijken voor een webtoepassing die [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) en [Azure SQL Database gebruikt.](https://azure.microsoft.com/documentation/services/sql-database/)
Er is een stand-byregio ingesteld voor failoverscenario's.

![Diagram van de referentiearchitectuur voor een PaaS-webtoepassing](./media/ddos-best-practices/image11.png)

Azure Traffic Manager leidt binnenkomende aanvragen naar Application Gateway in een van de regio's. Tijdens normale bewerkingen wordt aanvragen naar Application Gateway in het actieve gebied doorgeslopen. Als dat gebied niet meer beschikbaar is, wordt Traffic Manager niet naar Application Gateway in het stand-bygebied.

Al het verkeer van het internet bestemd voor de webapplicatie wordt via Traffic Manager doorgestuurd naar het [openbare IP-adres van](/azure/application-gateway/application-gateway-web-app-overview) de Application Gateway. In dit scenario wordt de app-service (web-app) zelf niet direct naar buiten gericht en wordt deze beschermd door Application Gateway. 

We raden u aan de WAF SKU (prevent mode) van de Application Gateway te configureren om te beschermen tegen Layer 7 (HTTP/HTTPS/WebSocket) aanvallen. Daarnaast zijn web-apps geconfigureerd om alleen verkeer vanaf het IP-adres van de Application Gateway te [accepteren.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Zie [dit artikel](/azure/architecture/reference-architectures/app-service-web-app/multi-region)voor meer informatie over deze referentiearchitectuur.

### <a name="mitigation-for-non-web-paas-services"></a>Mitigatie voor niet-web PaaS-services

#### <a name="hdinsight-on-azure"></a>HDInsight op Azure

Deze referentiearchitectuur toont het configureren van DDoS Protection Standard voor een [Azure HDInsight-cluster.](/azure/hdinsight/) Zorg ervoor dat het HDInsight-cluster is gekoppeld aan een virtueel netwerk en dat DDoS-beveiliging is ingeschakeld op het virtuele netwerk.

![Deelvensters 'HDInsight' en 'Geavanceerde instellingen', met virtuele netwerkinstellingen](./media/ddos-best-practices/image12.png)

![Selectie voor het inschakelen van DDoS-beveiliging](./media/ddos-best-practices/image13.png)

In deze architectuur wordt verkeer dat vanaf het internet naar het HDInsight-cluster is bestemd, doorgestuurd naar het openbare IP-adres dat is gekoppeld aan de HDInsight-gatewayloadbalancer. De gatewayload balancer stuurt het verkeer vervolgens rechtstreeks naar de hoofdknooppunten of de werknemersknooppunten. Omdat DDoS Protection Standard is ingeschakeld op het virtuele HDInsight-netwerk, krijgen alle openbare IP's in het virtuele netwerk DDoS-bescherming voor Laag 3 en 4. Deze referentiearchitectuur kan worden gecombineerd met de n-tier- en multi-regio referentiearchitecturen.

Zie [Azure HDInsight uitbreiden met een Azure Virtual Network-documentatie](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over deze referentiearchitectuur.


> [!NOTE]
> Azure App Service-omgeving voor PowerApps of API-beheer in een virtueel netwerk met een openbaar IP-adres worden beide niet native ondersteund.

## <a name="next-steps"></a>Volgende stappen

* [Gedeelde verantwoordelijkheid in de cloud](shared-responsibility.md)

* [Productpagina Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS Protection-documentatie](/azure/virtual-network/ddos-protection-overview)
