---
title: Herstel na nood gevallen voor Azure Service Fabric | Microsoft Docs
description: Azure Service Fabric biedt de mogelijkheden die nodig zijn om alle typen nood gevallen te behandelen. In dit artikel worden de soorten rampen beschreven die zich kunnen voordoen en hoe u deze kunt verwerken.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1be891d38eb918d65cd8efda86e9a81fa629cf38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883994"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Herstel na nood geval in azure Service Fabric
Een essentieel onderdeel van het leveren van hoge Beschik baarheid zorgt ervoor dat Services alle verschillende soorten storingen kunnen overlaten. Dit is vooral belang rijk voor storingen die ongepland zijn en buiten uw besturings element vallen. In dit artikel worden enkele veelvoorkomende fout modi beschreven die nood herstel kunnen zijn als ze niet correct worden gemodelleerd en beheerd. Ook worden de beperkingen en acties besproken die moeten worden uitgevoerd als er toch een nood geval is opgetreden. Het doel is om het risico van uitval tijd of gegevens verlies te beperken of te elimineren wanneer er fouten optreden, gepland of anders optreden.

## <a name="avoiding-disaster"></a>Nood gevallen voor komen
Het primaire doel van Service Fabric is om u te helpen uw omgeving en uw services zo te model leren dat veelvoorkomende fout typen geen rampen zijn. 

In het algemeen zijn er twee soorten scenario's voor nood gevallen/storingen:

1. Hardware-of software fouten
2. Operationele fouten

### <a name="hardware-and-software-faults"></a>Hardware-en software fouten
Hardware-en software fouten zijn onvoorspelbaar. De eenvoudigste manier om fouten te overleven, is dat er meer exemplaren van de service worden uitgevoerd, verdeeld over hardware-of software fout grenzen. Als uw service bijvoorbeeld wordt uitgevoerd op een bepaalde computer, is het mislukken van de ene computer een nood geval voor die service. De eenvoudige manier om deze nood geval te vermijden, is om ervoor te zorgen dat de service daad werkelijk wordt uitgevoerd op meerdere computers. Testen is ook nodig om ervoor te zorgen dat de actieve service niet wordt verstoord door de ene computer. Capaciteits planning zorgt ervoor dat een vervangend exemplaar op een andere locatie kan worden gemaakt en dat de resterende Services niet overbelasten. Hetzelfde patroon is afhankelijk van wat u wilt voor komen dat de fout optreedt. Bijvoorbeeld. Als u zich zorgen maakt over het mislukken van een SAN, voert u uit op meerdere San's. Als u zich zorgen maakt over het verlies van een rek met servers, voert u uit op meerdere racks. Als u bang bent dat data centers verloren gaan, moet uw service worden uitgevoerd in meerdere Azure-regio's of-data centers. 

Wanneer u dit type spanned modus uitvoert, bent u nog steeds van toepassing op enkele typen gelijktijdige storingen, maar er zijn enkele en zelfs meerdere storingen van een bepaald type (bijvoorbeeld: één virtuele machine of netwerk koppeling mislukt) automatisch verwerkt (en dus niet langer een ' ramp '). Service Fabric biedt een groot aantal mechanismen voor het uitbreiden van het cluster en het opnieuw plaatsen van mislukte knoop punten en services. Service Fabric kunt ook een groot aantal exemplaren van uw services uitvoeren om te voor komen dat deze soorten ongeplande storingen leiden tot echte rampen.

Er kunnen redenen zijn waarom het uitvoeren van een implementatie die groot genoeg is voor storingen, niet haalbaar is. Het is bijvoorbeeld mogelijk dat er meer hardwarebronnen worden gebruikt dan u wilt betalen ten opzichte van de kans op fouten. Bij het verwerken van gedistribueerde toepassingen, kan het zijn dat extra communicatie-hops of status replicatie kosten tussen geografische afstanden een onaanvaardbaar latentie veroorzaken. Wanneer deze regel wordt getekend, verschilt voor elke toepassing. Voor software fouten is het mogelijk dat de fout zich in de service bevindt die u probeert te schalen. In dit geval wordt de nood herstel bewerking niet voor komen, omdat de fout voorwaarde voor alle exemplaren is gecorreleerd.

