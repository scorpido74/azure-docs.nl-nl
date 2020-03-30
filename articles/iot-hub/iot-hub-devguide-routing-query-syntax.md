---
title: Query op Azure IoT Hub-berichtroutering | Microsoft Documenten
description: Meer informatie over de querytaal voor het routeren van iot-hubberichten die u gebruiken om uitgebreide query's toe te passen op berichten om de gegevens te ontvangen die voor u van belang zijn.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271107"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Querysyntaxis voor IoT Hub-berichtroutering

Met berichtroutering kunnen gebruikers verschillende gegevenstypen routeren, namelijk apparaattelemetrieberichten, gebeurtenissen in de levenscyclus van het apparaat en gebeurtenissen van apparaattwee wijzigingen naar verschillende eindpunten. U ook uitgebreide query's toepassen op deze gegevens voordat u deze routert om de gegevens te ontvangen die voor u van belang zijn. In dit artikel wordt de querytaal voor het routeren van iot-hub-berichten beschreven en worden enkele veelvoorkomende querypatronen weergegeven.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met berichtroutering u de eigenschappen van het bericht en de berichttekst opvragen, evenals dubbele tags voor apparaten en dubbele eigenschappen van het apparaat. Als de berichttekst geen JSON is, kan berichtroutering het bericht nog steeds routeren, maar kunnen query's niet worden toegepast op de berichttekst.  Query's worden beschreven als Booleaanse expressies waarbij een Booleaanse true ervoor zorgt dat de query slaagt welke routes alle binnenkomende gegevens en Booleaanse false de query niet haalt en er geen gegevens worden gerouteerd. Als de expressie wordt geëvalueerd als null of ongedefinieerd, wordt deze als onwaar behandeld en wordt er een fout gegenereerd in diagnostische logboeken in geval van een fout. De syntaxis van de query moet correct zijn voor de route die moet worden opgeslagen en geëvalueerd.  

## <a name="message-routing-query-based-on-message-properties"></a>Berichtringsquery op basis van berichteigenschappen 

De IoT Hub definieert een [gemeenschappelijk formaat](iot-hub-devguide-messages-construct.md) voor alle device-to-cloud messaging voor interoperabiliteit tussen protocollen. IoT Hub-bericht gaat uit van de volgende JSON-weergave van het bericht. Systeemeigenschappen worden toegevoegd voor alle gebruikers en identificeren inhoud van het bericht. Gebruikers kunnen selectief toepassingseigenschappen aan het bericht toevoegen. We raden u aan unieke eigenschapsnamen te gebruiken, omdat IoT Hub device-to-cloud-berichten niet hoofdlettergevoelig zijn. Als u bijvoorbeeld meerdere eigenschappen met dezelfde naam hebt, verzendt IoT Hub slechts één van de eigenschappen.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systeemeigenschappen

Systeemeigenschappen helpen bij het identificeren van de inhoud en de bron van de berichten. 

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| Contenttype | tekenreeks | De gebruiker geeft het inhoudstype van het bericht op. Als u query's op de berichttekst wilt toestaan, moet deze waarde worden ingesteld toepassing/JSON. |
| contentCodering | tekenreeks | De gebruiker geeft het coderingstype van het bericht op. Toegestane waarden zijn UTF-8, UTF-16, UTF-32 als de contentType is ingesteld op toepassing/JSON. |
| iothub-verbinding-apparaat-id | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en identificeert de id van het apparaat. Als u `$connectionDeviceId`wilt query's gebruiken, gebruikt u . |
| iothub-enqueuedtime | tekenreeks | Deze waarde wordt ingesteld door IoT Hub en vertegenwoordigt de werkelijke tijd van het uiten van het bericht in UTC. Als u `enqueuedTime`wilt query's gebruiken, gebruikt u . |
| iothub-interface-naam | tekenreeks | Deze waarde wordt ingesteld door de gebruiker en vertegenwoordigt de naam van de digitale twin interface die het telemetriebericht implementeert. Als u `$interfaceName`wilt query's gebruiken, gebruikt u . Deze functie is beschikbaar als onderdeel van de openbare preview van [IoT Plug and Play.](../iot-pnp/overview-iot-plug-and-play.md) |

Zoals beschreven in de [IoT Hub-berichten,](iot-hub-devguide-messages-construct.md)zijn er extra systeemeigenschappen in een bericht. Naast **contentType,** **contentEncoding**en **inqueuedTime**kunnen ook de **verbindingDeviceId** en **connectionModuleId** worden opgevraagd.

