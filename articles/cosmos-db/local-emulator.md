---
title: Lokaal ontwikkelen met Azure Cosmos Emulator
description: Met behulp van Azure Cosmos Emulator kunt u uw toepassing lokaal ontwikkelen en testen, kosteloos en zonder dat u een Azure-abonnement hoeft te nemen.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 08/19/2020
ms.openlocfilehash: 40c32226f0e79e66db45d0c32614eaa4c5b543f9
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607540"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Azure Cosmos Emulator gebruiken voor lokaal ontwikkelen en testen

De Azure Cosmos Emulator biedt een lokale omgeving waarin de Azure Cosmos DB-service wordt geëmuleerd voor ontwikkelingsdoeleinden. Met behulp van de Azure Cosmos Emulator kunt u de toepassing lokaal ontwikkelen en testen, kosteloos en zonder een Azure-abonnement te maken. Als u tevreden bent over hoe de toepassing werkt in de Azure Cosmos Emulator, kunt u overstappen naar een Azure Cosmos-account in de cloud.

U kunt met behulp van Azure Cosmos Emulator ontwikkelen met [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)en [Table](local-emulator.md#table-api) API-accounts. Op dit moment biedt de Data Explorer-weergave in de emulator echter alleen volledige ondersteuning voor clients voor de SQL-API. 

## <a name="how-the-emulator-works"></a>Hoe de emulator werkt

Azure Cosmos Emulator biedt een zeer betrouwbare emulatie van de Azure Cosmos DB-service. De emulator ondersteunt dezelfde functionaliteit als Azure Cosmos DB, inclusief ondersteuning voor het maken van en uitvoeren van query's op gegevens, het inrichten en schalen van containers, en het uitvoeren van opgeslagen procedures en triggers. U kunt toepassingen ontwikkelen en testen met behulp van Azure Cosmos Emulator en deze op globale schaal implementeren in Azure door slechts één wijziging aan te brengen in de configuratie van het verbindingseindpunt voor Azure Cosmos DB.

Emulatie van de Azure Cosmos DB-service is zeer betrouwbaar, maar de implementatie van de emulator is anders dan die van de service. De emulator gebruikt bijvoorbeeld standaardbesturingssysteemonderdelen, zoals het lokale bestandssysteem voor persistentie en de HTTPS-protocolstack voor connectiviteit. Hierdoor zijn bepaalde functies die afhankelijk zijn van de Azure-infrastructuur, zoals globale replicatie, vertragingen van milliseconden voor lezen/schrijven en instelbare consistentieniveaus, niet beschikbaar.

U kunt ook gegevens migreren tussen Azure Cosmos Emulator en de Azure Cosmos DB-service met behulp van de [Azure Cosmos DB Data Migration Tool](https://github.com/azure/azure-documentdb-datamigrationtool).

U kunt Azure Cosmos Emulator uitvoeren in de Windows Docker-container. Raadpleeg de [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) voor de pull-opdracht van Docker en [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) voor het `Dockerfile` en meer informatie.

## <a name="differences-between-the-emulator-and-the-service"></a>Verschillen tussen de emulator en de service

Omdat Azure Cosmos Emulator een geëmuleerde omgeving biedt op de lokale computer van de ontwikkelaar, zijn er enkele verschillen in functionaliteit tussen de emulator en een Azure Cosmos-account in de cloud:

* Op dit moment biedt Data Explorer in de emulator alleen ondersteuning voor clients voor de SQL-API. De Data Explorer-weergave en bewerkingen voor Azure Cosmos DB-API's, zoals MongoDB-, Table-, Graph-en Cassandra-API's, worden niet volledig ondersteund.
* Azure Cosmos Emulator ondersteunt slechts één vast account en een bekende hoofdsleutel. Het opnieuw genereren van sleutels is niet mogelijk in Azure Cosmos Emulator, maar de standaardsleutel kan worden gewijzigd via de opdrachtregel.
* De Azure Cosmos-emulator ondersteunt een Azure Cosmos-account in de [ingerichte doorvoer](set-throughput.md) modus; een Azure Cosmos-account wordt momenteel niet ondersteund in de [serverloze](serverless.md) modus.
* Azure Cosmos Emulator is geen schaalbare service en biedt geen ondersteuning voor een groot aantal containers.
* Azure Cosmos Emulator biedt geen verschillende [Azure Cosmos DB-consistentieniveaus](consistency-levels.md).
* Azure Cosmos Emulator biedt geen [replicatie in meerdere regio's](distribute-data-globally.md).
* Aangezien uw exemplaar van Azure Cosmos Emulator mogelijk niet altijd is bijgewerkt met de meest recente wijzigingen via de Azure Cosmos DB-service, is het belangrijk om de [Azure Cosmos DB-capaciteitsplanner](https://www.documentdb.com/capacityplanner) te raadplegen om een nauwkeurige schatting te maken van de productiedoorvoerbehoeften van uw toepassing.
* Wanneer u Azure Cosmos Emulator gebruikt, kunt u standaard maximaal 25 containers met een vaste grootte maken (alleen ondersteund bij gebruik van Azure Cosmos DB-SDK's) of vijf containers van onbeperkte grootte met behulp van Azure Cosmos Emulator. Zie [De PartitionCount-waarde instellen](#set-partitioncount) voor meer informatie over het wijzigen van deze waarde.
* Emulator ondersteunt de maximale ID-eigenschaps grootte van 254 tekens.

## <a name="system-requirements"></a>Systeemvereisten

Voor Azure Cosmos Emulator gelden de volgende hardware- en softwarevereisten:

* Softwarevereisten
  * Windows Server 2012 R2, Windows Server 2016 of Windows 10
  * 64-bits besturingssysteem
* Minimale hardwarevereisten
  * 2 GB RAM
  * 10 GB beschikbare schijfruimte

## <a name="installation"></a>Installatie

U kunt Azure Cosmos Emulator downloaden en installeren vanuit het [Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator) of u kunt de emulator uitvoeren in Docker voor Windows. Zie [Uitvoeren op Docker](#running-on-docker) voor instructies over het gebruik van de emulator op Docker voor Windows.

> [!NOTE]
> Als u Azure Cosmos Emulator wilt installeren, configureren en uitvoeren, hebt u beheerdersbevoegdheden nodig op de computer. De emulator maakt/voegt een certificaat toe en stelt ook de firewallregels in om de emulatorservices te kunnen uitvoeren. Daarom moet de emulator dergelijke bewerkingen kunnen uitvoeren.

## <a name="running-on-windows"></a>Uitvoeren in Windows

Als u Azure Cosmos Emulator wilt starten, selecteert u de Start-knop of drukt u op de Windows-toets. Begin met het typen van **Azure Cosmos Emulator** en selecteer de emulator in de lijst met toepassingen.

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="Selecteer de knop Start of druk op de Windows-toets, begin met het typen van de Azure Cosmos-emulator en selecteer de emulator in de lijst met toepassingen":::

Wanneer de emulator wordt gestart, ziet u een pictogram in het systeemvak op de taakbalk van Windows. 

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="Melding in systeemvak van lokale Azure Cosmos Emulator":::

Azure Cosmos Emulator wordt standaard uitgevoerd op de lokale computer ('localhost') en luistert op poort 8081.

Azure Cosmos Emulator wordt standaard geïnstalleerd in `C:\Program Files\Azure Cosmos DB Emulator`. U kunt de emulator ook starten en stoppen vanaf de opdrachtregel. Zie de [informatie over het opdrachtregelprogramma](#command-line) voor meer informatie.

## <a name="start-data-explorer"></a>Data Explorer starten

Als Azure Cosmos Emulator wordt gestart, wordt automatisch Azure Cosmos Data Explorer geopend in uw browser. Het adres wordt weergegeven als `https://localhost:8081/_explorer/index.html`. Als u de explorer sluit en later opnieuw wilt openen, kunt u de URL in uw browser openen of deze starten vanuit Azure Cosmos Emulator in het Windows-systeemvak, zoals hieronder wordt weergegeven.

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="Startprogramma voor Data Explorer van lokale Azure Cosmos Emulator":::

## <a name="checking-for-updates"></a>Controleren op updates

Data Explorer geeft aan of er een nieuwe update beschikbaar is om te downloaden.

> [!NOTE]
> Gegevens die zijn gemaakt in de ene versie van Azure Cosmos Emulator (zie %LOCALAPPDATA%\CosmosDBEmulator of de optionele DataPath-instellingen), zijn niet gegarandeerd toegankelijk wanneer u een andere versie gebruikt. Als u uw gegevens voor de lange termijn wilt bewaren, verdient het aanbeveling om die gegevens op te slaan in een Azure Cosmos-account, in plaats van in Azure Cosmos Emulator.

## <a name="authenticating-requests"></a>Aanvragen verifiëren

Net als bij Azure Cosmos DB in de cloud, moet elke aanvraag die u op basis van Azure Cosmos Emulator maakt, worden geverifieerd. Azure Cosmos Emulator ondersteunt één vast account en een bekende verificatiesleutel voor hoofdsleutelverificatie. Dit account en deze sleutel zijn de enige referenties die zijn toegestaan voor gebruik met Azure Cosmos Emulator. Dit zijn:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> De hoofdsleutel ondersteund door Azure Cosmos Emulator is alleen bedoeld voor gebruik met de emulator. U kunt niet uw Azure Cosmos DB-account en -sleutel voor productie gebruiken met Azure Cosmos Emulator.

> [!NOTE]
> Als u de emulator hebt gestart met de optie/Key, gebruikt u de gegenereerde sleutel in plaats van `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Zie de [informatie over het opdrachtregelprogramma](#command-line) voor meer informatie.

Bovendien ondersteunt Azure Cosmos Emulator (net als Azure Cosmos DB) alleen beveiligde communicatie via TLS.

## <a name="running-on-a-local-network"></a>Uitvoeren op een lokaal netwerk

U kunt de emulator uitvoeren op een lokaal netwerk. U maakt netwerktoegang mogelijk door de optie `/AllowNetworkAccess` op te geven op de [opdrachtregel](#command-line-syntax). U moet hiervoor ook `/Key=key_string` of `/KeyFile=file_name` opgeven. U kunt `/GenKeyFile=file_name` gebruiken om vooraf een bestand met een willekeurige sleutel te genereren. Vervolgens kunt u die doorgeven aan `/KeyFile=file_name` of `/Key=contents_of_file`.

Om voor de eerste keer toegang te krijgen tot het netwerk, moet de gebruiker de emulator afsluiten en de gegevensmap van de emulator verwijderen (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Ontwikkelen met de emulator

### <a name="sql-api"></a>SQL-API

Wanneer Azure Cosmos Emulator wordt uitgevoerd op uw computer, kunt u elke ondersteunde [Azure Cosmos DB-SDK](sql-api-sdk-dotnet-standard.md) of de [Azure Cosmos DB REST-API](/rest/api/cosmos-db/) gebruiken voor interactie met de emulator. Azure Cosmos Emulator bevat ook een ingebouwde Data Explorer. Hiermee kunt u containers maken voor de SQL-API of Cosmos DB voor MongoDB-API en kunt u items weergeven en bewerken zonder code te schrijven.

```csharp
// Connect to the Azure Cosmos Emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

Wanneer Azure Cosmos Emulator wordt uitgevoerd op uw computer, kunt u de [API voor MongoDB van Azure Cosmos DB](mongodb-introduction.md) gebruiken voor interactie met de emulator. Start de emulator als beheerder vanaf de opdrachtprompt met '/EnableMongoDbEndpoint'. Gebruik vervolgens de volgende verbindingsreeks om verbinding te maken met het MongoDB API-account:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tabel-API

Wanneer Azure Cosmos Emulator wordt uitgevoerd op uw computer, kunt u de [Table-API-SDK van Azure Cosmos DB](table-storage-how-to-use-dotnet.md) gebruiken voor interactie met de emulator. Start de emulator als beheerder vanaf de opdrachtprompt met '/EnableTableEndpoint'. Voer vervolgens de volgende code uit om verbinding te maken met het Table-API-account:

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

Start de emulator als beheerder vanaf de opdrachtprompt met '/EnableCassandraEndpoint'. U kunt ook de omgevingsvariabele `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`instellen.

* [Installeer Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Installeer Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

* Voer de volgende opdrachten uit in een opdrachtpromptvenster met normale rechten:

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

Start de emulator als beheerder vanaf de opdrachtprompt met '/EnableGremlinEndpoint'. U kunt ook de omgevingsvariabele `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`instellen.

* [Installeer apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4).

* Maak in Data Explorer van de emulator een database 'db1' en een verzameling 'coll1'. Kies '/name' voor de partitiesleutel.

* Voer de volgende opdrachten uit in een opdrachtpromptvenster met normale rechten:

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

* Voer in de Gremlin-shell de volgende opdrachten uit om verbinding te maken met het Gremlin-eindpunt:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-tlsssl-certificate"></a>Het TLS/SSL-certificaat exporteren

.NET-talen en -runtime maken gebruik van het Windows-certificaatarchief om veilig verbinding te maken met de lokale Azure DB Cosmos Emulator. Andere talen hebben hun eigen methode voor het beheren en gebruiken van certificaten. Java maakt gebruik van een eigen [certificaatarchief](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), terwijl Python gebruikmaakt van [socketwrappers](https://docs.python.org/2/library/ssl.html).

Om een certificaat te verkrijgen voor gebruik bij talen en runtimes die niet worden geïntegreerd in het certificaatarchief van Windows, moet u het exporteren met behulp van de Windows Certificate Manager. U kunt dit proces starten door certlm.msc uit te voeren of door de stapsgewijze instructies te volgen in [De Azure Cosmos Emulator-certificaten exporteren](./local-emulator-export-ssl-certificates.md). Zodra de certificaatmanager wordt uitgevoerd, opent u de persoonlijke certificaten zoals hieronder weergegeven en exporteert u het certificaat met de beschrijvende naam 'DocumentDBEmulatorCertificate' als een BASE 64-gecodeerd X.509-bestand (.cer).

:::image type="content" source="./media/local-emulator/database-local-emulator-ssl_certificate.png" alt-text="TLS/SSL-certificaat voor lokale Azure Cosmos Emulator":::

Volg de instructies in [Een certificaat toevoegen aan het Java CA-certificaatarchief](https://docs.microsoft.com/azure/java-add-certificate-ca-store) om het X.509-certificaat te importeren in het Java-certificaatarchief. Zodra het certificaat is geïmporteerd in het certificaatarchief, kunnen clients voor de SQL-API en de Azure Cosmo API voor MongoDB verbinding maken met Azure Cosmos Emulator.

Bij het verbinden met de emulator vanuit SDK's van Python en Node.js, is TLS-verificatie uitgeschakeld.

## <a name="command-line-tool-reference"></a><a id="command-line"></a>Opdrachtregelprogramma
Vanaf de installatielocatie kunt u de opdrachtregel gebruiken om de emulator te starten en te stoppen, opties te configureren en andere bewerkingen uit te voeren.

### <a name="command-line-syntax"></a>De syntaxis van opdrachtregel

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Typ `Microsoft.Azure.Cosmos.Emulator.exe /?` bij de opdrachtprompt om een lijst met opties te zien.

|**Optie** | **Beschrijving** | **Opdracht**| **Argumenten**|
|---|---|---|---|
|[Geen argumenten] | Start Azure Cosmos Emulator met de standaardinstellingen. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Help] |Toont de lijst met ondersteunde opdrachtregelargumenten.|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |Vraagt de status op van Azure Cosmos Emulator. De status wordt aangegeven door de afsluitcode: 1 = starten, 2 = wordt uitgevoerd, 3 = gestopt. Een negatieve afsluitcode geeft aan dat er een fout is opgetreden. Er wordt geen andere uitvoer geproduceerd. | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Afsluiten| Sluit Azure Cosmos Emulator af.| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | Specificeert het pad waarin de gegevensbestanden worden opgeslagen. De standaardwaarde is %LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Een toegankelijk pad |
|Poort | Specificeert het poortnummer dat moet worden gebruikt voor de emulator. De standaardwaarde is 8081. |Microsoft.Azure.Cosmos.Emulator.exe/Port =\<port\> | \<port\>: Enkel poort nummer |
| ComputePort | Specificeert het poortnummer dat moet worden gebruikt voor de Compute Interop Gateway-service. De testpoort voor het HTTP-eindpunt de gateway wordt berekend als ComputePort + 79. Daarom moeten ComputePort en ComputePort + 79 open en beschikbaar zijn. De standaardwaarde is 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Enkel poort nummer |
| EnableMongoDbEndpoint=3.2 | Schakelt MongoDB API 3.2 in | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | Schakelt MongoDB API 3.6 in | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | Specificeert het poortnummer dat moet worden gebruikt MongoDB compatibiliteit-API. De standaardwaarde is 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Enkel poort nummer|
| EnableCassandraEndpoint | Schakelt Cassandra-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Specificeert het poortnummer dat moet worden gebruikt voor het Cassandra-eindpunt. De standaardwaarde is 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Enkel poort nummer |
| EnableGremlinEndpoint | Schakelt Gremlin-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Het poortnummer dat moet worden gebruikt voor het Gremlin-eindpunt. De standaardwaarde is 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Enkel poort nummer |
|EnableTableEndpoint | Schakelt Azure Table-API in | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | Het poortnummer dat moet worden gebruikt voor het Azure Table-eindpunt. De standaardwaarde is 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Enkel poort nummer|
| KeyFile | Leest de autorisatiesleutel uit het opgegeven bestand. Gebruik de optie /GenKeyFile om een keyfile te genereren | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Pad naar het bestand |
| ResetDataPath | Verwijdert recursief alle bestanden in het opgegeven pad. Als u geen pad opgeeft, wordt standaard %LOCALAPPDATA%\CosmosDbEmulator gebruikt. | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Bestandspad  |
| StartTraces  |  Start het verzamelen van logboeken voor foutopsporing met behulp van LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | Stopt het verzamelen van logboeken voor foutopsporing met behulp van LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  Start het verzamelen van logboeken voor foutopsporing met behulp van het hulpprogramma Windows Performance Recorder. | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | Stopt het verzamelen van logboeken voor foutopsporing met behulp van het hulpprogramma Windows Performance Recorder. | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | De emulator genereert het zelfondertekende TLS/SSL-certificaat standaard opnieuw als het SAN van het certificaat de domeinnaam van de emulator-host, het lokale IPv4-adres, 'localhost' en '127.0.0.1' niet bevat. Met deze optie kan de emulator dan niet worden gestart. Vervolgens moet u de optie /GenCert gebruiken om een nieuw zelfondertekend TLS/SSL-certificaat te maken en te installeren. | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Genereert en installeert een nieuw zelfondertekend TLS/SSL-certificaat. Voeg eventueel een door komma's gescheiden lijst met extra DNS-namen toe om toegang tot de emulator te krijgen via het netwerk. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Optionele, door komma's gescheiden lijst met extra DNS-namen  |
| DirectPorts |Specificeert de poorten die worden gebruikt voor rechtstreekse connectiviteit. Standaardwaarden zijn 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPorts:\<directports\> | \<directports\>: Door komma's gescheiden lijst met 4 poorten |
| Sleutel |De autorisatiesleutel voor de emulator. De sleutel moet de base 64-codering zijn van een 64-byte-vector. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: Sleutel moet de base-64-code ring van een 64-byte-vector zijn|
| EnableRateLimiting | Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is ingeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Geeft aan dat het beperkingsgedrag van de aanvraagsnelheid is uitgeschakeld. |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | De gebruikersinterface van de emulator niet weergeven. | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | Geen Data Explorer weergeven bij het opstarten. |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | Specificeert het maximum aantal gepartitioneerde containers. Zie [Het aantal containers wijzigen](#set-partitioncount) voor meer informatie. | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Maximum aantal toegestane containers met één partitie. De standaardwaarde is 25. Maximaal toegestaan is 250.|
| DefaultPartitionCount| Specificeert het standaardaantal partities voor een gepartitioneerde container. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> De standaard waarde is 25.|
| AllowNetworkAccess | Geeft toegang tot de emulator via een netwerk. U moet ook/Key = \<key_string\> of/keyfile = door geven \<file_name\> om netwerk toegang in te scha kelen. | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> of Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/keyfile =\<file_name\>| |
| NoFirewall | Firewallregels niet aanpassen wanneer de optie /AllowNetworkAccess wordt gebruikt. |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | Een nieuwe autorisatiesleutel genereren en opslaan in het opgegeven bestand. De gegenereerde sleutel kan worden gebruikt met de opties/Key of/KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Consistentie | Het standaard consistentieniveau voor het account instellen. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: De waarde moet een van de volgende [consistentie niveaus](consistency-levels.md)zijn: sessie, sterk, uiteindelijk of BoundedStaleness. De standaardwaarde is Sessie. |
| ? | Het helpbericht weergeven.| | |

## <a name="change-the-number-of-containers"></a><a id="set-partitioncount"></a>Het aantal containers wijzigen

U kunt standaard maximaal 25 containers met een vaste grootte maken (alleen ondersteund bij gebruik van Azure Cosmos DB-SDK's) of vijf containers van onbeperkte grootte met behulp van Azure Cosmos Emulator. Door de waarde voor **PartitionCount** te wijzigen, kunt u maximaal 250 containers met een vaste grootte maken of vijftig containers van onbeperkte grootte, of een combinatie van de twee die niet groter is dan 250 containers met een vaste grootte (waarbij één container van onbeperkte grootte = vijf containers met een vaste grootte). Het wordt echter afgeraden om de emulator uit te voeren met meer dan 200 containers met een vaste grootte. Vanwege de overhead die hierdoor wordt toegevoegd aan de I/O-bewerkingen van de schijf, ontstaan er dan onvoorspelbare time-outs bij het gebruik van de eindpunt-API's.

Als u probeert een container te maken nadat het huidige aantal partities is overschreden, genereert de emulator een ServiceUnavailable-uitzondering, met het volgende bericht.

'Sorry, we are currently experiencing high demand in this region, and cannot fulfill your request at this time. We work continuously to bring more and more capacity online, and encourage you to try again. (Er is momenteel sprake van grote vraag in deze regio en we kunnen uw aanvraag op dit moment niet verwerken. We doen ons best om meer en meer capaciteit online te brengen en adviseren om het nogmaals te proberen.)
ActivityId: 12345678-1234-1234-1234-123456789abc'

Als u het aantal beschikbare containers voor Azure Cosmos Emulator wilt wijzigen, doet u het volgende:

1. Verwijder alle lokale Azure Cosmos Emulator-gegevens door met de rechtermuisknop op het pictogram van **Azure Cosmos Emulator** in het systeemvak te klikken en vervolgens te klikken op **Reset Data…** (Gegevens opnieuw instellen...).
2. Verwijder alle emulator-gegevens in de map `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Sluit alle geopende exemplaren door met de rechtermuisknop te klikken op het pictogram van de **Azure Cosmos DB Emulator** in het systeemvak en vervolgens te klikken op **Afsluiten**. Het afsluiten van alle exemplaren kan een paar minuten duren.
4. Installeer de nieuwste versie van [Azure Cosmos Emulator](https://aka.ms/cosmosdb-emulator).
5. Start de emulator met de vlag PartitionCount door een waarde < = 250 in te stellen. Bijvoorbeeld: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>De emulator beheren

De emulator wordt geleverd met een PowerShell-module voor het starten, stoppen, en verwijderen van de emulator, en voor het ophalen van de status van de service. Voer de volgende cmdlet uit om de PowerShell-module te gebruiken:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

of plaats de map `PSModules` in uw `PSModulesPath` en importeer deze zoals wordt weergegeven in de volgende opdracht:

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

Azure Cosmos Emulator kan worden uitgevoerd in Docker voor Windows. De emulator werkt niet op Docker voor Oracle Linux.

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
> Als u een fout over een poortconflict ziet (de opgegeven poort is al in gebruik) wanneer u de opdracht docker run uitvoert, kunt u een aangepaste poort doorgeven door de poortnummers aan te passen. U kunt '-p 8081:8081' bijvoorbeeld wijzigen in '-p 443:8081'.

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

Gebruik nu het eindpunt en de hoofdsleutel uit de reactie in uw client en importeer het TLS/SSL-certificaat in de host. Doe het volgende vanaf een opdrachtprompt met beheerdersbevoegdheden om het TLS/SSL-certificaat te importeren:

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

** \: https // ** \<emulator endpoint provided in response> **/_explorer/index.html**

Als er een .NET-clienttoepassing wordt uitgevoerd in een Linux-docker-container en als u de Azure Cosmos-emulator uitvoert op een hostmachine, volgt u de onderstaande sectie voor Linux om het certificaat te importeren in de Linux-docker-container.

## <a name="running-on-mac-or-linux"></a>Uitvoeren op Mac of Linux<a id="mac"></a>

De Cosmos-emulator kan momenteel alleen worden uitgevoerd in Windows. Gebruikers met Mac of Linux kunnen de emulator uitvoeren in een virtuele Windows-machine die wordt gehost in een Hyper Visor zoals Parallels of VirtualBox. Hieronder ziet u de stappen om dit te doen.

Voer in de Windows-VM de onderstaande opdracht uit en noteer het IPv4-adres.

```cmd
ipconfig.exe
```

In uw toepassing moet u de URI wijzigen die wordt gebruikt als eindpunt om het IPv4-adres te gebruiken dat wordt geretourneerd door `ipconfig.exe` in plaats van `localhost`.

De volgende stap is om vanuit de Windows-VM de Cosmos-emulator te starten vanaf de opdrachtregel met behulp van de volgende opties.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

Ten slotte moeten we het certificaat vertrouwens proces oplossen tussen de toepassing die wordt uitgevoerd op de Linux-of Mac-omgeving en de emulator. We hebben twee opties:

1. De SSL-validatie in de toepassing uitschakelen:

# <a name="net-standard-21"></a>[.NET Standard 2.1 +](#tab/ssl-netstd21)

   Voor elke toepassing die wordt uitgevoerd in een framework dat compatibel is met .NET Standard 2,1 of hoger, kunnen we gebruikmaken van het `CosmosClientOptions.HttpClientFactory` volgende:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2,0](#tab/ssl-netstd20)

   Voor elke toepassing die wordt uitgevoerd in een framework dat compatibel is met .NET Standard 2,0, kunnen we gebruikmaken van het `CosmosClientOptions.HttpClientFactory` volgende:

   [!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

   Voor Node.js toepassingen kunt u het bestand wijzigen `package.json` om het in te stellen `NODE_TLS_REJECT_UNAUTHORIZED` tijdens het starten van de toepassing:

   ```json
   "start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
   ```

--- 

> [!NOTE]
> Het uitschakelen van SSL-validatie wordt alleen aanbevolen voor ontwikkelings doeleinden en moet niet worden uitgevoerd in een productie omgeving.

2. Importeer het Emulator-CA-certificaat in de Linux-of Mac-omgeving:

### <a name="linux"></a>Linux

Als u werkt met Linux, vertrouwt .NET op OpenSSL voor de validatie:

1. [Exporteer het certificaat in PFX-indeling](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX is beschikbaar wanneer u de persoonlijke sleutel exporteert). 

1. Kopieer het PFX-bestand naar uw Linux-omgeving.

1. Converteer het PFX-bestand naar een CRT-bestand.

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. Kopieer het CRT-bestand naar de map met aangepaste certificaten in uw Linux-distributie. In het geval van Debian-distributies is dat meestal `/usr/local/share/ca-certificates/`.

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. Werk de CA-certificaten bij, waarmee ook de map `/etc/ssl/certs/` wordt bijgewerkt.

   ```bash
   update-ca-certificates
   ```

### <a name="macos"></a>macOS

Voer de volgende stappen uit als u werkt met een Mac:

1. [Exporteer het certificaat in PFX-indeling](./local-emulator-export-ssl-certificates.md#how-to-export-the-azure-cosmos-db-tlsssl-certificate) (PFX is beschikbaar wanneer u de persoonlijke sleutel exporteert).

1. Kopieer het PFX-bestand naar uw Mac-omgeving.

1. Open de toepassing *Sleutelhangertoegang* en importeer het PFX-bestand.

1. Open de lijst met certificaten en zoek het certificaat met de naam `localhost`.

1. Open het contextmenu voor dat specifieke item, selecteer *Toon info* en selecteer bij *Vertrouw* > *Gebruik bij dit certificaat* de optie *Vertrouw altijd*. 

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="Open het contextmenu voor dat specifieke item, selecteer Toon info en selecteer bij Vertrouw &gt; Gebruik bij dit certificaat de optie Vertrouw altijd":::

Als u deze stappen hebt uitgevoerd, wordt het certificaat dat door de emulator wordt gebruikt, vertrouwd in uw omgeving bij het verbinden met het IP-adres dat wordt weergegeven door `/AllowNetworkAccess`.

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik de volgende tips voor het oplossen van problemen die u kunnen optreden met Azure Cosmos Emulator:

- Als u een nieuwe versie van de emulator hebt geïnstalleerd en er fouten optreden, zorg dan dat u uw gegevens opnieuw instelt. U kunt uw gegevens opnieuw instellen door met de rechtermuisknop te klikken op het pictogram van Azure Cosmos Emulator in het systeemvak en vervolgens te klikken op Reset Data... (Gegevens opnieuw instellen...). Als de fouten hiermee niet zijn opgelost, kunt u de emulator en eventuele oudere versies van de emulator verwijderen, indien aanwezig. Verwijder de map C:\Program files\Azure Cosmos Emulator en installeer de emulator opnieuw. Zie [De lokale emulator verwijderen](#uninstall) voor instructies.

- Als Azure Cosmos Emulator vastloopt, verzamelt u dumpbestanden uit de map %LOCALAPPDATA%\CrashDumps, comprimeert u deze en opent u een ondersteuningsticket via de [Azure-portal](https://portal.azure.com).

- Als `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` vastloopt, kan dit een indicatie zijn dat de prestatiemeteritems beschadigd zijn. Dit probleem kunt u meestal oplossen door de volgende opdracht uit te voeren vanaf een opdrachtprompt met beheerdersrechten:

  ```cmd
  lodctr /R
   ```

- Als er sprake is van een verbindingsprobleem, [verzamelt u traceringsbestanden](#trace-files), comprimeert u de bestanden en opent u een ondersteuningsticket via de [Azure-portal](https://portal.azure.com).

- Als u het bericht **Service niet beschikbaar** krijgt, is het mogelijk dat de emulator de netwerkstack niet kan initialiseren. Controleer of de veilige Pulse-client of Juniper-netwerkclient is geïnstalleerd. De netwerkfilterstuurprogramma's van deze clients kunnen mogelijk de oorzaak zijn van het probleem. Doorgaans kan het probleem worden opgelost door stuurprogramma's voor netwerkfilters van derden te verwijderen. U kunt de emulator ook starten met/DisableRIO, waarmee de netwerkcommunicatie van de emulator wordt overgezet naar normale Winsock. 

- Als u **de aanvraag ' verboden ' ontvangt, wordt het bericht ': ' verzonden met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS...** verbindings problemen. Dit kan worden veroorzaakt door algemene wijzigingen in het besturings systeem (bijvoorbeeld insider preview Build 20170) of de instellingen van de browser die TLS 1,3 als standaard inschakelen. Er kan een soort gelijke fout optreden wanneer u de SDK gebruikt om een aanvraag uit te voeren op de Cosmos-emulator, zoals **Microsoft.Azure.Documents.DocumentClientException: er wordt een aanvraag gemaakt met een verboden versleuteling in het Transit Protocol of de versleuteling. Controleer de mini maal toegestane protocol instelling voor SSL/TLS-accounts**. Dit wordt op dit moment verwacht, omdat Cosmos-emulator alleen gebruikmaakt van TLS 1,2-protocol. De aanbevolen oplossing is om de instellingen te wijzigen en standaard te bewerken in TLS 1,2. Ga voor instantie in IIS-beheer naar ' sites '-> "standaard websites" en zoek de site bindingen voor poort 8081 en bewerk ze om TLS 1,3 uit te scha kelen. Een vergelijk bare bewerking kan worden uitgevoerd voor de webbrowser via de opties voor instellingen.

- Als de emulator wordt uitgevoerd en uw computer naar de slaapstand gaat of er besturingssysteemupdates worden uitgevoerd, wordt mogelijk het bericht **Service is momenteel niet beschikbaar** weergegeven. Stel de gegevens van de emulator opnieuw in door met de rechtermuisknop op het pictogram te klikken dat wordt weergegeven in het Windows-systeemvak. Selecteer vervolgens **Reset Data** (Gegevens opnieuw instellen).

### <a name="collect-trace-files"></a><a id="trace-files"></a>Traceringsbestanden verzamelen

Voor het verzamelen van foutopsporingsgegevens, voert u de volgende opdrachten uit vanaf een opdrachtprompt voor een beheerder:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `Microsoft.Azure.Cosmos.Emulator.exe /shutdown`. Kijk in het systeemvak of het programma is afgesloten. Dit kan een minuut duren. U kunt ook gewoon klikken op **Exit** in de gebruikersinterface van Azure Cosmos Emulator.
3. `Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces`
4. `Microsoft.Azure.Cosmos.Emulator.exe`
5. Reproduceer het probleem. Als Data Explorer niet werkt, hoeft u alleen te wachten tot de browser een paar seconden wordt geopend om de fout te achterhalen.
6. `Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces`
7. Ga naar `%ProgramFiles%\Azure Cosmos DB Emulator` en zoek het bestand docdbemulator_000001.etl op.
8. Open een ondersteuningsticket via de [Azure-portal](https://portal.azure.com) en stuur het ETL-bestand mee, evenals de stappen om het probleem te reproduceren.

### <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>De lokale emulator verwijderen

1. Sluit alle geopende exemplaren van de lokale emulator door met de rechtermuisknop te klikken op het pictogram van Azure Cosmos Emulator in het systeemvak en vervolgens te klikken op Exit. Het afsluiten van alle exemplaren kan een paar minuten duren.
2. Typ in het zoekvak Windows **Apps en onderdelen** en klik op **Apps en onderdelen (systeeminstellingen)** .
3. Ga in de lijst met apps naar **Azure Cosmos DB Emulator**, selecteer de app, klik op **Verwijderen**, bevestig dit en klik nogmaals op **Verwijderen**.
4. Wanneer de app is verwijderd, gaat u naar `%LOCALAPPDATA%\CosmosDBEmulator` en verwijdert u de map.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de lokale emulator kunt gebruiken voor gratis lokale ontwikkelingsdoeleinden. U kunt nu verdergaan met de volgende zelfstudie om te leren hoe u TLS/SSL-certificaten voor de emulator exporteert.

> [!div class="nextstepaction"]
> [De Azure Cosmos Emulator-certificaten exporteren](local-emulator-export-ssl-certificates.md)