### <a name="operational-faults"></a>Operationele fouten
Zelfs als uw service wordt verspreid over de hele wereld met veel redundantie, kan deze nog steeds disastrous-gebeurtenissen ervaren. Als iemand bijvoorbeeld per ongeluk de DNS-naam voor de service opnieuw configureert, of deze verwijdert. Stel dat u een stateful Service Fabric-service hebt en iemand die service per ongeluk hebt verwijderd. Tenzij er sprake is van een andere beperking, is die service en alle statussen hiervan nu verdwenen. Voor deze typen operationele rampen (' al') zijn verschillende oplossingen vereist en de herstel stappen dan bij normale, niet-geplande storingen. 

De beste manieren om deze typen operationele fouten te vermijden, zijn
1. operationele toegang tot de omgeving beperken
2. strikt controleren op gevaarlijke bewerkingen
3. pas automatisering toe, voorkom hand matig of buiten-band wijzigingen en valideer specifieke wijzigingen in de daad werkelijke omgeving voordat u ze toebrengt
4. Zorg ervoor dat destructieve bewerkingen ' zacht ' zijn. Tijdelijke bewerkingen worden niet onmiddellijk van kracht of kunnen binnen een bepaald tijd venster ongedaan worden gemaakt

Service Fabric biedt enkele mechanismen om operationele fouten te voor komen, zoals het bieden van toegangs beheer op [basis van rollen](service-fabric-cluster-security-roles.md) voor cluster bewerkingen. Voor de meeste van deze operationele fouten zijn echter bedrijfs activiteiten en andere systemen vereist. Service Fabric biedt een aantal mechanismen voor het naleven van operationele fouten, met name back-up en herstel voor stateful Services.

## <a name="managing-failures"></a>Fouten beheren
Het doel van Service Fabric is bijna altijd automatisch beheer van fouten. Voor het afhandelen van bepaalde soorten fouten, moeten services echter extra code hebben. Andere soorten fouten mogen _niet_ automatisch worden aangepakt als gevolg van veiligheids-en bedrijfs continuïteits redenen. 

### <a name="handling-single-failures"></a>Verwerking van enkele fouten
Eén machine kan om verschillende redenen mislukken. Enkele van deze oorzaken zijn hardware, zoals voedingen en hardwarestoringen. Andere fouten bevinden zich in software. Dit omvat fouten van het werkelijke besturings systeem en de service zelf. Service Fabric detecteert deze typen fouten automatisch, met inbegrip van gevallen waarin de computer wordt geïsoleerd van andere computers vanwege netwerk problemen.

Ongeacht het type van de service, leidt het uitvoeren van één exemplaar tot uitval tijd voor die service als één exemplaar van de code om een of andere reden mislukt. 

Als u één storing wilt afhandelen, kunt u het eenvoudigste doen door ervoor te zorgen dat uw services standaard worden uitgevoerd op meer dan één knoop punt. Voor stateless Services kan dit worden bereikt door een `InstanceCount` groter dan 1 te hebben. Voor stateful Services is de minimale aanbeveling altijd een `TargetReplicaSetSize` en `MinReplicaSetSize` van ten minste 3. Wanneer u meer exemplaren van uw service code uitvoert, zorgt u ervoor dat de service een enkele fout automatisch kan verwerken. 

### <a name="handling-coordinated-failures"></a>Gecoördineerde fouten verwerken
Er kunnen gecoördineerde fouten optreden in een cluster als gevolg van geplande of ongeplande infrastructuur fouten en wijzigingen, of geplande software wijzigingen. Service Fabric modellen infrastructuur zones die gecoördineerde fouten als fout domeinen ondervinden. Gebieden die gecoördineerde software wijzigingen zullen ondervinden, worden als upgrade domeinen gemodelleerd. Meer informatie over fout-en upgrade domeinen vindt u in [dit document waarin de](service-fabric-cluster-resource-manager-cluster-description.md) cluster topologie en-definitie worden beschreven.

