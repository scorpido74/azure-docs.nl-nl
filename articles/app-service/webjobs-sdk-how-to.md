---
title: De webjobs SDK gebruiken
description: Meer informatie over het schrijven van code voor de webjobs SDK. Maak gebeurtenis gerichte achtergrond verwerkings taken die toegang hebben tot gegevens in Azure en services van derden.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5a8d5f96449cfecd4628c38fa2788a1e06e96b07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758890"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>De Azure WebJobs SDK gebruiken voor gebeurtenisgestuurde verwerking op de achtergrond

Dit artikel bevat richt lijnen voor het werken met de Azure WebJobs SDK. Zie aan [de slag met de Azure WEBJOBS SDK voor op gebeurtenissen gebaseerde achtergrond verwerking](webjobs-sdk-get-started.md)om aan de slag te gaan met webjobs. 

## <a name="webjobs-sdk-versions"></a>SDK-versies van webjobs

Dit zijn de belangrijkste verschillen tussen versie 3. *x* en versie 2. *x* van de webjobs SDK:

* Versie 3. *x* voegt ondersteuning toe voor .net core.
* In versie 3. *x*, moet u de opslag bindings uitbreiding expliciet installeren die is vereist voor de webjobs SDK. In versie 2. *x*, zijn de opslag bindingen opgenomen in de SDK.
* Visual Studio-hulp programma voor .NET core (3.* x*)-projecten wijken af van hulp middelen voor .NET Framework (2.* x*)-projecten. Zie [Webjobs ontwikkelen en implementeren met behulp van Visual Studio-Azure app service](webjobs-dotnet-deploy-vs.md)voor meer informatie.

Indien mogelijk zijn er voor beelden beschikbaar voor versie 3. *x* en versie 2. *x*.

> [!NOTE]
> [Azure functions](../azure-functions/functions-overview.md) is gebaseerd op de webjobs SDK en dit artikel bevat koppelingen naar Azure functions documentatie voor sommige onderwerpen. Houd rekening met de volgende verschillen tussen functies en de webjobs SDK:
> * Azure Functions versie 2. *x* komt overeen met webjobs SDK versie 3. *x*en Azure functions 1. *x* komt overeen met webjobs SDK 2. *x*. Bron code opslagplaatsen gebruiken de webjobs SDK-nummering.
> * Voorbeeld code voor Azure Functions C#-klassen bibliotheken is vergelijkbaar met de `FunctionName` SDK-code van webjobs, behalve dat u geen kenmerk hebt in een webjobs SDK-project.
> * Sommige typen bindingen worden alleen ondersteund in functies, zoals HTTP (webhooks) en Event Grid (dat is gebaseerd op HTTP).
>
> Zie [compare the Webjobs SDK and Azure functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)voor meer informatie.

## <a name="webjobs-host"></a>Webjobs-host

De host is een runtime container voor functions.  Het luistert naar triggers en aanroepen-functies. In versie 3. *x*, de host is een implementatie van `IHost`. In versie 2. *x*gebruikt u het `JobHost` -object. U maakt een exemplaar van een host in uw code en schrijft code om het gedrag aan te passen.

Dit is een belang rijk verschil tussen het rechtstreeks gebruiken van de webjobs SDK en het indirect via Azure Functions. In Azure Functions beheert de service de host en u kunt de host niet aanpassen door code te schrijven. Met Azure Functions kunt u het gedrag van de host aanpassen via instellingen in het bestand host. json. Deze instellingen zijn teken reeksen, geen code en Hiermee worden de soorten aanpassingen beperkt die u kunt uitvoeren.

### <a name="host-connection-strings"></a>Verbindingsteken reeksen voor de host

De webjobs SDK zoekt naar Azure Storage en Azure Service Bus verbindings reeksen in het bestand local. settings. json wanneer u lokaal uitvoert, of in de omgeving van de Webtaak wanneer u uitvoert in Azure. Een opslag connection string instelling met de naam `AzureWebJobsStorage` is standaard vereist.  

Versie 2. met *x* van de SDK kunt u uw eigen namen voor deze verbindings reeksen gebruiken of elders opslaan. U kunt namen in code instellen met behulp van de [`JobHostConfiguration`], zoals hier wordt weer gegeven:

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

Omdat versie 3. *x* de standaard configuratie-api's voor .net Core gebruikt, is er geen API om Connection String namen te wijzigen.

### <a name="host-development-settings"></a>Instellingen voor het ontwikkelen van hosts

