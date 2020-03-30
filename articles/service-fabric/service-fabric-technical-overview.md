---
title: Meer informatie over de terminologie van Azure Service Fabric
description: Leer de belangrijkste terminologie en concepten van Service Fabric die in de rest van de documentatie worden gebruikt.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258237"
---
# <a name="service-fabric-terminology-overview"></a>Overzicht van de terminologie van Service Fabric

Azure Service Fabric is een gedistribueerde systemen platform waarmee u gemakkelijk pakket, implementeren en beheren van schaalbare en betrouwbare microservices.  U [Service Fabric-clusters overal hosten:](service-fabric-deploy-anywhere.md)Azure, in een on-premises datacenter of op elke cloudprovider.  Service Fabric is de orchestrator die Azure Service Fabric Mesh van de [voeding aandrijft.](/azure/service-fabric-mesh) U elk framework gebruiken om uw services te schrijven en te kiezen waar u de toepassing wilt uitvoeren vanuit meerdere omgevingskeuzes. In dit artikel wordt de terminologie beschreven die Service Fabric gebruikt om inzicht te krijgen in de termen die in de documentatie worden gebruikt.

## <a name="infrastructure-concepts"></a>Infrastructuurconcepten

**Cluster:** een met het netwerk verbonden set virtuele of fysieke machines waarin uw microservices worden geïmplementeerd en beheerd.  Clusters kunnen naar duizenden machines worden geschaald.

**Knooppunt**: Een machine of VM die deel uitmaakt van een cluster wordt een *knooppunt*genoemd. Aan elk knooppunt wordt een knooppuntnaam (tekenreeks) toegewezen. Knooppunten hebben kenmerken, zoals plaatsingseigenschappen. Elke machine of VM heeft een `FabricHost.exe`windows-service voor automatisch starten, die bij `Fabric.exe` `FabricGateway.exe`het opstarten wordt uitgevoerd en vervolgens twee uitvoerbare bestanden start: en . Deze twee executables vormen het knooppunt. Voor het testen van scenario's u meerdere knooppunten `Fabric.exe` hosten `FabricGateway.exe`op één machine of VM door meerdere exemplaren van en.

## <a name="application-and-service-concepts"></a>Toepassings- en serviceconcepten

**Service Fabric Mesh-toepassing:** Service Fabric Mesh-toepassingen worden beschreven door het Resourcemodel (YAML- en JSON-bronbestanden) en kunnen worden geïmplementeerd in elke omgeving waar Service Fabric wordt uitgevoerd.

**Service Fabric Native Application:** Service Fabric Native Applications worden beschreven door het Native Application Model (XML-gebaseerde applicatie- en servicemanifesten).  Native applications servicefabric kunnen niet worden uitgevoerd in Service Fabric Mesh.

### <a name="service-fabric-mesh-application-concepts"></a>Concepten van servicefabric mesh-toepassingen

**Toepassing:** Een toepassing is de eenheid van implementatie, versiebeheer en levensduur van een Mesh-toepassing. De levenscyclus van elke toepassingsinstantie kan onafhankelijk worden beheerd.  Toepassingen bestaan uit een of meer servicecodepakketten en -instellingen. Een toepassing wordt gedefinieerd met het RM-schema (Azure Resource Model).  Services worden beschreven als eigenschappen van de toepassingsbron in een RM-sjabloon.  Netwerken en volumes die door de toepassing worden gebruikt, worden door de toepassing verwezen.  Bij het maken van een toepassing worden de toepassing, service(s), het netwerk en het volume(en) gemodelleerd met behulp van het Service Fabric Resource Model.

**Service:** Een service in een toepassing vertegenwoordigt een microservice en voert een volledige en zelfstandige functie uit. Elke service bestaat uit een of meer codepakketten die alles beschrijven wat nodig is om de containerafbeelding uit te voeren die aan het codepakket is gekoppeld.  Het aantal services in een toepassing kan worden opgeschaald en omlaag.

**Netwerk:** een netwerkbron maakt een privénetwerk voor uw toepassingen en is onafhankelijk van de toepassingen of services die ernaar verwijzen. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk. Netwerken zijn inzetbare bronnen waarnaar wordt verwezen door toepassingen.

