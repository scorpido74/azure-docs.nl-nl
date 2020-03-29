---
title: Gebruikersfuncties maken - in Azure Digital Twins | Microsoft Documenten
description: Gebruikersgedefinieerde functies, matchers en roltoewijzingen maken in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276939"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Gebruikersfuncties maken in Azure Digital Twins

[Met de door de gebruiker gedefinieerde functies](./concepts-user-defined-functions.md) kunnen gebruikers aangepaste logica configureren die moet worden uitgevoerd op basis van binnenkomende telemetrieberichten en ruimtelijke grafiekmetagegevens. Gebruikers kunnen ook gebeurtenissen naar vooraf gedefinieerde [eindpunten](./how-to-egress-endpoints.md)verzenden.

Deze gids loopt door een voorbeeld laten zien hoe te detecteren en alert op een lezing die een bepaalde temperatuur overschrijdt van ontvangen temperatuur gebeurtenissen.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Naslaginformatie over clientbibliotheek

Functies die beschikbaar zijn als helpermethoden in de door de gebruiker gedefinieerde functies, worden weergegeven in het referentiedocument voor de [clientbibliotheek.](./reference-user-defined-functions-client-library.md)

## <a name="create-a-matcher"></a>Een matcher maken

Matchers zijn grafiekobjecten die bepalen welke door de gebruiker gedefinieerde functies worden uitgevoerd voor een bepaald telemetriebericht.

- Geldige matcher conditie vergelijkingen:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Valid matcher condition targets:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

De volgende voorbeeldmatcher evalueert naar true op `"Temperature"` elke sensortelemetriegebeurtenis met als waarde voor het gegevenstype. U meerdere matchers maken op een door de gebruiker gedefinieerde functie door een geverifieerd HTTP POST-verzoek in te dienen om:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

Met JSON body:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Waarde | Vervangen door |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | De serverregio waarin het exemplaar wordt gehost |

## <a name="create-a-user-defined-function"></a>Een door de gebruiker gedefinieerde functie maken

Het maken van een door de gebruiker gedefinieerde functie omvat het maken van een meerdelige HTTP-aanvraag naar de Azure Digital Twins Management API's.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Nadat de matchers zijn gemaakt, uploadt u het functiefragment met de volgende geverifieerde HTTP POST-aanvraag voor meerdere delen naar:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Gebruik de volgende body:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Waarde | Vervangen door |
| --- | --- |
| USER_DEFINED_BOUNDARY | Een meerdelige naam van de inhoud |
| YOUR_SPACE_IDENTIFIER | De spatie-id  |
| YOUR_MATCHER_IDENTIFIER | De id van de matcher die u wilt gebruiken |

1. Controleer of de kopteksten: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Controleer of het lichaam meerdelijk is:

   - Het eerste deel bevat de vereiste door de gebruiker gedefinieerde functiemetagegevens.
   - Het tweede deel bevat de JavaScript-compute-logica.

1. Vervang in de sectie **USER_DEFINED_BOUNDARY** `YOUR_SPACE_IDENTIFIER`de waarden **spaceId** ( ) en **matchers** (`YOUR_MATCHER_IDENTIFIER`) ( ) vervangen.
1. Controleer of de door de JavaScript-gebruiker `Content-Type: text/javascript`gedefinieerde functie wordt geleverd als .

### <a name="example-functions"></a>Voorbeeldfuncties

Stel de sensor telemetrie lezing rechtstreeks **Temperature**voor de `sensor.DataType`sensor met gegevenstype Temperatuur , dat is:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

De **telemetrieparameter** legt de kenmerken **SensorId** en **Message** bloot, overeenkomend met een bericht dat door een sensor wordt verzonden. De parameter **executionContext** legt de volgende kenmerken bloot:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

In het volgende voorbeeld registreren we een bericht als de sensor telemetriemeting een vooraf gedefinieerde drempelwaarde overschrijdt. Als uw diagnostische instellingen zijn ingeschakeld in de instantie Azure Digital Twins, worden logboeken van door de gebruiker gedefinieerde functies ook doorgestuurd:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

