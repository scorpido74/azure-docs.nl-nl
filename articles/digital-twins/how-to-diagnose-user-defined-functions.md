---
title: Fout opsporing voor Udf's in azure Digital Apparaatdubbels | Microsoft Docs
description: Richt lijnen over het opsporen van fouten in Udf's in azure Digital Apparaatdubbels.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 24dc01d47bece4191d1b142a58c4ad7b6d9fb6cf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876568"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Fout opsporing door door de gebruiker gedefinieerde functies in azure Digital Apparaatdubbels

In dit artikel wordt een overzicht gegeven van de manier waarop door de gebruiker gedefinieerde functies in azure Digital Apparaatdubbels worden vastgesteld en opgespoord. Vervolgens worden enkele van de meest voorkomende scenario's geïdentificeerd die zijn gevonden bij het opsporen van fouten.

>[!TIP]
> Lees [hoe u bewaking en logboek registratie kunt configureren](./how-to-configure-monitoring.md) voor meer informatie over het instellen van hulpprogram ma's voor fout opsporing in azure Digital apparaatdubbels met behulp van activiteiten logboeken, Diagnostische logboeken en Azure monitor.

## <a name="debug-issues"></a>Problemen met fout opsporing

Als u weet hoe u problemen kunt vaststellen in azure Digital Apparaatdubbels, hebt u de mogelijkheid om problemen effectief te analyseren, de oorzaken van problemen te identificeren en de juiste oplossingen te bieden.

Er zijn verschillende hulpprogram ma's voor logboek registratie, analyse en diagnose.

### <a name="enable-logging-for-your-instance"></a>Logboek registratie inschakelen voor uw exemplaar

Azure Digital Apparaatdubbels ondersteunt robuuste logboek registratie, bewaking en analyses. Ontwikkel aars van oplossingen kunnen Azure Monitor-logboeken, Diagnostische logboeken, activiteiten logboeken en andere services gebruiken om de complexe bewakings behoeften van een IoT-app te ondersteunen. Opties voor logboek registratie kunnen worden gecombineerd om records op te vragen of weer te geven in verschillende services en om een gedetailleerde logboek registratie te bieden voor veel services.

* Lees [hoe u bewaking en logboek registratie configureert](./how-to-configure-monitoring.md)voor logboek registratie configuratie die specifiek is voor Azure Digital apparaatdubbels.
* Raadpleeg het [Azure monitor](../azure-monitor/overview.md) -overzicht voor meer informatie over de krachtige logboek instellingen die via Azure monitor kunnen worden ingeschakeld.
* Raadpleeg het artikel [gegevens verzamelen en gebruiken van uw Azure-resources](../azure-monitor/platform/diagnostic-logs-overview.md) voor het configureren van instellingen voor Diagnostische logboeken in azure Digital Apparaatdubbels via Azure Portal, Azure CLI of Power shell.

Na de configuratie kunt u alle logboek categorieën, metrische gegevens en krachtige Azure Monitor Log Analytics-werk ruimten gebruiken ter ondersteuning van uw fout opsporing.

### <a name="trace-sensor-telemetry"></a>Telemetrie tracerings sensor

Als u de telemetrie van de sensor wilt traceren, controleert u of de diagnostische instellingen zijn ingeschakeld voor uw Azure Digital Apparaatdubbels-exemplaar. Zorg er vervolgens voor dat alle gewenste logboek categorieën zijn geselecteerd. Controleer ten slotte of de gewenste logboeken naar Azure Monitor logboeken worden verzonden.

Als u een telemetrie-bericht sensor wilt vergelijken met de respectieve logboeken, kunt u een correlatie-ID opgeven voor de gebeurtenis gegevens die worden verzonden. Als u dit wilt doen, `x-ms-client-request-id` stelt u de eigenschap in op een GUID.

Open Azure Monitor Log Analytics na het verzenden van telemetrie om logboeken te zoeken met de set correlatie-ID:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Query waarde | Vervangen door |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | De correlatie-ID die is opgegeven voor de gebeurtenis gegevens |

Als u logboek registratie inschakelt voor uw door de gebruiker gedefinieerde functie, worden deze logboeken weer gegeven in `UserDefinedFunction`uw log Analytics-exemplaar met de categorie. Als u deze wilt ophalen, voert u de volgende query voorwaarde in log Analytics in:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Lees [aan de slag met query's](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over krachtige query bewerkingen.

## <a name="identify-common-issues"></a>Veelvoorkomende problemen identificeren

Het is belang rijk om problemen op te lossen en veelvoorkomende problemen op te sporen. Verschillende problemen die vaak optreden bij het ontwikkelen van door de gebruiker gedefinieerde functies, worden in de volgende subsecties samenvatten.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Controleren of een roltoewijzing is gemaakt

Zonder dat er een roltoewijzing in de beheer-API is gemaakt, heeft de door de gebruiker gedefinieerde functie geen toegang om acties uit te voeren, zoals het verzenden van meldingen, het ophalen van meta gegevens en het instellen van berekende waarden in de topologie.

Controleer of er een roltoewijzing bestaat voor uw door de gebruiker gedefinieerde functie via uw beheer-API:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parameterwaarde | Vervangen door |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | De ID van de door de gebruiker gedefinieerde functie om roltoewijzingen op te halen voor|

Meer informatie [over het maken van een roltoewijzing voor uw door de gebruiker gedefinieerde functie](./how-to-user-defined-functions.md)als er geen roltoewijzingen zijn.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Controleer of de overeenkomst werkt voor de telemetrie van een sensor

Met de volgende aanroep van uw Azure Digital Apparaatdubbels instances-beheer-API kunt u bepalen of een bepaalde overeenkomst van toepassing is voor de gegeven sensor.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | De ID van de overeenkomst die u wilt evalueren |
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

### <a name="check-what-a-sensor-triggers"></a>Controleren welke sensor wordt geactiveerd

Met de volgende aanroep van de Azure Digital Apparaatdubbels Management-Api's kunt u de id's bepalen van uw door de gebruiker gedefinieerde functies die zijn geactiveerd door de binnenkomende telemetrie van de opgegeven sensor:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parameter | Vervangen door |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | De ID van de sensor voor het verzenden van telemetrie |

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

Als u geen meldingen van de geactiveerde door de gebruiker gedefinieerde functie ontvangt, controleert u of de object type parameter van de topologie overeenkomt met het type id dat wordt gebruikt.

**Onjuist** Hierbij

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Dit scenario doet zich voor omdat de gebruikte id naar een sensor verwijst terwijl het opgegeven topologie object type `Space`is.

**Juiste** Hierbij

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

De eenvoudigste manier om dit probleem niet op te lossen is het `Notify` gebruik van de-methode voor het meta gegevens object.

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

## <a name="common-diagnostic-exceptions"></a>Veelvoorkomende diagnostische uitzonde ringen

Als u Diagnostische instellingen inschakelt, kunnen deze algemene uitzonde ringen optreden:

1. **Beperking**: als uw door de gebruiker gedefinieerde functie de limieten voor de uitvoerings snelheid overschrijdt die worden beschreven in het artikel [service limieten](./concepts-service-limits.md) , wordt dit beperkt. Er worden geen verdere bewerkingen uitgevoerd totdat de beperkings limieten verlopen.

1. **Geen gegevens gevonden**: als uw door de gebruiker gedefinieerde functie probeert toegang te krijgen tot meta gegevens die niet bestaan, mislukt de bewerking.

1. **Niet gemachtigd**: als uw door de gebruiker gedefinieerde functie geen roltoewijzing heeft ingesteld of onvoldoende machtigingen heeft om toegang te krijgen tot bepaalde meta gegevens uit de topologie, mislukt de bewerking.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het inschakelen van [controle en logboeken](./how-to-configure-monitoring.md) in azure Digital apparaatdubbels.

- Lees het artikel [overzicht van Azure-activiteiten logboek](../azure-monitor/platform/activity-logs-overview.md) voor meer opties voor Azure-logboek registratie.
