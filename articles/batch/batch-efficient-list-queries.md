---
title: Efficiënte lijst query's ontwerpen
description: Verbeter de prestaties door uw query's te filteren bij het aanvragen van informatie over batch-resources zoals Pools, Jobs, taken en reken knooppunten.
ms.topic: article
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fea8efd4e4946b67754bad98589b728e8d696425
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116108"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>Query's maken om batch resources efficiënt weer te geven

Hier leest u hoe u de prestaties van uw Azure Batch-toepassing kunt verhogen door de hoeveelheid gegevens die door de service wordt geretourneerd, te verminderen wanneer u een query uitvoert op taken, taken, reken knooppunten en andere resources met de [batch .net][api_net] -bibliotheek.

Bijna alle batch-toepassingen moeten een type bewaking of een andere bewerking uitvoeren die de batch-service uitvoert, vaak regel matig intervallen. Als u bijvoorbeeld wilt bepalen of er taken in de wachtrij voor een taak resteren, moet u gegevens ophalen voor elke taak in de taak. Als u de status van knoop punten in uw pool wilt bepalen, moet u gegevens op elk knoop punt in de pool ophalen. In dit artikel wordt uitgelegd hoe u dergelijke query's op de meest efficiënte manier uitvoert.

> [!NOTE]
> De batch-service biedt speciale API-ondersteuning voor de algemene scenario's voor het tellen van taken in een taak en het tellen van reken knooppunten in de batch-pool. In plaats van een lijst query voor deze te gebruiken, kunt u de bewerkingen [taak aantallen ophalen][rest_get_task_counts] en [aantal aantallen van lijst groepen][rest_get_node_counts] aanroepen. Deze bewerkingen zijn efficiënter dan een lijst query, maar retour neren meer beperkte informatie. Zie [aantal taken en reken knooppunten per status](batch-get-resource-counts.md). 


## <a name="meet-the-detaillevel"></a>Voldoen aan de DetailLevel
In een productie batch-toepassing kunnen entiteiten zoals Jobs, taken en reken knooppunten het aantal duizend tallen. Wanneer u informatie nodig hebt over deze resources, moet u een mogelijke grote hoeveelheid gegevens ' cross ' door kruisen van de batch-service naar uw toepassing op elke query. Door het aantal items en het type informatie dat wordt geretourneerd door een query te beperken, kunt u de snelheid van uw query's verhogen en dus ook de prestaties van uw toepassing.

Dit code fragment voor [batch .net][api_net] API *bevat een* lijst met alle taken die aan een taak zijn gekoppeld, samen met *alle* eigenschappen van elke taak:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

U kunt een veel efficiëntere lijst query uitvoeren, maar door een ' detail niveau ' toe te passen op uw query. U doet dit door een [ODATADetailLevel][odata] -object op te geven voor de methode [JobOperations. ListTasks][net_list_tasks] . Dit fragment retourneert alleen de eigenschappen van de ID, opdracht regel en reken knooppunt voor voltooide taken:

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

