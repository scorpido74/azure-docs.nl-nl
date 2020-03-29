---
title: Azure Service Fabric-herstel na noodgevallen
description: Azure Service Fabric biedt mogelijkheden om rampen het probleem aan te pakken. In dit artikel worden de soorten rampen beschreven die zich kunnen voordoen en hoe ermee om te gaan.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371644"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Herstel na noodgevallen in Azure Service Fabric
Een essentieel onderdeel van het leveren van hoge beschikbaarheid is ervoor te zorgen dat services alle verschillende soorten storingen kunnen overleven. Dit is vooral belangrijk voor storingen die ongepland zijn en buiten uw controle vallen. 

In dit artikel worden enkele veelvoorkomende foutmodi beschreven die mogelijk rampen zijn als ze niet correct zijn gemodelleerd en beheerd. Het bespreekt ook mitigaties en maatregelen te nemen als een ramp toch gebeurt. Het doel is om het risico van downtime of gegevensverlies te beperken of te elimineren wanneer er al dan niet geplande storingen optreden.

## <a name="avoiding-disaster"></a>Het vermijden van rampen
Het belangrijkste doel van Azure Service Fabric is om u te helpen zowel uw omgeving als uw services zo te modelleren dat veelvoorkomende fouttypen geen rampen zijn. 

In het algemeen zijn er twee soorten scenario's voor rampen/storingen:
- Hardware- en softwarefouten
- Operationele fouten

### <a name="hardware-and-software-faults"></a>Hardware- en softwarefouten
Hardware- en softwarefouten zijn onvoorspelbaar. De eenvoudigste manier om fouten te overleven is het uitvoeren van meer kopieën van de service over hardware of software fout grenzen. 

Als uw service bijvoorbeeld slechts op één machine wordt uitgevoerd, is het uitvallen van die ene machine een ramp voor die service. De eenvoudige manier om deze ramp te voorkomen is ervoor te zorgen dat de service op meerdere machines wordt uitgevoerd. Testen is ook nodig om ervoor te zorgen dat het uitvallen van één machine de bedrijfsservice niet verstoort. Capaciteitsplanning zorgt ervoor dat een vervangende instantie elders kan worden gemaakt en dat capaciteitsvermindering de resterende services niet overbelast. 

Hetzelfde patroon werkt, ongeacht wat je probeert te voorkomen dat het falen van. Als u zich bijvoorbeeld zorgen maakt over het mislukken van een SAN, loopt u meerdere SAN's tegen. Als u zich zorgen maakt over het verlies van een rack van servers, loopt u over meerdere racks. Als u zich zorgen maakt over het verlies van datacenters, moet uw service worden uitgevoerd in meerdere Azure-regio's, in meerdere Azure Availability Zones of in uw eigen datacenters. 

