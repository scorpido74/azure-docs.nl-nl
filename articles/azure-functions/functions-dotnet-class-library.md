---
title: Naslaginformatie over Azure Functions C#-ontwikkelaars
description: Meer informatie over het ontwikkelen van Azure-functies met C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277061"
---
# <a name="azure-functions-c-developer-reference"></a>Naslaginformatie over Azure Functions C#-ontwikkelaars

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Dit artikel is een inleiding tot het ontwikkelen van Azure-functies met Behulp van C# in .NET-klassenbibliotheken.

Azure Functions ondersteunt programmeertalen C# en C# script. Zie [C# script (.csx) naslaginformatie](functions-reference-csharp.md)over het [gebruik van C# in de Azure-portal.](functions-create-function-app-portal.md)

In dit artikel wordt ervan uitgegaan dat u de volgende artikelen al hebt gelezen:

* [Handleiding azure functions-ontwikkelaars](functions-reference.md)
* [Hulpprogramma's voor Azure-functies Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Ondersteunde versies

Versies van de runtime van de functies werken met specifieke versies van .NET. In de volgende tabel ziet u het hoogste niveau van .NET Core en .NET Framework en .NET Core dat kan worden gebruikt met een specifieke versie van functies in uw project. 

| Runtime-versie voor functies | Max.NET-versie |
| ---- | ---- |
| Functies 3.x | .NET Core 3.1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.6 |

Zie overzicht van [azure-versies voor runtime-versies](functions-versions.md) van Azure Functions voor meer informatie

## <a name="functions-class-library-project"></a>Functiesklassebibliotheekproject

In Visual Studio maakt de projectsjabloon **Azure Functions** een c#-klassebibliotheekproject dat de volgende bestanden bevat:

* [host.json](functions-host-json.md) - slaat configuratie-instellingen op die van invloed zijn op alle functies in het project wanneer deze lokaal of in Azure worden uitgevoerd.
* [local.settings.json](functions-run-local.md#local-settings-file) - slaat app-instellingen en verbindingstekenreeksen op die worden gebruikt wanneer ze lokaal worden uitgevoerd. Dit bestand bevat geheimen en wordt niet gepubliceerd in uw functie-app in Azure. Voeg in plaats daarvan [app-instellingen toe aan de functie-app.](functions-develop-vs.md#function-app-settings)

Wanneer u het project bouwt, wordt een mapstructuur gegenereerd die lijkt op het volgende voorbeeld in de uitvoermap voor bouwen:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Deze map is wat wordt geïmplementeerd in uw functie-app in Azure. De bindende extensies die nodig zijn in [versie 2.x](functions-versions.md) van de runtime functies worden [aan het project toegevoegd als NuGet-pakketten.](./functions-bindings-register.md#vs)

> [!IMPORTANT]
> Met het buildproces wordt voor elke functie een *function.json-bestand* gekanalen. Dit *functie.json-bestand* is niet bedoeld om direct te worden bewerkt. U de bindende configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. Zie [Functies uitschakelen](disable-function.md)voor meer informatie over het uitschakelen van een functie.


## <a name="methods-recognized-as-functions"></a>Methoden die als functies worden herkend

In een klassenbibliotheek is een functie `FunctionName` een statische methode met een en een triggerkenmerk, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Het `FunctionName` kenmerk markeert de methode als een functie-item. De naam moet uniek zijn binnen een project, beginnen met `_`een `-`letter en alleen letters, cijfers, en , tot 127 tekens in lengte bevatten. Projectsjablonen maken vaak `Run`een methode met de naam , maar de methodenaam kan elke geldige C#-methodenaam zijn.

Het kenmerk trigger geeft het triggertype op en bindt invoergegevens aan een methodeparameter. De voorbeeldfunctie wordt geactiveerd door een wachtrijbericht en het wachtrijbericht wordt doorgegeven aan de methode in de `myQueueItem` parameter.

## <a name="method-signature-parameters"></a>Parameters voor methodehandtekening

De methodehandtekening kan andere parameters bevatten dan die welke met het kenmerk trigger worden gebruikt. Hier volgen enkele van de aanvullende parameters die u opnemen:

* [Invoer- en uitvoerbindingen](functions-triggers-bindings.md) die als zodanig zijn gemarkeerd door ze te versieren met kenmerken.  
* Een `ILogger` `TraceWriter` parameter[( versie 1.x-only](functions-versions.md#creating-1x-apps)) voor [logboekregistratie](#logging).
* Een `CancellationToken` parameter voor [sierlijke uitschakeling.](#cancellation-tokens)
* [Bindingsexpressieparameters](./functions-bindings-expressions-patterns.md) om triggermetadata te krijgen.

De volgorde van parameters in de functiehandtekening doet er niet toe. U bijvoorbeeld triggerparameters voor of na andere bindingen plaatsen en u de parameter logger voor of na trigger- of bindingsparameters plaatsen.

### <a name="output-binding-example"></a>Voorbeeld van uitvoerbinding

In het volgende voorbeeld wordt het voorgaande gewijzigd door een uitvoerwachtrijbinding toe te voegen. De functie schrijft het wachtrijbericht dat de functie activeert naar een nieuw wachtrijbericht in een andere wachtrij.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

In de bindende referentieartikelen[(Opslagwachtrijen](functions-bindings-storage-queue.md), bijvoorbeeld) wordt uitgelegd welke parametertypen u gebruiken met trigger-, invoer- of uitvoerbindingskenmerken.

### <a name="binding-expressions-example"></a>Voorbeeld van bindingsexpressies

Met de volgende code wordt de naam van de wachtrij opgehaald die moet `insertionTime` worden bewaakt vanuit een app-instelling en wordt de tijd voor het maken van wachtrijberichten in de parameter weergegeven.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatisch gegenereerde functie.json

Met het buildproces wordt een *function.json-bestand* in een functiemap in de buildmap. Zoals eerder opgemerkt, is dit bestand niet bedoeld om direct te worden bewerkt. U de bindende configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. 

Het doel van dit bestand is om informatie te verstrekken aan de schaalcontroller om te gebruiken voor [het schalen van beslissingen over het verbruiksplan](functions-scale.md#how-the-consumption-and-premium-plans-work). Om deze reden heeft het bestand alleen triggerinfo, geen invoer- of uitvoerbindingen.

Het gegenereerde *function.json-bestand* bevat een `configurationSource` eigenschap die de runtime aangeeft dat ze .NET-kenmerken moeten gebruiken voor bindingen, in plaats van *function.json-configuratie.* Hier volgt een voorbeeld:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

De *functie.json-bestandsgeneratie* wordt uitgevoerd door het NuGet-pakket [\.Microsoft NET\.Sdk-functies\.](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Hetzelfde pakket wordt gebruikt voor zowel versie 1.x als 2.x van de runtime functies. Het doelkader is wat een 1.x-project onderscheidt van een 2.x-project. Hier zijn de relevante onderdelen van *.csproj-bestanden,* met verschillende doelkaders en hetzelfde `Sdk` pakket:

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Onder `Sdk` de pakketafhankelijkheden zijn triggers en bindingen. Een 1.x-project verwijst naar 1.x triggers en bindingen omdat deze triggers en bindingen zich richten op het .NET Framework, terwijl 2.x triggers en bindingen target .NET Core.

Het `Sdk` pakket is ook afhankelijk van [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), en indirect op [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Deze afhankelijkheden zorgen ervoor dat uw project de versies gebruikt van de pakketten die werken met de runtime-versie van functies waarop het project is gericht. Heeft bijvoorbeeld `Newtonsoft.Json` versie 11 voor .NET Framework 4.6.1, maar de runtime functies waarop .NET `Newtonsoft.Json` Framework 4.6.1 is gericht, is alleen compatibel met 9.0.1. Dus uw functiecode in dat `Newtonsoft.Json` project moet ook 9.0.1 gebruiken.

De broncode `Microsoft.NET.Sdk.Functions` voor is beschikbaar in de GitHub repo [azure-functies\-\-vs\-build\-sdk.](https://github.com/Azure/azure-functions-vs-build-sdk)

## <a name="runtime-version"></a>Runtime-versie

Visual Studio gebruikt de [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) om functiesprojecten uit te voeren. De Core Tools is een command-line interface voor de functions runtime.

Als u de Core Tools installeert met behulp van npm, heeft dat geen invloed op de Core Tools-versie die door Visual Studio wordt gebruikt. Voor de runtimeversie 1.x functies slaat Visual Studio de versies van Core Tools op in *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* en gebruikt het de nieuwste versie die daar is opgeslagen. Voor Functies 2.x zijn de Core-hulpprogramma's opgenomen in de extensie **Azure-functies en Webjobs Tools.** Voor zowel 1.x als 2.x u zien welke versie wordt gebruikt in de console-uitvoer wanneer u een functieproject uitvoert:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Ondersteunde typen voor bindingen

Elke binding heeft zijn eigen ondersteunde types; Een blobtriggerkenmerk kan bijvoorbeeld worden toegepast op een tekenreeksparameter, `CloudBlockBlob` een POCO-parameter, een parameter of een van de andere ondersteunde typen. In [het bindend referentieartikel voor blobbindingen](functions-bindings-storage-blob-trigger.md#usage) worden alle ondersteunde parametertypen weergegeven. Zie [Triggers en bindingen en](functions-triggers-bindings.md) de bindende [referentiedocumenten voor elk bindend type voor](functions-triggers-bindings.md#next-steps)meer informatie.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Binding met de retourwaarde van de methode

U een methoderetourwaarde gebruiken voor een uitvoerbinding door het kenmerk toe te passen op de retourwaarde van de methode. Zie Triggers [en bindingen](./functions-bindings-return-value.md)voor voorbeelden. 

Gebruik de retourwaarde alleen als een succesvolle functieuitvoering altijd resulteert in een retourwaarde die moet worden doorgegeven aan de uitvoerbinding. Gebruik `ICollector` of, `IAsyncCollector`zoals in de volgende sectie wordt weergegeven.

## <a name="writing-multiple-output-values"></a>Meerdere uitvoerwaarden schrijven

Als u meerdere waarden wilt schrijven naar een uitvoerbinding of als een succesvolle functieaanroep [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) er mogelijk niet toe leidt dat er iets wordt doorgegeven aan de uitvoerbinding, gebruikt u de typen of typen. Deze typen zijn alleen-schrijfverzamelingen die naar de uitvoerbinding worden geschreven wanneer de methode is voltooid.

In dit voorbeeld worden meerdere wachtrijberichten `ICollector`in dezelfde wachtrij geschreven met :

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Logboekregistratie

Als u de uitvoer wilt registreren bij uw streaminglogboeken in C#, neemt u een argument van het type [ILogger op.](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) We raden u `log`aan deze naam te geven, zoals in het volgende voorbeeld:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Vermijd `Console.Write` het gebruik in Azure-functies. Zie [Logboeken schrijven in C#-functies](functions-monitoring.md#write-logs-in-c-functions) in het artikel **Azure-functies controleren** voor meer informatie.

## <a name="async"></a>Async

Als u een functie [asynchroon wilt](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)maken, gebruikt u het `async` trefwoord en retourneert u een `Task` object.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

U geen `out` parameters gebruiken in async-functies. Gebruik in plaats daarvan de [functieretourwaarde](#binding-to-method-return-value) of een [verzamelobject](#writing-multiple-output-values) voor uitvoerbindingen.

## <a name="cancellation-tokens"></a>Annuleringstokens

Een functie kan een [parameter CancellationToken](/dotnet/api/system.threading.cancellationtoken) accepteren, waarmee het besturingssysteem uw code kan melden wanneer de functie op het punt staat te worden beëindigd. U deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status achterlaat.

In het volgende voorbeeld ziet u hoe u controleren op dreigende functiebeëindiging.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
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
}
```

## <a name="environment-variables"></a>Omgevingsvariabelen

Als u een omgevingsvariabele of een `System.Environment.GetEnvironmentVariable`waarde voor app-instelling wilt krijgen, gebruikt u , zoals weergegeven in het volgende codevoorbeeld:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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
}
```

App-instellingen kunnen worden afgelezen op basis van omgevingsvariabelen, zowel bij het lokaal ontwikkelen als bij het uitvoeren in Azure. Bij het lokaal ontwikkelen komen `Values` de app-instellingen uit de verzameling in het bestand *local.settings.json.* Haalt in beide omgevingen, lokaal en Azure, `GetEnvironmentVariable("<app setting name>")` de waarde op van de benoemde app-instelling. Wanneer u bijvoorbeeld lokaal wordt uitgevoerd, wordt 'Mijn sitenaam' geretourneerd als het bestand `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` *local.settings.json* .

De eigenschap [System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) is een alternatieve API voor het verkrijgen `GetEnvironmentVariable` van app-instellingswaarden, maar we raden u aan deze te gebruiken zoals hier wordt weergegeven.

## <a name="binding-at-runtime"></a>Binding bij runtime

In C# en andere .NET-talen u een [noodzakelijk](https://en.wikipedia.org/wiki/Imperative_programming) bindend patroon gebruiken, in tegenstelling tot de [*declaratieve*](https://en.wikipedia.org/wiki/Declarative_programming) bindingen in kenmerken. Verplichte binding is handig wanneer bindende parameters moeten worden berekend tijdens runtime in plaats van ontwerptijd. Met dit patroon u zich binden aan ondersteunde invoer- en uitvoerbindingen on-the-fly in uw functiecode.

Definieer als volgt een dwingende binding:

- **Neem geen** attribuut op in de functiehandtekening voor uw gewenste imperatieve bindingen.
- Geef een [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) invoerparameter [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)door of .
- Gebruik het volgende C#-patroon om de gegevensbinding uit te voeren.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`is het kenmerk .NET dat `T` uw binding definieert en een invoer- of uitvoertype is dat wordt ondersteund door dat bindingstype. `T`kan geen `out` parametertype zijn `out JObject`(zoals). De tabel uitvoerbinding van Mobiele apps ondersteunt bijvoorbeeld [zes uitvoertypen,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)maar u alleen [\<ICollector T->](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of [IAsyncCollector\<T->](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) gebruiken met een dwingende binding.

### <a name="single-attribute-example"></a>Voorbeeld van één kenmerk

Met de volgende voorbeeldcode wordt een [binding voor de uitvoer van opslagblobs](functions-bindings-storage-blob-output.md) met blobpad dat bij de looptijd is gedefinieerd, en wordt vervolgens een tekenreeks naar de blob geschreven.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definieert de invoer- of uitvoerbinding van de [opslagblob](functions-bindings-storage-blob.md) en [TextWriter](/dotnet/api/system.io.textwriter) is een ondersteund uitvoerbindingstype.

### <a name="multiple-attribute-example"></a>Voorbeeld van meerdere kenmerken

In het voorgaande voorbeeld wordt de app-instelling voor de hoofdtekenreeks voor de accountverbinding opslagvan de functie-app (wat wil is `AzureWebJobsStorage`) opdeplaats. U een aangepaste app-instelling opgeven die u voor het opslagaccount `BindAsync<T>()`wilt gebruiken door het [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) toe te voegen en de kenmerkarray door te geven aan. Gebruik `Binder` een parameter, niet `IBinder`.  Bijvoorbeeld:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Triggers en bindingen 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Meer informatie over aanbevolen procedures voor Azure-functies](functions-best-practices.md)
