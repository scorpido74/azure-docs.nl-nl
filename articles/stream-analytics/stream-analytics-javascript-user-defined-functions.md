---
title: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics
description: In deze zelfstudie voert u geavanceerde querymechanismen uit met door de gebruiker gedefinieerde JavaScript-functies
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: 8a26e369783da8b59837e669dcd45a338ce82722
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935005"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics
 
Azure Stream Analytics ondersteunt door de gebruiker gedefinieerde functies die zijn geschreven in JavaScript. Met de uitgebreide set van **String**-, **RegExp**-, **Math**-, **Array**- en **Date**-methoden van JavaScript kunt u gemakkelijker complexe gegevenstransformaties maken met Stream Analytics-taken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker gedefinieerde JavaScript-functie definiëren
> * De functie toevoegen aan de portal
> * Een query definiëren die de functie uitvoert

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="javascript-user-defined-functions"></a>Door de gebruiker gedefinieerde JavaScript-functies
Door de gebruiker gedefinieerde JavaScript-functies ondersteunen staatloze, scalaire rekenfuncties die geen externe verbinding nodig hebben. De resultaatwaarde van een functie mag alleen een scalaire (enkelvoudige) waarde zijn. Nadat u een door de gebruiker gedefinieerde JavaScript-functie aan een taak hebt toegevoegd, kunt u de functie overal in de query als een ingebouwde scalaire functie gebruiken.

Hier volgen enkele scenario's waarin door de gebruiker gedefinieerde JavaScript-functies mogelijk interessant kunnen zijn:
* Het parseren en manipuleren van tekenreeksen die functies met reguliere expressies bevatten, bijvoorbeeld **Regexp_Replace()** en **Regexp_Extract()**
* Het (de)coderen van gegevens, bijvoorbeeld bij een conversie van binair naar hexadecimaal
* Het uitvoeren van rekenkundige berekeningen met **Math**-functies van JavaScript
* Het uitvoeren van matrixbewerkingen zoals sorteren, samenvoegen, zoeken en vullen

Hier volgen enkele dingen die u met een door de gebruiker gedefinieerde JavaScript-functie niet kunt doen in Stream Analytics:
* Externe REST-eindpunten aanroepen, bijvoorbeeld, het uitvoeren van een reverse IP-lookup of het ophalen van referentiegegevens uit een externe bron
* Serialisatie of deserialisatie van gebeurtenissen met aangepaste indeling uitvoeren voor invoer/uitvoer
* Aangepaste combinaties maken

Hoewel functies als **Date.GetDate()** of **Math.random()** niet zijn geblokkeerd in de definitie van functies, kunt u ze beter niet gebruiken. Deze functies resulteren **niet** telkens wanneer u ze aanroept hetzelfde resultaat. Bovendien houdt de Azure Stream Analytics-service geen logboek bij van functieaanroepen en de geretourneerde resultaten. Als een functie verschillende resultaten voor dezelfde gebeurtenissen retourneert, wordt herhaalbaarheid niet gegarandeerd wanneer een taak door u of door de Stream Analytics-service opnieuw wordt gestart.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Een door de gebruiker gedefinieerde JavaScript-functie toevoegen in Azure Portal
Voer de volgende stappen uit om een door de gebruiker gedefinieerde Java script-functie te maken onder een bestaande Stream Analytics-taak:

