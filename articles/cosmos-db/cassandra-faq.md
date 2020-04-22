---
title: Veelgestelde vragen over Azure Cosmos DB API voor Cassandra.
description: Krijg antwoorden op veelgestelde vragen over Azure Cosmos DB API voor Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727383"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Veelgestelde vragen over de Azure Cosmos DB API voor Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Wat zijn enkele belangrijke verschillen tussen Apache Cassandra en Cassandra API?

- Apache Cassandra beveelt een limiet van 100 MB aan voor de grootte van een partitiesleutel. Cassandra API maakt maximaal 10 GB per partitie mogelijk.
- Apache Cassandra u duurzame commits uitschakelen - dat wil zeggen het schrijven overslaan naar de commit log en ga direct naar de Memtable (s). Dit kan leiden tot verlies van gegevens als het knooppunt naar beneden gaat voordat Memtables wordt doorgespoeld naar SStables op schijf. Cosmos DB doet altijd duurzame commits, zodat je nooit gegevensverlies zult hebben.
- Apache Cassandra kan verminderde prestaties zien als de werkbelasting veel vervangingen en/of verwijderingen met zich meebrengt. De reden hiervoor is tombstones dat de leeswerkbelasting moet overslaan om de nieuwste gegevens op te halen. Cassandra API ziet geen verminderde leesprestaties wanneer de werkbelasting veel vervangingen en/of verwijderingen heeft.
- Tijdens scenario's voor hoge vervangingsworkloads moet verdichting worden uitgevoerd om SSTables op schijf samen te voegen (samenvoegen is nodig omdat de schrijfbewerkingen van Apache Cassandra alleen worden toegevoegd, dus meerdere updates worden opgeslagen als afzonderlijke SSTable-vermeldingen die periodiek moeten worden samengevoegd). Dit kan ook leiden tot verminderde leesprestaties tijdens verdichting. Dit gebeurt niet in Cassandra API omdat het geen verdichting implementeert.
- Het instellen van een replicatiefactor van 1 is mogelijk met Apache Cassandra. Het leidt echter tot een lage beschikbaarheid als het enige knooppunt met de gegevens naar beneden gaat. Dit is geen probleem met Azure Cosmos DB Cassandra API omdat er altijd een replicatiefactor van 4 (quorum van 3) is.
- Het toevoegen/verwijderen van knooppunten in Apache Cassandra vereist veel handmatige interventie, maar ook een hoge CPU op het nieuwe knooppunt, terwijl bestaande knooppunten een aantal van hun tokenbereiken verplaatsen naar het nieuwe knooppunt. Dit is hetzelfde bij het ontmantelen van een bestaand knooppunt. Het uitschalen gebeurt echter naadloos onder de motorkap in Azure Cosmos DB Cassandra API, zonder problemen die worden waargenomen in de service/toepassing.
- Het is niet nodig om num_tokens in te stellen op elk knooppunt in het cluster zoals in Apache Cassandra. Knooppunten en tokenbereiken worden volledig beheerd door Cosmos DB.
- Azure Cosmos DB Cassandra API is volledig beheerd, zodat u niet de nodetool commando's zoals reparatie, ontmanteling etc. die worden gebruikt in Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Andere veelgestelde vragen

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Wat wordt de protocolversie ondersteund door Azure Cosmos DB Cassandra API? Is er een plan om andere protocollen te ondersteunen?

Azure Cosmos DB Cassandra API ondersteunt CQL versie 3.x. Het is CQL compatibiliteit is gebaseerd op de openbare [Apache Cassandra GitHub repository](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Als u feedback hebt over het ondersteunen van andere protocollen, laat [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)het ons dan weten via [spraakfeedback van gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db) of stuur een e-mail naar .

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Waarom is het kiezen van een doorvoer voor een tabel een vereiste?

Azure Cosmos DB stelt de standaarddoorvoer voor uw container in op basis van waar u de tabel maakt van - portal of CQL.
Azure Cosmos DB biedt garanties voor prestaties en latentie, met bovengrenzen bij gebruik. Deze garantie is mogelijk wanneer de motor governance kan afdwingen op de activiteiten van de huurder. Het instellen van doorvoer zorgt ervoor dat u de gegarandeerde doorvoer en latentie krijgt, omdat het platform deze capaciteit reserveert en het succes van de werking garandeert.
U [de doorvoer elastisch wijzigen](manage-scale-cassandra.md) om te profiteren van de seizoensgebondenheid van uw toepassing en kosten te besparen.

Het doorvoerconcept wordt uitgelegd in het artikel [Aanvraageenheden in Azure Cosmos DB.](request-units.md) De doorvoer voor een tabel wordt gelijkmatig verdeeld over de onderliggende fysieke partities.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Wat is de standaard RU/s van de tabel wanneer deze is gemaakt via CQL? Wat als ik het moet veranderen?

Azure Cosmos DB gebruikt aanvraageenheden per seconde (RU/s) als valuta voor het leveren van doorvoer. Tabellen gemaakt via CQL hebben 400 RU. U de RU van het portaal wijzigen.

CQL (CQL)

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Wat gebeurt er als de doorvoer is opgebruikt?

Azure Cosmos DB biedt garanties voor prestaties en latentie, met bovengrenzen bij gebruik. Deze garantie is mogelijk wanneer de motor governance kan afdwingen op de activiteiten van de huurder. Dit is mogelijk op basis van het instellen van de doorvoer, wat ervoor zorgt dat u de gegarandeerde doorvoer en latentie krijgt, omdat platform deze capaciteit reserveert en het succes van de werking garandeert.
Wanneer u deze capaciteit overneemt, krijgt u een overbelast foutbericht dat aangeeft dat uw capaciteit is opgebruikt.
0x1001 Overbelast: de aanvraag kan niet worden verwerkt omdat "Aanvraagpercentage groot is". Op dit moment is het essentieel om te zien welke bewerkingen en hun volume dit probleem veroorzaakt. U een idee krijgen over verbruikte capaciteit die over de ingerichte capaciteit gaat met statistieken op de portal. Dan moet u ervoor zorgen dat de capaciteit bijna even veel wordt verbruikt over alle onderliggende partities. Als u ziet dat het grootste deel van de doorvoer wordt verbruikt door één partitie, hebt u scheefgroei van de werkbelasting.

Er zijn statistieken beschikbaar die u laten zien hoe doorvoer wordt gebruikt gedurende uren, dagen en per zeven dagen, over partities of in totaal. Zie [Controleren en debuggen met statistieken in Azure Cosmos DB](use-metrics.md)voor meer informatie.

Diagnostische logboeken worden uitgelegd in het [diagnoslogartikel Azure Cosmos DB.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Is de primaire sleutelkaart voor het partitiesleutelconcept van Azure Cosmos DB?

Ja, de partitiesleutel wordt gebruikt om de entiteit op de juiste locatie te plaatsen. In Azure Cosmos DB wordt het gebruikt om de juiste logische partitie te vinden die is opgeslagen op een fysieke partitie. Het partitioneringsconcept wordt goed uitgelegd in het [artikel Partitie en schaal in Azure Cosmos DB.](partition-data.md) De essentiële take away hier is dat een logische partitie niet moet gaan over de 10-GB limiet vandaag.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Wat gebeurt er als ik een volledige quotummelding krijg die aangeeft dat een partitie vol is?

Azure Cosmos DB is een SLA-gebaseerd systeem dat onbeperkte schaal biedt, met garanties voor latentie, doorvoer, beschikbaarheid en consistentie. Deze onbeperkte opslag is gebaseerd op horizontale schaal uit gegevens met behulp van partitionering als het belangrijkste concept. Het partitioneringsconcept wordt goed uitgelegd in het [artikel Partitie en schaal in Azure Cosmos DB.](partition-data.md)

De limiet van 10 GB voor het aantal entiteiten of items per logische partitie waaraan u zich moet houden. Om ervoor te zorgen dat uw toepassing goed schaalt, raden we u aan *geen* hot partitie te maken door alle informatie in één partitie op te slaan en op te vragen. Deze fout kan alleen komen als uw gegevens scheef zijn: dat wil zeggen,&nbsp;je hebt veel gegevens voor een partitiesleutel (meer dan 10 GB). U de distributie van gegevens vinden via de opslagportal. Manier om deze fout op te lossen is door de tabel opnieuw te maken en een gedetailleerde primaire (partitiesleutel) te kiezen, waardoor gegevens beter kunnen worden verdeeld.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Is het mogelijk om Cassandra API te gebruiken als key value store met miljoenen of miljarden individuele partitiesleutels?

Azure Cosmos DB kan onbeperkte gegevens opslaan door de opslag uit te schalen. Dit staat los van de doorvoer. Ja, u cassandra API altijd gebruiken om sleutel/waarden op te slaan en op te halen door de juiste primaire/partitiesleutel op te geven. Deze afzonderlijke sleutels krijgen hun eigen logische partitie en zitten bovenop fysieke partitie zonder problemen.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Is het mogelijk om meer dan één tabel te maken met Apache Cassandra API van Azure Cosmos DB?

Ja, het is mogelijk om meer dan één tabel te maken met Apache Cassandra API. Elk van deze tabellen wordt behandeld als eenheid voor doorvoer en opslag.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Is het mogelijk om meer dan één tabel achter elkaar te maken?

Azure Cosmos DB is resource-beheerd systeem voor zowel gegevens- als besturingsvlakactiviteiten. Containers zoals verzamelingen, tabellen zijn runtime-entiteiten die zijn ingericht voor een bepaalde doorvoercapaciteit. Het snel achter elkaar maken van deze containers wordt niet verwacht en wordt beperkt. Als u tests hebt die tabellen onmiddellijk laten vallen/maken, probeert u ze te plaatsen.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Wat is het maximum aantal tabellen dat kan worden gemaakt?

Er is geen fysieke limiet op het aantal [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) tabellen, stuur een e-mail op als je een groot aantal tabellen (waar de totale constante grootte gaat meer dan 10 TB aan gegevens) die moeten worden gemaakt van de gebruikelijke 10s of 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Wat is het maximale aantal keyspace dat we kunnen creëren?

Er is geen fysieke limiet op het aantal keyspaces omdat het [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) metagegevenscontainers zijn, stuur een e-mail op als je om de een of andere reden een groot aantal keyspaces hebt.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Is het mogelijk om veel gegevens in te brengen nadat u van de normale tabel bent begonnen?

Ja, uitgaande van uniform gedistribueerde partities, wordt de opslagcapaciteit automatisch beheerd en neemt toe naarmate u meer gegevens invoert. U dus met vertrouwen zoveel gegevens importeren als u nodig hebt zonder knooppunten te beheren en in te richten, en meer. Als u echter veel directe gegevensgroei verwacht, is het logischer om direct in te [voorzien in de verwachte doorvoer](set-throughput.md) in plaats van lager te beginnen en deze onmiddellijk te verhogen.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Is het mogelijk om yaml-bestandsinstellingen te leveren om Apache Casssandra API van Azure Cosmos DB-gedrag te configureren?

Apache Cassandra API van Azure Cosmos DB is een platformservice. Het biedt compatibiliteit op protocolniveau voor het uitvoeren van bewerkingen. Het verbergt de complexiteit van beheer, monitoring en configuratie. Als ontwikkelaar/gebruiker hoeft u zich geen zorgen te maken over beschikbaarheid, grafstenen, sleutelcache, rijcache, bloeifilter en tal van andere instellingen. De Apache Cassandra API van Azure Cosmos DB richt zich op het leveren van lees- en schrijfprestaties die u nodig hebt zonder de overhead van configuratie en beheer.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Ondersteunt Apache Cassandra API voor Azure Cosmos DB de statusopdrachten knooppunttoevoeging/clusterstatus/knooppunt?

Apache Cassandra API is een platformservice die capaciteitsplanning maakt en inspeelt op de elasticiteitseisen voor doorvoer & opslag een fluitje van een cent. Met Azure Cosmos DB voorziet u in doorvoer, die u nodig hebt. Vervolgens u het op en neer een aantal keren door de dag zonder zorgen te maken over het toevoegen / verwijderen van knooppunten of het beheer ervan. Dit houdt in dat u het knooppunt, clusterbeheertool ook niet hoeft te gebruiken.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Wat gebeurt er met betrekking tot verschillende config-instellingen voor keyspace-creatie zoals eenvoudig/netwerk?

Azure Cosmos DB biedt wereldwijde distributie out-of-the-box voor beschikbaarheid en lage latentie redenen. U hoeft geen replica's of andere dingen in te stellen. Alle schrijfbewerkingen zijn altijd blijvend vastgelegd in elke regio waar u schrijft en tegelijkertijd prestatiegaranties biedt.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Wat gebeurt er met betrekking tot verschillende instellingen voor tabelmetadata zoals bloomfilter, caching, read repair change, gc_grace, compressie memtable_flush_period en meer?

Azure Cosmos DB biedt prestaties voor lees-/schrijf- en doorvoerzonder dat u een van de configuratie-instellingen hoeft aan te raken en deze per ongeluk hoeft te manipuleren.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Wordt time-to-live (TTL) ondersteund voor Cassandra tafels?

Ja, TTL wordt ondersteund.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Is het mogelijk om de status van het knooppunt, de replicastatus, de gc en de OS-parameters eerder te controleren met verschillende hulpprogramma's? Wat moet er nu in de gaten worden gehouden?

Azure Cosmos DB is een platformservice die u helpt de productiviteit te verhogen en u geen zorgen te maken over het beheren en bewaken van infrastructuur. U hoeft alleen maar te zorgen voor doorvoer die beschikbaar is op portal statistieken om te vinden of je krijgt gewurgd en verhogen of verlagen van die doorvoer.
Monitor [SLA's](monitor-accounts.md).
Gebruik [Metrische gegevens](use-metrics.md) Diagnostische [logboeken gebruiken](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Welke client SDKs kan werken met Apache Cassandra API van Azure Cosmos DB?

Apache Cassandra SDK's client drivers die CQLv3 gebruiken werden gebruikt voor clientprogramma's. Als u andere stuurprogramma's hebt die u gebruikt of [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)als u problemen ondervindt, stuurt u e-mail naar .

### <a name="is-composite-partition-key-supported"></a>Wordt de samengestelde partitiesleutel ondersteund?

Ja, u de normale syntaxis gebruiken om samengestelde partitiesleutel te maken.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan ik sstableloader gebruiken voor het laden van gegevens?

Nee, sstableloader wordt niet ondersteund.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kan een on-premises Apache Cassandra-cluster worden gekoppeld aan de Cassandra API van Azure Cosmos DB?

Op dit moment heeft Azure Cosmos DB een geoptimaliseerde ervaring voor cloudomgevingen zonder overhead van bewerkingen. Als u koppeling nodig [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) hebt, stuurt u e-mail naar met een beschrijving van uw scenario. We werken aan het aanbieden om het on-premises/andere cloud Cassandra-cluster te koppelen aan de Cassandra API van Cosomos DB.

### <a name="does-cassandra-api-provide-full-backups"></a>Biedt Cassandra API volledige back-ups?

Azure Cosmos DB biedt twee gratis volledige back-ups die vandaag met een interval van vier uur zijn gemaakt voor alle API's. Dit zorgt ervoor dat u geen back-upschema en andere dingen hoeft in te stellen.
Als u de retentie en frequentie wilt [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) wijzigen, stuurt u een e-mail naar of verhoogt u een ondersteuningsaanvraag. U vindt informatie over back-upmogelijkheden in het artikel [Automatische online back-up en herstel met het](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) azure cosmos DB-artikel.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hoe verwerkt de Cassandra API-account failover als een regio uitvalt?

De Azure Cosmos DB Cassandra API leent van het wereldwijd gedistribueerde platform van Azure Cosmos DB. Als u ervoor wilt zorgen dat uw toepassing downtime voor datacenters kan tolereren, schakelt u ten minste één regio in voor het account in de Azure Cosmos [DB-portal Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md) U de prioriteit van de regio instellen met behulp van de portal [Ontwikkelen met Azure Cosmos DB-accounts met meerdere regio's.](high-availability.md)

U zoveel regio's toevoegen als u wilt voor het account en bepalen waar het niet kan mislukken door een failoverprioriteit te geven. Om de database te gebruiken, moet u daar ook een toepassing verstrekken. Wanneer u dit doet, zullen uw klanten geen downtime ervaren.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexeert de Apache Cassandra API standaard alle kenmerken van een entiteit?

Nee. Cassandra API ondersteunt [secundaire indexen](cassandra-secondary-index.md), die zich gedraagt op een zeer vergelijkbare manier als Apache Cassandra. Het indexeert niet standaard elk kenmerk.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan ik de nieuwe Cassandra API SDK lokaal gebruiken met de emulator?

Ja, dit wordt ondersteund. U vindt hier meer informatie over hoe [u](local-emulator.md#cassandra-api) dit inschakelen


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Hoe kan ik gegevens van hun Apache Cassandra clusters migreren naar Cosmos DB?

U [hier](cassandra-import-data.md)lezen over migratieopties.


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Feature x van de reguliere Cassandra API werkt niet zoals vandaag, waar kan de feedback worden verstrekt?

Feedback geven via [feedback van gebruikers.](https://feedback.azure.com/forums/263030-azure-cosmos-db)

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met [het elastisch schalen van een Azure Cosmos DB Cassandra API-account.](manage-scale-cassandra.md)
