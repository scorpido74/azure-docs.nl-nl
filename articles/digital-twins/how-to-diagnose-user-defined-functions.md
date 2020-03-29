---
title: UDF's debuggen - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over aanbevolen benaderingen voor foutopsporingsfuncties door gebruikers in Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511634"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Gebruikersgedefinieerde functies debuggen in Azure Digital Twins

In dit artikel wordt samengevat hoe u door gebruikers gedefinieerde functies in Azure Digital Twins diagnosticeren en debuggen. Vervolgens identificeert het enkele van de meest voorkomende scenario's die worden gevonden bij het debuggen ervan.

>[!TIP]
> Lees [Hoe u controle en logboekregistratie configureert](./how-to-configure-monitoring.md) voor meer informatie over het instellen van foutopsporingshulpprogramma's in Azure Digital Twins met behulp van activiteitslogboeken, diagnostische logboeken en Azure Monitor.

## <a name="debug-issues"></a>Problemen met foutopsporing

Als u weet hoe u problemen binnen Azure Digital Twins diagnosticeren, u problemen effectief analyseren, de oorzaken van problemen identificeren en passende oplossingen voor deze problemen bieden.

Daartoe worden verschillende registratie-, analyse- en diagnostische tools geleverd.

### <a name="enable-logging-for-your-instance"></a>Logboekregistratie inschakelen voor uw instantie

Azure Digital Twins ondersteunt robuuste logboekregistratie, monitoring en analyse. Ontwikkelaars van oplossingen kunnen Azure Monitor-logboeken, diagnostische logboeken, activiteitslogboeken en andere services gebruiken om de complexe bewakingsbehoeften van een IoT-app te ondersteunen. Logopties kunnen worden gecombineerd om records op te vragen of weer te geven in verschillende services en om gedetailleerde logboekdekking te bieden voor veel services.

* Lees Voor het registreren van configuratie die specifiek is voor Azure Digital Twins, [leest u Hoe u bewaking en logboekregistratie configureert.](./how-to-configure-monitoring.md)
* Raadpleeg het overzicht [van Azure Monitor](../azure-monitor/overview.md) voor meer informatie over krachtige logboekinstellingen die zijn ingeschakeld via Azure Monitor.
* Bekijk het artikel [Loggegevens verzamelen en gebruiken uit uw Azure-bronnen](../azure-monitor/platform/platform-logs-overview.md) voor het configureren van diagnostische logboekinstellingen in Azure Digital Twins via de Azure-portal, Azure CLI of PowerShell.

Zodra u bent geconfigureerd, u alle logboekcategorieën, statistieken selecteren en krachtige azure monitor-logboekanalysewerkruimten gebruiken om uw foutopsporingsinspanningen te ondersteunen.

### <a name="trace-sensor-telemetry"></a>Telemetrie van de traceersensor

Als u de telemetrie van de sensor wilt traceren, controleert u of diagnostische instellingen zijn ingeschakeld voor uw Azure Digital Twins-exemplaar. Zorg er vervolgens voor dat alle gewenste logboekcategorieën zijn geselecteerd. Controleer ten slotte of de gewenste logboeken naar Azure Monitor-logboeken worden verzonden.

Als u een telemetriebericht van de sensor wilt koppelen aan de respectievelijke logboeken, u een correlatie-id opgeven voor de gebeurtenisgegevens die worden verzonden. Stel hiervoor de `x-ms-client-request-id` eigenschap in op een GUID.

Nadat u telemetrie hebt verzonden, opent u Azure Monitor-logboekanalyses om logboeken op te vragen met de ingestelde correlatie-id:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Querywaarde | Vervangen door |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | De correlatie-id die is opgegeven op de gebeurtenisgegevens |

