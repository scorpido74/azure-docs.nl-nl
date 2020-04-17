---
title: De WebJobs SDK gebruiken
description: Meer informatie over het schrijven van code voor de WebJobs SDK. Maak gebeurtenisgestuurde achtergrondverwerkingstaken die toegang hebben tot gegevens in Azure en services van derden.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: a1fd22772e72cba4cce3f9fa2751dc0df0e15bb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535595"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>De Azure WebJobs SDK gebruiken voor gebeurtenisgestuurde verwerking op de achtergrond

In dit artikel vindt u richtlijnen voor het werken met de Azure WebJobs SDK. Zie Aan de slag met [de Azure WebJobs SDK voor verwerking](webjobs-sdk-get-started.md)van gebeurtenissen. 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versies

Dit zijn de belangrijkste verschillen tussen versie 3. *x* en versie 2. *x* van de WebJobs SDK:

* Versie 3. *x* voegt ondersteuning toe voor .NET Core.
* In versie 3. *x*, u moet de extensie Opslagbinding die vereist is door de WebJobs SDK expliciet installeren. In versie 2. *x*, de opslagbindingen zijn opgenomen in de SDK.
* Visual Studio-tooling voor .NET Core (3.* x*) projecten verschillen van tooling voor .NET Framework (2.* x*) projecten. Zie [WebJobs ontwikkelen en implementeren met Visual Studio - Azure App Service](webjobs-dotnet-deploy-vs.md)voor meer informatie.

Indien mogelijk worden voorbeelden gegeven voor beide versie 3. *x* en versie 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) is gebouwd op de WebJobs SDK en in dit artikel worden koppelingen naar Azure-functies voor bepaalde onderwerpen aangemaakt. Let op deze verschillen tussen functies en de WebJobs SDK:
> * Azure Functions versie 2. *x* komt overeen met WebJobs SDK versie 3. *x*en Azure-functies 1. *x* komt overeen met WebJobs SDK 2. *x*. Broncoderepositories gebruiken de WebJobs SDK-nummering.
> * Voorbeeldcode voor Azure Functions C#-klassebibliotheken is als WebJobs SDK-code, behalve dat u geen `FunctionName` kenmerk nodig hebt in een WebJobs SDK-project.
> * Sommige bindingstypen worden alleen ondersteund in functies, zoals HTTP (Webhooks) en Event Grid (dat is gebaseerd op HTTP).
>
> Zie [De WebJobs SDK en Azure-functies vergelijken voor](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)meer informatie.

## <a name="webjobs-host"></a>WebJobs-host

De host is een runtime-container voor functies.  Het luistert naar triggers en aanroepen functies. In versie 3. *x*, de host `IHost`is een implementatie van . In versie 2. *x*, u `JobHost` gebruikt het object. U maakt een hostinstantie in uw code en schrijft code om het gedrag ervan aan te passen.

Dit is een belangrijk verschil tussen het direct gebruiken van de WebJobs SDK en het indirect gebruiken via Azure Functions. In Azure-functies regelt de service de host en u de host niet aanpassen door code te schrijven. Met Azure Functions u het hostgedrag aanpassen via instellingen in het host.json-bestand. Deze instellingen zijn tekenreeksen, geen code, en dit beperkt de soorten aanpassingen die u doen.

### <a name="host-connection-strings"></a>Tekenreeksen voor hostverbindingen

De WebJobs SDK zoekt naar Azure Storage- en Azure Service Bus-verbindingstekenreeksen in het bestand local.settings.json wanneer u lokaal of in de omgeving van de WebJob wordt uitgevoerd wanneer u in Azure wordt uitgevoerd. Standaard is een benoemde `AzureWebJobsStorage` opslagverbindingstekenreeks vereist.  

Versie 2. *x* van de SDK u uw eigen namen gebruiken voor deze verbindingstekenreeksen of deze elders opslaan. U namen in [`JobHostConfiguration`]code instellen met behulp van de, zoals hier weergegeven:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Want versie 3. *x* maakt gebruik van de standaard .NET Core-configuratie-API's, er is geen API om namen van verbindingstekenreeksen te wijzigen.

### <a name="host-development-settings"></a>Instellingen voor hostontwikkeling

U de host in de ontwikkelingsmodus uitvoeren om de lokale ontwikkeling efficiënter te maken. Hier volgen enkele instellingen die worden gewijzigd wanneer u in de ontwikkelingsmodus wordt uitgevoerd:

