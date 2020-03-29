---
title: Werken met JavaScript-geïntegreerde query-API in Azure Cosmos DB-opgeslagen procedures en triggers
description: In dit artikel worden de concepten voor javascript-taalgeïntegreerde query-API geïntroduceerd om opgeslagen procedures en triggers in Azure Cosmos DB te maken.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 7b7ad470b3330224e80a7160fc1a37bb5ee1cde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901831"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-query-API in Azure Cosmos DB

Naast het uitgeven van query's met behulp van de SQL API in Azure Cosmos DB, biedt de [Cosmos DB-server-side SDK](https://azure.github.io/azure-cosmosdb-js-server/) een JavaScript-interface voor het uitvoeren van geoptimaliseerde query's in Cosmos DB Stored Procedures en Triggers. U hoeft niet op de hoogte te zijn van de SQL-taal om deze JavaScript-interface te kunnen gebruiken. Met de JavaScript-query-API u programmatisch query's bouwen door predicaatfuncties door te geven in reeksfunctieaanroepen, met een syntaxis die bekend is met de array-ins van ECMAScript5 en populaire JavaScript-bibliotheken zoals Lodash. Query's worden ontleed door de JavaScript-runtime en efficiënt uitgevoerd met Azure Cosmos DB-indexen.

## <a name="supported-javascript-functions"></a>Ondersteunde JavaScript-functies

| **Functie** | **Beschrijving** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Hiermee start u een geketend gesprek dat moet worden beëindigd met waarde().|
|`filter(predicateFunction [, options] [, callback])`|Hiermee filtert u de invoer met behulp van een predicaatfunctie die true/false retourneert om in-/out-invoerdocumenten in de resulterende set te filteren. Deze functie gedraagt zich vergelijkbaar met een WHERE-clausule in SQL.|
|`flatten([isShallow] [, options] [, callback])`|Combineert en vlakt arrays van elk invoeritem af in één array. Deze functie gedraagt zich vergelijkbaar met SelectMany in LINQ.|
|`map(transformationFunction [, options] [, callback])`|Hiermee past u een projectie toe met een transformatiefunctie die elk invoeritem aan een JavaScript-object of -waarde toebrengt. Deze functie gedraagt zich vergelijkbaar met een SELECT-component in SQL.|
|`pluck([propertyName] [, options] [, callback])`|Deze functie is een snelkoppeling voor een kaart die de waarde van één eigenschap uit elk invoeritem haalt.|
|`sortBy([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoerdocumentstroom in oplopende volgorde te sorteren met behulp van het opgegeven predicaat. Deze functie gedraagt zich vergelijkbaar met een ORDER BY-clausule in SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Produceert een nieuwe set documenten door de documenten in de invoerdocumentstroom in aflopende volgorde te sorteren met behulp van het opgegeven predicaat. Deze functie gedraagt zich vergelijkbaar met een ORDER BY x DESC-component in SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Voert een zelf-join met binnenste array en voegt resultaten van beide zijden als tuples aan het resultaat projectie. Bijvoorbeeld, het samenvoegen van een persoon document met person.pets zou produceren [persoon, huisdier] tuples. Dit is vergelijkbaar met SelectMany in .NET LINK.|

Wanneer de volgende JavaScript-constructies worden opgenomen in de functie van predicaat en/of selector, worden ze automatisch geoptimaliseerd om rechtstreeks op Azure Cosmos DB-indexen uit te voeren:

- Eenvoudige `=` `+` `-` `*` `/` `%` `|` operatoren: `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!``~`
- Literals, met inbegrip van het object letterlijk:{}
- var, terug

De volgende JavaScript-constructies worden niet geoptimaliseerd voor Azure Cosmos DB-indexen:

- Controlestroom (bijvoorbeeld als, voor, terwijl)
- Functieaanroepen

Zie de [JavaScript-documentatie aan](https://azure.github.io/azure-cosmosdb-js-server/)de kant van de Cosmos DB Server Side .

## <a name="sql-to-javascript-cheat-sheet"></a>SQL naar JavaScript-spiekbriefje

In de volgende tabel worden verschillende SQL-query's en de bijbehorende JavaScript-query's gepresenteerd. Net als bij SQL-query's zijn eigenschappen (bijvoorbeeld item.id) hoofdlettergevoelig.

> [!NOTE]
> `__` (dubbele onderstreping) is een alias naar `getContext().getCollection()` bij gebruik van de JavaScript-query-API.

|**SQL**|**JavaScript-query-API**|**Beschrijving**|
|---|---|---|
|SELECTEER *<br>VAN documenten| __.map(functie(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;retourdocument;<br>});|Resultaten in alle documenten (paginated met voortzetting token) zoals het is.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id.<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;handelingen voor documenten. <br>VAN documenten|__.map(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;rendement {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;acties:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projecteert de id, het bericht (alias sj), en actie van alle documenten.|
|SELECTEER *<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;doc.id ==="X998_Y998";<br>});|Query's voor documenten met het predicaat: id = "X998_Y998".|
|SELECTEER *<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(documenten. Tags, 123)|__.filter(functie(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;x.Tags && x.Tags.indexOf(123) > -1 retourneren;<br>});|Query's voor documenten met een eigenschap Labels en Tags is een array met de waarde 123.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id.<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>VAN documenten<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;rendement {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.waarde();|Query's voor documenten met een predicaat, id = "X998_Y998", en vervolgens projecten van de id en het bericht (alias ed om msg).|
|WAARDE-tag SELECTEREN<br>VAN documenten<br>JOIN tag IN docs. Tags<br>BESTELLEN DOOR docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retourdocument. Tags && Array.isArray(doc. Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(functie(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;retourdocument._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.waarde()|Filters voor documenten met een arrayeigenschap, Tags en sorteert de resulterende documenten op de eigenschap _ts tijdstempelsysteem en hiermee wordt de array Tags afgevlakt+ afgevlakt.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over concepten en het schrijven en gebruiken van opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies in Azure Cosmos DB:

- [Opgeslagen procedures en triggers schrijven met Javascript Query API](how-to-write-javascript-query-api.md)
- [Werken met azure cosmos DB opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)
- [Opgeslagen procedures, triggers, door de gebruiker gedefinieerde functies gebruiken in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Api-verwijzing naar Azure Cosmos DB JavaScript-serverzijde](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
