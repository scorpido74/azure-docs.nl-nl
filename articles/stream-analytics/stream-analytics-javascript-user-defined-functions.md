---
title: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics
description: Dit artikel is een inleiding tot door de gebruiker gedefinieerde Java script-functies in Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235396"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Door de gebruiker gedefinieerde Java script-functies in Azure Stream Analytics
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die zijn geschreven in JavaScript. Met de uitgebreide set **teken reeks**- **, regexp**-, **Math**-, **array**-en **date** -methoden die Java script biedt, worden complexe gegevens transformaties met Stream Analytics taken eenvoudiger te maken.

## <a name="overview"></a>Overzicht

Door de gebruiker gedefinieerde Java script-functies ondersteunen stateless, scalaire, alleen Compute-functies waarvoor geen externe verbinding is vereist. De resultaatwaarde van een functie mag alleen een scalaire (enkelvoudige) waarde zijn. Nadat u een door de gebruiker gedefinieerde JavaScript-functie aan een taak hebt toegevoegd, kunt u de functie overal in de query als een ingebouwde scalaire functie gebruiken.

Hier volgen enkele scenario's waarin door de gebruiker gedefinieerde JavaScript-functies mogelijk interessant kunnen zijn:
* Het parseren en manipuleren van tekenreeksen die functies met reguliere expressies bevatten, bijvoorbeeld **Regexp_Replace()** en **Regexp_Extract()**
* Het (de)coderen van gegevens, bijvoorbeeld bij een conversie van binair naar hexadecimaal
* Mathematic-berekeningen uitvoeren met **wiskundige** functies van Java script
* Matrix bewerkingen uitvoeren zoals sorteren, samen voegen, zoeken en vullen

Hier volgen enkele dingen die u niet kunt doen met een door de gebruiker gedefinieerde Java script-functie in Stream Analytics:
* Externe REST-eind punten aanroepen, bijvoorbeeld omgekeerde IP-Zoek opdrachten uitvoeren of referentie gegevens ophalen uit een externe bron
* Serialisatie of deserialisatie van gebeurtenissen met aangepaste indeling uitvoeren voor invoer/uitvoer
* Aangepaste combinaties maken

Hoewel functies als **date. GetDate ()** of **Math. random ()** niet worden geblokkeerd in de functie definitie, moet u het gebruik ervan vermijden. Deze functies retour neren hetzelfde resultaat **niet** telkens wanneer u ze aanroept en de Azure stream Analytics-service houdt geen logboeken van functie aanroepen en retourneert resultaten. Als een functie verschillende resultaten voor dezelfde gebeurtenissen retourneert, is herhaal baarheid niet gegarandeerd wanneer een taak door u of door de Stream Analytics-service opnieuw wordt gestart.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Een door de gebruiker gedefinieerde Java script-functie toevoegen aan uw taak

> [!NOTE]
> Deze stappen werken aan de Stream Analytics taken die zijn geconfigureerd om te worden uitgevoerd in de Cloud. Als uw Stream Analytics-taak is geconfigureerd om te worden uitgevoerd op Azure IoT Edge, gebruikt u in plaats daarvan Visual Studio en [schrijft u de door de gebruiker gedefinieerde functie met C#](stream-analytics-edge-csharp-udf.md).

Als u een door de gebruiker gedefinieerde Java script-functie in uw Stream Analytics-taak wilt maken, selecteert u **functies** onder **taak topologie**. Selecteer vervolgens **Java script UDF** in het vervolg keuzemenu **+ toevoegen** . 

![Java script UDF toevoegen](./media/javascript/stream-analytics-jsudf-add.png)

Vervolgens moet u de volgende eigenschappen opgeven en **Opslaan**selecteren.

|Eigenschap|Beschrijving|
|--------|-----------|
|Functie alias|Voer een naam in om de functie in de query aan te roepen.|
|Uitvoer type|Type dat wordt geretourneerd door de door de gebruiker gedefinieerde Java script-functie naar uw Stream Analytics-query.|
|Functie definitie|Implementatie van uw Java script-functie die wordt uitgevoerd wanneer uw UDF wordt aangeroepen vanuit uw query.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Java script-Udf's testen en problemen oplossen 

U kunt uw Java script UDF-logica testen en fouten opsporen in een browser. Fout opsporing en testen van de logica van deze door de gebruiker gedefinieerde functies wordt momenteel niet ondersteund in de Stream Analytics Portal. Zodra de functie werkt zoals verwacht, kunt u deze toevoegen aan de Stream Analytics-taak zoals hierboven wordt vermeld en vervolgens rechtstreeks vanuit uw query aanroepen. U kunt uw query logica testen met Java script UDF met [Stream Analytics-hulpprogram ma's voor Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

JavaScript-runtime-fouten worden beschouwd als onherstelbaar en worden weergegeven via het activiteitenlogboek. U haalt het logboek op door in Azure Portal naar uw project te gaan en **Activiteitenlogboek** te selecteren.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Een door de gebruiker gedefinieerde JavaScript-functie in een query aanroepen

U kunt uw Java script-functie in uw query eenvoudig aanroepen met behulp van de functie alias, voorafgegaan door **UDF**. Hier volgt een voor beeld van een Java script UDF waarmee hexadecimale waarden worden geconverteerd naar een geheel getal dat wordt aangeroepen in een Stream Analytics query.

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

Java script-taal is hoofdletter gevoelig en behuizing van de object velden in Java script-code moeten overeenkomen met de behuizing van de velden in de binnenkomende gegevens. Met taken met compatibiliteits niveau 1,0 worden velden van SQL SELECT-instructie geconverteerd naar kleine letters. Onder compatibiliteits niveau 1,1 en hoger, hebben velden uit de SELECT-instructie hetzelfde hoofdletter gebruik dat is opgegeven in de SQL-query.

## <a name="other-javascript-user-defined-function-patterns"></a>Andere door de gebruiker gedefinieerde JavaScript-functiepatronen

### <a name="write-nested-json-to-output"></a>Geneste JSON naar uitvoer schrijven

Als u een follow-up verwerkingsstap hebt die de uitvoer van een Stream Analytics-taak gebruikt als invoer, als die invoer een JSON-indeling vereist, kunt u een JSON-tekenreeks naar uitvoer schrijven. In het volgende voorbeeld wordt de functie **JSON.stringify()** aangeroepen om alle naam-/waarde-paren van de invoer te verpakken en als één tekenreekswaarde in de uitvoer te schrijven.

**Definitie van een door de gebruiker gedefinieerde JavaScript-functie:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Voorbeeld query:**
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
* [C#-UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
