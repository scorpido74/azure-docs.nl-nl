---
title: 'Azure ExpressRoute: ontwerpen voor herstel na noodgevallen'
description: Deze pagina bevat architecturale aanbevelingen voor herstel na noodgevallen tijdens het gebruik van Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076690"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Ontwerpen voor noodherstel met ExpressRoute private peering

ExpressRoute is ontworpen voor hoge beschikbaarheid om privénetwerkconnectiviteit van carrierkwaliteit te bieden voor Microsoft-bronnen. Met andere woorden, er is geen enkel storingspunt in het ExpressRoute-pad binnen het Microsoft-netwerk. Zie [Ontwerpen voor hoge beschikbaarheid met ExpressRoute voor][HA]ontwerpoverwegingen om de beschikbaarheid van een ExpressRoute-circuit te maximaliseren.

Echter, het nemen van murphy's populaire adagium-*als er iets mis kan gaan, zal het*--in aanmerking, in dit artikel laten we ons richten op oplossingen die verder gaan dan mislukkingen die kunnen worden aangepakt met behulp van een enkele ExpressRoute circuit. Met andere woorden, in dit artikel laten we kijken naar de overwegingen van netwerkarchitectuur voor het bouwen van robuuste backend-netwerkconnectiviteit voor disaster recovery met behulp van georedundante ExpressRoute-circuits.

## <a name="need-for-redundant-connectivity-solution"></a>Behoefte aan redundante connectiviteitsoplossing

Er zijn mogelijkheden en instanties waarin een hele regionale service (of het nu die van Microsoft, netwerkserviceproviders, klant of andere cloudserviceproviders is) wordt afgebroken. De oorzaak voor een dergelijke regionale brede dienstverlening impact zijn natuurlijke calamiteit. Daarom is het voor bedrijfscontinuïteit en bedrijfskritische toepassingen belangrijk om te plannen voor disaster recovery.   

Ongeacht of u uw bedrijfskritieke toepassingen uitvoert in een Azure-regio of on-premises of ergens anders, u een andere Azure-regio gebruiken als uw failoversite. In de volgende artikelen wordt ingegaan op noodherstel van toepassingen en frontend-toegangsperspectieven:

- [Herstel na noodgevallen voor bedrijven][Enterprise DR]
- [SMB-herstel na noodgeval met Azure Site Recovery][SMB DR]

Als u voor bedrijfskritieke operaties afhankelijk bent van ExpressRoute-connectiviteit tussen uw on-premises netwerk en Microsoft, moet uw noodherstelplan ook georedundante netwerkconnectiviteit bevatten. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Uitdagingen bij het gebruik van meerdere ExpressRoute-circuits

Wanneer u dezelfde set netwerken met elkaar verbindt met meer dan één verbinding, introduceert u parallelle paden tussen de netwerken. Parallelle paden, wanneer niet goed ontworpen, kunnen leiden tot asymmetrische routing. Als u stateful entiteiten (bijvoorbeeld NAT, firewall) in het pad hebt, kan asymmetrische routering de verkeersstroom blokkeren.  Meestal komt u via het privé-peeringpad van ExpressRoute geen stateful entiteiten tegen, zoals NAT of Firewalls. Daarom blokkeert asymmetrische routering via ExpressRoute private peering niet noodzakelijkerwijs de verkeersstroom.
 
Als u echter balansverkeer laadt over georedundante parallelle paden, ongeacht of u stateful entiteiten hebt of niet, zou u inconsistente netwerkprestaties ervaren. In dit artikel bespreken we hoe we deze uitdagingen kunnen aanpakken.

## <a name="small-to-medium-on-premises-network-considerations"></a>Kleine tot middelgrote on-premises netwerkoverwegingen

Laten we eens kijken naar het voorbeeld netwerk geïllustreerd in het volgende diagram. In het voorbeeld wordt georedundante ExpressRoute-connectiviteit vastgesteld tussen de on-premises locatie van een Contoso en het VNet van Contoso in een Azure-regio. In het diagram geeft de effen groene lijn het voorkeurspad aan (via ExpressRoute 1) en de stippellijn staat voor stand-bypad (via ExpressRoute 2).

[![1]][1]

Wanneer u ExpressRoute-connectiviteit ontwerpt voor herstel na noodgevallen, moet u rekening houden met:

- met behulp van geo-redundante ExpressRoute-circuits
- gebruik maken van divers netwerk(en) van de serviceprovider voor verschillende ExpressRoute-circuits
- het ontwerpen van elk van het ExpressRoute circuit voor [hoge beschikbaarheid][HA]
- het beëindigen van het verschillende ExpressRoute-circuit op verschillende locaties in het klantennetwerk

Als u routes op alle ExpressRoute-paden identiek adverteert, wordt het on-premises verkeer in balans gebracht over alle ExpressRoute-paden met ECMP-routering (Equal-cost multi-path).