| Eigenschap | Ontwikkelingsinstelling |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`om de uitvoer van logboeken te maximaliseren. |
| `Queues.MaxPollingInterval`  | Een lage waarde om ervoor te zorgen dat wachtrijmethoden onmiddellijk worden geactiveerd.  |
| `Singleton.ListenerLockPeriod` | 15 seconden om te helpen bij een snelle iteratieve ontwikkeling. |

Het proces voor het inschakelen van de ontwikkelingsmodus is afhankelijk van de SDK-versie. 

#### <a name="version-3x"></a>Versie 3. *x*

Versie 3. *x* maakt gebruik van de standaard ASP.NET Core API's. Roep [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) de methode [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) op de instantie aan. Geef een `development`tekenreeks met de naam , zoals in dit voorbeeld:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

De `JobHostConfiguration` klasse `UseDevelopmentSettings` heeft een methode die de ontwikkelingsmodus mogelijk maakt.  In het volgende voorbeeld ziet u hoe u ontwikkelinstellingen gebruikt. Als `config.IsDevelopment` u `true` wilt terugkeren wanneer deze lokaal wordt `AzureWebJobsEnv` uitgevoerd, `Development`stelt u een variabele voor lokale omgeving in met de waarde .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gelijktijdige verbindingen beheren (versie 2.* x*)

In versie 3. *x*, de verbindingslimiet is standaard ingesteld op oneindige verbindingen. Als u om de een of andere reden [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) deze [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) limiet moet wijzigen, u de eigenschap van de klasse gebruiken.

In versie 2. *x*, u beheert het aantal gelijktijdige verbindingen met een host met behulp van de [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. In 2. *x*, moet u deze waarde verhogen vanaf de standaardwaarde van 2 voordat u uw WebJobs-host start.

Alle uitgaande HTTP-aanvragen die u `HttpClient` vanuit `ServicePointManager`een functie doet door flow through te gebruiken. Nadat u de ingestelde `DefaultConnectionLimit` `ServicePointManager` waarde hebt bereikt, begint u aanvragen in de wachtrij te plaatsen voordat u ze verzendt. Stel `DefaultConnectionLimit` dat uw is ingesteld op 2 en uw code maakt 1.000 HTTP-verzoeken. In eerste instantie zijn slechts twee aanvragen toegestaan door naar het besturingssysteem. De andere 998 staan in de rij tot er ruimte voor ze is. Dat betekent `HttpClient` dat je misschien een time-out hebt omdat het lijkt te zijn gemaakt, maar het verzoek is nooit door het besturingssysteem naar de doelserver verzonden. U ziet dus mogelijk gedrag dat niet logisch `HttpClient` lijkt te zijn: uw lokale inwoner neemt 10 seconden de tijd om een aanvraag te voltooien, maar uw service retourt elk verzoek in 200 ms. 

De standaardwaarde voor `Int32.MaxValue`ASP.NET toepassingen is en dat werkt waarschijnlijk goed voor WebJobs die wordt uitgevoerd in een basis- of hoger App-serviceplan. WebJobs heeft doorgaans de instelling Altijd bij nodig en dat wordt alleen ondersteund door basis- en hogere appserviceplannen.

Als uw WebJob wordt uitgevoerd in een gratis of gedeeld app-serviceplan, wordt uw toepassing beperkt door de sandbox app-service, die momenteel een [verbindingslimiet van 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)heeft. Met een niet-gebonden `ServicePointManager`verbindingslimiet in , is het waarschijnlijker dat de sandbox-verbindingsdrempel wordt bereikt en de site wordt afgesloten. In dat geval `DefaultConnectionLimit` kan het instellen op iets lager, zoals 50 of 100, dit voorkomen en nog steeds zorgen voor voldoende doorvoer.

De instelling moet worden geconfigureerd voordat http-aanvragen worden gedaan. Daarom moet de WebJobs-host de instelling niet automatisch aanpassen. Er kunnen HTTP-aanvragen optreden voordat de host wordt gestart, wat kan leiden tot onverwacht gedrag. De beste aanpak is om de `Main` waarde onmiddellijk `JobHost`in te stellen in uw methode voor de initialisatie, zoals hier weergegeven:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Triggers

Functies moeten openbare methoden zijn en moeten [`NoAutomaticTrigger`](#manual-triggers) één triggerkenmerk of het kenmerk hebben.

### <a name="automatic-triggers"></a>Automatische triggers

Automatische triggers roepen een functie aan als reactie op een gebeurtenis. Overweeg dit voorbeeld van een functie die wordt geactiveerd door een bericht dat is toegevoegd aan azure queue-opslag. Het reageert door het lezen van een blob van Azure Blob opslag:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Het `QueueTrigger` kenmerk vertelt de runtime om de functie aan `myqueue-items` te roepen wanneer er een wachtrijbericht in de wachtrij wordt weergegeven. Het `Blob` kenmerk vertelt de runtime om het wachtrijbericht te gebruiken om een blob in de *container met voorbeeldwerkitems* te lezen. De naam van het `samples-workitems` blobitem in de container wordt rechtstreeks`{queueTrigger}`verkregen uit de wachtrijtrigger als een bindende expressie ( ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Handmatige triggers

Als u een functie handmatig `NoAutomaticTrigger` wilt activeren, gebruikt u het kenmerk, zoals hier wordt weergegeven:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Het proces voor het handmatig activeren van de functie is afhankelijk van de SDK-versie.

#### <a name="version-3x"></a>Versie 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Invoer- en uitvoerbindingen

Invoerbindingen bieden een declaratieve manier om gegevens van Azure of services van derden beschikbaar te maken voor uw code. Uitvoerbindingen bieden een manier om gegevens bij te werken. In het artikel [Aan de slag](webjobs-sdk-get-started.md) wordt een voorbeeld van elk weergegeven.

U een methoderetourwaarde gebruiken voor een uitvoerbinding door het kenmerk toe te passen op de retourwaarde van de methode. Zie het voorbeeld in [De retourwaarde azure-functie gebruiken](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindingstypen

Het proces voor het installeren en beheren van bindingstypen is afhankelijk van of u versie 3 gebruikt. *x* of versie 2. *x* van de SDK. U het pakket vinden dat u wilt installeren voor een bepaald bindingstype in het gedeelte 'Pakketten' van het referentieartikel Azure Functions van dat [bindingstype.](#binding-reference-information) Een uitzondering is de trigger en binding bestanden (voor het lokale bestandssysteem), die niet wordt ondersteund door Azure-functies.

#### <a name="version-3x"></a>Versie 3. *x*

In versie 3. *x*, de opslagbindingen `Microsoft.Azure.WebJobs.Extensions.Storage` zijn opgenomen in de verpakking. Roep `AddAzureStorage` de extensiemethode `ConfigureWebJobs` aan in de methode, zoals hier wordt weergegeven:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Als u andere trigger- en bindingstypen wilt gebruiken, `Add<binding>` installeert u het NuGet-pakket dat deze bevat en roept u de extensiemethode aan die in de extensie is geïmplementeerd. Als u bijvoorbeeld een Azure Cosmos DB-binding `Microsoft.Azure.WebJobs.Extensions.CosmosDB` wilt `AddCosmosDB`gebruiken, installeert en aanroept u, zoals dit:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Als u de timertrigger of de binding Bestanden wilt `AddTimers` gebruiken, die deel uitmaken van kernservices, roept u respectievelijk de `AddFiles` of extensiemethoden aan.

#### <a name="version-2x"></a>Versie 2. *x*

Deze trigger- en bindingstypen zijn opgenomen in versie 2. *x* van `Microsoft.Azure.WebJobs` de verpakking:

* Blob Storage
* Queue Storage
* Table Storage

Als u andere trigger- en bindingstypen wilt gebruiken, `Use<binding>` installeert `JobHostConfiguration` u het NuGet-pakket dat deze bevat en roept u een methode op het object aan. Als u bijvoorbeeld een timertrigger wilt `Microsoft.Azure.WebJobs.Extensions` gebruiken, `UseTimers` installeert en roept u de `Main` methode in, zoals hier wordt weergegeven:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Als u de binding `Microsoft.Azure.WebJobs.Extensions` Bestanden `UseFiles`wilt gebruiken, installeert en aanroept u .

### <a name="executioncontext"></a>ExecutionContext

Met WebJobs [`ExecutionContext`]u zich binden aan een. Met deze binding hebt [`ExecutionContext`] u toegang tot de parameter als parameter in uw functiehandtekening. De volgende code gebruikt bijvoorbeeld het contextobject om toegang te krijgen tot de aanroep-id, waarmee u alle logboeken correleren die zijn geproduceerd door een bepaalde functieaanroep.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Het proces voor [`ExecutionContext`] binding aan de is afhankelijk van uw SDK-versie.

#### <a name="version-3x"></a>Versie 3. *x*

Roep `AddExecutionContextBinding` de extensiemethode `ConfigureWebJobs` aan in de methode, zoals hier wordt weergegeven:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

Het `Microsoft.Azure.WebJobs.Extensions` eerder genoemde pakket biedt ook een speciaal bindend `UseCore` type dat u registreren door de methode aan te roepen. Met deze binding [`ExecutionContext`] kunt u een parameter in uw functiehandtekening definiëren, die als volgt is ingeschakeld:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Bindingsconfiguratie

U het gedrag van sommige triggers en bindingen configureren. Het proces voor het configureren ervan is afhankelijk van de SDK-versie.

* **Versie 3. *x*:** Stel `Add<Binding>` de configuratie `ConfigureWebJobs`in wanneer de methode wordt aangeroepen.
* **Versie 2. *x:*** Stel configuratie in door eigenschappen in te `JobHost`stellen in een configuratieobject dat u doorgeeft aan .

Deze bindingsspecifieke instellingen zijn gelijk aan instellingen in het [host.json-projectbestand](../azure-functions/functions-host-json.md) in Azure-functies.

U de volgende bindingen configureren:

* [Azure CosmosDB-trigger](#azure-cosmosdb-trigger-configuration-version-3x)
* [Trigger gebeurtenishubs](#event-hubs-trigger-configuration-version-3x)
* [Queue Storage-trigger](#queue-storage-trigger-configuration)
* [SendGrid-binding](#sendgrid-binding-configuration-version-3x)
* [Trigger servicebus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB triggerconfiguratie (versie 3.* x*)

In dit voorbeeld ziet u hoe u de Azure Cosmos DB-trigger configureert:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Zie het bindende artikel [Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) voor meer informatie.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Gebeurtenishubs activeren configuratie (versie 3.* x*)

In dit voorbeeld ziet u hoe u de trigger gebeurtenishubs configureert:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Zie het bindende artikel [Gebeurtenishubs](../azure-functions/functions-bindings-event-hubs-output.md#hostjson-settings) voor meer informatie.

### <a name="queue-storage-trigger-configuration"></a>Configuratie van wachtrijopslagtrigger

In deze voorbeelden ziet u hoe u de trigger voor wachtrijopslag configureert:

#### <a name="version-3x"></a>Versie 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Zie het bindende artikel [wachtrijopslag.](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)

#### <a name="version-2x"></a>Versie 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Zie de referentie [host.json v1.x voor](../azure-functions/functions-host-json-v1.md#queues)meer informatie.

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid-bindingsconfiguratie (versie 3.* x*)

In dit voorbeeld ziet u hoe u de sendgrid-uitvoerbinding configureert:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Zie het bindende artikel [SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) voor meer informatie.

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus trigger configuratie (versie 3.* x*)

In dit voorbeeld ziet u hoe u de trigger servicebus configureert:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Zie het bindende artikel [Service Bus voor](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) meer informatie.

### <a name="configuration-for-other-bindings"></a>Configuratie voor andere bindingen

Sommige trigger- en bindingstypen definiëren hun eigen aangepaste configuratietypen. Met de trigger Bestand u bijvoorbeeld het hoofdpad opgeven dat moet worden bewaakt, zoals in deze voorbeelden:

#### <a name="version-3x"></a>Versie 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Bindingexpressies

In kenmerkconstructorparameters u expressies gebruiken die worden opgelost in waarden uit verschillende bronnen. In de volgende code wordt bijvoorbeeld `BlobTrigger` een expressie met `filename`de naam expressie het pad voor het kenmerk benoemd. Wanneer deze wordt gebruikt `filename` voor de uitvoerbinding, wordt de naam van de triggeringblob opgelost.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Zie [Expressies en patronen binden](../azure-functions/functions-bindings-expressions-patterns.md) in de documentatie voor Azure-functies voor meer informatie over bindende expressies.

### <a name="custom-binding-expressions"></a>Aangepaste bindingsexpressies

Soms wilt u een wachtrijnaam, een blobnaam of -container of een tabelnaam in code opgeven in plaats van deze te hard coderen. U bijvoorbeeld de wachtrijnaam voor `QueueTrigger` het kenmerk opgeven in een configuratiebestand of omgevingsvariabele.

U dat doen `NameResolver` door een `JobHostConfiguration` object in het object door te geven. U neemt tijdelijke aanduidingen op in trigger- `NameResolver` of bindingskenmerkconstructorparameters en uw code bevat de werkelijke waarden die moeten worden gebruikt in plaats van die tijdelijke aanduidingen. U identificeert tijdelijke aanduidingen door hen te omringen met procent (%) tekenen, zoals hier te zien:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Met deze code `logqueuetest` u een wachtrij gebruiken `logqueueprod` met de naam in de testomgeving en een wachtrij met de naam in productie. In plaats van een harde wachtrijnaam geeft u de `appSettings` naam op van een vermelding in de verzameling.

Er is een `NameResolver` standaardinstelling die van kracht wordt als u geen aangepaste optie opgeeft. De standaard krijgt waarden van app-instellingen of omgevingsvariabelen.

Uw `NameResolver` klas krijgt de `appSettings`wachtrijnaam van , zoals hier wordt weergegeven:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versie 3. *x*

U configureert de resolver met behulp van afhankelijkheidsinjectie. Deze monsters vereisen `using` de volgende verklaring:

```cs
using Microsoft.Extensions.DependencyInjection;
```

U voegt de resolver [`ConfigureServices`] toe [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)door de extensiemethode aan te roepen op , zoals in dit voorbeeld:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

Geef `NameResolver` uw klas `JobHost` door aan het object, zoals hier wordt weergegeven:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions `INameResolver` implementeert om waarden uit app-instellingen te halen, zoals in het voorbeeld wordt weergegeven. Wanneer u de WebJobs SDK rechtstreeks gebruikt, u een aangepaste implementatie schrijven die tijdelijke vervangingswaarden krijgt van welke bron u maar wilt.

## <a name="binding-at-runtime"></a>Binding bij runtime

Als u wat werk in uw functie moet doen `Queue` `Blob`voordat `Table`u een bindend `IBinder` kenmerk gebruikt, zoals, of, u de interface gebruiken.

In het volgende voorbeeld wordt een invoerwachtrijbericht weergegeven en wordt een nieuw bericht gemaakt met dezelfde inhoud in een uitvoerwachtrij. De naam van de uitvoerwachtrij wordt ingesteld op code in de hoofdtekst van de functie.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Zie [Binding tijdens runtime in](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) de azure-functiesdocumentatie voor meer informatie.

## <a name="binding-reference-information"></a>Bindende referentie-informatie

De documentatie over Azure-functies bevat referentiegegevens over elk bindingstype. U vindt de volgende informatie in elk bindend referentieartikel. (Dit voorbeeld is gebaseerd op de opslagwachtrij.)

* [Pakketten](../azure-functions/functions-bindings-storage-queue.md). Het pakket dat u moet installeren om ondersteuning voor de binding op te nemen in een WebJobs SDK-project.
* [Voorbeelden](../azure-functions/functions-bindings-storage-queue-trigger.md). Code monsters. Het voorbeeld van de C#-klassebibliotheek is van toepassing op de WebJobs SDK. Laat het `FunctionName` attribuut weg.
* [Kenmerken](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). De kenmerken die u moet gebruiken voor het bindingstype.
* [Configuratie](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Uitleg van de eigenschappen van het kenmerk en de parameters van de constructor.
* [Gebruik](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). De typen waaraan u binden en informatie over hoe de binding werkt. Bijvoorbeeld: polling algoritme, gif wachtrij verwerking.
  
Zie 'Ondersteunde bindingen' in het artikel [Triggers en bindingen](../azure-functions/functions-triggers-bindings.md#supported-bindings) voor Azure-functies voor een lijst met bindende referentieartikelen. In die lijst worden de HTTP-, Webhooks- en Event Grid-bindingen alleen ondersteund door Azure-functies, niet door de WebJobs SDK.

## <a name="disable-attribute"></a>Kenmerk uitschakelen 

Met [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) het kenmerk u bepalen of een functie kan worden geactiveerd. 

In het volgende voorbeeld wordt `Disable_TestJob` de functie `1` `True` niet uitgevoerd als de app-instelling een waarde heeft van of (hoofdletter ongevoelig). In dat geval wordt met de runtime een *logboekberichtfunctie 'Functions.TestJob' uitgeschakeld.*

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Wanneer u de waarden voor app-instelling wijzigt in de Azure-portal, wordt de WebJob opnieuw gestart om de nieuwe instelling op te halen.

Het kenmerk kan worden gedeclareerd op parameter-, methode- of klassenniveau. De instelnaam kan ook bindende expressies bevatten.

## <a name="timeout-attribute"></a>Kenmerk Time-out

Het [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) kenmerk zorgt ervoor dat een functie wordt geannuleerd als deze niet binnen een bepaalde tijd is voltooid. In het volgende voorbeeld wordt de functie één dag uitgevoerd zonder het kenmerk Timeout. Een time-out zorgt ervoor dat de functie na 15 seconden wordt geannuleerd.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

U het kenmerk Time-out toepassen op klasse- of methodeniveau `JobHostConfiguration.FunctionTimeout`en u een algemene time-out opgeven met behulp van. Time-outs op klasse- of methodeniveau overschrijven globale time-outs.

## <a name="singleton-attribute"></a>Kenmerk Singleton

Het [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) kenmerk zorgt ervoor dat slechts één exemplaar van een functie wordt uitgevoerd, zelfs wanneer er meerdere exemplaren van de hostweb-app zijn. Het doet dit met behulp van [gedistribueerde vergrendeling](#viewing-lease-blobs).

In dit voorbeeld wordt slechts `ProcessImage` één exemplaar van de functie op een bepaald moment uitgevoerd:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Sommige triggers hebben ingebouwde ondersteuning voor gelijktijdigheidsbeheer:

* **QueueTrigger**. Ingesteld `JobHostConfiguration.Queues.BatchSize` `1`op .
* **ServiceBusTrigger**. Ingesteld `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` `1`op .
* **FileTrigger**. Ingesteld `FileProcessor.MaxDegreeOfParallelism` `1`op .

U deze instellingen gebruiken om ervoor te zorgen dat uw functie als één ton op één exemplaar wordt uitgevoerd. Als u ervoor wilt zorgen dat slechts één exemplaar van de functie wordt uitgevoerd wanneer de web-app wordt geschaald naar meerdere instanties, past u een singleton-slot op listenerniveau toe op de functie (`[Singleton(Mode = SingletonMode.Listener)]`). Listenersloten worden aangeschaft wanneer de JobHost wordt gestart. Als drie geschaalde exemplaren allemaal tegelijkertijd worden gestart, wordt het slot slechts één keer toegewezen en wordt er slechts één listener gestart.

### <a name="scope-values"></a>Bereikwaarden

U een *bereikexpressie/-waarde* op een enkele ton opgeven. De expressie/waarde zorgt ervoor dat alle uitvoeringen van de functie op een specifiek bereik worden geserialiseerd. Het implementeren van meer gedetailleerde vergrendeling op deze manier kan zorgen voor een bepaald niveau van parallellisme voor uw functie, terwijl serialiseren andere aanroepen zoals bepaald door uw eisen. In de volgende code wordt bijvoorbeeld de bereikexpressie gekoppeld aan de `Region` waarde van het binnenkomende bericht. Wanneer de wachtrij drie berichten bevat in respectievelijk de regio's Oost, Oost en West, worden de berichten met regio Oost serieel uitgevoerd terwijl het bericht met regio West parallel wordt uitgevoerd met berichten in Oost.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

De standaardscope voor `SingletonScope.Function`een vergrendeling is, wat betekent dat het vergrendelingsbereik (het blobleasepad) is gekoppeld aan de volledig gekwalificeerde functienaam. Als u alle `SingletonScope.Host` functies wilt vergrendelen, geeft u een scope-id-naam op die hetzelfde is voor alle functies die u niet tegelijkertijd wilt uitvoeren. In het volgende voorbeeld wordt `AddItem` `RemoveItem` slechts één exemplaar van of wordt uitgevoerd tegelijk:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Leaseblobs weergeven

De WebJobs SDK gebruikt [Azure blob-leases](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) onder de hoezen om gedistribueerde vergrendeling te implementeren. De lease blobs gebruikt door Singleton `azure-webjobs-host` kan `AzureWebJobsStorage` worden gevonden in de container in de opslag account onder het pad "sloten". Het leaseblobpad voor het `ProcessImage` eerste voorbeeld dat `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`eerder wordt weergegeven, kan bijvoorbeeld . Alle paden zijn voorzien van de JobHost ID, in dit geval 061851c758f04938a44426aa9ab3869c0.

## <a name="async-functions"></a>Async-functies

Zie de [documentatie azure-functies](../azure-functions/functions-dotnet-class-library.md#async)voor informatie over het coderen van async-functies.

## <a name="cancellation-tokens"></a>Annuleringstokens

Zie de documentatie azure-functies over [annuleringstokens en het gracieuze afsluiten voor](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)informatie over het omgaan met annuleringstokens.

## <a name="multiple-instances"></a>Meerdere exemplaren

Als uw web-app op meerdere exemplaren wordt uitgevoerd, wordt een continue WebJob uitgevoerd op elke instantie, luisterend naar triggers en aanroepende functies. De verschillende triggerbindingen zijn ontworpen om werk efficiënt samen te delen tussen instanties, zodat u met uitschalen naar meer instanties meer belasting verwerken.

Hoewel sommige triggers kunnen leiden tot dubbele verwerking, voorkomen wachtrij- en blobopslagtriggers automatisch dat een functie een wachtrijbericht of blob meerdere tijd verwerkt. Zie [Ontwerpen voor identieke invoer](../azure-functions/functions-idempotent.md) in de azure-functiesdocumentatie voor meer informatie.

De timertrigger zorgt er automatisch voor dat slechts één exemplaar van de timer wordt uitgevoerd, zodat u niet meer dan één functie-instantie op een bepaalde geplande tijd laat draaien.

Als u ervoor wilt zorgen dat slechts één exemplaar van een functie wordt uitgevoerd, [`Singleton`](#singleton-attribute) zelfs als er meerdere exemplaren van de hostweb-app zijn, u het kenmerk gebruiken.

## <a name="filters"></a>Filters

Functiefilters (voorbeeld) bieden een manier om de WebJobs-uitvoeringspijplijn aan te passen met uw eigen logica. Filters zijn vergelijkbaar met [ASP.NET Core-filters.](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters) U ze implementeren als declaratieve kenmerken die worden toegepast op uw functies of klassen. Zie [Functiefilters voor](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)meer informatie .

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Wij raden het logging framework aan dat voor ASP.NET is ontwikkeld. In het artikel [Aan de slag](webjobs-sdk-get-started.md) u zien hoe u het gebruiken. 

### <a name="log-filtering"></a>Logboekfiltering

Elk logboek dat `ILogger` door een `Category` `Level`instantie is gemaakt, heeft een bijbehorende en . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)is een opsomming, en de gehele code geeft relatief belang aan:

|Logniveau    |Code|
|------------|---|
|Tracering       | 0 |
|Fouten opsporen       | 1 |
|Informatie | 2 |
|Waarschuwing     | 3 |
|Fout       | 4 |
|Kritiek    | 5 |
|Geen        | 6 |

U elke categorie onafhankelijk [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)filteren op een bepaalde. U wilt bijvoorbeeld alle logboeken voor blobtriggerverwerking `Error` zien, maar alleen en hoger voor al het andere.

#### <a name="version-3x"></a>Versie 3. *x*

Versie 3. *x* van de SDK is afhankelijk van de filtering die in .NET Core is ingebouwd. Met `LogCategories` de klasse u categorieën definiëren voor specifieke functies, triggers of gebruikers. Het definieert ook filters voor `Startup` `Results`specifieke hoststatussen, zoals en . Hierdoor u de logging-uitvoer verfijnen. Als er geen overeenkomst wordt gevonden binnen de gedefinieerde `Default` categorieën, valt het filter terug naar de waarde wanneer het bericht wordt gefilterd.

`LogCategories`vereist de volgende gebruiksinstructie:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

In het volgende voorbeeld wordt een filter gemaakt waarmee `Warning` standaard alle logboeken op niveau worden gefilterd. De `Function` `results` categorieën en `Host.Results` categorieën (gelijk aan in versie 2.* x*) worden `Error` gefilterd op het niveau. Het filter vergelijkt de huidige categorie `LogCategories` met alle geregistreerde niveaus in de instantie en kiest de langste overeenkomst. Dit betekent `Debug` dat het `Host.Triggers` `Host.Triggers.Queue` niveau `Host.Triggers.Blob`geregistreerd voor wedstrijden of . Hiermee u bredere categorieën beheren zonder dat u ze allemaal hoeft toe te voegen.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versie 2. *x*

In versie 2. *x* van de SDK, `LogCategoryFilter` gebruikt u de klasse om het filteren te regelen. De `LogCategoryFilter` eigenschap `Default` heeft een eigenschap `Information`met een initiële `Information` `Warning`waarde `Error`van `Critical` , wat betekent dat alle `Debug` `Trace` berichten op de , , of niveaus worden geregistreerd, maar alle berichten op de of niveaus worden weggefilterd.

Zoals `LogCategories` in versie 3. *x*, `CategoryLevels` u met de eigenschap logboekniveaus opgeven voor specifieke categorieën, zodat u de logboekuitvoer verfijnen. Als er geen overeenkomst `CategoryLevels` wordt gevonden in het `Default` woordenboek, valt het filter terug naar de waarde wanneer wordt besloten of het bericht moet worden gefilterd.

In het volgende voorbeeld wordt een filter gemaakt `Warning` dat standaard alle logboeken op niveau filtert. De `Function` `Host.Results` categorieën en categorieën `Error` worden gefilterd op het niveau. Hiermee `LogCategoryFilter` wordt de huidige `CategoryLevels` categorie vergeleken met alle geregistreerde en kiest u de langste overeenkomst. Dus `Debug` het niveau `Host.Triggers` geregistreerd `Host.Triggers.Queue` `Host.Triggers.Blob`voor zal overeenkomen of . Hiermee u bredere categorieën beheren zonder dat u ze allemaal hoeft toe te voegen.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Aangepaste telemetrie voor toepassingsinzichten

Het proces voor het implementeren van aangepaste telemetrie voor [Application Insights](../azure-monitor/app/app-insights-overview.md) is afhankelijk van de SDK-versie. Zie [Logboekregistratie Toepassingsinzichten toevoegen](webjobs-sdk-get-started.md#add-application-insights-logging)voor meer informatie over het configureren van toepassingsinzichten.

#### <a name="version-3x"></a>Versie 3. *x*

Want versie 3. *x* van de WebJobs SDK is afhankelijk van de .NET Core generieke host, een aangepaste telemetriefabriek wordt niet meer geleverd. Maar u aangepaste telemetrie toevoegen aan de pijplijn met behulp van afhankelijkheidsinjectie. De voorbeelden in deze sectie `using` vereisen de volgende instructies:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Met de volgende [`ITelemetryInitializer`] aangepaste implementatie [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) kunt u [`TelemetryConfiguration`]uw eigen implementatie toevoegen aan de standaardinstelling .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Roep [`ConfigureServices`] de bouwer in [`ITelemetryInitializer`] om uw aangepaste toe te voegen aan de pijplijn.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Wanneer [`TelemetryConfiguration`] de is gebouwd, alle [`ITelemetryInitializer`] geregistreerde soorten zijn opgenomen. Zie [Application Insights API voor aangepaste gebeurtenissen en statistieken voor](../azure-monitor/app/api-custom-events-metrics.md)meer informatie.

In versie 3. *x*, hoeft u niet [`TelemetryClient`] langer te spoelen wanneer de host stopt. Het .NET Core afhankelijkheidsinjectiesysteem beschikt automatisch `ApplicationInsightsLoggerProvider`over de [`TelemetryClient`]geregistreerde , die de .

#### <a name="version-2x"></a>Versie 2. *x*

In versie 2. *x*, [`TelemetryClient`] het intern gemaakt door de Application Insights-provider voor de WebJobs SDK gebruikt [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Wanneer het eindpunt Application Insights niet beschikbaar is of binnenkomende aanvragen inderotting, slaat dit kanaal [aanvragen op in het bestandssysteem van de web-app en wordt deze later opnieuw ingediend.](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)

De [`TelemetryClient`] is gemaakt door een `ITelemetryClientFactory`klasse die implementeert . Standaard is dit [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)de .

Als u een deel van de Pipeline Application Insights `ITelemetryClientFactory`wilt wijzigen, u uw [`TelemetryClient`]eigen pijplijn leveren en de host gebruikt uw klasse om een. Deze code overschrijft `DefaultTelemetryClientFactory` bijvoorbeeld om een `ServerTelemetryChannel`eigenschap van :

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Het `SamplingPercentageEstimatorSettings` object configureert [adaptieve bemonstering](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Dit betekent dat Applications Insights in bepaalde scenario's met een hoog volume een geselecteerde subset van telemetriegegevens naar de server stuurt.

Nadat u de telemetriefabriek hebt gemaakt, geeft u deze door aan de logboekregistratieprovider Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen

In dit artikel worden codefragmenten verstrekt die laten zien hoe u veelvoorkomende scenario's voor het werken met de WebJobs SDK verwerken. Zie [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)voor volledige voorbeelden.

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
['TelemetrieClient']: /dotnet/api/microsoft.applicationinsights.telemetryclient
['Services configureren']: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetrieConfiguratie']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostconfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
