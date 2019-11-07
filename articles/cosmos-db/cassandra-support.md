---
title: Apache Cassandra-functies en opdrachten ondersteund door Azure Cosmos DB Cassandra API
description: Meer informatie over de ondersteuning van Apache Cassandra-functies in Azure Cosmos DB Cassandra-API
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 12df79696033e69abbf48f053c1a594be9409cda
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721104"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Door Azure Cosmos DB Cassandra API ondersteunde Apache Cassandra-functies 

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt communiceren met de Azure Cosmos DB Cassandra-API via open-source Cassandra client[stuurprogramma's](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) die compatibel zijn met Cassandra Query Language (CQL) v4 [wire-protocol](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver). 

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
  * min, Max, Gem, aantal
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
  


## <a name="cassandra-api-limits"></a>Cassandra-API limieten

Azure Cosmos DB Cassandra-API heeft geen limiet wat betreft de grootte van gegevens die in een tabel worden opgeslagen. Er kunnen honderden terabytes of petabytes aan gegevens worden opgeslagen terwijl de partitiesleutellimieten worden gerespecteerd. Op dezelfde manier heeft elke entiteit of een gelijkwaardige rij geen limieten voor het aantal kolommen. De totale grootte van de entiteit mag echter niet meer zijn dan 2 MB. De gegevens per partitie sleutel mogen niet groter zijn dan 10 GB, zoals in alle andere Api's.

## <a name="tools"></a>Hulpprogramma's 

Azure Cosmos DB Cassandra-API is een beheerd serviceplatform. Het vereist geen beheeroverhead of hulpprogramma's zoals Garbage Collector, Java Virtual Machine (JVM) en nodetool om het cluster te beheren. Het ondersteunt hulpprogramma’s zoals cqlsh, dat binaire CQLv4-compatibiliteit gebruikt. 

* Azure Portal Data Explorer, metrische gegevens, logboek diagnostiek, Power shell en CLI zijn andere ondersteunde mechanismen om het account te beheren.

## <a name="cql-shell"></a>CQL Shell  

Het CQLSH-opdracht regel hulpprogramma wordt geleverd met Apache Cassandra 3.1.1 en is out-of-Box voor een aantal omgevings variabelen.

**Windows:**

Als u Windows gebruikt, raden we u aan het [Windows-Bestands systeem](https://docs.microsoft.com/en-us/windows/wsl/install-win10#install-the-windows-subsystem-for-linux)in te scha kelen voor Linux. U kunt vervolgens de onderstaande Linux-opdrachten volgen.

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
curl https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
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

* Een spatie maken (de replicatie-instellingen voor deze opdracht worden genegeerd)
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - alleen niet-geregistreerde opdrachten worden ondersteund 
* DELETE

Alle ruwe bewerkingen die worden uitgevoerd via een met CQL v4 compatibele SDK, retour neren extra informatie over de verbruikte fout-en aanvraag eenheden. De opdrachten DELETE en UPDATE moeten worden afgehandeld met resource governance, om ervoor te zorgen dat de ingerichte door Voer optimaal wordt gebruikt.

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

Azure Cosmos DB Cassandra-API biedt een keuze aan consistentie voor leesbewerkingen.  De consistentie toewijzing wordt [hier](consistency-levels-across-apis.md#cassandra-mapping)beschreven.

## <a name="permission-and-role-management"></a>Machtigings- en rolbeheer

Azure Cosmos DB ondersteunt op rollen gebaseerd toegangs beheer (RBAC) voor het inrichten, draaien van sleutels, het weer geven van metrische gegevens en alleen-lezen wacht woorden/sleutels die kunnen worden verkregen via de [Azure Portal](https://portal.azure.com). Azure Cosmos DB biedt geen ondersteuning voor rollen voor ruwe activiteiten.

## <a name="keyspace-and-table-options"></a>Opties voor de Keys en tabellen

De opties voor regio naam, klasse, replication_factor en Data Center in de opdracht ' spatie maken ' worden momenteel genegeerd. Het systeem maakt gebruik van de [globale distributie](global-dist-under-the-hood.md) replicatie methode van de onderliggende Azure Cosmos DB om de regio's toe te voegen. Als u de aanwezigheid van gegevens over meerdere regio's nodig hebt, kunt u deze op account niveau inschakelen met Power shell, CLI of portal, zie het artikel [regio's toevoegen](how-to-manage-database-account.md#addremove-regions-from-your-database-account) voor meer informatie. Durable_writes kan niet worden uitgeschakeld omdat Azure Cosmos DB ervoor zorgt dat elke schrijf bewerking duurzaam is. Azure Cosmos DB repliceert in elke regio de gegevens over de replicaset die bestaat uit vier replica's en deze replicaset- [configuratie](global-dist-under-the-hood.md) kan niet worden gewijzigd.
 
Alle opties worden genegeerd bij het maken van de tabel, met uitzonde ring van gc_grace_seconds, die moeten worden ingesteld op nul.
De spatie en tabel hebben een extra optie met de naam ' cosmosdb_provisioned_throughput ' met een minimum waarde van 400 RU/s. De door Voer van de schijf ruimte maakt het delen van de door voer over meerdere tabellen mogelijk en is handig voor scenario's waarbij de ingerichte door Voer niet wordt gebruikt voor alle tabellen. Met de opdracht ALTER TABLE kunt u de ingerichte door Voer in de regio's wijzigen. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Gebruik van Cassandra verbindings beleid opnieuw proberen

Azure Cosmos DB is een bron systeem. Dit betekent dat u een bepaald aantal bewerkingen in een bepaalde seconde kunt uitvoeren op basis van de aanvraag eenheden die door de bewerkingen worden gebruikt. Als een toepassing die limiet in een opgegeven seconde overschrijdt, worden aanvragen op basis van een rente beperking en uitzonde ringen gegenereerd. Met de Cassandra-API in Azure Cosmos DB worden deze uitzonde ringen omgezet in overbelaste fouten op het Cassandra systeem eigen protocol. Om ervoor te zorgen dat uw toepassing aanvragen kan onderscheppen en opnieuw proberen in het geval van een limiet, worden de [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) -en [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -extensies gegeven. Als u andere Sdk's gebruikt om toegang te krijgen tot Cassandra-API in Azure Cosmos DB, maakt u een verbindings beleid om het opnieuw te proberen op deze uitzonde ringen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing

