---
title: De API voor MongoDB (3.6-versie) van Azure Cosmos DB ondersteunde functies en syntaxis
description: Meer informatie over de API voor MongoDB (3.6-versie) van Azure Cosmos DB ondersteunt functies en syntaxis.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: fe80b7343f67b06981707081931e35142985688e
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668782"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>De API van Azure Cosmos DB voor MongoDB (versie 3.6): ondersteunde functies en syntaxis

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U communiceren met de API van Azure Cosmos DB voor MongoDB met [drivers](https://docs.mongodb.org/ecosystem/drivers)behulp van een van de open-source MongoDB-clientstuurprogramma's. De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande clientstuurprogramma's mogelijk doordat de API functioneert conform het MongoDB-[wireprotocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Door de API van Azure Cosmos DB voor MongoDB te gebruiken, u genieten van de voordelen van de MongoDB die u gewend bent, met alle bedrijfsmogelijkheden die Cosmos DB biedt: [wereldwijde distributie,](distribute-data-globally.md) [automatische sharding,](partition-data.md)beschikbaarheids- en latentiegaranties, versleuteling in rust, back-ups en nog veel meer.

## <a name="protocol-support"></a>Ondersteuning voor protocol

De API van Azure Cosmos DB voor MongoDB is standaard compatibel met MongoDB-serverversie **3.6** voor nieuwe accounts. De ondersteunde operators en eventuele beperkingen of uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met de API van Azure Cosmos DB voor MongoDB. Houd er rekening mee dat bij het gebruik van de API van Azure Cosmos DB voor `*.mongo.cosmos.azure.com` MongoDB-accounts de 3.6-versie `*.documents.azure.com`van accounts het eindpunt in de indeling heeft, terwijl de 3.2-versie van accounts het eindpunt in de indeling heeft.

## <a name="query-language-support"></a>Ondersteuning voor querytaal

De API van Azure Cosmos DB voor MongoDB biedt uitgebreide ondersteuning voor MongoDB-querytaalconstructs. Hieronder vindt u de gedetailleerde lijst met momenteel ondersteunde bewerkingen, operators, fasen, opdrachten en opties.

## <a name="database-commands"></a>Databaseopdrachten

De API van Azure Cosmos DB voor MongoDB biedt ondersteuning voor de volgende databaseopdrachten:

### <a name="query-and-write-operation-commands"></a>Opdrachten voor query- en schrijfbewerkingen

|Opdracht  |Ondersteund |
|---------|---------|
|delete | Ja |
|find | Ja     |
|findAndModify | Ja  |
|getLastError|   Ja |
|getMore  |  Ja  |
|getPrevError | Nee  |
|insert  |   Ja  |
|parallelCollectionScan  | Ja   |
|resetFout |    Nee  |
|update  |   Ja  |
|[Stromen wijzigen](mongodb-change-streams.md)  |  Ja  |
|GridfS (Gridfs) |   Ja  |

### <a name="authentication-commands"></a>Verificatieopdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|authenticate    |   Ja      |
|logout    |      Ja   |
|getnonce   |    Ja     |


### <a name="administration-commands"></a>Beheeropdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|Afgetopte verzamelingen   |   Nee      |
|cloneCollectionAsCapped cloneCollectionAsCapped cloneCollectionAsCapped cloneCollection     |   Nee      |
|collMod collMod     |   Nee      |
|collMod: expireAfterSeconds   |   Nee      |
|convertToCapped convertToCapped   |  Nee       |
|copydb copydb     |  Nee       |
|maken   |    Ja     |
|createIndexes     |  Ja       |
|currentOp     |  Ja       |
|drop     |   Ja      |
|dropDatabase     |  Ja       |
|dropIndexes     |   Ja      |
|filemd5    |   Ja      |
|killCursors    |  Ja       |
|killOp     |   Nee      |
|listCollections     |  Ja       |
|listDatabases     |  Ja       |
|listIndexes     |  Ja       |
|reIndex     |    Ja     |
|naamvancollectie wijzigen     |    Nee     |
|connectionStatus    |     Nee    |

### <a name="diagnostics-commands"></a>Diagnostics commands

|Opdracht  |Ondersteund |
|---------|---------|
|buildInfo         |   Ja      |
|collStats    |  Ja       |
|connPoolStats connPoolStats     |  Nee       |
|connectionStatus     |  Nee       |
|gegevensGrootte     |   Nee      |
|dbHash (dbHash)    |    Nee     |
|dbStats     |   Ja      |
|Uitleggen     | Nee        |
|uitleggen: executionStats     |     Nee    |
|functies     |    Nee     |
|hostInfo     |   Nee      |
|listDatabases         |   Ja      |
|lijstOpdrachten     |  Nee       |
|Profiler     |  Nee       |
|serverStatus     |  Nee       |
|top     |    Nee     |
|whatsmyuri     |   Ja      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Samenvoegingspijplijn</a>

### <a name="aggregation-commands"></a>Samenvoegingsopdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|aggregate |   Ja  |
|count     |   Ja  |
|distinct  | Ja |
|kaart Verminderen | Nee |

### <a name="aggregation-stages"></a>Samenvoegingsfasen

|Opdracht  |Ondersteund |
|---------|---------|
|$collStats    |Nee|
|$project    |Ja|
|$match    |Ja|
|$redact|    Ja|
|$limit    |Ja|
|$skip    |Ja|
|$unwind|    Ja|
|$group    |    Ja|
|$sample|        Ja|
|$sort    |Ja|
|$geoNear|    Nee|
|$lookup    |    Ja|
|$out        |Ja|
|$indexStats|        Nee|
|$facet    |Nee|
|$bucket|    Nee|
|$bucketAuto|    Nee|
|$sortByCount|    Ja|
|$addFields    |Ja|
|$replaceRoot|    Ja|
|$count    |Ja|
|$currentOp|    Nee|
|$listLocalSessions    |Nee|
|$listSessions    |Nee|
|$graphLookup    |Nee|

### <a name="boolean-expressions"></a>Booleaanse expressies

|Opdracht  |Ondersteund |
|---------|---------|
|$and| Ja|
|$or|Ja|
|$not|Ja|

### <a name="set-expressions"></a>Expressies voor instellen

|Opdracht  |Ondersteund |
|---------|---------|
| $setEquals | Ja|
|$setIntersection|Ja|
| $setUnion|Ja|
| $setDifference|Ja|
| $setIsSubset|Ja|
| $anyElementTrue|Ja|
| $allElementsTrue|Ja|

### <a name="comparison-expressions"></a>Expressies voor vergelijken

|Opdracht  |Ondersteund |
|---------|---------|
|$cmp     |  Ja       |
|$eq|    Ja| 
|$gt |    Ja| 
|$gte|    Ja| 
|$lt    |Ja|
|$lte|    Ja| 
|$ne    |    Ja| 
|$in    |    Ja| 
|$nin    |    Ja| 

### <a name="arithmetic-expressions"></a>Rekenkundige expressies

|Opdracht  |Ondersteund |
|---------|---------|
|$abs |  Ja       |
| $add |  Ja       |
| $ceil |  Ja       |
| $divide |  Ja       |
| $exp |  Ja       |
| $floor |  Ja       |
| $ln |  Ja       |
| $log |  Ja       |
| $log10 |  Ja       |
| $mod |  Ja       |
| $multiply |  Ja       |
| $pow |  Ja       |
| $sqrt |  Ja       |
| $subtract |  Ja       |
| $trunc |  Ja       |

### <a name="string-expressions"></a>Tekenreeksexpressies

|Opdracht  |Ondersteund |
|---------|---------|
|$concat |  Ja       |
| $indexOfBytes|  Ja       |
| $indexOfCP|  Ja       |
| $split|  Ja       |
| $strLenBytes|  Ja       |
| $strLenCP|  Ja       |
| $strcasecmp|  Ja       |
| $substr|  Ja       |
| $substrBytes|  Ja       |
| $substrCP|  Ja       |
| $toLower|  Ja       |
| $toUpper|  Ja       |

### <a name="text-search-operator"></a>Besturingselement voor tekstzoeken

|Opdracht  |Ondersteund |
|---------|---------|
| $meta | Nee|

### <a name="array-expressions"></a>Matrixexpressies

|Opdracht  |Ondersteund |
|---------|---------|
|$arrayElemAt    |    Ja|
|$arrayToObject|    Ja|
|$concatArrays    |    Ja|
|$filter    |    Ja|
|$indexOfArray    |Ja|
|$isArray    |    Ja|
|$objectToArray    |Ja|
|$range    |Ja|
|$reverseArray    |    Ja|
|$reduce|    Ja|
|$size    |    Ja|
|$slice    |    Ja|
|$zip    |    Ja|
|$in    |    Ja|

### <a name="variable-operators"></a>Variabele operatoren

|Opdracht  |Ondersteund |
|---------|---------|
|$map    |Nee|
|$let    |Ja|

### <a name="system-variables"></a>Systeemvariabelen

|Opdracht  |Ondersteund |
|---------|---------|
|$$CURRENT|    Ja|
|$$DESCEND|        Ja|
|$$KEEP        |Ja|
|$$PRUNE    |    Ja|
|$$REMOVE    |Ja|
|$$ROOT        |Ja|

### <a name="literal-operator"></a>Letterlijke operator

|Opdracht  |Ondersteund |
|---------|---------|
|$literal    |Ja|

### <a name="date-expressions"></a>Datumexpressies

|Opdracht  |Ondersteund |
|---------|---------|
|$dayOfYear    |Ja    |
|$dayOfMonth|    Ja    |
|$dayOfWeek    |Ja    |
|$year    |Ja    |
|$month    |Ja|    
|$week    |Ja    |
|$hour    |Ja    |
|$minute|    Ja|    
|$second    |Ja    |
|$millisecond|    Ja|    
|$dateToString    |Ja    |
|$isoDayOfWeek    |Ja    |
|$isoWeek    |Ja    |
|$dateFromParts|    Nee|    
|$dateToParts    |Nee    |
|$dateFromString|    Nee|
|$isoWeekYear    |Ja    |

### <a name="conditional-expressions"></a>Voorwaardelijke expressies

|Opdracht  |Ondersteund |
|---------|---------|
| $cond| Ja|
| $ifNull| Ja|
| $switch |Ja|

### <a name="data-type-operator"></a>Gegevenstypeoperator

|Opdracht  |Ondersteund |
|---------|---------|
| $type| Ja|

### <a name="accumulator-expressions"></a>Accumulatorexpressieen

|Opdracht  |Ondersteund |
|---------|---------|
|$sum    |Ja    |
|$avg    |Ja    |
|$first|    Ja|
|$last    |Ja    |
|$max    |Ja    |
|$min    |Ja    |
|$push|    Ja|
|$addToSet|    Ja|
|$stdDevPop|    Nee    |
|$stdDevSamp|    Nee|

### <a name="merge-operator"></a>Operator samenvoegen

|Opdracht  |Ondersteund |
|---------|---------|
| $mergeObjects | Ja|

## <a name="data-types"></a>Gegevenstypen

|Opdracht  |Ondersteund |
|---------|---------|
|Double    |Ja    |
|Tekenreeks    |Ja    |
|Object    |Ja    |
|Matrix    |Ja    |
|Binaire gegevens    |Ja|    
|ObjectId    |Ja    |
|Booleaans    |Ja    |
|Date    |Ja    |
|Null    |Ja    |
|32-bits geheel getal (int)    |Ja    |
|Tijdstempel    |Ja    |
|64-bits geheel getal (lang)    |Ja    |
|MinKey MinKey    |Ja    |
|MaxKey (MaxKey)    |Ja    |
|Decimal128    |Ja|    
|Reguliere expressie    |Ja|
|Javascript    |Ja|
|JavaScript (met bereik)|    Ja    |
|Undefined    |Ja    |

## <a name="indexes-and-index-properties"></a>Indexen en indexeigenschappen

### <a name="indexes"></a>Indexen

|Opdracht  |Ondersteund |
|---------|---------|
|Index met één veld    |Ja    |
|Samengestelde index    |Ja    |
|Multikey-index    |Ja    |
|Tekstindex    |Nee|
|2dsfeer    |Ja    |
|2d-index    |Nee    |
|Gehashte index    | Ja|

### <a name="index-properties"></a>Indexeigenschappen

|Opdracht  |Ondersteund |
|---------|---------|
|TTL|    Ja    |
|Uniek    |Ja|
|Gedeeltelijk|    Nee|
|Geval Ongevoelig    |Nee|
|Sparse    |Nee |
|Achtergrond|    Ja |

## <a name="operators"></a>Operators

### <a name="logical-operators"></a>Logische operators

|Opdracht  |Ondersteund |
|---------|---------|
|$or    |    Ja|
|$and    |    Ja|
|$not    |    Ja|
|$nor    |    Ja| 

### <a name="element-operators"></a>Elementenoperatoren

|Opdracht  |Ondersteund |
|---------|---------|
|$exists|    Ja|
|$type    |    Ja|

### <a name="evaluation-query-operators"></a>Query-operatoren voor evaluatie

|Opdracht  |Ondersteund |
|---------|---------|
|$expr    |    Nee|
|$jsonSchema    |    Nee|
|$mod    |    Ja|
|$regex |    Ja|
|$text    | Nee (Niet ondersteund. Gebruik $regex in plaats daarvan.)| 
|$where    |Nee| 

In de $regex query's staan links verankerde expressies indexzoeken toe. Als u echter de aanpassingsfuncties i (geen hoofdlettergevoeligheid) en m (meerdere regels) gebruikt, wordt de verzameling gescand in alle expressies.

Wanneer $ of | moet worden opgenomen, kunt u het beste twee (of meer) regex-query’s maken. Bijvoorbeeld, gezien de volgende ```find({x:{$regex: /^abc$/})```oorspronkelijke query: , het moet worden gewijzigd als volgt:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Het eerste deel maakt gebruik van de index om alleen te zoeken naar de documenten die beginnen met ^abc en het tweede deel zoekt naar een overeenkomst tussen de exacte vermeldingen. De streepoperator | fungeert als de functie: or. De query ```find({x:{$regex: /^abc|^def/})``` komt overeen met de documenten waarin het veld x waarden heeft die beginnen met abc of def. Als u de index wilt gebruiken, kunt u de query het beste opsplitsen in twee verschillende query’s die zijn verbonden met de operator $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Arrayoperatoren

|Opdracht  |Ondersteund | 
|---------|---------|
| $all | Ja| 
| $elemMatch | Ja| 
| $size | Ja | 

### <a name="comment-operator"></a>Operator voor opmerkingen

|Opdracht  |Ondersteund | 
|---------|---------|
$comment |Ja| 

### <a name="projection-operators"></a>Projectieoperatoren

|Opdracht  |Ondersteund |
|---------|---------|
|$elemMatch    |Ja|
|$meta|    Nee|
|$slice    | Ja|

### <a name="update-operators"></a>Operators voor updates

#### <a name="field-update-operators"></a>Operators voor veldupdates

|Opdracht  |Ondersteund |
|---------|---------|
|$inc    |    Ja|
|$mul    |    Ja|
|$rename    |    Ja|
|$setOnInsert|    Ja|
|$set    |Ja|
|$unset| Ja|
|$min    |Ja|
|$max    |Ja|
|$currentDate    | Ja|

#### <a name="array-update-operators"></a>Operators voor matrixupdates

|Opdracht  |Ondersteund |
|---------|---------|
|$    |Ja|
|$[]|    Ja|
|$[<identifier>]|    Ja|
|$addToSet    |Ja|
|$pop    |Ja|
|$pullAll|    Ja|
|$pull    |Ja|
|$push    |Ja|
|$pushAll| Ja|


#### <a name="update-modifiers"></a>Modifiers bijwerken

|Opdracht  |Ondersteund |
|---------|---------|
|$each    |    Ja|
|$slice    |Ja|
|$sort    |Ja|
|$position    |Ja|

#### <a name="bitwise-update-operator"></a>Operators voor bitwise-updates

|Opdracht  |Ondersteund |
|---------|---------|
| $bit    |    Ja|    
|$bitsAllSet    |    Nee|
|$bitsAnySet    |    Nee|
|$bitsAllClear    |Nee|
|$bitsAnyClear    |Nee|

### <a name="geospatial-operators"></a>Georuimtelijke operators

Operator | Ondersteund| 
--- | --- |
$geoWithin | Ja |
$geoIntersects | Ja | 
$near |  Ja |
$nearSphere |  Ja |
$geometry |  Ja |
$minDistance | Ja |
$maxDistance | Ja |
$center | Ja |
$centerSphere | Ja |
$box | Ja |
$polygon |  Ja |

## <a name="cursor-methods"></a>Cursormethoden

|Opdracht  |Ondersteund |
|---------|---------|
|cursor.batchSize()    |    Ja|
|cursor.close()    |Ja|
|cursor.isGesloten()|        Ja|
|cursor.collation()|    Nee|
|cursor.comment()    |Ja|
|cursor.count()    |Ja|
|cursor.explain()|    Nee|
|cursor.forEach()    |Ja|
|cursor.hasNext()    |Ja|
|cursor.hint()    |Ja|
|cursor.isExhausted()|    Ja|
|cursor.itcount()    |Ja|
|cursor.limit()    |Ja|
|cursor.map()    |Ja|
|cursor.maxScan()    |Ja|
|cursor.maxTimeMS()|    Ja|
|cursor.max()    |Ja|
|cursor.min()    |Ja|
|cursor.volgende()|    Ja|
|cursor.noCursorTimeout()    |Nee|
|cursor.objsLeftInBatch()    |Ja|
|cursor.pretty()|    Ja|
|cursor.readConcern()|    Ja|
|cursor.readPref()        |Ja|
|cursor.returnKey()    |Nee|
|cursor.showRecordId()|    Nee|
|cursor.size()    |Ja|
|cursor.overslaan()    |Ja|
|cursor.sort()    |    Ja|
|cursor.tailable()|    Nee|
|cursor.toArray()    |Ja|

## <a name="sort-operations"></a>Bewerkingen sorteren

Tijdens het gebruik van bewerking `findOneAndUpdate`, worden sorteerbewerkingen op één veld ondersteund, maar sorteerbewerkingen op meerdere velden worden niet ondersteund.

## <a name="unique-indexes"></a>Unieke indexen

Unieke indexen zorgen ervoor dat een specifiek veld geen dubbele waarden heeft voor alle documenten in een verzameling, vergelijkbaar met de manier waarop uniciteit wordt bewaard op de standaardsleutel '_id'. U aangepaste indexen maken in Cosmos DB met de opdracht createIndex, inclusief de 'unieke' beperking.

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Cosmos DB biedt ondersteuning voor een TTL (time-to-live) op basis van de tijdstempel van het document. TTL kan worden ingeschakeld voor verzamelingen door naar [Azure Portal](https://portal.azure.com) te gaan.

## <a name="user-and-role-management"></a>Gebruikers- en rolbeheer

Cosmos DB biedt nog geen ondersteuning voor gebruikers en rollen. Cosmos DB ondersteunt echter op rollen gebaseerdtoegangscontrole (RBAC) en alleen-lezen en alleen-lezen wachtwoorden/sleutels die kunnen worden verkregen via de [Azure-portal](https://portal.azure.com) (pagina Verbindingstekenreeks).

## <a name="replication"></a>Replicatie

Cosmos DB biedt ondersteuning voor automatische, systeemeigen replicatie op de laagste lagen. Deze logica is uitgebreid om tevens globale replicatie met een lage latentie te bereiken. Cosmos DB biedt geen ondersteuning voor handmatige replicatieopdrachten.

## <a name="write-concern"></a>Schrijfprobleem

Bepaalde toepassingen vertrouwen op een [schrijfprobleem](https://docs.mongodb.com/manual/reference/write-concern/) dat het aantal vereiste reacties tijdens een schrijfbewerking opgeeft. Vanwege de manier waarop replicatie op de achtergrond in Cosmos DB wordt afgehandeld, zijn alle schrijfbewerkingen automatisch Quorum-bewerkingen. Alle schrijfproblemen die in clientcode zijn opgegeven, worden genegeerd. Zie [Consistentieniveaus gebruiken om de beschikbaarheid en prestaties te maximaliseren](consistency-levels.md) voor meer informatie.

## <a name="sharding"></a>Sharding

Azure Cosmos DB biedt ondersteuning voor automatische sharding aan serverzijde. Het beheert shard creatie, plaatsing en balanceren automatisch. Azure Cosmos DB ondersteunt geen handmatige shardingsopdrachten, wat betekent dat u geen opdrachten hoeft aan te roepen zoals addShard, balancerStart, moveChunk enz. U hoeft alleen de shardtoets op te geven tijdens het maken van de containers of het opvragen van de gegevens.

## <a name="sessions"></a>Sessies

Azure Cosmos DB ondersteunt nog geen opdrachten voor serversessies.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie check [Mongo 3.6 versie functies](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

<sup>Opmerking: In dit artikel wordt een functie van Azure Cosmos DB beschreven die compatibiliteit met het protocol voor draad met MongoDB-databases biedt. Microsoft voert geen MongoDB-databases uit om deze service te bieden. Azure Cosmos DB is niet aangesloten bij MongoDB, Inc.</sup>
