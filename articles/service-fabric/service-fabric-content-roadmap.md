---
title: Meer informatie over Azure Service Fabric
description: Meer informatie over de kernconcepten en belangrijke gebieden van Azure Service Fabric. Biedt een uitgebreid overzicht van Service Fabric en hoe u microservices maken.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4e6e21f5f9ebfeddb5292e00dc8a929341e77372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458142"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Dus je wilt meer weten over Service Fabric?
Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices.  Service Fabric heeft echter een groot oppervlak en er is veel te leren.  Dit artikel bevat een samenvatting van Service Fabric en beschrijft de kernconcepten, programmeermodellen, levenscyclus van toepassingen, testen, clusters en statusbewaking. Lees het [overzicht](service-fabric-overview.md) en [wat zijn microservices?](service-fabric-overview-microservices.md) voor een introductie en hoe Service Fabric kan worden gebruikt om microservices te maken. Dit artikel bevat geen uitgebreide inhoudslijst, maar houdt wel een link naar overzicht en aan de slag met artikelen voor elk gebied van Service Fabric. 

## <a name="core-concepts"></a>Basisconcepten
[Service Fabric terminologie,](service-fabric-technical-overview.md) [Application model,](service-fabric-application-model.md)en [Ondersteunde programmeermodellen](service-fabric-choose-framework.md) bieden meer concepten en beschrijvingen, maar hier zijn de basisprincipes.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Ontwerptijd: servicetype, servicepakket en manifest, toepassingstype, toepassingspakket en manifest
Een servicetype is de naam/versie die is toegewezen aan de codepakketten, gegevenspakketten en configuratiepakketten van een service. Dit wordt gedefinieerd in een Bestand ServiceManifest.xml. Het servicetype bestaat uit uitvoerbare code- en serviceconfiguratie-instellingen, die tijdens uitvoering worden geladen en statische gegevens die door de service worden verbruikt.

Een servicepakket is een schijfmap met het ServiceManifest.xml-bestand van het servicetype, dat verwijst naar de code, statische gegevens en configuratiepakketten voor het servicetype. Een servicepakket kan bijvoorbeeld verwijzen naar de code, statische gegevens en configuratiepakketten die deel uitmaken van een databaseservice.

Een toepassingstype is de naam/versie die is toegewezen aan een verzameling servicetypen. Dit wordt gedefinieerd in een bestand ApplicationManifest.xml.

![Service Fabric-toepassingstypen en servicetypen][cluster-imagestore-apptypes]

Het toepassingspakket is een schijfmap die het applicationmanifest.xml-bestand bevat, dat verwijst naar de servicepakketten voor elk servicetype dat deel uitmaakt van het toepassingstype. Een toepassingspakket voor een type e-mailtoepassing kan bijvoorbeeld verwijzingen bevatten naar een wachtrijservicepakket, een frontend-servicepakket en een databaseservicepakket.  

