---
title: Voorbereidende stappen voor het migreren van gegevens naar de API van Azure Cosmos DB voor MongoDB
description: Dit document bevat een overzicht van de vereisten voor een gegevens migratie van MongoDB naar Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445201"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Stappen voorafgaand aan de migratie voor gegevens migraties van MongoDB naar de API van Azure Cosmos DB voor MongoDB

Voordat u uw gegevens migreert van MongoDB (on-premises of in de Cloud (IaaS)) naar de Azure Cosmos DB-API voor MongoDB, moet u het volgende doen:

1. [Azure Cosmos DB-account maken](#create-account)
2. [Een schatting maken van de door Voer die nodig is voor uw workloads](#estimate-throughput)
3. [Kies een optimale partitie sleutel voor uw gegevens](#partitioning)
4. [Meer informatie over het indexerings beleid dat u op uw gegevens kunt instellen](#indexing)

Als u de bovenstaande vereisten voor migratie al hebt voltooid, raadpleegt u de [MongoDb-gegevens migreren naar Azure Cosmos DB-API voor MongoDb](../dms/tutorial-mongodb-cosmos-db.md) voor de daad werkelijke gegevens migratie-instructies. Als dat niet het geval is, bevat dit document instructies voor het afhandelen van deze vereisten. 

## <a id="create-account"></a>Een Azure Cosmos DB-account maken 

Voordat u met de migratie begint, moet u [een Azure Cosmos-account maken met behulp van de API van Azure Cosmos DB voor MongoDb](create-mongodb-dotnet.md). 

Bij het maken van het account kunt u instellingen kiezen om uw gegevens [wereld wijd te distribueren](distribute-data-globally.md) . U hebt ook de optie voor het inschakelen van schrijf bewerkingen met meerdere regio's (of configuratie met meerdere masters), waarmee u elk van uw regio's zowel een schrijf-als Lees regio kunt.

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>De door Voer voor uw workloads schatten

Voordat u de migratie start met behulp van de [database Migration service (DMS)](../dms/dms-overview.md), moet u de hoeveelheid door Voer schatten die moet worden ingericht voor uw Azure Cosmos-data bases en-verzamelingen.

De door Voer kan worden ingericht op een van de volgende:

- Verzameling

- Database

> [!NOTE]
> U kunt ook een combi natie van het bovenstaande hebben, waarbij sommige verzamelingen in een Data Base mogelijk een toegewezen ingerichte door voer hebben en anderen de door Voer kunnen delen. Zie de pagina [door Voer instellen voor een Data Base en een container](set-throughput.md) voor meer informatie.
>

U moet eerst beslissen of u een Data Base of een door Voer op verzamelings niveau wilt inrichten, of een combi natie van beide. Over het algemeen kunt u het beste een speciale door Voer configureren op het niveau van de verzameling. Door Voer in te richten op database niveau, kunnen verzamelingen in uw data base de ingerichte door Voer delen. Bij een gedeelde door Voer is er echter geen garantie voor een specifieke door Voer voor elke afzonderlijke verzameling, en u krijgt geen voorspel bare prestaties voor een specifieke verzameling.

Als u niet zeker weet hoeveel door Voer voor elke afzonderlijke verzameling moet worden toegewezen, kunt u door Voer op database niveau kiezen. U kunt de ingerichte door Voer die is geconfigureerd op uw Azure Cosmos-data base zien als een logische equivalent van de berekenings capaciteit van een MongoDB-VM of een fysieke server, maar rendabeler met de mogelijkheid om elastisch te schalen. Zie de [door Voer inrichten op Azure Cosmos-containers en-data bases](set-throughput.md)voor meer informatie.

Als u de door Voer op database niveau inricht, moeten alle verzamelingen die in die data base worden gemaakt, worden gemaakt met een partitie-Shard sleutel. Zie [partitioneren en horizon taal schalen in azure Cosmos DB](partition-data.md)voor meer informatie over partitioneren. Als u tijdens de migratie geen partitie-Shard sleutel opgeeft Azure Database Migration Service, wordt het sleutel veld Shard automatisch gevuld met een *_id* kenmerk dat automatisch wordt gegenereerd voor elk document.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Het optimale aantal aanvraag eenheden (RUs) dat moet worden ingericht

In Azure Cosmos DB wordt de door Voer vooraf ingericht en in aanvraag eenheden (RU) per seconde gemeten. Als u werk belastingen hebt waarop MongoDB wordt uitgevoerd op een virtuele machine of on-premises, kunt u zien of RU als eenvoudige abstractie voor fysieke resources is, zoals voor de grootte van een virtuele machine of een on-premises server en de resources die ze bezitten, bijvoorbeeld geheugen, CPU, IOPs. 

In tegens telling tot Vm's of on-premises servers is RUs op elk gewenst moment eenvoudig omhoog en omlaag schalen. U kunt het aantal ingerichte RUs binnen enkele seconden wijzigen en u wordt alleen gefactureerd voor het maximum aantal RUs dat u voor een bepaalde periode van één uur inricht. Zie [aanvraag eenheden in azure Cosmos DB](request-units.md)voor meer informatie.

Hieronder vindt u belang rijke factoren die van invloed zijn op het aantal vereiste RUs:
- **Item (bijvoorbeeld document) grootte**: als de grootte van een item/document toeneemt, wordt het aantal dat is gebruikt voor het lezen of schrijven van het item/document ook verhoogd.
- **Aantal item eigenschappen**: aangenomen dat de [standaard indexering](index-overview.md) voor alle eigenschappen, het aantal verbruikte RUs voor het schrijven van een item neemt toe naarmate het aantal item eigenschappen toeneemt. U kunt het verbruik van aanvraag eenheden voor schrijf bewerkingen verlagen door [het aantal geïndexeerde eigenschappen te beperken](index-policy.md).
- **Gelijktijdige bewerkingen**: verbruikte aanvraag eenheden zijn ook afhankelijk van de frequentie waarmee verschillende ruwe bewerkingen (zoals schrijven, lezen, bijwerken, verwijderen) en complexere query's worden uitgevoerd. U kunt [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) gebruiken om de gelijktijdigheids behoeften van uw huidige MongoDb-gegevens uit te voeren.
- **Query patronen**: de complexiteit van een query is van invloed op het aantal aanvraag eenheden dat door de query wordt verbruikt.

Als u JSON-bestanden exporteert met [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) en inzicht hebt in het aantal schrijf bewerkingen, lees bewerkingen, updates en verwijderingen die per seconde worden uitgevoerd, kunt u de [Azure Cosmos DB capacity planner](https://www.documentdb.com/capacityplanner) gebruiken om het aanvankelijke aantal RUs te ramen dat moet worden ingericht. De capaciteits planner houdt geen rekening met de kosten van complexere query's. Als u complexe query's op uw gegevens hebt, worden er dus extra RUs verbruikt. Ook wordt ervan uitgegaan dat alle velden worden geïndexeerd en dat sessie consistentie wordt gebruikt. De beste manier om de kosten van query's te begrijpen is door uw gegevens (of voorbeeld gegevens) te migreren naar Azure Cosmos DB, [verbinding te maken met het eind punt van de Cosmos DB](connect-mongodb-account.md) en een voorbeeld query uit de MongoDb-shell uit te voeren met behulp van de `getLastRequestStastistics`-opdracht om de aanvraag kosten op te halen, waardoor het aantal verbruikte RUs wordt uitgevoerd:

`db.runCommand({getLastRequestStatistics: 1})`

Met deze opdracht wordt een JSON-document uitgevoerd dat lijkt op het volgende:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Nadat u het aantal door een query verbruikte RUs hebt begrepen en de gelijktijdigheids behoeften voor die query, kunt u het aantal ingerichte RUs aanpassen. Het optimaliseren van RUs is geen eenmalige gebeurtenis: u moet het RUs-aanbod voortdurend optimaliseren of schalen, afhankelijk van het feit of u geen zwaar verkeer verwacht, in plaats van een zware werk belasting of het importeren van gegevens.

## <a id="partitioning"></a>Kies uw partitie sleutel
Partitioneren is een belang rijk aandachtspunt voordat u migreert naar een wereld wijd gedistribueerde data base, zoals Azure Cosmos DB. Azure Cosmos DB maakt gebruik van partitionering om afzonderlijke containers in een Data Base te schalen om te voldoen aan de schaal baarheid en prestatie behoeften van uw toepassing. In partitioneren worden de items in een container onderverdeeld in afzonderlijke subsets met de naam logische partities. Zie de [sectie een partitie sleutel kiezen](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)voor meer informatie en aanbevelingen over het kiezen van de juiste partitie sleutel voor uw gegevens. 

## <a id="indexing"></a>Uw gegevens indexeren
Azure Cosmos DB indexeert standaard al uw gegevens velden bij opname. U kunt het [indexerings beleid](index-policy.md) in azure Cosmos DB op elk gewenst moment wijzigen. Het wordt vaak aanbevolen om indexeren uit te scha kelen bij het migreren van gegevens en deze vervolgens weer in te scha kelen wanneer de gegevens al in Cosmos DB. Meer informatie over indexeren vindt u in de sectie [indexering in azure Cosmos DB](index-overview.md) van deze informatie. 

[Azure database Migration service](../dms/tutorial-mongodb-cosmos-db.md) wordt automatisch MongoDb-verzamelingen met unieke indexen gemigreerd. De unieke indexen moeten echter worden gemaakt vóór de migratie. Azure Cosmos DB biedt geen ondersteuning voor het maken van unieke indexen wanneer er al gegevens in uw verzamelingen aanwezig zijn. Zie [unieke sleutels in azure Cosmos DB](unique-keys.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Migreer uw MongoDB-gegevens naar Cosmos DB met behulp van de Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Door Voer in te richten op Azure Cosmos-containers en-data bases](set-throughput.md)
* [Partitionering in Azure Cosmos DB](partition-data.md)
* [Globale distributie in Azure Cosmos DB](distribute-data-globally.md)
* [Indexering in Azure Cosmos DB](index-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