U kunt de host uitvoeren in de ontwikkel modus om lokale ontwikkeling efficiënter te maken. Hier volgen enkele van de instellingen die worden gewijzigd wanneer u uitvoert in de ontwikkelings modus:

| Eigenschap | Ontwikkelings instelling |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`om de logboek uitvoer te maximaliseren. |
| `Queues.MaxPollingInterval`  | Een lage waarde om ervoor te zorgen dat wachtrij methoden direct worden geactiveerd.  |
| `Singleton.ListenerLockPeriod` | 15 seconden om te helpen bij snelle herhalings ontwikkeling. |

Het proces voor het inschakelen van de ontwikkelings modus is afhankelijk van de SDK-versie. 

#### <a name="version-3x"></a>Versie 3. *x*

Versie 3. *x* maakt gebruik van de standaard ASP.net core-api's. Roep de [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) -methode aan [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) voor het exemplaar. Geef een teken reeks `development`met de naam, zoals in dit voor beeld:

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

De `JobHostConfiguration` klasse heeft een `UseDevelopmentSettings` methode waarmee de ontwikkelings modus wordt ingeschakeld.  In het volgende voor beeld ziet u hoe u ontwikkelings instellingen gebruikt. Als u `config.IsDevelopment` de `true` retour nering wilt uitvoeren wanneer het lokaal wordt uitgevoerd, stelt `AzureWebJobsEnv` u een lokale `Development`omgevings variabele met de naam met de waarde in.

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

In versie 3. *x*, de verbindings limiet wordt standaard ingesteld op oneindige verbindingen. Als u deze limiet om een of andere reden moet wijzigen, kunt u de [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) eigenschap van de [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) klasse gebruiken.

In versie 2. *x*, bepaalt u het aantal gelijktijdige verbindingen met een host met behulp van de API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . In 2. *x*moet u deze waarde verhogen van de standaard instelling van 2 voordat u de host voor webjobs start.

Alle uitgaande HTTP-aanvragen die u vanuit een functie hebt gemaakt `HttpClient` met behulp van flow through `ServicePointManager`. Nadat u de waarde in hebt ingesteld `DefaultConnectionLimit`in `ServicePointManager` , wordt het in de wachtrij plaatsen van aanvragen gestart voordat ze worden verzonden. Stel dat `DefaultConnectionLimit` je is ingesteld op 2 en dat je code 1.000 HTTP-aanvragen maakt. In eerste instantie worden slechts twee aanvragen toegestaan via het besturings systeem. De andere 998 worden in de wachtrij geplaatst totdat er ruimte is. Dit betekent dat `HttpClient` er mogelijk een time-out optreedt omdat het lijkt alsof de aanvraag is gedaan, maar de aanvraag werd nooit verzonden door het besturings systeem naar de doel server. Het is dus mogelijk dat u het gedrag ziet dat niet duidelijk lijkt: `HttpClient` uw lokale bewerking neemt 10 seconden in beslag om een aanvraag te volt ooien, maar uw service retourneert elke aanvraag in 200 MS. 

De standaard waarde voor ASP.NET-toepassingen `Int32.MaxValue`is en dat is waarschijnlijk goed geschikt voor webtaken die worden uitgevoerd in een basis-of hoger app service abonnement. Webjobs hebben doorgaans de instelling altijd aan nodig en alleen ondersteund door basis-en snellere App Service plannen.

Als uw Webtaak wordt uitgevoerd in een gratis of gedeeld App Service-abonnement, wordt uw toepassing beperkt door de App Service sandbox, die momenteel een [verbindings limiet van 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)heeft. Met een niet-afhankelijke verbindings limiet `ServicePointManager`in, is het waarschijnlijker dat de drempel waarde voor de sandbox-verbinding wordt bereikt en de site wordt afgesloten. In dat geval kan het `DefaultConnectionLimit` instellen op iets lager, zoals 50 of 100, ervoor zorgen dat dit niet gebeurt en toch voldoende door Voer toestaan.

De instelling moet worden geconfigureerd voordat er HTTP-aanvragen worden gedaan. Daarom mag de webjobs-host de instelling niet automatisch aanpassen. Er kunnen HTTP-aanvragen worden uitgevoerd voordat de host wordt gestart. Dit kan leiden tot onverwacht gedrag. De beste manier is om de waarde direct in uw methode `Main` in te stellen voordat `JobHost`u de initialisatie maakt, zoals hier wordt weer gegeven:

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

Functies moeten open bare methoden zijn en moeten één trigger kenmerk of het [`NoAutomaticTrigger`](#manual-triggers) kenmerk hebben.

### <a name="automatic-triggers"></a>Automatische triggers

Automatische triggers roepen een functie aan in reactie op een gebeurtenis. Bekijk dit voor beeld van een functie die wordt geactiveerd door een bericht dat is toegevoegd aan Azure Queue-opslag. Er wordt gereageerd door een BLOB te lezen uit Azure Blob-opslag:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Het `QueueTrigger` kenmerk vertelt de runtime om de functie aan te roepen wanneer er een wachtrij bericht `myqueue-items` in de wachtrij wordt weer gegeven. Het `Blob` kenmerk geeft de runtime de tijd om het wachtrij bericht te gebruiken voor het lezen van een BLOB in de container *sample-werk items* . De naam van het BLOB-item in `samples-workitems` de container wordt rechtstreeks opgehaald uit de wachtrij trigger als een bindings`{queueTrigger}`expressie ().

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Hand matige triggers

Als u een functie hand matig wilt activeren `NoAutomaticTrigger` , gebruikt u het kenmerk, zoals hier wordt weer gegeven:

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

Het proces voor het hand matig activeren van de functie is afhankelijk van de SDK-versie.

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

## <a name="input-and-output-bindings"></a>Invoer-en uitvoer bindingen

Invoer bindingen bieden een declaratieve manier om gegevens van Azure of services van derden beschikbaar te maken voor uw code. Uitvoer bindingen bieden een manier om gegevens bij te werken. In het artikel [aan](webjobs-sdk-get-started.md) de slag ziet u een voor beeld van elk.

U kunt de retour waarde van een methode voor een uitvoer binding gebruiken door het kenmerk toe te passen op de retour waarde van de methode. Zie het voor beeld in [het gebruik van de functie retour waarde van Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindings typen

Het proces voor het installeren en beheren van bindings typen is afhankelijk van het feit of u versie 3 gebruikt. *x* of versie 2. *x* van de SDK. U kunt het pakket dat moet worden geïnstalleerd voor een bepaald bindings type vinden in de sectie ' pakketten ' van het Azure Functions [referentie artikel](#binding-reference-information)van het bindings type. Een uitzonde ring hierop is de trigger bestanden en binding (voor het lokale bestands systeem) die niet wordt ondersteund door Azure Functions.

#### <a name="version-3x"></a>Versie 3. *x*

In versie 3. *x*, de opslag bindingen zijn opgenomen in het `Microsoft.Azure.WebJobs.Extensions.Storage` pakket. Roep de `AddAzureStorage` uitbreidings methode aan in `ConfigureWebJobs` de-methode, zoals hier wordt weer gegeven:

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

Als u andere typen triggers en bindingen wilt gebruiken, installeert u het NuGet-pakket dat `Add<binding>` deze bevat en roept u de extensie methode aan die in de uitbrei ding is geïmplementeerd. Als u bijvoorbeeld een Azure Cosmos DB binding wilt gebruiken, moet u deze als `Microsoft.Azure.WebJobs.Extensions.CosmosDB` volgt installeren `AddCosmosDB`en aanroepen:

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

Als u de timer trigger of de binding van de bestanden wilt gebruiken, die deel uitmaken van `AddTimers` de `AddFiles` kern Services, roept u respectievelijk de methoden of extensie aan.

#### <a name="version-2x"></a>Versie 2. *x*

Deze trigger-en bindings typen zijn opgenomen in versie 2. *x* van het `Microsoft.Azure.WebJobs` pakket:

* Blob Storage
* Queue Storage
* Table Storage

Als u andere typen triggers en bindingen wilt gebruiken, installeert u het NuGet-pakket dat `Use<binding>` deze bevat en `JobHostConfiguration` roept u een methode aan voor het object. Als u bijvoorbeeld een timer trigger wilt gebruiken, installeert `Microsoft.Azure.WebJobs.Extensions` en aanroept `UseTimers` u de `Main` -methode, zoals hier wordt weer gegeven:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Voor het gebruik van de bestanden binding `Microsoft.Azure.WebJobs.Extensions` , installeren `UseFiles`en aanroepen.

### <a name="executioncontext"></a>ExecutionContext

Met webjobs kunt u verbinding maken [`ExecutionContext`]met een. Met deze binding krijgt u toegang tot de [`ExecutionContext`] as a-para meter in uw functie handtekening. De volgende code gebruikt bijvoorbeeld het context object om toegang te krijgen tot de aanroep-ID, die u kunt gebruiken voor het correleren van alle logboeken die zijn geproduceerd door een bepaalde functie aanroep.  

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

Het proces voor het verbinden met [`ExecutionContext`] de is afhankelijk van uw SDK-versie.

#### <a name="version-3x"></a>Versie 3. *x*

Roep de `AddExecutionContextBinding` uitbreidings methode aan in `ConfigureWebJobs` de-methode, zoals hier wordt weer gegeven:

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

Het `Microsoft.Azure.WebJobs.Extensions` eerder genoemde pakket biedt ook een speciaal bindings type dat u kunt registreren door de `UseCore` methode aan te roepen. Met deze binding kunt u een [`ExecutionContext`] para meter definiëren in uw functie handtekening, die als volgt wordt ingeschakeld:

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

## <a name="binding-configuration"></a>Bindings configuratie

U kunt het gedrag van sommige triggers en bindingen configureren. Het proces voor het configureren van deze onderdelen is afhankelijk van de SDK-versie.

* **Versie 3. *x*:** Stel de configuratie in `Add<Binding>` wanneer de methode wordt `ConfigureWebJobs`aangeroepen in.
* **Versie 2. *x*:** Stel de configuratie in door eigenschappen in te stellen in een configuratie object dat `JobHost`u doorgeeft aan.

Deze binding-specifieke instellingen zijn gelijk aan instellingen in het [bestand host. json-project](../azure-functions/functions-host-json.md) in azure functions.

U kunt de volgende bindingen configureren:

* [Trigger voor Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs trigger](#event-hubs-trigger-configuration-version-3x)
* [Queue Storage-trigger](#queue-storage-trigger-configuration)
* [SendGrid-binding](#sendgrid-binding-configuration-version-3x)
* [Service Bus trigger](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuratie van trigger voor Azure CosmosDB (versie 3.* x*)

In dit voor beeld ziet u hoe u de Azure Cosmos DB trigger configureert:

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

Zie het artikel over [Azure CosmosDB-binding](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) voor meer informatie.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuratie van Event Hubs trigger (versie 3.* x*)

In dit voor beeld ziet u hoe u de Event Hubs trigger configureert:

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

Zie het artikel over [Event hubs binding](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) voor meer informatie.

### <a name="queue-storage-trigger-configuration"></a>Configuratie van trigger voor opslag van wachtrij

In deze voor beelden ziet u hoe u de opslag trigger voor de wachtrij configureert:

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

Zie voor meer informatie het artikel [binding van wachtrij opslag](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) .

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

Zie de [referentie host. json v1. x](../azure-functions/functions-host-json-v1.md#queues)voor meer informatie.

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuratie van SendGrid-binding (versie 3.* x*)

In dit voor beeld ziet u hoe u de SendGrid-uitvoer binding kunt configureren:

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

Zie het [SendGrid-bindings](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) artikel voor meer informatie.

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuratie van Service Bus trigger (versie 3.* x*)

In dit voor beeld ziet u hoe u de Service Bus trigger configureert:

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

Zie het artikel over [Service Bus binding](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) voor meer informatie.

### <a name="configuration-for-other-bindings"></a>Configuratie voor andere bindingen

Bij sommige typen triggers en bindingen worden hun eigen aangepaste configuratie typen gedefinieerd. Met de bestands trigger kunt u bijvoorbeeld het hoofdpad opgeven dat u wilt bewaken, zoals in de volgende voor beelden:

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

In kenmerk-constructor-para meters kunt u expressies gebruiken die worden omgezet in waarden van verschillende bronnen. In de volgende code maakt het pad voor het `BlobTrigger` kenmerk bijvoorbeeld een expressie met de naam. `filename` Wanneer deze wordt gebruikt voor de uitvoer `filename` binding, wordt omgezet naar de naam van de trigger-blob.

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

Zie [expressies en patronen binden](../azure-functions/functions-bindings-expressions-patterns.md) in de Azure functions documentatie voor meer informatie over het binden van expressies.

### <a name="custom-binding-expressions"></a>Aangepaste bindings expressies

Soms wilt u een wachtrij naam, een blobnaam of container of een tabel naam opgeven in de code in plaats van deze op te geven. U kunt bijvoorbeeld de naam van de wachtrij voor het `QueueTrigger` kenmerk opgeven in een configuratie bestand of omgevings variabele.

U kunt dit doen door een `NameResolver` object door te geven aan `JobHostConfiguration` het object. U neemt tijdelijke aanduidingen op in de para meters voor de trigger- `NameResolver` of bindings kenmerken, en uw code levert de werkelijke waarden die moeten worden gebruikt in plaats van deze tijdelijke aanduidingen. U identificeert tijdelijke aanduidingen door deze te omheen met een percentage (%) tekens, zoals hier wordt weer gegeven:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Met deze code kunt u een wachtrij gebruiken `logqueuetest` met de naam in de test omgeving `logqueueprod` en één met de naam in productie. In plaats van een in code vastgelegde wachtrij naam, geeft u de naam op van een vermelding `appSettings` in de verzameling.

Er is een standaard `NameResolver` waarde die van kracht is als u geen aangepaste versie opgeeft. De standaard waarden worden opgehaald uit de app-instellingen of omgevings variabelen.

Uw `NameResolver` klasse haalt de naam van de `appSettings`wachtrij op, zoals hier wordt weer gegeven:

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

U kunt de resolver configureren met behulp van afhankelijkheids injectie. Deze voor beelden vereisen de `using` volgende instructie:

```cs
using Microsoft.Extensions.DependencyInjection;
```

U voegt de resolver toe door de [`ConfigureServices`] uitbreidings methode aan [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)te roepen, zoals in dit voor beeld:

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

Geef uw `NameResolver` klasse door aan het `JobHost` object, zoals hier wordt weer gegeven:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementeert `INameResolver` om waarden op te halen uit de app-instellingen, zoals wordt weer gegeven in het voor beeld. Wanneer u de webjobs SDK rechtstreeks gebruikt, kunt u een aangepaste implementatie schrijven die waarden voor het vervangen van tijdelijke aanduidingen uit de gewenste bron ophaalt.

## <a name="binding-at-runtime"></a>Binding tijdens runtime

Als u een aantal werk in uw functie wilt uitvoeren voordat u een bindings kenmerk gebruikt `Queue`, `Blob`zoals, `Table`of, kunt u de `IBinder` interface gebruiken.

In het volgende voor beeld wordt een invoer wachtrij bericht gebruikt en wordt er een nieuw bericht gemaakt met dezelfde inhoud in een uitvoer wachtrij. De naam van de uitvoer wachtrij wordt ingesteld op code in de hoofd tekst van de functie.

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

Zie [binding tijdens runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) in de Azure functions-documentatie voor meer informatie.

## <a name="binding-reference-information"></a>Referentie-informatie voor binding

De Azure Functions documentatie bevat Naslag informatie over elk bindings type. U vindt de volgende informatie in elk referentie artikel over de binding. (Dit voor beeld is gebaseerd op de opslag wachtrij.)

* [Pakketten](../azure-functions/functions-bindings-storage-queue.md). Het pakket dat u moet installeren om ondersteuning te bieden voor de binding in een webjobs SDK-project.
* [Voor beelden](../azure-functions/functions-bindings-storage-queue-trigger.md). Code voorbeelden. Het voor beeld van de C#-klassen bibliotheek is van toepassing op de webjobs SDK. Laat het `FunctionName` kenmerk weg.
* [Kenmerken](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). De kenmerken die moeten worden gebruikt voor het bindings type.
* [Configuratie](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Uitleg van de kenmerk eigenschappen en constructor-para meters.
* [Gebruik](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). De typen waarmee u verbinding kunt maken en informatie over de werking van de binding. Bijvoorbeeld: polling-algoritme, verwerking van de verontreinigde wachtrij.
  
Zie ' ondersteunde bindingen ' in het artikel [Triggers en bindingen](../azure-functions/functions-triggers-bindings.md#supported-bindings) voor Azure functions voor een lijst met artikelen met een bindings verwijzing. In deze lijst worden de HTTP-, webhooks-en Event Grid-bindingen alleen ondersteund door Azure Functions, niet door de webjobs SDK.

## <a name="disable-attribute"></a>Kenmerk uitschakelen 

Met [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) het kenmerk kunt u bepalen of een functie kan worden geactiveerd. 

In het volgende voor beeld wordt de functie niet `Disable_TestJob` uitgevoerd als de app `1` - `True` instelling een waarde heeft van of (niet hoofdletter gevoelig). In dat geval maakt de runtime een functie van het logboek bericht ' functions *. TestJob ' is uitgeschakeld*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Wanneer u de waarden van de app-instellingen in de Azure Portal wijzigt, start de Webtaak opnieuw op om de nieuwe instelling op te halen.

Het kenmerk kan worden gedeclareerd op de para meter, methode of class-niveau. De naam van de instelling kan ook binding expressies bevatten.

## <a name="timeout-attribute"></a>Time-outkenmerk

Het [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) kenmerk zorgt ervoor dat een functie wordt geannuleerd als deze niet binnen een opgegeven tijd wordt voltooid. In het volgende voor beeld wordt de functie uitgevoerd voor één dag zonder het kenmerk timeout. Time-out zorgt ervoor dat de functie na 15 seconden wordt geannuleerd.

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

U kunt het kenmerk timeout op het niveau van klasse of methode Toep assen en u kunt een globale time-out `JobHostConfiguration.FunctionTimeout`opgeven met behulp van. Time-outs van het niveau van klasse-of methode-niveau overschrijven globale time-outs.

## <a name="singleton-attribute"></a>Singleton kenmerk

Het [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) kenmerk zorgt ervoor dat er slechts één exemplaar van een functie wordt uitgevoerd, zelfs wanneer er meerdere exemplaren van de web-app voor de host zijn. Dit doet u met behulp van [gedistribueerd vergren delen](#viewing-lease-blobs).

In dit voor beeld wordt slechts één exemplaar van de `ProcessImage` functie op een bepaald moment uitgevoerd:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode. listener

Sommige triggers hebben ingebouwde ondersteuning voor gelijktijdigheids beheer:

* **Queue trigger**. Ingesteld `JobHostConfiguration.Queues.BatchSize` op `1`.
* **ServiceBusTrigger**. Ingesteld `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` op `1`.
* **FileTrigger**. Ingesteld `FileProcessor.MaxDegreeOfParallelism` op `1`.

U kunt deze instellingen gebruiken om ervoor te zorgen dat de functie wordt uitgevoerd als een singleton op één exemplaar. Als u er zeker van wilt zijn dat er slechts één exemplaar van de functie wordt uitgevoerd wanneer de web-app naar meerdere exemplaren wordt geschaald, moet u een singleton-`[Singleton(Mode = SingletonMode.Listener)]`vergren deling op listener-niveau Toep assen op de functie (). Luister vergrendelingen worden verkregen wanneer de JobHost wordt gestart. Als drie uitgebreide exemplaren op hetzelfde moment allemaal worden gestart, wordt de vergren deling door slechts één van de instanties opgehaald en wordt slechts één listener gestart.

### <a name="scope-values"></a>Bereik waarden

U kunt een *bereik expressie/-waarde* opgeven voor een singleton. De expressie/waarde zorgt ervoor dat alle uitvoeringen van de functie op een specifiek bereik worden geserialiseerd. Als u op deze manier meer granulaire vergren delingen implementeert, kunt u een zekere mate van parallellisme voor uw functie toestaan tijdens het serialiseren van andere aanroepen zoals bepaald door uw vereisten. In de volgende code wordt de bereik expressie bijvoorbeeld gekoppeld aan de `Region` waarde van het inkomende bericht. Wanneer de wachtrij drie berichten bevat in de regio's Oost, Oost en West, worden de berichten met regio-Oosten serieel uitgevoerd terwijl het bericht met regio West parallel wordt uitgevoerd met die in Oost.

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

### <a name="singletonscopehost"></a>SingletonScope. host

Het standaard bereik voor een vergren `SingletonScope.Function`deling is, wat betekent dat de vergren deling (het pad van de BLOB-lease) is gekoppeld aan de volledig gekwalificeerde functie naam. Als u de functies wilt vergren delen, geeft `SingletonScope.Host` u een scope-id op die gelijk is aan alle functies die u niet tegelijkertijd wilt uitvoeren, en gebruikt u deze naam. In het volgende voor beeld wordt slechts één exemplaar `AddItem` van `RemoveItem` of uitgevoerd op een tijdstip:

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

### <a name="viewing-lease-blobs"></a>Lease-blobs weer geven

De webjobs SDK maakt gebruik van [Azure Blob-leases](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) onder de kaften voor het implementeren van gedistribueerde vergren deling. De lease-blobs die worden gebruikt door Singleton, vindt `azure-webjobs-host` u in de `AzureWebJobsStorage` container in het opslag account onder het pad "sloten". Het is `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`bijvoorbeeld mogelijk dat het pad naar de lease `ProcessImage` -BLOB voor het eerste voor beeld dat eerder wordt weer gegeven. Alle paden bevatten de JobHost-ID, in dit geval 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Async-functies

Zie de [Azure functions-documentatie](../azure-functions/functions-dotnet-class-library.md#async)voor meer informatie over het coderen van async-functies.

## <a name="cancellation-tokens"></a>Annulerings tokens

Voor informatie over het afhandelen van insluitings tokens raadpleegt u de Azure Functions-documentatie over het [annulerings token en](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)het probleemloos afsluiten.

## <a name="multiple-instances"></a>Meerdere exemplaren

Als uw web-app wordt uitgevoerd op meerdere exemplaren, wordt een doorlopende webtoepassing uitgevoerd op elke instantie, die luistert naar triggers en aanroepende functies. De verschillende trigger bindingen zijn ontworpen om efficiënt werk samen te delen tussen verschillende instanties, zodat u met meer instanties kunt uitschalen om meer belasting te verwerken.

Hoewel sommige triggers kunnen leiden tot dubbele verwerking, wachtrij-en Blob Storage-triggers, voor komen dat een functie meer dan één keer een wachtrij bericht of BLOB verwerkt. Zie voor meer informatie [ontwerpen voor identieke invoer](../azure-functions/functions-idempotent.md) in de Azure functions documentatie.

De timer trigger zorgt er automatisch voor dat er slechts één exemplaar van de timer wordt uitgevoerd, zodat er niet meer dan één functie-exemplaar wordt uitgevoerd op een bepaald gepland tijdstip.

Als u er zeker van wilt zijn dat er slechts één exemplaar van een functie wordt uitgevoerd, zelfs wanneer er meerdere exemplaren van de Web-App van [`Singleton`](#singleton-attribute) de host zijn, kunt u het kenmerk gebruiken.

## <a name="filters"></a>Filters

Functie filters (preview) bieden een manier om de uitvoering van de webjobs-pijp lijn met uw eigen logica aan te passen. Filters zijn vergelijkbaar met [ASP.net core filters](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). U kunt deze implementeren als declaratieve kenmerken die op uw functies of klassen worden toegepast. Zie [functie filters](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)voor meer informatie.

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

We raden aan het Framework voor logboek registratie dat is ontwikkeld voor ASP.NET uit te voeren. In het artikel aan de [slag](webjobs-sdk-get-started.md) ziet u hoe u het kunt gebruiken. 

### <a name="log-filtering"></a>Logboek filtering

Elk logboek dat door een `ILogger` exemplaar wordt gemaakt, `Category` heeft `Level`een bijbehorend en. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)is een opsomming, en de gehele code geeft het relatieve belang aan:

|Logniveau    |Code|
|------------|---|
|Tracering       | 0 |
|Fouten opsporen       | 1 |
|Informatie | 2 |
|Waarschuwing     | 3 |
|Fout       | 4 |
|Kritiek    | 5 |
|Geen        | 6 |

U kunt elke categorie afzonderlijk filteren op een bepaald [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)type. Het is bijvoorbeeld mogelijk dat u alle logboeken wilt weer geven voor de verwerking van `Error` BLOB-triggers, maar alleen en hoger voor alle andere.

#### <a name="version-3x"></a>Versie 3. *x*

Versie 3. *x* van de SDK is afhankelijk van de filters die zijn ingebouwd in .net core. Met `LogCategories` de-klasse kunt u categorieën definiëren voor specifieke functies, triggers of gebruikers. Het definieert ook filters voor specifieke hostnamen, zoals `Startup` en `Results`. Zo kunt u de uitvoer van logboek registratie nauw keurig afstemmen. Als er geen overeenkomst wordt gevonden binnen de gedefinieerde categorieën, wordt het filter terugvallen `Default` op de waarde wanneer u beslist of het bericht moet worden gefilterd.

`LogCategories`vereist de volgende using-instructie:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

In het volgende voor beeld wordt een filter gebouwd dat standaard alle logboeken op het `Warning` niveau filtert. De `Function` categorieën `results` en (equivalent met `Host.Results` in versie 2).* x*) worden gefilterd `Error` op het niveau. Het filter vergelijkt de huidige categorie met alle geregistreerde niveaus in `LogCategories` het exemplaar en kiest de langste overeenkomst. Dit betekent dat het `Debug` niveau is geregistreerd `Host.Triggers` voor `Host.Triggers.Queue` overeenkomsten `Host.Triggers.Blob`of. Zo kunt u grotere categorieën beheren zonder dat u deze hoeft toe te voegen.

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

In versie 2. *x* van de SDK gebruikt u de klasse `LogCategoryFilter` om de filtering te beheren. De `LogCategoryFilter` heeft een `Default` eigenschap met de aanvankelijke waarde `Information`van, wat inhoudt dat alle `Information`berichten `Warning`in `Error`de, `Critical` ,, of niveaus worden geregistreerd, maar dat `Debug` er `Trace` berichten worden gefilterd.

Net als `LogCategories` bij versie 3. *x*kunt u `CategoryLevels` met de eigenschap logboek niveaus opgeven voor specifieke categorieën, zodat u de uitvoer van logboek registratie kunt aanpassen. Als er geen overeenkomst wordt gevonden in `CategoryLevels` de woorden lijst, wordt het filter terugvallen op de `Default` waarde wanneer u beslist of het bericht moet worden gefilterd.

In het volgende voor beeld wordt een filter gemaakt dat standaard alle logboeken op `Warning` het niveau filtert. De `Function` categorieën `Host.Results` en worden op het `Error` niveau gefilterd. De `LogCategoryFilter` huidige categorie vergelijkt met alle geregistreerde `CategoryLevels` en kiest de langste overeenkomst. Het `Debug` niveau dat is geregistreerd `Host.Triggers` voor zal `Host.Triggers.Queue` overeenkomen met of `Host.Triggers.Blob`. Zo kunt u grotere categorieën beheren zonder dat u deze hoeft toe te voegen.

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

### <a name="custom-telemetry-for-application-insights"></a>Aangepaste telemetrie voor Application Insights

Het proces voor het implementeren van aangepaste telemetriegegevens voor [Application Insights](../azure-monitor/app/app-insights-overview.md) is afhankelijk van de SDK-versie. Zie [Application Insights logboek registratie toevoegen](webjobs-sdk-get-started.md#add-application-insights-logging)voor meer informatie over het configureren van Application Insights.

#### <a name="version-3x"></a>Versie 3. *x*

Omdat versie 3. *x* van de webjobs SDK is afhankelijk van de algemene host van .net core, een aangepaste telemetrie-Factory is niet meer beschikbaar. Maar u kunt aangepaste telemetrie toevoegen aan de pijp lijn met behulp van afhankelijkheids injectie. Voor de voor beelden in deze sectie zijn `using` de volgende instructies vereist:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Met de volgende aangepaste implementatie [`ITelemetryInitializer`] van kunt u uw eigen [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) toepassing toevoegen aan [`TelemetryConfiguration`]de standaard instelling.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Bel [`ConfigureServices`] de opbouw functie om uw aangepaste [`ITelemetryInitializer`] toe te voegen aan de pijp lijn.

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

Wanneer het [`TelemetryConfiguration`] is gebouwd, worden alle geregistreerde typen [`ITelemetryInitializer`] van opgenomen. Zie [Application Insights-API voor aangepaste gebeurtenissen en metrische gegevens](../azure-monitor/app/api-custom-events-metrics.md)voor meer informatie.

In versie 3. *x*, hoeft u niet meer te wissen [`TelemetryClient`] wanneer de host wordt gestopt. Het .NET core-injectie beafhankelijkheidssysteem maakt automatisch een verwijdering van de `ApplicationInsightsLoggerProvider`geregistreerde, waardoor de [`TelemetryClient`]wordt leeg gemaakt.

#### <a name="version-2x"></a>Versie 2. *x*

In versie 2. *x*, de [`TelemetryClient`] interne door de Application Insights provider voor de webjobs SDK gebruikte [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Wanneer het Application Insights-eind punt niet beschikbaar is of het beperken van binnenkomende aanvragen, slaat dit kanaal [aanvragen op in het bestands systeem van de web-app en verzendt deze later opnieuw](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

De [`TelemetryClient`] wordt gemaakt door een klasse die wordt geïmplementeerd `ITelemetryClientFactory`. Standaard is dit de [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Als u een deel van de Application Insights-pijp lijn wilt wijzigen, kunt u uw eigen `ITelemetryClientFactory`klasse opgeven en wordt de host gebruikt voor het maken van een [`TelemetryClient`]. Deze code overschrijft `DefaultTelemetryClientFactory` bijvoorbeeld voor het wijzigen van een eigenschap van `ServerTelemetryChannel`:

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

Met `SamplingPercentageEstimatorSettings` het object worden [adaptieve steek proeven](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)geconfigureerd. Dit betekent dat in bepaalde scenario's met grote volumes, toepassingen Insights een geselecteerde subset van telemetriegegevens naar de server verzendt.

Nadat u de telemetrie-Factory hebt gemaakt, geeft u deze door aan de Application Insights logging provider:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen

In dit artikel vindt u code fragmenten die laten zien hoe u veelvoorkomende scenario's kunt verwerken voor het werken met de webjobs SDK. Zie voor volledige voor beelden [Azure-webjobs-SDK-voor beelden](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

['ExecutionContext']: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
