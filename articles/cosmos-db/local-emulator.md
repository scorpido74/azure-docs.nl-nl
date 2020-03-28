---
title: Lokaal ontwikkelen met de Azure Cosmos Emulator
description: Met de Azure Cosmos Emulator u uw toepassing lokaal gratis ontwikkelen en testen, zonder een Azure-abonnement te maken.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 01/31/2020
ms.openlocfilehash: 287933de6403d680c5aa5b6c78df49abe5f2ac56
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238508"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>De Azure Cosmos Emulator gebruiken voor lokale ontwikkeling en testen

De Azure Cosmos Emulator biedt een lokale omgeving die de Azure Cosmos DB-service emuleert voor ontwikkelingsdoeleinden. Met de Azure Cosmos Emulator u uw toepassing lokaal ontwikkelen en testen, zonder een Azure-abonnement te maken of kosten te maken. Wanneer u tevreden bent over de werking van uw toepassing in de Azure Cosmos Emulator, u overschakelen naar het gebruik van een Azure Cosmos-account in de cloud.

U ontwikkelen met Azure Cosmos Emulator met [SQL,](local-emulator.md#sql-api) [Cassandra](local-emulator.md#cassandra-api), [MongoDB,](local-emulator.md#azure-cosmos-dbs-api-for-mongodb) [Gremlin](local-emulator.md#gremlin-api)en [Table](local-emulator.md#table-api) API-accounts. Op dit moment ondersteunt de Weergave Data Explorer in de emulator echter alleen clients voor SQL API volledig. 

## <a name="how-the-emulator-works"></a>Hoe de emulator werkt

De Azure Cosmos Emulator biedt een high-fidelity emulatie van de Azure Cosmos DB-service. Het ondersteunt identieke functionaliteit als Azure Cosmos DB, inclusief ondersteuning voor het maken en opvragen van gegevens, het inrichten en schalen van containers en het uitvoeren van opgeslagen procedures en triggers. U toepassingen ontwikkelen en testen met behulp van de Azure Cosmos Emulator en deze op wereldwijde schaal implementeren in Azure door slechts één configuratiewijziging aan te brengen in het verbindingseindpunt voor Azure Cosmos DB.

Emulatie van de Azure Cosmos DB-service is zeer betrouwbaar, maar de implementatie van de emulator is anders dan die van de service. De emulator gebruikt bijvoorbeeld standaardbesturingssysteemonderdelen, zoals het lokale bestandssysteem voor persistentie en de HTTPS-protocolstack voor connectiviteit. Functionaliteit die afhankelijk is van Azure-infrastructuur, zoals globale replicatie, enkele-cijferige millisecondelatentie voor lees-/schrijfbewerkingen en tunable consistentieniveaus zijn niet van toepassing.

U gegevens migreren tussen de Azure Cosmos Emulator en de Azure Cosmos DB-service met behulp van het [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool).

U Azure Cosmos Emulator uitvoeren op de Windows Docker-container, zie de [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) voor de opdracht dockerpull en [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) voor de `Dockerfile` en meer informatie.

## <a name="differences-between-the-emulator-and-the-service"></a>Verschillen tussen de emulator en de service

Omdat de Azure Cosmos Emulator een geëmuleerde omgeving biedt die wordt uitgevoerd op het werkstation voor lokale ontwikkelaars, zijn er enkele verschillen in functionaliteit tussen de emulator en een Azure Cosmos-account in de cloud:

* Momenteel ondersteunt Data Explorer in de emulator clients voor SQL API. De gegevensverkennersweergave en -bewerkingen voor Azure Cosmos DB-API's zoals MongoDB, Tabel, Grafiek en Cassandra API's worden niet volledig ondersteund.
* De Azure Cosmos Emulator ondersteunt slechts één vast account en een bekende hoofdsleutel. Sleutelregeneratie is niet mogelijk in de Azure Cosmos Emulator, maar de standaardsleutel kan worden gewijzigd met behulp van de opdrachtregeloptie.
* De Azure Cosmos Emulator is geen schaalbare service en ondersteunt geen groot aantal containers.
* De Azure Cosmos Emulator biedt geen verschillende [Azure Cosmos DB-consistentieniveaus.](consistency-levels.md)
* De Azure Cosmos Emulator biedt geen [replicatie met meerdere regio's.](distribute-data-globally.md)
* Aangezien uw exemplaar van de Azure Cosmos Emulator mogelijk niet altijd up-to-date is met de meest recente wijzigingen in de Azure Cosmos DB-service, moet u verwijzen naar de [Azure Cosmos DB-capaciteitsplanner](https://www.documentdb.com/capacityplanner) om nauwkeurig de productiedoorvoerbehoeften van uw toepassing te schatten.
* Wanneer u de Azure Cosmos Emulator gebruikt, u standaard maximaal 25 containers met een vaste grootte maken (alleen ondersteund met Azure Cosmos DB SDKs) of 5 onbeperkte containers met behulp van de Azure Cosmos Emulator. Zie [De PartitionCount-waarde instellen](#set-partitioncount) voor meer informatie over het wijzigen van deze waarde.

## <a name="system-requirements"></a>Systeemvereisten

De Azure Cosmos Emulator heeft de volgende hardware- en softwarevereisten:

* Softwarevereisten
  * Windows Server 2012 R2, Windows Server 2016 of Windows 10
  * 64-bits besturingssysteem
* Minimale hardwarevereisten
  * 2 GB RAM
  * 10 GB beschikbare schijfruimte

## <a name="installation"></a>Installeren

U de Azure Cosmos Emulator downloaden en installeren vanuit het [Microsoft Download Center](https://aka.ms/cosmosdb-emulator) of u de emulator uitvoeren op Docker voor Windows. Zie [Uitvoeren op Docker](#running-on-docker) voor instructies over het gebruik van de emulator op Docker voor Windows.

> [!NOTE]
> Als u de Azure Cosmos Emulator wilt installeren, configureren en uitvoeren, moet u beheerdersbevoegdheden op de computer hebben. De emulator maakt/voegt een certificaat toe en stelt ook de firewallregels in om zijn services uit te voeren; daarom is het noodzakelijk dat de emulator dergelijke bewerkingen kan uitvoeren.

## <a name="running-on-windows"></a>Uitvoeren in Windows

Als u de Azure Cosmos Emulator wilt starten, selecteert u de knop Start of drukt u op de Windows-toets. Begin met het typen van **Azure Cosmos Emulator**en selecteer de emulator in de lijst met toepassingen.

![Selecteer de knop Start of druk op de Windows-toets, begin met typen **Azure Cosmos Emulator*** en selecteer de emulator in de lijst met toepassingen](./media/local-emulator/database-local-emulator-start.png)

Wanneer de emulator wordt gestart, ziet u een pictogram in het systeemvak op de taakbalk van Windows. ![Melding van de lokale emulator-taakbalk van Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

De Azure Cosmos Emulator wordt standaard uitgevoerd op de lokale machine ("localhost") die luistert op poort 8081.

De Azure Cosmos Emulator `C:\Program Files\Azure Cosmos DB Emulator` is standaard geïnstalleerd. U kunt de emulator ook starten en stoppen vanaf de opdrachtregel. Zie de [informatie over het opdrachtregelprogramma](#command-line) voor meer informatie.

## <a name="start-data-explorer"></a>Data Explorer starten

Wanneer de Azure Cosmos Emulator wordt gestart, wordt de Azure Cosmos Data Explorer automatisch geopend in uw browser. Het adres wordt weergegeven als `https://localhost:8081/_explorer/index.html`. Als u de verkenner sluit en deze later opnieuw wilt openen, u de URL in uw browser openen of deze starten vanuit de Azure Cosmos Emulator in het Windows-ladepictogram zoals hieronder weergegeven.

![Azure Cosmos lokale emulator data explorer launcher](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Controleren op updates

Data Explorer geeft aan of er een nieuwe update beschikbaar is om te downloaden.

> [!NOTE]
> Gegevens die zijn gemaakt in één versie van de Azure Cosmos Emulator (zie %LOCALAPPDATA%\CosmosDBEmulator of optionele instellingen voor gegevenspad) zijn niet gegarandeerd toegankelijk wanneer u een andere versie gebruikt. Als u uw gegevens voor de lange termijn moet blijven gebruiken, wordt aanbevolen deze gegevens op te slaan in een Azure Cosmos-account, in plaats van in de Azure Cosmos-emulator.

## <a name="authenticating-requests"></a>Aanvragen verifiëren

Net als bij Azure Cosmos DB in de cloud moet elk verzoek dat u doet tegen de Azure Cosmos Emulator worden geverifieerd. De Azure Cosmos Emulator ondersteunt één vast account en een bekende verificatiesleutel voor hoofdsleutelverificatie. Dit account en deze sleutel zijn de enige referenties die zijn toegestaan voor gebruik met de Azure Cosmos Emulator. Dit zijn:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> De hoofdsleutel die wordt ondersteund door de Azure Cosmos Emulator is alleen bedoeld voor gebruik met de emulator. U uw Azure Cosmos DB-account en sleutel van de productie niet gebruiken met de Azure Cosmos Emulator.

> [!NOTE]
> Als u de emulator met de optie /Key hebt `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`gestart, gebruikt u de gegenereerde sleutel in plaats van . Zie Naslaginformatie over opdrachtregelgereedschap voor meer informatie over /Key.For more information about /Key option, see [Command-line tool reference.](#command-line)

Net als bij de Azure Cosmos DB ondersteunt de Azure Cosmos Emulator alleen beveiligde communicatie via SSL.

## <a name="running-on-a-local-network"></a>Uitvoeren op een lokaal netwerk

U kunt de emulator uitvoeren op een lokaal netwerk. Als u netwerktoegang `/AllowNetworkAccess` wilt inschakelen, geeft u de optie `/Key=key_string` op `/KeyFile=file_name`de [opdrachtregel](#command-line-syntax)op, waarvoor u ook moet opgeven of . U kunt `/GenKeyFile=file_name` gebruiken om een bestand te genereren met een willekeurige sleutel vooraf. Dan kun je `/KeyFile=file_name` dat `/Key=contents_of_file`doorgeven aan of.

Om netwerktoegang voor de eerste keer in te schakelen, moet de gebruiker de emulator afsluiten en de gegevensmap van de emulator verwijderen (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Ontwikkelen met de emulator

### <a name="sql-api"></a>SQL-API

Zodra u de Azure Cosmos Emulator op uw bureaublad hebt uitgevoerd, u alle ondersteunde [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) of de Azure Cosmos DB REST [API](/rest/api/cosmos-db/) gebruiken om met de emulator te communiceren. De Azure Cosmos Emulator bevat ook een ingebouwde Data Explorer waarmee u containers maken voor SQL API of Cosmos DB voor Mongo DB API, en items bekijken en bewerken zonder code te schrijven.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

Zodra u de Azure Cosmos Emulator op uw bureaublad hebt uitgevoerd, u de [API van Azure Cosmos DB voor MongoDB](mongodb-introduction.md) gebruiken om met de emulator te communiceren. Start emulator vanaf opdrachtprompt als beheerder met "/EnableMongoDbEndpoint". Gebruik vervolgens de volgende verbindingstekenreeks om verbinding te maken met het MongoDB API-account:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabel-API

Zodra u de Azure Cosmos Emulator op uw bureaublad hebt uitgevoerd, u de [Azure Cosmos DB Table API SDK](table-storage-how-to-use-dotnet.md) gebruiken om te communiceren met de emulator. Start emulator vanaf opdrachtprompt als beheerder met "/EnableTableEndpoint". Voer vervolgens de volgende code uit om verbinding te maken met het api-account van de tabel:

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

Start emulator vanaf een opdrachtprompt voor beheerders met "/EnableCassandraEndpoint". U ook de omgevingsvariabele `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`instellen.

* [Python 2.7 installeren](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLI/CQLSH installeren](https://cassandra.apache.org/download/)

* Voer de volgende opdrachten uit in een normaal opdrachtpromptvenster:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* Voer in de CQLSH-shell de volgende opdrachten uit om verbinding te maken met het Cassandra-eindpunt:

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

Start emulator van een administrator opdracht prompt met "/EnableGremlinEndpoint". U ook de omgevingsvariabele instellen`AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Installeer apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* Maak in de Data Explorer van de emulator een database "db1" en een verzameling "coll1"; kies voor de partitiesleutel "/naam"

* Voer de volgende opdrachten uit in een normaal opdrachtpromptvenster:

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

* Voer in de schil Gremlin de volgende opdrachten uit om verbinding te maken met het Gremlin-eindpunt:

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

Om een certificaat te verkrijgen voor gebruik bij talen en runtimes die niet worden geïntegreerd in het certificaatarchief van Windows, moet u het exporteren met behulp van de Windows Certificate Manager. U het starten door certlm.msc uit te voeren of de stapsgewijze instructies te volgen in [De Azure Cosmos Emulator-certificaten exporteren.](./local-emulator-export-ssl-certificates.md) Zodra de certificaatmanager wordt uitgevoerd, opent u de persoonlijke certificaten zoals hieronder weergegeven en exporteert u het certificaat met de beschrijvende naam 'DocumentDBEmulatorCertificate' als een BASE 64-gecodeerd X.509-bestand (.cer).

![SSL-certificaat voor een lokale Azure Cosmos DB Emulator](./media/local-emulator/database-local-emulator-ssl_certificate.png)

Volg de instructies in [Een certificaat toevoegen aan het Java CA-certificaatarchief](https://docs.microsoft.com/azure/java-add-certificate-ca-store) om het X.509-certificaat te importeren in het Java-certificaatarchief. Zodra het certificaat is geïmporteerd in het certificaatarchief, kunnen clients voor SQL en Azure Cosmos DB's API voor MongoDB verbinding maken met de Azure Cosmos Emulator.

Bij het verbinden met de emulator van Python en Node.js SDK's, is SSL-verificatie uitgeschakeld.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Opdrachtregelprogramma
Vanaf de installatielocatie u de opdrachtregel gebruiken om de emulator te starten en te stoppen, opties te configureren en andere bewerkingen uit te voeren.

### <a name="command-line-syntax"></a>De syntaxis van opdrachtregel

    Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Typ `Microsoft.Azure.Cosmos.Emulator.exe /?` bij de opdrachtprompt om een lijst met opties te zien.

|**Optie** | **Beschrijving** | **Opdracht**| **Argumenten**|
|---|---|---|---|
|[Geen argumenten] | Hiermee start u de Azure Cosmos Emulator op met standaardinstellingen. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Toont de lijst met ondersteunde opdrachtregelargumenten.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Krijgt de status van de Azure Cosmos Emulator. De status wordt aangegeven door de afsluitcode: 1 = starten, 2 = wordt uitgevoerd, 3 = gestopt. Een negatieve afsluitcode geeft aan dat er een fout is opgetreden. Er wordt geen andere uitvoer geproduceerd. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Afsluiten| Sluit de Azure Cosmos Emulator af.| Microsoft.Azure.Cosmos.Emulator.exe /Afsluiten | |
|DataPath | Specificeert het pad waarin de gegevensbestanden worden opgeslagen. Standaardwaarde is %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<gegevenspad\>: een toegankelijk pad |
|Poort | Specificeert het poortnummer dat moet worden gebruikt voor de emulator. Standaardwaarde is 8081. |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<poort\> | \<poort\>: enkel poortnummer |
| ComputePort (ComputePort) | Heb het poortnummer opgegeven dat moet worden gebruikt voor de Compute Interop Gateway-service. De HTTP-eindpuntssondepoort van de gateway wordt berekend als ComputePort + 79. Daarom moeten ComputePort en ComputePort + 79 open en beschikbaar zijn. De standaardwaarde is 8900. | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>: Single port nummer |
| EnableMongoDbEndpoint=3.2 | MongoDB API 3.2 inschakelen | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | MongoDB API 3.6 inschakelen | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Specificeert het poortnummer dat moet worden gebruikt MongoDB compatibiliteit-API. De standaardwaarde is 10255. |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongopo0rt\>: enkel poortnummer|
| CassandraEndpoint inschakelen | Maakt Cassandra API mogelijk | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort CassandraPort | Hiermee geeft u het poortnummer op dat moet worden gebruikt voor het Cassandra-eindpunt. De standaardwaarde is 10350. | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>: Enkel poortnummer |
| GremlinEndpoint inschakelen | Gremlin API inschakelt | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort GremlinPort | Poortnummer dat u moet gebruiken voor het Gremlin-eindpunt. Standaardwaarde is 8901. | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<poort\> | \<poort\>: enkel poortnummer |
|Inschakelbaar eindpunt inschakelen | Azure Table API in- | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TabelPort | Poortnummer dat u moet gebruiken voor het Azure Table Endpoint. Standaardwaarde is 8902. | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<poort\> | \<poort\>: enkel poortnummer|
| Keyfile | Lees de autorisatiesleutel uit het opgegeven bestand. De optie /GenKeyFile gebruiken om een keyfile te genereren | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Pad naar het bestand |
| ResetDataPath | Recursief verwijdert alle bestanden in het opgegeven pad. Als u geen pad opgeeft, wordt het standaard weergegeven in %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<pad> | \<pad\>: Bestandspad  |
| StartTraces  |  Begin met het verzamelen van foutopsporingstraceringslogboeken met LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Stop met het verzamelen van foutopsporingslogboeken met LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Begin met het verzamelen van foutopsporingstraceringslogboeken met het windows performance-opnamegereedschap. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Stop met het verzamelen van foutopsporingslogboeken met het windows performance-opnamegereedschap. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailonsslCertificateNameMismatch FailonsslCertificateNameMismatch FailonsslCertificateNameMismatch Failon | Standaard regenereert de Emulator zijn zelfondertekende SSL-certificaat, als het SAN van het certificaat niet de domeinnaam van de Emulator-host, het lokale IPv4-adres, 'localhost' en '127.0.0.1' bevat. Met deze optie zal de emulator mislukken bij het opstarten in plaats daarvan. U moet dan de optie /GenCert gebruiken om een nieuw zelfondertekend SSL-certificaat te maken en te installeren. | Microsoft.azure.cosmos.emulator.exe /FailonsslCertificateNameMismatch  | |
| GenCert GenCert | Een nieuw zelfondertekend SSL-certificaat genereren en installeren. eventueel inclusief een door komma's gescheiden lijst met extra DNS-namen voor toegang tot de Emulator via het netwerk. | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-namen\> |\<dns-namen\>: Optionele door komma's gescheiden lijst met extra dns-namen  |
| DirectPorts |Specificeert de poorten die worden gebruikt voor rechtstreekse connectiviteit. Standaardwaarden zijn 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: door komma's gescheiden lijst met 4 poorten |
| Sleutel |De autorisatiesleutel voor de emulator. De sleutel moet de base 64-codering zijn van een 64-byte-vector. | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<sleutel\> | \<sleutel\>: sleutel moet de base 64-codering zijn van een 64-byte-vector|
| EnableRateLimiting | Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is ingeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is uitgeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | De gebruikersinterface van de emulator niet weergeven. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Geen Data Explorer weergeven bij het opstarten. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Hiermee geeft u het maximumaantal partitiecontainers op. Zie [Het aantal containers wijzigen](#set-partitioncount) voor meer informatie. | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitietelling\>: Maximaal aantal toegestane enkele partitiecontainers. De standaardwaarde is 25. Maximaal toegestaan is 250.|
| DefaultPartitionCount| Hiermee geeft u het standaardaantal partities voor een partitiecontainer op. | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<standaardpartitieaantal\> Standaardwaarde is 25.|
| AllowNetworkAccess | Geeft toegang tot de emulator via een netwerk. U moet ook /Key=\<sleutelreeks\> of /KeyFile=\<bestandsnaam\> doorgeven om netwerktoegang in te schakelen. | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> of Microsoft.Azure.cosmos.emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | Pas firewallregels niet aan wanneer de optie /AllowNetworkAccess wordt gebruikt. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Een nieuwe autorisatiesleutel genereren en opslaan in het opgegeven bestand. De gegenereerde sleutel kan worden gebruikt met de opties/Key of/KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<pad naar sleutelbestand\> | |
| Consistentie | Het standaard consistentieniveau voor het account instellen. | Consistentie van Microsoft.Azure.Cosmos.Emulator.exe\</Consistency=\> | \<consistentie\>: waarde moet een van de volgende [consistentieniveaus](consistency-levels.md) zijn: sessie, sterk, mogelijk of gebonden veroudering. De standaardwaarde is Sessie. |
| ? | Het helpbericht weergeven.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Het aantal containers wijzigen

Standaard u maximaal 25 containers met een vaste grootte maken (alleen ondersteund met Azure Cosmos DB SDKs) of 5 onbeperkte containers met behulp van de Azure Cosmos Emulator. Door de **partitioncount-waarde** te wijzigen, u maximaal 250 containers met een vaste grootte of 50 onbeperkte containers maken, of een combinatie van de twee die niet meer dan 250 containers met een vaste grootte (waarbij één onbeperkte container = 5 containers met een vaste grootte) worden bereikt. Het is echter niet aan te raden om de emulator in te stellen om te draaien met meer dan 200 containers met een vaste grootte. Vanwege de overhead die het toevoegt aan de schijf IO-bewerkingen, die resulteren in onvoorspelbare time-outs bij het gebruik van de endpoint API's.

Als u probeert een container te maken nadat het huidige aantal partities is overschreden, gooit de emulator een uitzondering ServiceUnavailable met het volgende bericht.

"Helaas, we ervaren momenteel een grote vraag in deze regio, en kan niet voldoen aan uw verzoek op dit moment. We werken continu om meer en meer capaciteit online te brengen en moedigen u aan om het opnieuw te proberen.
ActivityId: 12345678-1234-1234-1234-123456789abc"

Voer de volgende stappen uit om het aantal containers te wijzigen dat beschikbaar is in de Azure Cosmos Emulator:

1. Verwijder alle lokale Azure Cosmos Emulator-gegevens door met de rechtermuisknop op het Azure **Cosmos DB Emulator-pictogram** op de systeemlade te klikken en vervolgens op **Gegevens opnieuw instellen te klikken...**.
2. Verwijder alle emulatorgegevens `%LOCALAPPDATA%\CosmosDBEmulator`in deze map.
3. Sluit alle geopende exemplaren door met de rechtermuisknop te klikken op het pictogram van de **Azure Cosmos DB Emulator** in het systeemvak en vervolgens te klikken op **Afsluiten**. Het afsluiten van alle exemplaren kan een paar minuten duren.
4. Installeer de nieuwste versie van de [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator).
5. Start de emulator met de vlag PartitionCount door een waarde < = 250 in te stellen. Bijvoorbeeld: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>De emulator beheren

De emulator wordt geleverd met een PowerShell-module om de status van de service te starten, te stoppen, te verwijderen en op te halen. Voer de volgende cmdlet uit om de PowerShell-module te gebruiken:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

of plaats `PSModules` de `PSModulesPath` map op uw en importeer deze zoals weergegeven in de volgende opdracht:

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

De Azure Cosmos Emulator kan worden uitgevoerd op Docker voor Windows. De emulator werkt niet op Docker voor Oracle Linux.

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
> Als u een fout in poortconflict ziet (opgegeven poort is al in gebruik) wanneer u de opdracht dockerrun uitvoert, u een aangepaste poort passeren door de poortnummers te wijzigen. U bijvoorbeeld de '-p 8081:8081' wijzigen in '-p 443:8081'

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

Als u een .NET-clienttoepassing hebt die wordt uitgevoerd op een Linux-dockercontainer en als u Azure Cosmos-emulator op een hostmachine uitvoert, volgt u het onderstaande gedeelte voor Linux om het certificaat in de Linux-dockercontainer te importeren.

## <a name="running-on-mac-or-linux"></a>Draait op Mac of Linux<a id="mac"></a>

Momenteel kan de Cosmos emulator alleen worden uitgevoerd op Windows. Gebruikers met Mac of Linux kunnen de emulator uitvoeren in een Virtuele Windows-machine die een hypervisor hostzoals Parallels of VirtualBox. Hieronder staan de stappen om dit in te schakelen.

Voer de onderstaande opdracht in de Windows VM uit en noteer het IPv4-adres.

```cmd
ipconfig.exe
```

Binnen uw toepassing moet u de URI die als eindpunt wordt `ipconfig.exe` gebruikt `localhost`wijzigen om het IPv4-adres te gebruiken dat is geretourneerd door in plaats van .

De volgende stap, vanuit de Windows VM, start de Cosmos emulator vanaf de opdrachtregel met behulp van de volgende opties.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Tot slot moeten we het Emulator CA-certificaat importeren in de Linux- of Mac-omgeving.

### <a name="linux"></a>Linux

Als u aan Linux werkt, worden .NET-relays op OpenSSL ingeschakeld om de validatie uit te brengen:

1. [Exporteer het certificaat in PFX-formaat](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (PFX is beschikbaar bij het kiezen om de privésleutel te exporteren). 

1. Kopieer dat PFX-bestand naar uw Linux-omgeving.

1. Het PFX-bestand converteren naar een CRT-bestand

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Kopieer het CRT-bestand naar de map met aangepaste certificaten in uw Linux-distributie. Vaak op Debian distributies, `/usr/local/share/ca-certificates/`is het gelegen op .

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Werk de CA-certificaten bij, waarmee de `/etc/ssl/certs/` map wordt bijgewerkt.

   ```bash
   update-ca-certificates
   ```

### <a name="mac-os"></a>Mac OS

Gebruik de volgende stappen als u aan Mac werkt:

1. [Exporteer het certificaat in PFX-formaat](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-ssl-certificate) (PFX is beschikbaar bij het kiezen om de privésleutel te exporteren).

1. Kopieer dat PFX-bestand naar uw Mac-omgeving.

1. Open de *toepassing Keychain Access* en importeer het PFX-bestand.

1. Open de lijst met certificaten en `localhost`identificeer de lijst met de naam .

1. Open het contextmenu voor dat specifieke item, selecteer *Item opvragen* en selecteer onder *Vertrouwen* > *wanneer u deze certificaatoptie gebruikt,* De optie *Altijd vertrouwen .* 

   ![Open het contextmenu voor dat specifieke item, selecteer Item opvragen en onder Vertrouwen - Selecteer bij gebruik van deze certificaatoptie Altijd vertrouwen](./media/local-emulator/mac-trust-certificate.png)

Na het volgen van deze stappen vertrouwt uw omgeving op het certificaat `/AllowNetworkAccess`dat door de Emulator wordt gebruikt wanneer u verbinding maakt met het IP-adres door .

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik de volgende tips om problemen op te lossen die u tegenkomt met de Azure Cosmos Emulator:

- Als u een nieuwe versie van de emulator hebt geïnstalleerd en er fouten optreden, zorg dan dat u uw gegevens opnieuw instelt. U uw gegevens opnieuw instellen door met de rechtermuisknop op het Azure Cosmos Emulator-pictogram op het systeemvak te klikken en vervolgens op Gegevens opnieuw instellen te klikken.... Als dat de fouten niet oplost, u de emulator en eventuele oudere versies van de emulator verwijderen als deze worden gevonden, de map C:\Program files\Azure Cosmos DB Emulator verwijderen en de emulator opnieuw installeren. Zie [De lokale emulator verwijderen](#uninstall) voor instructies.

- Als de Azure Cosmos Emulator vastloopt, verzamelt u dumpbestanden uit de map '%LOCALAPPDATA%\CrashDumps', comprimeert u deze en opent u een ondersteuningsticket vanaf de [Azure-portal.](https://portal.azure.com)

- Als u crashes `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`in, dit kan een symptoom waar de prestatiemeteritems zijn in een beschadigde staat. Meestal wordt het probleem opgelost door de volgende opdracht uit een opdrachtprompt voor beheerders uit te voeren:

  ```cmd
  lodctr /R
   ```

- Als u een verbindingsprobleem tegenkomt, [verzamelt u traceerbestanden,](#trace-files)comprimeert u deze en opent u een ondersteuningsticket in de [Azure-portal.](https://portal.azure.com)

- Als u het bericht **Service niet beschikbaar** krijgt, is het mogelijk dat de emulator de netwerkstack niet kan initialiseren. Controleer of de veilige Pulse-client of Juniper-netwerkclient is geïnstalleerd. De netwerkfilterstuurprogramma's van deze clients kunnen mogelijk de oorzaak zijn van het probleem. Doorgaans kan het probleem worden opgelost door stuurprogramma's voor netwerkfilters van derden te verwijderen. U ook de emulator starten met /DisableRIO, die de communicatie van het emulatornetwerk overschakelt naar gewone Winsock. 

- Als de emulator wordt uitgevoerd en uw computer naar de slaapstand gaat of er besturingssysteemupdates worden uitgevoerd, wordt mogelijk het bericht **Service is momenteel niet beschikbaar** weergegeven. Stel de gegevens van de emulator opnieuw in door met de rechtermuisknop te klikken op het pictogram dat op het systeemvan de windows-meldingslade wordt weergegeven en selecteer **Gegevens opnieuw instellen.**

### <a name="collect-trace-files"></a><a id="trace-files"></a>Traceringsbestanden verzamelen

Voor het verzamelen van foutopsporingsgegevens, voert u de volgende opdrachten uit vanaf een opdrachtprompt voor een beheerder:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Kijk in het systeemvak of het programma is afgesloten. Dit kan een minuut duren. U ook gewoon op **Afsluiten** klikken in de gebruikersinterface van Azure Cosmos Emulator.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Reproduceer het probleem. Als Data Explorer niet werkt, hoeft u alleen te wachten tot de browser een paar seconden wordt geopend om de fout te achterhalen.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Ga naar `%ProgramFiles%\Azure Cosmos DB Emulator` en zoek het bestand docdbemulator_000001.etl op.
8. Open een ondersteuningsticket in de [Azure-portal](https://portal.azure.com) en voeg het .etl-bestand samen met repro-stappen toe.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>De lokale emulator verwijderen

1. Sluit alle geopende exemplaren van de lokale emulator af door met de rechtermuisknop op het Azure Cosmos Emulator-pictogram op het systeemvak te klikken en vervolgens op Afsluiten te klikken. Het afsluiten van alle exemplaren kan een paar minuten duren.
2. Typ in het zoekvak Windows **Apps en onderdelen** en klik op **Apps en onderdelen (systeeminstellingen)**.
3. Ga in de lijst met apps naar **Azure Cosmos DB Emulator**, selecteer de app, klik op **Verwijderen**, bevestig dit en klik nogmaals op **Verwijderen**.
4. Wanneer de app is verwijderd, gaat u naar `%LOCALAPPDATA%\CosmosDBEmulator` en verwijdert u de map.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de lokale emulator kunt gebruiken voor gratis lokale ontwikkelingsdoeleinden. U kunt nu verder gaan met de volgende zelfstudie om te leren hoe u SSL-certificaten voor de emulator exporteert.

> [!div class="nextstepaction"]
> [De Azure Cosmos Emulator-certificaten exporteren](local-emulator-export-ssl-certificates.md)
