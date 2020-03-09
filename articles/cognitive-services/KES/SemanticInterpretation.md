---
title: Semantische interpretatie-Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van semantische interpretatie in de Knowledge Exploration Service-API (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385654"
---
# <a name="semantic-interpretation"></a>Semantische interpretatie

Semantische interpretatie koppelt semantische uitvoer met elk geïnterpreteerd pad via de grammatica.  Met name evalueert de service de volg orde van de instructies in de `tag` elementen die door de interpretatie worden gepasseerd om de uiteindelijke uitvoer te berekenen.  

Een instructie kan een toewijzing van een letterlijke waarde of een variabele zijn voor een andere variabele.  Het kan ook de uitvoer van een functie met 0 of meer para meters aan een variabele toewijzen.  Elke functie parameter kan worden opgegeven met behulp van een letterlijke waarde of een variabele.  Als de functie geen uitvoer retourneert, wordt de toewijzing wegge laten.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Een variabele wordt opgegeven met behulp van een naam-id die begint met een letter en die alleen uit letters (A-Z), cijfers (0-9) en het onderstrepings teken (\_) bestaat.  Het type wordt impliciet afgeleid van de letterlijke waarde of functie-uitvoer die eraan is toegewezen. 

Hieronder ziet u een lijst met gegevens typen die momenteel worden ondersteund:

|Type|Beschrijving|Voorbeelden|
|----|----|----|
|Tekenreeks|Reeks van 0 of meer tekens|"Hallo wereld!"<br/>""|
|Bool|Booleaanse waarde|true<br/>onwaar|
|Int32|32-bits geheel getal met teken.  -2.1 E9 tot 2.1 E9|123<br/>-321|
|Int64|64-bits geheel getal met teken. -9.2 E18 en 9.2 E18|9876543210|
|Double-waarde|Drijvende komma met dubbele precisie. 1.7 e +/-308 (15 cijfers)|123,456789<br/>1.23456789 E2|
|Guid|Wereld wijd unieke id|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Query's uitvoeren|Query-expressie waarmee een subset van gegevens objecten in de index wordt opgegeven|All ()<br/>En (*K1*, *Q2*)|

## <a name="semantic-functions"></a>Semantische functies

Er is een ingebouwde set met semantische functies.  Ze staan het bouwen van geavanceerde query's toe en bieden context gevoelige controle over grammatica-interpretaties.

### <a name="and-function"></a>En-functie

`query = And(query1, query2);`

Retourneert een query die is samengesteld uit het snij punt van twee invoer query's.

### <a name="or-function"></a>Or-functie

`query = Or(query1, query2);`

Retourneert een query die is samengesteld uit de samen voeging van twee invoer query's.

### <a name="all-function"></a>Functie all

`query = All();`

Retourneert een query die alle gegevens objecten bevat.

In het volgende voor beeld gebruiken we de functie all () om iteratief een query op te bouwen op basis van het snij punt van 1 of meer tref woorden.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>De functie geen

`query = None();`

Retourneert een query die geen gegevens objecten bevat.

In het volgende voor beeld gebruiken we de functie geen () om een query iteratief te maken op basis van de samen voeging van 1 of meer tref woorden.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Query functie

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Retourneert een query die alleen gegevens objecten bevat waarvan het kenmerk *attr* overeenkomt met *de waardewaarde volgens* de *opgegeven bewerking,* die standaard wordt ingesteld op EQ.  Normaal gesp roken gebruikt u een `attrref`-element om een query te maken op basis van de overeenkomende invoer query teken reeks.  Als een waarde wordt gegeven of op een andere manier wordt opgehaald, kan de functie query () worden gebruikt om een query te maken die overeenkomt met deze waarde.

In het volgende voor beeld gebruiken we de functie query () om ondersteuning te implementeren om academische publicaties van een bepaald decennium op te geven.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Samengestelde functie

`query = Composite(innerQuery);`

Retourneert een query die een *innerQuery* omvat die overeenkomt met de onderliggende kenmerken van een gemeen schappelijk kenmerk *attr*.  De inkapseling vereist dat het samengestelde kenmerk *attr* van een overeenkomend gegevens object ten minste één waarde heeft die afzonderlijk aan de *innerQuery*voldoet.  Houd er rekening mee dat een query op subkenmerken van een samengesteld kenmerk moet worden ingekapseld met de functie samengestelde () voordat deze kan worden gecombineerd met andere query's.

De volgende query retourneert bijvoorbeeld academische publicaties door ' Harry Shum ' terwijl hij bij ' micro soft ' was:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Anderzijds retourneert de volgende query academische publicaties waarbij een van de auteurs ' Harry Shum ' is en een van de connecties ' micro soft ' is:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>De functie GetVariable

`value = GetVariable(name, scope);`

Retourneert de waarde van de *naam* van de variabele die is gedefinieerd onder het opgegeven *bereik*.  *naam* is een id die begint met een letter en bestaat uit letters (a-Z), cijfers (0-9) en het onderstrepings teken (_).  het *bereik* kan worden ingesteld op request of System.  Houd er rekening mee dat variabelen die onder verschillende bereiken zijn gedefinieerd, onderscheiden van elkaar, met inbegrip van de waarden die zijn gedefinieerd via de uitvoer van semantische functies.

Variabelen voor de aanvraag bereik worden gedeeld met alle interpretaties binnen de huidige interpret-aanvraag.  Ze kunnen worden gebruikt voor het beheren van de zoek opdracht naar interpretaties via de grammatica.

Systeem variabelen zijn vooraf gedefinieerd door de service en kunnen worden gebruikt voor het ophalen van verschillende statistieken over de huidige status van het systeem.  Hieronder ziet u de reeks momenteel ondersteunde systeem variabelen:

|Naam|Type|Beschrijving|
|----|----|----|
|IsAtEndOfQuery|Bool|waar als de huidige interpretatie overeenkomt met alle invoer query tekst|
|IsBeyondEndOfQuery|Bool|waar als de huidige interpretatie is voltooid met een aanvulling op de tekst van de invoer query|

### <a name="setvariable-function"></a>De functie SetVariable

`SetVariable(name, value, scope);`

Hiermee wordt een *waarde* toegewezen aan de *naam* van een variabele onder het opgegeven *bereik*.  *naam* is een id die begint met een letter en bestaat uit letters (a-Z), cijfers (0-9) en het onderstrepings teken (_).  Op dit moment is de enige geldige waarde voor *Scope* "Request".  Er zijn geen instel bare systeem variabelen.

Variabelen voor de aanvraag bereik worden gedeeld met alle interpretaties binnen de huidige interpret-aanvraag.  Ze kunnen worden gebruikt voor het beheren van de zoek opdracht naar interpretaties via de grammatica.

### <a name="assertequals-function"></a>De functie AssertEquals

`AssertEquals(value1, value2);`

Als *waarde1* en *waarde2* overeenkomen, is de functie geslaagd en heeft deze geen neven effecten.  Anders mislukt de functie en weigert de interpretatie.

### <a name="assertnotequals-function"></a>De functie AssertNotEquals

`AssertNotEquals(value1, value2);`

Als *waarde1* en *waarde2* niet overeenkomen, is de functie geslaagd en heeft deze geen neven effecten.  Anders mislukt de functie en weigert de interpretatie.