**Codepakket**: Codepakketten beschrijven alles wat nodig is om de containerafbeelding uit te voeren die aan het codepakket is gekoppeld, inclusief het volgende:

* Containernaam, versie en register
* CPU- en geheugenbronnen die nodig zijn voor elke container
* Netwerkeindpunten
* Volumes die in de container moeten worden gemonteerd, verwijzend naar een afzonderlijke volumebron.

Alle codepakketten die worden gedefinieerd als onderdeel van een toepassingsbron, worden samen geïmplementeerd en geactiveerd als een groep.

**Volume:** Volumes zijn mappen die in uw containerexemplaren worden gemonteerd en die u gebruiken om de status aan te houden. Het volumestuurprogramma voor Azure Files monteert een Azure Files-share aan een container en biedt betrouwbare gegevensopslag via elke API die bestandsopslag ondersteunt. Volumes zijn inzetbare resources waarnaar wordt verwezen door toepassingen.

### <a name="service-fabric-native-application-concepts"></a>Native applicationconcepten servicefabric

**Toepassing**: Een toepassing is een verzameling samenstellende services die een bepaalde functie of functies uitvoeren. De levenscyclus van elke toepassingsinstantie kan onafhankelijk worden beheerd.

**Service:** Een service voert een volledige en zelfstandige functie uit en kan onafhankelijk van andere services worden gestart en uitgevoerd. Een service bestaat uit code, configuratie en gegevens. Voor elke service bestaat code uit de uitvoerbare binaire bestanden, bestaat de configuratie uit service-instellingen die kunnen worden geladen tijdens uitvoering en gegevens bestaan uit willekeurige statische gegevens die door de service moeten worden verbruikt.

**Toepassingstype:** de naam/versie die is toegewezen aan een verzameling servicetypen. Het is gedefinieerd `ApplicationManifest.xml` in een bestand en is ingesloten in een map met toepassingspakketten. De map wordt vervolgens gekopieerd naar het afbeeldingsarchief van het Service Fabric-cluster. U vervolgens een benoemde toepassing maken vanuit dit toepassingstype in het cluster.

Lees het artikel [toepassingsmodel](service-fabric-application-model.md) voor meer informatie.

**Toepassingspakket:** een schijfmap met het `ApplicationManifest.xml` bestand van het toepassingstype. Verwijst naar de servicepakketten voor elk servicetype dat deel uitmaakt van het toepassingstype. De bestanden in de map met toepassingspakket worden gekopieerd naar het afbeeldingsarchief van het Service Fabric-cluster. Een toepassingspakket voor een type e-mailtoepassing kan bijvoorbeeld verwijzingen bevatten naar een wachtrijservicepakket, een frontend-servicepakket en een databaseservicepakket.

**Benoemde toepassing**: Nadat u een toepassingspakket naar het afbeeldingsarchief hebt gekopieerd, maakt u een instantie van de toepassing binnen het cluster. U maakt een instantie wanneer u het toepassingstype van het toepassingspakket opgeeft met de naam of versie ervan. Aan elke instantie van het toepassingstype wordt een uri-naam (uniform resource-id) toegewezen die eruitziet als: `"fabric:/MyNamedApp"`. Binnen een cluster u meerdere benoemde toepassingen maken vanuit één toepassingstype. U ook benoemde toepassingen maken van verschillende toepassingstypen. Elke benoemde toepassing wordt onafhankelijk beheerd en geversioned.

**Servicetype:** de naam/versie die is toegewezen aan de codepakketten, gegevenspakketten en configuratiepakketten van een service. Het servicetype wordt gedefinieerd `ServiceManifest.xml` in het bestand en ingesloten in een servicepakketmap. De servicepakketmap wordt vervolgens verwezen door `ApplicationManifest.xml` het bestand van een toepassingspakket. Binnen het cluster u na het maken van een benoemde toepassing een benoemde service maken op basis van een van de servicetypen van het toepassingstype. Het bestand van `ServiceManifest.xml` het servicetype beschrijft de service.

Lees het artikel [toepassingsmodel](service-fabric-application-model.md) voor meer informatie.

Er zijn twee soorten diensten:

* **Stateless:** Gebruik een statusloze service wanneer de permanente status van de service is opgeslagen in een externe opslagservice, zoals Azure Storage, Azure SQL Database of Azure Cosmos DB. Gebruik een stateless service wanneer de service geen permanente opslag heeft. Voor een rekenmachineservice waarbij waarden worden doorgegeven aan de service, wordt bijvoorbeeld een berekening uitgevoerd die deze waarden gebruikt en vervolgens wordt een resultaat geretourneerd.
* **Stateful:** Gebruik een stateful service wanneer u wilt dat Service Fabric de status van uw service beheert via de programmeermodellen Betrouwbare Collecties of Betrouwbare Actoren. Wanneer u een benoemde service maakt, geeft u op hoeveel partities u uw status wilt spreiden voor schaalbaarheid. Geef ook op hoe vaak u uw status moet repliceren op verschillende knooppunten, voor betrouwbaarheid. Elke benoemde service heeft één primaire replica en meerdere secundaire replica's. U wijzigt de status van uw benoemde service wanneer u naar de primaire replica schrijft. Service Fabric repliceert deze status vervolgens naar alle secundaire replica's om uw status synchroon te houden. Servicefabric detecteert automatisch wanneer een primaire replica mislukt en promoot een bestaande secundaire replica naar een primaire replica. Service Fabric maakt vervolgens een nieuwe secundaire replica.  

**Replica's of instanties** verwijzen naar code (en status voor stateful services) van een service die is geïmplementeerd en uitgevoerd. Zie [Replica's en instanties](service-fabric-concepts-replica-lifecycle.md).

**Herconfiguratie** verwijst naar het proces van elke wijziging in de replicaset van een service. Zie [Herconfiguratie](service-fabric-concepts-reconfiguration.md).

**Servicepakket:** een schijfmap met het `ServiceManifest.xml` bestand van het servicetype. Dit bestand verwijst naar de code, statische gegevens en configuratiepakketten voor het servicetype. De bestanden in de map met servicepakketten worden `ApplicationManifest.xml` verwezen door het bestand van het toepassingstype. Een servicepakket kan bijvoorbeeld verwijzen naar de code, statische gegevens en configuratiepakketten die deel uitmaken van een databaseservice.

**Benoemde service:** Nadat u een benoemde toepassing hebt gemaakt, u een instantie van een van de servicetypen binnen het cluster maken. U geeft het servicetype op met de naam/versie. Aan elk instantievan het servicetype wordt een URI-naam toegewezen die onder de uri van de benoemde toepassing is ingedeeld. Als u bijvoorbeeld een 'MyDatabase'-service maakt met de naam 'MyNamedApp' `"fabric:/MyNamedApp/MyDatabase"`met de naam MyNamedApp, ziet de URI eruit als: . Binnen een benoemde toepassing u verschillende benoemde services maken. Elke benoemde service kan zijn eigen partitieschema en instantie- of replicatellingen hebben.

**Codepakket:** een schijfmap met de uitvoerbare bestanden van het servicetype, meestal EXE/DLL-bestanden. De bestanden in de map codepakket worden verwezen `ServiceManifest.xml` door het bestand van het servicetype. Wanneer u een benoemde service maakt, wordt het codepakket gekopieerd naar het knooppunt of de knooppunten die zijn geselecteerd om de benoemde service uit te voeren. Dan begint de code te worden uitgevoerd. Er zijn twee soorten codepakketuitvoerbare bestanden:

* **Gastexecutables**: Executables die worden uitgevoerd als-is op het host besturingssysteem (Windows of Linux). Deze uitvoerbare bestanden koppelen niet aan of verwijzen naar runtime-bestanden van Service Fabric en gebruiken daarom geen programmeermodellen van Service Fabric. Deze uitvoerbare gegevens kunnen sommige functies van Service Fabric niet gebruiken, zoals de naamgevingsservice voor het detectie van eindpunten. Gastuitvoerbare gegevens kunnen geen loadmetrics rapporteren die specifiek zijn voor elke serviceinstantie.
* **Servicehost-uitvoerbare gegevens:** uitvoerbare neigen met programmeermodellen van Service Fabric door te linken naar runtime-bestanden van Service Fabric, waardoor servicefabric-functies worden in- Een benoemde serviceinstantie kan bijvoorbeeld eindpunten registreren bij de naamgevingsservice van Service Fabric en kan ook belastingsstatistieken rapporteren.

