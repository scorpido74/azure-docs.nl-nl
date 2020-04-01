---
title: Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies
description: Meer informatie over de ondersteuning van Apache Cassandra-functies in Azure Cosmos DB Cassandra-API
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 223544f7ceddce6bc2071d561da1cff1c0d4b53b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420158"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt communiceren met de Azure Cosmos DB Cassandra-API via open-source Cassandra client[stuurprogramma's](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) die compatibel zijn met Cassandra Query Language (CQL) v4 [wire-protocol](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec). 

Door de Azure Cosmos DB Cassandra API te gebruiken, kunt u profiteren van de voordelen van de Apache Cassandra API’s en van de enterprise-mogelijkheden die Azure Cosmos DB biedt. De enterprise-mogelijkheden zijn onder andere [wereldwijde distributie](distribute-data-globally.md), [automatische scale-out partitionering](partition-data.md), beschikbaarheid- en latentiegaranties, versleuteling van gegevens in rust, back-ups en nog veel meer.

## <a name="cassandra-protocol"></a>Cassandra-protocol 

De Cassandra-API van Azure Cosmos DB is compatibel met CQL versie **v4**. De ondersteunde CQL-opdrachten, hulpprogramma's, beperkingen en uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met Azure Cosmos DB Cassandra-API.

## <a name="cassandra-driver"></a>Cassandra-stuurprogramma

De volgende versies van de Cassandra-stuurprogramma's worden ondersteund door Azure Cosmos DB Cassandra-API:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL-gegevenstypen 

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-gegevenstypen:

* ascii  
* bigint  
* blob  
* booleaans  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* tekst  
* tijd  
* tijdstempel  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>CQL-functies

Azure Cosmos DB Cassandra-API ondersteunt de volgende CQL-functies:

* Token  
* Statistische functies
  * min, max, avg, tellen
* Blob-conversiefuncties 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID- en timeuuid-functies 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Limieten voor Cassandra-API

Azure Cosmos DB Cassandra-API heeft geen limiet wat betreft de grootte van gegevens die in een tabel worden opgeslagen. Er kunnen honderden terabytes of petabytes aan gegevens worden opgeslagen terwijl de partitiesleutellimieten worden gerespecteerd. Op dezelfde manier heeft elke entiteit of rij-equivalent geen beperkingen op het aantal kolommen. De totale omvang van de entiteit mag echter niet meer bedragen dan 2 MB. De gegevens per partitiesleutel mogen niet hoger zijn dan 20 GB zoals in alle andere API's.

## <a name="tools"></a>Hulpprogramma's 

Azure Cosmos DB Cassandra-API is een beheerd serviceplatform. Het vereist geen beheeroverhead of hulpprogramma's zoals Garbage Collector, Java Virtual Machine (JVM) en nodetool om het cluster te beheren. Het ondersteunt hulpprogramma’s zoals cqlsh, dat binaire CQLv4-compatibiliteit gebruikt. 

* De gegevensverkenner, metrische gegevens, logboekdiagnoses, PowerShell en CLI van Azure Portal zijn andere ondersteunde mechanismen om het account te beheren.

## <a name="cql-shell"></a>CQL Shell  

CQLSH command-line utility wordt geleverd met Apache Cassandra 3.1.1 en werkt out of box door het instellen van een aantal omgevingsvariabelen.

**Windows:**

Als u windows gebruikt, raden we u aan het [Windows-bestandssysteem voor Linux](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux)in te schakelen. U dan de linux commando's hieronder volgen.

**UNIX/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL-opdrachten

Azure Cosmos DB ondersteunt de volgende databaseopdrachten op Cassandra-API-accounts.

* KEYSPACE MAKEN (De replicatie-instellingen voor deze opdracht worden genegeerd)
* CREATE TABLE 
* MAAK-INDEX (zonder indexnaam op te geven en volledig bevroren indexen die nog niet worden ondersteund)
* FILTERING TOESTAAN
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - alleen niet-geregistreerde opdrachten worden ondersteund 
* DELETE

