---
title: Naslaginformatie over scriptontwikkelaars van Azure Functions C#
description: Meer informatie over het ontwikkelen van Azure-functies met behulp van C#-script.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276814"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Verwijzing naar Azure Functions C# script (.csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Dit artikel is een inleiding tot het ontwikkelen van Azure-functies met behulp van C# script (*.csx*).

Azure Functions ondersteunt programmeertalen C# en C# script. Zie [C# developer reference](functions-dotnet-class-library.md)als u op zoek bent naar richtlijnen voor [het gebruik van C# in een visual studio-bibliotheekproject.](functions-develop-vs.md)

In dit artikel wordt ervan uitgegaan dat u de [handleiding azure functions-ontwikkelaars](functions-reference.md)al hebt gelezen.

## <a name="how-csx-works"></a>Hoe .csx werkt

De C#-scriptervaring voor Azure-functies is gebaseerd op de [Azure WebJobs SDK.](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction) Gegevens stromen via methodeargumenten naar uw C#-functie. Argumentnamen worden opgegeven `function.json` in een bestand en er zijn vooraf gedefinieerde namen voor toegang tot zaken als de functielogger en annuleringstokens.

Met het *.csx-formaat* u minder boilerplate schrijven en u concentreren op het schrijven van slechts een C#-functie. In plaats van alles in een naamruimte `Run` en klasse te verpakken, definieerje je gewoon een methode. Voeg zoals gewoonlijk alle verwijzingen naar de assemblage en naamruimten aan het begin van het bestand toe.

De *CSX-bestanden van een* functie-app worden gecompileerd wanneer een instantie wordt geïnitialiseerd. Deze compilatiestap betekent dat zaken als koude start langer kunnen duren voor C# scriptfuncties in vergelijking met C# klassebibliotheken. Deze compilatiestap is ook de reden waarom C#-scriptfuncties kunnen worden bewerkt in de Azure-portal, terwijl C#-klassebibliotheken dat niet zijn.

## <a name="folder-structure"></a>Mapstructuur

De mapstructuur voor een C#-scriptproject ziet er als volgt uit:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Er is een gedeeld [host.json-bestand](functions-host-json.md) dat kan worden gebruikt om de functie-app te configureren. Elke functie heeft zijn eigen codebestand (.csx) en bindend configuratiebestand (function.json).

