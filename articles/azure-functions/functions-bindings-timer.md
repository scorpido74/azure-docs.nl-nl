---
title: Timertrigger voor Azure-functies
description: Meer informatie over het gebruik van timertriggers in Azure-functies.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056415"
---
# <a name="timer-trigger-for-azure-functions"></a>Timertrigger voor Azure-functies 

In dit artikel wordt uitgelegd hoe u werkt met timertriggers in Azure-functies. Met een timertrigger u een functie volgens een schema uitvoeren. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

De timertrigger wordt geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) versie 2.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

De timertrigger wordt geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) versie 3.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/)

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die wordt uitgevoerd telkens wanneer de minuten een waarde hebben die door vijf deelbaar is (bijvoorbeeld als de functie om 18:57:00 uur begint, is de volgende voorstelling om 19:00:00 uur). Het [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt doorgegeven aan de functie.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een timertriggerbinding in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie schrijft een logboek met de vraag of deze functieaanroep te wijten is aan een gemiste gebeurtenis op het schema. Het [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt doorgegeven aan de functie.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Hier is de C# scriptcode:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld ziet u een timertriggerbinding in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie schrijft een logboek met de vraag of deze functieaanroep te wijten is aan een gemiste gebeurtenis op het schema. Een [timerobject](#usage) wordt doorgegeven aan de functie.

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Hier is de JavaScript-code:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een timertriggerbinding gebruikt waarvan de configuratie wordt beschreven in het *bestand function.json.* De werkelijke [Python-functie](functions-reference-python.md) die de binding gebruikt, wordt beschreven in het * __init.py-bestand.__* Het object dat in de functie is doorgegeven, is van het [object azure.functions.TimerRequest](/python/api/azure-functions/azure.functions.timerrequest). De functielogica schrijft naar de logboeken om aan te geven of de huidige aanroep te wijten is aan een gemiste gebeurtenis op het schema. 

Hier zijn de bindende gegevens in het *function.json-bestand:*

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Hier is de Python-code:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

# <a name="java"></a>[Java](#tab/java)

De volgende voorbeeldfunctie activeert en voert elke vijf minuten uit. De `@TimerTrigger` annotatie op de functie definieert het schema met dezelfde tekenreeksindeling als [CRON-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)het [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

De constructor van het kenmerk neemt `TimeSpan`een CRON-expressie of een . U kunt `TimeSpan` alleen gebruiken als de functie-app wordt uitgevoerd op een App Service-abonnement. `TimeSpan`wordt niet ondersteund voor verbruik of elastische premiumfuncties.

In het volgende voorbeeld wordt een CRON-expressie weergegeven:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De `@TimerTrigger` annotatie op de functie definieert het schema met dezelfde tekenreeksindeling als [CRON-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `TimerTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet worden ingesteld op "timerTrigger". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op "in". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die het timerobject in functiecode vertegenwoordigt. | 
|**Schema**|**Expressie plannen**|Een [CRON-expressie](#ncrontab-expressions) of een [TimeSpan-waarde.](#timespan) A `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een App Service Plan. U de schema-expressie in een app-instelling plaatsen en **%** deze eigenschap instellen op de naam van de app-instelling die is verpakt in borden, zoals in dit voorbeeld: %ScheduleAppSetting%. |
|**runOnStartup**|**RunonStartup**|Als `true`, wordt de functie aangeroepen wanneer de runtime begint. De runtime wordt bijvoorbeeld gestart wanneer de functie-app wordt opgehaald nadat deze is inactief als gevolg van inactiviteit. wanneer de functie-app opnieuw wordt opgestart als gevolg van functiewijzigingen en wanneer de functie-app wordt geschaald. Dus **runOnStartup** moet zelden of `true`nooit worden ingesteld op , vooral in de productie. |
|**useMonitor**|**UseMonitor**|Instellen `true` op `false` of om aan te geven of de planning moet worden gecontroleerd. Planningsbewaking blijft optredens van schema's om ervoor te zorgen dat het schema correct wordt onderhouden, zelfs wanneer functie-app-exemplaren opnieuw worden opgestart. Als dit niet expliciet is `true` ingesteld, geldt de standaardinstelling voor schema's met een herhalingsinterval dat groter is dan of gelijk is aan 1 minuut. Voor schema's die meer dan één `false`keer per minuut activeren, is de standaardinstelling .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> We raden u aan `true` **runOnStartup** niet in te stellen op in productie. Met deze instelling wordt code uitgevoerd op zeer onvoorspelbare tijden. In bepaalde productie-instellingen kunnen deze extra uitvoeringen leiden tot aanzienlijk hogere kosten voor apps die worden gehost in verbruiksplannen. Met **runOnStartup** ingeschakeld wordt de trigger bijvoorbeeld aangeroepen wanneer de functie-app wordt geschaald. Zorg ervoor dat u het productiegedrag van uw functies volledig begrijpt voordat **u runOnStartup** in productie inschakelt.   

## <a name="usage"></a>Gebruik

Wanneer een timertriggerfunctie wordt aangeroepen, wordt een timerobject doorgegeven aan de functie. De volgende JSON is een voorbeeldweergave van het timerobject.

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

De `IsPastDue` eigenschap `true` is wanneer de huidige functie aanroep is later dan gepland. Een herstart van een functie-app kan er bijvoorbeeld toe leiden dat een aanroep wordt gemist.

## <a name="ncrontab-expressions"></a>NCRONTAB-expressies 

Azure Functions gebruikt de [NCronTab-bibliotheek](https://github.com/atifaziz/NCrontab) om NCRONTAB-expressies te interpreteren. Een NCRONTAB-expressie is vergelijkbaar met een CRON-expressie, behalve dat deze een extra zesde veld aan het begin bevat dat in enkele seconden voor tijdprecisie moet worden gebruikt:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>"0 5 * * * *"</nobr>|om hh:05:00 waar hh is elk uur (eenmaal per uur)|
|Alle waarden`*`( )|<nobr>"0 * 5 * * *"</nobr>|om 5:mm:00 elke dag, waar mm is elke minuut van het uur (60 keer per dag)|
|Een bereik`-` (operator)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05,hh:mm:06, en hh:mm:07 waar hh:mm elke minuut van elk uur is (3 keer per minuut)|
|Een set waarden`,` (operator)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05,hh:mm:08, en hh:mm:10 waar hh:mm elke minuut van elk uur is (3 keer per minuut)|
|Een intervalwaarde`/` (operator)|<nobr>"0 */5 * * * *"</nobr>|hh:00:00, hh:05:00, hh:10:00, enzovoort, enzovoort door hh:55:00 waar hh elk uur is (12 keer per uur)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Voorbeelden van NCRONTAB

Hier volgen enkele voorbeelden van NCRONTAB-expressies die u gebruiken voor de timertrigger in Azure-functies.

|Voorbeeld|Wanneer geactiveerd  |
|---------|---------|
|`"0 */5 * * * *"`|eens in de vijf minuten|
|`"0 0 * * * *"`|eenmaal op de top van elk uur|
|`"0 0 */2 * * *"`|eens in de twee uur|
|`"0 0 9-17 * * *"`|eens per uur van 9.00 tot 17.00 uur|
|`"0 30 9 * * *"`|elke dag om 9:30 uur|
|`"0 30 9 * * 1-5"`|om 9:30 uur elke weekdag|
|`"0 30 9 * Jan Mon"`|om 9:30 uur elke maandag in januari|


### <a name="ncrontab-time-zones"></a>NCRONTAB-tijdzones

De getallen in een CRON-expressie verwijzen naar een tijd en datum, niet naar een tijdspanne. Een 5 in het `hour` veld verwijst bijvoorbeeld naar 5:00 AM, niet elke 5 uur.

De standaardtijdzone die wordt gebruikt bij de CRON-expressies is Gecoördineerde universele tijd (UTC). Als u uw CRON-expressie wilt hebben op basis van `WEBSITE_TIME_ZONE`een andere tijdzone, maakt u een app-instelling voor uw functie-app met de naam . Stel de waarde in op de naam van de gewenste tijdzone zoals weergegeven in de [Microsoft Time Zone Index](https://technet.microsoft.com/library/cc749073).

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`wordt momenteel niet ondersteund op het Linux-verbruiksplan.

*Eastern Standard Time* is bijvoorbeeld UTC-05:00. Als u uw timertrigger elke dag om 10:00 UUR EST wilt laten activeren, gebruikt u de volgende NCRONTAB-expressie die verantwoordelijk is voor utc-tijdzone:

```
"0 0 15 * * *"
``` 

Of maak een app-instelling `WEBSITE_TIME_ZONE` voor uw functie-app met de naam en stel de waarde in **op Eastern Standard Time.**  Gebruikt vervolgens de volgende NCRONTAB-expressie: 

```
"0 0 10 * * *"
``` 

Wanneer u `WEBSITE_TIME_ZONE`deze gebruikt, wordt de tijd aangepast voor tijdsveranderingen in de specifieke tijdzone, zoals zomertijd. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een App Service Plan.

In tegenstelling tot `TimeSpan` een CRON-expressie geeft een waarde het tijdsinterval tussen elke functieaanroep op. Wanneer een functie is voltooid nadat deze langer is uitgevoerd dan het opgegeven interval, roept de timer de functie onmiddellijk opnieuw aan.

Uitgedrukt als een `TimeSpan` tekenreeks, `hh:mm:ss` `hh` het formaat is wanneer is minder dan 24. Wanneer de eerste twee cijfers 24 of `dd:hh:mm`hoger zijn, is de notatie . Hier volgen enkele voorbeelden:

|Voorbeeld |Wanneer geactiveerd  |
|---------|---------|
|"01:00:00" | elk uur        |
|"00:01:00"|elke minuut         |
|"24:00:00" | elke 24 dagen        |
|"1.00:00:00" | Elke dag        |

## <a name="scale-out"></a>Uitschalen

Als een functie-app wordt geschaald naar meerdere exemplaren, wordt slechts één exemplaar van een timergeactiveerde functie uitgevoerd in alle instanties.

## <a name="function-apps-sharing-storage"></a>Functie-apps delen opslag

Als u opslagaccounts deelt in functie-apps die niet zijn geïmplementeerd in de app-service, moet u mogelijk expliciet host-id toewijzen aan elke app.

| Versie functies | Instelling                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (en hoger)  | `AzureFunctionsWebHost__hostid`Omgevingsvariabele |
| 1.x               | `id`in *host.json*                                  |

U de identificatiewaarde weglaten of de identificatieconfiguratie van elke functie-app handmatig instellen op een andere waarde.

De timertrigger maakt gebruik van een opslagvergrendeling om ervoor te zorgen dat er slechts één timerinstantie is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps dezelfde identificerende configuratie delen en elk een timertrigger gebruikt, wordt er slechts één timer uitgevoerd.

## <a name="retry-behavior"></a>Gedrag opnieuw proberen

In tegenstelling tot de wachtrijtrigger probeert de timertrigger het niet opnieuw nadat een functie is mislukt. Wanneer een functie mislukt, wordt deze pas de volgende keer in de planning weer gebeld.

## <a name="troubleshooting"></a>Problemen oplossen

Zie [Problemen met timergeactiveerde functies niet afvuren](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)voor informatie over wat u moet doen wanneer de trigger voor de timer niet werkt zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ga naar een quickstart met een timertrigger](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