Met de georedundante ExpressRoute-circuits moeten we echter rekening houden met verschillende netwerkprestaties met verschillende netwerkpaden (met name voor netwerklatentie). Om consistentere netwerkprestaties te krijgen tijdens normaal gebruik, u de voorkeur geven aan het ExpressRoute-circuit dat de minimale latentie biedt.

U Azure beïnvloeden om het ene ExpressRoute-circuit te verkiezen boven een ander circuit met behulp van een van de volgende technieken (weergegeven in de volgorde van effectiviteit):

- reclame meer specifieke route over de voorkeur ExpressRoute circuit in vergelijking met andere ExpressRoute circuit(s)
- configureren van een hoger verbindingsgewicht op de verbinding die het virtuele netwerk koppelt aan het gewenste ExpressRoute-circuit
- reclame voor de routes over minder voorkeur ExpressRoute circuit met langere AS Path (AS Path prepend)

### <a name="more-specific-route"></a>Meer specifieke route

Het volgende diagram illustreert het beïnvloeden van ExpressRoute pad selectie met behulp van meer specifieke route advertentie. In het geïllustreerde voorbeeld wordt contoso on-premises /24 IP-bereik geadverteerd als twee /25 adresbereiken via het voorkeurspad (ExpressRoute 1) en als /24 via het stand-by pad (ExpressRoute 2).

[![2]][2]

Omdat /25 specifieker is, vergeleken met /24, zou Azure het verkeer dat bestemd is naar 10.1.11.0/24 via ExpressRoute 1 in de normale staat verzenden. Als beide verbindingen van ExpressRoute 1 naar beneden gaan, dan zou vNet de 10.1.11.0/24 routereclame slechts via UitdrukkelijkeRoute 2 zien; en daarom wordt het stand-by circuit gebruikt in deze storingstoestand.

### <a name="connection-weight"></a>Verbindingsgewicht

De volgende schermafbeelding illustreert het configureren van het gewicht van een ExpressRoute-verbinding via Azure-portal.

[![3]][3]

Het volgende diagram illustreert het beïnvloeden van expressroute padselectie met behulp van verbindingsgewicht. Het standaardverbindingsgewicht is 0. In het onderstaande voorbeeld wordt het gewicht van de verbinding voor ExpressRoute 1 geconfigureerd als 100. Wanneer een VNet een routevoorvoegsel ontvangt dat via meer dan één ExpressRoute-circuit wordt geadverteerd, geeft het VNet de voorkeur aan de verbinding met het hoogste gewicht.

[![4]][4]

Als beide verbindingen van ExpressRoute 1 naar beneden gaan, dan zou vNet de 10.1.11.0/24 routereclame slechts via UitdrukkelijkeRoute 2 zien; en daarom wordt het stand-by circuit gebruikt in deze storingstoestand.

### <a name="as-path-prepend"></a>AS pad prepend

Het volgende diagram illustreert het beïnvloeden van expressroute padselectie met behulp van AS pad prepend. In het diagram geeft de routeadvertentie via ExpressRoute 1 het standaardgedrag van eBGP aan. Op de routeadvertentie via ExpressRoute 2 wordt het ASN van het onterreinnetwerk bovendien voorbereid op het AS-pad van de route. Wanneer dezelfde route wordt ontvangen via meerdere ExpressRoute-circuits, volgens het eBGP-routeselectieproces, geeft VNet de voorkeur aan de route met het kortste AS-pad. 

[![5]][5]

Als beide verbindingen van ExpressRoute 1 naar beneden gaan, dan zou de VNet de routeadvertentie 10.1.11.0/24 alleen via ExpressRoute 2 zien. Bijgevolg zou het langere AS-pad irrelevant worden. Daarom zou het stand-by circuit worden gebruikt in deze storingstaat.

Als u azure gebruikt om een van uw ExpressRoute boven andere te verkiezen, moet u er ook voor zorgen dat het on-premises netwerk ook de voorkeur geeft aan hetzelfde ExpressRoute-pad voor Azure-gebonden verkeer om asymmetrische stromen te voorkomen. Meestal wordt de lokale voorkeurswaarde gebruikt om het on-premises netwerk te beïnvloeden om het ene ExpressRoute-circuit boven het andere te verkiezen. Lokale voorkeur is een interne BGP (iBGP) statistiek. De BGP-route met de hoogste lokale voorkeurswaarde heeft de voorkeur.

> [!IMPORTANT]
> Wanneer u bepaalde ExpressRoute-circuits als stand-by gebruikt, moet u deze actief beheren en periodiek failoverbewerking testen. 
> 

## <a name="large-distributed-enterprise-network"></a>Groot gedistribueerd bedrijfsnetwerk