De bestanden in de map met toepassingspakket worden gekopieerd naar het afbeeldingsarchief van het Cluster ServiceFabric. U vervolgens een benoemde toepassing maken van dit toepassingstype, dat vervolgens binnen het cluster wordt uitgevoerd. Nadat u een benoemde toepassing hebt gemaakt, u een benoemde service maken op basis van een van de servicetypen van het toepassingstype. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Run time: clusters en knooppunten, benoemde toepassingen, benoemde services, partities en replica's
Een [Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Clusters kunnen naar duizenden machines worden geschaald.

Een machine of VM die onderdeel uitmaakt van een cluster, heet een knooppunt. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken zoals plaatsingseigenschappen. Elke machine of VM heeft een `FabricHost.exe`windows-service voor automatisch starten, die bij `Fabric.exe` `FabricGateway.exe`het opstarten wordt uitgevoerd en vervolgens twee uitvoerbare bestanden start: en . Deze twee executables vormen het knooppunt. Voor ontwikkel- of testscenario's u meerdere knooppunten hosten `Fabric.exe` op `FabricGateway.exe`één machine of vm door meerdere exemplaren van en.

Een benoemde toepassing is een verzameling benoemde services die een bepaalde functie of functies uitvoert. Een service voert een volledige en zelfstandige functie uit (deze kan onafhankelijk van andere services worden gestart en uitgevoerd) en bestaat uit code, configuratie en gegevens. Nadat een toepassingspakket naar het afbeeldingsarchief is gekopieerd, maakt u een instantie van de toepassing binnen het cluster door het toepassingstype van het toepassingspakket op te geven (met de naam/versie). Aan elke instantie van het toepassingstype wordt een URI-naam toegewezen die lijkt op *stof:/MyNamedApp*. Binnen een cluster u meerdere benoemde toepassingen maken vanuit één toepassingstype. U ook benoemde toepassingen maken van verschillende toepassingstypen. Elke benoemde toepassing wordt onafhankelijk beheerd en geversioned.

Nadat u een benoemde toepassing hebt gemaakt, u een instantie van een van de servicetypen (een benoemde service) binnen het cluster maken door het servicetype op te geven (met de naam/versie). Aan elk instantievan het servicetype wordt een URI-naam toegewezen die onder de uri van de benoemde toepassing is ingedeeld. Als u bijvoorbeeld een 'MyDatabase'-service maakt met de naam 'MyNamedApp' met de naam 'MyNamedApp', ziet de URI eruit als: *fabric:/MyNamedApp/MyDatabase*. Binnen een benoemde toepassing u een of meer benoemde services maken. Elke benoemde service kan zijn eigen partitieschema en instantie/replicatellingen hebben. 

Er zijn twee soorten diensten: stateloos en stateful. Stateless services slaan de status niet op binnen de service. Stateless-services hebben helemaal geen permanente opslag of slaan de permanente status op in een externe opslagservice zoals Azure Storage, Azure SQL Database of Azure Cosmos DB. Een stateful service slaat status binnen de service op en maakt gebruik van betrouwbare collecties of betrouwbare actoren programmeermodellen om de status te beheren. 

Wanneer u een benoemde service maakt, geeft u een partitieschema op. Services met grote hoeveelheden status splitsen de gegevens over partities. Elke partitie is verantwoordelijk voor een deel van de volledige status van de service, dat is verspreid over de knooppunten van het cluster.  

In het volgende diagram ziet u de relatie tussen toepassingen en service-exemplaren, partities en replica's.

![Partities en replica's binnen een service][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Partitionering, schalen en beschikbaarheid
[Partitioneren](service-fabric-concepts-partitioning.md) is niet uniek voor Service Fabric. Een bekende vorm van partitionering is het partitioneren van gegevens, of sharding. Stateful services met grote hoeveelheden status splitsen de gegevens over partities. Elke partitie is verantwoordelijk voor een deel van de volledige status van de service. 

De replica's van elke partitie zijn verspreid over de knooppunten van het cluster, waardoor de status van de benoemde service kan [worden geschaald.](service-fabric-concepts-scalability.md) Naarmate de gegevensbehoeften toenemen, groeien partities en brengt Service Fabric partities tussen knooppunten opnieuw in evenwicht om efficiënt gebruik te maken van hardwarebronnen. Als u nieuwe knooppunten aan het cluster toevoegt, brengt Service Fabric de partitiereplica's opnieuw in evenwicht over het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de discussie over de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, u het aantal knooppunten in het cluster verminderen. Service Fabric brengt de partitiereplica's opnieuw in evenwicht in het verminderde aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt.

Binnen een partitie hebben stateless named services instanties, terwijl stateful named services replica's hebben. Meestal hebben stateless named services slechts één partitie omdat ze geen interne status hebben. De partitie-exemplaren voorzien in [beschikbaarheid](service-fabric-availability-services.md). Als één instantie mislukt, blijven andere instanties normaal werken en maakt Service Fabric een nieuwe instantie. Stateful named services behouden hun status binnen replica's en elke partitie heeft zijn eigen replica set. Lees- en schrijfbewerkingen worden uitgevoerd op één replica (het primaire). Wijzigingen in de status van schrijfbewerkingen worden gerepliceerd naar meerdere andere replica's (actieve secondaries genoemd). Als een replica mislukt, bouwt Service Fabric een nieuwe replica van de bestaande replica's.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Staatloze en stateful microservices voor Service Fabric
Met Service Fabric kunt u toepassingen bouwen die uit microservices of containers bestaan. Staatloze microservices (zoals protocolgateways en webproxy's) handhaven buiten een aanvraag en de reactie erop van de service geen veranderlijke status. Azure Cloud Services-werkrollen zijn een voorbeeld van een staatloze service. Stateful microservices (zoals gebruikersaccounts, databases, apparaten, winkelwagentjes en wachtrijen) handhaven een veranderlijke, gezaghebbende status, buiten de aanvraag en de reactie erop. De huidige toepassingen op internetschaal bestaan uit een combinatie van staatloze en stateful microservices. 

Een belangrijke differentiatie met Service Fabric is de sterke focus op het bouwen van stateful diensten, hetzij met de [ingebouwde programmeermodellen](service-fabric-choose-framework.md) of met gecontaineriseerde stateful diensten. De [toepassingsscenario's](service-fabric-application-scenarios.md) beschrijven de scenario's waarin stateful services worden gebruikt.

Waarom hebben stateful microservices samen met staatlozen? De twee belangrijkste redenen zijn:

* U oltp-services (high-throughput, low-latency, failure-tolerante online transactieverwerking) bouwen door code en gegevens op dezelfde machine dicht bij te houden. Enkele voorbeelden zijn interactieve winkelpuien, zoeken, Internet of Things (IoT) systemen, handelssystemen, credit card processing en fraude detectie systemen, en persoonlijke record management.
* U het ontwerp van toepassingen vereenvoudigen. Stateful microservices verwijderen de noodzaak van extra wachtrijen en caches, die traditioneel nodig zijn om de beschikbaarheid en latentie vereisten van een zuiver stateless toepassing aan te pakken. Stateful services zijn van nature hoge beschikbaarheid en lage latentie, waardoor het aantal bewegende onderdelen dat u in uw toepassing als geheel moet beheren, wordt verminderd.

## <a name="supported-programming-models"></a>Ondersteunde programmeermodellen
Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren. Services kunnen de API's van Service Fabric gebruiken om optimaal te profiteren van de functies en toepassingsframeworks van het platform. Services kunnen ook elk gecompileerd uitvoerbaar programma zijn dat in elke taal is geschreven en gehost op een Service Fabric-cluster. Zie [Ondersteunde programmeermodellen](service-fabric-choose-framework.md)voor meer informatie .

### <a name="containers"></a>Containers
Service Fabric implementeert en activeert services standaard als processen. Service Fabric kan ook services implementeren in [containers.](service-fabric-containers-overview.md) Belangrijk is dat u services mengen in processen en services in containers in dezelfde toepassing. Service Fabric ondersteunt implementatie van Linux-containers en Windows-containers op Windows Server 2016. U bestaande toepassingen, stateloze services of stateful services implementeren in containers. 

### <a name="reliable-services"></a>Reliable Services
[Reliable Services](service-fabric-reliable-services-introduction.md) is een lichtgewicht framework voor het schrijven van services die integreren met het Service Fabric-platform en profiteren van de volledige set platformfuncties. Betrouwbare services kunnen stateloos zijn (vergelijkbaar met de meeste serviceplatforms, zoals webservers of Werknemersrollen in Azure Cloud Services), waarbij status wordt gehandhaafd in een externe oplossing, zoals Azure DB of Azure Table Storage. Betrouwbare services kunnen ook stateful zijn, waarbij de status rechtstreeks in de service zelf wordt gehandhaafd met behulp van betrouwbare verzamelingen. Status wordt [zeer beschikbaar](service-fabric-availability-services.md) gesteld door middel van replicatie en gedistribueerd via [partitionering,](service-fabric-concepts-partitioning.md)allemaal automatisch beheerd door Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Het [Reliable Actor-framework](service-fabric-reliable-actors-introduction.md) is gebouwd bovenop Betrouwbare Services en is een toepassingsframework dat het Virtual Actor-patroon implementeert, gebaseerd op het actorontwerppatroon. De Reliable Actor kader maakt gebruik van onafhankelijke eenheden van compute en staat met single-threaded uitvoering genoemd acteurs. Het Reliable Actor-framework biedt ingebouwde communicatie voor actoren en vooraf ingestelde staatspersistentie en scale-outconfiguraties.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric integreert met [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) als een eersteklas programmeermodel voor het bouwen van web- en API-toepassingen.  ASP.NET Core kan op twee verschillende manieren worden gebruikt in Service Fabric:

- Gehost als gast uitvoerbaar. Dit wordt voornamelijk gebruikt om bestaande ASP.NET Core-toepassingen op Service Fabric uit te voeren zonder codewijzigingen.
- Voer binnen in een betrouwbare service. Dit maakt een betere integratie met de Service Fabric runtime mogelijk en maakt stateful ASP.NET Core-services mogelijk.

### <a name="guest-executables"></a>Uitvoerbare gastbestanden
Een [gast uitvoerbaar](service-fabric-guest-executables-introduction.md) is een bestaande, willekeurige uitvoerbare (geschreven in elke taal) gehost op een Service Fabric cluster naast andere diensten. Gastexecutables integreren niet rechtstreeks met Service Fabric API's. Ze profiteren echter nog steeds van functies die het platform biedt, zoals aangepaste status- en belastingrapportage en servicevindbaarheid door REST-API's aan te roepen. Ze hebben ook volledige ondersteuning voor de levenscyclus van toepassingen. 

## <a name="application-lifecycle"></a>Toepassingslevenscyclus
Net als bij andere platforms gaat een toepassing op Service Fabric meestal door de volgende fasen: ontwerp, ontwikkeling, testen, implementatie, upgrade, onderhoud en verwijdering. Service Fabric biedt eersteklas ondersteuning voor de volledige levenscyclus van applicaties van cloudtoepassingen, van ontwikkeling tot implementatie, dagelijks beheer en onderhoud tot uiteindelijke ontmanteling. Het servicemodel maakt het mogelijk verschillende rollen onafhankelijk deel te nemen aan de levenscyclus van de toepassing. De levenscyclus van [de Service Fabric-toepassing](service-fabric-application-lifecycle.md) biedt een overzicht van de API's en hoe deze worden gebruikt door de verschillende rollen gedurende de fasen van de levenscyclus van de Service Fabric-toepassing. 

De volledige levenscyclus van de app kan worden beheerd met [PowerShell-cmdlets,](/powershell/module/ServiceFabric/) [CLI-opdrachten,](service-fabric-sfctl.md) [C# API's,](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient) [Java API's](/java/api/overview/azure/servicefabric)en [REST API's](/rest/api/servicefabric/). U ook pijplijnen voor continue integratie/continue implementatie instellen met behulp van hulpprogramma's zoals [Azure Pipelines](service-fabric-set-up-continuous-integration.md) of [Jenkins.](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## <a name="test-applications-and-services"></a>Testtoepassingen en -services
Om echt cloudservices te maken, is het van cruciaal belang om te controleren of uw toepassingen en services bestand zijn tegen storingen in de echte wereld. De Fault Analysis Service is ontworpen voor het testen van services die zijn gebouwd op Service Fabric. Met de [Fault Analysis Service](service-fabric-testability-overview.md)u zinvolle fouten veroorzaken en volledige testscenario's uitvoeren tegen uw toepassingen. Deze fouten en scenario's oefenen en valideren van de talrijke toestanden en overgangen die een service gedurende zijn hele levensduur zal ervaren, allemaal op een gecontroleerde, veilige en consistente manier.

[Acties](service-fabric-testability-actions.md) zijn gericht op een service voor het testen met behulp van individuele fouten. Een serviceontwikkelaar kan deze gebruiken als bouwstenen om ingewikkelde scenario's te schrijven. Voorbeelden van gesimuleerde fouten zijn:

* Start een knooppunt opnieuw om een willekeurig aantal situaties te simuleren waarin een machine of VM opnieuw wordt opgestart.
* Een replica van uw stateful service verplaatsen om taakverdeling, failover of applicatie-upgrade te simuleren.
* Beroep doen op quorumverlies op een stateful service om een situatie te creëren waarin schrijfbewerkingen niet kunnen doorgaan omdat er niet genoeg 'back-up' of 'secundaire' replica's zijn om nieuwe gegevens te accepteren.
* Beroep op gegevensverlies op een stateful service om een situatie te creëren waarin alle in-memory status volledig is weggevaagd.

[Scenario's](service-fabric-testability-scenarios.md) zijn complexe bewerkingen die bestaan uit een of meer acties. De Fault Analysis Service biedt twee ingebouwde complete scenario's:

* [Chaos scenario](service-fabric-controlled-chaos.md)- simuleert continue, interleaved fouten (zowel sierlijk en onsierlijk) in het cluster gedurende langere tijd.
* [Failoverscenario](service-fabric-testability-scenarios.md#failover-test)- een versie van het chaostestscenario dat zich richt op een specifieke servicepartitie terwijl andere services onaangetast blijven.

## <a name="clusters"></a>Clusters
Een [Service Fabric-cluster](service-fabric-deploy-anywhere.md) is een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd. Clusters kunnen naar duizenden machines worden geschaald. Een machine of VM die deel uitmaakt van een cluster, wordt een clusterknooppunt genoemd. Aan elk knooppunt wordt een knooppuntnaam toegewezen (een tekenreeks). Knooppunten hebben kenmerken zoals plaatsingseigenschappen. Elke machine of VM heeft een `FabricHost.exe`auto-start service, die begint te draaien op boot en start vervolgens twee executables: Fabric.exe en FabricGateway.exe. Deze twee executables vormen het knooppunt. Voor het testen van scenario's u meerdere knooppunten `Fabric.exe` hosten `FabricGateway.exe`op één machine of VM door meerdere exemplaren van en.

Service Fabric-clusters kunnen worden gemaakt op virtuele of fysieke machines met Windows Server of Linux. U Service Fabric-toepassingen implementeren en uitvoeren in elke omgeving waar u een set Windows Server- of Linux-computers hebt die met elkaar verbonden zijn: on-premises, op Microsoft Azure of op elke cloudprovider.

### <a name="clusters-on-azure"></a>Clusters op Azure
Het uitvoeren van Service Fabric-clusters op Azure biedt integratie met andere Azure-functies en -services, waardoor bewerkingen en beheer van het cluster eenvoudiger en betrouwbaarder worden. Een cluster is een Azure Resource Manager-bron, zodat u clusters modelleren zoals alle andere bronnen in Azure. Resource Manager biedt ook eenvoudig beheer van alle resources die door het cluster als één eenheid worden gebruikt. Clusters op Azure zijn geïntegreerd met Azure-diagnostische gegevens en Azure Monitor-logboeken. Clusterknooppunttypen zijn [virtuele machineschaalsets,](/azure/virtual-machine-scale-sets/index)dus er is een functie voor automatisch schalen ingebouwd.

U een cluster op Azure maken via de [Azure-portal,](service-fabric-cluster-creation-via-portal.md)vanuit een [sjabloon](service-fabric-cluster-creation-via-arm.md)of vanuit [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Service Fabric op Linux stelt u in staat om zeer beschikbare, zeer schaalbare applicaties op Linux te bouwen, implementeren en beheren, net zoals u dat op Windows zou doen. De Service Fabric frameworks (Reliable Services and Reliable Actors) zijn beschikbaar in Java op Linux, naast C# (.NET Core). U ook [gastuitvoerbare services](service-fabric-guest-executables-introduction.md) bouwen met elke taal of kader. Het orkestreren van Docker containers wordt ook ondersteund. Docker-containers kunnen uitvoerbare gasten of native Service Fabric-services uitvoeren, die gebruikmaken van de Service Fabric-frameworks. Voor meer informatie, lees over [Service Fabric op Linux](service-fabric-deploy-anywhere.md).

Er zijn een aantal functies die worden ondersteund op Windows, maar niet op Linux. Lees [Verschillen tussen servicestructuur op Linux en Windows](service-fabric-linux-windows-differences.md)voor meer informatie.

### <a name="standalone-clusters"></a>Zelfstandige clusters
Service Fabric biedt een installatiepakket voor u om standalone Service Fabric-clusters on-premises of op elke cloudprovider te maken. Zelfstandige clusters geven u de vrijheid om een cluster te hosten waar u maar wilt. Als uw gegevens onderhevig zijn aan naleving of wettelijke beperkingen, of als u uw gegevens lokaal wilt houden, u uw eigen cluster en toepassingen hosten. Service Fabric-toepassingen kunnen worden uitgevoerd in meerdere hostingomgevingen zonder wijzigingen, dus uw kennis van het bouwen van toepassingen wordt overgedragen van de ene hostingomgeving naar de andere. 

[Uw eerste zelfstandige Service Fabric-cluster maken](service-fabric-cluster-creation-for-windows-server.md)

Linux standalone clusters worden nog niet ondersteund.

### <a name="cluster-security"></a>Clusterbeveiliging
Clusters moeten worden beveiligd om te voorkomen dat onbevoegde gebruikers verbinding maken met uw cluster, vooral wanneer er productieworkloads op worden uitgevoerd. Hoewel het mogelijk is om een onbeveiligd cluster te maken, kunnen anonieme gebruikers verbinding maken als beheereindpunten worden blootgesteld aan het openbare internet. Het is niet mogelijk om later beveiliging in te schakelen op een onbeveiligd cluster: clusterbeveiliging is ingeschakeld tijdens het maken van het cluster.

De clusterbeveiligingsscenario's zijn:
* Beveiliging van knooppunt tot knooppunt
* Client-to-node beveiliging
* Toegangsbeheer op basis van rollen (RBAC)

Lees [Een cluster beveiligen](service-fabric-cluster-security.md)voor meer informatie.

### <a name="scaling"></a>Schalen
Als u nieuwe knooppunten aan het cluster toevoegt, brengt Service Fabric de partitiereplica's en -exemplaren opnieuw in evenwicht in het toegenomen aantal knooppunten. De algehele prestaties van toepassingen verbeteren en de discussie over de toegang tot het geheugen neemt af. Als de knooppunten in het cluster niet efficiënt worden gebruikt, u het aantal knooppunten in het cluster verminderen. Service Fabric brengt de partitiereplica's en instanties opnieuw in evenwicht in het verminderde aantal knooppunten om beter gebruik te maken van de hardware op elk knooppunt. U clusters op Azure [handmatig](service-fabric-cluster-scale-up-down.md) of [programmatisch](service-fabric-cluster-programmatic-scaling.md)schalen. Zelfstandige clusters kunnen [handmatig](service-fabric-cluster-windows-server-add-remove-nodes.md)worden geschaald.

### <a name="cluster-upgrades"></a>Clusterupgrades
Periodiek worden nieuwe versies van de Runtime van Service Fabric uitgebracht. Voer runtime of fabric-upgrades uit op uw cluster, zodat u altijd een [ondersteunde versie](service-fabric-support.md)uitvoert. Naast fabric-upgrades u ook clusterconfiguratie bijwerken, zoals certificaten of toepassingspoorten.

Een cluster van Servicefabric is een bron waarvan u eigenaar bent, maar die gedeeltelijk door Microsoft wordt beheerd. Microsoft is verantwoordelijk voor het patchen van het onderliggende besturingssysteem en het uitvoeren van fabric-upgrades op uw cluster. U instellen dat uw cluster automatische fabric-upgrades ontvangt, wanneer Microsoft een nieuwe versie uitbrengt, of ervoor kiezen om een gewenste ondersteunde fabricversie te selecteren. Structuur- en configuratie-upgrades kunnen worden ingesteld via de Azure-portal of via Resource Manager. Lees Voor meer informatie [een cluster van servicefabric .](service-fabric-cluster-upgrade.md) 

Een standalone cluster is een bron die u volledig bezit. U bent verantwoordelijk voor het patchen van het onderliggende besturingssysteem en het initiëren van stofupgrades. Als uw cluster [https://www.microsoft.com/download](https://www.microsoft.com/download)verbinding kan maken, u instellen dat uw cluster het nieuwe runtime-pakket Service Fabric automatisch downloadt en instelt. U zou dan de upgrade starten. Als uw cluster geen [https://www.microsoft.com/download](https://www.microsoft.com/download)toegang heeft, u het nieuwe runtime-pakket handmatig downloaden vanaf een met internet verbonden machine en vervolgens de upgrade starten. Lees Voor meer informatie [een zelfstandig cluster van servicefabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Statuscontrole
Service Fabric introduceert een [statusmodel](service-fabric-health-introduction.md) dat is ontworpen om ongezonde cluster- en toepassingsvoorwaarden op specifieke entiteiten (zoals clusterknooppunten en servicereplica's) te markeren. Het gezondheidsmodel maakt gebruik van gezondheidsverslaggevers (systeemcomponenten en waakhonden). Het doel is eenvoudig en snel diagnose en reparatie. Service schrijvers moeten vooraf nadenken over gezondheid en hoe [de gezondheid rapportage te ontwerpen](service-fabric-report-health.md#design-health-reporting). Elke aandoening die van invloed kan zijn op de gezondheid moet worden gerapporteerd over, vooral als het kan helpen vlag problemen dicht bij de wortel. De gezondheidsinformatie kan tijd en moeite besparen op foutopsporing en onderzoek zodra de service op schaal in productie is.

De Service Fabric verslaggevers controleren geïdentificeerde voorwaarden van belang. Zij rapporteren over deze voorwaarden op basis van hun lokale visie. Het [gezondheidsarchief](service-fabric-health-introduction.md#health-store) verzamelt gezondheidsgegevens die door alle verslaggevers worden verzonden om te bepalen of entiteiten wereldwijd gezond zijn. Het model is bedoeld om rijk, flexibel en gebruiksvriendelijk te zijn. De kwaliteit van de statusrapporten bepaalt de nauwkeurigheid van de statusweergave van het cluster. False positives die ten onrechte ongezonde problemen weergeven, kunnen een negatieve invloed hebben op upgrades of andere services die gezondheidsgegevens gebruiken. Voorbeelden van dergelijke diensten zijn reparatiediensten en waarschuwingsmechanismen. Daarom is enige gedachte nodig om rapporten te verstrekken die de voorwaarden van belang op de best mogelijke manier vastleggen.

Rapportage kan worden gedaan vanaf:
* De bewaakte servicereplica of -instantie van de service service service gecontroleerd.
* Interne waakhonden die worden geïmplementeerd als servicefabric-service (bijvoorbeeld een serviceloze service die de voorwaarden bewaakt en rapporten uitgeeft). De waakhonden kunnen worden ingezet op alle knooppunten of kunnen worden geinitiseerd aan de bewaakte service.
* Interne waakhonden die op de Service Fabric-knooppunten draaien, maar niet worden geïmplementeerd als Service Fabric-services.
* Externe waakhonden die de bron van buiten het cluster Service Fabric onderzoeken (bijvoorbeeld bewakingsservice zoals Gomez).

Kant-en-klare onderdelen Service Fabric rapporteren de status van alle entiteiten in het cluster. [Systeemstatusrapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) bieden inzicht in cluster- en toepassingsfunctionaliteit en vlagproblemen via de status. Voor toepassingen en services controleren systeemstatusrapporten of entiteiten zijn geïmplementeerd en correct gedragen vanuit het perspectief van de runtime van Service Fabric. De rapporten bieden geen statusbewaking van de bedrijfslogica van de service of detecteren processen die niet meer reageren. Als u gezondheidsinformatie wilt toevoegen die specifiek is voor de logica van uw service, [implementeert u aangepaste statusrapportage](service-fabric-report-health.md) in uw services.

Service Fabric biedt meerdere manieren om [statusrapporten](service-fabric-view-entities-aggregated-health.md) te bekijken die zijn samengevoegd in het gezondheidsarchief:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) of andere visualisatietools.
* Statusquery's (via [PowerShell,](/powershell/module/ServiceFabric/) [CLI,](service-fabric-sfctl.md)de [C# FabricClient API's](/dotnet/api/system.fabric.fabricclient.healthclient) en [Java FabricClient API's](/java/api/system.fabric)of [REST API's).](/rest/api/servicefabric)
* Algemene query's die een lijst met entiteiten retourneren die de status hebben als een van de eigenschappen (via PowerShell, CLI, API's of REST).

## <a name="monitoring-and-diagnostics"></a>Controle en diagnose
[Monitoring en diagnostiek](service-fabric-diagnostics-overview.md) zijn essentieel voor het ontwikkelen, testen en implementeren van applicaties en services in elke omgeving. Service Fabric-oplossingen werken het beste wanneer u monitoring en diagnostiek plant en implementeert die ervoor zorgen dat toepassingen en services werken zoals verwacht in een lokale ontwikkelingsomgeving of in productie.

De belangrijkste doelstellingen van monitoring en diagnostiek zijn:

- Hardware- en infrastructuurproblemen detecteren en diagnosticeren
- Software- en app-problemen detecteren, uitvaltijd van de service verminderen
- Inzicht in het verbruik van hulpbronnen en helpen bij het stimuleren van operationele beslissingen
- Prestaties van toepassingen, service en infrastructuur optimaliseren
- Genereer bedrijfsinzichten en identificeer verbeterpunten
 
De totale workflow van monitoring en diagnostiek bestaat uit drie stappen:

1. Gebeurtenisgeneratie: dit omvat gebeurtenissen (logboeken, traces, aangepaste gebeurtenissen) op het infrastructuurniveau (cluster), platform en toepassings-/serviceniveau
2. Gebeurtenisaggregatie: gegenereerde gebeurtenissen moeten worden verzameld en samengevoegd voordat ze kunnen worden weergegeven
3. Analyse: gebeurtenissen moeten worden gevisualiseerd en toegankelijk in een bepaald formaat, om analyse en weergave mogelijk te maken als dat nodig is

Er zijn meerdere producten beschikbaar die deze drie gebieden bestrijken, en u bent vrij om verschillende technologieën voor elk te kiezen. Lees Voor meer informatie [Monitoring en diagnostische gegevens voor Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Volgende stappen
* Leer hoe u een [cluster maakt in Azure](service-fabric-cluster-creation-via-portal.md) of hoe u een [zelfstandig cluster maakt in Windows](service-fabric-cluster-creation-for-windows-server.md).
* Maak een service met het programmeermodel [Reliable Services](service-fabric-reliable-services-quick-start.md) of [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Meer informatie over het [migreren van Cloud Services](service-fabric-cloud-services-migration-differences.md).
* Leer [services te controleren en te diagnosticeren.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) 
* Meer informatie over [het testen van uw apps en services](service-fabric-testability-overview.md).
* Leer [clusterbronnen beheren en orkestreren](service-fabric-cluster-resource-manager-introduction.md).
* Kijk door de [Service Fabric monsters.](https://aka.ms/servicefabricsamples)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).
* Lees de [teamblog](https://blogs.msdn.microsoft.com/azureservicefabric/) voor artikelen en aankondigingen.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