Als in dit voorbeeld scenario duizenden taken in de taak zijn, worden de resultaten van de tweede query doorgaans veel sneller geretourneerd dan de eerste. Meer informatie over het gebruik van ODATADetailLevel bij het weer geven van items met de batch .NET API is [hieronder](#efficient-querying-in-batch-net)opgenomen.

> [!IMPORTANT]
> We raden u ten zeerste aan om *altijd* een ODATADetailLevel-object aan te bieden aan uw .net API List-aanroepen om de maximale efficiëntie en prestaties van uw toepassing te garanderen. Als u een detail niveau opgeeft, kunt u de reactie tijden van de batch-service verlagen, het netwerk gebruik verbeteren en het geheugen gebruik door client toepassingen minimaliseren.
> 
> 

## <a name="filter-select-and-expand"></a>Filteren, selecteren en uitvouwen
De [batch .net][api_net] -en [batch][api_rest] -api's bieden de mogelijkheid om zowel het aantal geretourneerde items in een lijst te verminderen als de hoeveelheid gegevens die voor elk item wordt geretourneerd. U doet dit door **filter**-, **Select**-en **expand-teken reeksen** op te geven bij het uitvoeren van lijst query's.

### <a name="filter"></a>Filteren
De filter teken reeks is een expressie waarmee het aantal geretourneerde items wordt verminderd. U kunt bijvoorbeeld alleen de actieve taken voor een taak weer geven of alleen reken knooppunten weer geven die gereed zijn om taken uit te voeren.

* De filter teken reeks bestaat uit een of meer expressies, met een expressie die bestaat uit een eigenschaps naam, een operator en een waarde. De eigenschappen die kunnen worden opgegeven, zijn specifiek voor elk entiteits type waarvoor u een query uitvoert, evenals de Opera tors die voor elke eigenschap worden ondersteund.
* Meerdere expressies kunnen worden gecombineerd met behulp van de `and` logische `or`Opera tors en.
* In dit voor beeld wordt gefilterde teken reeks alleen de actieve `(state eq 'running') and startswith(id, 'renderTask')`' render-taken ' weer gegeven:.

### <a name="select"></a>Selecteer
De teken reeks selecteren beperkt de eigenschaps waarden die voor elk item worden geretourneerd. U geeft een lijst met eigenschapnamen op, en alleen de eigenschaps waarden worden geretourneerd voor de items in de query resultaten.

* De Select-teken reeks bestaat uit een lijst met door komma's gescheiden namen van eigenschappen. U kunt een van de eigenschappen opgeven voor het entiteits type dat u zoekt.
* In dit voor beeld wordt een teken reeks selecteren geeft aan dat er slechts drie eigenschaps `id, state, stateTransitionTime`waarden worden geretourneerd voor elke taak:.

### <a name="expand"></a>Uitvouwen
De Uitvouw teken reeks vermindert het aantal API-aanroepen dat nodig is om bepaalde gegevens te verkrijgen. Wanneer u een Uitvouw teken reeks gebruikt, kan er meer informatie over elk item worden verkregen met één API-aanroep. In plaats van eerst de lijst met entiteiten te verkrijgen en vervolgens informatie te vragen voor elk item in de lijst, gebruikt u een Uitvouw teken reeks om dezelfde informatie in één API-aanroep te verkrijgen. Minder API-aanroepen betekent betere prestaties.

* Net als bij de selectie teken reeks bepaalt de Uitvouw teken reeks of bepaalde gegevens worden opgenomen in de lijst query resultaten.
* De Uitvouw teken reeks wordt alleen ondersteund als deze wordt gebruikt bij het weer geven van taken, taak planningen, taken en groepen. Op dit moment wordt alleen statistische informatie ondersteund.
* Als alle eigenschappen vereist zijn en er geen teken reeks voor selectie is opgegeven, *moet* de Uitvouw teken reeks worden gebruikt om statistische gegevens op te halen. Als er een teken reeks wordt gebruikt om een subset van eigenschappen op te `stats` halen, kan vervolgens worden opgegeven in de teken reeks selecteren en moet de Uitvouw teken reeks niet worden opgegeven.
* In dit voor beeld wordt de teken reeks uitgebreid geeft aan dat er statistische gegevens moeten worden geretourneerd `stats`voor elk item in de lijst:.

> [!NOTE]
> Bij het samen stellen van een van de drie typen query reeksen (filteren, selecteren en uitvouwen), moet u ervoor zorgen dat de namen van de eigenschappen en de aanvraag overeenkomen met die van hun REST API element tegen hangers. Als u bijvoorbeeld werkt met de .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) -klasse, moet u in plaats van **status**een **status** opgeven, ook al is de .net-eigenschap [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State). Zie de onderstaande tabellen voor eigenschaps toewijzingen tussen de .NET-en REST-Api's.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>Regels voor het filteren, selecteren en uitvouwen van teken reeksen
* Eigenschappen in filters, selecteren en Uitvouw teken reeksen moeten worden weer gegeven zoals in de [batch rest][api_rest] -API, zelfs wanneer u [batch .net][api_net] of een van de andere batch-sdk's gebruikt.
* Alle eigenschapnamen zijn hoofdletter gevoelig, maar eigenschaps waarden zijn niet hoofdletter gevoelig.
* Datum-en tijd teken reeksen kunnen een van de twee indelingen hebben en moeten worden `DateTime`voorafgegaan door.
  
  * Voor beeld van de indeling W3C-DTF:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * Voor beeld van RFC 1123-indeling:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* Booleaanse teken reeksen zijn `true` ofwel `false`of.
* Als er een ongeldige eigenschap of operator is opgegeven, `400 (Bad Request)` treedt er een fout op.

## <a name="efficient-querying-in-batch-net"></a>Efficiënte query's uitvoeren in batch .NET
Binnen de [batch .net][api_net] -API wordt de klasse [ODATADetailLevel][odata] gebruikt voor het leveren van filters, selecteren en uitvouwen van teken reeksen om bewerkingen weer te geven. De klasse ODataDetailLevel heeft drie open bare teken reeks eigenschappen die kunnen worden opgegeven in de constructor of die rechtstreeks op het object kan worden ingesteld. Vervolgens geeft u het ODataDetailLevel-object als een para meter door aan de verschillende lijst bewerkingen, zoals [ListPools][net_list_pools], [ListJobs][net_list_jobs]en [ListTasks][net_list_tasks].