### <a name="application-properties"></a>Toepassingseigenschappen

Toepassingseigenschappen zijn door de gebruiker gedefinieerde tekenreeksen die aan het bericht kunnen worden toegevoegd. Deze velden zijn optioneel.  

### <a name="query-expressions"></a>Queryexpressies

Een query op de eigenschappen van het `$` berichtsysteem moet vooraf worden bevestigd met het symbool. Query's op toepassingseigenschappen worden geopend met hun naam `$`en mogen niet vooraf zijn bevestigd met het symbool. Als een naam van `$`de toepassingseigenschap begint met , dan zal IoT Hub zoeken naar het in het systeem eigenschappen, en het is niet gevonden, dan zal het kijken in de eigenschappen van de toepassing. Bijvoorbeeld: 

Zoeken op inhoud van systeemeigenschapCodering 

```sql
$contentEncoding = 'UTF-8'
```

Ga als volgt te werk om query's op de verwerking van toepassingseigenschappenPath:

```sql
processingPath = 'hot'
```

Als u deze query's wilt combineren, u Booleaanse expressies en -functies gebruiken:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Een volledige lijst van ondersteunde operatoren en functies wordt weergegeven in [Expressie en voorwaarden](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Opdrachtringsquery voor berichten op basis van berichttekst

Als u query's op de berichttekst wilt inschakelen, moet het bericht zich in een JSON hebben gecodeerd in UTF-8, UTF-16 of UTF-32. De `contentType` moet worden `application/JSON` `contentEncoding` ingesteld op en op een van de ondersteunde UTF-coderingen in de eigenschap van het systeem. Als deze eigenschappen niet zijn opgegeven, evalueert IoT Hub de queryexpressie op de berichttekst niet. 

In het volgende voorbeeld ziet u hoe u een bericht maakt met een goed gevormde en gecodeerde JSON-body: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Dit laat zien hoe de codering van het lichaam in javascript te behandelen. Als u een voorbeeld in C#wilt zien, downloadt u de [Azure IoT C#-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Rits het bestand master.zip uit. Het Program.cs-bestand van de Visual Studio-oplossing *SimulatedDevice*laat zien hoe u berichten coderen en verzenden naar een IoT-hub. Dit is hetzelfde voorbeeld dat wordt gebruikt voor het testen van de berichtroutering, zoals uitgelegd in de [zelfstudie Berichtroutering](tutorial-routing.md). Aan de onderkant van Program.cs, het heeft ook een methode om te lezen in een van de gecodeerde bestanden, decoderen, en schrijf het terug uit als ASCII, zodat u het lezen. 


### <a name="query-expressions"></a>Queryexpressies

Een query op de berichttekst moet `$body`vooraf worden vastgelegd met de . U een hoofdverwijzing, verwijzing naar de hoofdarray of meerdere verwijzingen naar de hoofdtekst gebruiken in de queryexpressie. Uw queryexpressie kan ook een hoofdverwijzing combineren met de eigenschappen van het berichtsysteem en de verwijzing naar de eigenschappen van berichttoepassingen. De volgende zijn bijvoorbeeld alle geldige queryexpressies: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Routeringsquery voor berichten op basis van apparaattweeling 

Met berichtroutering u query's uitvoeren op [Device Twin-tags](iot-hub-devguide-device-twins.md) en -eigenschappen, die JSON-objecten zijn. Query's op module twin wordt ook ondersteund. Hieronder ziet u een voorbeeld van Device Twin-tags en -eigenschappen.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Queryexpressies

Een query op bericht tweeling moet `$twin`worden vooraf vastgesteld met de . Uw queryexpressie kan ook een dubbele tag of eigenschapsverwijzing combineren met een verwijzing naar de hoofdtekst, de eigenschappen van het berichtsysteem en de verwijzing naar de eigenschappen van berichttoepassingen. We raden u aan unieke namen in tags en eigenschappen te gebruiken, omdat de query niet hoofdlettergevoelig is. Dit is op zowel apparaattweelingen als moduletweelingen van toepassing. Ook afzien `twin`van `$twin` `body`het `$body`gebruik , , , of , als een eigenschap namen. De volgende zijn bijvoorbeeld alle geldige queryexpressies: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routeringsquery op hoofd- of apparaattweeling met een periode in de payload- of eigenschapsnaam wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het routeren van berichten](iot-hub-devguide-messages-d2c.md).
* Probeer de [zelfstudie voor het routeren van berichten](tutorial-routing.md).