Wanneer u een groot gedistribueerd bedrijfsnetwerk hebt, hebt u waarschijnlijk meerdere ExpressRoute-circuits. Laten we in deze sectie eens kijken hoe u disaster recovery ontwerpen met behulp van de actief actieve ExpressRoute-circuits, zonder dat er extra stand-bycircuits nodig zijn. 

Laten we eens kijken naar het voorbeeld geïllustreerd in het volgende diagram. In het voorbeeld heeft Contoso twee on-premises locaties die zijn verbonden met twee Contoso IaaS-implementatie in twee verschillende Azure-regio's via ExpressRoute-circuits op twee verschillende peeringlocaties. 

[![6]][6]

Hoe we het disaster recovery ontwerpen heeft invloed op hoe cross regional naar cross locatie (regio1/regio2 naar locatie2/locatie1) verkeer wordt omgeleid. Laten we eens kijken naar twee verschillende rampenarchitecturen die het verkeer tussen regio-locatie en regio's anders doorkruisen.

### <a name="scenario-1"></a>Scenario 1

In het eerste scenario, laten we ontwerpen disaster recovery zodanig dat al het verkeer tussen een Azure-regio en on-premises netwerk stromen via de lokale ExpressRoute circuit in de stabiele staat. Als het lokale ExpressRoute-circuit uitvalt, wordt het externe ExpressRoute-circuit gebruikt voor alle verkeersstromen tussen Azure en on-premises netwerk.

Scenario 1 wordt geïllustreerd in het volgende diagram. In het diagram geven groene lijnen paden aan voor de verkeersstroom tussen VNet1 en on-premises netwerken. De blauwe lijnen geven paden aan voor de verkeersstroom tussen VNet2 en on-premises netwerken. Vaste lijnen geven het gewenste pad aan in de steady-state en de stippellijnen geven het verkeerspad aan in het uitvallen van het overeenkomstige ExpressRoute-circuit dat een steady-state verkeersstroom draagt. 

[![7]][7]

U het scenario ontwerpen met verbindingsgewicht om VNets te beïnvloeden om de verbinding te verkiezen boven lokale peeringlocatie ExpressRoute voor on-premises netwerkgebonden verkeer. Om de oplossing te voltooien, moet u zorgen voor symmetrische omgekeerde verkeersstroom. U de lokale voorkeur op de iBGP-sessie tussen uw BGP-routers (waarop ExpressRoute-circuits aan on-premises zijde worden beëindigd) gebruiken om de voorkeur te geven aan een ExpressRoute-circuit. De oplossing wordt geïllustreerd in het volgende diagram. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Scenario 2 wordt geïllustreerd in het volgende diagram. In het diagram geven groene lijnen paden aan voor de verkeersstroom tussen VNet1 en on-premises netwerken. De blauwe lijnen geven paden aan voor de verkeersstroom tussen VNet2 en on-premises netwerken. In steady-state (vaste lijnen in het diagram) stroomt al het verkeer tussen VNets en on-premises locaties grotendeels via Microsoft backbone en stroomt door de interconnectie tussen on-premises locaties alleen in de storingsstatus (stippellijnen in het diagram) van een ExpressRoute.

[![9]][9]

De oplossing wordt geïllustreerd in het volgende diagram. Zoals geïllustreerd, u het scenario ontwerpen met behulp van een specifiekere route (optie 1) of AS-pad prepend (optie 2) om de selectie van VNet-paden te beïnvloeden. Als u de selectie van on-premises netwerkroutevoor Azure-gebonden verkeer wilt beïnvloeden, moet u de interconnectie tussen de on-premises locatie configureren als minder gunstig. Howe u configureert de interconnectiekoppeling als bij voorkeur, afhankelijk van het routeringsprotocol dat binnen het on-premises netwerk wordt gebruikt. U lokale voorkeur gebruiken met iBGP of metric met IGP (OSPF of IS-IS).

[![10]][10]


## <a name="next-steps"></a>Volgende stappen

In dit artikel bespraken we hoe te ontwerpen voor noodherstel van een ExpressRoute-circuit privé-peering-connectiviteit. In de volgende artikelen wordt ingegaan op noodherstel van toepassingen en frontend-toegangsperspectieven:

- [Herstel na noodgevallen voor bedrijven][Enterprise DR]
- [SMB-herstel na noodgeval met Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "kleine tot middelgrote on-premises netwerkoverwegingen"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "het beïnvloeden van padselectie met meer specifieke routes"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "verbindingsgewicht configureren via Azure-portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "padselectie beïnvloeden met verbindingsgewicht"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "het beïnvloeden van padselectie met AS-padvoorbereiding"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "grote gedistribueerde on-premises netwerkoverwegingen"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "actief actieve ExpressRoute-circuitsoplossing 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "actief actieve ExpressRoute-circuitsoplossing 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