> [!NOTE]
> Deze stappen werken aan de Stream Analytics taken die zijn geconfigureerd om te worden uitgevoerd in de Cloud. Als uw Stream Analytics-taak is geconfigureerd om te worden uitgevoerd op Azure IoT Edge, gebruikt u in plaats daarvan Visual Studio en [schrijft C#u de door de gebruiker gedefinieerde functie met ](stream-analytics-edge-csharp-udf.md).

1.  Zoek uw Stream Analytics-taak in Azure Portal.

2. Selecteer **functies**onder de kop **taak topologie** . Er wordt een lege functielijst weergegeven.

3.  Als u een nieuwe door de gebruiker gedefinieerde functie wilt maken, selecteert u **+ toevoegen**.

4.  Selecteer in de blade **Nieuwe functie** bij **Functietype** de optie **JavaScript**. In de editor wordt een standaardfunctiesjabloon weergegeven.

5.  Voer bij **UDF-alias** **hex2Int** in en verander de functie-implementatie als volgt:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Selecteer **Opslaan**. Uw functie wordt weergegeven in de lijst met functies.
7.  Selecteer de nieuwe **hex2Int**-functie en controleer de functiedefinitie. Alle functies hebben een functiealias waaraan het voorvoegsel **UDF** is toegevoegd. U moet *het voorvoegsel opnemen* wanneer u de functie aanroept in de Stream Analytics-query. In dat geval roept u **UDF.hex2Int** aan.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Een door de gebruiker gedefinieerde JavaScript-functie in een query aanroepen

1. Selecteer in de query-editor onder de kop **taak topologie** de optie **query**.
2.  Bewerk de query en roep de door de gebruiker gedefinieerde functie als volgt aan:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Als u het voorbeeldgegevensbestand wilt uploaden, klik dan met de rechtermuisknop op de taakinvoer.
4.  Als u de query wilt testen, selecteert u **Testen**.


## <a name="supported-javascript-objects"></a>Ondersteunde JavaScript-objecten
Door de gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics ondersteunen standaard ingebouwde JavaScript-objecten. Zie [Algemene objecten](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects) voor een lijst van deze objecten.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Typeconversie van Stream Analytics en JavaScript

Er zijn verschillen in de typen die de querytaal van Stream Analytics en JavaScript ondersteunen. Deze tabel bevat de conversietoewijzingen tussen de twee:

Stream Analytics | Javascript
--- | ---
bigint | Number (In JavaScript kunnen alleen gehele getallen tot exact 2^53 worden weergegeven)
Datum/tijd | Date (JavaScript ondersteunt alleen milliseconden)
double | Aantal
nvarchar(MAX) | Tekenreeks
Record | Object
Matrix | Matrix
NULL | Null


Hier volgen JavaScript-naar-Stream Analytics-conversies:


Javascript | Stream Analytics
--- | ---
Aantal | Bigint (als het een rond getal tussen long.MinValue en long.MaxValue is; anders is het double)
Datum | Datum/tijd
Tekenreeks | nvarchar(MAX)
Object | Record
Matrix | Matrix
Null, niet gedefinieerd | NULL
Elk ander type (bijvoorbeeld een functie of fout) | Niet ondersteund (resulteert in een runtime-fout)

Java script-taal is hoofdletter gevoelig en behuizing van de object velden in Java script-code moeten overeenkomen met de behuizing van de velden in de binnenkomende gegevens. Houd er rekening mee dat taken met compatibiliteits niveau 1,0 velden van SQL SELECT-instructie worden geconverteerd naar kleine letters. Onder compatibiliteits niveau 1,1 en hoger, hebben velden uit de SELECT-instructie hetzelfde hoofdletter gebruik dat is opgegeven in de SQL-query.

## <a name="troubleshooting"></a>Problemen oplossen
JavaScript-runtime-fouten worden beschouwd als onherstelbaar en worden weergegeven via het activiteitenlogboek. U haalt het logboek op door in Azure Portal naar uw project te gaan en **Activiteitenlogboek** te selecteren.


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
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze snelstart zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Klik in het menu aan de linkerkant in Azure Portal op **Resourcegroepen** en klik vervolgens op de resource die u hebt gemaakt.  
2. Klik op de pagina van uw resourcegroep op **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en klik vervolgens op **Verwijderen**.

## <a name="get-help"></a>Hulp krijgen
Meer hulp vindt u mogelijk op het [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Stream Analytics-taak gemaakt waarmee een eenvoudige door de gebruiker gedefinieerde JavaScript-functie wordt uitgevoerd. Voor meer informatie over Stream Analytics gaat u verder met de artikelen over realtime scenario's:

> [!div class="nextstepaction"]
> [Analyse van realtime Twitter-gevoel in Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