Alle CRUD-bewerkingen die worden uitgevoerd via een CQL v4-compatibele SDK, geven extra informatie over fout- en aanvraageenheden die worden verbruikt. De taken Delete en UPDATE moeten worden behandeld met resourcegovernance die in aanmerking wordt genomen om een zo efficiënt mogelijk gebruik van de ingerichte doorvoer te garanderen.

* Let wel: de waarde gc_grace_seconds moet nul zijn als deze is opgegeven.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Consistentietoewijzing 

Azure Cosmos DB Cassandra-API biedt een keuze aan consistentie voor leesbewerkingen.  De consistentie mapping is [hier](consistency-levels-across-apis.md#cassandra-mapping)gedetailleerd .

## <a name="permission-and-role-management"></a>Machtigings- en rolbeheer

Azure Cosmos DB ondersteunt rbac (role-based access control) voor het inrichten, roteren van sleutels, het weergeven van statistieken en alleen-lezen en lezen wachtwoorden/sleutels die via de [Azure-portal](https://portal.azure.com)kunnen worden verkregen. Azure Cosmos DB ondersteunt geen rollen voor CRUD-activiteiten.

## <a name="keyspace-and-table-options"></a>Opties voor sleutelruimte en tabel

De opties voor regionaam, klasse, replication_factor en datacenter in de opdracht Sleutelruimte maken worden momenteel genegeerd. Het systeem gebruikt de [onderliggende azure](global-dist-under-the-hood.md) cosmos DB's globale distributiereplicatiemethode om de regio's toe te voegen. Als u de aanwezigheid van gegevens in verschillende regio's nodig hebt, u deze op accountniveau inschakelen met PowerShell, CLI of portal, voor meer informatie, zie het artikel [over het toevoegen van regio's.](how-to-manage-database-account.md#addremove-regions-from-your-database-account) Durable_writes kan niet worden uitgeschakeld omdat Azure Cosmos DB ervoor zorgt dat elke schrijf duurzaam is. In elke regio repliceert Azure Cosmos DB de gegevens over de replicaset die uit vier replica's bestaat en kan deze [configuratie](global-dist-under-the-hood.md) van de replicaset niet worden gewijzigd.
 
Alle opties worden genegeerd bij het maken van de tabel, behalve gc_grace_seconds, die moet worden ingesteld op nul.
De Keyspace en de tabel hebben een extra optie met de naam "cosmosdb_provisioned_throughput" met een minimumwaarde van 400 RU/s. Met de Keyspace-doorvoer u doorvoer delen over meerdere tabellen en is het handig voor scenario's waarin niet alle tabellen de ingerichte doorvoer gebruiken. Met de opdracht Tabel wijzigen u de ingerichte doorvoer in de regio's wijzigen. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Gebruik van het Cassandra-beleid voor opnieuw proberen

Azure Cosmos DB is een resource beheerd systeem. Dit betekent dat u een bepaald aantal bewerkingen in een bepaalde seconde uitvoeren op basis van de aanvraageenheden die door de bewerkingen worden verbruikt. Als een toepassing die limiet in een bepaalde seconde overschrijdt, zijn aanvragen tariefbeperkt en worden uitzonderingen gegenereerd. De Cassandra API in Azure Cosmos DB vertaalt deze uitzonderingen naar overbelaste fouten op het Cassandra native protocol. Om ervoor te zorgen dat uw toepassing verzoeken kan onderscheppen en opnieuw proberen in geval van beperking van de snelheid, worden de [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) en de [Java-extensies](https://github.com/Azure/azure-cosmos-cassandra-extensions) verstrekt. Als u andere SDK's gebruikt om toegang te krijgen tot de Cassandra API in Azure Cosmos DB, maakt u een verbindingsbeleid om deze uitzonderingen opnieuw te proberen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing

