---
title: Netwerkarchitectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Netwerkarchitectuur voor het implementeren van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b3bc87b183803c0854542d6925af7429b593d2af
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605176"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (Large Instances) netwerkarchitectuur

De architectuur van Azure-netwerkservices is een belangrijk onderdeel van de succesvolle implementatie van SAP-toepassingen op HANA Large Instance. Sap HANA op Azure -implementaties (Large Instances) heeft doorgaans een groter SAP-landschap met verschillende SAP-oplossingen met verschillende formaten databases, CPU-bronverbruik en geheugengebruik. Het is waarschijnlijk dat niet alle IT-systemen zich al in Azure bevinden. Uw SAP-landschap is vaak ook hybride vanuit het oogpunt van DBMS- en SAP-toepassingen met behulp van een mengsel van NetWeaver en S/4HANA en SAP HANA en andere DBMS. Azure biedt verschillende services waarmee u de verschillende DBMS-, NetWeaver- en S/4HANA-systemen in Azure uitvoeren. Azure biedt u ook netwerktechnologie om Azure eruit te laten zien als een virtueel datacenter voor uw on-premises software-implementaties

Tenzij uw complete IT-systemen worden gehost in Azure. Azure-netwerkfunctionaliteit wordt gebruikt om de on-premises wereld te verbinden met uw Azure-assets, waardoor Azure eruit ziet als een virtueel datacenter van u. De gebruikte Azure-netwerkfunctionaliteit is: 