* [ODATADetailLevel][odata]. [FilterClause][odata_filter]: Beperk het aantal geretourneerde items.
* [ODATADetailLevel][odata]. [SelectClause][odata_select]: Geef op welke eigenschaps waarden met elk item worden geretourneerd.
* [ODATADetailLevel][odata]. [ExpandClause][odata_expand]: gegevens ophalen voor alle items in één API-aanroep in plaats van afzonderlijke aanroepen voor elk item.

In het volgende code fragment wordt de batch .NET API gebruikt om de batch-service op een efficiënte manier te doorzoeken op de statistieken van een specifieke set met groepen. In dit scenario heeft de batch gebruiker zowel test-als productie Pools. De test groep-Id's worden voorafgegaan door ' test ' en de productie pool-Id's worden voorafgegaan door ' Prod '. In het fragment is *myBatchClient* een correct geïnitialiseerd exemplaar van de klasse [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) .

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
> Een instantie van [ODATADetailLevel][odata] die is geconfigureerd met SELECT-en Expand-componenten, kan ook worden door gegeven aan de toepasselijke Get-methoden, zoals [pool Operations. GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__), om de hoeveelheid gegevens die wordt geretourneerd, te beperken.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch-REST naar .NET API-toewijzingen
Eigenschaps namen in filters, selecteren en uitvouwen teken reeksen *moeten* overeenkomen met hun rest APIe equivalenten, zowel naam als case. De onderstaande tabellen bevatten toewijzingen tussen de .NET-en REST API-equivalenten.

### <a name="mappings-for-filter-strings"></a>Toewijzingen voor filter teken reeksen
* **.Net-lijst methoden**: voor elk van de .net API-methoden in deze kolom wordt een [ODATADetailLevel][odata] -object als para meter geaccepteerd.
* **Rest-lijst aanvragen**: elke rest API pagina die aan deze kolom is gekoppeld, bevat een tabel die de eigenschappen en bewerkingen specificeert die zijn toegestaan in *filter* teken reeksen. U gebruikt deze eigenschapnamen en bewerkingen wanneer u een [ODATADetailLevel. FilterClause][odata_filter] -teken reeks maakt.