Wanneer een service over meerdere fysieke instanties (machines, racks, datacenters, regio's) wordt overspannen, bent u nog steeds onderhevig aan bepaalde typen gelijktijdige fouten. Maar enkele en zelfs meerdere storingen van een bepaald type (bijvoorbeeld een enkele virtuele machine of netwerkkoppeling niet) worden automatisch verwerkt en dus zijn niet langer een "ramp." 

Service Fabric biedt mechanismen voor het uitbreiden van het cluster en behandelt het terugbrengen van mislukte knooppunten en services. Service Fabric maakt het ook mogelijk om veel exemplaren van uw services uit te voeren om te voorkomen dat ongeplande fouten in echte rampen worden omgezet.

Er kunnen redenen zijn waarom het uitvoeren van een implementatie die groot genoeg is om storingen te overspannen, niet haalbaar is. Er zijn bijvoorbeeld meer hardwarebronnen nodig dan u bereid bent te betalen ten opzichte van de kans op een mislukking. Wanneer u te maken hebt met gedistribueerde toepassingen, kunnen extra communicatiehopof statusreplicatiekosten over geografische afstanden onaanvaardbare latentie veroorzaken. Waar deze lijn wordt getrokken verschilt voor elke toepassing. 

Voor softwarefouten in het bijzonder, de fout kan worden in de service die u probeert te schalen. In dit geval voorkomen meer kopieën de ramp niet, omdat de foutvoorwaarde in alle instanties is gecorreleerd.

### <a name="operational-faults"></a>Operationele fouten
Zelfs als uw service is overspannen over de hele wereld met veel ontslagen, kan het nog steeds rampzalige gebeurtenissen ervaren. Iemand kan bijvoorbeeld per ongeluk de DNS-naam voor de service opnieuw configureren of deze zonder meer verwijderen. 

Stel dat u een stateful Service Fabric-service had en dat iemand die service per ongeluk heeft verwijderd. Tenzij er een andere mitigatie, die dienst en alle van de staat die het had zijn nu verdwenen. Dit soort operationele rampen ("oeps") vereisen andere oplossingen en stappen voor herstel dan reguliere ongeplande fouten. 

De beste manieren om dit soort operationele fouten te voorkomen zijn:
- Beperk de operationele toegang tot de omgeving.
- Controleer gevaarlijke operaties strikt.
- Stel automatisering op, voorkom handmatige of out-of-bandwijzigingen en valideer specifieke wijzigingen ten opzichte van de omgeving voordat u deze opstelt.
- Zorg ervoor dat destructieve bewerkingen 'zacht' zijn. Zachte bewerkingen worden niet onmiddellijk van kracht of kunnen binnen een tijdvenster ongedaan worden gemaakt.

Service Fabric biedt mechanismen om operationele fouten te voorkomen, zoals het bieden van [op rollen gebaseerde](service-fabric-cluster-security-roles.md) toegangscontrole voor clusterbewerkingen. De meeste van deze operationele fouten vereisen echter organisatorische inspanningen en andere systemen. Service Fabric biedt mechanismen voor het overleven van operationele fouten, met name [back-up en herstel voor stateful services.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Fouten beheren
Het doel van Service Fabric is het automatisch beheren van storingen. Maar om bepaalde soorten fouten te verwerken, moeten services extra code hebben. Andere soorten storingen mogen om veiligheidsredenen en _bedrijfscontinuïteitsredenen niet_ automatisch worden aangepakt. 

### <a name="handling-single-failures"></a>Afzonderlijke fouten verwerken
Enkele machines kunnen om allerlei redenen uitvallen. Soms is het hardware oorzaken, zoals voedingen en netwerk hardware storingen. Andere storingen zijn in software. Deze omvatten storingen van het besturingssysteem en de service zelf. Service Fabric detecteert automatisch dit soort storingen, waaronder gevallen waarin de machine wordt geïsoleerd van andere machines als gevolg van netwerkproblemen.

Ongeacht het type service resulteert het uitvoeren van één exemplaar in downtime voor die service als die ene kopie van de code om welke reden dan ook mislukt. 

Om een enkele fout te verwerken, is het eenvoudigste wat u doen ervoor zorgen dat uw services standaard op meer dan één knooppunt worden uitgevoerd. Voor staatloze diensten, `InstanceCount` zorg ervoor dat groter is dan 1. Voor stateful diensten, de `TargetReplicaSetSize` minimale `MinReplicaSetSize` aanbeveling is dat en zijn beide ingesteld op 3. Het uitvoeren van meer kopieën van uw servicecode zorgt ervoor dat uw service elke enkele fout automatisch kan verwerken. 

### <a name="handling-coordinated-failures"></a>Omgaan met gecoördineerde storingen
Gecoördineerde fouten in een cluster kunnen het gevolg zijn van geplande of ongeplande infrastructuurfouten en -wijzigingen of geplande softwarewijzigingen. Service Fabric modelleert infrastructuurzones die gecoördineerde storingen ervaren als *foutdomeinen.* Gebieden die gecoördineerde softwarewijzigingen zullen ervaren, worden gemodelleerd als *upgradedomeinen.* Zie [Een cluster servicestructuur beschrijven met clusterbeheer voor](service-fabric-cluster-resource-manager-cluster-description.md)foutendomeinen, upgradedomeinen en clustertopologie.

Service Fabric houdt standaard rekening met fout- en upgradedomeinen bij het plannen van de plaats waar uw services moeten worden uitgevoerd. Service Fabric probeert er standaard voor te zorgen dat uw services in verschillende fout- en upgradedomeinen worden uitgevoerd, zodat uw services beschikbaar blijven als er geplande of ongeplande wijzigingen plaatsvinden. 

Stel dat het uitvallen van een stroombron ervoor zorgt dat alle machines op een rack tegelijkertijd uitvallen. Met meerdere exemplaren van de service draait, het verlies van veel machines in fout domein storing verandert in slechts een ander voorbeeld van een enkele fout voor een service. Daarom is het beheren van fout- en upgradedomeinen van cruciaal belang om een hoge beschikbaarheid van uw services te garanderen. 

Wanneer u Service Fabric in Azure uitvoert, worden foutdomeinen en upgradedomeinen automatisch beheerd. In andere omgevingen zijn ze dat misschien niet. Als u uw eigen clusters on-premises bouwt, moet u uw lay-out van het foutdomein correct toewijzen en plannen.

Upgradedomeinen zijn handig voor het modelleren van gebieden waar software tegelijkertijd wordt geüpgraded. Hierdoor definiëren upgradedomeinen ook vaak de grenzen waar software wordt uitgeschakeld tijdens geplande upgrades. Upgrades van zowel Service Fabric als uw services volgen hetzelfde model. Zie voor meer informatie over het uitvoeren van upgrades, upgradedomeinen en het statusmodel Service Fabric dat helpt voorkomen dat onbedoelde wijzigingen van invloed zijn op het cluster en uw service:

 - [Toepassingsupgrade](service-fabric-application-upgrade.md)
 - [Zelfstudie voor het bijwerken van toepassingen](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric-statusmodel](service-fabric-health-introduction.md)

U de lay-out van uw cluster visualiseren met behulp van de clusterkaart in [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Knooppunten verspreid over foutdomeinen in Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Het modelleren van foutgebieden, het uitvoeren van upgrades, het uitvoeren van veel exemplaren van uw servicecode en -status, plaatsingsregels om ervoor te zorgen dat uw services over fout- en upgradedomeinen worden uitgevoerd en ingebouwde statusbewaking zijn slechts *enkele* van de functies die Service Fabric biedt om te voorkomen dat normale operationele problemen en fouten in rampen veranderen. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Omgaan met gelijktijdige hardware- of softwarefouten
We hebben het over losse mislukkingen gehad. Zoals u zien, zijn ze gemakkelijk te hanteren voor zowel stateless als stateful services door meer kopieën van de code (en status) te laten uitvoeren in fout- en upgradedomeinen. 

Meerdere gelijktijdige willekeurige fouten kunnen ook gebeuren. Deze hebben meer kans om te leiden tot downtime of een werkelijke ramp.


#### <a name="stateless-services"></a>Staatloze diensten
Het aantal instanties voor een statusloze service geeft het gewenste aantal exemplaren aan dat moet worden uitgevoerd. Wanneer een (of alle) exemplaren mislukken, reageert Service Fabric door automatisch vervangende exemplaren op andere knooppunten te maken. Service Fabric blijft vervangingen maken totdat de service weer op de gewenste instantie is.

Stel dat de statusloze service `InstanceCount` een waarde van -1 heeft. Deze waarde betekent dat één instantie moet worden uitgevoerd op elk knooppunt in het cluster. Als sommige van deze exemplaren mislukken, detecteert Service Fabric dat de service niet in de gewenste status is en probeert het de exemplaren te maken op de knooppunten waar ze ontbreken.

#### <a name="stateful-services"></a>Staatsdiensten
Er zijn twee soorten stateful diensten:
- Stateful met aanhoudende staat.
- Stateful met niet-aanhoudende staat. (Staat wordt opgeslagen in het geheugen.)

Herstel van het mislukken van een stateful service is afhankelijk van het type van de stateful service, hoeveel replica's de service had en hoeveel replica's zijn mislukt.

In een stateful service worden binnenkomende gegevens gerepliceerd tussen replica's (de primaire en alle actieve secondaries). Als een meerderheid van de replica's de gegevens ontvangt, worden gegevens beschouwd als *quorumvastgelegd.* (Voor vijf replica's zijn er drie een quorum.) Dit betekent dat er op elk moment ten minste een quorum van replica's met de nieuwste gegevens zal zijn. Als replica's mislukken (zeg twee van de vijf), kunnen we de quorumwaarde gebruiken om te berekenen of we kunnen herstellen. (Omdat de resterende drie van de vijf replica's nog steeds up, is het gegarandeerd dat ten minste een replica volledige gegevens zal hebben.)

Wanneer een quorum van replica's mislukt, wordt de partitie aangegeven in een *quorumverliesstatus* te zijn. Stel dat een partitie vijf replica's heeft, wat betekent dat ten minste drie gegarandeerd volledige gegevens hebben. Als een quorum (drie van de vijf) replica's mislukt, kan Service Fabric niet bepalen of de resterende replica's (twee van de vijf) voldoende gegevens hebben om de partitie te herstellen. In gevallen waarin Service Fabric quorumverlies detecteert, is het standaardgedrag ervan om extra schrijfbewerkingen naar de partitie te voorkomen, quorumverlies te declareren en te wachten tot een quorum replica's is hersteld.

Bepalen of er een ramp is gebeurd voor een stateful service en deze vervolgens te beheren, volgt drie fasen:

1. Bepalen of er quorumverlies is geweest of niet.
   
   Quorumverlies wordt aangegeven wanneer een meerderheid van de replica's van een stateful service tegelijkertijd is uitgeschakeld.
2. Bepalen of het quorumverlies permanent is of niet.
   
   Meestal zijn storingen van voorbijgaande aard. Processen worden opnieuw gestart, knooppunten worden opnieuw gestart, virtuele machines worden opnieuw gestart en netwerkpartities genezen. Soms, hoewel, mislukkingen zijn permanent. Of fouten permanent zijn of niet hangt af van de vraag of de stateful service blijft zijn status of dat het houdt het alleen in het geheugen: 
   
   - Voor services zonder aanhoudende status leidt een fout van een quorum of meer replica's _onmiddellijk_ tot blijvend quorumverlies. Wanneer Service Fabric quorumverlies detecteert in een stateful niet-permanente service, gaat het onmiddellijk over tot stap 3 door (mogelijk) gegevensverlies aan te geven. Doorgaan naar gegevensverlies is logisch omdat Service Fabric weet dat het geen zin heeft om te wachten tot de replica's terugkomen. Zelfs als ze herstellen, zullen de gegevens verloren gaan vanwege het niet-aanhoudende karakter van de service.
   - Voor stateful persistente services zorgt een fout van een quorum of meer replica's ervoor dat Service Fabric wacht tot de replica's terugkomen en het quorum herstellen. Dit resulteert in een servicestoring voor _eventuele schrijfbewerkingen_ naar de getroffen partitie (of 'replicaset') van de service. Echter, leest kan nog steeds mogelijk zijn met verminderde consistentie garanties. De standaardhoeveelheid tijd die Service Fabric wacht tot het quorum wordt hersteld, is *oneindig,* omdat doorgaan een (potentiële) gebeurtenis voor gegevensverlies is en andere risico's met zich meebrengt. Dit betekent dat Service Fabric niet doorgaat naar de volgende stap, tenzij een beheerder actie onderneemt om gegevensverlies te declareren.
3. Bepalen of gegevens verloren gaan en herstellen van back-ups.

   Als quorumverlies is gedeclareerd (automatisch of via administratieve actie), gaan Service Fabric en de services verder met het bepalen of gegevens daadwerkelijk verloren zijn gegaan. Op dit punt, Service Fabric weet ook dat de andere replica's niet terugkomen. Dat was de beslissing die werd genomen toen we niet langer wachtten tot het quorumverlies zichzelf zou oplossen. De beste manier van handelen voor de dienst is meestal te bevriezen en te wachten op specifieke administratieve interventie.
   
   Wanneer Service Fabric `OnDataLossAsync` de methode aanroept, is dit altijd vanwege _vermoedelijk gegevensverlies._ Service Fabric zorgt ervoor dat deze oproep wordt geleverd aan de _best_ overgebleven replica. Dit is welke replica heeft de meeste vooruitgang geboekt. 
   
   De reden waarom we altijd zeggen _dat er sprake_ is van vermoedelijk e-bestand verlies, is dat het mogelijk is dat de resterende replica dezelfde status heeft als de primaire status toen het quorum verloren ging. Echter, zonder die staat om het te vergelijken met, er is geen goede manier voor Service Fabric of operators om zeker te weten.     
   
   Dus wat doet een `OnDataLossAsync` typische implementatie van de methode doen?
   1. De implementatie `OnDataLossAsync` logs die is geactiveerd, en het vuurt uit alle nodige administratieve waarschuwingen.
   1. Meestal, de uitvoering pauzeert en wacht op verdere beslissingen en handmatige acties worden genomen. Dit komt omdat zelfs als back-ups beschikbaar zijn, ze mogelijk moeten worden voorbereid. 
   
      Als bijvoorbeeld twee verschillende services informatie coördineren, moeten deze back-ups mogelijk worden gewijzigd om ervoor te zorgen dat de informatie waar deze twee services om geven, na het herstel consistent is. 
   1. Vaak is er een andere telemetrie of uitlaat van de dienst. Deze metagegevens kunnen worden opgenomen in andere services of in logboeken. Deze informatie kan worden gebruikt als dat nodig is om te bepalen of er oproepen zijn ontvangen en verwerkt op de primaire die niet aanwezig waren in de back-up of gerepliceerd naar deze specifieke replica. Deze oproepen moeten mogelijk opnieuw worden afgespeeld of aan de back-up worden toegevoegd voordat herstel mogelijk is.  
   1. De implementatie vergelijkt de status van de resterende replica met die in alle beschikbare back-ups. Als u betrouwbare collecties van Service Fabric gebruikt, zijn er [tools en processen](service-fabric-reliable-services-backup-restore.md) beschikbaar om dit te doen. Het doel is om te zien of de status binnen de replica voldoende is, en om te zien wat de back-up zou kunnen ontbreken.
   1. Nadat de vergelijking is voltooid en nadat het herstel is voltooid (indien nodig), moet de servicecode **true** retourneren als er statuswijzigingen zijn aangebracht. Als de replica heeft vastgesteld dat het de best beschikbare kopie van de status was en geen wijzigingen heeft aangebracht, retourneert de code **false.** 
   
      Een waarde van **true** geeft aan dat alle _andere_ resterende replica's nu mogelijk niet in overeenstemming zijn met deze. Ze zullen worden gedropt en herbouwd van deze replica. Een waarde van **false** geeft aan dat er geen statuswijzigingen zijn aangebracht, zodat de andere replica's kunnen houden wat ze hebben. 

Het is van cruciaal belang dat serviceauteurs potentiële scenario's voor gegevensverlies en -fouten uitvoeren voordat services in productie worden geïmplementeerd. Om te beschermen tegen de mogelijkheid van gegevensverlies, is het belangrijk om periodiek [een back-up](service-fabric-reliable-services-backup-restore.md) van de status van een van uw stateful diensten naar een geo-redundante winkel. 

U moet er ook voor zorgen dat u de mogelijkheid om de staat te herstellen. Omdat back-ups van veel verschillende services op verschillende tijdstippen worden genomen, moet u ervoor zorgen dat uw services na een herstel een consistent beeld van elkaar hebben. 

Denk bijvoorbeeld aan een situatie waarin een service een nummer genereert en opslaat en deze vervolgens naar een andere service stuurt die deze ook opslaat. Na een herstel ontdekt u mogelijk dat de tweede service het nummer heeft, maar de eerste niet, omdat de back-up die bewerking niet heeft opgenomen.

Als u erachter komt dat de resterende replica's onvoldoende zijn om door te gaan in een scenario voor gegevensverlies en u de servicestatus niet reconstrueren op basis van telemetrie of uitlaatgassen, bepaalt de frequentie van uw back-ups uw best mogelijke doel voor herstelpunten (RPO). Service Fabric biedt veel tools voor het testen van verschillende foutscenario's, waaronder permanent quorum en gegevensverlies dat moet worden hersteld van een back-up. Deze scenario's zijn opgenomen als onderdeel van de testability tools in Service Fabric, beheerd door de Fault Analysis Service. Zie Inleiding tot de Fault [Analysis Service](service-fabric-testability-overview.md)voor meer informatie over deze tools en patronen. 

> [!NOTE]
> Systeemdiensten kunnen ook quorumverlies lijden. De impact is specifiek voor de betreffende dienst. Quorumverlies in de naamgevingsservice is bijvoorbeeld van invloed op naamomzetting, terwijl quorumverlies in de Failover Manager-service nieuwe servicecreatie en failovers blokkeert. 
> 
> De Service Fabric-systeemservices volgen hetzelfde patroon als uw services voor staatsbeheer, maar we raden u niet aan om ze uit quorumverlies te halen en in mogelijk gegevensverlies te verwerken. In plaats daarvan raden we u aan ondersteuning te [zoeken](service-fabric-support.md) om een oplossing te vinden die is gericht op uw situatie. Het is meestal beter om gewoon te wachten tot de down replica's terug te keren.
>

#### <a name="troubleshooting-quorum-loss"></a>Problemen met quorumverlies oplossen

Replica's kunnen met tussenpozen worden uitgeschakeld als gevolg van een tijdelijke storing. Wacht enige tijd als Service Fabric probeert om ze te brengen. Als replica's langer dan een verwachte duur zijn uitgevoerd, volgt u de volgende acties voor het oplossen van problemen:
- Replica's kunnen crashen. Controleer statusrapporten op replicaniveau en uw toepassingslogboeken. Verzamel crash dumps en neem de nodige maatregelen om te herstellen.
- Het replicaproces kan niet meer reageren. Controleer uw toepassingslogboeken om dit te verifiëren. Verzamel procesdumps en stop het niet-reagerende proces. Service Fabric maakt een vervangingsproces en probeert de replica terug te brengen.
- Knooppunten die de replica's hosten, kunnen worden uitgeschakeld. Start de onderliggende virtuele machine opnieuw op om de knooppunten omhoog te brengen.

Soms is het mogelijk niet mogelijk om replica's te herstellen. De schijven zijn bijvoorbeeld mislukt of de machines reageren fysiek niet. In deze gevallen moet Service Fabric worden verteld niet te wachten op het herstel van replica's.

Gebruik deze methoden *niet* als mogelijk gegevensverlies onaanvaardbaar is om de service online te brengen. In dat geval moeten alle inspanningen worden geleverd om fysieke machines terug te winnen.

De volgende acties kunnen leiden tot gegevensverlies. Controleer voordat je ze volgt.
   
> [!NOTE]
> Het is _nooit_ veilig om deze methoden anders te gebruiken dan op een gerichte manier tegen specifieke partities. 
>

- Gebruik `Repair-ServiceFabricPartition -PartitionId` de `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` of API. Met deze API kan de id van de partitie worden opgegeven om uit quorumverlies te gaan en naar mogelijk gegevensverlies te gaan.
- Als uw cluster regelmatig fouten ondervindt waardoor services in een quorumverliesstatus terecht komen en mogelijk _gegevensverlies acceptabel is,_ kan het opgeven van een geschikte [QuorumLossWaitDuration-waarde](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) ervoor zorgen dat uw service automatisch herstelt. Service Fabric wacht op `QuorumLossWaitDuration` de opgegeven waarde (standaard is oneindig) voordat u herstel uitvoert. We raden deze methode *niet* aan omdat dit onverwachte gegevensverliezen kan veroorzaken.

## <a name="availability-of-the-service-fabric-cluster"></a>Beschikbaarheid van het cluster Service Fabric
In het algemeen is het cluster Service Fabric een sterk gedistribueerde omgeving zonder enkele storingspunten. Een storing van één knooppunt veroorzaakt geen problemen met beschikbaarheid of betrouwbaarheid voor het cluster, voornamelijk omdat de servicefabric-systeemservices dezelfde richtlijnen volgen die eerder zijn opgegeven. Dat wil zeggen, ze worden standaard altijd uitgevoerd met drie of meer replica's en systeemservices die stateloos zijn, worden uitgevoerd op alle knooppunten. 

De onderliggende lagen voor netwerk- en storingsdetectie van Service Fabric zijn volledig verdeeld. De meeste systeemservices kunnen worden herbouwd met metagegevens in het cluster of weten hoe ze hun status vanaf andere plaatsen opnieuw kunnen synchroniseren. De beschikbaarheid van het cluster kan in het gedrang komen als systeemservices in situaties met quorumverlies komen, zoals eerder beschreven. In deze gevallen u bepaalde bewerkingen mogelijk niet uitvoeren op het cluster (zoals het starten van een upgrade of het implementeren van nieuwe services), maar het cluster zelf is nog steeds actief. 

Services op een lopend cluster blijven in deze omstandigheden actief, tenzij ze schrijven aan de systeemservices vereisen om te blijven functioneren. Als Failovermanager bijvoorbeeld quorumverlies heeft, blijven alle services worden uitgevoerd. Maar alle services die niet mislukken, kunnen niet automatisch opnieuw worden opgestart, omdat dit de betrokkenheid van Failover Manager vereist. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Fouten van een datacenter of een Azure-gebied
In zeldzame gevallen kan een fysiek datacenter tijdelijk niet meer beschikbaar zijn door stroomverlies of netwerkconnectiviteit. In deze gevallen zijn uw Service Fabric-clusters en -services in dat datacenter of Azure-gebied niet beschikbaar. Uw gegevens blijven echter _behouden._ 

Voor clusters die in Azure worden uitgevoerd, u updates over storingen weergeven op de [Azure-statuspagina.][azure-status-dashboard] In het hoogst onwaarschijnlijke geval dat een fysiek datacenter gedeeltelijk of volledig wordt vernietigd, kunnen alle Service Fabric-clusters die daar worden gehost of de services erin, verloren gaan. Dit verlies omvat een status waarvan geen back-up is opgenomen buiten dat datacenter of deze regio.

Er zijn twee verschillende strategieën voor het overleven van de permanente of aanhoudende mislukking van een enkel datacenter of regio: 

- Voer afzonderlijke Service Fabric-clusters uit in meerdere dergelijke regio's en gebruik een mechanisme voor failover en failback tussen deze omgevingen. Dit soort multi-cluster actief/actief/passief model vereist extra beheer- en operationele code. Dit model vereist ook coördinatie van back-ups van de services in een datacenter of regio, zodat ze beschikbaar zijn in andere datacenters of regio's wanneer deze mislukt. 
- Voer één Cluster Servicefabric uit dat meerdere datacenters of regio's omvat. De minimaal ondersteunde configuratie voor deze strategie is drie datacenters of regio's. Het aanbevolen aantal regio's of datacenters is vijf. 
  
  Dit model vereist een complexere clustertopologie. Het voordeel is echter dat het falen van een datacenter of regio wordt omgezet van een ramp in een normale fout. Deze fouten kunnen worden verwerkt door de mechanismen die werken voor clusters binnen één regio. Foutdomeinen, upgradedomeinen en servicefabric-plaatsingsregels zorgen ervoor dat workloads worden gedistribueerd, zodat ze normale fouten tolereren. 
  
  Zie [Plaatsingsbeleid voor Service Fabric-services voor](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)meer informatie over beleidsregels waarmee services in dit type cluster kunnen worden ingeschakeld.

### <a name="random-failures-that-lead-to-cluster-failures"></a>Willekeurige fouten die leiden tot clusterfouten
Service Fabric heeft het concept van *zaadknooppunten.* Dit zijn knooppunten die de beschikbaarheid van het onderliggende cluster behouden. 

Seed-knooppunten helpen ervoor te zorgen dat het cluster blijft bestaan door leases met andere knooppunten op te zetten en als tiebreakers te dienen tijdens bepaalde soorten fouten. Als willekeurige fouten een meerderheid van de seednodes in het cluster verwijderen en ze niet snel worden teruggebracht, wordt het cluster automatisch afgesloten. Het cluster mislukt dan. 

In Azure beheert Service Fabric Resource Provider clusterconfiguraties van Service Fabric. Resourceprovider verdeelt standaard seednodes over fout- en upgradedomeinen voor het *primaire knooppunttype.* Als het primaire knooppunttype is gemarkeerd als silver- of gold-duurzaamheid, probeert het cluster, wanneer u een zaadknooppunt verwijdert (door het primaire knooppunttype te schalen of handmatig te verwijderen), een ander niet-zaadknooppunt te promoten vanaf de beschikbare capaciteit van het primaire knooppunt. Deze poging mislukt als u minder beschikbare capaciteit hebt dan het betrouwbaarheidsniveau van uw cluster vereist voor uw primaire knooppunttype.

In zowel zelfstandige Service Fabric-clusters als Azure is het primaire knooppunttype het type dat de zaden uitvoert. Wanneer u een primair knooppunttype definieert, maakt Service Fabric automatisch gebruik van het aantal knooppunten dat wordt geleverd door maximaal negen seedknooppunten en zeven replica's van elke systeemservice te maken. Als een reeks willekeurige fouten een meerderheid van deze replica's tegelijk uitschakelt, worden de systeemservices in het quorumverlies ingevoerd. Als een meerderheid van de zaadknooppunten verloren gaat, wordt het cluster kort daarna afgesloten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het simuleren van verschillende fouten met behulp van het [testabiliteitsframework](service-fabric-testability-overview.md).
- Lees andere resources voor noodherstel en hoge beschikbaarheid. Microsoft heeft een grote hoeveelheid richtlijnen over deze onderwerpen gepubliceerd. Hoewel sommige van deze bronnen verwijzen naar specifieke technieken voor gebruik in andere producten, bevatten ze veel algemene aanbevolen procedures die u toepassen in de context servicestructuur:
  - [Controlelijst voor beschikbaarheid](/azure/architecture/checklist/resiliency-per-service)
  - [Een noodhersteloefening uitvoeren](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Herstel na noodgevallen en hoge beschikbaarheid voor Azure-toepassingen][dr-ha-guide]
- Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
