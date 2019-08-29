---
title: Prestaties en schaal in Durable Functions-Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097999"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestaties en schaal in Durable Functions (Azure Functions)

Als u de prestaties en schaal baarheid wilt optimaliseren, is het belang rijk dat u de unieke schaal kenmerken van [Durable functions](durable-functions-overview.md)begrijpt.

Om het schaal gedrag te begrijpen, moet u enkele details van de onderliggende Azure Storage Provider begrijpen.

## <a name="history-table"></a>Geschiedenis tabel

De **geschiedenis** tabel is een Azure Storage tabel die de geschiedenis gebeurtenissen bevat voor alle Orchestration-instanties binnen een task hub. De naam van deze tabel bevindt zich in de vorm *TaskHubName*geschiedenis. Als instanties worden uitgevoerd, worden nieuwe rijen toegevoegd aan deze tabel. De partitie sleutel van deze tabel is afgeleid van de exemplaar-ID van de indeling. Een exemplaar-ID is in de meeste gevallen wille keurig. Dit zorgt ervoor dat interne partities in Azure Storage optimaal worden gedistribueerd.

Wanneer een Orchestration-exemplaar moet worden uitgevoerd, worden de juiste rijen van de geschiedenis tabel in het geheugen geladen. Deze *geschiedenis gebeurtenissen* worden vervolgens opnieuw afgespeeld in de Orchestrator-functie code om deze terug te krijgen in de status van het eerder vastgelegde controle punt. Het gebruik van de uitvoerings geschiedenis om de status op deze manier opnieuw op te bouwen, wordt beïnvloed door het [patroon gebeurtenis bronnen](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabel met exemplaren

De tabel instances is een andere Azure Storage tabel die de statussen bevat van alle indelings instanties binnen een task hub. Als er exemplaren worden gemaakt, worden er nieuwe rijen aan deze tabel toegevoegd. De partitie sleutel van deze tabel is de indelings exemplaar-ID en de rij is een vaste constante. Er is één rij per Orchestration-exemplaar.

Deze tabel wordt gebruikt om te voldoen aan de instantie query [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) aanvragen van de api's GetStatusAsync `getStatus` (.net) en (Java script), evenals de [http API-status query](durable-functions-http-api.md#get-instance-status). Het wordt uiteindelijk consistent met de inhoud van de eerder genoemde **geschiedenis** tabel. Het gebruik van een afzonderlijke Azure Storage tabel om op deze manier efficiënt te voldoen aan de instantie query bewerkingen, wordt beïnvloed door het [CQRS-patroon (Command and query Responsibility segregation)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Interne wachtrij Triggers

Orchestrator-functies en-activiteit functies worden beide geactiveerd door interne wacht rijen in de taak hub van de functie-app. Met behulp van wacht rijen op deze manier beschikt u over betrouw bare leverings garanties voor ' ten minste één keer '. Er zijn twee typen wacht rijen in Durable Functions: de **controle wachtrij** en de **wachtrij met werk items**.

### <a name="the-work-item-queue"></a>De wachtrij voor werk items

Er bevindt zich één werk item wachtrij per taak-hub in Durable Functions. Het is een eenvoudige wachtrij en gedraagt zich op dezelfde manier `queueTrigger` als een andere wachtrij in azure functions. Deze wachtrij wordt gebruikt voor het activeren van stateless *activiteiten functies* door één bericht tegelijk te delegeren. Elk van deze berichten bevat invoer van de functie activiteit en aanvullende meta gegevens, zoals welke functie moet worden uitgevoerd. Wanneer een Durable Functions toepassing wordt geschaald naar meerdere Vm's, zijn deze Vm's allemaal concurrerend om werk te verkrijgen vanuit de werk wachtrij.

### <a name="control-queues"></a>Controle wachtrij (en)

Er zijn meerdere *controle wachtrijen* per taak hub in Durable functions. Een *controle wachtrij* is geavanceerder dan de vereenvoudigde wachtrij voor werk items. Controle wachtrijen worden gebruikt om de stateful Orchestrator-functies te activeren. Omdat de Orchestrator-functie instanties stateful Singleton zijn, is het niet mogelijk een concurrerend consument model te gebruiken om de belasting over Vm's te verdelen. Orchestrator-berichten worden in plaats daarvan verdeeld over de controle wachtrijen. Meer informatie over dit gedrag vindt u in de volgende secties.

Controle wachtrijen bevatten diverse indelings levenscyclus bericht typen. Voor beelden zijn [Orchestrator-besturings berichten](durable-functions-instance-management.md), *antwoord* berichten over de functie van de activiteit en timer berichten. Net als 32 berichten worden in een enkele poll van een controle wachtrij uit de wachtrij verwijderd. Deze berichten bevatten payload-gegevens en meta gegevens met inbegrip van de indeling waarvoor deze is bedoeld. Als meerdere uit de wachtrij geplaatste berichten zijn bedoeld voor hetzelfde exemplaar van de indeling, worden deze verwerkt als een batch.

### <a name="queue-polling"></a>Polling voor wachtrij

De uitbrei ding duurzame taak implementeert een wille keurig exponentiële uitstel algoritme om het effect van polling bij een niet-actieve wachtrij op kosten voor opslag transacties te verminderen. Wanneer een bericht wordt gevonden, controleert de runtime onmiddellijk op een ander bericht. Wanneer er geen bericht wordt gevonden, wordt gewacht tot een bepaalde tijd voordat u het opnieuw probeert. Na volgende mislukte pogingen om een wachtrij bericht op te halen, blijft de wacht tijd toenemen totdat de maximale wacht tijd is bereikt. de standaard waarde is 30 seconden.

De maximale polling vertraging kan worden geconfigureerd via de `maxQueuePollingInterval` eigenschap in het [bestand host. json](../functions-host-json.md#durabletask). Als u dit instelt op een hogere waarde, kan dit leiden tot hogere latenties voor bericht verwerking. Hogere latenties worden alleen verwacht na Peri Oden van inactiviteit. Als u dit instelt op een lagere waarde, kan dit leiden tot hogere opslag kosten als gevolg van grotere opslag transacties.

> [!NOTE]
> Bij het uitvoeren van de Azure Functions verbruiks-en Premium-abonnementen, wordt elke regel van elk besturings element en elke werk item elke 10 seconden gecontroleerd door de [Azure functions Scale-controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) . Deze extra polling is nodig om te bepalen wanneer u functie-app-exemplaren wilt activeren en schaal beslissingen wilt maken. Op het moment van schrijven is dit 10 seconden interval constant en kan het niet worden geconfigureerd.

## <a name="storage-account-selection"></a>Opslag account selecteren

De wacht rijen, tabellen en blobs die door Durable Functions worden gebruikt, worden gemaakt in een geconfigureerd Azure Storage-account. Het account dat moet worden gebruikt, kan worden `durableTask/azureStorageConnectionStringName` opgegeven met behulp van de instelling in het bestand **host. json** .

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Als u niets opgeeft, wordt `AzureWebJobsStorage` het standaard opslag account gebruikt. Voor prestatie gevoelige workloads wordt het configureren van een niet-standaard opslag account echter aanbevolen. Durable Functions maakt gebruik van Azure Storage intensief, en met behulp van een toegewezen opslag account wordt het gebruik geïsoleerd van Durable Functions opslag van het interne gebruik door de Azure Functions-host.

## <a name="orchestrator-scale-out"></a>Orchestrator-uitschalen

Activiteit functies zijn stateless en worden automatisch geschaald door Vm's toe te voegen. Orchestrator-functies, anderzijds, worden gepartitioneerd over een of meer controle wachtrijen. Het aantal controle wachtrijen wordt gedefinieerd in het **host. json** -bestand. In het volgende voor beeld wordt de `durableTask/partitionCount` eigenschap host. json ingesteld op. `3`

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Een task hub kan worden geconfigureerd met tussen de 1 en 16 partities. Als deze niet is opgegeven, is het standaard aantal partities **4**.

Bij het uitschalen naar meerdere functie-instanties (meestal op verschillende Vm's), krijgt elk exemplaar een vergren deling van een van de controle wachtrijen. Deze vergren delingen worden intern geïmplementeerd als Blob Storage-leases en zorgen ervoor dat een Orchestration-exemplaar alleen op één exemplaar van een host tegelijk wordt uitgevoerd. Als een taak hub is geconfigureerd met drie controle wachtrijen, kunnen indelings instanties worden verdeeld over Maxi maal drie Vm's. Er kunnen extra Vm's worden toegevoegd om de capaciteit voor de uitvoering van de activiteit functie te verhogen.

In het volgende diagram ziet u hoe de Azure Functions host communiceert met de opslag entiteiten in een geschaalde omgeving.

![Diagram schalen](./media/durable-functions-perf-and-scale/scale-diagram.png)

Zoals u in het vorige diagram ziet, concurreren alle Vm's met berichten in de werk wachtrij. Er kunnen echter maar drie Vm's berichten van controle wachtrijen ophalen en elke VM vergrendelt één controle wachtrij.

De indelings instanties worden gedistribueerd over alle exemplaren van de controle wachtrij. De distributie wordt uitgevoerd door hashing van de exemplaar-ID van de indeling. Exemplaar-Id's zijn standaard wille keurige GUID'S, zodat exemplaren gelijkmatig over alle controle wachtrijen worden verdeeld.

Over het algemeen zijn Orchestrator-functies lichter en zijn ze niet vereist voor een grote hoeveelheid reken kracht. Daarom is het niet nodig om een groot aantal controle wachtrij partities te maken om een goede door voer te verkrijgen. De meeste zware werkzaamheden moeten worden uitgevoerd in stateless activiteiten functies, die oneindig kunnen worden uitgebreid.

## <a name="auto-scale"></a>Automatisch schalen

Net als bij alle Azure Functions die worden uitgevoerd in het verbruiks abonnement, ondersteunt Durable Functions automatisch schalen via de [Azure functions Scale-controller](../functions-scale.md#runtime-scaling). De schaal controller bewaakt de latentie van alle wacht rijen door regel matig de opdracht _Peek_ te geven. Op basis van de latentie van de gepeekte berichten, bepaalt de schaal controller of Vm's moeten worden toegevoegd of verwijderd.

Als de schaal controller bepaalt dat de bericht latenties van de controle wachtrij te hoog zijn, worden er VM-exemplaren toegevoegd totdat de bericht latentie afneemt naar een acceptabel niveau of het aantal partities van de controle wachtrij bereikt. De schaal controller voegt voortdurend VM-exemplaren toe als wacht tijden voor werk items in de wachtrij hoog zijn, ongeacht het aantal partities.

## <a name="thread-usage"></a>Thread gebruik

Orchestrator-functies worden uitgevoerd op één thread om ervoor te zorgen dat de uitvoering deterministisch kan worden uitgevoerd in veel herhalingen. Als gevolg van deze uitvoering met één thread is het belang rijk dat Orchestrator functions-threads geen CPU-intensieve taken uitvoeren, I/O of blok keren om een wille keurige reden. Werk waarvoor I/O, blok keren of meerdere threads nodig zijn, moet worden verplaatst naar activiteiten functies.

Activiteit functies hebben hetzelfde gedrag als normale, door de wachtrij geactiveerde functies. Ze kunnen I/O, CPU-intensieve bewerkingen uitvoeren en meerdere threads gebruiken. Omdat activiteit triggers stateless zijn, kunnen ze vrij uitschalen naar een niet-gebonden aantal Vm's.

## <a name="concurrency-throttles"></a>Gelijktijdigheids beperking

Azure Functions ondersteunt het uitvoeren van meerdere functies gelijktijdig in één app-exemplaar. Met deze gelijktijdige uitvoering wordt de parallelle afkorting verbeterd en wordt het aantal ' koude start ' geminimaliseerd dat een typische app in de loop van de tijd zal werken. Een hoge gelijktijdigheid kan echter leiden tot een hoog geheugen gebruik per VM. Afhankelijk van de behoeften van de functie-app, kan het nodig zijn om de gelijktijdigheid per exemplaar te beperken om te voor komen dat er onvoldoende geheugen beschikbaar is in situaties met hoge belasting.

U kunt zowel de functie activiteit als de gelijktijdigheids limieten van de Orchestrator-functie configureren in het bestand **host. json** . De relevante instellingen zijn `durableTask/maxConcurrentActivityFunctions` `durableTask/maxConcurrentOrchestratorFunctions` respectievelijk.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

In het vorige voor beeld kunnen Maxi maal 10 Orchestrator-functies en tien activiteit functies gelijktijdig worden uitgevoerd op één virtuele machine. Als dat niet is opgegeven, wordt het aantal uitvoeringen van gelijktijdige activiteiten en Orchestrator-functies beperkt bij 10X het aantal kern geheugens op de virtuele machine.

> [!NOTE]
> Deze instellingen zijn handig voor het beheren van geheugen en CPU-gebruik op één virtuele machine. Als er echter meer dan meerdere Vm's zijn uitgebreid, heeft elke VM een eigen set limieten. Deze instellingen kunnen niet worden gebruikt voor het beheren van gelijktijdigheid op een globaal niveau.

## <a name="orchestrator-function-replay"></a>Orchestrator-functie opnieuw afspelen

Zoals eerder vermeld, worden Orchestrator-functies opnieuw afgespeeld met de inhoud van de **geschiedenis** tabel. De functie code van Orchestrator wordt standaard elke keer dat een batch berichten uit een controle wachtrij wordt verwijderd, opnieuw afgespeeld.

Dit agressieve replay-gedrag kan worden uitgeschakeld door **uitgebreide sessies**in te scha kelen. Wanneer uitgebreide sessies zijn ingeschakeld, worden Orchestrator-functie exemplaren in het geheugen langer bewaard en kunnen nieuwe berichten zonder een volledig herhaling worden verwerkt. Uitgebreide sessies worden ingeschakeld door in `durableTask/extendedSessionsEnabled` het `true` bestand **host. json** te worden ingesteld. De `durableTask/extendedSessionIdleTimeoutInSeconds` instelling wordt gebruikt om te bepalen hoe lang een niet-actieve sessie in het geheugen wordt gehouden:

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

Het standaard effect van het inschakelen van uitgebreide sessies is dat I/O wordt beperkt tegen het Azure Storage-account en de algehele door voer te verbeteren.

Een mogelijk nadeel van deze functie is echter dat niet-actieve Orchestrator-functie exemplaren langer in het geheugen blijven. Er zijn twee effecten waar u rekening mee moet houden:

1. Algehele toename van het geheugen gebruik van de functie-app.
2. Algehele afname van de door Voer als er veel gelijktijdige, langdurige Orchestrator-functies worden uitgevoerd.

Als `durableTask/extendedSessionIdleTimeoutInSeconds` bijvoorbeeld is ingesteld op 30 seconden, dan is een korte functie van een orchestrator, die in minder dan 1 seconde wordt uitgevoerd, gedurende 30 seconden nog steeds geheugen in beslag neemt. Het telt ook op basis van `durableTask/maxConcurrentOrchestratorFunctions` het eerder vermelde quotum, waardoor andere Orchestrator-functies mogelijk niet meer worden uitgevoerd.

> [!NOTE]
> Deze instellingen mogen alleen worden gebruikt nadat een Orchestrator-functie volledig is ontwikkeld en getest. Het standaard gedrag voor agressief opnieuw afspelen is handig voor het detecteren van idempotentie-fouten in Orchestrator-functies tijdens de ontwikkelings periode.

## <a name="performance-targets"></a>Prestatie doelen

Bij het plannen van het gebruik van Durable Functions voor een productie toepassing is het belang rijk om de prestatie vereisten vroeg in het plannings proces in overweging te nemen. In deze sectie worden enkele eenvoudige gebruiks scenario's en de verwachte maximum doorvoer aantallen besproken.

* **Uitvoering van sequentiële activiteit**: In dit scenario wordt een Orchestrator-functie beschreven waarmee een reeks activiteit wordt uitgevoerd, één na de andere. Het lijkt veel op het voor beeld van een [functie](durable-functions-sequence.md) koppeling.
* **Uitvoering van parallelle activiteit**: In dit scenario wordt een Orchestrator-functie beschreven waarmee veel activiteit functies parallel worden uitgevoerd met behulp van het uitwaaieren [van](durable-functions-cloud-backup.md) de ventilator.
* **Verwerking van parallelle antwoorden**: Dit scenario is de tweede helft van het [](durable-functions-cloud-backup.md) uitwaaieren van de ventilator. Het is gericht op de prestaties van de ventilator. Het is belang rijk te weten dat in tegens telling tot uitwaaiers de ventilator wordt uitgevoerd door één functie-exemplaar van Orchestrator en daarom alleen op één virtuele machine kan worden uitgevoerd.
* **Externe gebeurtenis verwerking**: Dit scenario bevat één Orchestrator-functie-exemplaar dat op [externe gebeurtenissen](durable-functions-external-events.md)een voor een kan wachten.

> [!TIP]
> In tegens telling tot uitwaaiers zijn ventilatoren beperkt tot één virtuele machine. Als uw toepassing gebruikmaakt van het uitwaaieren, een ventilator patroon en u zich zorgen maakt over de prestaties van de ventilator, kunt u overwegen om de activiteit functie te verdelen over meerdere [onderliggende](durable-functions-sub-orchestrations.md)indelingen.

De volgende tabel bevat de verwachte *maximum* waarden voor door Voer voor de eerder beschreven scenario's. ' Instance ' verwijst naar één exemplaar van een Orchestrator-functie die wordt uitgevoerd op een enkele kleine ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) VM in azure app service. In alle gevallen wordt ervan uitgegaan dat [uitgebreide sessies](#orchestrator-function-replay) zijn ingeschakeld. De werkelijke resultaten kunnen variëren, afhankelijk van de CPU of het I/O-werk dat door de functie code wordt uitgevoerd.

| Scenario | Maximumdoorvoer |
|-|-|
| Uitvoering van sequentiële activiteit | 5 activiteiten per seconde, per instantie |
| Uitvoering van parallelle activiteit (uitwaaieren) | 100 activiteiten per seconde, per instantie |
| Verwerking van parallelle antwoorden (ventilator-in) | 150 reacties per seconde, per instantie |
| Externe gebeurtenis verwerking | 50 gebeurtenissen per seconde, per instantie |

> [!NOTE]
> Deze nummers zijn actueel vanaf de release van de v 1.4.0 (GA) van de uitbrei ding Durable Functions. Deze getallen kunnen na verloop van tijd veranderen naarmate het onderdeel verloopt en als er optimalisaties worden uitgevoerd.

Als u de door u verwachte doorvoer nummers niet ziet en het CPU-en geheugen gebruik op de juiste manier wordt weer gegeven, controleert u of de oorzaak is gerelateerd aan [de status van uw opslag account](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). De uitbrei ding van de Durable Functions kan een Azure Storage account aanzienlijk laden en voldoende hoge belasting kan leiden tot beperking van het opslag account.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste Durable Function maken in C#](durable-functions-create-first-csharp.md)
