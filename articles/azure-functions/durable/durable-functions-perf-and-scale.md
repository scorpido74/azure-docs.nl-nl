---
title: Prestaties en schaal in duurzame functies - Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277906"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Prestaties en schaalbaarheid in Durable Functions (Azure Functions)

Om de prestaties en schaalbaarheid te optimaliseren, is het belangrijk om de unieke schaaleigenschappen van [duurzame functies](durable-functions-overview.md)te begrijpen.

Om het schaalgedrag te begrijpen, moet u enkele details van de onderliggende Azure Storage-provider begrijpen.

## <a name="history-table"></a>Geschiedenistabel

De tabel **Geschiedenis** is een Azure Storage-tabel met de geschiedenisgebeurtenissen voor alle orchestration-instanties binnen een taakhub. De naam van deze tabel staat in het formulier *TaskHubName*History. Naarmate instanties worden uitgevoerd, worden nieuwe rijen aan deze tabel toegevoegd. De partitiesleutel van deze tabel is afgeleid van de instantie-id van de orchestration. Een instantie-id is in de meeste gevallen willekeurig, wat zorgt voor een optimale verdeling van interne partities in Azure Storage.

Wanneer een orchestration-instantie moet worden uitgevoerd, worden de juiste rijen van de tabel Geschiedenis in het geheugen geladen. Deze *geschiedenisgebeurtenissen* worden vervolgens opnieuw afgespeeld in de orchestrator-functiecode om deze terug te krijgen in de eerder gecontroleerde status. Het gebruik van uitvoeringsgeschiedenis om de status op deze manier weer op te bouwen, wordt beïnvloed door het [event sourcing-patroon.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)

## <a name="instances-table"></a>Instantiestabel

De tabel **Instanties** is een andere Azure Storage-tabel die de statussen bevat van alle orchestration- en entiteitsinstanties binnen een taakhub. Als instanties worden gemaakt, worden nieuwe rijen aan deze tabel toegevoegd. De partitiesleutel van deze tabel is de orchestration instance ID of entiteitssleutel en de rijsleutel is een vaste constante. Er is één rij per instantie van orkestratie of entiteit.

Deze tabel wordt gebruikt om te `GetStatusAsync` voldoen aan `getStatus` aanvragen voor instantiequery's van de (.NET) en (JavaScript) API's en de [statusquery HTTP API](durable-functions-http-api.md#get-instance-status). Het wordt uiteindelijk in overeenstemming gehouden met de inhoud van de eerder genoemde **geschiedenistabel.** Het gebruik van een aparte Azure Storage-tabel om op deze manier efficiënt aan instance querybewerkingen te voldoen, wordt beïnvloed door het [CQRS-patroon (Command and Query Responsibility Segregation).](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Interne wachtrijtriggers

Orchestrator-functies en activiteitsfuncties worden beide geactiveerd door interne wachtrijen in de taakhub van de functie-app. Het gebruik van wachtrijen op deze manier biedt betrouwbare "ten minste eenmaal" bericht levering garanties. Er zijn twee soorten wachtrijen in Duurzame functies: de **besturingselementwachtrij** en de **wachtrij voor werkitems**.

### <a name="the-work-item-queue"></a>De wachtrij voor werkitems

Er is één wachtrij voor werkitems per taakhub in Duurzame functies. Het is een basiswachtrij en gedraagt `queueTrigger` zich op dezelfde manier als elke andere wachtrij in Azure-functies. Deze wachtrij wordt gebruikt om statusloze *activiteitsfuncties* te activeren door één bericht tegelijk in de wachtrij te zetten. Elk van deze berichten bevat activiteitsfunctie-ingangen en aanvullende metagegevens, zoals welke functie moet worden uitgevoerd. Wanneer een toepassing Voor duurzame functies wordt geschaald naar meerdere VM's, concurreren deze VM's allemaal om werk uit de wachtrij voor werkitems te verwerven.

### <a name="control-queues"></a>Wachtrij(en) beheren

Er zijn meerdere *controlewachtrijen* per taakhub in Duurzame functies. Een *besturingselementwachtrij* is geavanceerder dan de eenvoudigere wachtrij voor werkitems. Controlewachtrijen worden gebruikt om de stateful orchestrator- en entiteitsfuncties te activeren. Omdat de instantie van de orchestrator- en entiteitsfunctie stateful singletons zijn, is het niet mogelijk om een concurrerend consumentenmodel te gebruiken om belasting over VM's te verdelen. In plaats daarvan worden orchestrator- en entiteitsberichten in balans gebracht in de controlewachtrijen. Meer details over dit gedrag vindt u in volgende secties.

Controlewachtrijen bevatten verschillende berichttypen van de orkestlevenscyclus. Voorbeelden hiervan zijn [orchestrator control berichten,](durable-functions-instance-management.md)activiteit functie *respons* berichten, en timer berichten. Maar liefst 32 berichten worden in één poll uit een besturingselementwachtrij verwijderd. Deze berichten bevatten payload gegevens evenals metadata, waaronder welke orchestration instantie is bedoeld voor. Als meerdere berichten in de wachtrij zijn bedoeld voor dezelfde orchestration-instantie, worden ze verwerkt als een batch.

### <a name="queue-polling"></a>Wachtrijpeilingen

De duurzame taakextensie implementeert een willekeurig exponentieel back-offalgoritme om het effect van idle-queue polling op de kosten van opslagtransacties te verminderen. Wanneer een bericht wordt gevonden, wordt met de runtime onmiddellijk gecontroleerd op een ander bericht; wanneer er geen bericht wordt gevonden, wacht het een periode voordat u het opnieuw probeert. Na daaropvolgende mislukte pogingen om een wachtrijbericht te krijgen, blijft de wachttijd toenemen totdat deze de maximale wachttijd bereikt, die standaard 30 seconden bedraagt.

De maximale polling vertraging `maxQueuePollingInterval` is configureerbaar via de eigenschap in het [host.json bestand](../functions-host-json.md#durabletask). Als u deze eigenschap instelt op een hogere waarde, kunnen er hogere latencies voor berichtverwerking ontstaan. Hogere latencies zouden pas worden verwacht na perioden van inactiviteit. Als u deze eigenschap op een lagere waarde instelt, kunnen hogere opslagkosten ontstaan als gevolg van hogere opslagtransacties.

> [!NOTE]
> Wanneer u de Azure Functions Consumption- en Premium-abonnementen uitvoert, [wordt](../functions-scale.md#how-the-consumption-and-premium-plans-work) elke besturingselement- en werkitemwachtrij eenmaal per 10 seconden door de enquête gehouden. Deze extra polling is nodig om te bepalen wanneer de functie-app-exemplaren moeten worden geactiveerd en om schaalbeslissingen te nemen. Op het moment van schrijven is dit interval van 10 seconden constant en kan het niet worden geconfigureerd.

## <a name="storage-account-selection"></a>Selectie van opslagaccount

De wachtrijen, tabellen en blobs die worden gebruikt door duurzame functies, worden gemaakt in een geconfigureerd Azure Storage-account. Het te gebruiken account kan `durableTask/storageProvider/connectionStringName` worden `durableTask/azureStorageConnectionStringName` opgegeven met de instelling (of instelling in Duurzame functies 1.x) in het **bestand host.json.**

### <a name="durable-functions-2x"></a>Duurzame functies 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Duurzame functies 1,x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Als dit niet `AzureWebJobsStorage` is opgegeven, wordt het standaardopslagaccount gebruikt. Voor prestatiegevoelige workloads wordt het configureren van een niet-standaardopslagaccount echter aanbevolen. Duurzame functies maken veel gebruik van Azure Storage en het gebruik van een speciaal opslagaccount isoleert het opslaggebruik van duurzame functies van het interne gebruik door de Azure Functions-host.

## <a name="orchestrator-scale-out"></a>Orchestrator scale-out

Activiteitsfuncties worden stateloos en automatisch uitgeschroefd door VM's toe te voegen. Orchestrator-functies en entiteiten worden daarentegen *verdeeld* over een of meer controlewachtrijen. Het aantal controlewachtrijen wordt gedefinieerd in het **bestand host.json.** In het volgende voorbeeld host.json-fragment wordt de `durableTask/storageProvider/partitionCount` eigenschap (of `durableTask/partitionCount` in Duurzame functies 1.x) ingesteld op `3`.

### <a name="durable-functions-2x"></a>Duurzame functies 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Duurzame functies 1,x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Een taakhub kan worden geconfigureerd met tussen de 1 en 16 partities. Als dit niet is opgegeven, is het standaardaantal **partitie4**.

Wanneer u uitschalen naar meerdere functiehost-exemplaren (meestal op verschillende VM's), krijgt elke instantie een vergrendeling op een van de besturingselementwachtrijen. Deze vergrendelingen worden intern geïmplementeerd als blob-opslagleases en zorgen ervoor dat een instantie of entiteit voor orkestratie slechts op één hostexemplaar tegelijk wordt uitgevoerd. Als een taakhub is geconfigureerd met drie controlewachtrijen, kunnen orchestration-instanties en -entiteiten worden belast over maar liefst drie VM's. Er kunnen extra VM's worden toegevoegd om de capaciteit voor de uitvoering van de activiteitsfunctie te vergroten.

In het volgende diagram ziet u hoe de Azure Functions-host samenwerkt met de opslagentiteiten in een uitgeschroefde omgeving.

![Schaaldiagram](./media/durable-functions-perf-and-scale/scale-diagram.png)

Zoals in het vorige diagram wordt weergegeven, concurreren alle VM's om berichten in de wachtrij voor werkitems. Er kunnen echter slechts drie VM's berichten ophalen uit controlewachtrijen en elke VM vergrendelt één besturingselementwachtrij.

Orchestration-exemplaren en -entiteiten worden verdeeld over alle controlewachtrijinstanties. De verdeling gebeurt door de instantie-id van de orchestration of de entiteitsnaam en het sleutelpaar te hashing. Orkestratie-instantie-id's zijn standaard willekeurige GUID's, zodat instanties gelijkmatig over alle controlewachtrijen worden verdeeld.

Over het algemeen zijn orchestrator-functies bedoeld om licht te zijn en mogen ze geen grote hoeveelheden rekenkracht vereisen. Het is daarom niet nodig om een groot aantal controlewachtrijpartities te maken om een grote doorvoer voor orkestraties te krijgen. Het grootste deel van het zware werk moet worden gedaan in stateless activiteit functies, die oneindig kan worden uitgeschaald.

## <a name="auto-scale"></a>Automatisch schalen

Zoals bij alle Azure-functies die worden uitgevoerd in de abonnementen Verbruik en Elastische Premium, ondersteunt duurzame functies automatische schaal via de [schaalcontroller azure-functies.](../functions-scale.md#runtime-scaling) De schaalcontroller bewaakt de latentie van alle wachtrijen door periodiek _peek-opdrachten_ uit te geven. Op basis van de latencies van de gegluurde berichten beslist de schaalcontroller of vm's worden toegevoegd of verwijderd.

Als de schaalcontroller vaststelt dat de latende latenomvan het controlewachtrijbericht te hoog is, worden VM-exemplaren toegevoegd totdat de latentie van het bericht afneemt tot een aanvaardbaar niveau of het aantal controlewachtrijpartities bereikt. Op dezelfde manier voegt de schaalcontroller voortdurend VM-exemplaren toe als de latende latenom van de werk-itemwachtrij hoog is, ongeacht het aantal partities.

> [!NOTE]
> Vanaf duurzame functies 2.0 kunnen functie-apps worden geconfigureerd om binnen VNET-beveiligde serviceeindpunten in het Elastic Premium-abonnement uit te voeren. In deze configuratie activeert de duurzame functies het starten van schaalaanvragen in plaats van de schaalcontroller.

## <a name="thread-usage"></a>Threadgebruik

Orchestrator-functies worden uitgevoerd op één thread om ervoor te zorgen dat de uitvoering deterministisch kan zijn voor veel herhalingen. Vanwege deze single-threaded uitvoering, is het belangrijk dat orchestrator functie threads niet CPU-intensieve taken uitvoeren, doen I / O, of blokkeren om welke reden dan ook. Alle werkzaamheden waarvoor I/O, blokkering of meerdere threads nodig zijn, moeten worden verplaatst naar activiteitsfuncties.

Activiteitsfuncties hebben allemaal hetzelfde gedrag als reguliere functies die in de wachtrij worden geactiveerd. Ze kunnen veilig I/O doen, CPU-intensieve bewerkingen uitvoeren en meerdere threads gebruiken. Omdat activiteitstriggers stateloos zijn, kunnen ze vrij uitschalen naar een onbegrensd aantal VM's.

Entiteitsfuncties worden ook uitgevoerd op één thread en bewerkingen worden één-voor-één verwerkt. Entiteitsfuncties hebben echter geen beperkingen op het type code dat kan worden uitgevoerd.

## <a name="concurrency-throttles"></a>Gelijktijdigheid suurt

Azure Functions ondersteunt het gelijktijdig uitvoeren van meerdere functies binnen één app-exemplaar. Deze gelijktijdige uitvoering helpt het parallellisme te verhogen en minimaliseert het aantal "koude starts" dat een typische app in de loop van de tijd zal ervaren. Echter, hoge gelijktijdigheid kan uitlaat per-VM systeembronnen zoals netwerkverbindingen of beschikbaar geheugen. Afhankelijk van de behoeften van de functie-app, kan het nodig zijn om de gelijktijdigheid per instantie te beperken om te voorkomen dat het geheugen in situaties met hoge belasting opraakt.

Concurrencylimieten activiteit, orkestratie en entiteitsfunctie kunnen worden geconfigureerd in het **bestand host.json.** De relevante `durableTask/maxConcurrentActivityFunctions` instellingen zijn `durableTask/maxConcurrentOrchestratorFunctions` voor activiteitsfuncties en voor zowel orchestrator- als entiteitsfuncties.

### <a name="functions-20"></a>Functies 2.0

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

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

In het vorige voorbeeld kunnen maximaal 10 orchestrator- of entiteitsfuncties en 10 activiteitsfuncties gelijktijdig op één virtuele virtuele machine worden uitgevoerd. Indien dit niet is opgegeven, wordt het aantal gelijktijdige activiteits- en orchestrator- of entiteitsfunctieuitvoeringen beperkt tot 10x het aantal cores op de VM.

> [!NOTE]
> Deze instellingen zijn handig om het geheugen- en CPU-gebruik op één vm te beheren. Wanneer echter uitgeschaald over meerdere VM's, heeft elke VM zijn eigen set limieten. Deze instellingen kunnen niet worden gebruikt om gelijktijdigheid op mondiaal niveau te controleren.

## <a name="extended-sessions"></a>Uitgebreide sessies

Uitgebreide sessies is een instelling die orkestraties en entiteiten in het geheugen houdt, zelfs nadat ze berichten hebben verwerkt. Het typische effect van het inschakelen van uitgebreide sessies is een vermindering van i/o ten opzichte van het Azure Storage-account en de algehele verbeterde doorvoer.

U uitgebreide sessies `durableTask/extendedSessionsEnabled` inschakelen `true` door in te stellen op het **bestand host.json.** De `durableTask/extendedSessionIdleTimeoutInSeconds` instelling kan worden gebruikt om te bepalen hoe lang een niet-actieve sessie in het geheugen wordt gehouden:

**Functies 2.0**
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

**Functies 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Er zijn twee mogelijke nadelen van deze instelling bewust te zijn van:

1. Er is een algehele toename van het geheugengebruik van de functie-app.
2. Er kan een algehele daling van de doorvoer zijn als er veel gelijktijdige, kortstondige orchestrator- of entiteitsfunctieuitvoeringen zijn.

Als u bijvoorbeeld `durableTask/extendedSessionIdleTimeoutInSeconds` is ingesteld op 30 seconden, neemt een kortstondige orchestrator- of entiteitsfunctieepisode die in minder dan 1 seconde wordt uitgevoerd, nog steeds 30 seconden het geheugen in beslag. Het telt ook `durableTask/maxConcurrentOrchestratorFunctions` mee voor het eerder genoemde quotum, waardoor mogelijk andere orchestrator- of entiteitsfuncties kunnen worden uitgevoerd.

De specifieke effecten van uitgebreide sessies op orchestrator- en entiteitsfuncties worden in de volgende secties beschreven.

### <a name="orchestrator-function-replay"></a>Orchestrator functie replay

Zoals eerder vermeld, worden orchestrator-functies opnieuw afgespeeld met behulp van de inhoud van de tabel **Geschiedenis.** Standaard wordt de orchestrator-functiecode opnieuw afgespeeld telkens wanneer een batch berichten uit een besturingselementwachtrij wordt verwijderd. Zelfs als u het fan-out-, fan-in-patroon gebruikt en wacht op `Task.WhenAll` alle taken `context.df.Task.all` (bijvoorbeeld in .NET of in JavaScript), zullen er herhalingen plaatsvinden wanneer batches van taakreacties in de loop van de tijd worden verwerkt. Wanneer uitgebreide sessies zijn ingeschakeld, worden orchestrator-functie-exemplaren langer in het geheugen bewaard en kunnen nieuwe berichten worden verwerkt zonder een volledige geschiedenisherhaling.

De prestatieverbetering van uitgebreide sessies wordt het vaakst waargenomen in de volgende situaties:

* Wanneer er een beperkt aantal orchestration-exemplaren gelijktijdig wordt uitgevoerd.
* Wanneer orkestraties een groot aantal opeenvolgende acties hebben (bijvoorbeeld honderden aanroepen van de activiteitsfunctie) die snel worden voltooid.
* Wanneer orkestraties fan-out en fan-in een groot aantal acties die rond dezelfde tijd te voltooien.
* Wanneer orchestrator-functies grote berichten moeten verwerken of cpu-intensieve gegevensverwerking moeten uitvoeren.

In alle andere situaties is er meestal geen waarneembare prestatieverbetering voor orchestrator-functies.

> [!NOTE]
> Deze instellingen mogen alleen worden gebruikt nadat een orchestrator-functie volledig is ontwikkeld en getest. Het standaard agressieve replay-gedrag kan handig zijn voor het detecteren van [orchestrator-functiecodebeperkingen](durable-functions-code-constraints.md) schendingen tijdens de ontwikkeling en is daarom standaard uitgeschakeld.

### <a name="entity-function-unloading"></a>Entiteit functie lossen

Entiteitsfuncties verwerken maximaal 20 bewerkingen in één batch. Zodra een entiteit klaar is met het verwerken van een batch bewerkingen, blijft de status van een entiteit bestaan en wordt uit het geheugen gelost. U het lossen van entiteiten uit het geheugen uitstellen met behulp van de instelling uitgebreide sessies. Entiteiten blijven hun statuswijzigingen zoals voorheen aanhouden, maar blijven gedurende de geconfigureerde periode in het geheugen om het aantal belastingen uit Azure Storage te verminderen. Deze vermindering van de belasting van Azure Storage kan de algehele doorvoer van veelgebruikte entiteiten verbeteren.

## <a name="performance-targets"></a>Prestatiedoelen

Bij het plannen van duurzame functies voor een productietoepassing is het belangrijk om vroeg in het planningsproces rekening te houden met de prestatievereisten. Deze sectie behandelt een aantal basisgebruiksscenario's en de verwachte maximale doorvoernummers.

* **Sequentiële activiteitsuitvoering**: In dit scenario wordt een orchestrator-functie beschreven die de ene na de andere reeks activiteitsfuncties uitvoert. Het lijkt het meest op het [voorbeeld van functiechaining.](durable-functions-sequence.md)
* **Parallelle activiteitsuitvoering**: In dit scenario wordt een orchestrator-functie beschreven die veel activiteitsfuncties parallel uitvoert met behulp van het [fan-out-patroon, fan-in.](durable-functions-cloud-backup.md)
* **Parallelle responsverwerking**: Dit scenario is de tweede helft van [het Fan-out, Fan-in patroon.](durable-functions-cloud-backup.md) Het richt zich op de prestaties van de fan-in. Het is belangrijk op te merken dat in tegenstelling tot fan-out, fan-in wordt gedaan door een enkele orchestrator functie instantie, en daarom kan alleen draaien op een enkele VM.
* **Externe gebeurtenisverwerking:** dit scenario vertegenwoordigt één instantie van de orchestratorfunctie die één voor één op [externe gebeurtenissen](durable-functions-external-events.md)wacht.
* **Verwerking van entiteitsbewerking:** in dit scenario wordt getest hoe snel _een enkele_ [tegenentiteit](durable-functions-entities.md) een constante stroom bewerkingen kan verwerken.

> [!TIP]
> In tegenstelling tot fan-out, fan-in operaties zijn beperkt tot een enkele VM. Als uw toepassing het fan-out-, ventilator-in-patroon gebruikt en u zich zorgen maakt over de prestaties van ventilatoren, u overwegen de ventilator-uitventilatoren van de activiteitsfunctie te verdelen over meerdere [suborchestrations.](durable-functions-sub-orchestrations.md)

In de volgende tabel worden de verwachte *maximale* doorvoernummers voor de eerder beschreven scenario's weergegeven. 'Instantie' verwijst naar één exemplaar van een orchestrator-functie die wordt uitgevoerd op één kleine[(A1)](../../virtual-machines/sizes-previous-gen.md)VM in Azure App Service. In alle gevallen wordt ervan uitgegaan dat [uitgebreide sessies](#orchestrator-function-replay) zijn ingeschakeld. De werkelijke resultaten kunnen variëren afhankelijk van het CPU- of I/O-werk dat door de functiecode wordt uitgevoerd.

| Scenario | Maximale doorvoer |
|-|-|
| Sequentiële uitvoering van de activiteit | 5 activiteiten per seconde, per exemplaar |
| Parallelle activiteitsuitvoering (uitwaaiering) | 100 activiteiten per seconde, per geval |
| Parallelle responsverwerking (ventilator-in) | 150 reacties per seconde, per exemplaar |
| Externe gebeurtenisverwerking | 50 gebeurtenissen per seconde, per exemplaar |
| Verwerking van de bewerking van entiteiten | 64 bewerkingen per seconde |

> [!NOTE]
> Deze nummers zijn actueel vanaf de v1.4.0 (GA) release van de extensie Duurzame functies. Deze getallen kunnen in de loop van de tijd veranderen naarmate de functie rijpt en naarmate optimalisaties worden uitgevoerd.

Als u de doorvoernummers die u verwacht niet ziet en uw CPU- en geheugengebruik gezond lijkt, controleert u of de oorzaak gerelateerd is aan [de status van uw opslagaccount.](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) De extensie Duurzame functies kan een azure-opslagaccount aanzienlijk belasten en voldoende hoge belastingen kunnen leiden tot beperking van opslagaccount.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over noodherstel en geodistributie](durable-functions-disaster-recovery-geo-distribution.md)
