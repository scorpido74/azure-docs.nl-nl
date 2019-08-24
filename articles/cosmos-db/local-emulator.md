---
title: Lokaal ontwikkelen met de Azure Cosmos-emulator
description: Met de Azure Cosmos-emulator kunt u uw toepassing lokaal gratis ontwikkelen en testen zonder dat u een Azure-abonnement hoeft te maken.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: 4c26431ee0d506dda547fb4027845baa15c9a134
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997891"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>De Azure Cosmos-emulator gebruiken voor lokale ontwikkeling en tests

De Azure Cosmos-emulator biedt een lokale omgeving die de Azure Cosmos DB-Service voor ontwikkelings doeleinden emuleert. Met de Azure Cosmos-emulator kunt u uw toepassing lokaal ontwikkelen en testen zonder een Azure-abonnement te maken of kosten te betalen. Wanneer u tevreden bent over hoe uw toepassing werkt in de Azure Cosmos-emulator, kunt u overschakelen naar het gebruik van een Azure Cosmos-account in de Cloud.

U kunt ontwikkelen met behulp van de Azure Cosmos-emulator met [SQL](local-emulator.md#sql-api)-, [Cassandra](local-emulator.md#cassandra-api)-, [MongoDb](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)-, [Gremlin](local-emulator.md#gremlin-api)-en [Table](local-emulator.md#table-api) -API-accounts. Op dit moment biedt de Data Explorer-weer gave in de emulator echter volledige ondersteuning voor clients voor de SQL-API. 

## <a name="how-the-emulator-works"></a>Hoe de emulator werkt

De Azure Cosmos-emulator biedt een zeer betrouw bare emulatie van de Azure Cosmos DB-service. Het ondersteunt identieke functionaliteit als Azure Cosmos DB, inclusief ondersteuning voor het maken en opvragen van gegevens, het inrichten en schalen van containers en het uitvoeren van opgeslagen procedures en triggers. U kunt toepassingen ontwikkelen en testen met behulp van de Azure Cosmos-emulator en deze implementeren in azure op wereld wijde schaal door slechts één configuratie wijziging aan te brengen in het verbindings eindpunt voor Azure Cosmos DB.

Emulatie van de Azure Cosmos DB-service is zeer betrouwbaar, maar de implementatie van de emulator is anders dan die van de service. De emulator gebruikt bijvoorbeeld standaardbesturingssysteemonderdelen, zoals het lokale bestandssysteem voor persistentie en de HTTPS-protocolstack voor connectiviteit. Functionaliteit die afhankelijk is van een Azure-infra structuur zoals globale replicatie, latentie in milliseconden voor lees-en schrijf bewerkingen en instel bare consistentie niveaus zijn niet van toepassing.

U kunt gegevens migreren tussen de Azure Cosmos-emulator en de Azure Cosmos DB-service met het [Azure Cosmos DB hulp programma voor gegevens migratie](https://github.com/azure/azure-documentdb-datamigrationtool).

U kunt de Azure Cosmos-emulator uitvoeren op de Windows docker-container. Zie de docker- [hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) voor de docker-opdracht en [github](https://github.com/Azure/azure-cosmos-db-emulator-docker) voor de bron code van de emulator.

## <a name="differences-between-the-emulator-and-the-service"></a>Verschillen tussen de emulator en de service

Omdat de Azure Cosmos-emulator een geëmuleerde omgeving biedt die wordt uitgevoerd op het lokale werk station voor ontwikkel aars, zijn er enkele verschillen in de functionaliteit van de emulator en een Azure Cosmos-account in de Cloud:

* Momenteel Data Explorer in de emulator clients voor SQL API ondersteunt. De Data Explorer weer gave en bewerkingen voor Azure Cosmos DB-Api's, zoals MongoDB-, Table-, Graph-en Cassandra-api's, worden niet volledig ondersteund.
* De Azure Cosmos-emulator ondersteunt slechts één vaste account en een bekende hoofd sleutel. Het opnieuw genereren van sleutels is niet mogelijk in de Azure Cosmos-emulator, maar de standaard sleutel kan worden gewijzigd met behulp van de opdracht regel optie.
* De Azure Cosmos-emulator is geen schaal bare service en biedt geen ondersteuning voor een groot aantal containers.
* De Azure Cosmos-emulator biedt geen verschillende [Azure Cosmos DB-consistentie niveaus](consistency-levels.md).
* De Azure Cosmos-emulator biedt geen [replicatie voor meerdere regio's](distribute-data-globally.md).
* Als uw kopie van de Azure Cosmos-emulator mogelijk niet altijd up-to-date is met de meest recente wijzigingen in de Azure Cosmos DB-Service, raadpleegt u de [Azure Cosmos DB capacity planner](https://www.documentdb.com/capacityplanner) om nauw keurige schatting te maken van de productie doorvoer (RUs)-behoeften van uw modules.
* Wanneer u de Azure Cosmos-emulator gebruikt, kunt u standaard Maxi maal 25 containers met een vaste grootte maken (alleen ondersteund met behulp van Azure Cosmos DB Sdk's) of vijf onbeperkte containers met behulp van de Azure Cosmos-emulator. Zie [De PartitionCount-waarde instellen](#set-partitioncount) voor meer informatie over het wijzigen van deze waarde.

## <a name="system-requirements"></a>Systeemvereisten

De Azure Cosmos-emulator heeft de volgende hardware-en software vereisten:

* Softwarevereisten
  * Windows Server 2012 R2, Windows Server 2016 of Windows 10
  * 64-bits besturings systeem
* Minimale hardwarevereisten
  * 2 GB RAM
  * 10 GB beschikbare schijfruimte

## <a name="installation"></a>Installatie

U kunt de Azure Cosmos-emulator downloaden en installeren vanuit het [micro soft Download centrum](https://aka.ms/cosmosdb-emulator) of u kunt de emulator uitvoeren op docker voor Windows. Zie [Uitvoeren op Docker](#running-on-docker) voor instructies over het gebruik van de emulator op Docker voor Windows.

> [!NOTE]
> Als u de Azure Cosmos-emulator wilt installeren, configureren en uitvoeren, moet u beheerders bevoegdheden op de computer hebben. De emulator maakt/voegt een certificaat toe en stelt ook de firewall regels in om de services uit te voeren. Daarom is het nodig dat de emulator dergelijke bewerkingen kan uitvoeren.

## <a name="running-on-windows"></a>Uitvoeren in Windows

Als u de Azure Cosmos-emulator wilt starten, selecteert u de knop Start of drukt u op de Windows-toets. Begin met het typen van de **Azure Cosmos-emulator**en selecteer de emulator in de lijst met toepassingen.

![Selecteer de knop Start of druk op de Windows-toets, begin met het typen van * * Azure Cosmos emulator * * en selecteer de emulator in de lijst met toepassingen](./media/local-emulator/database-local-emulator-start.png)

Wanneer de emulator wordt gestart, ziet u een pictogram in het systeemvak op de taakbalk van Windows. ![Melding van de taak balk op de lokale emulator Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

De Azure Cosmos-emulator wordt standaard op de lokale computer (localhost) uitgevoerd op poort 8081.

De Azure Cosmos-emulator is standaard `C:\Program Files\Azure Cosmos DB Emulator` geïnstalleerd. U kunt de emulator ook starten en stoppen vanaf de opdrachtregel. Zie de [informatie over het opdrachtregelprogramma](#command-line) voor meer informatie.

## <a name="start-data-explorer"></a>Data Explorer starten

Wanneer de Azure Cosmos-emulator wordt gestart, wordt automatisch de Azure Cosmos-Data Explorer in uw browser geopend. Het adres wordt weer `https://localhost:8081/_explorer/index.html`gegeven als. Als u de Verkenner sluit en deze later opnieuw wilt openen, kunt u de URL openen in uw browser of starten vanuit de Azure Cosmos-emulator in het pictogram van de Windows-systeemvak, zoals hieronder wordt weer gegeven.

![Azure Cosmos Local emulator Data Explorer-start programma](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Controleren op updates

Data Explorer geeft aan of er een nieuwe update beschikbaar is om te downloaden.

> [!NOTE]
> Gegevens die zijn gemaakt in één versie van de Azure Cosmos-emulator (Zie Optionele instellingen voor%LOCALAPPDATA%\CosmosDBEmulator of gegevenspad), zijn niet gegarandeerd toegankelijk als u een andere versie gebruikt. Als u uw gegevens voor de lange termijn wilt behouden, is het raadzaam dat u die gegevens opslaat in een Azure Cosmos-account in plaats van in de Azure Cosmos-emulator.

## <a name="authenticating-requests"></a>Aanvragen verifiëren

Net als bij Azure Cosmos DB in de Cloud, moet elke aanvraag die u maakt op basis van de Azure Cosmos-emulator, worden geverifieerd. De Azure Cosmos-emulator ondersteunt één vast account en een bekende verificatie sleutel voor hoofd sleutel verificatie. Dit account en deze sleutel zijn de enige referenties die zijn toegestaan voor gebruik met de Azure Cosmos-emulator. Dit zijn:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> De hoofd sleutel die wordt ondersteund door de Azure Cosmos-emulator is uitsluitend bedoeld voor gebruik met de emulator. U kunt uw productie Azure Cosmos DB-account en-sleutel niet gebruiken met de Azure Cosmos-emulator.

> [!NOTE]
> Als u de emulator hebt gestart met de optie/Key, gebruikt u vervolgens de gegenereerde sleutel `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`in plaats van. Zie voor meer informatie over de optie/Key de [Naslag Gids opdracht regel Programma's.](#command-line)

Net als bij de Azure Cosmos DB ondersteunt de Azure Cosmos-emulator alleen beveiligde communicatie via SSL.

## <a name="running-on-a-local-network"></a>Uitvoeren op een lokaal netwerk

U kunt de emulator uitvoeren op een lokaal netwerk. Als u netwerk toegang wilt inschakelen, `/AllowNetworkAccess` geeft u de optie op op de [opdracht regel](#command-line-syntax), die ook vereist `/Key=key_string` dat `/KeyFile=file_name`u of opgeeft. U kunt gebruiken `/GenKeyFile=file_name` om een bestand met een wille keurige sleutel vooraf te genereren. Vervolgens kunt u deze door geven `/KeyFile=file_name` aan `/Key=contents_of_file`of.

Om netwerk toegang in te scha kelen voor de eerste keer dat de gebruiker de emulator afsluit en de gegevensdirectory van de emulator (%LOCALAPPDATA%\CosmosDBEmulator) verwijdert.

## <a name="developing-with-the-emulator"></a>Ontwikkelen met de emulator

### <a name="sql-api"></a>SQL-API

Zodra u de Azure Cosmos-emulator hebt die op uw bureau blad wordt uitgevoerd, kunt u alle ondersteunde [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) of de [Azure Cosmos DB rest API](/rest/api/cosmos-db/) gebruiken om met de emulator te communiceren. De Azure Cosmos-emulator bevat ook een ingebouwde Data Explorer waarmee u containers kunt maken voor de SQL-API of de Cosmos DB voor Mongo DB-API, en hoe u items weergeeft en bewerkt zonder dat u code hoeft te schrijven.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

Zodra u de Azure Cosmos-emulator hebt die op uw bureau blad wordt uitgevoerd, kunt u de [API van de Azure Cosmos DB voor MongoDb](mongodb-introduction.md) gebruiken om met de emulator te communiceren. Start Emulator vanaf de opdracht prompt als beheerder met '/EnableMongoDbEndpoint '. Gebruik vervolgens de volgende connection string om verbinding te maken met het MongoDB-API-account:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabel-API

Zodra de Azure Cosmos-emulator op uw bureau blad wordt uitgevoerd, kunt u de [Azure Cosmos DB Table-API SDK](table-storage-how-to-use-dotnet.md) gebruiken om met de emulator te communiceren. Start Emulator vanaf de opdracht prompt als beheerder met '/EnableTableEndpoint '. Voer vervolgens de volgende code uit om verbinding te maken met het Table-API-account:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra-API

Start Emulator vanaf een beheerders opdracht prompt met '/EnableCassandraEndpoint '. U kunt ook de omgevings variabele `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`instellen.

* [Python 2,7 installeren](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLI/CQLSH installeren](https://cassandra.apache.org/download/)

* Voer de volgende opdrachten uit in een normaal opdracht prompt venster:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Voer in de CQLSH-shell de volgende opdrachten uit om verbinding te maken met het Cassandra-eind punt:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin-API

Start Emulator vanaf een beheerders opdracht prompt met '/EnableGremlinEndpoint '. U kunt ook de omgevings variabele instellen`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installeer Apache-tinkerpop-Gremlin-console-3.3.4](https://tinkerpop.apache.org/downloads.html)

* Maak in de emulator Data Explorer een data base ' db1 ' en een verzameling ' coll1 '. Kies voor de partitie sleutel "/name"

* Voer de volgende opdrachten uit in een normaal opdracht prompt venster:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Voer in de Gremlin-shell de volgende opdrachten uit om verbinding te maken met het Gremlin-eind punt:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Het SSL-certificaat exporteren

.NET-talen en -runtime maken gebruik van het Windows-certificaatarchief om veilig verbinding te maken met de lokale Azure DB Cosmos Emulator. Andere talen hebben hun eigen methode voor het beheren en gebruiken van certificaten. Java maakt gebruik van een eigen [certificaatarchief](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), terwijl Python gebruikmaakt van [socketwrappers](https://docs.python.org/2/library/ssl.html).

Om een certificaat te verkrijgen voor gebruik bij talen en runtimes die niet worden geïntegreerd in het certificaatarchief van Windows, moet u het exporteren met behulp van de Windows Certificate Manager. U kunt dit starten door certlm. msc uit te voeren of door de stapsgewijze instructies te volgen in [de Azure Cosmos-emulator certificaten exporteren](./local-emulator-export-ssl-certificates.md). Zodra de certificaatmanager wordt uitgevoerd, opent u de persoonlijke certificaten zoals hieronder weergegeven en exporteert u het certificaat met de beschrijvende naam 'DocumentDBEmulatorCertificate' als een BASE 64-gecodeerd X.509-bestand (.cer).

![SSL-certificaat voor een lokale Azure Cosmos DB Emulator](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Volg de instructies in [Een certificaat toevoegen aan het Java CA-certificaatarchief](https://docs.microsoft.com/azure/java-add-certificate-ca-store) om het X.509-certificaat te importeren in het Java-certificaatarchief. Zodra het certificaat in het certificaat archief is geïmporteerd, kunnen clients voor SQL-en Azure Cosmos DB-API voor MongoDB verbinding maken met de Azure Cosmos-emulator.

Bij het verbinden met de emulator van Python en Node.js SDK's, is SSL-verificatie uitgeschakeld.

## <a id="command-line"></a>Opdrachtregelprogramma
Vanaf de installatie locatie kunt u de opdracht regel gebruiken om de emulator te starten en te stoppen, opties te configureren en andere bewerkingen uit te voeren.

### <a name="command-line-syntax"></a>De syntaxis van opdrachtregel

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Typ `CosmosDB.Emulator.exe /?` bij de opdrachtprompt om een lijst met opties te zien.

|**Optie** | **Beschrijving** | **Opdracht**| **Argumenten**|
|---|---|---|---|
|[Geen argumenten] | Start de Azure Cosmos-emulator met de standaard instellingen. |CosmosDB.Emulator.exe| |
|[Help] |Toont de lijst met ondersteunde opdrachtregelargumenten.|CosmosDB.Emulator.exe /? | |
| GetStatus |Hiermee wordt de status van de Azure Cosmos-emulator opgehaald. De status wordt aangegeven door de afsluitcode: 1 = starten, 2 = wordt uitgevoerd, 3 = gestopt. Een negatieve afsluitcode geeft aan dat er een fout is opgetreden. Er wordt geen andere uitvoer geproduceerd. | CosmosDB.Emulator.exe /GetStatus| |
| Afsluiten| De Azure Cosmos-emulator wordt afgesloten.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Specificeert het pad waarin de gegevensbestanden worden opgeslagen. De standaard waarde is%LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<gegevenspad\> | \<gegevenspad\>: Een toegankelijk pad |
|Port | Specificeert het poortnummer dat moet worden gebruikt voor de emulator. De standaard waarde is 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<poort\>: Enkel poortnummer |
| ComputePort | Het poort nummer opgegeven dat moet worden gebruikt voor de compute Interop-Gateway Service. De poort voor de HTTP-eindpunt test van de gateway wordt berekend als ComputePort + 79. Daarom moeten ComputePort en ComputePort + 79 open en beschikbaar zijn. De standaard waarden zijn 8900, 8979. | CosmosDB. emulator. exe/ComputePort = \<ComputePort\> | \<computeport\>: Enkel poortnummer |
| EnableMongoDbEndpoint | Hiermee schakelt u de MongoDB-API in | CosmosDB.Emulator.exe /EnableMongoDbEndpoint | |
| MongoPort | Specificeert het poortnummer dat moet worden gebruikt MongoDB compatibiliteit-API. De standaard waarde is 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongopoort\>: Enkel poortnummer|
| EnableCassandraEndpoint | Hiermee schakelt u Cassandra-API | CosmosDB. emulator. exe/EnableCassandraEndpoint | |
| CassandraPort | Hiermee geeft u het poort nummer op dat moet worden gebruikt voor het Cassandra-eind punt. De standaard waarde is 10350. | CosmosDB. emulator. exe/CassandraPort = \<CassandraPort\> | \<cassandraport\>: Enkel poortnummer |
| EnableGremlinEndpoint | Hiermee schakelt u de Gremlin-API in | CosmosDB. emulator. exe/EnableGremlinEndpoint | |
| GremlinPort | Het poort nummer dat moet worden gebruikt voor het Gremlin-eind punt. De standaard waarde is 8901. | CosmosDB. emulator. exe/GremlinPort =\<poort\> | \<poort\>: Enkel poortnummer |
|EnableTableEndpoint | Hiermee schakelt u Azure Table-API | CosmosDB. emulator. exe/EnableTableEndpoint | |
|TablePort | Het poort nummer dat moet worden gebruikt voor het Azure-tabel eindpunt. De standaard waarde is 8902. | CosmosDB. emulator. exe/TablePort =\<poort\> | \<poort\>: Enkel poortnummer|
| KeyFile | Lees de autorisatie sleutel van het opgegeven bestand. De/GenKeyFile-optie gebruiken om een keyfile te genereren | CosmosDB. emulator. exe/keyfile =\<bestands naam\> | \<bestands\>naam: Pad naar het bestand |
| ResetDataPath | Verwijdert recursief alle bestanden in het opgegeven pad. Als u geen pad opgeeft, wordt standaard%LOCALAPPDATA%\CosmosDbEmulator | CosmosDB. emulator. exe/ResetDataPath =\<pad > | \<pad\>: Pad naar bestand  |
| StartTraces  |  Verzamelen van traceer logboeken voor fout opsporing starten. | CosmosDB. emulator. exe/StartTraces | |
| StopTraces     | Stoppen met het verzamelen van traceer logboeken voor fout opsporing. | CosmosDB. emulator. exe/StopTraces  | |
|FailOnSslCertificateNameMismatch | Standaard genereert de emulator het zelfondertekende SSL-certificaat opnieuw als het SAN van het certificaat niet de domein naam van de emulator host, het lokale IPv4-adres, ' localhost ' en ' 127.0.0.1 ' bevat. Als deze optie is ingeschakeld, mislukt de emulator bij het opstarten. Vervolgens moet u de/GenCert-optie gebruiken om een nieuw zelfondertekend SSL-certificaat te maken en te installeren. | CosmosDB. emulator. exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Genereer en installeer een nieuw zelfondertekend SSL-certificaat. u kunt eventueel een door komma's gescheiden lijst met extra DNS-namen gebruiken om toegang te krijgen tot de emulator via het netwerk. | CosmosDB. emulator. exe/GenCert =\<DNS-namen\> |\<DNS-namen\>: Optionele, door komma's gescheiden lijst met extra DNS-namen  |
| DirectPorts |Specificeert de poorten die worden gebruikt voor rechtstreekse connectiviteit. Standaardwaarden zijn 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: Door komma's gescheiden lijst met 4 poorten |
| Sleutel |De autorisatiesleutel voor de emulator. De sleutel moet de base 64-codering zijn van een 64-byte-vector. | CosmosDB.Emulator.exe /Key:\<sleutel\> | \<sleutel\>: De sleutel moet de base 64-codering zijn van een 64-byte-vector|
| EnableRateLimiting | Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is ingeschakeld. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is uitgeschakeld. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | De gebruikersinterface van de emulator niet weergeven. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Geen Data Explorer weergeven bij het opstarten. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Hiermee geeft u het maximum aantal gepartitioneerde containers op. Zie [het aantal containers wijzigen](#set-partitioncount) voor meer informatie. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<aantal partities\>: Maximum aantal toegestane containers voor een enkele partitie. De standaard waarde is 25. Maximaal toegestaan is 250.|
| DefaultPartitionCount| Hiermee geeft u het standaard aantal partities voor een gepartitioneerde container op. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<de\> standaard waarde voor defaultpartitioncount is 25.|
| AllowNetworkAccess | Geeft toegang tot de emulator via een netwerk. U moet ook /Key=\<sleutelreeks\> of /KeyFile=\<bestandsnaam\> doorgeven om netwerktoegang in te schakelen. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> of  CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Pas de firewall regels niet aan wanneer de optie/AllowNetworkAccess wordt gebruikt. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Een nieuwe autorisatiesleutel genereren en opslaan in het opgegeven bestand. De gegenereerde sleutel kan worden gebruikt met de opties/Key of/KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<pad naar sleutelbestand\> | |
| Consistentie | Het standaard consistentieniveau voor het account instellen. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistentie\>: De waarde moet een van de volgende [consistentieniveaus](consistency-levels.md) zijn: Sessie, Sterk, Mogelijk of Gebonden veroudering. De standaardwaarde is Sessie. |
| ? | Het helpbericht weergeven.| | |

## <a id="set-partitioncount"></a>Het aantal containers wijzigen

Standaard kunt u Maxi maal 25 containers met een vaste grootte maken (alleen ondersteund met behulp van Azure Cosmos DB Sdk's) of vijf onbeperkte containers met behulp van de Azure Cosmos-emulator. Door de waarde **PartitionCount** aan te passen, kunt u maxi maal 250 containers met een vaste grootte of 50 onbeperkte containers maken, of een combi natie van de twee die niet groter is dan 250 vaste grootte containers (waarbij één onbeperkte container = 5 containers met een vaste grootte). Het is echter niet raadzaam de emulator zo in te stellen dat deze wordt uitgevoerd met meer dan 200 containers met een vaste grootte. Vanwege de overhead die wordt toegevoegd aan de i/o-bewerkingen van de schijf, wat resulteert in onvoorspelbare time-outs bij gebruik van de Endpoint-Api's.

Als u probeert een container te maken nadat het huidige aantal partities is overschreden, genereert de emulator een ServiceUnavailable-uitzonde ring met het volgende bericht.

We ondervinden momenteel hoge vraag in deze regio en kunnen op dit moment niet aan uw aanvraag voldoen. We werken continu meer en meer capaciteit online en moedigen u aan om het opnieuw te proberen.
U kunt op elk gewenst moment of askcosmosdb@microsoft.com om een andere reden geen e-mail bericht maken.
ActivityId 12345678-1234-1234-1234-123456789abc"

Voer de volgende stappen uit om het aantal containers te wijzigen dat beschikbaar is in de Azure Cosmos-emulator:

1. Verwijder alle lokale Azure Cosmos-emulator gegevens door met de rechter muisknop op het pictogram van de **Azure Cosmos DB emulator** op het systeemvak te klikken en vervolgens op **gegevens opnieuw instellen te klikken...** .
2. Alle emulator-gegevens in deze map `%LOCALAPPDATA%\CosmosDBEmulator`verwijderen.
3. Sluit alle geopende exemplaren door met de rechtermuisknop te klikken op het pictogram van de **Azure Cosmos DB Emulator** in het systeemvak en vervolgens te klikken op **Afsluiten**. Het afsluiten van alle exemplaren kan een paar minuten duren.
4. Installeer de nieuwste versie van de [Azure Cosmos-emulator](https://aka.ms/cosmosdb-emulator).
5. Start de emulator met de vlag PartitionCount door een waarde < = 250 in te stellen. Bijvoorbeeld: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>De emulator beheren

De emulator wordt geleverd met een Power shell-module om de status van de service te starten, te stoppen, te verwijderen en op te halen. Voer de volgende cmdlet uit om de Power shell-module te gebruiken:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

of plaats de `PSModules` map op uw `PSModulesPath` en importeer deze zoals wordt weer gegeven in de volgende opdracht:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Hier volgt een samenvatting van de opdrachten voor het beheren van de emulator vanuit PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Syntaxis**

`Get-CosmosDbEmulatorStatus`

**Opmerkingen**

Retourneert een van deze ServiceControllerStatus-waarden: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running of ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntaxis**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Opmerkingen**

Start de emulator. Standaard wacht de opdracht totdat de emulator klaar is om aanvragen te accepteren. Gebruik de optie -NoWait als u wilt dat de cmdlet wordt geretourneerd zodra deze de emulator heeft gestart.

### `Stop-CosmosDbEmulator`

**Syntaxis**

 `Stop-CosmosDbEmulator [-NoWait]`

**Opmerkingen**

Stopt de emulator. Standaard wacht deze opdracht totdat de emulator volledig is afgesloten. Gebruik de optie -NoWait als u wilt dat de cmdlet wordt geretourneerd zodra het afsluiten van de emulator wordt gestart.

### `Uninstall-CosmosDbEmulator`

**Syntaxis**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Opmerkingen**

Verwijdert de emulator en optioneel ook de inhoud van $env:LOCALAPPDATA\CosmosDbEmulator.
De cmdlet zorgt ervoor dat de emulator is gestopt deze wordt verwijderd.

## <a name="running-on-docker"></a>Uitvoeren op Docker

De Azure Cosmos-emulator kan worden uitgevoerd op docker voor Windows. De emulator werkt niet op Docker voor Oracle Linux.

Nadat u [Docker voor Windows](https://www.docker.com/docker-windows) hebt geïnstalleerd, schakelt u over naar Windows-containers door met de rechtermuisknop te klikken op het Docker-pictogram op de werkbalk en **Overschakelen naar Windows-containers** te selecteren.

Haal vervolgens de installatiekopie van de emulator op van Docker Hub met de volgende opdracht in uw favoriete shell.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Voer de volgende opdrachten uit om de installatiekopie te starten.

Vanaf de opdrachtregel:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Als er een fout in de poort conflict wordt weer gegeven (de opgegeven poort is al in gebruik) wanneer u de opdracht docker run uitvoert, kunt u een aangepaste poort door geven door de poort nummers te wijzigen. U kunt bijvoorbeeld '-p 8081:8081 ' wijzigen in '-p 443:8081 '

Vanuit PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

De reactie ziet er ongeveer als volgt uit:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Gebruik nu het eindpunt en de hoofdsleutel uit de reactie in uw client en importeer het SSL-certificaat in de host. Doe het volgende vanaf een opdrachtprompt voor een beheerder om het SSL-certificaat te importeren:

Vanaf de opdrachtregel:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Vanuit PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Wanneer u de interactieve shell sluit nadat de emulator is gestart, wordt de container van de emulator afgesloten.

U opent de Data Explorer door naar de volgende URL in uw browser te gaan. Het eindpunt van de emulator wordt vermeld in het reactiebericht dat hierboven wordt getoond.

    https://<emulator endpoint provided in response>/_explorer/index.html

## Uitvoeren op Mac of Linux<a id="mac"></a>

De Cosmos-emulator kan momenteel alleen worden uitgevoerd in Windows. Gebruikers die Mac of Linux uitvoeren, kunnen de-emulator uitvoeren op een virtuele Windows-machine die een Hyper Visor zoals parallelle of VirtualBox Host. Hieronder vindt u de stappen om dit in te scha kelen.

Voer in de Windows-VM de onderstaande opdracht uit en noteer het IPv4-adres.

```cmd
ipconfig.exe
```

In uw toepassing moet u de URI voor het DocumentClient-object wijzigen om het IPv4-adres te gebruiken `ipconfig.exe`dat wordt geretourneerd door. De volgende stap is het omzeilen van de CA-validatie bij het maken van het DocumentClient-object. Hiervoor moet u een HttpClientHandler opgeven voor de DocumentClient-constructor, die een eigen implementatie voor ServerCertificateCustomValidationCallback heeft.

Hieronder ziet u een voor beeld van hoe de code eruit moet zien.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Start ten slotte vanaf de Windows-VM de Cosmos-emulator vanaf de opdracht regel met behulp van de volgende opties.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik de volgende tips om problemen op te lossen die u tegen komt met de Azure Cosmos-emulator:

- Als u een nieuwe versie van de emulator hebt geïnstalleerd en er fouten optreden, zorg dan dat u uw gegevens opnieuw instelt. U kunt uw gegevens opnieuw instellen door met de rechter muisknop op het pictogram van de Azure Cosmos-emulator in het systeemvak te klikken en vervolgens op gegevens opnieuw instellen te klikken.... Als de fouten niet worden opgelost, kunt u de emulator en eventuele oudere versies van de emulator verwijderen, indien gevonden, de map C:\Program files\Azure Cosmos DB emulator verwijderen en de emulator opnieuw installeren. Zie [De lokale emulator verwijderen](#uninstall) voor instructies.

- Als de Azure Cosmos-emulator vastloopt, verzamelt u dump bestanden uit de map%LOCALAPPDATA%\CrashDumps, comprimeert u deze en koppelt u [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)deze aan een e-mail bericht aan.

- Als u vastloopt `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, is dit mogelijk een symptoom waarbij de status van de prestatie meter items beschadigd is. Als u de volgende opdracht uitvoert vanaf een opdracht prompt met beheerders rechten, wordt het probleem doorgaans opgelost:

  ```cmd
  lodctr /R
   ```

- Als er sprake is van een verbindingsprobleem, [verzamelt u de traceringsbestanden](#trace-files), comprimeert u de bestanden en verstuurt u het gecomprimeerde bestand als bijlage bij een e-mail naar [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Als u het bericht **Service niet beschikbaar** krijgt, is het mogelijk dat de emulator de netwerkstack niet kan initialiseren. Controleer of de veilige Pulse-client of Juniper-netwerkclient is geïnstalleerd. De netwerkfilterstuurprogramma's van deze clients kunnen mogelijk de oorzaak zijn van het probleem. Doorgaans kan het probleem worden opgelost door stuurprogramma's voor netwerkfilters van derden te verwijderen. U kunt ook de emulator starten met/DisableRIO, waarmee de emulator netwerk communicatie wordt overgeschakeld naar een regel matige Winsock. 

- Als de emulator wordt uitgevoerd en uw computer naar de slaapstand gaat of er besturingssysteemupdates worden uitgevoerd, wordt mogelijk het bericht **Service is momenteel niet beschikbaar** weergegeven. Stel de gegevens van de emulator opnieuw in door met de rechter muisknop te klikken op het pictogram dat wordt weer gegeven op de Windows-systeemvak en selecteer **gegevens opnieuw instellen**.

### <a id="trace-files"></a>Traceringsbestanden verzamelen

Voor het verzamelen van foutopsporingsgegevens, voert u de volgende opdrachten uit vanaf een opdrachtprompt voor een beheerder:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Kijk in het systeemvak of het programma is afgesloten. Dit kan een minuut duren. U kunt ook gewoon op **Afsluiten** klikken in de gebruikers interface van de Azure Cosmos-emulator.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduceer het probleem. Als Data Explorer niet werkt, hoeft u alleen te wachten tot de browser een paar seconden wordt geopend om de fout te achterhalen.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Ga naar `%ProgramFiles%\Azure Cosmos DB Emulator` en zoek het bestand docdbemulator_000001.etl op.
7. Stuur het .etl-bestand samen met de reproductiestappen naar [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) om de fout op te sporen.

### <a id="uninstall"></a>De lokale emulator verwijderen

1. Sluit alle geopende exemplaren van de lokale emulator af door met de rechter muisknop op het pictogram van de Azure Cosmos-emulator op het systeemvak te klikken en vervolgens op Afsluiten te klikken. Het afsluiten van alle exemplaren kan een paar minuten duren.
2. Typ in het zoekvak Windows **Apps en onderdelen** en klik op **Apps en onderdelen (systeeminstellingen)** .
3. Ga in de lijst met apps naar **Azure Cosmos DB Emulator**, selecteer de app, klik op **Verwijderen**, bevestig dit en klik nogmaals op **Verwijderen**.
4. Wanneer de app is verwijderd, gaat u naar `%LOCALAPPDATA%\CosmosDBEmulator` en verwijdert u de map.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de lokale emulator kunt gebruiken voor gratis lokale ontwikkelingsdoeleinden. U kunt nu verder gaan met de volgende zelfstudie om te leren hoe u SSL-certificaten voor de emulator exporteert.

> [!div class="nextstepaction"]
> [De Azure Cosmos-emulator certificaten exporteren](local-emulator-export-ssl-certificates.md)
