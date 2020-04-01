---
title: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics
description: Dit artikel is een inleiding tot JavaScript-door de gebruiker gedefinieerde functies in Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235396"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>JavaScript-door de gebruiker gedefinieerde functies in Azure Stream Analytics
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die zijn geschreven in JavaScript. Met de uitgebreide set **reeks tekenreeks-,** **RegExp-,** **Math-,** **Array-** en **Date-methoden** die JavaScript biedt, worden complexe gegevenstransformaties met Stream Analytics-taken gemakkelijker te maken.

## <a name="overview"></a>Overzicht

JavaScript-door de gebruiker gedefinieerde functies ondersteunen stateloze, alleen compute-scalaire functies waarvoor geen externe connectiviteit vereist is. De resultaatwaarde van een functie mag alleen een scalaire (enkelvoudige) waarde zijn. Nadat u een door de gebruiker gedefinieerde JavaScript-functie aan een taak hebt toegevoegd, kunt u de functie overal in de query als een ingebouwde scalaire functie gebruiken.

Hier volgen enkele scenario's waarin door de gebruiker gedefinieerde JavaScript-functies mogelijk interessant kunnen zijn:
* Het parseren en manipuleren van tekenreeksen die functies met reguliere expressies bevatten, bijvoorbeeld **Regexp_Replace()** en **Regexp_Extract()**
* Het (de)coderen van gegevens, bijvoorbeeld bij een conversie van binair naar hexadecimaal
* Mathematic-berekeningen uitvoeren met **JavaScript-wiskundige** functies
* Arraybewerkingen uitvoeren zoals sorteren, lid worden, zoeken en invullen

Hier volgen enkele dingen die u niet doen met een JavaScript-functie die door de gebruiker is gedefinieerd in Stream Analytics:
* Roep externe REST-eindpunten uit, bijvoorbeeld door reverse IP-lookup te doen of referentiegegevens uit een externe bron te halen
* Serialisatie of deserialisatie van gebeurtenissen met aangepaste indeling uitvoeren voor invoer/uitvoer
* Aangepaste combinaties maken

Hoewel functies zoals **Date.GetDate()** of **Math.random()** niet worden geblokkeerd in de definitie van functies, moet u voorkomen dat u ze gebruikt. Deze functies geven **niet** hetzelfde resultaat elke keer dat u ze belt en de Azure Stream Analytics-service houdt geen logboek bij van functieaanroepen en geretourneerde resultaten. Als een functie een ander resultaat op dezelfde gebeurtenissen retourneert, is herhaalbaarheid niet gegarandeerd wanneer een taak opnieuw wordt gestart door u of door de Stream Analytics-service.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Een door javascript-gebruiker gedefinieerde functie toevoegen aan uw taak

> [!NOTE]
> Deze stappen werken op de Stream Analytics-taken die zijn geconfigureerd om in de cloud uit te voeren. Als uw Stream [Analytics-taak](stream-analytics-edge-csharp-udf.md)is geconfigureerd om uit te voeren op Azure IoT Edge, gebruikt u Visual Studio en schrijft u de door de gebruiker gedefinieerde functie met C# .

Als u een JavaScript-gebruikersgedefinieerde functie wilt maken in uw functie Stream Analytics, selecteert u **Functies** onder **Taaktopologie**. Selecteer vervolgens **JavaScript UDF** in het vervolgkeuzemenu **+Toevoegen.** 

![JavaScript UDF toevoegen](./media/javascript/stream-analytics-jsudf-add.png)

U moet vervolgens de volgende eigenschappen opgeven en **Opslaan**selecteren.

|Eigenschap|Beschrijving|
|--------|-----------|
|Functiealias|Voer een naam in om de functie in uw query aan te roepen.|
|Uitvoertype|Tekst die wordt geretourneerd door uw JavaScript-door de gebruiker gedefinieerde functie naar uw Stream Analytics-query.|
|Functiedefinitie|Implementatie van uw JavaScript-functie die wordt uitgevoerd telkens wanneer uw UDF wordt aangeroepen vanuit uw zoekopdracht.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF's testen en oplossen 

U uw JavaScript UDF-logica in elke browser testen en debuggen. Het opsporen en testen van de logica van deze door de gebruiker gedefinieerde functies wordt momenteel niet ondersteund in de Stream Analytics-portal. Zodra de functie werkt zoals verwacht, u deze toevoegen aan de streamanalytics-taak zoals hierboven vermeld en deze vervolgens rechtstreeks vanuit uw query aanroepen. U uw querylogica testen met JavaScript UDF met behulp van [Stream Analytics-hulpprogramma's voor Visual Studio.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)

JavaScript-runtime-fouten worden beschouwd als onherstelbaar en worden weergegeven via het activiteitenlogboek. U haalt het logboek op door in Azure Portal naar uw project te gaan en **Activiteitenlogboek** te selecteren.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Een door de gebruiker gedefinieerde JavaScript-functie in een query aanroepen

U uw JavaScript-functie eenvoudig aanroepen in uw query met behulp van de functiealias die vooraf is gekoppeld **aan udf.** Hier is een voorbeeld van een JavaScript UDF die hexadecimale waarden omzet naar een geheel getal dat wordt aangeroepen in een Stream Analytics-query.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Ondersteunde JavaScript-objecten

Door de gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics ondersteunen standaard ingebouwde JavaScript-objecten. Zie [Algemene objecten](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) voor een lijst van deze objecten.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Typeconversie van Stream Analytics en JavaScript

Er zijn verschillen in de typen die de querytaal van Stream Analytics en JavaScript ondersteunen. Deze tabel bevat de conversietoewijzingen tussen de twee:

Stream Analytics | Javascript
--- | ---
bigint | Number (In JavaScript kunnen alleen gehele getallen tot exact 2^53 worden weergegeven)
DateTime | Date (JavaScript ondersteunt alleen milliseconden)
double | Aantal
nvarchar(MAX) | Tekenreeks
Record | Object
Matrix | Matrix
NULL | Null

Hier volgen JavaScript-naar-Stream Analytics-conversies:

Javascript | Stream Analytics
--- | ---
Aantal | Bigint (als het een rond getal tussen long.MinValue en long.MaxValue is; anders is het double)
Date | DateTime
Tekenreeks | nvarchar(MAX)
Object | Record
Matrix | Matrix
Null, niet gedefinieerd | NULL
Elk ander type (bijvoorbeeld een functie of fout) | Niet ondersteund (resulteert in een runtime-fout)

JavaScript-taal is hoofdlettergevoelig en de behuizing van de objectvelden in JavaScript-code moet overeenkomen met de behuizing van de velden in de binnenkomende gegevens. Taken met compatibiliteitsniveau 1.0 worden van SQL SELECT-instructie omgezet in kleine letters. Onder compatibiliteitsniveau 1.1 en hoger hebben velden van de select-instructie dezelfde behuizing als opgegeven in de SQL-query.

## <a name="other-javascript-user-defined-function-patterns"></a>Andere door de gebruiker gedefinieerde JavaScript-functiepatronen

### <a name="write-nested-json-to-output"></a>Geneste JSON naar uitvoer schrijven

Als u een follow-up verwerkingsstap hebt die de uitvoer van een Stream Analytics-taak gebruikt als invoer, als die invoer een JSON-indeling vereist, kunt u een JSON-tekenreeks naar uitvoer schrijven. In het volgende voorbeeld wordt de functie **JSON.stringify()** aangeroepen om alle naam-/waarde-paren van de invoer te verpakken en als één tekenreekswaarde in de uitvoer te schrijven.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Voorbeeldquery:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>Volgende stappen

* [Machine Learning UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
