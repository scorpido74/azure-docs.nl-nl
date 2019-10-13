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
ms.openlocfilehash: 0dcca2175d6ccc35a51bccb1e47f75d25cb8b11f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299188"
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
  


## <a name="cassandra-query-language-limits"></a>Limieten van Cassandra Query Language

Azure Cosmos DB Cassandra-API heeft geen limiet wat betreft de grootte van gegevens die in een tabel worden opgeslagen. Er kunnen honderden terabytes of petabytes aan gegevens worden opgeslagen terwijl de partitiesleutellimieten worden gerespecteerd. Evenzo heeft elke entiteit of rij-equivalent geen limieten voor het aantal kolommen; maar de totale omvang van de entiteit mag niet groter zijn dan 2 MB.

## <a name="tools"></a>Tools 

Azure Cosmos DB Cassandra-API is een beheerd serviceplatform. Het vereist geen beheeroverhead of hulpprogramma's zoals Garbage Collector, Java Virtual Machine (JVM) en nodetool om het cluster te beheren. Het ondersteunt hulpprogramma’s zoals cqlsh, dat binaire CQLv4-compatibiliteit gebruikt. 

* Andere ondersteunde mechanismen voor het beheren van het account zijn Azure Portal’s Data Explorer, metrische gegevens, logboekdiagnose, PowerShell en CLI.

## <a name="cql-shell"></a>CQL Shell  

Het CQLSH opdrachtregel-hulpprogramma wordt geleverd met Apache Cassandra 3.1.1 en werkt standaard met de volgende omgevingsvariabelen ingeschakeld:

Voordat u de volgende opdrachten uitvoert, [voegt u een Baltimore-basiscertificaat toe aan het cacerts-archief](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**UNIX/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>CQL-opdrachten

Azure Cosmos DB ondersteunt de volgende databaseopdrachten op Cassandra-API-accounts.

* Een spatie maken (de replicatie-instellingen voor deze opdracht worden genegeerd, het systeem gebruikt het [replicatie model van](global-dist-under-the-hood.md)de onderliggende Azure Cosmos db. Als u de aanwezigheid van gegevens in meerdere regio's nodig hebt, kunt u deze op het account niveau inschakelen met Power shell, CLI of portal. Zie [How to add or Remove Regions for u account voor](how-to-manage-database-account.md#addremove-regions-from-your-database-account) meer informatie.
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - alleen niet-geregistreerde opdrachten worden ondersteund 
* DELETE

Alle ruwe bewerkingen die worden uitgevoerd via een met CQLV4 compatibele SDK, retour neren extra informatie over de fout, verbruikte aanvraag eenheden. Verwijder-en update-opdrachten moeten worden afgehandeld met resource governance om te voor komen dat de ingerichte door Voer wordt gebruikt. 
* Let wel: de waarde gc_grace_seconds moet nul zijn als deze is opgegeven.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Consistentietoewijzing 

Azure Cosmos DB Cassandra-API biedt een keuze aan consistentie voor leesbewerkingen.  De consistentie toewijzing wordt beschreven [hier [(https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Machtigings- en rolbeheer

Azure Cosmos DB ondersteunt op rollen gebaseerd toegangs beheer (RBAC) voor het inrichten, draaien van sleutels, het weer geven van metrische gegevens en alleen-lezen wacht woorden/sleutels die kunnen worden verkregen via de [Azure Portal](https://portal.azure.com). Azure Cosmos DB biedt geen ondersteuning voor rollen voor ruwe activiteiten. 

## <a name="keyspace-and-table-options"></a>Opties voor de Keys en tabellen

De opties voor de regio naam, klasse, replication_factor, Data Center in de opdracht Create Keys keys worden momenteel genegeerd. Het systeem maakt gebruik van de [globale distributie](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) van de onderliggende Azure Cosmos DB als u de vereiste regio's toevoegt. Als u de aanwezigheid van gegevens in meerdere regio's nodig hebt, kunt u deze op account niveau inschakelen met Power shell, CLI of portal, zie dit document: https://docs.microsoft.com/en-us/azure/cosmos-db/how-to-manage-database-account#addremove-regions-from-your-database-account als u meer wilt weten. Durable_writes kan niet worden uitgeschakeld. Cosmos DB zorgt ervoor dat elke schrijf bewerking wordt gegarandeerd. In elke regio Cosmos DB repliceert gegevens over de replicaset die van vier replica's bestaan en kan deze replicaset- [configuratie](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) niet worden gewijzigd. Alle opties voor het maken van een tabel worden genegeerd, met uitzonde ring van gc_grace_seconds. dit moet nul zijn.
De cosmosdb_provisioned_throughput en de tabel hebben een extra optie-de minimum waarde van 400. Door Voer van de spatie ruimte kan door voer worden gedeeld in meerdere tabellen en nuttig voor scenario's wanneer niet alle tabellen de door Voer gebruiken. Met ALTER TABLE kunt u de ingerichte door Voer in de regio's wijzigen. Sampleks maken met replicatie = {' class ': ' SimpleStrategy '} en cosmosdb_provisioned_throughput = 2000;  
CREATE TABLE sampleks. T1 (user_id int PRIMARY KEY, LastName-tekst) met cosmosdb_provisioned_throughput = 2000; ALTER TABLE gks1. T1 WITH cosmosdb_provisioned_throughput = 10000;

## <a name="usage-of-cassandra-retry-connection-policy"></a>Gebruik van Cassandra verbindings beleid opnieuw proberen

Azure Cosmos DB is bron systeem. Dit betekent dat u een bepaald aantal bewerkingen in een bepaalde seconde kunt uitvoeren op basis van de ingerichte door Voer, gebaseerd op de aanvraag eenheden die worden verbruikt door bewerkingen. Als de toepassing de limiet overschrijdt die in een opgegeven aantal uitzonde ringen voor de tweede aanvraag wordt gegenereerd. De Cosmos DB Cassandra-API, vertaalt deze uitzonde ringen op overbelaste fouten op het Cassandra systeem eigen protocol. Om ervoor te zorgen dat uw toepassing de snelheids limiet voor een [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) -en [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) -helper kan onderscheppen en opnieuw kan worden ingesteld. Als u andere Sdk's gebruikt om toegang te krijgen tot Cassandra-API Cosmos DB maakt u het verbindings beleid voor een nieuwe poging om deze uitzonde ringen op te halen. 

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing

