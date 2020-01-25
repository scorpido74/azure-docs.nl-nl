---
title: Azure Cosmos DB-API voor MongoDB (3,6-versie) ondersteunde functies en syntaxis
description: Meer informatie over de Azure Cosmos DB-API voor MongoDB (3,6-versie) ondersteunde functies en syntaxis.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: a32affab45ab99a89113644bb08c4f2b57d69018
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721010"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB-API voor MongoDB (3,6-versie): ondersteunde functies en syntaxis

Azure Cosmos DB is de wereldwijd gedistribueerde multimodel-databaseservice van Microsoft. U kunt met behulp van een van de open-source MongoDB-client [Stuur Programma's](https://docs.mongodb.org/ecosystem/drivers)communiceren met de API van Azure Cosmos DB voor MongoDb. De API van Azure Cosmos DB voor MongoDB maakt het gebruik van bestaande clientstuurprogramma's mogelijk doordat de API functioneert conform het MongoDB-[wireprotocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Door gebruik te maken van de API van Azure Cosmos DB voor MongoDB hebt u de beschikking over de voordelen van de vertrouwde MongoDB, met alle zakelijke mogelijkheden die Cosmos DB biedt: [wereldwijde distributie](distribute-data-globally.md), [automatische sharding](partition-data.md), garanties voor beschikbaarheid en latentie, automatisch indexeren van alle velden, versleuteling van niet-actieve gegevens, het maken van back-ups, en meer.

## <a name="protocol-support"></a>Ondersteuning voor protocol

De API van de Azure Cosmos DB voor MongoDB is standaard compatibel met MongoDB Server versie **3,6** voor nieuwe accounts. De ondersteunde operators en eventuele beperkingen of uitzonderingen worden hieronder vermeld. Elk clientstuurprogramma dat deze protocollen kent, kan verbinding maken met de API van Azure Cosmos DB voor MongoDB. Houd er rekening mee dat wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts, de 3,6-versie van accounts het eind punt in de indeling heeft `*.mongo.cosmos.azure.com`, terwijl de 3,2-versie van accounts het eind punt in de indeling `*.documents.azure.com`heeft.

## <a name="query-language-support"></a>Ondersteuning voor querytaal

De API van Azure Cosmos DB voor MongoDB biedt uitgebreide ondersteuning voor MongoDB-querytaalconstructs. Hieronder vindt u een overzicht van de huidige ondersteunde bewerkingen, Opera Tors, fasen, opdrachten en opties.

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
|resetError |   Nee  |
|update  |   Ja  |
|[Stroom wijzigen](mongodb-change-streams.md)  |  Ja  |
|GridFS |   Ja  |

### <a name="authentication-commands"></a>Verificatieopdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|authenticate    |   Ja      |
|afmelden    |      Ja   |
|getnonce   |    Ja     |


### <a name="administration-commands"></a>Beheeropdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|Gelimiteerde verzamelingen   |   Nee      |
|cloneCollectionAsCapped     |   Nee      |
|collMod     |   Nee      |
|collMod: expireAfterSeconds   |   Nee      |
|convertToCapped   |  Nee       |
|copydb     |  Nee       |
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
|renameCollection     |    Nee     |
|connectionStatus    |     Nee    |

### <a name="diagnostics-commands"></a>Diagnostics commands

|Opdracht  |Ondersteund |
|---------|---------|
|buildInfo       |   Ja      |
|collStats    |  Ja       |
|connPoolStats     |  Nee       |
|connectionStatus     |  Nee       |
|dataSize     |   Nee      |
|dbHash    |    Nee     |
|dbStats     |   Ja      |
|bespreken     | Nee        |
|uitleg: executionStats     |     Nee    |
|database     |    Nee     |
|hostInfo     |   Nee      |
|listDatabases       |   Ja      |
|listCommands     |  Nee       |
|Profiler     |  Nee       |
|serverStatus     |  Nee       |
|Boven     |    Nee     |
|whatsmyuri     |   Ja      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Samenvoegingspijplijn</a>

### <a name="aggregation-commands"></a>Samenvoegingsopdrachten

|Opdracht  |Ondersteund |
|---------|---------|
|aggregate |   Ja  |
|count     |   Ja  |
|distinct  | Ja |
|mapReduce | Nee |

### <a name="aggregation-stages"></a>Samenvoegingsfasen

|Opdracht  |Ondersteund |
|---------|---------|
|$collStats |Nee|
|$project   |Ja|
|$match |Ja|
|$redact|   Ja|
|$limit |Ja|
|$skip  |Ja|
|$unwind|   Ja|
|$group |   Ja|
|$sample|       Ja|
|$sort  |Ja|
|$geoNear|  Nee|
|$lookup    |   Ja|
|$out       |Ja|
|$indexStats|       Nee|
|$facet |Nee|
|$bucket|   Nee|
|$bucketAuto|   Nee|
|$sortByCount|  Ja|
|$addFields |Ja|
|$replaceRoot|  Ja|
|$count |Ja|
|$currentOp|    Nee|
|$listLocalSessions |Nee|
|$listSessions  |Nee|
|$graphLookup   |Nee|

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
|$eq|   Ja| 
|$gt |  Ja| 
|$gte|  Ja| 
|$lt    |Ja|
|$lte|  Ja| 
|$ne    |   Ja| 
|$in    |   Ja| 
|$nin   |   Ja| 

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

### <a name="text-search-operator"></a>Zoek operator voor tekst

|Opdracht  |Ondersteund |
|---------|---------|
| $meta | Nee|

### <a name="array-expressions"></a>Matrixexpressies

|Opdracht  |Ondersteund |
|---------|---------|
|$arrayElemAt   |   Ja|
|$arrayToObject|    Ja|
|$concatArrays  |   Ja|
|$filter    |   Ja|
|$indexOfArray  |Ja|
|$isArray   |   Ja|
|$objectToArray |Ja|
|$range |Ja|
|$reverseArray  |   Ja|
|$reduce|   Ja|
|$size  |   Ja|
|$slice |   Ja|
|$zip   |   Ja|
|$in    |   Ja|

### <a name="variable-operators"></a>Variabelen operators

|Opdracht  |Ondersteund |
|---------|---------|
|$map   |Nee|
|$let   |Ja|

### <a name="system-variables"></a>Systeemvariabelen

|Opdracht  |Ondersteund |
|---------|---------|
|$ $CURRENT| Ja|
|$ $DESCEND|     Ja|
|$ $KEEP     |Ja|
|$ $PRUNE    |   Ja|
|$ $REMOVE   |Ja|
|$ $ROOT     |Ja|

### <a name="literal-operator"></a>Letterlijke operator

|Opdracht  |Ondersteund |
|---------|---------|
|$literal   |Ja|

### <a name="date-expressions"></a>Datumexpressies

|Opdracht  |Ondersteund |
|---------|---------|
|$dayOfYear |Ja    |
|$dayOfMonth|   Ja |
|$dayOfWeek |Ja    |
|$year  |Ja    |
|$month |Ja|   
|$week  |Ja    |
|$hour  |Ja    |
|$minute|   Ja|    
|$second    |Ja    |
|$millisecond|  Ja|    
|$dateToString  |Ja    |
|$isoDayOfWeek  |Ja    |
|$isoWeek   |Ja    |
|$dateFromParts|    Nee| 
|$dateToParts   |Nee |
|$dateFromString|   Nee|
|$isoWeekYear   |Ja    |

### <a name="conditional-expressions"></a>Voorwaardelijke expressies

|Opdracht  |Ondersteund |
|---------|---------|
| $cond| Ja|
| $ifNull| Ja|
| $switch |Ja|

### <a name="data-type-operator"></a>Gegevens type operator

|Opdracht  |Ondersteund |
|---------|---------|
| $type| Ja|

### <a name="accumulator-expressions"></a>Accumulator expressies

|Opdracht  |Ondersteund |
|---------|---------|
|$sum   |Ja    |
|$avg   |Ja    |
|$first|    Ja|
|$last  |Ja    |
|$max   |Ja    |
|$min   |Ja    |
|$push| Ja|
|$addToSet| Ja|
|$stdDevPop|    Nee  |
|$stdDevSamp|   Nee|

### <a name="merge-operator"></a>Operator samen voegen

|Opdracht  |Ondersteund |
|---------|---------|
| $mergeObjects | Ja|

## <a name="data-types"></a>Gegevenstypen

|Opdracht  |Ondersteund |
|---------|---------|
|Double |Ja    |
|Tekenreeks |Ja    |
|Object |Ja    |
|Matrix  |Ja    |
|Binary Data    |Ja|   
|Id   |Ja    |
|Booleaans    |Ja    |
|Datum   |Ja    |
|Null   |Ja    |
|32-bits geheel getal (int)   |Ja    |
|Tijdstempel  |Ja    |
|64-bits geheel getal (lang)  |Ja    |
|MinKey |Ja    |
|MaxKey |Ja    |
|Decimal128 |Ja|   
|Reguliere expressie |Ja|
|JavaScript |Ja|
|Java script (met bereik)|   Ja |
|Niet gedefinieerd  |Ja    |

## <a name="indexes-and-index-properties"></a>Indexen en index eigenschappen

### <a name="indexes"></a>Indexen

|Opdracht  |Ondersteund |
|---------|---------|
|Index met één veld |Ja    |
|Samengestelde index |Ja    |
|MultiKey-index |Ja    |
|Tekst index |Nee|
|2dsphere   |Ja    |
|2D-index   |Nee |
|Gehashte index   | Ja|

### <a name="index-properties"></a>Eigenschappen van index

|Opdracht  |Ondersteund |
|---------|---------|
|TTL|   Ja |
|Uniek |Ja|
|Gedeeltelijk|   Nee|
|Niet hoofdletter gevoelig   |Nee|
|Sparse |Nee |
|Achtergrond|    Ja |

## <a name="operators"></a>Operators

### <a name="logical-operators"></a>Logische operators

|Opdracht  |Ondersteund |
|---------|---------|
|$or    |   Ja|
|$and   |   Ja|
|$not   |   Ja|
|$nor   |   Ja| 

### <a name="element-operators"></a>Element operators

|Opdracht  |Ondersteund |
|---------|---------|
|$exists|   Ja|
|$type  |   Ja|

### <a name="evaluation-query-operators"></a>Opera tors voor evaluatie query's

|Opdracht  |Ondersteund |
|---------|---------|
|$expr  |   Nee|
|$jsonSchema    |   Nee|
|$mod   |   Ja|
|$regex |   Ja|
|$text  | Nee (niet ondersteund. Gebruik in plaats daarvan $regex.)| 
|$where |Nee| 

In de $regex query's kunnen links verankerde expressies index zoeken toestaan. Als u echter de aanpassingsfuncties i (geen hoofdlettergevoeligheid) en m (meerdere regels) gebruikt, wordt de verzameling gescand in alle expressies.

Wanneer $ of | moet worden opgenomen, kunt u het beste twee (of meer) regex-query’s maken. Als u bijvoorbeeld de volgende oorspronkelijke query hebt opgegeven: ```find({x:{$regex: /^abc$/})```, moet deze als volgt worden gewijzigd:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Het eerste deel maakt gebruik van de index om alleen te zoeken naar de documenten die beginnen met ^abc en het tweede deel zoekt naar een overeenkomst tussen de exacte vermeldingen. De streepoperator | fungeert als de functie: or. De query ```find({x:{$regex: /^abc|^def/})``` komt overeen met de documenten waarin het veld x waarden heeft die beginnen met abc of def. Als u de index wilt gebruiken, kunt u de query het beste opsplitsen in twee verschillende query’s die zijn verbonden met de operator $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Matrix operators

|Opdracht  |Ondersteund | 
|---------|---------|
| $all | Ja| 
| $elemMatch | Ja| 
| $size | Ja | 

### <a name="comment-operator"></a>Operator voor opmerkingen

|Opdracht  |Ondersteund | 
|---------|---------|
$comment |Ja| 

### <a name="projection-operators"></a>Projectie operators

|Opdracht  |Ondersteund |
|---------|---------|
|$elemMatch |Ja|
|$meta| Nee|
|$slice | Ja|

### <a name="update-operators"></a>Operators voor updates

#### <a name="field-update-operators"></a>Operators voor veldupdates

|Opdracht  |Ondersteund |
|---------|---------|
|$inc   |   Ja|
|$mul   |   Ja|
|$rename    |   Ja|
|$setOnInsert|  Ja|
|$set   |Ja|
|$unset| Ja|
|$min   |Ja|
|$max   |Ja|
|$currentDate   | Ja|

#### <a name="array-update-operators"></a>Operators voor matrixupdates

|Opdracht  |Ondersteund |
|---------|---------|
|$  |Ja|
|$[]|   Ja|
|$ [<identifier>]|   Ja|
|$addToSet  |Ja|
|$pop   |Ja|
|$pullAll|  Ja|
|$pull  |Ja|
|$push  |Ja|
|$pushAll| Ja|


#### <a name="update-modifiers"></a>Bijwerk opties

|Opdracht  |Ondersteund |
|---------|---------|
|$each  |   Ja|
|$slice |Ja|
|$sort  |Ja|
|$position  |Ja|

#### <a name="bitwise-update-operator"></a>Operators voor bitwise-updates

|Opdracht  |Ondersteund |
|---------|---------|
| $bit  |   Ja|    
|$bitsAllSet    |   Nee|
|$bitsAnySet    |   Nee|
|$bitsAllClear  |Nee|
|$bitsAnyClear  |Nee|

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
|cursor. batchSize () |   Ja|
|cursor. Close () |Ja|
|cursor. isClosed ()|     Ja|
|cursor. Collation ()|    Nee|
|cursor. comment ()   |Ja|
|cursor. Count () |Ja|
|cursor. uitleg ()|  Nee|
|cursor. forEach ()   |Ja|
|cursor. hasNext ()   |Ja|
|cursor. Hint ()  |Ja|
|cursor. isExhausted ()|  Ja|
|cursor. itcount ()   |Ja|
|cursor. Limit () |Ja|
|cursor. map ()   |Ja|
|cursor. maxScan ()   |Ja|
|cursor. maxTimeMS ()|    Ja|
|cursor. Max ()   |Ja|
|cursor. min ()   |Ja|
|cursor. volgende ()| Ja|
|cursor. noCursorTimeout ()   |Nee|
|cursor. objsLeftInBatch ()   |Ja|
|cursor. pretty ()|   Ja|
|cursor. readConcern ()|  Ja|
|cursor. readPref ()      |Ja|
|cursor. reklare () |Nee|
|cursor. showRecordId ()| Nee|
|cursor. size ()  |Nes|
|cursor. Skip ()  |Ja|
|cursor.sort()  |   Ja|
|cursor. staart ()| Nee|
|cursor. toArray ()   |Ja|

## <a name="sort-operations"></a>Sorteer bewerkingen

Tijdens het gebruik van bewerking `findOneAndUpdate`, worden sorteerbewerkingen op één veld ondersteund, maar sorteerbewerkingen op meerdere velden worden niet ondersteund.

## <a name="unique-indexes"></a>Unieke indexen

Unieke indexen zorgen ervoor dat een specifiek veld geen dubbele waarden bevat in alle documenten van een verzameling, net zoals de uniekheid van de standaard-id-sleutel behouden blijft. U kunt met behulp van de opdracht createIndex aangepaste indexen maken in Cosmos DB, met inbegrip van de uniekheidsbeperking.

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Cosmos DB biedt ondersteuning voor een TTL (time-to-live) op basis van de tijdstempel van het document. TTL kan worden ingeschakeld voor verzamelingen door naar [Azure Portal](https://portal.azure.com) te gaan.

## <a name="user-and-role-management"></a>Gebruikers- en rolbeheer

Cosmos DB biedt nog geen ondersteuning voor gebruikers en rollen. Cosmos DB ondersteunt echter op rollen gebaseerd toegangs beheer (RBAC) en alleen-lezen en alleen-lezen wacht woorden/sleutels die kunnen worden verkregen via de [Azure Portal](https://portal.azure.com) (pagina verbindings reeks).

## <a name="replication"></a>Replicatie

Cosmos DB biedt ondersteuning voor automatische, systeemeigen replicatie op de laagste lagen. Deze logica is uitgebreid om tevens globale replicatie met een lage latentie te bereiken. Cosmos DB biedt geen ondersteuning voor handmatige replicatieopdrachten.

## <a name="write-concern"></a>Schrijfprobleem

Bepaalde toepassingen vertrouwen op een [schrijfprobleem](https://docs.mongodb.com/manual/reference/write-concern/) dat het aantal vereiste reacties tijdens een schrijfbewerking opgeeft. Vanwege de manier waarop replicatie op de achtergrond in Cosmos DB wordt afgehandeld, zijn alle schrijfbewerkingen automatisch Quorum-bewerkingen. Alle schrijfproblemen die in clientcode zijn opgegeven, worden genegeerd. Zie [Consistentieniveaus gebruiken om de beschikbaarheid en prestaties te maximaliseren](consistency-levels.md) voor meer informatie.

## <a name="sharding"></a>Sharding

Azure Cosmos DB biedt ondersteuning voor automatische sharding aan serverzijde. Het beheert Shard automatisch maken, plaatsen en balanceren. Azure Cosmos DB biedt geen ondersteuning voor hand matige sharding-opdrachten, wat betekent dat u geen opdrachten hoeft te roepen zoals addShard, balancerStart, moveChunk, enzovoort. U hoeft alleen de Shard-sleutel op te geven tijdens het maken van de containers of het uitvoeren van query's op de gegevens.

## <a name="sessions"></a>Sessies

Azure Cosmos DB biedt nog geen ondersteuning voor de opdrachten aan de server zijde.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg voor meer informatie [Mongo 3,6-versie functies](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

<sup>Opmerking: in dit artikel wordt een functie van Azure Cosmos DB beschreven die compatibiliteit met MongoDB-data bases biedt. Micro soft voert geen MongoDB-data bases uit om deze service te leveren. Azure Cosmos DB is niet gekoppeld aan MongoDB, Inc.</sup>