- Azure virtuele netwerken zijn verbonden met het [ExpressRoute-circuit](https://azure.microsoft.com/services/expressroute/) dat verbinding maakt met uw on-premises netwerkassets.
- Een ExpressRoute-circuit dat on-premises verbinding maakt met Azure moet een minimale bandbreedte van [1 Gbps of hoger](https://azure.microsoft.com/pricing/details/expressroute/)hebben. Deze minimale bandbreedte zorgt voor voldoende bandbreedte voor de overdracht van gegevens tussen on-premises systemen en systemen die op VM's draaien. Het maakt ook voldoende bandbreedte mogelijk voor verbinding met Azure-systemen van on-premises gebruikers.
- Alle SAP-systemen in Azure zijn ingesteld in virtuele netwerken om met elkaar te communiceren.
- Active Directory en DNS gehost on-premises worden uitgebreid naar Azure via ExpressRoute van on-premises, of worden uitgevoerd voltooid in Azure.

Voor het specifieke geval van integratie van HANA Large Instances in de Azure-datacenternetwerkstructuur wordt azure ExpressRoute-technologie ook gebruikt


> [!NOTE] 
> Slechts één Azure-abonnement kan worden gekoppeld aan slechts één tenant in een HANA Large Instance-stempel in een specifieke Azure-regio. Omgekeerd kan één HANA Large Instance-stempeltenant worden gekoppeld aan slechts één Azure-abonnement. Deze vereiste komt overeen met andere factureerbare objecten in Azure.

Als SAP HANA op Azure (Large Instances) wordt geïmplementeerd in meerdere verschillende Azure-regio's, wordt een afzonderlijke tenant geïmplementeerd in de HANA Large Instance-stempel. U beide onder hetzelfde Azure-abonnement uitvoeren, zolang deze exemplaren deel uitmaken van hetzelfde SAP-landschap. 

> [!IMPORTANT] 
> Alleen de implementatiemethode azure resource manager wordt ondersteund met SAP HANA op Azure (Large Instances).

 

## <a name="additional-virtual-network-information"></a>Aanvullende informatie over virtueel netwerk

Als u een virtueel netwerk wilt verbinden met ExpressRoute, moet een Azure ExpressRoute-gateway worden gemaakt. Zie [Over Expressroute-gateways voor ExpressRoute voor](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie voor meer informatie. 

Een Azure ExpressRoute-gateway wordt met ExpressRoute gebruikt voor een infrastructuur buiten Azure of naar een Azure Large Instance-stempel. U de Azure ExpressRoute-gateway verbinden met maximaal vier verschillende ExpressRoute-circuits, zolang deze verbindingen afkomstig zijn van verschillende Microsoft enterprise edge-routers. Zie [SAP HANA (Large Instances) infrastructuur en connectiviteit op Azure voor](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie. 

> [!NOTE] 
> De maximale doorvoer die u bereiken met een ExpressRoute-gateway is 10 Gbps met behulp van een ExpressRoute-verbinding. Het kopiëren van bestanden tussen een VM die zich in een virtueel netwerk bevindt en een on-premises systeem (als één kopiestream) bereikt niet de volledige doorvoer van de verschillende gateway-SKU's. Als u gebruik wilt maken van de volledige bandbreedte van de ExpressRoute-gateway, gebruikt u meerdere streams. Of u moet verschillende bestanden kopiëren in parallelle streams van één bestand.


## <a name="networking-architecture-for-hana-large-instance"></a>Netwerkarchitectuur voor HANA Large Instance
De netwerkarchitectuur voor HANA Large Instance kan worden opgesplitst in vier verschillende delen:

- On-premises netwerk- en ExpressRoute-verbinding met Azure. Dit deel is het domein van de klant en is verbonden met Azure via ExpressRoute. Dit Expressroute circuit wordt volledig betaald door u als klant. De bandbreedte moet groot genoeg zijn om het netwerkverkeer tussen uw on-premises assets en de Azure-regio waartegen u verbinding maakt, te verwerken. Zie rechtsonder in de volgende figuur.
- Azure-netwerkservices, zoals eerder besproken, met virtuele netwerken, die opnieuw ExpressRoute-gateways moeten toevoegen. Dit deel is een gebied waar u de juiste ontwerpen moet vinden voor uw toepassingsvereisten, beveiliging en nalevingsvereisten. Of u HANA Large Instance gebruikt, is een ander punt om rekening mee te houden in termen van het aantal virtuele netwerken en Azure-gateway SKU's om uit te kiezen. Zie de rechterbovenhoek in de figuur.
- Connectiviteit van HANA Large Instance via ExpressRoute-technologie in Azure. Dit onderdeel wordt geïmplementeerd en afgehandeld door Microsoft. Het enige wat u hoeft te doen is een aantal IP-adresbereiken op te geven na de implementatie van uw assets in HANA Large Instance en het ExpressRoute-circuit verbinden met de virtuele netwerken. Zie [SAP HANA (Large Instances) infrastructuur en connectiviteit op Azure voor](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie. Er zijn geen extra kosten voor u als klant voor de connectiviteit tussen de Azure-netwerkstructuur voor datacenters en HANA Large Instance-eenheden.
- Netwerken binnen de HANA Large Instance stempel, die meestal transparant is voor jou.

![Virtueel netwerk verbonden met SAP HANA op Azure (Large Instances) en on-premises](./media/hana-overview-architecture/image1-architecture.png)

De vereiste dat uw on-premises assets verbinding moeten maken via ExpressRoute met Azure, verandert niet omdat u HANA Large Instance gebruikt. De vereiste om een of meerdere virtuele netwerken te hebben waarop de VM's worden uitgevoerd, waardoor de toepassingslaag wordt gehost die verbinding maakt met de HANA-exemplaren die worden gehost in HANA-eenheden voor grote instanties, verandert ook niet. 

De verschillen met SAP-implementaties in Azure zijn:

- De HANA Large Instance units van uw klanthuurder zijn via een ander ExpressRoute-circuit verbonden met uw virtuele netwerken. Om de belastingsvoorwaarden te scheiden, delen de on-premises naar Azure virtual network ExpressRoute-circuits en de circuits tussen Virtuele Azure-netwerken en HANA Large Instances niet dezelfde routers.
- Het werkbelastingsprofiel tussen de SAP-toepassingslaag en de HANA Large Instance is van een andere aard, met veel kleine aanvragen en bursts zoals gegevensoverdrachten (resultaatsets) van SAP HANA in de toepassingslaag.
- De SAP-toepassingsarchitectuur is gevoeliger voor netwerklatentie dan typische scenario's waarin gegevens worden uitgewisseld tussen on-premises en Azure.
- De Azure ExpressRoute-gateway heeft ten minste twee ExpressRoute-verbindingen. Een circuit dat is aangesloten vanuit on-premises en een die is aangesloten vanuit HANA Large Instances. Dit laat alleen ruimte voor nog eens twee extra circuits van verschillende MEEs om verbinding te maken op ExpressRoute Gateway. Deze beperking staat los van het gebruik van ExpressRoute Fast Path. Alle verbonden circuits delen de maximale bandbreedte voor binnenkomende gegevens van de ExpressRoute-gateway.

Met revisie 3 van HANA Large Instance-stempels kan de netwerklatentie tussen VM's en HANA Large Instance-eenheden hoger zijn dan een typische VM-naar-VM-netwerklatentie. Afhankelijk van de Azure-regio kunnen de gemeten waarden hoger zijn dan de 0,7 ms-latentie die is geclassificeerd als onderhet gemiddelde in [SAP Note #1100926 - FAQ: Netwerkprestaties](https://launchpad.support.sap.com/#/notes/1100926/E). Afhankelijk van Azure Region en tool om de latentie van het netwerk tussen een Azure VM en HANA Large Instance-eenheid te meten, kan de gemeten latentie oplopen tot ongeveer 2 milliseconden. Niettemin implementeren klanten SAP HANA-gebaseerde productie SAP-toepassingen met succes op SAP HANA Large Instance. Zorg ervoor dat u uw bedrijfsprocessen grondig test in Azure HANA Large Instance. Een nieuwe functionaliteit, genaamd ExpressRoute Fast Path, is in staat om de netwerklatentie tussen HANA Large Instances en toepassingslaag VM's in Azure aanzienlijk te verminderen (zie hieronder). 

Met revisie 4 van HANA Large Instance-stempels wordt de netwerklatentie tussen Azure VM's die in de nabijheid van de HANA Large Instance-stempel worden geïmplementeerd, ervaren om te voldoen aan de gemiddelde of beter dan gemiddelde classificatie zoals gedocumenteerd in [SAP Note #1100926 - FAQ: Netwerkprestaties](https://launchpad.support.sap.com/#/notes/1100926/E) als Azure ExpressRoute Fast Path is geconfigureerd (zie hieronder). Als u Azure VM's in de nabijheid van HANA Large Instance-eenheden van Revisie 4 wilt implementeren, moet u azure [proximity placementgroepen](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)gebruiken. De manier waarop nabijheidsplaatsingsgroepen kunnen worden gebruikt om de SAP-toepassingslaag in hetzelfde Azure-datacenter te vinden als de gehoste HANA-eenheden voor grote instantie van Revisie 4, wordt beschreven in [Azure Proximity Placement Groups voor optimale netwerklatentie met SAP-toepassingen.](sap-proximity-placement-scenarios.md)

Om deterministische netwerklatentie tussen VM's en HANA Large Instance te bieden, is de keuze van de ExpressRoute gateway SKU essentieel. In tegenstelling tot de verkeerspatronen tussen on-premises en VM's, kan het verkeerspatroon tussen VM's en HANA Large Instance kleine maar hoge uitbarstingen van verzoeken en gegevensvolumes ontwikkelen die moeten worden verzonden. Om dergelijke uitbarstingen goed te verwerken, raden we het gebruik van de UltraPerformance gateway SKU ten zeerste aan. Voor de klasse Type II van HANA Large Instance SKU's is het gebruik van de UltraPerformance gateway SKU als ExpressRoute-gateway verplicht.

> [!IMPORTANT] 
> Gezien het totale netwerkverkeer tussen de SAP-toepassings- en databaselagen worden alleen de SKU's met HighPerformance of UltraPerformance-gateway voor virtuele netwerken ondersteund voor verbinding maken met SAP HANA op Azure (Large Instances). Voor HANA Large Instance Type II SKU's wordt alleen de UltraPerformance gateway SKU ondersteund als ExpressRoute-gateway. Uitzonderingen zijn van toepassing bij het gebruik van ExpressRoute Fast Path (zie hieronder)

### <a name="expressroute-fast-path"></a>Snel pad expressroute
Om de latentie te verlagen, werd ExpressRoute Fast Path in mei 2019 geïntroduceerd en uitgebracht voor de specifieke connectiviteit van HANA Large Instances met virtuele Azure-netwerken die de SAP-toepassingsVM's hosten. Het grote verschil met de tot nu toe uitgerolde oplossing is dat de gegevensstromen tussen VM's en HANA Large Instances niet meer via de ExpressRoute-gateway worden gerouteerd. In plaats daarvan communiceren de VM's die zijn toegewezen in het subnet(en) van het virtuele Azure-netwerk rechtstreeks met de toegewezen enterprise edge-router. 

> [!IMPORTANT] 
> De ExpressRoute Fast Path-functionaliteit vereist dat de subnetten waarop de SAP-toepassingsVM's worden uitgevoerd, zich bevinden in hetzelfde Virtuele Azure-netwerk dat is aangesloten op de HANA Large Instances. VM's in virtuele Azure-netwerken die zijn gekoppeld aan het virtuele Azure-netwerk dat rechtstreeks is verbonden met de HANA Large Instance-eenheden, profiteren niet van ExpressRoute Fast Path. Als gevolg hiervan typische hub en spaak virtuele netwerk ontwerpen, waar de ExpressRoute circuits zijn verbinding te maken tegen een hub virtueel netwerk en virtuele netwerken met de SAP-applicatie laag (spaken) krijgen peered, de optimalisatie door ExpressRoute Fast Path zal niet werken. In addtion ondersteunt ExpressRoute Fast Path vandaag de dag geen door de gebruiker gedefinieerde routeringsregels (UDR). Zie [ExpressRoute virtual network gateway en FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)voor meer informatie. 


Lees het document [Een virtueel netwerk verbinden met HANA-grote exemplaren voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)meer informatie over het configureren van ExpressRoute Fast Path.    

> [!NOTE]
> Een UltraPerformance ExpressRoute-gateway is vereist om ExpressRoute Fast Path te laten werken


## <a name="single-sap-system"></a>Eén SAP-systeem

De eerder getoonde on-premises infrastructuur is via ExpressRoute verbonden met Azure. Het ExpressRoute-circuit maakt verbinding met een Microsoft enterprise edge router (MSEE). Zie [Technisch overzicht ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie. Nadat de route is ingesteld, wordt deze verbonden met de Azure-backbone.

> [!NOTE] 
> Als u SAP-landschappen in Azure wilt uitvoeren, maakt u verbinding met de bedrijfsedgerouter die het dichtst bij de Azure-regio in het SAP-landschap ligt. HANA Large Instance-stempels zijn verbonden via speciale enterprise edge-routerapparaten om de netwerklatentie tussen VM's in Azure IaaS- en HANA Large Instance-stempels te minimaliseren.

De ExpressRoute-gateway voor de VM's die SAP-toepassingsexemplaren hosten, is verbonden met één ExpressRoute-circuit dat verbinding maakt met on-premises. Hetzelfde virtuele netwerk is verbonden met een aparte enterprise edge-router die is gewijd aan het verbinden met large instance-stempels. Met ExpressRoute Fast Path wordt de gegevensstroom van HANA Large Instances naar de SAP-toepassingslaag VM's niet meer door de ExpressRoute-gateway gerouteerd en daarmee de latentie van het netwerk voor retouren verminderd.

Dit systeem is een eenvoudig voorbeeld van één SAP-systeem. De SAP-toepassingslaag wordt gehost in Azure. De SAP HANA-database draait op SAP HANA op Azure (Large Instances). De aanname is dat de ExpressRoute-gatewaybandbreedte van 2 Gbps of 10 Gbps-doorvoer geen knelpunt vormt.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Meerdere SAP-systemen of grote SAP-systemen

Als meerdere SAP-systemen of grote SAP-systemen worden geïmplementeerd om verbinding te maken met SAP HANA op Azure (Large Instances), kan de doorvoer van de ExpressRoute-gateway een knelpunt worden. Of u wilt productie- en niet-productiesystemen isoleren in verschillende virtuele Azure-netwerken. In een dergelijk geval splitst u de toepassingslagen op in meerdere virtuele netwerken. U ook een speciaal virtueel netwerk maken dat verbinding maakt met HANA Large Instance voor gevallen zoals:

- Back-ups rechtstreeks uitvoeren vanuit de HANA-exemplaren in HANA Large Instance naar een VM in Azure die NFS-shares host.
- Het kopiëren van grote back-ups of andere bestanden van HANA Large Instance-eenheden naar schijfruimte die in Azure wordt beheerd.

Gebruik een apart virtueel netwerk om VM's te hosten die opslag beheren voor massaoverdracht van gegevens tussen HANA Large Instances en Azure. Deze regeling voorkomt de effecten van grote bestands- of gegevensoverdracht van HANA Large Instance naar Azure op de ExpressRoute-gateway die de VM's bedient waarop de SAP-toepassingslaag wordt uitgevoerd. 

Voor een meer schaalbare netwerkarchitectuur:

- Maak gebruik van meerdere virtuele netwerken voor één grotere SAP-toepassingslaag.
- Implementeer één afzonderlijk virtueel netwerk voor elk geïmplementeerd SAP-systeem, in vergelijking met het combineren van deze SAP-systemen in afzonderlijke subnetten onder hetzelfde virtuele netwerk.

  Een meer schaalbare netwerkarchitectuur voor SAP HANA op Azure (Large Instances):

![SAP-toepassingslaag implementeren via meerdere virtuele netwerken](./media/hana-overview-architecture/image4-networking-architecture.png)

Afhankelijk van de regels en beperkingen, wilt u toepassen tussen de verschillende virtuele netwerken hosting VM's van verschillende SAP-systemen, moet u peer die virtuele netwerken. Zie [Virtueel netwerkpeeren](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)voor meer informatie over virtueel netwerkpeeren.


## <a name="routing-in-azure"></a>Routering in Azure

Standaard implementatie zijn drie overwegingen voor netwerkroutering belangrijk voor SAP HANA op Azure (Large Instances):

* SAP HANA op Azure (Large Instances) is alleen toegankelijk via Azure VM's en de speciale ExpressRoute-verbinding, niet rechtstreeks van on-premises. Directe toegang van on-premises tot de HANA Large Instance units, zoals geleverd door Microsoft aan u, is niet onmiddellijk mogelijk. De tijdelijke routeringsbeperkingen zijn te wijten aan de huidige Azure-netwerkarchitectuur die wordt gebruikt voor SAP HANA Large Instance. Sommige beheerclients en alle toepassingen die directe toegang nodig hebben, zoals SAP Solution Manager die on-premises wordt uitgevoerd, kunnen geen verbinding maken met de SAP HANA-database. Voor uitzonderingen raadpleegt u de sectie 'Directe routering naar HANA Large Instances'.

* Als hana-eenheden voor grote instanties zijn geïmplementeerd in twee verschillende Azure-regio's voor herstel na noodgevallen, zijn in het verleden dezelfde tijdelijke routeringsbeperkingen toegepast. Met andere woorden, IP-adressen van een HANA Large Instance-eenheid in een regio (bijvoorbeeld US West) zijn niet doorgestuurd naar een HANA Large Instance-eenheid die in een andere regio (bijvoorbeeld HET Oosten van de VS) wordt geïmplementeerd. Deze beperking was onafhankelijk van het gebruik van Azure-netwerkpeering in verschillende regio's of het cross-aansluiten van de ExpressRoute-circuits die HANA Large Instance-eenheden verbinden met virtuele netwerken. Zie de afbeelding in de sectie 'HanA-eenheden voor grote instanties in meerdere regio's gebruiken' voor een grafische weergave. Deze beperking, die bij de geïmplementeerde architectuur kwam, verbood het onmiddellijke gebruik van HANA-systeemreplicatie als noodherstelfunctionaliteit. Zoek voor recente wijzigingen de sectie 'HanA-eenheden voor grote instanties in meerdere regio's gebruiken' op. 

* SAP HANA op Azure -eenheden (Large Instances) heeft een toegewezen IP-adres van het IP-pooladresbereik van de server dat u hebt ingediend bij het aanvragen van de IMPLEMENTATIE van hana-grote instanties. Zie [SAP HANA (Large Instances) infrastructuur en connectiviteit op Azure voor](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)meer informatie. Dit IP-adres is toegankelijk via het Azure-abonnementen en -circuit dat virtuele Azure-netwerken verbindt met HANA Large Instances. Het IP-adres dat is toegewezen aan het IP-pooladresbereik van die server, wordt rechtstreeks toegewezen aan de hardware-eenheid. Het is *niet* meer toegewezen via NAT, zoals het geval was in de eerste implementaties van deze oplossing. 

### <a name="direct-routing-to-hana-large-instances"></a>Directe routering naar HANA-grote exemplaren

Standaard werkt de tijdelijke routering niet in deze scenario's:

* Tussen HANA Large Instance units en een on-premises implementatie.

* Tussen HANA Large Instance routing die worden geïmplementeerd in twee verschillende regio's.

Er zijn drie manieren om tijdelijke routering in die scenario's mogelijk te maken:

- Een reverse-proxy om gegevens te routeren, van en naar. F5 BIG-IP, NGINX met Traffic Manager geïmplementeerd in het Virtuele Azure-netwerk dat verbinding maakt met HANA Large Instances en on-premises als een virtuele firewall/traffic routing-oplossing.
- [Iptables-regels](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) gebruiken in een Linux-VM om routering tussen on-premises locaties en HANA Large Instance-eenheden of tussen HANA Large Instance-eenheden in verschillende regio's mogelijk te maken. De VM met IPTables moet worden geïmplementeerd in het virtuele Azure-netwerk dat verbinding maakt met HANA Large Instances en on-premises. De VM moet dienovereenkomstig worden aangepast, zodat de netwerkdoorvoer van de VM voldoende is voor het verwachte netwerkverkeer. Voor meer informatie over de bandbreedte van het VM-netwerk raadpleegt u het artikel [Grootte van virtuele Linux-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) zou een andere oplossing zijn om direct verkeer tussen on-premises en HANA Large instance units mogelijk te maken. 

Al het verkeer van deze oplossingen zou worden gerouteerd via een virtueel Azure-netwerk en als zodanig kan het verkeer bovendien worden beperkt door de gebruikte zachte apparaten of door Azure Network Security Groups, zodat bepaalde IP-adressen of IP-adresbereiken van on-premises kunnen worden geblokkeerd of expliciet toegang tot HANA Large Instances kunnen worden toegestaan. 

> [!NOTE]  
> Houd er rekening mee dat implementatie en ondersteuning voor aangepaste oplossingen met netwerkapparaten van derden of IPTables niet door Microsoft wordt geleverd. De steun moet worden verleend door de leverancier van het gebruikte onderdeel of de integrator. 

#### <a name="express-route-global-reach"></a>Express Route Wereldwijd bereik
Microsoft introduceerde een nieuwe functionaliteit genaamd [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Global Reach kan worden gebruikt voor HANA Large Instances in twee scenario's:

- Directe toegang inschakelen van on-premises tot uw HANA Large Instance-eenheden die in verschillende regio's zijn geïmplementeerd
- Directe communicatie tussen uw HANA-eenheden voor grote instanties in verschillende regio's inschakelen


##### <a name="direct-access-from-on-premises"></a>Directe toegang vanuit on-premises
In de Azure-regio's waar Global Reach wordt aangeboden, u de Global Reach-functionaliteit voor uw ExpressRoute-circuit inschakelen die uw on-premises netwerk verbindt met het virtuele Azure-netwerk dat ook verbinding maakt met uw HANA Large Instance-eenheden. Er zijn enkele kostenimplicaties voor de on-premises kant van uw ExpressRoute-circuit. Voor prijzen, controleer de prijzen voor [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Er zijn geen extra kosten voor u in verband met het circuit dat de HANA Large Instance-eenheid(s) verbindt met Azure. 

> [!IMPORTANT]  
> In het geval van het gebruik van Global Reach voor het inschakelen van directe toegang tussen uw HANA Large Instance-eenheden en on-premises assets, wordt de netwerkgegevens- en controlestroom **niet omgeleid via virtuele Azure-netwerken,** maar rechtstreeks tussen de Microsoft enterprise exchange-routers. Als gevolg hiervan worden nsg- of ASG-regels of elk type firewall, NVA of proxy die u hebt geïmplementeerd in een virtueel Azure-netwerk niet aangeraakt. **Als u ExpressRoute Global Reach gebruikt om directe toegang van on-premises naar HANA Large instance units beperkingen en machtigingen voor toegang tot HANA grote Instance units in te schakelen, moeten worden gedefinieerd in firewalls aan de on-premises kant** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>HANA-grote exemplaren verbinden in verschillende Azure-regio's
Op dezelfde manier kan expressroute global reach worden gebruikt voor het aansluiten van on-premises op HANA Large Instance-eenheden, het kan worden gebruikt om twee HANA Large Instance-tenants die voor u zijn geïmplementeerd in twee verschillende regio's aan te sluiten. De isolatie is de ExpressRoute-circuits die uw HANA Large Instance-tenants gebruiken om in beide regio's verbinding te maken met Azure. Er zijn geen extra kosten voor het aansluiten van twee HANA Large Instance-tenants die in twee verschillende regio's worden geïmplementeerd. 

> [!IMPORTANT]  
> De gegevensstroom en controlestroom van het netwerkverkeer tussen de verschillende HANA Large instance tenants worden niet omgeleid via azure-netwerken. Als gevolg hiervan u azure-functionaliteit of NVA's niet gebruiken om communicatiebeperkingen af te dwingen tussen uw twee HANA Large Instances-tenants. 

Lees het document [Een virtueel netwerk verbinden met HANA-grote exemplaren voor](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route)meer informatie over hoe U ExpressRoute Global Reach inschakelen.


## <a name="internet-connectivity-of-hana-large-instance"></a>Internetverbinding van HANA Large Instance
HANA Large Instance *heeft* geen directe internetverbinding. Deze beperking kan bijvoorbeeld uw mogelijkheid beperken om de os-afbeelding rechtstreeks bij de leverancier van het besturingssysteem te registreren. Mogelijk moet u werken met uw lokale SUSE Linux Enterprise Server Subscription Management Tool-server of Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Gegevensversleuteling tussen VM's en HANA Large Instance
Gegevens die worden overgedragen tussen HANA Large Instance en VM's, worden niet versleuteld. Echter, puur voor de uitwisseling tussen de HANA DBMS kant en JDBC / ODBC-gebaseerde toepassingen, u versleuteling van het verkeer. Zie [deze documentatie van SAP voor](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)meer informatie.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>HANA-eenheden voor grote instanties in meerdere regio's gebruiken

Als u noodherstelset-ups wilt realiseren, moet u SHANA-eenheden voor grote instanties in meerdere Azure-regio's hebben. Zelfs met het gebruik van Azure [Global Vnet Peering] werkt de tijdelijke routering standaard niet tussen HANA Large Instance-tenants in twee verschillende regio's. Global Reach opent echter het communicatiepad tussen de HANA Large Instance-eenheden die u in twee verschillende regio's hebt ingericht. Dit gebruiksscenario van ExpressRoute Global Reach maakt het mogelijk:

 - HANA-systeemreplicatie zonder extra proxy's of firewalls
 - Back-ups kopiëren tussen HANA Large Instance-eenheden in twee verschillende regio's om systeemkopieën of systeemvernieuwingen uit te voeren


![Virtueel netwerk dat is verbonden met Azure Large Instance-stempels in verschillende Azure-regio's](./media/hana-overview-architecture/image8-multiple-regions.png)

De figuur laat zien hoe de verschillende virtuele netwerken in beide regio's zijn verbonden met twee verschillende ExpressRoute-circuits die worden gebruikt om verbinding te maken met SAP HANA op Azure (Large Instances) in beide Azure-regio's (grijze lijnen). Reden voor deze twee kruisverbindingen is om te beschermen tegen een storing van de MEE's aan weerszijden. De communicatiestroom tussen de twee virtuele netwerken in de twee Azure-regio's wordt verondersteld te worden verwerkt over de [globale peering](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) van de twee virtuele netwerken in de twee verschillende regio's (blauwe stippellijn). De dikke rode lijn beschrijft de ExpressRoute Global Reach-verbinding, waarmee de HANA Large Instance-eenheden van uw tenants in twee verschillende regio's met elkaar kunnen communiceren. 

> [!IMPORTANT] 
> Als u meerdere ExpressRoute-circuits hebt gebruikt, moeten de BGP-instellingen voor as-pad en de BGP-instellingen voor lokale voorkeur worden gebruikt om een goede routering van het verkeer te garanderen.

**Volgende stappen**
- Verwijs [SAP HANA -opslagarchitectuur (Large Instances)](hana-storage-architecture.md)
