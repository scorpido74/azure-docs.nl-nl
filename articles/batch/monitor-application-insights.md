---
title: Batch bewaken met Azure Application Insights | Microsoft Documenten
description: Meer informatie over het implementeren van een Azure Batch .NET-toepassing met behulp van de Azure Application Insights-bibliotheek.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: b1f4fb0207d4f659861dbd3fdfd1b2d502409935
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022457"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Een Azure Batch .NET-toepassing controleren en debuggen met Toepassingsinzichten

[Application Insights](../azure-monitor/app/app-insights-overview.md) biedt ontwikkelaars een elegante en krachtige manier om toepassingen die zijn geïmplementeerd voor Azure-services te controleren en te debuggen. Gebruik Application Insights om prestatiemeteritems en uitzonderingen te controleren, evenals uw code te instrumenteren met aangepaste statistieken en tracering. Door application insights te integreren met uw Azure Batch-toepassing u diepgaande inzichten krijgen in gedrag ingeburgerd en problemen in bijna realtime onderzoeken.

In dit artikel ziet u hoe u de Application Insights-bibliotheek toevoegt en configureert in uw Azure Batch .NET-oplossing en uw toepassingscode implementeren. Het toont ook manieren om uw toepassing te controleren via de Azure-portal en aangepaste dashboards te bouwen. Voor ondersteuning voor Application Insights in andere talen, bekijk de [documentatie over talen, platforms en integraties.](../azure-monitor/app/platforms.md)

Een voorbeeld C# oplossing met code om dit artikel te begeleiden is beschikbaar op [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). In dit voorbeeld wordt de instrumentatiecode van Application Insights toegevoegd aan het voorbeeld [TopNWords.](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) Als u niet bekend bent met dat voorbeeld, probeert u topnwords eerst te bouwen en uit te voeren. Als u dit doet, krijgt u inzicht in een basisbatchwerkstroom voor het parallel verwerken van een set invoerblobs op meerdere compute nodes. 

> [!TIP]
> Configureer als alternatief uw Batch-oplossing om Application Insights-gegevens weer te geven, zoals VM-prestatiemeteritems in Batch Explorer. [Batch Explorer](https://github.com/Azure/BatchExplorer) is een gratis, rijk uitgeruste, zelfstandige clienttool om Azure Batch-toepassingen te maken, te debuggen en te controleren. Download een [installatiepakket](https://azure.github.io/BatchExplorer/) voor Mac, Linux of Windows. Bekijk de [repo batch-insights](https://github.com/Azure/batch-insights) voor snelle stappen om Application Insights-gegevens in Batch Explorer in te schakelen. 
>

## <a name="prerequisites"></a>Vereisten
* [Visual Studio 2017 of hoger](https://www.visualstudio.com/vs)

* [Batchaccount en gekoppelde opslagrekening](batch-account-create-portal.md)

* [Application Insights-resource](../azure-monitor/app/create-new-resource.md )
  
   * Gebruik de Azure-portal om een Application *Insights-bron*te maken. Selecteer het *type Algemene* **toepassing**.

   * Kopieer de [instrumentatiesleutel](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) van het portaal. Het is later in dit artikel vereist.
  
  > [!NOTE]
  > Mogelijk worden [kosten in rekening gebracht](https://azure.microsoft.com/pricing/details/application-insights/) voor de gegevens die zijn opgeslagen in Application Insights. Dit omvat de diagnostische en monitoring gegevens besproken in dit artikel.
  > 

## <a name="add-application-insights-to-your-project"></a>Toepassingsinzichten toevoegen aan uw project

Het **Microsoft.ApplicationInsights.WindowsServer** NuGet-pakket en de afhankelijkheden ervan zijn vereist voor uw project. Voeg ze toe of herstel ze aan het project van uw toepassing. Als u het pakket `Install-Package` wilt installeren, gebruikt u de opdracht of NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referentietoepassingsinzichten van uw .NET-toepassing met behulp van de naamruimte **Microsoft.ApplicationInsights.**

## <a name="instrument-your-code"></a>Instrument uw code

Om uw code te instrumenteren, moet uw oplossing een Application Insights [TelemetryClient maken.](/dotnet/api/microsoft.applicationinsights.telemetryclient) In het voorbeeld laadt de TelemetryClient de configuratie uit het [bestand ApplicationInsights.config.](../azure-monitor/app/configuration-with-applicationinsights-config.md) Zorg ervoor dat u ApplicationInsights.config in de volgende projecten bijwerkt met uw toepassingsinstrumentsleutel Application Insights: Microsoft.Azure.Batch.Samples.TelemetryStartTask en TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Voeg ook de instrumentatiesleutel toe aan het bestand TopNWords.cs.

In het voorbeeld in TopNWords.cs wordt de volgende [instrumentatieaanroepen](../azure-monitor/app/api-custom-events-metrics.md) uit de API voor Toepassingsinzichten gebruikt:
* `TrackMetric()`- Houdt bij hoe lang een compute node gemiddeld nodig heeft om het vereiste tekstbestand te downloaden.
* `TrackTrace()`- Voegt foutopsporingsoproepen toe aan uw code.
* `TrackEvent()`- Tracks interessante gebeurtenissen vast te leggen.

In dit voorbeeld wordt met opzet uitzonderingsverwerking weggelaten. In plaats daarvan rapporteert Application Insights automatisch onverwerkte uitzonderingen, wat de foutopsporingservaring aanzienlijk verbetert. 

In het volgende fragment wordt uitgelegd hoe u deze methoden gebruiken.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch-initialisator-helper voor telemetrie
Bij het rapporteren van telemetrie voor een bepaalde server en instantie gebruikt Application Insights de Azure VM-rol en de VM-naam voor de standaardwaarden. In de context van Azure Batch wordt in het voorbeeld weergegeven hoe u de naam van de groep en de naam van het rekenknooppunt gebruikt. Gebruik een [telemetrieinitialisator](../azure-monitor/app/api-filtering-sampling.md#add-properties) om de standaardwaarden te overschrijven. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Als u de initialisator voor telemetrie wilt inschakelen, bevat het bestand ApplicationInsights.config in het TopNWordsSample-project het volgende:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>De taak en taken bijwerken om toepassingsinzichten op te nemen

Als u Application Insights correct wilt uitvoeren op uw compute nodes, controleert u of de binaire bestanden correct zijn geplaatst. Voeg de vereiste binaire bestanden toe aan de verzameling resourcebestanden van uw taak, zodat ze worden gedownload op het moment dat uw taak wordt uitgevoerd. De volgende fragmenten zijn vergelijkbaar met code in Job.cs.

Maak eerst een statische lijst met Application Insights-bestanden die u wilt uploaden.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Maak vervolgens de tijdelijke bestanden die door de taak worden gebruikt.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

De `FileToStage` methode is een helperfunctie in het codevoorbeeld waarmee u eenvoudig een bestand van lokale schijf naar een Azure Storage-blob uploaden. Elk bestand wordt later gedownload naar een compute node en verwezen door een taak.

Voeg ten slotte de taken toe aan de taak en voeg de benodigde toepassingsinzichten toe.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Gegevens weergeven in de Azure-portal

Nu u de taak en taken hebt geconfigureerd om Toepassingsinzichten te gebruiken, voert u de voorbeeldtaak uit in uw groep. Navigeer naar de Azure-portal en open de Application Insights-bron die u hebt ingericht. Nadat de groep is ingericht, moet u beginnen met het zien van gegevens die stromen en worden geregistreerd. De rest van dit artikel raakt aan slechts een paar Application Insights functies, maar voel je vrij om de volledige feature set te verkennen.

### <a name="view-live-stream-data"></a>Live streamgegevens bekijken

Als u traceerlogboeken wilt weergeven in uw bron Applications Insights, klikt u op **Live Stream**. In de volgende schermafbeelding ziet u hoe u live gegevens bekijken die afkomstig zijn van de compute-knooppunten in de groep, bijvoorbeeld het CPU-gebruik per compute-knooppunt.

![Gegevens over het gegevensbestand van live streamgegevens](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Tracelogs weergeven

Als u traceerlogboeken wilt weergeven in uw bron Applications Insights, klikt u op **Zoeken**. In deze weergave wordt een lijst weergegeven met diagnostische gegevens die zijn vastgelegd door Application Insights, inclusief traces, gebeurtenissen en uitzonderingen. 

De volgende schermafbeelding laat zien hoe een enkele tracering voor een taak wordt geregistreerd en later wordt opgevraagd voor foutopsporingsdoeleinden.

![Afbeelding van logboeken traceren](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Onverwerkte uitzonderingen weergeven

In de volgende schermafbeeldingen ziet u hoe Application Insights uitzonderingen registreert die uit uw toepassing worden gegooid. In dit geval u binnen enkele seconden na het gooien van de toepassing de uitzondering inzoomen op een specifieke uitzondering en het probleem diagnosticeren.

![Onverwerkte uitzonderingen](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Downloadtijd blob meten

Aangepaste statistieken zijn ook een waardevol hulpmiddel in de portal. U bijvoorbeeld de gemiddelde tijd weergeven die elke compute-knooppunt nodig had om het vereiste tekstbestand te downloaden dat het verwerkte.

Ga als lid van het voorbeelddiagram als een voorbeeldgrafiek:
1. Klik in de bron Application Insights op **Grafiek met statistieken verkenner** > **Toevoegen**.
2. Klik op **Bewerken** op de grafiek die is toegevoegd.
2. Werk de grafiekdetails als volgt bij:
   * **Grafiektype** instellen op **Raster**.
   * **Aggregatie** instellen op **Gemiddeld**.
   * **Groeps instellen** op **Knooppunt**.
   * Selecteer in **Statistieken**in enkele seconden **aangepaste** > **blobdownload**.
   * Pas het **kleurenpalet van het scherm** aan naar keuze. 

![Blob-downloadtijd per knooppunt](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Compute-knooppunten continu bewaken

Het is u mogelijk opgevallen dat alle statistieken, inclusief prestatiemeteritems, alleen worden geregistreerd wanneer de taken worden uitgevoerd. Dit gedrag is handig omdat het de hoeveelheid gegevens beperkt die Application Insights registreert. Er zijn echter gevallen waarin u altijd de compute nodes wilt controleren. Ze kunnen bijvoorbeeld achtergrondwerk uitvoeren dat niet is gepland via de Batch-service. Stel in dit geval een bewakingsproces in dat moet worden uitgevoerd voor de levensduur van het compute-knooppunt. 

Een manier om dit gedrag te bereiken is door een proces te spawnen dat de Application Insights-bibliotheek laadt en op de achtergrond wordt uitgevoerd. In het voorbeeld laadt de starttaak de binaire bestanden op de machine en houdt het een proces voor onbepaalde tijd actief. Configureer het configuratiebestand Application Insights voor dit proces om extra gegevens uit te zenden waarin u geïnteresseerd bent, zoals prestatiemeteritems.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Om de beheerbaarheid van uw oplossing te vergroten, u de assemblage bundelen in een [toepassingspakket.](./batch-application-packages.md) Als u het toepassingspakket vervolgens automatisch wilt implementeren in uw groepen, voegt u een verwijzing naar het toepassingspakket toe aan de poolconfiguratie.
>

## <a name="throttle-and-sample-data"></a>Throttle- en voorbeeldgegevens 

Vanwege het grootschalige karakter van Azure Batch-toepassingen die in productie worden uitgevoerd, u de hoeveelheid gegevens die door Application Insights wordt verzameld, beperken om kosten te beheren. Zie [Sampling in Application Insights](../azure-monitor/app/sampling.md) voor sommige mechanismen om dit te bereiken.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [applicatie-inzichten](../azure-monitor/app/app-insights-overview.md).

* Voor ondersteuning voor Application Insights in andere talen, bekijk de [documentatie over talen, platforms en integraties.](../azure-monitor/app/platforms.md)


