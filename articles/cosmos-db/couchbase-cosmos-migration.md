---
title: Migreren van CouchBase naar Azure Cosmos DB SQL API
description: Stapsgewijze richtlijnen voor het migreren van CouchBase naar Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210942"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Migreren van CouchBase naar Azure Cosmos DB SQL API

Azure Cosmos DB is een schaalbare, wereldwijd gedistribueerde, volledig beheerde database. Het biedt gegarandeerde lage latentie toegang tot uw gegevens. Zie het [overzichtsartikel](introduction.md) voor meer informatie over Azure Cosmos DB. In dit artikel vindt u instructies voor het migreren van Java-toepassingen die zijn verbonden met Couchbase naar een SQL API-account in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Verschillen in nomenclatuur

Hieronder volgen de belangrijkste functies die anders werken in Azure Cosmos DB in vergelijking met Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase-server| Account       |
|Emmer           | Database      |
|Emmer           | Container/Verzameling |
|JSON-document    | Item / Document |

## <a name="key-differences"></a>Belangrijke verschillen

* Azure Cosmos DB heeft een veld 'ID' in het document, terwijl Couchbase de ID als onderdeel van de bucket heeft. Het veld 'ID' is uniek in de partitie.

* Azure Cosmos DB schaalt met behulp van de partitionering- of shardingtechniek. Dat betekent dat het de gegevens splitst in meerdere scherven / partities. Deze partities/shards worden gemaakt op basis van de eigenschap partitiesleutel die u opgeeft. U de partitiesleutel selecteren om leesbewerkingen te optimaliseren en ook lees-/schrijfbewerkingen te optimaliseren. Zie het artikel [partitionering](./partition-data.md) voor meer informatie.

* In Azure Cosmos DB is het niet vereist dat de hiërarchie op het hoogste niveau de verzameling aangeeft omdat de verzamelingsnaam al bestaat. Deze functie maakt de JSON-structuur veel eenvoudiger. Het volgende is een voorbeeld dat verschillen in het gegevensmodel tussen Couchbase en Azure Cosmos DB weergeeft:

   **Couchbase**: Document ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Verwijs "ID" in het document zoals hieronder weergegeven

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Ondersteuning voor Java SDK

Azure Cosmos DB heeft volgende SDK's om verschillende Java-frameworks te ondersteunen:

* Async SDK
* Spring Boot SDK

In de volgende secties wordt beschreven wanneer elk van deze SDK's moet worden gebruikt. Neem een voorbeeld waarbij we drie soorten workloads hebben:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase als documentrepository & aangepaste query's op basis van voorjaarsgegevens

Als de werkbelasting die u migreert is gebaseerd op Spring Boot Based SDK, u de volgende stappen gebruiken:

1. Bovenliggende gegevens toevoegen aan het POM.xml-bestand:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Eigenschappen toevoegen aan het POM.xml-bestand:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Voeg afhankelijkheden toe aan het POM.xml-bestand:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Voeg toepassingseigenschappen toe onder resources en geef het volgende op. Zorg ervoor dat u de parameters URL, sleutel en databasenaam vervangt:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definieer de naam van de verzameling in het model. U ook verdere annotaties opgeven. Id, partitiesleutel om ze expliciet aan te duiden:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Hieronder volgen de codefragmenten voor CRUD-bewerkingen:

### <a name="insert-and-update-operations"></a>Bewerkingen invoegen en bijwerken

Waar *_repo* het voorwerp van repository en *doc* is, is het object van de POJO-klasse. U kunt `.save` gebruiken om in te voegen of upsert (als document met opgegeven ID gevonden). In het volgende codefragment ziet u hoe u een documentobject invoegt of bijwerkt:

```_repo.save(doc);```

### <a name="delete-operation"></a>Bewerking verwijderen

Overweeg het volgende codefragment, waarbij het documentobject id- en partitiesleutel verplicht heeft om het object te lokaliseren en te verwijderen:

```_repo.delete(doc);```

### <a name="read-operation"></a>Bewerking lezen

U het document lezen met of zonder de partitiesleutel op te geven. Als u de partitiesleutel niet opgeeft, wordt deze behandeld als een kruispartitiequery. Houd rekening met de volgende codevoorbeelden, de eerste wordt uitgevoerd met id- en partitiesleutelveld. In het tweede voorbeeld wordt een normaal veld & zonder het veld partitiesleutel op te geven.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Dat is het, u nu gebruik maken van uw toepassing met Azure Cosmos DB. Het volledige codevoorbeeld voor het voorbeeld dat in dit document wordt beschreven, is beschikbaar in de [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub repo.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase als documentrepository & n1QL-query's gebruikt

N1QL-query's is de manier om query's in couchbase te definiëren.

|N1QL-query | Azure CosmosDB-query|
|-------------------|-------------------|
|SELECT`TravelDocument`META( ).id `TravelDocument`AS id, .* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xxx.xxx.xxxx " and country = 'India' and ANY m in Visas SATISFIES m.type == ` Validity` 'Multi-Entry' and m.Country IN ['India', Bhutan'] ORDER BY DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

U de volgende wijzigingen in uw N1QL-query's opmerken:

* U hoeft het META-trefwoord niet te gebruiken of te verwijzen naar het document op het eerste niveau. In plaats daarvan u uw eigen verwijzing naar de container maken. In dit voorbeeld hebben we het beschouwd als "c" (het kan van alles zijn). Deze verwijzing wordt gebruikt als voorvoegsel voor alle velden op het eerste niveau. Fr voorbeeld, c.id, c.country etc.

* In plaats van ANY u nu een join op subdocument doen en verwijzen met een speciale alias zoals m. Nadat u alias voor een subdocument hebt gemaakt, moet u alias gebruiken. Bijvoorbeeld, m.Country.

* De volgorde van OFFSET is anders in Azure Cosmos DB-query, eerst moet u OFFSET opgeven en vervolgens LIMIT. Het wordt aanbevolen om Spring Data SDK niet te gebruiken als u maximaal aangepaste gedefinieerde query's gebruikt, omdat deze onnodige overhead aan de clientzijde kan hebben terwijl u de query doorgeeft aan Azure Cosmos DB. In plaats daarvan hebben we een directe Async Java SDK, die in dit geval veel efficiënt kan worden gebruikt.

### <a name="read-operation"></a>Leesbewerking

Gebruik de Async Java SDK met de volgende stappen:

1. Configureer de volgende afhankelijkheid naar het POM.xml-bestand:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Maak een verbindingsobject voor Azure `ConnectionBuilder` Cosmos DB met behulp van de methode zoals weergegeven in het volgende voorbeeld. Zorg ervoor dat u deze verklaring in de boon zodanig dat de volgende code moet slechts eenmaal uitgevoerd:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Als u de query wilt uitvoeren, moet u het volgende codefragment uitvoeren:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Nu, met behulp van bovenstaande methode u meerdere query's doorgeven en uitvoeren zonder gedoe. Als u de vereiste hebt om één grote query uit te voeren, die kan worden opgesplitst in meerdere query's, probeert u vervolgens het volgende codefragment in plaats van het vorige:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Met de vorige code u query's parallel uitvoeren en de gedistribueerde uitvoeringen verhogen om te optimaliseren. Verder u de invoeg- en updatebewerkingen ook uitvoeren:

### <a name="insert-operation"></a>Invoegen, bewerking

Voer de volgende code uit om het document in te voegen:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonneer je dan op Mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsert-bewerking

Voor de bewerking van Upsert moet u het document opgeven dat moet worden bijgewerkt. Als u het volledige document wilt ophalen, u het fragment gebruiken dat onder kopleesbewerking wordt vermeld en vervolgens het vereiste veld(en) wijzigen. Het volgende codefragment upserts het document:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Abonneer je dan op mono. Raadpleeg het mono-abonnementsfragment in de invoegbewerking.

### <a name="delete-operation"></a>Bewerking verwijderen

Following snippet will do delete operation:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Abonneer je vervolgens op mono, verwijs mono-abonnementsfragment in invoegbewerking. Het volledige codevoorbeeld is beschikbaar in de [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub repo.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase als sleutel/waardepaar

Dit is een eenvoudig type werkbelasting waarbij u opzoekingen uitvoeren in plaats van query's. Gebruik de volgende stappen voor sleutel-/waardeparen:

1. Overweeg om /ID als primaire sleutel te hebben, waardoor u de opzoekbewerking direct in de specifieke partitie uitvoeren. Maak een verzameling en geef "/ID" op als partitiesleutel.

1. Schakel de indexering volledig uit. Omdat u opzoekbewerkingen uitvoert, heeft het geen zin om overhead te indexeren. Als u indexering wilt uitschakelen, meldt u zich aan bij Azure Portal, gaat u naar Azure Cosmos DB-account. Open de **Gegevensverkenner,** selecteer uw **database** en de **container**. Open het tabblad **Schalen & instellingen** en selecteer het **indexeringsbeleid**. Momenteel ziet indexeringsbeleid er als volgt uit:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Vervang het bovenstaande indexeringsbeleid door het volgende beleid:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Gebruik het volgende codefragment om het verbindingsobject te maken. Verbindingsobject (in plaatsen @Bean of statisch maken):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Nu u de CRUD-bewerkingen als volgt uitvoeren:

### <a name="read-operation"></a>Leesbewerking

Als u het item wilt lezen, gebruikt u het volgende fragment:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Invoegen, bewerking

Als u een item wilt invoegen, u de volgende code uitvoeren:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Abonneer je dan op mono als:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert-bewerking

Als u de waarde van een item wilt bijwerken, raadpleegt u het onderstaande codefragment:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Abonneer je vervolgens op mono, verwijs mono-abonnementsfragment in invoegbewerking.

### <a name="delete-operation"></a>Bewerking verwijderen

Gebruik het volgende fragment om de verwijderbewerking uit te voeren:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Abonneer je vervolgens op mono, verwijs mono-abonnementsfragment in invoegbewerking. Het volledige codevoorbeeld is beschikbaar in de [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub repo.

## <a name="data-migration"></a>Gegevensmigratie

Er zijn twee manieren om gegevens te migreren.

* **Azure Data Factory gebruiken:** Dit is de meest aanbevolen methode om de gegevens te migreren. Configureer de bron als Couchbase en sink als Azure Cosmos DB SQL API, zie het azure [cosmos DB Data Factory-connectorartikel](../data-factory/connector-azure-cosmos-db.md) voor gedetailleerde stappen.

* Gebruik het hulpprogramma voor **het importeren van Azure Cosmos DB-gegevens:** Deze optie wordt aanbevolen om te migreren met VM's met minder hoeveelheid gegevens. Zie het artikel [Gegevensimporteur](./import-data.md) voor gedetailleerde stappen.

## <a name="next-steps"></a>Volgende stappen

* Zie [Prestatie- en schaaltests uitvoeren met het azure cosmos DB-artikel](./performance-testing.md) voor het uitvoeren van prestatietests.
* Zie [Prestatietips voor het](./performance-tips-async-java.md) artikel Azure Cosmos DB om de code te optimaliseren.
* Ontdek Java Async V3 SDK, [SDK referentie](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub repo.
