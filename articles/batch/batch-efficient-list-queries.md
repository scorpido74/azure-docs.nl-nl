---
title: Efficiënte lijstquery's ontwerpen - Azure Batch | Microsoft Documenten
description: Verhoog de prestaties door uw query's te filteren wanneer u informatie opvraagt over batchbronnen zoals groepen, taken, taken en rekenknooppunten.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: df923ac479ce5f5a3668c18c616b11348dc6c0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022236"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Query's maken om batchbronnen efficiënt weer te geven

Hier vindt u meer informatie over hoe u de prestaties van uw Azure Batch-toepassing verhogen door de hoeveelheid gegevens die door de service wordt geretourneerd, te verminderen wanneer u taken, taken, rekenknooppunten en andere bronnen opvraagt met de [Batch .NET-bibliotheek.][api_net]

Bijna alle Batch-toepassingen moeten een soort controle of andere bewerking uitvoeren die de Batch-service opvraagt, vaak met regelmatige tussenpozen. Als u bijvoorbeeld wilt bepalen of er nog in de wachtrij staanvan taken in een taak, moet u gegevens over elke taak in de taak opvragen. Als u de status van knooppunten in uw groep wilt bepalen, moet u gegevens op elk knooppunt in de groep verzamelen. In dit artikel wordt uitgelegd hoe u dergelijke query's op de meest efficiënte manier uitvoeren.

> [!NOTE]
> De batchservice biedt speciale API-ondersteuning voor de algemene scenario's voor het tellen van taken in een taak en het tellen van compute-knooppunten in batchgroep. In plaats van hiervoor een lijstquery te gebruiken, u de [bewerkingen Aantal taken][rest_get_task_counts] en het knooppunt [lijstgroep][rest_get_node_counts] groep en lijst uitvoeren. Deze bewerkingen zijn efficiënter dan een lijstquery, maar geven beperktere informatie weer. Zie [Taken tellen en rekenknooppunten op status](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Maak kennis met het DetailLevel
In een batchtoepassing voor productiekunnen entiteiten zoals taken, taken en compute nodes in duizenden getalen. Wanneer u informatie opvraagt over deze bronnen, moet een mogelijk grote hoeveelheid gegevens "de draad doorkruisen" van de Batch-service naar uw toepassing voor elke query. Door het aantal items en het type informatie dat door een query wordt geretourneerd, te beperken, u de snelheid van uw query's en dus de prestaties van uw toepassing verhogen.

In [dit batchfragment van de NET][api_net] API-code worden *elke* taak weergegeven die aan een taak is gekoppeld, samen met *alle* eigenschappen van elke taak:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

U echter een veel efficiëntere lijstquery uitvoeren door een detailniveau toe te passen op uw query. Dit doe je door een [ODATADetailLevel-object][odata] te leveren aan de methode [JobOperations.ListTasks.][net_list_tasks] In dit fragment worden alleen de eigenschappen ID, opdrachtregel en gegevensgegevens van voltooide taken geretourneerd:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

In dit voorbeeldscenario worden de resultaten van de tweede query doorgaans veel sneller geretourneerd dan de eerste keer als er duizenden taken in de taak zitten. Hieronder vindt u meer informatie over het gebruik van ODATADetailLevel wanneer u items met de Batch .NET API [aangeeft.](#efficient-querying-in-batch-net)

> [!IMPORTANT]
> We raden u ten zeerste aan *om altijd* een ODATADetailLevel-object aan te bieden aan uw .NET API-lijstoproepen om maximale efficiëntie en prestaties van uw toepassing te garanderen. Door een detailniveau op te geven, u helpen om de responstijden van batchservices te verlagen, het netwerkgebruik te verbeteren en het geheugengebruik per clienttoepassingen te minimaliseren.
> 
> 

## <a name="filter-select-and-expand"></a>Filteren, selecteren en uitvouwen
De [BATCH.NET][api_net] en [Batch REST][api_rest] API's bieden de mogelijkheid om zowel het aantal items dat in een lijst wordt geretourneerd als de hoeveelheid informatie die voor elk wordt geretourneerd, te verminderen. U doet dit door **filter**op te geven, **tekenreeksen** **te selecteren**en uit te vouwen bij het uitvoeren van lijstquery's.

### <a name="filter"></a>Filteren
De filtertekenreeks is een expressie die het aantal items dat wordt geretourneerd vermindert. Vermeld bijvoorbeeld alleen de actieve taken voor een taak of lijst alleen rekenknooppunten die klaar zijn om taken uit te voeren.

* De filtertekenreeks bestaat uit een of meer expressies, met een expressie die bestaat uit een eigenschapsnaam, operator en waarde. De eigenschappen die kunnen worden opgegeven, zijn specifiek voor elk entiteitstype dat u opvraagt, evenals de operatoren die voor elke eigenschap worden ondersteund.
* Meerdere expressies kunnen worden gecombineerd `and` `or`met behulp van de logische operatoren en .
* In deze voorbeeldtekenreeks worden alleen de `(state eq 'running') and startswith(id, 'renderTask')`actieve taken 'renderen' weergegeven: .

### <a name="select"></a>Selecteer
De tekenreeks selecteren beperkt de eigenschapswaarden die voor elk item worden geretourneerd. U geeft een lijst met eigenschapsnamen op en alleen die eigenschapswaarden worden geretourneerd voor de items in de queryresultaten.

* De selectietekenreeks bestaat uit een door komma's gescheiden lijst met eigenschapsnamen. U een van de eigenschappen opgeven voor het entiteitstype dat u opvraagt.
* In dit voorbeeld selecteert u op dat voor elke `id, state, stateTransitionTime`taak slechts drie eigenschapswaarden moeten worden geretourneerd: .

### <a name="expand"></a>Uitvouwen
De uitvouwtekenreeks vermindert het aantal API-aanroepen dat nodig is om bepaalde informatie te verkrijgen. Wanneer u een uitvouwtekenreeks gebruikt, u meer informatie over elk item verkrijgen met één API-aanroep. In plaats van eerst de lijst met entiteiten te verkrijgen en vervolgens informatie op te vragen voor elk item in de lijst, gebruikt u een uitvouwtekenreeks om dezelfde informatie in één API-aanroep te verkrijgen. Minder API-aanroepen betekent betere prestaties.

* Net als bij de selectietekenreeks bepaalt de uitvouwtekenreeks of bepaalde gegevens zijn opgenomen in de queryresultaten van de lijst.
* De uitvouwtekenreeks wordt alleen ondersteund wanneer deze wordt gebruikt in het aanbieden van taken, taakschema's, taken en groepen. Momenteel ondersteunt het alleen statistiekeninformatie.
* Wanneer alle eigenschappen vereist zijn en er geen selectietekenreeks is opgegeven, *moet* de uitvouwtekenreeks worden gebruikt om statistiekeninformatie te krijgen. Als een selecte tekenreeks wordt gebruikt om `stats` een subset van eigenschappen te verkrijgen, kan deze worden opgegeven in de selectietekenreeks en hoeft de uitvouwtekenreeks niet te worden opgegeven.
* In dit voorbeeld wordt opgeeft dat statistiekengegevens moeten `stats`worden geretourneerd voor elk item in de lijst: .

> [!NOTE]
> Wanneer u een van de drie querytekenreekstypen (filter, selecteren en uitvouwen) maakt, moet u ervoor zorgen dat de eigenschapsnamen en -aanvragen overeenkomen met die van de tegenhangers van hun REST API-element. Wanneer u bijvoorbeeld met de klasse .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) werkt, moet u **de status** opgeven in plaats van **status**, ook al is de eigenschap [.NET CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Zie de onderstaande tabellen voor eigenschapstoewijzingen tussen de API's .NET en REST.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regels voor het filteren, selecteren en uitvouwen van tekenreeksen
* Eigenschappennamen in filter-, selecteer- en uitvouwtekenreeksen moeten worden weergegeven zoals in de [API Batch REST][api_rest] , zelfs wanneer u Batch [.NET][api_net] of een van de andere Batch-SDK's gebruikt.
* Alle eigenschapsnamen zijn hoofdlettergevoelig, maar eigenschapswaarden zijn ongevoelig.
* Datum-/tijdtekenreeksen kunnen een van de twee `DateTime`indelingen zijn en moeten worden voorafgegaan door .
  
  * Voorbeeld van de W3C-DTF-indeling:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Voorbeeld van rfc 1123-indeling:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Booleaanse snaren `true` `false`zijn of .
* Als een ongeldige eigenschap of `400 (Bad Request)` operator is opgegeven, treedt er een fout op.

## <a name="efficient-querying-in-batch-net"></a>Efficiënt query's in Batch .NET
Binnen de [Batch .NET][api_net] API wordt de klasse [ODATADetailLevel][odata] gebruikt voor het leveren van filter-, selectie- en uitvouwtekenreeksen om bewerkingen weer te geven. De klasse ODataDetailLevel heeft drie openbare tekenreekseigenschappen die in de constructor kunnen worden opgegeven of die rechtstreeks op het object kunnen worden ingesteld. Vervolgens geeft u het object ODataDetailLevel als parameter door aan de verschillende [lijstbewerkingen,][net_list_pools]zoals ListPools , [ListJobs][net_list_jobs]en [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [Filterclausule:][odata_filter]beperk het aantal artikelen dat wordt geretourneerd.
* [ODATADetailLevel][odata]. [SelecteerClausule:][odata_select]Geef op welke eigenschapswaarden bij elk item worden geretourneerd.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: Gegevens ophalen voor alle items in één API-aanroep in plaats van afzonderlijke aanroepen voor elk item.

In het volgende codefragment wordt de Batch .NET API gebruikt om de Batch-service efficiënt op te vragen voor de statistieken van een specifieke set groepen. In dit scenario heeft de batchgebruiker zowel test- als productiegroepen. De testpool-id's zijn vooraf bevestigd met "test", en de productiepool-id's zijn vooraf bevestigd met "prik". In het fragment is *myBatchClient* een goed geinitialiseerde instantie van de klasse [BatchClient.](/dotnet/api/microsoft.azure.batch.batchclient)

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Een instantie van [ODATADetailLevel][odata] die is geconfigureerd met Selectie- en uitvouwclausules, kan ook worden doorgegeven aan geschikte Methoden voor get, zoals [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), om de hoeveelheid gegevens die wordt geretourneerd te beperken.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST naar .NET API-toewijzingen
Eigenschapnamen in filter-, selectie- en uitvouwtekenreeksen *moeten* hun REST API-tegenhangers weergeven, zowel in naam als in geval. De onderstaande tabellen bieden toewijzingen tussen de tegenhangers .NET en REST API.

### <a name="mappings-for-filter-strings"></a>Toewijzingen voor filtertekenreeksen
* **.NET-lijstmethoden**: Elk van de .NET API-methoden in deze kolom accepteert een [odatadetailniveauobject][odata] als parameter.
* **RESTlijstaanvragen:** Elke REST API-pagina waaraan in deze kolom is gekoppeld, bevat een tabel die de eigenschappen en bewerkingen opgeeft die zijn toegestaan in *filtertekenreeksen.* U gebruikt deze eigenschapsnamen en -bewerkingen wanneer u een [Tekenreeks ODATADetailLevel.FilterClause][odata_filter] maakt.

| .NET-lijstmethoden | Aanvragen van restlijstaanvragen |
| --- | --- |
| [CertificateOperations.ListCertificaten][net_list_certs] |[De certificaten in een account weergeven][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[De bestanden weergeven die aan een taak zijn gekoppeld][rest_list_task_files] |
| [JobOperations.ListJobPreparationandReleaseTaakstatus][net_list_jobprep_status] |[De status van de taken voor het voorbereiden van werk en taakvrijgave voor een taak weergeven][rest_list_jobprep_status] |
| [Vacatures.ListJobs][net_list_jobs] |[De vacatures in een account weergeven][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[De bestanden op een knooppunt weergeven][rest_list_nodefiles] |
| [Taken van Taken.List][net_list_tasks] |[De taken weergeven die aan een taak zijn gekoppeld][rest_list_tasks] |
| [JobscheduleOperations.ListJobSchedules][net_list_job_schedules] |[De taakroosters in een account weergeven][rest_list_job_schedules] |
| [TaakplanningOperations.ListJobs][net_list_schedule_jobs] |[De taken weergeven die zijn gekoppeld aan een taakschema][rest_list_schedule_jobs] |
| [PoolOperations.listComputeNodes][net_list_compute_nodes] |[De compute-knooppunten in een groep weergeven][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[De pools in een account weergeven][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Toewijzingen voor geselecteerde tekenreeksen
* **Batch .NET-typen**: Batch .NET API-typen.
* **REST API-entiteiten**: Elke pagina in deze kolom bevat een of meer tabellen met de eigenschapnamen REST API voor het type. Deze eigenschapsnamen worden gebruikt wanneer u *bepaalde* tekenreeksen maakt. U gebruikt dezelfde eigenschapsnamen wanneer u een [Tekenreeks ODATADetailLevel.SelectClause][odata_select] maakt.

| Batch .NET-typen | REST API-entiteiten |
| --- | --- |
| [Certificaat][net_cert] |[Informatie over een certificaat][rest_get_cert] |
| [CloudJob][net_job] |[Informatie over een taak][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Informatie over een taakschema][rest_get_schedule] |
| [ComputeNode][net_node] |[Informatie over een knooppunt][rest_get_node] |
| [CloudPool (CloudPool)][net_pool] |[Informatie over een groep][rest_get_pool] |
| [CloudTask][net_task] |[Informatie over een taak][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Voorbeeld: een filtertekenreeks maken
Wanneer u een filtertekenreeks voor [ODATADetailLevel.FilterClause][odata_filter]maakt, raadpleegt u de bovenstaande tabel onder 'Toewijzingen voor filtertekenreeksen' om de rest-api-documentatiepagina te vinden die overeenkomt met de lijstbewerking die u wilt uitvoeren. U vindt de filterbare eigenschappen en de ondersteunde operatoren in de eerste tabel met meerdere rijen op die pagina. Als u bijvoorbeeld alle taken waarvan de exitcode niet-nul was, wilt ophalen, geeft deze rij in [Lijst de taken op die aan een taak zijn gekoppeld,][rest_list_tasks] de toepasselijke eigenschappentekenreeks en de toegestane operatoren op:

| Eigenschap | Bewerkingen toegestaan | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

De filtertekenreeks voor het weergeven van alle taken met een niet-nulexitcode zou dus zijn:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Voorbeeld: een geselecteerde tekenreeks maken
Als u [ODATADetailLevel.SelectClause wilt][odata_select]construeren, raadpleegt u de bovenstaande tabel onder 'Toewijzingen voor geselecteerde tekenreeksen' en navigeert u naar de API-pagina REST die overeenkomt met het type entiteit dat u aangeeft. U vindt de selecteerbare eigenschappen en hun ondersteunde operatoren in de eerste tabel met meerdere rijen op die pagina. Als u bijvoorbeeld alleen de id- en opdrachtregel voor elke taak in een lijst wilt ophalen, vindt u deze rijen in de toepasselijke tabel op [Informatie ophalen over een taak:][rest_get_task]

| Eigenschap | Type | Opmerkingen |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

De selectietekenreeks voor het opnemen van alleen de id- en opdrachtregel bij elke vermelde taak is dan:

`id, commandLine`

## <a name="code-samples"></a>Codevoorbeelden
### <a name="efficient-list-queries-code-sample"></a>Voorbeeld van efficiënte lijstquery's
Bekijk het voorbeeldproject [EfficientListQueries][efficient_query_sample] op GitHub om te zien hoe efficiënte lijstquery's de prestaties in een toepassing kunnen beïnvloeden. Deze C#-consoletoepassing maakt en voegt een groot aantal taken toe aan een taak. Vervolgens worden meerdere aanroepen naar de methode [JobOperations.ListTasks][net_list_tasks] en [worden ODATADetailLevel-objecten][odata] die zijn geconfigureerd met verschillende eigenschapswaarden doorgegeven om de hoeveelheid gegevens die moeten worden geretourneerd, te variëren. Het produceert output vergelijkbaar met de volgende:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Zoals weergegeven in de verstreken tijden, u de responstijden van query's sterk verlagen door de eigenschappen en het aantal items dat wordt geretourneerd te beperken. U deze en andere voorbeeldprojecten vinden in de [azure-batch-samples][github_samples] repository op GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-bibliotheek en codevoorbeeld
Naast het bovenstaande voorbeeld van de EfficientListQueries-code vindt u het [BatchMetrics-project][batch_metrics] in de [GitHub-opslagplaats voor azure-batch-samples.][github_samples] Het voorbeeldproject BatchMetrics laat zien hoe u de voortgang van azure batch-batches efficiënt controleren met behulp van de Batch-API.

Het [voorbeeld batchmetrics][batch_metrics] bevat een .NET-klassebibliotheekproject dat u in uw eigen projecten opnemen en een eenvoudig opdrachtregelprogramma om het gebruik van de bibliotheek uit te oefenen en aan te tonen.

De voorbeeldtoepassing binnen het project toont de volgende bewerkingen aan:

1. Specifieke kenmerken selecteren om alleen de eigenschappen te downloaden die u nodig hebt
2. Filteren op de overgangstijden van de status om alleen wijzigingen te downloaden sinds de laatste query

De volgende methode wordt bijvoorbeeld weergegeven in de batchmetrische bibliotheek. Het retourneert een ODATADetailLevel `id` `state` dat aangeeft dat alleen de eigenschappen en eigenschappen moeten worden verkregen voor de entiteiten die worden opgevraagd. Het geeft ook aan dat alleen entiteiten `DateTime` waarvan de status is gewijzigd sinds de opgegeven parameter moet worden geretourneerd.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Volgende stappen
### <a name="parallel-node-tasks"></a>Parallelle knooppunttaken
[Maximaliseer azure batch compute resource usage with concurrent node tasks](batch-parallel-node-tasks.md) is another article related to Batch application performance. Sommige typen workloads kunnen profiteren van het uitvoeren van parallelle taken op grotere- maar minder - compute-knooppunten. Bekijk het [voorbeeldscenario](batch-parallel-node-tasks.md#example-scenario) in het artikel voor meer informatie over een dergelijk scenario.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
