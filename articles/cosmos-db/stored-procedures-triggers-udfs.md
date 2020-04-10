---
title: Werken met opgeslagen procedures, triggers en UDF's in Azure Cosmos DB
description: In dit artikel worden de concepten zoals opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB geïntroduceerd.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 13256377b8a8aaebf59196df57eef67d3b960cb8
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010542"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies

Azure Cosmos DB biedt taalgeïntegreerde, transactionele uitvoering van Java script. Wanneer u de SQL API in Azure Cosmos DB gebruikt, u **opgeslagen procedures,** **triggers**en **door de gebruiker gedefinieerde functies (UDF's)** schrijven in de JavaScript-taal. U kunt uw logica schrijven in JavaScript die wordt uitgevoerd binnen de database-engine. U triggers, opgeslagen procedures en UDF's maken en uitvoeren met behulp van [Azure-portal,](https://portal.azure.com/)de [javascript-taalgeïntegreerde query-API in Azure Cosmos DB](javascript-query-api.md) of de Cosmos DB SQL [API-client SDKs.](how-to-use-stored-procedures-triggers-udfs.md)

## <a name="benefits-of-using-server-side-programming"></a>Voordelen van het gebruik van server-side programmering

Met het schrijven van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies (UDF's) in JavaScript u rijke toepassingen bouwen en hebben ze de volgende voordelen:

* **Procedurele logica:** JavaScript als een programmeertaal op hoog niveau die een rijke en vertrouwde interface biedt om bedrijfslogica uit te drukken. U een reeks complexe bewerkingen uitvoeren op de gegevens.

* **Atoomtransacties:** Azure Cosmos DB garandeert dat de databasebewerkingen die binnen één opgeslagen procedure of een trigger worden uitgevoerd, atoom zijn. Met deze atoomfunctionaliteit kan een toepassing gerelateerde bewerkingen combineren tot één batch, zodat alle bewerkingen slagen of geen van deze bewerkingen slagen.

* **Prestaties:** De JSON-gegevens zijn intrinsiek toegewezen aan het JavaScript-taaltypesysteem. Deze mapping zorgt voor een aantal optimalisaties zoals luie materialisatie van JSON-documenten in de bufferpool en maakt ze on-demand beschikbaar voor de uitvoerende code. Er zijn andere prestatievoordelen verbonden aan het verzenden van bedrijfslogica naar de database, waaronder:

   * *Batching:* U bewerkingen zoals inserts groeperen en in bulk verzenden. De latentiekosten voor netwerkverkeer en de overhead van de winkel om afzonderlijke transacties te maken, worden aanzienlijk verminderd.

   * *Pre-compilatie:* Opgeslagen procedures, triggers en UDF's worden impliciet vooraf gecompileerd naar de bytecode-indeling om compilatiekosten te voorkomen op het moment van elke aanroep van het script. Als gevolg van pre-compilatie, de aanroep van opgeslagen procedures is snel en heeft een lage voetafdruk.

   * *Sequencing:* Soms hebben bewerkingen een triggermechanisme nodig dat een of meer updates voor de gegevens kan uitvoeren. Naast Atomicity zijn er ook prestatievoordelen bij het uitvoeren aan de serverzijde.

* **Inkapseling:** Opgeslagen procedures kunnen worden gebruikt om logica op één plaats te groeperen. Inkapseling voegt een abstractielaag toe bovenop de gegevens, waardoor u uw toepassingen onafhankelijk van de gegevens ontwikkelen. Deze abstractielaag is handig wanneer de gegevens schemaloos zijn en u niet hoeft te beheren dat u extra logica rechtstreeks in uw toepassing hoeft toe te voegen. Met de abstractie u de gegevens beveiligen door de toegang van de scripts te stroomlijnen.

> [!TIP]
> Opgeslagen procedures zijn het meest geschikt voor bewerkingen die schrijfzwaar zijn en waarvoor een transactie vereist is voor een partitiesleutelwaarde. Bij de beslissing om opgeslagen procedures te gebruiken, optimaliseren rond het inkapselen van de maximale hoeveelheid schrijfbewerkingen mogelijk. Over het algemeen zijn opgeslagen procedures niet de meest efficiënte manier om grote aantallen lees- of querybewerkingen uit te voeren, dus het gebruik van opgeslagen procedures om grote aantallen reads te batchen om terug te keren naar de klant zal niet het gewenste voordeel opleveren. Voor de beste prestaties moeten deze leeszware bewerkingen aan de clientzijde worden uitgevoerd met behulp van de Cosmos SDK. 

## <a name="transactions"></a>Transacties

Transactie in een typische database kan worden gedefinieerd als een reeks bewerkingen die worden uitgevoerd als één logische eenheid van het werk. Elke transactie biedt **ACID-eigendomsgaranties**. ACID is een bekend acroniem dat staat voor: **Een**tomicity, **C**onsistency, **I**solation, en **D**urability. 

* Atomiciteit garandeert dat alle operaties die binnen een transactie worden uitgevoerd, worden behandeld als één eenheid, en ze zijn allemaal toegewijd of geen van hen zijn. 

* Consistentie zorgt ervoor dat de gegevens altijd in een geldige staat zijn voor transacties. 

* Isolatie garandeert dat geen twee transacties elkaar verstoren – veel commerciële systemen bieden meerdere isolatieniveaus die kunnen worden gebruikt op basis van de toepassingsbehoeften. 

* Duurzaamheid zorgt ervoor dat elke wijziging die in een database wordt vastgelegd altijd aanwezig zal zijn.

In Azure Cosmos DB wordt JavaScript-runtime gehost in de database-engine. Daarom worden aanvragen binnen de opgeslagen procedures en de triggers uitgevoerd in hetzelfde bereik als de databasesessie. Met deze functie kan Azure Cosmos DB ZUUR-eigenschappen garanderen voor alle bewerkingen die deel uitmaken van een opgeslagen procedure of een trigger. Zie bijvoorbeeld [het artikel over transacties implementeren.](how-to-write-stored-procedures-triggers-udfs.md#transactions)

### <a name="scope-of-a-transaction"></a>Bereik van een transactie

Opgeslagen procedures zijn gekoppeld aan een Azure Cosmos-container en de uitvoering van de opgeslagen procedure wordt afgestemd op een logische partitiesleutel. Opgeslagen procedures moeten een logische partitiesleutelwaarde bevatten tijdens de uitvoering die de logische partitie voor het bereik van de transactie definieert. Zie Het [partitioneringsartikel van Azure Cosmos DB](partition-data.md) voor meer informatie.

### <a name="commit-and-rollback"></a>Commit en terugdraaien

Transacties zijn native geïntegreerd in het Azure Cosmos DB JavaScript-programmeermodel. Binnen een JavaScript-functie worden alle bewerkingen automatisch verpakt onder één transactie. Als de JavaScript-logica in een opgeslagen procedure zonder uitzonderingen wordt voltooid, worden alle bewerkingen binnen de transactie toegewezen aan de database. Instructies `BEGIN TRANSACTION` zoals `COMMIT TRANSACTION` en (bekend van relationele databases) zijn impliciet in Azure Cosmos DB. Als er uitzonderingen zijn op het script, wordt de runtime van Azure Cosmos DB JavaScript de hele transactie teruggedraaid. Als zodanig is het gooien van `ROLLBACK TRANSACTION` een uitzondering in feite gelijk aan een in Azure Cosmos DB.

### <a name="data-consistency"></a>Gegevensconsistentie

Opgeslagen procedures en triggers worden altijd uitgevoerd op de primaire replica van een Azure Cosmos-container. Deze functie zorgt ervoor dat de leest van opgeslagen procedures [een sterke consistentie](consistency-levels-tradeoffs.md)bieden. Query's met behulp van door de gebruiker gedefinieerde functies kunnen worden uitgevoerd op de primaire of een secundaire replica. Opgeslagen procedures en triggers zijn bedoeld om transactionele schrijfbewerkingen te ondersteunen - ondertussen wordt alleen-lezen logica het best geïmplementeerd als logica aan de toepassing en query's met behulp van de [Azure Cosmos DB SQL API SDKs](sql-api-dotnet-samples.md), zal u helpen de database doorvoer te verzadigen. 

## <a name="bounded-execution"></a>Gebonden uitvoering

Alle Azure Cosmos DB-bewerkingen moeten binnen de opgegeven time-outduur zijn voltooid. Deze beperking is van toepassing op JavaScript-functies - opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies. Als een bewerking niet binnen die termijn is voltooid, wordt de transactie teruggedraaid.

U ervoor zorgen dat uw JavaScript-functies binnen de tijdslimiet zijn voltooid of een op voortzetting gebaseerd model implementeren om de uitvoering van batch/hervatten te implementeren. Om de ontwikkeling van opgeslagen procedures en triggers te vereenvoudigen om tijdslimieten te verwerken, retourneren alle functies onder de Azure Cosmos-container (bijvoorbeeld items maken, lezen, bijwerken en verwijderen) een booleaanse waarde die aangeeft of die bewerking wordt voltooid. Als deze waarde onjuist is, is dit een indicatie dat de procedure de uitvoering moet afronden omdat het script meer tijd of ingerichte doorvoer verbruikt dan de geconfigureerde waarde. Bewerkingen die in de wachtrij staan vóór de eerste niet-geaccepteerde winkelbewerking, zijn gegarandeerd voltooid als de opgeslagen procedure op tijd is voltooid en geen aanvragen meer in de wachtrij staan. Bewerkingen moeten dus één voor één in de wachtrij worden geplaatst met behulp van de terugbelconventie van JavaScript om de controlestroom van het script te beheren. Omdat scripts worden uitgevoerd in een server-side omgeving, worden ze strikt geregeld. Scripts die herhaaldelijk de uitvoeringsgrenzen schenden, kunnen inactief zijn gemarkeerd en kunnen niet worden uitgevoerd en moeten opnieuw worden gemaakt om de uitvoeringsgrenzen te eren.

JavaScript-functies zijn ook onderworpen aan [de ingerichte doorvoercapaciteit.](request-units.md) JavaScript-functies kunnen binnen korte tijd een groot aantal aanvraageenheden gebruiken en kunnen tariefbeperkt zijn als de ingerichte doorvoercapaciteitlimiet is bereikt. Het is belangrijk op te merken dat scripts extra doorvoer verbruiken naast de doorvoer die wordt besteed aan het uitvoeren van databasebewerkingen, hoewel deze databasebewerkingen iets goedkoper zijn dan het uitvoeren van dezelfde bewerkingen van de client.

## <a name="triggers"></a>Triggers

Azure Cosmos DB biedt ondersteuning voor twee typen triggers:

### <a name="pre-triggers"></a>Pre-triggers

Azure Cosmos DB biedt triggers die kunnen worden aangeroepen door een bewerking uit te voeren op een Azure Cosmos-item. U kunt bijvoorbeeld een pre-trigger opgeven wanneer u een item maakt. In dit geval wordt de pre-trigger uitgevoerd voordat het item wordt gemaakt. Pre-triggers kunnen geen invoerparameters hebben. Indien nodig kan het aanvraagobject worden gebruikt om de hoofdtekst van het document van de oorspronkelijke aanvraag bij te werken. Wanneer triggers zijn geregistreerd, kunnen gebruikers de bewerkingen opgeven waarmee deze kunnen worden uitgevoerd. Als een trigger `TriggerOperation.Create`is gemaakt met , betekent dit dat het gebruik van de trigger in een vervangen de operatie niet is toegestaan. Zie Bijvoorbeeld het artikel [Over het schrijven van triggers schrijven.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

### <a name="post-triggers"></a>Post-triggers

Net als bij pre-triggers zijn post-triggers ook gekoppeld aan een bewerking op een Azure Cosmos-item en zijn er geen invoerparameters nodig. Ze worden uitgevoerd *nadat* de bewerking is voltooid en hebben toegang tot het antwoordbericht dat naar de client wordt verzonden. Zie Bijvoorbeeld het artikel [Over het schrijven van triggers schrijven.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

> [!NOTE]
> Geregistreerde triggers worden niet automatisch uitgevoerd wanneer hun bijbehorende bewerkingen (maken / verwijderen / vervangen / bijwerken) plaatsvinden. Ze moeten expliciet worden aangeroepen bij het uitvoeren van deze bewerkingen. Zie voor meer informatie hoe u het artikel [triggers uitvoert.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a name="user-defined-functions"></a><a id="udfs"></a>Door de gebruiker gedefinieerde functies

[Door de gebruiker gedefinieerde functies](sql-query-udfs.md) (UDF's) worden gebruikt om de syntaxis van SQL API-querytaal uit te breiden en aangepaste bedrijfslogica eenvoudig te implementeren. Ze kunnen alleen worden aangeroepen binnen query's. UDF's hebben geen toegang tot het contextobject en zijn bedoeld om te worden gebruikt als alleen JavaScript berekenen. Daarom kunnen UDF's worden uitgevoerd op secundaire replica's. Zie Bijvoorbeeld het artikel [Hoe u door de gebruiker gedefinieerde functies schrijft.](how-to-write-stored-procedures-triggers-udfs.md#udfs)

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>JavaScript-taalgeïntegreerde query-API

Naast het uitgeven van query's met SQL API-querysyntaxis, u met de SDK aan de [serverzijde](https://azure.github.io/azure-cosmosdb-js-server) query's uitvoeren met behulp van een JavaScript-interface zonder enige kennis van SQL. Met de JavaScript-query-API u programmatisch query's bouwen door predicaatfuncties door te geven in volgorde van functieaanroepen. Query's worden ontleed door de JavaScript-runtime en worden efficiënt uitgevoerd binnen Azure Cosmos DB. Zie [Werken met javascript-taal-api-artikel](javascript-query-api.md) voor meer informatie over JavaScript-query-API-ondersteuning. Zie Voor voorbeelden [hoe opgeslagen procedures en triggers kunnen worden geschreven met behulp van javascript query-API-artikel.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven en gebruiken van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB met de volgende artikelen:

* [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies schrijven](how-to-write-stored-procedures-triggers-udfs.md)

* [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies gebruiken](how-to-use-stored-procedures-triggers-udfs.md)

* [Werken met javascript-taalgeïntegreerde query-API](javascript-query-api.md)
