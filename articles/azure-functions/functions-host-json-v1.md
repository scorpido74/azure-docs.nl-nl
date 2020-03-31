---
title: host.json-verwijzing voor Azure-functies 1.x
description: Referentiedocumentatie voor het Azure Functions host.json-bestand met de v1-runtime.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 2b00e2343e0959e07b195e2e98c6719a1893b8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277048"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.json-verwijzing voor Azure-functies 1.x

> [!div class="op_single_selector" title1="Selecteer de versie van de runtime van Azure-functies die u gebruikt: "]
> * [Versie 1](functions-host-json-v1.md)
> * [Versie 2](functions-host-json.md)

Het bestand *host.json* metagegevens bevat algemene configuratieopties die van invloed zijn op alle functies voor een functie-app. In dit artikel worden de instellingen weergegeven die beschikbaar zijn voor de v1-runtime. Het JSON-schema http://json.schemastore.org/hostis op .

> [!NOTE]
> Dit artikel is voor Azure Functions 1.x.  Zie [host.json-verwijzing naar Azure Functions 2.x](functions-host-json.md)voor een referentie van host.json in Functies 2.x .

Andere configuratieopties voor functie-apps worden beheerd in uw [app-instellingen.](functions-app-settings.md)

Sommige instellingen host.json worden alleen gebruikt wanneer ze lokaal worden uitgevoerd in het bestand [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>Voorbeeld van host.json-bestand

De volgende *voorbeeldhost.json-bestanden* hebben alle mogelijke opties opgegeven.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

In de volgende secties van dit artikel wordt elke eigenschap op het hoogste niveau uitgelegd. Alle zijn optioneel, tenzij anders aangegeven.

## <a name="aggregator"></a>Aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Configuratie-instellingen voor de [Azure Cosmos DB-trigger en bindingen](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|GatewayMode|Gateway|De verbindingsmodus die door de functie wordt gebruikt bij het maken van verbinding met de Azure Cosmos DB-service. Opties `Direct` zijn en`Gateway`|
|Protocol|Https (https)|Het verbindingsprotocol dat door de functie wordt gebruikt bij de verbinding met de Azure Cosmos DB-service.  Lees [hier een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leaseVoorvoegsel|N.v.t.|Lease-voorvoegsel voor alle functies in een app.|

## <a name="durabletask"></a>duurzaamTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Configuratie-instellingen voor [gebeurtenishubtriggers en -bindingen](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>Functies

Een lijst met functies die de taakhost uitvoert. Een lege array betekent dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal hardlopen.](functions-run-local.md) In functie-apps in Azure moet u in plaats daarvan de stappen volgen in [Hoe u functies in Azure-functies uitschakelt](disable-function.md) om specifieke functies uit te schakelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functieTime-out

Geeft de time-outduur voor alle functies aan. In een serverloos verbruiksplan is het geldige bereik 1 seconde tot 10 minuten en de standaardwaarde 5 minuten. In een App Service-abonnement is er geen algemene limiet en is de standaardinstelling _null_, wat geen time-out aangeeft.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuratie-instellingen voor [hoststatusmonitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|enabled|waar|Hiermee geeft u op of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijdsinterval tussen de periodieke achtergrondstatuscontroles. | 
|healthCheckWindow|2 minuten|Een schuiftijdvenster dat wordt `healthCheckThreshold` gebruikt in combinatie met de instelling.| 
|healthCheckThreshold|6|Maximaal aantal keren dat de statuscontrole kan mislukken voordat een hostwordt gestart.| 
|tegenDrempel|0,80|De drempel waarop een prestatiemeter als ongezond wordt beschouwd.| 

## <a name="http"></a>http

Configuratie-instellingen voor [http-triggers en bindingen](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Wanneer ingeschakeld, zorgt deze instelling ervoor dat de aanvraagverwerkingpijplijn periodiek systeemprestatietellers controleert, zoals verbindingen/threads/processen/geheugen/cpu/etc. en als een van deze tellers boven een ingebouwde hoge drempel (80%) staat, worden aanvragen geweigerd met een 429 "Te bezet" antwoord totdat de teller(en) terugkeren naar normale niveaus.|
|maxConcurrentRequests|onbegrensd (`-1`)|Het maximum aantal HTTP-functies dat parallel wordt uitgevoerd. Hiermee u gelijktijdigheid beheren, wat kan helpen bij het beheren van het gebruik van resources. U bijvoorbeeld een HTTP-functie hebben die veel systeembronnen (geheugen/cpu/sockets) gebruikt, zodat het problemen veroorzaakt wanneer gelijktijdigheid te hoog is. Of u hebt mogelijk een functie die uitgaande aanvragen maakt voor een service van derden, en die oproepen moeten worden beperkt. In deze gevallen kan het toepassen van een gaspedaal hier helpen.|
|maxOutstandingRequests|onbegrensd (`-1`)|Het maximum aantal openstaande aanvragen dat op een bepaald moment wordt gehouden. Deze limiet omvat aanvragen die in de wachtrij staan, maar die niet zijn gestart met uitvoeren, evenals uitvoeringen in uitvoering. Alle binnenkomende aanvragen boven deze limiet worden afgewezen met een antwoord van 429 'Te bezet'. Dat stelt bellers in staat om op tijd gebaseerde strategieën voor nieuwe technieken te gebruiken en helpt u ook om maximale aanvraaglatencies te controleren. Dit bepaalt alleen wachtrijen die plaatsvinden binnen het uitvoeringspad van scripthost. Andere wachtrijen, zoals de wachtrij voor ASP.NET aanvraag, zijn nog steeds van kracht en worden niet beïnvloed door deze instelling.|
|routeVoorvoegsel|api|Het routevoorvoegsel dat van toepassing is op alle routes. Gebruik een lege tekenreeks om het standaardvoorvoegsel te verwijderen. |

## <a name="id"></a>id

De unieke ID voor een jobhost. Kan een kleine letters GUID met streepjes verwijderd. Vereist bij lokaal hardlopen. Wanneer u in Azure wordt uitgevoerd, raden we u aan geen id-waarde in te stellen. Een ID wordt automatisch gegenereerd `id` in Azure wanneer deze wordt weggelaten. 

Als u een Opslagaccount deelt in meerdere functie-apps, `id`controleert u of elke functie-app een andere . U de `id` eigenschap weglaten of handmatig `id` instellen dat elke functie-app op een andere waarde wordt ingesteld. De timertrigger maakt gebruik van een opslagvergrendeling om ervoor te zorgen dat er slechts één timerinstantie is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps `id` hetzelfde delen en elk een timertrigger gebruikt, wordt er slechts één timer uitgevoerd.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Logger

Hiermee besturingselementen voor logboeken die zijn geschreven door een [ILogger-object](functions-monitoring.md#write-logs-in-c-functions) of door [context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|categorieFilter|N.v.t.|Hiermee geeft u filtering op categorie op| 
|defaultLevel defaultLevel defaultLevel defaultLevel|Informatie|Voor categorieën die niet `categoryLevels` in de array zijn opgegeven, verzendt u logboeken op dit niveau en hoger naar Application Insights.| 
|categorieNiveaus|N.v.t.|Een array met categorieën die het minimale logboekniveau opgeeft dat voor elke categorie naar Toepassingsstatistieken moet worden verzonden. De hier opgegeven categorie bepaalt alle categorieën die met dezelfde waarde beginnen en langere waarden hebben voorrang. In het vorige *voorbeeldhost.json-bestand* worden alle categorieën die beginnen met `Information` het logboek Host.Aggregator op niveau. Alle andere categorieën die beginnen met 'Host', zoals `Error` Host.Executor, loggen op niveau.| 

## <a name="queues"></a>Wachtrijen

Configuratie-instellingen voor [opslagwachtrijtriggers en -bindingen](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxPollingInterval|60000|Het maximale interval in milliseconden tussen wachtrijpeilingen.| 
|zichtbaarheidTime-out|0|Het tijdsinterval tussen nieuwe pogingen bij verwerking van een bericht mislukt.| 
|batchSize|16|Het aantal wachtrijberichten dat de runtime van de functies gelijktijdig ophaalt en parallel verwerkt. Wanneer het aantal dat wordt `newBatchThreshold`verwerkt wordt neerop de , de runtime krijgt een andere batch en begint de verwerking van deze berichten. Dus het maximum aantal gelijktijdige berichten dat `batchSize` `newBatchThreshold`per functie wordt verwerkt is plus . Deze limiet is afzonderlijk van toepassing op elke functie die in de wachtrij wordt geactiveerd. <br><br>Als u parallelle uitvoering wilt voorkomen voor berichten die `batchSize` in één wachtrij zijn ontvangen, u instellen op 1. Deze instelling elimineert echter alleen gelijktijdigheid zolang uw functie-app op één virtuele machine (VM) wordt uitgevoerd. Als de functie-app wordt geschaald naar meerdere VM's, kan elke VM één instantie van elke functie die in de wachtrij wordt geactiveerd, uitvoeren.<br><br>Het `batchSize` maximum is 32. | 
|maxDequeueCount|5|Het aantal keren dat u een bericht wilt verwerken voordat u het naar de gifwachtrij verplaatst.| 
|newBatchThreshold|batchGrootte/2|Wanneer het aantal berichten dat gelijktijdig wordt verwerkt, aan dit aantal wordt afgenomen, wordt een andere batch opgehaald.| 

## <a name="sendgrid"></a>SendGrid

Configuratie-instelling voor de [sendgrind-uitvoerbinding](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|from|N.v.t.|Het e-mailadres van de afzender voor alle functies.| 

## <a name="servicebus"></a>serviceBus

Configuratie-instelling voor [servicebustriggers en -bindingen](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige oproepen naar de callback die de berichtpomp moet initiëren. Standaard verwerkt de runtime Functies meerdere berichten tegelijk. Als u de runtime wilt richten op het verwerken `maxConcurrentCalls` van slechts één wachtrij of onderwerpbericht tegelijk, stelt u deze in op 1. | 
|prefetchCount prefetchCount prefetchCount prefetch|N.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.| 
|automatische time-out automatisch vernieuwen|00:05:00|De maximale duur waarbinnen het berichtslot automatisch wordt verlengd.| 

## <a name="singleton"></a>Singleton

Configuratie-instellingen voor singleton-vergrendelingsgedrag. Zie [GitHub-probleem over singleton-ondersteuning](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)voor meer informatie.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|lockPeriode|00:00:15|De periode waarvoor functieniveauvergrendelingen worden genomen. De sloten automatisch vernieuwen.| 
|listenerLockPeriod|00:01:00|De periode waarvoor luisteraar vergrendelt, wordt genomen.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijdsinterval dat wordt gebruikt voor het herstel van listenervergrendeling als een listenerslot niet kan worden verkregen bij het opstarten.| 
|lockAcquisitionTimeout|00:01:00|De maximale tijd van de runtime zal proberen om een slot te verwerven.| 
|lockAcquisitionPollingInterval|N.v.t.|Het interval tussen pogingen voor het verkrijgen van het slot.| 

## <a name="tracing"></a>Tracering

*Versie 1.x*

Configuratie-instellingen voor logboeken die `TraceWriter` u maakt met behulp van een object. Zie [C# Logging](functions-reference-csharp.md#logging) en [Node.js Logging](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|consoleLevel|Info|Het traceringsniveau voor consolelogboekregistratie. `off`Opties zijn: `error` `warning`, `info`, `verbose`, en .|
|fileLoggingMode|foutopsporingAlleen|Het traceringsniveau voor bestandsregistratie. Opties `never`zijn `always` `debugOnly`, , .| 

## <a name="watchdirectories"></a>horlogeDirectories

Een set [gedeelde codemappen](functions-reference-csharp.md#watched-directories) die moeten worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze mappen wordt gewijzigd, de wijzigingen worden opgepikt door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van het host.json-bestand](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Bekijk globale instellingen in omgevingsvariabelen](functions-app-settings.md)