Ga als volgt te werk om alle recente telemetrielogboekenquery te lezen:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Als u logboekregistratie inschakelt voor uw door de gebruiker gedefinieerde functie, worden deze logboeken weergegeven in de instantie logboekanalyse met de categorie `UserDefinedFunction`. Voer de volgende queryvoorwaarde in logboekanalyses in:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Lees [Aan de slag met query's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over krachtige querybewerkingen.

## <a name="identify-common-issues"></a>Veelvoorkomende problemen identificeren

Zowel het diagnosticeren als het identificeren van veelvoorkomende problemen zijn belangrijk bij het oplossen van problemen. Verschillende problemen die vaak worden aangetroffen bij het ontwikkelen van door de gebruiker gedefinieerde functies worden samengevat in de volgende subsecties.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Controleren of er een roltoewijzing is gemaakt

Zonder een roltoewijzing die is gemaakt in de Beheer-API, heeft de door de gebruiker gedefinieerde functie geen toegang tot acties zoals het verzenden van meldingen, het ophalen van metagegevens en het instellen van berekende waarden binnen de topologie.

Controleer of er een roltoewijzing bestaat voor uw door de gebruiker gedefinieerde functie via uw Management API:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parameterwaarde | Vervangen door |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | De id van de door de gebruiker gedefinieerde functie om roltoewijzingen op te halen voor|

Meer informatie [over het maken van een roltoewijzing voor uw door de gebruiker gedefinieerde functie,](./how-to-user-defined-functions.md)als er geen roltoewijzingen bestaan.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Controleren of de matcher werkt voor de telemetrie van een sensor

Met de volgende oproep tegen de BeheerAPI van Azure Digital Twins-exemplaren u bepalen of een bepaalde matcher van toepassing is op de opgegeven sensor.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | De ID van de matcher die u wilt evalueren |
| *YOUR_SENSOR_IDENTIFIER* | De ID van de sensor die u wilt evalueren |

Reactie:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Controleren wat een sensor activeert

Met de volgende oproep tegen de Azure Digital Twins Management API's u de id's bepalen van uw door de gebruiker gedefinieerde functies die worden geactiveerd door de binnenkomende telemetrie van de gegeven sensor:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | De ID van de sensor om telemetrie te verzenden |

Reactie:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Probleem met het ontvangen van meldingen

Wanneer u geen meldingen ontvangt van de door de gebruiker gedefinieerde functie, moet u controleren of de parameter topologieobject type overeenkomt met het type id dat wordt gebruikt.

**Onjuist** Voorbeeld:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Dit scenario ontstaat omdat de gebruikte id verwijst naar een `Space`sensor terwijl het opgegeven topologieobjecttype .

**Correct** Voorbeeld:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

De eenvoudigste manier om dit probleem niet `Notify` tegen te komen, is door de methode op het metagegevensobject te gebruiken.

Voorbeeld:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Algemene diagnostische uitzonderingen

Als u diagnostische instellingen inschakelt, u de volgende veelvoorkomende uitzonderingen tegenkomen:

1. **Beperking:** als uw door de gebruiker gedefinieerde functie de uitvoeringssnelheidlimieten overschrijdt die in het artikel [Servicelimieten](./concepts-service-limits.md) worden beschreven, wordt deze beperkt. Er worden geen verdere bewerkingen uitgevoerd totdat de beperkingslimieten zijn verlopen.

1. **Gegevens niet gevonden:** als uw door de gebruiker gedefinieerde functie probeert toegang te krijgen tot metagegevens die niet bestaan, mislukt de bewerking.

1. **Niet geautoriseerd:** als uw door de gebruiker gedefinieerde functie geen roltoewijzingsset heeft of onvoldoende toestemming heeft om toegang te krijgen tot bepaalde metagegevens van de topologie, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het inschakelen [van monitoring en logboeken](./how-to-configure-monitoring.md) in Azure Digital Twins.

- Lees het [artikel overzicht van azure activity log](../azure-monitor/platform/platform-logs-overview.md) voor meer Azure-logboekopties.