**Gegevenspakket:** een schijfmap met statische, alleen-lezen gegevensbestanden van het servicetype, meestal foto-, geluids- en videobestanden. De bestanden in de map gegevenspakket worden verwezen `ServiceManifest.xml` door het bestand van het servicetype. Wanneer u een benoemde service maakt, wordt het gegevenspakket gekopieerd naar het knooppunt of knooppunten dat is geselecteerd om de benoemde service uit te voeren. De code wordt uitgevoerd en heeft nu toegang tot de gegevensbestanden.

**Configuratiepakket:** een schijfmap met statische, alleen-voor-lezen configuratiebestanden van het servicetype, doorgaans tekstbestanden. De bestanden in de map configuratiepakket worden verwezen `ServiceManifest.xml` door het bestand van het servicetype. Wanneer u een benoemde service maakt, worden de bestanden in het configuratiepakket gekopieerd naar een of meer knooppunten die zijn geselecteerd om de benoemde service uit te voeren. Vervolgens begint de code uit te voeren en heeft hij nu toegang tot de configuratiebestanden.

**Containers**: Service Fabric implementeert en activeert standaard services als processen. Service Fabric kan ook services implementeren in containerafbeeldingen. Containers zijn een virtualisatietechnologie die het onderliggende besturingssysteem abstraheert van toepassingen. Een toepassing en de looptijd, afhankelijkheden en systeembibliotheken worden uitgevoerd in een container. De container heeft volledige, privétoegang tot de eigen geïsoleerde weergave van de besturingssysteemconstructies. Service Fabric ondersteunt Windows Server-containers en Docker-containers op Linux. Lees [Service Fabric en containers](service-fabric-containers-overview.md)voor meer informatie.

**Partitieschema:** Wanneer u een benoemde service maakt, geeft u een partitieschema op. Services met aanzienlijke hoeveelheden status splitsen de gegevens over partities, waardoor de status wordt verspreid over de knooppunten van het cluster. Door de gegevens over partities te splitsen, kan de status van uw benoemde service worden geschaald. Binnen een partitie hebben stateless named services instanties, terwijl stateful named services replica's hebben. Meestal hebben stateless named services slechts één partitie, omdat ze geen interne status hebben. De partitie-exemplaren voorzien in beschikbaarheid. Als één instantie mislukt, blijven andere instanties normaal werken en maakt Service Fabric een nieuwe instantie. Stateful named services behouden hun status binnen replica's en elke partitie heeft zijn eigen replica set, zodat de status wordt gehouden in sync. Als een replica mislukt, bouwt Service Fabric een nieuwe replica van de bestaande replica's.

Lees het artikel [over betrouwbare services van Partition Service Fabric](service-fabric-concepts-partitioning.md) voor meer informatie.

## <a name="system-services"></a>Systeemservices

Er zijn systeemservices die in elk cluster worden gemaakt en die de platformmogelijkheden van Service Fabric bieden.

**Naamgevingsservice:** elk cluster van servicefabric heeft een naamgevingsservice, waarmee servicenamen worden opgelost naar een locatie in het cluster. U beheert de servicenamen en -eigenschappen, zoals een DNS (Internet Domain Name System) voor het cluster. Clients communiceren veilig met elk knooppunt in het cluster door de naamgevingsservice te gebruiken om een servicenaam en de locatie op te lossen. Toepassingen worden binnen het cluster verplaatst. Dit kan bijvoorbeeld te wijten zijn aan fouten, resourcebalancing of het wijzigen van de grootte van het cluster. U services en clients ontwikkelen die de huidige netwerklocatie oplossen. Clients verkrijgen het werkelijke IP-adres en de poort van de machine waar deze momenteel wordt uitgevoerd.

Lees [Communiceren met services](service-fabric-connect-and-communicate-with-services.md) voor meer informatie over de CLIENT- en servicecommunicatie-API's die werken met de naamgevingsservice.

**Image Store-service**: Elk cluster van servicefabric heeft een Image Store-service waar geïmplementeerd, versie-toepassingspakketten worden bewaard. Kopieer een toepassingspakket naar de Image Store en registreer vervolgens het toepassingstype in dat toepassingspakket. Nadat het toepassingstype is ingericht, maakt u er een benoemde toepassing van. U een toepassingstype uitschrijven bij de Image Store-service nadat alle benoemde toepassingen zijn verwijderd.

Lees [De instelling ImageStoreConnectionString begrijpen](service-fabric-image-store-connection-string.md) voor meer informatie over de Image Store-service.

Lees [het artikel Een toepassingsartikel implementeren](service-fabric-deploy-remove-applications.md) voor meer informatie over het implementeren van toepassingen naar de Image Store-service.

**Failovermanager-service:** elk cluster van servicefabric heeft een Failover Manager-service die verantwoordelijk is voor de volgende acties:

 - Voert functies uit die verband houden met hoge beschikbaarheid en consistentie van services.
 - Orkestreert toepassings- en clusterupgrades.
 - Werkt met andere systeemcomponenten.

**Service voor reparatiebeheer:** dit is een optionele systeemservice waarmee reparatieacties op een cluster kunnen worden uitgevoerd op een manier die veilig, automatisbaar en transparant is. Reparatiemanager wordt gebruikt in:

   - Azure-onderhoudsreparaties uitvoeren op Azure Service Fabric-clusters voor [silver- en gold-duurzaamheid.](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)
   - Het uitvoeren van reparatieacties voor [Patch Orchestration Application](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Implementatie- en toepassingsmodellen

Als u uw services wilt implementeren, moet u beschrijven hoe ze moeten worden uitgevoerd. Service Fabric ondersteunt drie verschillende implementatiemodellen:

### <a name="resource-model-preview"></a>Resourcemodel (voorbeeld)

Service Fabric Resources zijn alles wat individueel kan worden geïmplementeerd in Service Fabric; toepassingen, services, netwerken en volumes. Resources worden gedefinieerd met behulp van een JSON-bestand, dat kan worden geïmplementeerd op een clustereindpunt.  Voor Service Fabric Mesh wordt het Azure Resource Model-schema gebruikt. Een YAML-bestandsschema kan ook worden gebruikt om eenvoudiger definitiebestanden te maken. Resources kunnen overal worden geïmplementeerd waar Service Fabric wordt uitgevoerd. Het resourcemodel is de eenvoudigste manier om uw Service Fabric-toepassingen te beschrijven. De belangrijkste focus ligt op eenvoudige implementatie en beheer van containerdiensten. Lees [Inleiding tot het Service Fabric Resource Model](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)voor meer informatie.

### <a name="native-model"></a>Native model

Het native applicatiemodel biedt uw toepassingen volledige toegang op laag niveau tot Service Fabric. Toepassingen en services worden gedefinieerd als geregistreerde typen in XML-manifestbestanden.

Het native model ondersteunt de Reliable Services en Reliable Actors frameworks, die toegang bieden tot de Service Fabric runtime API's en cluster management API's in C# en Java. Het native model ondersteunt ook willekeurige containers en executables. Het native model wordt niet ondersteund in de [Service Fabric Mesh-omgeving.](/azure/service-fabric-mesh/service-fabric-mesh-overview)

**Betrouwbare services:** een API om stateloze en stateful services te bouwen. Stateful services slaan hun status op in betrouwbare verzamelingen, zoals een woordenboek of een wachtrij. U ook verschillende communicatiestacks aansluiten, zoals Web API en Windows Communication Foundation (WCF).

**Betrouwbare actoren:** een API om stateloze en stateful objecten te bouwen via het virtuele Actor-programmeermodel. Dit model is handig als u veel onafhankelijke eenheden van berekening of staat hebt. Dit model maakt gebruik van een turn-based threading-model, dus het is het beste om code te vermijden die naar andere actoren of services roept, omdat een afzonderlijke actor geen andere binnenkomende aanvragen kan verwerken totdat alle uitgaande aanvragen zijn voltooid.

U uw bestaande toepassingen ook uitvoeren op Service Fabric:

**Containers**: Service Fabric ondersteunt de implementatie van Docker-containers op Linux- en Windows Server-containers op Windows Server 2016, samen met ondersteuning voor hyper-v-isolatiemodus. In het [toepassingsmodel](service-fabric-application-model.md)Service Fabric vertegenwoordigt een container een toepassingshost waarin meerdere servicereplica's worden geplaatst. Service Fabric kan alle containers uitvoeren en het scenario is vergelijkbaar met het uitvoerbare scenario voor gasten, waarbij u een bestaande toepassing in een container verpakt. Daarnaast u [ook Service Fabric-services in containers uitvoeren.](service-fabric-services-inside-containers.md)

**Gastuitvoerbare gegevens:** u elk type code uitvoeren, zoals Node.js, Python, Java of C++ in Azure Service Fabric as a service. Service Fabric verwijst naar dit soort services als gastexecutables, die worden behandeld als staatloze services. De voordelen van het uitvoeren van een gast die uitvoerbaar is in een Cluster Service Fabric zijn onder hoge beschikbaarheid, statusbewaking, beheer van de levenscyclus van toepassingen, hoge dichtheid en vindbaarheid.

Lees het [programmeermodel kiezen voor uw serviceartikel](service-fabric-choose-framework.md) voor meer informatie.

### <a name="docker-compose"></a>Docker componeren 

[Docker Compose](https://docs.docker.com/compose/) maakt deel uit van het Docker project. Service Fabric biedt beperkte ondersteuning voor [het implementeren van toepassingen met behulp van het Docker Compose-model.](service-fabric-docker-compose.md)

## <a name="environments"></a>Omgevingen

Service Fabric is een open-source platform technologie waar verschillende diensten en producten op gebaseerd zijn. Microsoft biedt de volgende opties:

 - **Azure Service Fabric Mesh:** een volledig beheerde service voor het uitvoeren van Service Fabric-toepassingen in Microsoft Azure.
 - **Azure Service Fabric:** het azure hosted Service Fabric-clusteraanbod. Het biedt integratie tussen Service Fabric en de Azure-infrastructuur, samen met upgrade- en configuratiebeheer van Service Fabric-clusters.
 - **Service Fabric standalone:** een set installatie- en configuratietools om [Service Fabric-clusters overal](/azure/service-fabric/service-fabric-deploy-anywhere) te implementeren (on-premises of op elke cloudprovider). Niet beheerd door Azure.
 - **Service Fabric development cluster**: Biedt een lokale ontwikkelingservaring op Windows, Linux of Mac voor de ontwikkeling van Service Fabric-toepassingen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrix voor ondersteuning van het omgevings-, framework- en implementatiemodel

Verschillende omgevingen hebben verschillende ondersteuningsniveaus voor frameworks en implementatiemodellen. In de volgende tabel worden de ondersteunde combinaties van framework en implementatiemodellen beschreven.

| Type toepassing | Beschreven door | Azure Service Fabric Mesh | Azure Service Fabric-clusters (elk besturingssysteem)| Lokaal cluster | Zelfstandig cluster |
|---|---|---|---|---|---|
| Mesh-toepassingen voor servicefabric | Resourcemodel (YAML & JSON) | Ondersteund |Niet ondersteund | Windows- ondersteund, Linux en Mac- niet ondersteund | Windows- niet ondersteund |
|Native toepassingen voor servicefabric | Native Application Model (XML) | Niet ondersteund| Ondersteund|Ondersteund|Windows- ondersteund|

In de volgende tabel worden de verschillende toepassingsmodellen en de tooling beschreven die voor hen bestaat ten opzichte van Service Fabric.

| Type toepassing | Beschreven door | Visual Studio | Eclipse | SFCTL (SFCTL) | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Mesh-toepassingen voor servicefabric | Resourcemodel (YAML & JSON) | VS 2017 |Niet ondersteund |Niet ondersteund | Alleen ondersteund - Mesh-omgeving | Niet ondersteund|
|Native toepassingen voor servicefabric | Native Application Model (XML) | VS 2017 en VS 2015| Ondersteund|Ondersteund|Ondersteund|Ondersteund|

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Service Fabric:

* [Overzicht van Service Fabric](service-fabric-overview.md)
* [Waarom een microservices-benadering voor het ontwikkelen van toepassingen?](service-fabric-overview-microservices.md)
* [Toepassingsscenario's](service-fabric-application-scenarios.md)

Voor meer informatie over Service Fabric Mesh:

* [Overzicht van Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview)