De bindende extensies die nodig zijn in [versie 2.x en latere versies](functions-versions.md) van de runtime functies worden gedefinieerd in het `extensions.csproj` bestand, met de werkelijke bibliotheekbestanden in de `bin` map. Wanneer u lokaal ontwikkelt, moet u [bindende extensies registreren.](./functions-bindings-register.md#extension-bundles) Bij het ontwikkelen van functies in de Azure-portal wordt deze registratie voor u uitgevoerd.

## <a name="binding-to-arguments"></a>Binding aan argumenten

Invoer- of uitvoergegevens zijn gebonden aan een `name` C#-scriptfunctieparameter via de eigenschap in het configuratiebestand *function.json.* In het volgende voorbeeld wordt een *function.json-bestand* en *run.csx-bestand* weergegeven voor een functie die in de wachtrij wordt geactiveerd. De parameter die gegevens uit het `myQueueItem` wachtrijbericht ontvangt, wordt `name` benoemd omdat dat de waarde van de eigenschap is.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

De `#r` verklaring wordt [later in dit artikel](#referencing-external-assemblies)uitgelegd.

## <a name="supported-types-for-bindings"></a>Ondersteunde typen voor bindingen

Elke binding heeft zijn eigen ondersteunde types; Een blobtrigger kan bijvoorbeeld worden gebruikt met een tekenreeksparameter, `CloudBlockBlob` een POCO-parameter, een parameter of een van de andere ondersteunde typen. In [het bindend referentieartikel voor blobbindingen](functions-bindings-storage-blob-trigger.md#usage) worden alle ondersteunde parametertypen voor blobtriggers weergegeven. Zie [Triggers en bindingen en](functions-triggers-bindings.md) de bindende [referentiedocumenten voor elk bindend type voor](functions-triggers-bindings.md#next-steps)meer informatie.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Aangepaste klassen verwijzen

Als u een aangepaste POCO-klasse (Plain Old CLR Object) moet gebruiken, u de klassendefinitie in hetzelfde bestand opnemen of in een apart bestand plaatsen.

In het volgende voorbeeld wordt een voorbeeld *van run.csx* weergegeven met een POCO-klassendefinitie.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Een POCO-klasse moet een getter en setter hebben die voor elke eigenschap zijn gedefinieerd.

## <a name="reusing-csx-code"></a>Csx-code opnieuw gebruiken

U klassen en methoden gebruiken die zijn gedefinieerd in andere *.csx-bestanden* in uw *run.csx-bestand.* Gebruik daarvoor `#load` richtlijnen in uw *run.csx-bestand.* In het volgende voorbeeld wordt `MyLogger` een naam registratieroutine gedeeld in *myLogger.csx* `#load` en in *run.csx* geladen met behulp van de richtlijn:

Voorbeeld *run.csx:*

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Voorbeeld *mylogger.csx:*

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Het gebruik van een gedeeld *.csx-bestand* is een veelvoorkomend patroon wanneer u de gegevens die tussen functies worden doorgegeven met behulp van een POCO-object sterk wilt typen. In het volgende vereenvoudigde voorbeeld delen een HTTP-trigger en `Order` wachtrijtrigger een POCO-object dat is genoemd om de ordergegevens sterk te typen:

Voorbeeld *run.csx* voor HTTP-trigger:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Voorbeeld *run.csx* voor wachtrijtrigger:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Voorbeeld *order.csx:*

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

U een relatief `#load` pad gebruiken met de richtlijn:

* `#load "mylogger.csx"`laadt een bestand in de functiemap.
* `#load "loadedfiles\mylogger.csx"`hiermee wordt een bestand in een map in de functiemap geladen.
* `#load "..\shared\mylogger.csx"`laadt een bestand in een map op hetzelfde niveau als de functiemap, dat wil zeggen direct onder *wwwroot.*

De `#load` richtlijn werkt alleen met *.csx-bestanden,* niet met *.cs-bestanden.*

## <a name="binding-to-method-return-value"></a>Binding met de retourwaarde van de methode

U een methoderetourwaarde gebruiken voor een `$return` uitvoerbinding met de naam in *function.json*. Zie Triggers [en bindingen](./functions-bindings-return-value.md)voor voorbeelden.

Gebruik de retourwaarde alleen als een succesvolle functieuitvoering altijd resulteert in een retourwaarde die moet worden doorgegeven aan de uitvoerbinding. Gebruik `ICollector` of, `IAsyncCollector`zoals in de volgende sectie wordt weergegeven.

## <a name="writing-multiple-output-values"></a>Meerdere uitvoerwaarden schrijven

Als u meerdere waarden wilt schrijven naar een uitvoerbinding of als een succesvolle functieaanroep [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) er mogelijk niet toe leidt dat er iets wordt doorgegeven aan de uitvoerbinding, gebruikt u de typen of typen. Deze typen zijn alleen-schrijfverzamelingen die naar de uitvoerbinding worden geschreven wanneer de methode is voltooid.

In dit voorbeeld worden meerdere wachtrijberichten `ICollector`in dezelfde wachtrij geschreven met :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Logboekregistratie

Als u de uitvoer wilt registreren bij uw streaminglogboeken in C#, neemt u een argument van het type [ILogger op.](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) We raden u `log`aan deze naam te noemen. Vermijd `Console.Write` het gebruik in Azure-functies.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Zie Logboeken schrijven in C#-functies `TraceWriter`in het artikel **Azure-functies controleren** voor informatie over een nieuwer logboekenframework dat u gebruiken in plaats [van: Logboeken in C#-functies.](functions-monitoring.md#write-logs-in-c-functions)

## <a name="async"></a>Async

Als u een functie [asynchroon wilt](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)maken, gebruikt u het `async` trefwoord en retourneert u een `Task` object.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

U geen `out` parameters gebruiken in async-functies. Gebruik in plaats daarvan de [functieretourwaarde](#binding-to-method-return-value) of een [verzamelobject](#writing-multiple-output-values) voor uitvoerbindingen.

## <a name="cancellation-tokens"></a>Annuleringstokens

Een functie kan een [parameter CancellationToken](/dotnet/api/system.threading.cancellationtoken) accepteren, waarmee het besturingssysteem uw code kan melden wanneer de functie op het punt staat te worden beëindigd. U deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status achterlaat.

In het volgende voorbeeld ziet u hoe u controleren op dreigende functiebeëindiging.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Naamruimten importeren

Als u naamruimten moet importeren, u dit `using` zoals gewoonlijk doen, met de clausule.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

De volgende naamruimten worden automatisch geïmporteerd en zijn daarom optioneel:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Verwijzen naar externe samenstellingen

Voeg voor kadersamenstellingen referenties toe `#r "AssemblyName"` met behulp van de richtlijn.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

De volgende samenstellingen worden automatisch toegevoegd door de hostingomgeving azure functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Naar de volgende samenstellingen kan worden verwezen onder `#r "AssemblyName"`de naam "Simple-name":

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Aangepaste samenstellingen verwijzen

Als u wilt verwijzen naar een aangepaste assemblage, u een *gedeelde* assemblage of een *privéassemblage* gebruiken:

* Gedeelde samenstellingen worden gedeeld over alle functies binnen een functie-app. Als u naar een aangepaste verzameling wilt `bin` verwijzen, uploadt u de verzameling naar een map met de naam in de hoofdmap van de [functie-app](functions-reference.md#folder-structure) (wwwroot).

* Privéassemblages maken deel uit van de context van een bepaalde functie en ondersteunen sideloading van verschillende versies. Privévergaderingen moeten worden geüpload in een `bin` map in de functiemap. Verwijs naar de verzamelingen met `#r "MyAssembly.dll"`de bestandsnaam, zoals .

Zie de sectie over [pakketbeheer](#using-nuget-packages)voor informatie over het uploaden van bestanden naar uw functiemap.

### <a name="watched-directories"></a>Bekeken mappen

De map met het functiescriptbestand wordt automatisch in de gaten gehouden voor wijzigingen in verzamelingen. Als u wilt kijken naar wijzigingen in `watchDirectories` andere mappen, voegt u deze toe aan de lijst in [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>NuGet-pakketten gebruiken
Als u NuGet-pakketten wilt gebruiken in een functie van 2.x en later C#, uploadt u een *function.proj-bestand* naar de map van de functie in het bestandssysteem van de functie-app. Hier is een voorbeeld *function.proj* bestand dat een verwijzing naar *Microsoft.ProjectOxford.Face* versie *1.1.0*toevoegt:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Als u een aangepaste NuGet-feed wilt gebruiken, geeft u de feed op in een *Nuget.Config-bestand* in de hoofdmap van de functie-app. Zie [NuGet-gedrag configureren](/nuget/consume-packages/configuring-nuget-behavior)voor meer informatie .

> [!NOTE]
> In 1.x C#-functies wordt naar NuGet-pakketten verwezen met een *project.json-bestand* in plaats van een *function.proj-bestand.*

Gebruik in plaats daarvan een *project.json-bestand* voor 1.x-functies. Hier is een voorbeeld *project.json* bestand:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Een function.proj-bestand gebruiken

1. Open de functie in de Azure-portal. Op het tabblad Logboeken wordt de uitvoer van de pakketinstallatie weergegeven.
2. Als u een *function.proj-bestand* wilt uploaden, gebruikt u een van de methoden die zijn beschreven in het [referentieonderwerp Voor functie-apps](functions-reference.md#fileupdate) in het referentieonderwerp azure functions-ontwikkelaars.
3. Nadat het *functie.proj-bestand* is geüpload, ziet u uitvoer zoals het volgende voorbeeld in het streaminglogboek van uw functie:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Omgevingsvariabelen

Als u een omgevingsvariabele of een `System.Environment.GetEnvironmentVariable`waarde voor app-instelling wilt krijgen, gebruikt u , zoals weergegeven in het volgende codevoorbeeld:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Binding bij runtime

In C# en andere .NET-talen u een [dwingend](https://en.wikipedia.org/wiki/Imperative_programming) bindend patroon gebruiken, in tegenstelling tot de [*declaratieve*](https://en.wikipedia.org/wiki/Declarative_programming) bindingen in *function.json*. Verplichte binding is handig wanneer bindende parameters moeten worden berekend tijdens runtime in plaats van ontwerptijd. Met dit patroon u zich binden aan ondersteunde invoer- en uitvoerbindingen on-the-fly in uw functiecode.

Definieer als volgt een dwingende binding:

- **Neem geen** vermelding in *function.json* op voor uw gewenste imperatieve bindingen.
- Geef een [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) invoerparameter [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)door of .
- Gebruik het volgende C#-patroon om de gegevensbinding uit te voeren.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`is het kenmerk .NET dat `T` uw binding definieert en een invoer- of uitvoertype is dat wordt ondersteund door dat bindingstype. `T`kan geen `out` parametertype zijn `out JObject`(zoals). De tabel uitvoerbinding van mobiele apps ondersteunt bijvoorbeeld [zes uitvoertypen,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)maar [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) `T`u alleen [\<ICollector T->](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of voor.

### <a name="single-attribute-example"></a>Voorbeeld van één kenmerk

Met de volgende voorbeeldcode wordt een [binding voor de uitvoer van opslagblobs](functions-bindings-storage-blob-output.md) met blobpad dat bij de looptijd is gedefinieerd, en wordt vervolgens een tekenreeks naar de blob geschreven.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definieert de invoer- of uitvoerbinding van de [opslagblob](functions-bindings-storage-blob.md) en [TextWriter](/dotnet/api/system.io.textwriter) is een ondersteund uitvoerbindingstype.

### <a name="multiple-attribute-example"></a>Voorbeeld van meerdere kenmerken

In het voorgaande voorbeeld wordt de app-instelling voor de hoofdtekenreeks voor de accountverbinding opslagvan de functie-app (wat wil is `AzureWebJobsStorage`) opdeplaats. U een aangepaste app-instelling opgeven die u voor het opslagaccount `BindAsync<T>()`wilt gebruiken door het [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) toe te voegen en de kenmerkarray door te geven aan. Gebruik `Binder` een parameter, niet `IBinder`.  Bijvoorbeeld:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

In de volgende tabel worden de .NET-kenmerken weergegeven voor elk bindingstype en de pakketten waarin ze zijn gedefinieerd.

> [!div class="mx-codeBreakAll"]
> | Binding | Kenmerk | Verwijzing toevoegen |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Opslagwachtrij | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Opslag-blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Opslagtabel | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over aanbevolen procedures voor Azure-functies](functions-best-practices.md)
