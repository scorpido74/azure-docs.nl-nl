---
title: Timer trigger voor Azure Functions
description: Meer informatie over het gebruik van timer triggers in Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functies, gebeurtenis verwerking, dynamische compute, serverloze architectuur
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 6ac83a054b146b9d515386332779c4fe94cde7c3
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263429"
---
# <a name="timer-trigger-for-azure-functions"></a>Timer trigger voor Azure Functions 

In dit artikel wordt uitgelegd hoe u met timer triggers in Azure Functions kunt werken. Met een timer trigger kunt u een functie uitvoeren volgens een schema. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De timer trigger is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakketten-functions 2. x

De timer trigger is opgenomen in het pakket [micro soft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Voorbeeld

Zie het taalspecifieke voor beeld:

* [C#](#c-example)
* [C#script (. CSX)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#Hierbij

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die wordt uitgevoerd elke keer dat de minuten een waarde hebben die deelbaar is door vijf (bijvoorbeeld als de functie wordt gestart om 18:57:00, de volgende prestaties op 19:00:00). Het [`TimerInfo`-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt door gegeven aan de functie.

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

### <a name="c-script-example"></a>C#script voorbeeld

In het volgende voor beeld ziet u een binding van een timer trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie wordt een logboek geschreven dat aangeeft of deze functie wordt aangeroepen vanwege een gemiste schema gebeurtenis. Het [`TimerInfo`-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt door gegeven aan de functie.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Dit is de C# script code:

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

### <a name="f-example"></a>F#Hierbij

In het volgende voor beeld ziet u een binding van een timer trigger in een *Function. json* -bestand en een [ F# script functie](functions-reference-fsharp.md) die gebruikmaakt van de binding. Met de functie wordt een logboek geschreven dat aangeeft of deze functie wordt aangeroepen vanwege een gemiste schema gebeurtenis. Het [`TimerInfo`-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) object wordt door gegeven aan de functie.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Dit is de F# script code:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Java-voor beeld

In het volgende voor beeld wordt de functie geactiveerd en wordt elke vijf minuten uitgevoerd. De `@TimerTrigger`-annotatie voor de functie definieert het schema met dezelfde teken reeks indeling als [cron-expressies](https://en.wikipedia.org/wiki/Cron#CRON_expression).

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

### <a name="javascript-example"></a>Java script-voor beeld

In het volgende voor beeld ziet u een binding van een timer trigger in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie wordt een logboek geschreven dat aangeeft of deze functie wordt aangeroepen vanwege een gemiste schema gebeurtenis. Er wordt een [Timer object](#usage) door gegeven aan de functie.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Dit is de Java script-code:

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

### <a name="python-example"></a>Python-voor beeld

In het volgende voor beeld wordt een timer trigger binding gebruikt waarvan de configuratie wordt beschreven in het bestand *Function. json* . De daad werkelijke [python-functie](functions-reference-python.md) die gebruikmaakt van de binding, wordt beschreven in het bestand  *__init__. py* . Het object dat is door gegeven aan de functie, is van het type [Azure. functions. TimerRequest-object](/python/api/azure-functions/azure.functions.timerrequest). De functie logica schrijft naar de logboeken die aangeven of de huidige aanroep wordt veroorzaakt door een gemist schema voorval. 

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Dit is de python-code:

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

## <a name="attributes"></a>Kenmerken

Gebruik in [ C# class libraries](functions-dotnet-class-library.md)het [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

De constructor van het kenmerk heeft een CRON-expressie of een `TimeSpan`. U kunt `TimeSpan` alleen gebruiken als de functie-app wordt uitgevoerd op een App Service plan. In het volgende voor beeld ziet u een CRON-expressie:

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

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `TimerTrigger`.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | n.v.t. | Moet worden ingesteld op ' Timer trigger '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | n.v.t. | Moet worden ingesteld op in. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | n.v.t. | De naam van de variabele die het Timer object in functie code vertegenwoordigt. | 
|**schedule**|**ScheduleExpression**|Een [cron-expressie](#ncrontab-expressions) of een [time span](#timespan) -waarde. Een `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een App Service plan. U kunt de schema-expressie in een app-instelling plaatsen en deze eigenschap instellen op de naam van de app-instelling in **%-** tekens, zoals in dit voor beeld:% ScheduleAppSetting%. |
|**runOnStartup**|**RunOnStartup**|Als `true`, de functie wordt aangeroepen wanneer de runtime wordt gestart. De runtime wordt bijvoorbeeld gestart wanneer de functie-app wordt geactiveerd nadat de inactiviteit is voltooid. Wanneer de functie-app opnieuw wordt gestart vanwege functie wijzigingen en wanneer de functie-app wordt geschaald. Daarom moet **runOnStartup** zelden worden ingesteld op `true`, met name bij de productie. |
|**useMonitor**|**UseMonitor**|Ingesteld op `true` of `false` om aan te geven of het schema moet worden bewaakt. Het plannen van de controle houdt in dat het schema wordt gebruikt om ervoor te zorgen dat het schema goed wordt onderhouden, zelfs wanneer de functie-app-exemplaren opnieuw worden gestart. Als niet expliciet is ingesteld, is de standaard waarde `true` voor schema's met een terugkeer patroon dat groter is dan of gelijk is aan 1 minuut. Voor schema's die meer dan één keer per minuut activeren, wordt de standaard waarde `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> We raden u aan om **runOnStartup** in te stellen op `true` in productie. Met deze instelling wordt de code op zeer onvoorspelbare tijden uitgevoerd. In bepaalde productie-instellingen kan deze extra uitvoeringen leiden tot aanzienlijk hogere kosten voor apps die worden gehost in verbruiks abonnementen. Als **runOnStartup** is ingeschakeld, wordt de trigger bijvoorbeeld aangeroepen wanneer uw functie-app wordt geschaald. Zorg ervoor dat u het productie gedrag van uw functies volledig begrijpt voordat u **runOnStartup** in productie inschakelt.   

## <a name="usage"></a>Gebruik

Wanneer een timer functie wordt geactiveerd, wordt er een timer object door gegeven aan de functie. De volgende JSON is een voorbeeld weergave van het object Timer.

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

De eigenschap `IsPastDue` is `true` wanneer de huidige functie aanroep later is dan gepland. Het opnieuw starten van een functie-app kan ertoe leiden dat een aanroep wordt gemist.

## <a name="ncrontab-expressions"></a>NCRONTAB-expressies 

Azure Functions maakt gebruik van de [NCronTab](https://github.com/atifaziz/NCrontab) -bibliotheek om NCronTab-expressies te interpreteren. Een NCRONTAB-exppression is vergelijkbaar met een CRON-expressie, behalve dat het een extra zesde veld bevat die aan het begin moet worden gebruikt voor tijd nauwkeurigheid in seconden:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Elk veld kan een van de volgende typen waarden hebben:

|Type  |Voorbeeld  |Wanneer geactiveerd  |
|---------|---------|---------|
|Een specifieke waarde |<nobr>"0 5 * * * *"</nobr>|op hh: 05:00 waarbij UU elk uur (één keer per uur)|
|Alle waarden (`*`)|<nobr>"0 * 5 * * *"</nobr>|bij 5: mm: 00 elke dag, waarbij mm elke minuut van het uur is (60 keer per dag)|
|Een bereik (`-`-operator)|<nobr>"5-7 * * * * *"</nobr>|op uu: mm: 05, uu: mm: 06 en uu: mm: 07 waarbij UU: mm elke minuut van elk uur (3 keer per minuut)|
|Een reeks waarden (de operator `,`)|<nobr>"5, 8, 10 * * * * *"</nobr>|op uu: mm: 05, uu: mm: 08 en uu: mm: 10 waarbij UU: mm elke minuut van elk uur (3 keer per minuut)|
|Een interval waarde (`/`-operator)|<nobr>"0 */5 * * * *"</nobr>|op hh: 05:00, uu: 10:00, uu: 15:00, enzovoort: 55:00 waarbij UU elk uur (12 keer per uur)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-voor beelden

Hier volgen enkele voor beelden van NCRONTAB-expressies die u kunt gebruiken voor de timer trigger in Azure Functions.

|Voorbeeld|Wanneer geactiveerd  |
|---------|---------|
|`"0 */5 * * * *"`|eenmaal per vijf minuten|
|`"0 0 * * * *"`|eenmaal aan de bovenkant van elk uur|
|`"0 0 */2 * * *"`|eenmaal per twee uur|
|`"0 0 9-17 * * *"`|eenmaal per uur van 9 tot 5 uur|
|`"0 30 9 * * *"`|elke dag om 9:30 uur|
|`"0 30 9 * * 1-5"`|om 9:30 uur om de dag|
|`"0 30 9 * Jan Mon"`|om 9:30 uur elke maandag in januari|


### <a name="ncrontab-time-zones"></a>NCRONTAB tijd zones

De getallen in een CRON-expressie verwijzen naar een datum en tijd, niet een tijds Panne. Bijvoorbeeld, een 5 in het veld `hour` verwijst naar 5:00 uur, niet om de vijf uur.

De standaardtijd zone die wordt gebruikt in de CRON-expressies is Coordinated Universal Time (UTC). Als u wilt dat uw CRON-expressie op basis van een andere tijd zone wordt gebruikt, maakt u een app-instelling voor de functie-app met de naam `WEBSITE_TIME_ZONE`. Stel de waarde in op de naam van de gewenste tijd zone, zoals weer gegeven in de [micro soft-tijd zone-index](https://technet.microsoft.com/library/cc749073). 

Bijvoorbeeld, *Eastern Standard Time* is UTC-05:00. Als u wilt dat uw timer trigger wordt geactiveerd om 10:00 uur EST elke dag, gebruikt u de volgende NCRONTAB-expressie die accounts voor UTC-tijd zone:

```
"0 0 15 * * *"
``` 

Of maak een app-instelling voor de functie-app met de naam `WEBSITE_TIME_ZONE` en stel de waarde in op **Eastern Standard Time**.  Gebruikt vervolgens de volgende NCRONTAB-expressie: 

```
"0 0 10 * * *"
``` 

Wanneer u `WEBSITE_TIME_ZONE` gebruikt, wordt de tijd aangepast voor tijd wijzigingen in de specifieke tijd zone, zoals zomer tijd. 

## <a name="timespan"></a>Duur

 Een `TimeSpan` kan alleen worden gebruikt voor een functie-app die wordt uitgevoerd op een App Service plan.

In tegens telling tot een CRON-expressie geeft een `TimeSpan`-waarde het tijds interval tussen elke functie aanroep. Wanneer een functie wordt voltooid nadat deze langer dan het opgegeven interval is uitgevoerd, roept de timer de functie opnieuw aan.

Uitgedrukt als een teken reeks, wordt de `TimeSpan`-indeling `hh:mm:ss` wanneer `hh` kleiner is dan 24. Als de eerste twee cijfers 24 of groter zijn, is de notatie `dd:hh:mm`. Hier volgen enkele voorbeelden:

|Voorbeeld |Wanneer geactiveerd  |
|---------|---------|
|"01:00:00" | elk uur        |
|"00:01:00"|elke minuut         |
|"24:00:00" | elke 24 dagen        |
|"1,00:00:00" | Elke dag        |

## <a name="scale-out"></a>Uitschalen

Als een functie-app wordt geschaald naar meerdere instanties, wordt slechts één exemplaar van een door een timer geactiveerde functie uitgevoerd voor alle exemplaren.

## <a name="function-apps-sharing-storage"></a>Functie-apps die opslag ruimte delen

Als u opslag accounts deelt in functie-apps die niet zijn geïmplementeerd op app service, moet u mogelijk expliciet een host-ID aan elke app toewijzen.

| Functie versie | Instelling                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x               | `AzureFunctionsWebHost__hostid`-omgevings variabele |
| 1.x               | `id` in *host. json*                                  |

U kunt de identificerende waarde weglaten of hand matig de identificerende configuratie van elke functie-app instellen op een andere waarde.

De timer trigger gebruikt een opslag vergrendeling om ervoor te zorgen dat er slechts één timer exemplaar is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps dezelfde identificerende configuratie delen en elk een timer trigger gebruikt, wordt er slechts één timer uitgevoerd.

## <a name="retry-behavior"></a>Gedrag voor opnieuw proberen

In tegens telling tot de wachtrij trigger, wordt de timer trigger niet meer geprobeerd nadat een functie is mislukt. Wanneer een functie mislukt, wordt deze niet opnieuw aangeroepen tot de volgende tijd op de planning.

## <a name="troubleshooting"></a>Problemen oplossen

Zie voor meer informatie over wat u moet doen wanneer de timer trigger niet werkt zoals verwacht, het [onderzoeken en rapporteren van problemen met door timer geactiveerde functies die niet](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)worden gestart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ga naar een Snelstartgids die gebruikmaakt van een timer trigger](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