| .NET-lijst methoden | Aanvragen van de REST-lijst |
| --- | --- |
| [Methode certificateoperations. ListCertificates][net_list_certs] |[De certificaten in een account weer geven][rest_list_certs] |
| [CloudTask. ListNodeFiles][net_list_task_files] |[De bestanden weer geven die zijn gekoppeld aan een taak][rest_list_task_files] |
| [JobOperations. ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[De status van de taken voor taak voorbereiding en taak release voor een taak weer geven][rest_list_jobprep_status] |
| [JobOperations. ListJobs][net_list_jobs] |[De taken in een account weer geven][rest_list_jobs] |
| [JobOperations. ListNodeFiles][net_list_nodefiles] |[De bestanden op een knoop punt weer geven][rest_list_nodefiles] |
| [JobOperations. ListTasks][net_list_tasks] |[De taken weer geven die aan een taak zijn gekoppeld][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[De taak planningen in een account weer geven][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[De taken weer geven die zijn gekoppeld aan een taak schema][rest_list_schedule_jobs] |
| [Pool Operations. ListComputeNodes][net_list_compute_nodes] |[De reken knooppunten in een pool weer geven][rest_list_compute_nodes] |
| [Pool Operations. ListPools][net_list_pools] |[De Pools in een account weer geven][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>Toewijzingen voor selectie teken reeksen
* **Batch .net-typen**: batch .net API-typen.
* **Rest API entiteiten**: elke pagina in deze kolom bevat een of meer tabellen met de rest API eigenschaps namen voor het type. Deze eigenschapnamen worden gebruikt wanneer *u teken reeksen* samen stelt. U gebruikt dezelfde eigenschaps namen wanneer u een teken reeks voor [ODATADetailLevel. SelectClause][odata_select] bouwt.

| Batch .NET-typen | REST API entiteiten |
| --- | --- |
| [Certificaat][net_cert] |[Informatie over een certificaat ophalen][rest_get_cert] |
| [Eigenschap cloudjob][net_job] |[Informatie over een taak ophalen][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[Informatie over een taak schema ophalen][rest_get_schedule] |
| [ComputeNode][net_node] |[Informatie over een knoop punt ophalen][rest_get_node] |
| [CloudPool][net_pool] |[Informatie over een groep ophalen][rest_get_pool] |
| [CloudTask][net_task] |[Informatie over een taak ophalen][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Voor beeld: een filter teken reeks maken
Wanneer u een filter teken reeks voor [ODATADetailLevel. FilterClause][odata_filter]bouwt, raadpleegt u de bovenstaande tabel onder toewijzingen voor filter reeksen om de rest API-documentatie pagina te vinden die overeenkomt met de lijst bewerking die u wilt uitvoeren. U vindt de filter eigenschappen en de ondersteunde Opera tors in de eerste kolom MultiRow op die pagina. Als u alle taken wilt ophalen waarvan de afsluit code niet gelijk is aan nul, bijvoorbeeld deze rij in [de lijst met taken die aan een taak zijn gekoppeld][rest_list_tasks] , geeft u de toepasselijke eigenschap teken reeks en toegestane Opera tors op:

| Eigenschap | Toegestane bewerkingen | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

De filter teken reeks voor het weer geven van alle taken met een niet-nul afsluit code zou dus als volgt zijn:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Voor beeld: een SELECT-teken reeks maken
Als u [ODATADetailLevel. SelectClause][odata_select]wilt maken, raadpleegt u de bovenstaande tabel onder ' toewijzingen voor Select strings ' en navigeert u naar de rest API pagina die overeenkomt met het type entiteit dat u wilt weer geven. U vindt de eigenschappen die kunnen worden geselecteerd en de Opera tors die worden ondersteund in de eerste kolom MultiRow op die pagina. Als u alleen de ID en de opdracht regel voor elke taak in een lijst wilt ophalen, kunt u deze rijen in de desbetreffende tabel vinden op [informatie over een taak ophalen][rest_get_task]:

| Eigenschap | Type | Opmerkingen |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

De teken reeks selecteren voor het opnemen van alleen de ID en de opdracht regel met elke weer gegeven taak is:

`id, commandLine`

## <a name="code-samples"></a>Codevoorbeelden
### <a name="efficient-list-queries-code-sample"></a>Code voorbeeld van een efficiënte lijst query's
Bekijk het [EfficientListQueries][efficient_query_sample] -voorbeeld project op github om te zien hoe een efficiënte lijst query de prestaties van een toepassing kan beïnvloeden. Met deze C#-console toepassing wordt een groot aantal taken gemaakt en toegevoegd aan een taak. Vervolgens worden er meerdere aanroepen naar de methode [JobOperations. ListTasks][net_list_tasks] gemaakt en worden [ODATADetailLevel][odata] -objecten door gegeven die zijn geconfigureerd met verschillende eigenschaps waarden om de hoeveelheid gegevens die moeten worden geretourneerd, te variëren. Dit levert uitvoer op die er ongeveer als volgt uitziet:

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

Zoals in de verstreken tijd wordt weer gegeven, kunt u de reactie tijden van query's aanzienlijk verlagen door de eigenschappen en het aantal geretourneerde items te beperken. U kunt deze en andere voorbeeld projecten vinden in de opslag plaats [Azure-batch-samples][github_samples] op github.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics-bibliotheek en code voorbeeld
Naast het voor beeld van de EfficientListQueries-code kunt u het [BatchMetrics][batch_metrics] -project vinden in de [Azure-batch-voor beelden github-][github_samples] opslag plaats. In het voorbeeld project van BatchMetrics ziet u hoe u de voortgang van Azure Batch taken efficiënt kunt bewaken met de batch-API.

Het [BatchMetrics][batch_metrics] -voor beeld bevat een .net-klassen bibliotheek project die u kunt opnemen in uw eigen projecten, en een eenvoudig opdracht regel programma voor het uitoefenen en demonstreren van het gebruik van de bibliotheek.

De voorbeeld toepassing in het project demonstreert de volgende bewerkingen:

1. Specifieke kenmerken selecteren om alleen de eigenschappen te downloaden die u nodig hebt
2. Filteren op status overgangs tijden om alleen wijzigingen te downloaden sinds de laatste query

De volgende methode wordt bijvoorbeeld weer gegeven in de BatchMetrics-bibliotheek. Er wordt een ODATADetailLevel geretourneerd waarin wordt aangegeven dat `id` alleen `state` de eigenschappen en moeten worden opgehaald voor de entiteiten waarvoor een query wordt uitgevoerd. Er wordt ook opgegeven dat alleen entiteiten waarvan de status is gewijzigd sinds `DateTime` de opgegeven para meter moet worden geretourneerd.

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
### <a name="parallel-node-tasks"></a>Parallelle knooppunt taken
[Maximaliseren van het gebruik van Azure batch Compute-resources met gelijktijdige knooppunt taken](batch-parallel-node-tasks.md) is een ander artikel dat betrekking heeft op de prestaties van batch-toepassingen. Sommige typen werk belastingen kunnen profiteren van het uitvoeren van parallelle taken op grotere, maar minder reken knooppunten. Bekijk het [voorbeeld scenario](batch-parallel-node-tasks.md#example-scenario) in het artikel voor meer informatie over een dergelijk scenario.


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
