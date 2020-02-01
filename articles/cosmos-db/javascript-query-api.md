---
title: Werken met geïntegreerde Java script-query-API in Azure Cosmos DB opgeslagen procedures en triggers
description: In dit artikel worden de concepten geïntroduceerd voor de in Java script geïntegreerde query-API voor het maken van opgeslagen procedures en triggers in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901831"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Java script-query-API in Azure Cosmos DB

Naast het uitgeven van query's met behulp van de SQL-API in Azure Cosmos DB, biedt de [Cosmos DB SDK aan de server zijde](https://azure.github.io/azure-cosmosdb-js-server/) een Java script-interface voor het uitvoeren van geoptimaliseerde query's in Cosmos DB opgeslagen procedures en triggers. U hoeft niet op de hoogte te zijn van de SQL-taal om deze Java script-interface te gebruiken. Met de Java script-query-API kunt u programmatisch query's maken door predikaten te gebruiken in volg orde van functie aanroepen, met een syntaxis die bekend is met de ECMAScript5's array-ingebouwde en populaire Java script-bibliotheken zoals Lodash. Query's worden door de Java Script-runtime geparseerd en efficiënt uitgevoerd met Azure Cosmos DB indices.

## <a name="supported-javascript-functions"></a>Ondersteunde Java script-functies

| **Functieassembly** | **Beschrijving** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Start een keten-aanroep die moet worden afgesloten met value().|
|`filter(predicateFunction [, options] [, callback])`|Filtert de invoer met behulp van een predicaat functie die resulteert in waar/onwaar om te filteren in/uit-invoer documenten in de resulterende set. Deze functie werkt die vergelijkbaar is met een WHERE-component in SQL.|
|`flatten([isShallow] [, options] [, callback])`|Hiermee worden matrices van elk invoer item gecombineerd en afgevlakt tot één matrix. Deze functie werkt die vergelijkbaar is met SelectMany in LINQ.|
|`map(transformationFunction [, options] [, callback])`|Van toepassing is een projectie een transformatiefunctie die elk item invoer wordt toegewezen aan een JavaScript-object of een waarde opgegeven. Deze functie werkt die vergelijkbaar is met een SELECT-component in SQL.|
|`pluck([propertyName] [, options] [, callback])`|Deze functie is een snelkoppeling voor een kaart die de waarde van één eigenschap van elk item invoer geëxtraheerd.|
|`sortBy([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoer document stroom in oplopende volg orde te sorteren met behulp van het opgegeven predicaat. Deze functie werkt die vergelijkbaar is met een ORDER BY-component in SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoer document stroom in aflopende volg orde te sorteren met het opgegeven predicaat. Deze functie werkt die vergelijkbaar is met een component ORDER BY x DESC in SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Voert een self-join uit met de binnenste matrix en voegt resultaten van beide zijden toe als Tuples voor de projectie van het resultaat. U kunt bijvoorbeeld een persoons document samen voegen met persoon. huis dieren zouden Tuples van [person, huisdier] opleveren. Dit is vergelijkbaar met SelectMany in .NET LINK.|

Wanneer ze zijn opgenomen in het predikaat en/of de selectie van functies, worden de volgende JavaScript-constructies automatisch geoptimaliseerd om te worden uitgevoerd op Azure Cosmos DB-indexen:

- Eenvoudige Opera tors: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Letterlijke waarden, met inbegrip van de letterlijke object: {}
- var, Ga terug

De volgende JavaScript-constructies kunnen niet worden geoptimaliseerd voor Azure Cosmos DB-indexen:

- Controlestroom (bijvoorbeeld, als voor, tijdens)
- Functieaanroepen

Zie de [Cosmos DB server side java script-documentatie](https://azure.github.io/azure-cosmosdb-js-server/)voor meer informatie.

## <a name="sql-to-javascript-cheat-sheet"></a>Cheat-blad van SQL naar Java script

De volgende tabel bevat verschillende SQL-query's en de bijbehorende JavaScript-query's. Net als bij SQL-query's zijn eigenschappen (bijvoorbeeld item.id) hoofdletter gevoelig.

> [!NOTE]
> `__` (dubbele onderstreping) is een alias naar `getContext().getCollection()` bij gebruik van de JavaScript-query-API.

|**SQL**|**Java script-query-API**|**Beschrijving**|
|---|---|---|
|SELECTEER *<br>VAN docs| __.map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;retourneren van doc-bestand;<br>});|Resultaten in alle documenten (gepagineerde met vervolgtoken) als is.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. bericht als msg,<br>&nbsp;&nbsp;&nbsp;docs. acties <br>VAN docs|__.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;{retourneren<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|De-id, een bericht (alias voor msg) en een actie uit alle documenten projecten.|
|SELECTEER *<br>VAN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retourneren van doc.id === 'X998_Y998';<br>});|Query's voor documenten met het predicaat: id = 'X998_Y998'.|
|SELECTEER *<br>VAN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS (docs. Tags, 123)|__.filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;retourneren van x.Tags & & x.Tags.indexOf(123) > -1;<br>});|Query's voor documenten die een eigenschap Tags en labels hebben, is een matrix met de waarde 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs. bericht als msg<br>VAN docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retourneren van doc.id === 'X998_Y998';<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{retourneren<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Query's voor documenten met een predicaat, id = 'X998_Y998', en vervolgens de id en het bericht (alias voor msg)-projecten.|
|SELECT VALUE-tag<br>VAN docs<br>Neem deel aan een tag IN docs. Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;document retourneren. Labels & & Array.isArray (doc-bestand. -Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retourneren van doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filters voor documenten met een matrixeigenschap, labels, en sorteert de resulterende documenten door de eigenschap _ts timestamp-systeem en projecten + de matrix Tags worden samengevoegd.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het schrijven en gebruiken van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB:

- [Opgeslagen procedures en triggers schrijven met behulp van Java script-query-API](how-to-write-javascript-query-api.md)
- [Werken met Azure Cosmos DB opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)
- [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies gebruiken in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript-serverzijde API-naslaginformatie](https://azure.github.io/azure-cosmosdb-js-server)
- [Java script-ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