Bij het plannen van de locatie waar uw services moeten worden uitgevoerd, Service Fabric standaard rekening gehouden met fout-en upgrade domeinen. Service Fabric probeert standaard om ervoor te zorgen dat uw services worden uitgevoerd in verschillende fout-en upgrade domeinen, zodat als de geplande of ongeplande wijzigingen worden aangebracht, uw services beschikbaar blijven. 

Stel dat het mislukken van een stroom storing ertoe leidt dat een rek van de computers tegelijkertijd mislukt. Met meerdere exemplaren van de service die het verlies van veel computers in fout domein fouten uitvoert, wordt er slechts een ander voor beeld van één storing voor een bepaalde service. Daarom is het beheer van fout domeinen essentieel om een hoge Beschik baarheid van uw services te garanderen. Wanneer Service Fabric in azure wordt uitgevoerd, worden fout domeinen automatisch beheerd. In andere omgevingen zijn ze mogelijk niet. Als u uw eigen clusters op locatie bouwt, moet u ervoor zorgen dat u de indeling van het fout domein correct toewijst en plant.

Upgrade domeinen zijn handig voor het model leren van gebieden waar software op hetzelfde moment wordt geüpgraded. Als gevolg hiervan definiëren upgrade domeinen ook vaak de grenzen waarin software wordt uitgevoerd tijdens geplande upgrades. Upgrades van zowel Service Fabric als uw services volgen hetzelfde model. Raadpleeg de volgende documenten voor meer informatie over rolling upgrades, het upgraden van domeinen en het Service Fabric status model waarmee wordt voor komen dat onbedoelde wijzigingen van invloed zijn op het cluster en uw service:

 - [Toepassings upgrade](service-fabric-application-upgrade.md)
 - [Zelf studie voor toepassings upgrade](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric status model](service-fabric-health-introduction.md)

U kunt de indeling van uw cluster visualiseren met behulp van de cluster toewijzing in [service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![knoop punten worden verdeeld over fout domeinen in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Het model leren van fouten, rolling upgrades, het uitvoeren van veel exemplaren van uw service code en-status, plaatsings regels om ervoor te zorgen dat uw services worden uitgevoerd in fout-en upgrade domeinen en ingebouwde status controle is slechts **enkele** van de functies die service Fabric biedt om normale operationele problemen en storingen in nood gevallen te houden. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Gelijktijdige verwerking van hardware-of software fouten
Hierboven hebben we over enkele storingen gesp roken. Zoals u kunt zien, is het eenvoudig om zowel stateless als stateful services te verwerken door meer exemplaren van de code (en de status) uit te voeren die worden uitgevoerd op fout-en upgrade domeinen. Er kunnen ook meerdere gelijktijdige, wille keurige fouten optreden. Dit leidt tot een daad werkelijke nood situatie.


### <a name="random-failures-leading-to-service-failures"></a>Wille keurige fouten die leiden tot service fouten
Stel dat de service een `InstanceCount` van 5 heeft en dat meerdere knoop punten waarop deze instanties worden uitgevoerd, tegelijk zijn mislukt. Service Fabric reageert door automatisch vervangende instanties te maken op andere knoop punten. Er worden nog steeds nieuwe vervangingen gemaakt totdat de service weer het aantal gewenste instanties heeft bereikt. Een ander voor beeld: Stel dat er een stateless service is met een `InstanceCount`van-1, wat betekent dat het wordt uitgevoerd op alle geldige knoop punten in het cluster. Stel dat sommige van deze instanties zijn mislukt. In dit geval Service Fabric u merkt dat de service niet de gewenste status heeft en probeert de instanties te maken op de knoop punten waar ze ontbreken. 

Voor stateful Services is de situatie afhankelijk van of de status van de service persistent is of niet. Het is ook afhankelijk van het aantal replica's dat de service had en de hoeveelheid die is mislukt. Vaststellen of een nood geval is opgetreden voor een stateful service en het beheer ervan drie fasen volgt:

1. Vaststellen of er sprake is van quorum verlies of niet
   - Een quorum verlies is een wille keurig moment dat een meerderheid van de replica's van een stateful service tegelijk actief is, met inbegrip van de primaire.
2. Vaststellen of het quorum verlies permanent is of niet
   - De meeste tijd zijn fouten tijdelijk. Processen worden opnieuw gestart, knoop punten opnieuw worden gestart, Vm's worden opnieuw gestart, het herstellen van netwerk partities. Soms zijn fouten permanent. 
     - Voor services zonder persistente status, een fout in een quorum of meer van replica's resulteert _onmiddellijk_ in permanent quorum verlies. Wanneer Service Fabric quorum verlies detecteert in een stateful, niet-persistente service, wordt dit onmiddellijk door gegeven aan stap 3 door het declareren van (potentieel) gegevens verlies. Als u doorgaat met gegevens verlies, is het zinnig omdat Service Fabric weet dat er geen punt in de wachtrij staat om de replica's terug te zetten, omdat zelfs als ze zijn hersteld, leeg zijn.
     - Als er sprake is van stateful persistente Services, mislukt een fout in een quorum of meer van replica's, Service Fabric te wachten tot de replica's weer actief zijn en het quorum kan worden hersteld. Dit resulteert in een service onderbreking voor _schrijf bewerkingen_ naar de betrokken partitie (of ' replicaset ') van de service. Lees bewerkingen kunnen echter nog steeds mogelijk zijn met beperkte consistentie garanties. De standaard hoeveelheid tijd die Service Fabric wacht totdat het quorum is hersteld, is oneindig, omdat het volgen van een (mogelijke) gegevens verlies gebeurtenis plaatsvindt en andere Risico's ondervindt. Het overschrijven van de standaard waarde voor `QuorumLossWaitDuration` is mogelijk, maar wordt niet aanbevolen. In plaats daarvan moeten alle inspanningen worden uitgevoerd om de replica's te herstellen. Hiervoor moet u de knoop punten met een back-up maken en ervoor zorgen dat de stations die de lokale permanente status hebben opgeslagen, opnieuw kunnen worden gekoppeld. Als het quorum verlies wordt veroorzaakt door een proces fout, probeert Service Fabric automatisch de processen opnieuw te maken en de replica's hierin opnieuw op te starten. Als dit mislukt, Service Fabric status fouten gerapporteerd. Als deze kunnen worden opgelost, worden de replica's meestal weer gegeven. Soms kunnen de replica's echter niet terug worden gebracht. De stations kunnen bijvoorbeeld allemaal mislukken of de computers die fysiek zijn vernietigd. In deze gevallen hebben we nu een gebeurtenis met een permanent quorum verlies. Als u wilt dat Service Fabric stopt met wachten totdat de replica's weer actief zijn, moet een cluster beheerder bepalen welke partities van invloed zijn op de services en de `Repair-ServiceFabricPartition -PartitionId` of `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`-API aanroepen.  Met deze API kunt u de ID van de partitie die van QuorumLoss wordt verplaatst, verplaatsen naar de mogelijke dataloss.

   > [!NOTE]
   > Het is _nooit_ veilig om deze API te gebruiken in plaats van op een gerichte manier op specifieke partities. 
   >

3. Vaststellen of er daad werkelijk gegevens verlies is en herstel van back-ups
   - Als Service Fabric de `OnDataLossAsync`-methode aanroept, is dit altijd het gevolg van _mogelijk_ gegevens verlies. Service Fabric zorgt ervoor dat deze aanroep wordt afgeleverd bij de _meest_ resterende replica. Dit is de meest voortgang van de replica. De reden hiervoor is dat het waarschijnlijk is _dat gegevens verloren_ gaan omdat de resterende replica in feite dezelfde staat heeft als de PDC. Zonder deze status te vergelijken met, is er echter geen goede manier voor Service Fabric of Opera tors om zeker te weten. Op dit punt weet Service Fabric ook dat de andere replica's niet meer terugkeren. Dit was de beslissing die werd genomen toen werd gewacht tot het quorum verlies vanzelf is opgelost. De beste actie voor de service is meestal om te blok keren en te wachten op specifieke administratieve interventie. Wat doet een typische implementatie van de `OnDataLossAsync` methode?
   - Eerst moet u vastleggen dat `OnDataLossAsync` is geactiveerd en alle benodigde beheerders waarschuwingen starten.
   - Normaal gesp roken wordt u op dit moment gepauzeerd en gewacht op verdere beslissingen en hand matige acties moeten worden ondernomen. Dit komt doordat zelfs als er back-ups beschikbaar zijn, moeten deze mogelijk worden voor bereid. Als bijvoorbeeld twee verschillende Services gegevens coördineren, moeten deze back-ups mogelijk worden gewijzigd om ervoor te zorgen dat de gegevens die in de twee services relevant zijn, consistent zijn. 
   - Vaak is er ook enige andere telemetrie of het afbreken van de service. Deze meta gegevens kunnen in andere services of in Logboeken zijn opgenomen. Deze informatie kan worden gebruikt om te bepalen of er aanroepen zijn ontvangen en verwerkt op de primaire server die niet aanwezig waren in de back-up of die zijn gerepliceerd naar deze specifieke replica. Deze moeten mogelijk opnieuw worden afgespeeld of worden toegevoegd aan de back-up voordat herstellen haalbaar is.  
   - Vergelijkingen van de resterende status van de replica naar die zijn opgenomen in back-ups die beschikbaar zijn. Als u de betrouw bare verzamelingen van Service Fabric gebruikt, zijn er hulpprogram ma's en processen beschikbaar om dit te doen, zoals beschreven in [dit artikel](service-fabric-reliable-services-backup-restore.md). Het doel is om te zien of de status binnen de replica voldoende is of dat de back-up mogelijk ontbreekt.
   - Zodra de vergelijking is uitgevoerd, en, indien nodig, de herstel bewerking is voltooid, moet de service code de waarde True retour neren als er status wijzigingen zijn aangebracht. Als de replica heeft vastgesteld dat het de beste beschik bare kopie van de status heeft en geen wijzigingen heeft aangebracht, retourneert onwaar. Waar geeft aan dat alle _Overige_ replica's mogelijk nu inconsistent zijn met deze waarde. Ze worden verwijderd en opnieuw opgebouwd op basis van deze replica. ONWAAR geeft aan dat er geen status wijzigingen zijn aangebracht, zodat de andere replica's kunnen blijven wat ze hebben. 

Het is zeer belang rijk dat service ontwerpers mogelijke gegevens verlies-en fout scenario's oefenen voordat Services ooit in productie worden geïmplementeerd. Ter bescherming tegen de mogelijkheid van gegevens verlies is het belang rijk om regel matig een [back-up te maken van de status](service-fabric-reliable-services-backup-restore.md) van uw stateful Services naar een geografisch redundante opslag. U moet er ook voor zorgen dat u de mogelijkheid hebt om deze te herstellen. Omdat back-ups van veel verschillende services op verschillende tijdstippen worden gemaakt, moet u ervoor zorgen dat u na het herstellen van uw services een consistente weer gave van elkaar hebt. Denk bijvoorbeeld aan een situatie waarbij een service een nummer genereert en opslaat, en deze vervolgens verzendt naar een andere service die deze ook opslaat. Na het herstellen weet u mogelijk dat de tweede service het nummer heeft, maar de eerste niet, omdat deze bewerking niet door de back-up is toegevoegd.

Als u merkt dat de resterende replica's onvoldoende zijn om door te gaan met een scenario voor gegevens verlies en u de service status niet opnieuw kunt opbouwen vanuit telemetrie of uitgeput, bepaalt de frequentie van uw back-ups uw best mogelijke Recovery Point Objective (RPO). Service Fabric biedt veel hulpprogram ma's voor het testen van verschillende fout scenario's, waaronder permanent quorum en gegevens verlies dat is vereist voor het herstellen van een back-up. Deze scenario's zijn opgenomen als onderdeel van de hulpprogram ma's voor test baarheid van Service Fabric, beheerd door de fout analyse service. [Hier](service-fabric-testability-overview.md)vindt u meer informatie over deze hulpprogram ma's en patronen. 

> [!NOTE]
> Systeem services kunnen ook quorum verlies lijden, met de impact die specifiek is voor de betreffende service. Bijvoorbeeld: quorum verlies in de naamgevings service beïnvloedt naam omzetting, terwijl quorum verlies in de failover Manager-service het maken van nieuwe services en failovers blokkeert. Hoewel de Service Fabric-systeem services hetzelfde patroon volgen als uw services voor status beheer, wordt het afgeraden om ze te verplaatsen uit het quorum verlies en tot potentiële gegevens verlies. De aanbeveling is in plaats daarvan te [zoeken](service-fabric-support.md) naar een oplossing die gericht is op uw specifieke situatie.  Normaal gesp roken verdient het de voor keur om eenvoudigweg te wachten tot de down keer dat er replica's worden geretourneerd.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Beschik baarheid van het Service Fabric cluster
Over het algemeen is het Service Fabric cluster zelf een zeer gedistribueerde omgeving zonder enige storings punten. Als er een storing optreedt in een van de knoop punten, worden er geen Beschik baarheid of betrouwbaarheids problemen voor het cluster veroorzaakt, voornamelijk omdat de Service Fabric systeem services dezelfde richt lijnen volgen die eerder werden gegeven: ze worden altijd uitgevoerd met drie of meer replica's standaard, en die systeem Services die stateless worden uitgevoerd op alle knoop punten. De onderliggende Service Fabric netwerk-en fout detectie lagen zijn volledig gedistribueerd. De meeste systeem services kunnen opnieuw worden opgebouwd op basis van meta gegevens in het cluster of u weet hoe u hun status opnieuw moet synchroniseren vanaf andere locaties. De beschik baarheid van het cluster kan worden aangetast als systeem services problemen met quorum verlies voordoen als hierboven beschreven. In dergelijke gevallen is het mogelijk dat u bepaalde bewerkingen op het cluster niet kunt uitvoeren, zoals het starten van een upgrade of het implementeren van nieuwe services, maar het cluster zelf is nog actief. Services die al worden uitgevoerd, blijven in deze omstandigheden actief, tenzij ze moeten worden geschreven naar de systeem services om verder te kunnen werken. Als de Failover Manager bijvoorbeeld zich in quorum verlies voordoet, blijven alle services actief, maar kunnen alle services die niet automatisch opnieuw worden opgestart, omdat hiervoor de betrokkenheid van de Failover Manager nodig is. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Storingen in een Data Center of Azure-regio
In zeldzame gevallen kan een fysiek Data Center tijdelijk niet beschikbaar zijn vanwege stroom uitval of netwerk verbinding. In deze gevallen is uw Service Fabric-clusters en-services in dat Data Center of Azure-regio niet beschikbaar. _Uw gegevens blijven echter behouden_. Voor clusters die in Azure worden uitgevoerd, kunt u updates op de Azure- [status pagina][azure-status-dashboard]bekijken op storingen. In het zeer onwaarschijnlijke geval dat een fysiek Data Center gedeeltelijk of volledig is vernietigd, kunnen eventuele Service Fabric clusters die daar worden gehost of de services erin verloren gaan. Dit omvat alle statussen waarvan geen back-up is gemaakt buiten dat Data Center of de regio.

Er zijn twee verschillende strategieën voor het blijven van de permanente of aanhoudende storing van één Data Center of regio. 

1. Voer afzonderlijke Service Fabric clusters uit in meerdere regio's en gebruik een mechanisme voor failover en failback tussen deze omgevingen. Voor deze sortering van het Active-Active-of Active-passieve model van meerdere clusters is meer beheer-en bewerkings code vereist. Dit vereist ook de coördinatie van back-ups van de services in één Data Center of regio, zodat deze beschikbaar zijn in andere data centers of regio's wanneer er een mislukt. 
2. Voer één Service Fabric-cluster uit dat meerdere data centers of regio's omvat. De mini maal ondersteunde configuratie hiervoor is drie data centers of regio's. Het aanbevolen aantal regio's of data centers is vijf. Hiervoor is een complexere cluster topologie vereist. Het voor deel van dit model is echter dat een storing van het ene Data Center of de regio wordt geconverteerd van een nood geval naar een normale storing. Deze fouten kunnen worden verwerkt door de mechanismen die werken voor clusters binnen één regio. Fout domeinen, upgrade domeinen en de plaatsings regels van Service Fabric zorgen ervoor dat werk belastingen worden gedistribueerd zodat ze normale storingen verdragen. Voor meer informatie over beleid waarmee services in dit type cluster kunnen worden gebruikt, leest u over het [beleid voor plaatsing](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Wille keurige fouten die leiden tot cluster fouten
Service Fabric heeft het concept van Seed-knoop punten. Dit zijn knoop punten die de beschik baarheid van het onderliggende cluster behouden. Deze knoop punten helpen ervoor te zorgen dat het cluster actief blijft door leases te maken met andere knoop punten en te fungeren als tiebreakers tijdens bepaalde soorten netwerk storingen. Als bij wille keurige fouten een meerderheid van de Seed-knoop punten in het cluster wordt verwijderd en deze niet meer wordt teruggebracht, wordt de Federatie ring van uw cluster samen vouwen, net zoals u het quorum van het Seed-knoop punt hebt verloren en het cluster uitvalt. In azure beheert Service Fabric resource provider Service Fabric cluster configuraties en worden standaard Seed-knoop punten gedistribueerd over het primaire knooppunt type fout en upgrade domeinen. Als het primaire NodeType is gemarkeerd als zilver of goud, geldt dat wanneer u een Seed-knoop punt verwijdert door te schalen in uw primaire NodeType of door een Seed-knoop punt hand matig te verwijderen, wordt geprobeerd een ander niet-Seed-knoop punt te promo veren van het primaire NodeType dat beschikbaar is capaciteit en mislukt als u minder beschik bare capaciteit hebt dan het betrouwbaarheids niveau van het cluster vereist voor uw primaire knooppunt type.

In zowel zelfstandige Service Fabric clusters en Azure is het ' primaire knooppunt type ' de seeding die de zaden uitvoert. Bij het definiëren van een primair knooppunt type zal Service Fabric automatisch profiteren van het aantal knoop punten dat wordt gegeven door Maxi maal 9 Seed-knoop punten te maken en 7 replica's van elk van de systeem services. Als een reeks wille keurige fouten tegelijkertijd een meerderheid van deze systeem service replica's uitmaakt, zullen de systeem services quorum verlies invoeren, zoals hierboven is beschreven. Als een meerderheid van de Seed-knoop punten verloren is gegaan, wordt het cluster binnenkort afgesloten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het simuleren van verschillende storingen met behulp van het test baarheids [raamwerk](service-fabric-testability-overview.md)
- Lees andere bronnen voor herstel na nood gevallen en hoge Beschik baarheid. Micro soft heeft een groot aantal richt lijnen gepubliceerd op deze onderwerpen. Hoewel sommige documenten verwijzen naar specifieke technieken voor gebruik in andere producten, bevatten ze veel algemene aanbevolen procedures die u ook kunt Toep assen in de context van de Service Fabric:
  - [Beschikbaarheidscontrolelijst](/azure/architecture/checklist/resiliency-per-service)
  - [Een nood herstel analyse uitvoeren](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen][dr-ha-guide]
- Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
