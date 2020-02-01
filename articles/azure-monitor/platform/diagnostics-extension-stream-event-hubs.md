---
title: Azure Diagnostics gegevens streamen naar Event Hubs
description: Azure Diagnostics met Event Hubs end-to-end configureren, met inbegrip van de richt lijnen voor algemene scenario's.
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 111fab880887b54b2415d433bda2368c951381bd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901215"
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Azure Diagnostics gegevens in het warme pad streamen met behulp van Event Hubs
Azure Diagnostics biedt flexibele manieren voor het verzamelen van metrische gegevens en logboeken van virtuele machines van Cloud Services (Vm's) en het overdragen van resultaten naar Azure Storage. Vanaf het tijds bestek van maart 2016 (SDK 2,9) kunt u Diagnostische gegevens verzenden naar aangepaste gegevens bronnen en dynamische padgegevens in seconden overdragen met behulp van [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/).

Ondersteunde gegevens typen zijn:

* ETW-gebeurtenissen (Event Tracing for Windows)
* Prestatiemeteritems
* Windows-gebeurtenis logboeken, inclusief toepassings Logboeken in het Windows-gebeurtenis logboek
* Logboeken van Azure Diagnostics-infrastructuur

In dit artikel wordt beschreven hoe u Azure Diagnostics kunt configureren met Event Hubs van end-to-end. Er worden ook richt lijnen gegeven voor de volgende algemene scenario's:

* De logboeken en metrische gegevens aanpassen die worden verzonden naar Event Hubs
* Configuraties in elke omgeving wijzigen
* Event Hubs stream-gegevens weer geven
* Problemen met de verbinding oplossen  

## <a name="prerequisites"></a>Vereisten
Event Hubs het ontvangen van gegevens van Azure Diagnostics wordt ondersteund in Cloud Services, Vm's, Virtual Machine Scale Sets en Service Fabric vanaf de Azure SDK 2,9 en de bijbehorende Azure-Hulpprogram Ma's voor Visual Studio.

* Azure Diagnostics extensie 1,6 ([Azure SDK voor .net 2,9 of hoger](https://azure.microsoft.com/downloads/) is gericht op deze standaard instelling)
* [Visual Studio 2013 of hoger](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Bestaande configuraties van Azure Diagnostics in een toepassing met behulp van een *. wadcfgx* -bestand en een van de volgende methoden:
  * Visual Studio: [Diagnostische gegevens configureren voor Azure Cloud Services en virtual machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)
  * Windows Power shell: [Diagnostische gegevens inschakelen in Azure Cloud Services met behulp van Power shell](../../cloud-services/cloud-services-diagnostics-powershell.md)
* Event Hubs naam ruimte die is ingericht per artikel, aan de [slag met Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Azure Diagnostics verbinden met Event Hubs Sink
Standaard worden door Azure Diagnostics altijd logboeken en metrische gegevens naar een Azure Storage-account verzonden. Een toepassing kan ook gegevens naar Event Hubs verzenden door een nieuwe sectie **sinks** toe te voegen onder het element **PublicConfig** / **WadCfg** van het bestand *. wadcfgx* . In Visual Studio wordt het *. wadcfgx* -bestand opgeslagen in het volgende pad: **Cloud Service Project** > **Roles** >  **(rolnaam)**  > bestand **Diagnostics. wadcfgx** .

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

In dit voor beeld wordt de URL van de Event Hub ingesteld op de volledig gekwalificeerde naam ruimte van de Event Hub: Event Hubs naam ruimte + '/' + Event Hub name.  

De Event Hub URL wordt weer gegeven in het [Azure Portal](https://go.microsoft.com/fwlink/?LinkID=213885) op het event hubs dash board.  

De **sink** -naam kan worden ingesteld op een wille keurige geldige teken reeks zolang dezelfde waarde consistent wordt gebruikt in het configuratie bestand.

> [!NOTE]
> Er zijn mogelijk extra sinks, zoals *applicationInsights* die in deze sectie zijn geconfigureerd. Met Azure Diagnostics kunnen een of meer sinks worden gedefinieerd als elke Sink ook wordt gedeclareerd in de sectie **PrivateConfig** .  
>
>

De Event Hubs Sink moet ook worden gedeclareerd en gedefinieerd in de sectie **PrivateConfig** van het *wadcfgx* -configuratie bestand.

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

De `SharedAccessKeyName` waarde moet overeenkomen met een sleutel en beleid van een Shared Access Signature (SAS) die is gedefinieerd in de **Event hubs** naam ruimte. Blader naar het Event Hubs dash board in de [Azure Portal](https://portal.azure.com), klik op het tabblad **configureren** en stel een benoemd beleid in (bijvoorbeeld ' SendRule ') dat machtigingen voor *verzenden* heeft. De **Storage account** wordt ook gedeclareerd in **PrivateConfig**. Als ze werken, hoeft u hier geen waarden te wijzigen. In dit voor beeld laten we de waarden leeg, wat een teken is dat een stroomafwaartse Asset de waarden zal instellen. Bijvoorbeeld, het configuratie bestand *ServiceConfiguration. Cloud. cscfg* Environment stelt de naam en sleutels van de omgeving in.  

> [!WARNING]
> De Event Hubs SAS-sleutel wordt opgeslagen als tekst zonder opmaak in het *. wadcfgx* -bestand. Deze sleutel wordt vaak ingecheckt bij broncode beheer of is beschikbaar als een asset in uw build-server, dus u moet deze zo nodig beveiligen. We raden u aan om hier een SAS-sleutel te gebruiken met *alleen machtigingen verzenden* , zodat een kwaadwillende gebruiker naar de Event hub kan schrijven, maar niet kan Luis teren of beheren.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Azure Diagnostics configureren voor het verzenden van Logboeken en metrische gegevens naar Event Hubs
Zoals eerder is besproken, worden alle standaard-en aangepaste diagnostische gegevens, dat wil zeggen, gegevens en Logboeken, automatisch verzonden naar Azure Storage in de geconfigureerde intervallen. Met Event Hubs en een extra sink kunt u elk hoofd-of Leaf-knoop punt in de hiërarchie opgeven dat naar de Event Hub moet worden verzonden. Dit omvat ETW-gebeurtenissen, prestatie meter items, Windows-gebeurtenis logboeken en toepassings Logboeken.   

Het is belang rijk om te bepalen hoeveel gegevens punten eigenlijk moeten worden overgedragen naar Event Hubs. Normaal gesp roken verzenden ontwikkel aars gegevens met een laag wacht tijd die snel moeten worden verbruikt en geïnterpreteerd. Voor beelden van systemen die waarschuwingen of regels voor automatisch schalen bewaken. Een ontwikkelaar kan ook een alternatieve analyse opslag of zoek-Store configureren, bijvoorbeeld Azure Stream Analytics, Elasticsearch, een aangepast bewakings systeem of een favoriete bewakings systeem van anderen.

Hier volgen enkele voor beelden van configuraties.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

In het bovenstaande voor beeld wordt de Sink toegepast op het bovenliggende **Performance Counters** -knoop punt in de hiërarchie, wat betekent dat alle onderliggende **Performance Counters** naar Event hubs worden verzonden.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

In het vorige voor beeld wordt de Sink alleen toegepast op drie tellers: **aanvragen in de wachtrij**, **geweigerde aanvragen**en **% processor tijd**.  

In het volgende voor beeld ziet u hoe een ontwikkelaar de hoeveelheid verzonden gegevens kan beperken tot de kritieke meet waarden die worden gebruikt voor de status van deze service.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

In dit voor beeld wordt de Sink toegepast op Logboeken en wordt deze alleen gefilterd op fout niveau tracering.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Een Cloud Services toepassing en diagnose configuratie implementeren en bijwerken
Visual Studio biedt het eenvoudigste pad voor het implementeren van de toepassing en de configuratie van Event Hubs sink. Als u het bestand wilt bekijken en bewerken, opent u het *. wadcfgx* -bestand in Visual Studio, bewerkt u het en slaat u het op. Het pad is een **Cloud Service Project** > **rollen** >  **(rolnaam)**  > **Diagnostische gegevens. wadcfgx**.  

Op dit punt worden alle Update acties voor implementatie en implementatie in Visual Studio, Visual Studio Team System en alle opdrachten of scripts die zijn gebaseerd op MSBuild en het `/t:publish` doel gebruiken de *. wadcfgx* in het verpakkings proces opnemen. Daarnaast implementeren implementaties en updates het bestand in azure met behulp van de juiste Azure Diagnostics agent-extensie op uw Vm's.

Nadat u de toepassing en Azure Diagnostics configuratie hebt geïmplementeerd, ziet u onmiddellijk activiteiten in het dash board van de Event Hub. Dit geeft aan dat u klaar bent om door te gaan met het weer geven van de hot path-gegevens in de listener-client of het analyse hulpprogramma van uw keuze.  

In de volgende afbeelding toont het Event Hubs dash board gezonden het verzenden van diagnostische gegevens naar de Event Hub op een even na 11 uur wordt gestart. Dat is het moment waarop de toepassing is geïmplementeerd met een bijgewerkt *wadcfgx* -bestand en de Sink correct is geconfigureerd.

![][0]  

> [!NOTE]
> Wanneer u updates voor het Azure Diagnostics configuratie bestand (. wadcfgx) maakt, is het raadzaam om de updates te pushen naar de volledige toepassing, evenals de configuratie door middel van Visual Studio Publishing of een Windows Power shell-script.  
>
>

## <a name="view-hot-path-data"></a>Informatie over dynamische paden weer geven
Zoals eerder besproken, zijn er veel gebruiks voorbeelden voor het Luis teren naar en verwerken van Event Hubs gegevens. Een eenvoudige benadering is het maken van een kleine test console toepassing om naar het Event Hub te Luis teren en de uitvoer stroom af te drukken. 

#### <a name="net-sdk-latest-500-or-latertablatest"></a>[.NET SDK nieuwste versie (5.0.0 of hoger)](#tab/latest)
In een console toepassing kunt u de volgende code, die in meer detail wordt uitgelegd in aan de [slag met Event hubs](../../event-hubs/get-started-dotnet-standard-send-v2.md)) plaatsen.

```csharp
using System;
using System.Text;
using System.Threading.Tasks;
using Azure.Storage.Blobs;
using Azure.Messaging.EventHubs;
using Azure.Messaging.EventHubs.Processor;
namespace Receiver1204
{
    class Program
    {
        private static readonly string ehubNamespaceConnectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        private static readonly string eventHubName = "EVENT HUB NAME";
        private static readonly string blobStorageConnectionString = "AZURE STORAGE CONNECTION STRING";
        private static readonly string blobContainerName = "BLOB CONTAINER NAME";

        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClientOptions options = new EventProcessorClientOptions { }
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }

        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));
            return Task.CompletedTask;
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
    }
}
```

#### <a name="net-sdk-legacy-410-or-earliertablegacy"></a>[.NET SDK verouderd (4.1.0 of eerder)](#tab/legacy)

In een console toepassing kunt u de volgende code, die in meer detail wordt uitgelegd in aan de [slag met Event hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)) plaatsen. Houd er rekening mee dat de console toepassing het Nuget-pakket van de [Event processor host](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/)moet bevatten. Vergeet niet om de waarden tussen punt haken in de **hoofd** functie te vervangen door waarden voor uw resources.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>";
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>";
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```
---

## <a name="troubleshoot-event-hubs-sinks"></a>Problemen met Event Hubs-sinks oplossen
* De Event Hub geeft niet de binnenkomende of uitgaande gebeurtenis activiteit zoals verwacht weer.

    Controleer of uw Event Hub is ingericht. Alle verbindings gegevens in het gedeelte **PrivateConfig** van *. wadcfgx* moeten overeenkomen met de waarden van uw resource zoals weer gegeven in de portal. Zorg ervoor dat er een SAS-beleid is gedefinieerd ("SendRule" in het voor beeld) in de portal en dat de machtiging *verzenden* wordt verleend.  
* Na een update wordt in de Event Hub niet langer de activiteit binnenkomende of uitgaande gebeurtenis weer gegeven.

    Controleer eerst of de Event Hub-en configuratie gegevens juist zijn zoals eerder is beschreven. Soms wordt de **PrivateConfig** opnieuw ingesteld in een implementatie-update. De aanbevolen oplossing is om alle wijzigingen aan te brengen in *. wadcfgx* in het project en vervolgens een volledige toepassings update te pushen. Als dit niet mogelijk is, zorgt u ervoor dat de diagnostische update een volledige **PrivateConfig** die de SAS-sleutel bevat, pusht.  
* Ik heb de suggesties uitgevoerd en de Event Hub werkt nog steeds niet.

    Zoek in de Azure Storage-tabel die logboeken en fouten bevat voor Azure Diagnostics zichzelf: **WADDiagnosticInfrastructureLogsTable**. Een optie is het gebruik van een hulp programma zoals [Azure Storage Explorer](https://www.storageexplorer.com) om verbinding te maken met dit opslag account, de tabel te bekijken en een query voor tijds tempel toe te voegen in de afgelopen 24 uur. U kunt het hulp programma gebruiken om een CSV-bestand te exporteren en dit te openen in een toepassing zoals micro soft Excel. In Excel kunt u eenvoudig zoeken naar telefoonkaart teken reeksen, zoals **Event hubs**, om te zien welke fout Er wordt gerapporteerd.  

## <a name="next-steps"></a>Volgende stappen
• Meer [informatie over Event hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Bijlage: voor beeld van een complete Azure Diagnostics-configuratie bestand (. wadcfgx)
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

De complementaire *ServiceConfiguration. Cloud. cscfg* voor dit voor beeld ziet er als volgt uit.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

De equivalente JSON-instellingen voor virtuele machines zijn als volgt:

Open bare instellingen:
```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}

```

Beveiligde instellingen:
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](../../event-hubs/event-hubs-about.md)
* [Een Event Hub maken](../../event-hubs/event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png