De volgende code activeert een melding als het temperatuurniveau boven de vooraf gedefinieerde constante stijgt:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Lees het voorbeeld Van de snelheid van [de functie snel start voor](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)een complexer door de gebruiker gedefinieerd functiecode.

## <a name="create-a-role-assignment"></a>Een roltoewijzing maken

Maak een roltoewijzing voor de door de gebruiker gedefinieerde functie die moet worden uitgevoerd onder. Als er geen roltoewijzing bestaat voor de door de gebruiker gedefinieerde functie, heeft deze niet de juiste machtigingen om te communiceren met de Beheer-API of heeft deze toegang tot acties op grafiekobjecten. Acties die een door de gebruiker gedefinieerde functie kan uitvoeren, worden opgegeven en gedefinieerd via op rollen gebaseerd toegangsbeheer binnen de Azure Digital Twins Management API's. Door de gebruiker gedefinieerde functies kunnen bijvoorbeeld worden beperkt in het bereik door bepaalde rollen of bepaalde toegangsbeheerpaden op te geven. Lees voor meer informatie de documentatie [voor toegangsbeheer op basis van rollen.](./security-role-based-access-control.md)

1. [Query de System API](./security-create-manage-role-assignments.md#retrieve-all-roles) voor alle rollen om de rol-ID die u wilt toewijzen aan uw door de gebruiker gedefinieerde functie te krijgen. Doe dit door een geverifieerd HTTP GET-verzoek in te dienen om:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Houd de gewenste rol-ID. Het zal worden doorgegeven als de`YOUR_DESIRED_ROLE_IDENTIFIER`JSON lichaam attribuut **roleId** ( ) hieronder.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`( ) is de door de gebruiker gedefinieerde functie-ID die eerder is gemaakt.
1. Zoek de **path** waarde`YOUR_ACCESS_CONTROL_PATH`van pad ( ) `fullpath`door uw spaties op te vragen met .
1. Kopieer de `spacePaths` geretourneerde waarde. Dat gebruik je hieronder. Een geverifieerd HTTP GET-verzoek indienen bij:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_SPACE_NAME | De naam van de ruimte die u wilt gebruiken |

1. Plak de `spacePaths` geretourneerde waarde in **het pad** om een door de gebruiker gedefinieerde functietoewijzing te maken door een geverifieerd HTTP POST-verzoek in te dienen om:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    Met JSON body:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Waarde | Vervangen door |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | De id voor de gewenste rol |
    | YOUR_USER_DEFINED_FUNCTION_ID | De id voor de door de gebruiker gedefinieerde functie die u wilt gebruiken |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | De id die het door de`UserDefinedFunctionId`gebruiker gedefinieerde functietype opgeeft ( ) |
    | YOUR_ACCESS_CONTROL_PATH | Het toegangsbeheerpad |

>[!TIP]
> Lees het artikel [Hoe maak en beheer je roltoewijzingen](./security-create-manage-role-assignments.md) voor meer informatie over door de gebruiker gedefinieerde functie Management API-bewerkingen en eindpunten.

## <a name="send-telemetry-to-be-processed"></a>Telemetrie verzenden om te worden verwerkt

De sensor die in de grafiek ruimtelijke intelligentie is gedefinieerd, stuurt telemetrie. Op zijn beurt activeert de telemetrie de uitvoering van de door de gebruiker gedefinieerde functie die is geüpload. De gegevensverwerker pikt de telemetrie op. Vervolgens wordt een uitvoeringsplan gemaakt voor het aanroepen van de door de gebruiker gedefinieerde functie.

1. Haal de matchers op voor de sensor waaruit de meting is gegenereerd.
1. Afhankelijk van welke matchers met succes zijn geëvalueerd, haalt u de bijbehorende door de gebruiker gedefinieerde functies op.
1. Voer elke door de gebruiker gedefinieerde functie uit.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van Azure Digital Twins-eindpunten](./how-to-egress-endpoints.md) om gebeurtenissen naar te verzenden.

- Lees [Routeringsgebeurtenissen en -berichten](./concepts-events-routing.md)voor meer informatie over routering in Azure Digital Twins.

- Bekijk de [referentiedocumentatie van](./reference-user-defined-functions-client-library.md)de clientbibliotheek .