---
title: Aanbevolen procedures voor Azure-functies
description: Lees aanbevolen procedures en patronen voor Azure-functies.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277776"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>De prestaties en betrouwbaarheid van Azure Functions verbeteren

Dit artikel biedt richtlijnen om de prestaties en betrouwbaarheid van uw [serverloze functie-apps](https://azure.microsoft.com/solutions/serverless/) te verbeteren.  

## <a name="general-best-practices"></a>Algemene best practices

Hieronder vindt u de aanbevolen procedures voor het bouwen en ontwerpen van uw serverloze oplossingen met Azure-functies.

### <a name="avoid-long-running-functions"></a>Lange lopende functies vermijden

Grote, langlopende functies kunnen onverwachte time-outproblemen veroorzaken. Zie [time-outs](functions-scale.md#timeout)van de functie-app voor meer informatie over de time-outs voor een bepaald hostingplan. 

Een functie kan groot worden door veel Node.js-afhankelijkheden. Het importeren van afhankelijkheden kan ook leiden tot verhoogde laadtijden die resulteren in onverwachte time-outs. Afhankelijkheden worden zowel expliciet als impliciet geladen. Een enkele module geladen door uw code kan zijn eigen extra modules laden. 

Waar mogelijk u grote functies refactoren in kleinere functiesets die samenwerken en reacties snel retourneren. Een webhook of HTTP-triggerfunctie kan bijvoorbeeld een bevestigingsantwoord binnen een bepaalde tijdslimiet vereisen. het is gebruikelijk voor webhooks om een onmiddellijke reactie te vereisen. U de HTTP-triggerpayload doorgeven aan een wachtrij die moet worden verwerkt door een wachtrijtriggerfunctie. Met deze aanpak u het werkelijke werk uitstellen en een onmiddellijke reactie retourneren.


### <a name="cross-function-communication"></a>Communicatie met verschillende functies

[Duurzame functies](durable/durable-functions-overview.md) en [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zijn gebouwd om statusovergangen en communicatie tussen meerdere functies te beheren.

Als u geen duurzame functies of logische apps gebruikt om met meerdere functies te integreren, u het beste opslagwachtrijen gebruiken voor cross-function communicatie. De belangrijkste reden is dat opslagwachtrijen goedkoper en veel gemakkelijker te bieden zijn dan andere opslagopties. 

Afzonderlijke berichten in een opslagwachtrij zijn beperkt in grootte tot 64 KB. Als u grotere berichten tussen functies moet doorgeven, kan een Azure Service Bus-wachtrij worden gebruikt om berichtformaten tot 256 KB in de standaardlaag en maximaal 1 MB in de Premium-laag te ondersteunen.

Servicebus-onderwerpen zijn handig als u berichtfiltering nodig hebt voordat u wordt verwerkt.

Gebeurtenishubs zijn handig om communicatie met een hoog volume te ondersteunen.


### <a name="write-functions-to-be-stateless"></a>Schrijffuncties om stateloos te zijn 

Functies moeten stateloos en idempotent indien mogelijk. Koppel alle vereiste statusgegevens aan uw gegevens. Een order die wordt verwerkt, heeft `state` bijvoorbeeld waarschijnlijk een geassocieerd lid. Een functie kan een order verwerken op basis van die status, terwijl de functie zelf stateloos blijft. 

Idempotente functies worden vooral aanbevolen met timer triggers. Als u bijvoorbeeld iets hebt dat absoluut één keer per dag moet worden uitgevoerd, schrijft u het zodat het op elk moment van de dag met dezelfde resultaten kan worden uitgevoerd. De functie kan worden afgesloten wanneer er een bepaalde dag geen werk is. Ook als een vorige run niet is voltooid, moet de volgende run verder gaan waar het gebleven was.


### <a name="write-defensive-functions"></a>Defensieve functies schrijven

Stel dat uw functie op elk gewenst moment een uitzondering kan tegenkomen. Ontwerp uw functies met de mogelijkheid om verder te gaan vanaf een vorig fail point tijdens de volgende uitvoering. Overweeg een scenario waarvoor de volgende acties vereist zijn:

1. Query voor 10.000 rijen in een database.
2. Maak een wachtrijbericht voor elk van deze rijen om verder op de lijn te verwerken.
 
Afhankelijk van hoe complex uw systeem is, u hebben: betrokken downstream-diensten zich slecht gedragen, netwerkuitval, of quotum limieten bereikt, enz. Al deze kunnen op elk gewenst moment van invloed zijn op uw functie. Je moet je functies ontwerpen om erop voorbereid te zijn.

Hoe reageert uw code als er een fout optreedt nadat u 5.000 van deze items in een wachtrij hebt geplaatst voor verwerking? Items bijhouden in een set die u hebt voltooid. Anders u ze de volgende keer opnieuw invoegen. Deze dubbele invoeging kan een ernstige impact hebben op uw werkstroom, dus [maak uw functies idempotent](functions-idempotent.md). 

Als een wachtrijitem al is verwerkt, moet u toestaan dat uw functie een no-op is.

Profiteer van defensieve maatregelen die al zijn geleverd voor onderdelen die u gebruikt in het Azure Functions-platform. Zie bijvoorbeeld **Gifwachtrijberichten verwerken** in de documentatie voor triggers en bindingen van [Azure Storage Queue.](functions-bindings-storage-queue-trigger.md#poison-messages) 

## <a name="scalability-best-practices"></a>Aanbevolen procedures voor schaalbaarheid

Er zijn een aantal factoren die van invloed zijn op de manier waarop exemplaren van uw functie-app worden geschaald. De details worden vermeld in de documentatie voor [functieschaling.](functions-scale.md)  Hieronder volgen enkele aanbevolen procedures om een optimale schaalbaarheid van een functie-app te garanderen.

### <a name="share-and-manage-connections"></a>Verbindingen delen en beheren

Verbindingen met externe bronnen zoveel mogelijk opnieuw gebruiken. Bekijk [hoe u verbindingen in Azure-functies beheert.](./manage-connections.md)

### <a name="avoid-sharing-storage-accounts"></a>Vermijd het delen van opslagaccounts

Wanneer u een functie-app maakt, moet u deze koppelen aan een opslagaccount. De opslagaccountverbinding wordt onderhouden in de [azurewebjobsstorage-toepassingsinstelling](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Mix test- en productiecode niet in dezelfde functie-app

Functies binnen een functie-app delen resources. Het geheugen wordt bijvoorbeeld gedeeld. Als u een functie-app in productie gebruikt, voegt u er geen testgerelateerde functies en resources aan toe. Dit kan onverwachte overhead veroorzaken tijdens de uitvoering van de productiecode.

Wees voorzichtig met wat u laadt in uw apps voor productiefuncties. Het geheugen wordt gemiddeld over elke functie in de app.

Als u een gedeelde verzameling hebt waarnaar wordt verwezen in meerdere .NET-functies, plaatst u deze in een gemeenschappelijke gedeelde map. Anders u per ongeluk meerdere versies van dezelfde binaire implementeren die zich anders gedragen tussen functies.

Gebruik geen verbose logging in productiecode, wat een negatieve impact heeft op de prestaties.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Async-code gebruiken, maar vermijd het blokkeren van oproepen

Asynchrone programmering is een aanbevolen best practice, vooral bij het blokkeren van I / O-bewerkingen zijn betrokken.

In C#, vermijd altijd `Result` verwijzen naar `Wait` de `Task` eigenschap of aanroepen methode op een instantie. Deze aanpak kan leiden tot draaduitputting.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Meerdere werkprocessen gebruiken

Standaard gebruikt elk hostexemplaar voor Functies één werkproces. Gebruik de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) om de prestaties te verbeteren, vooral met runtimes met één thread zoals Python, om het aantal werkprocessen per host te verhogen (tot 10). Azure Functions probeert vervolgens gelijktijdige functieaanroepen gelijkmatig over deze werknemers te distribueren. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die Functions maakt wanneer u uw toepassing uitschaalt om aan de vraag te voldoen. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Ontvang berichten in batch waar mogelijk

Sommige triggers zoals Event Hub maken het mogelijk om een batch berichten te ontvangen op één aanroep.  Batching berichten heeft veel betere prestaties.  U de maximale `host.json` batchgrootte in het bestand configureren zoals beschreven in de [referentiedocumentatie host.json](functions-host-json.md)

Voor C#-functies u het type wijzigen in een sterk getypte array.  Bijvoorbeeld, in `EventData sensorEvent` plaats van de `EventData[] sensorEvent`methode handtekening zou kunnen zijn .  Voor andere talen moet u de eigenschap kardinaliteit `function.json` `many` expliciet instellen in uw om batching in te schakelen [zoals hier wordt weergegeven.](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10)

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Hostgedrag configureren om gelijktijdigheid beter te verwerken

Het `host.json` bestand in de functie-app maakt configuratie van hostruntime en triggergedrag mogelijk.  Naast batchgedrag u gelijktijdigheid beheren voor een aantal triggers. Vaak kan het aanpassen van de waarden in deze opties elke instantie helpen om op de juiste manier te schalen voor de vereisten van de aangeroepen functies.

Instellingen in het host.json-bestand zijn van toepassing op alle functies in de app, binnen *één exemplaar* van de functie. Als u bijvoorbeeld een functie-app had [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) met twee HTTP-functies en aanvragen ingesteld op 25, telt een verzoek om een http-trigger te tellen voor de gedeelde 25 gelijktijdige aanvragen.  Wanneer die functie-app is geschaald naar 10 exemplaren, staan de twee functies effectief 250 gelijktijdige aanvragen toe (10 exemplaren * 25 gelijktijdige aanvragen per instantie). 

Andere configuratieopties voor hostzijn te vinden in het [configuratieartikel host.json](functions-host-json.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

* [Verbindingen beheren in Azure-functies](manage-connections.md)
* [Aanbevolen procedures voor Azure App Service](../app-service/app-service-best-practices.md)
