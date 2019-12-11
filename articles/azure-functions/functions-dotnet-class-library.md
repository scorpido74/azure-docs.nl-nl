---
title: Naslag C# informatie voor Azure functions ontwikkel aars
description: Meer informatie over het ontwikkelen van C#Azure functions met behulp van.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: f412e5ea358fe7b97476802f432616c37b05dbd9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975479"
---
# <a name="azure-functions-c-developer-reference"></a>Naslag C# informatie voor Azure functions ontwikkel aars

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Dit artikel is een inleiding tot het ontwikkelen van Azure Functions C# door gebruik te maken van de .net-klassen bibliotheken.

Azure Functions ondersteunt C# en C# script programmeer talen. [Zie C# Naslag informatie voor ontwikkel aars van scripts (. CSX)](functions-reference-csharp.md)als u op zoek bent naar richt lijnen voor [het gebruik C# van de Azure Portal](functions-create-function-app-portal.md).

In dit artikel wordt ervan uitgegaan dat u de volgende artikelen al hebt gelezen:

* [Hand leiding Azure Functions ontwikkel aars](functions-reference.md)
* [Azure Functions Visual Studio 2019-Hulpprogram Ma's](functions-develop-vs.md)

## <a name="supported-versions"></a>Ondersteunde versies

Versies van de functions-runtime werken met specifieke versies van .NET. De volgende tabel toont het hoogste niveau van .NET core en .NET Framework en .NET core dat kan worden gebruikt met een specifieke versie van functies in uw project. 

| Runtime versie van functions | Maximum versie van .NET |
| ---- | ---- |
| Functies 3. x | .NET Core 3,1 |
| Functions 2.x | .NET Core 2.2 |
| Functions 1.x | .NET Framework 4.6 |

Zie [Azure functions overzicht van runtime versies](functions-versions.md) voor meer informatie

## <a name="functions-class-library-project"></a>Class-bibliotheek project voor functies

In Visual Studio maakt de sjabloon **Azure functions** project een C# klassen bibliotheek project die de volgende bestanden bevat:

* [host. json](functions-host-json.md) : slaat configuratie-instellingen op die van invloed zijn op alle functies in het project wanneer lokaal of in azure wordt uitgevoerd.
* [Local. settings. json](functions-run-local.md#local-settings-file) : slaat app-instellingen en verbindings reeksen op die worden gebruikt bij het lokaal uitvoeren. Dit bestand bevat geheimen en wordt niet gepubliceerd in uw functie-app in Azure. Voeg in plaats daarvan [app-instellingen toe aan de functie-app](functions-develop-vs.md#function-app-settings).

Wanneer u het project bouwt, wordt een mapstructuur die eruitziet als in het volgende voor beeld gegenereerd in de map build Uitvoermap:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Deze map wordt geïmplementeerd in uw functie-app in Azure. De binding-uitbrei dingen die zijn vereist in [versie 2. x](functions-versions.md) van de functions-runtime, worden [toegevoegd aan het project als NuGet-pakketten](./functions-bindings-register.md#vs).

> [!IMPORTANT]
> Het bouw proces maakt een *Function. json* -bestand voor elke functie. Deze *functie. json* -bestand is niet bedoeld om rechtstreeks te worden bewerkt. U kunt de bindings configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. Zie [functies uitschakelen](disable-function.md)voor meer informatie over het uitschakelen van een functie.


## <a name="methods-recognized-as-functions"></a>Methoden die worden herkend als functions

In een klassen bibliotheek is een functie een statische methode met een `FunctionName` en een trigger kenmerk, zoals wordt weer gegeven in het volgende voor beeld:

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

Het kenmerk `FunctionName` markeert de methode als een functie-ingangs punt. De naam moet uniek zijn binnen een project, beginnen met een letter en mag alleen letters, cijfers, `_`en `-`bevatten, Maxi maal 127 tekens lang zijn. Project sjablonen maken vaak een methode met de naam `Run`, maar de naam van de methode C# kan een geldige methode naam zijn.

Het trigger kenmerk geeft het trigger type aan en bindt invoer gegevens aan een methode parameter. De functie voor beeld wordt geactiveerd door een wachtrij bericht en het wachtrij bericht wordt door gegeven aan de methode in de para meter `myQueueItem`.

## <a name="method-signature-parameters"></a>Methode handtekening parameters

De methode handtekening kan andere para meters bevatten dan de hand tekening die is gebruikt met het trigger kenmerk. Hier volgen enkele van de aanvullende para meters die u kunt gebruiken:

* [Invoer-en uitvoer bindingen](functions-triggers-bindings.md) die als zodanig zijn gemarkeerd door ze te verduidelijken met kenmerken.  
* Een `ILogger`-of `TraceWriter`-para meter ([alleen x-versie 1](functions-versions.md#creating-1x-apps)) voor [logboek registratie](#logging).
* Een `CancellationToken` para meter voor een [correcte afsluit](#cancellation-tokens)actie.
* [Bindings expressies](./functions-bindings-expressions-patterns.md) para meters voor het ophalen van meta gegevens van triggers.

De volg orde van de para meters in de functie handtekening is hierbij niet van belang. U kunt bijvoorbeeld trigger parameters plaatsen voor of na andere bindingen en u kunt de para meter logger voor of na de trigger-of bindings parameters plaatsen.

### <a name="output-binding-example"></a>Voor beeld van uitvoer binding

In het volgende voor beeld wordt de vorige wijziging aangebracht door een uitvoer wachtrij binding toe te voegen. Met de functie wordt het wachtrij bericht geschreven dat de functie activeert naar een nieuw wachtrij bericht in een andere wachtrij.

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

De bindende referentie artikelen ([opslag wachtrijen](functions-bindings-storage-queue.md), bijvoorbeeld) geven aan welke parameter typen u kunt gebruiken met de bindings kenmerken trigger, invoer of uitvoer.

### <a name="binding-expressions-example"></a>Voor beeld van bindings expressies

Met de volgende code wordt de naam van de wachtrij opgehaald die moet worden bewaakt vanuit een app-instelling en wordt de aanmaak tijd van het wachtrij bericht opgehaald in de para meter `insertionTime`.

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

## <a name="autogenerated-functionjson"></a>Automatisch gegenereerde functie. json

Het bouw proces maakt een *Function. json* -bestand in een functie map in de map build. Zoals eerder is vermeld, is dit bestand niet bedoeld om rechtstreeks te worden bewerkt. U kunt de bindings configuratie niet wijzigen of de functie uitschakelen door dit bestand te bewerken. 

Het doel van dit bestand is om informatie aan de schaal controller te verstrekken die moet worden gebruikt voor [het schalen van beslissingen over het verbruiks abonnement](functions-scale.md#how-the-consumption-and-premium-plans-work). Daarom heeft het bestand alleen trigger gegevens, geen invoer-of uitvoer bindingen.

De gegenereerde *functie. json* -bestand bevat een `configurationSource`-eigenschap die aangeeft dat de runtime .net-kenmerken voor bindingen moet gebruiken in plaats van *Function. json* -configuratie. Hier volgt een voorbeeld:

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

Het gegenereerde bestand *Function. json* wordt uitgevoerd door het NuGet-pakket [micro soft\.NET\.SDK\.functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Hetzelfde pakket wordt gebruikt voor versie 1. x en 2. x van de functions-runtime. Het doel raamwerk is een onderscheid tussen een 1. x-project en een 2. x-project. Hier vindt u de relevante delen van *. csproj* -bestanden, met verschillende doel raamwerken en hetzelfde `Sdk`-pakket:

**Functions 1. x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functies 2. x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Onder de `Sdk` pakket afhankelijkheden zijn triggers en bindingen. Een 1. x-project verwijst naar 1. x-triggers en-bindingen, omdat de triggers en bindingen het .NET Framework doel hebben, terwijl 2. x-triggers en-bindingen .NET core hebben.

Het `Sdk`-pakket is ook afhankelijk van [Newton soft. json](https://www.nuget.org/packages/Newtonsoft.Json)en indirect op [WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Deze afhankelijkheden zorgen ervoor dat uw project gebruikmaakt van de versies van de pakketten die werken met de runtime versie van functions die het project doel heeft. `Newtonsoft.Json` heeft bijvoorbeeld versie 11 voor .NET Framework 4.6.1, maar de functions-runtime die is gericht op .NET Framework 4.6.1 is alleen compatibel met `Newtonsoft.Json` 9.0.1. De functie code in dat project moet dus ook `Newtonsoft.Json` 9.0.1 gebruiken.

De bron code voor `Microsoft.NET.Sdk.Functions` is beschikbaar in de GitHub opslag plaats [azure\-functions\-versus\-build\-SDK](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Runtime versie

Visual Studio maakt gebruik van de [Azure functions core tools](functions-run-local.md#install-the-azure-functions-core-tools) om functies projecten uit te voeren. De belangrijkste Hulpprogram Ma's is een opdracht regel interface voor de functions-runtime.

Als u de basis Hulpprogramma's installeert met behulp van NPM, is dit niet van invloed op de kern Hulpprogramma's versie die wordt gebruikt door Visual Studio. Voor de functions runtime versie 1. x slaat Visual Studio versies van kern Hulpprogramma's op in *%userprofile%\AppData\Local\Azure.functions.cli* en maakt gebruik van de meest recente versie die daar is opgeslagen. Voor functies 2. x zijn de kern Hulpprogramma's opgenomen in de uitbrei ding **Azure functions en webjobs** . Voor beide 1. x en 2. x kunt u zien welke versie wordt gebruikt in de console-uitvoer wanneer u een functions-project uitvoert:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Ondersteunde typen voor bindingen

Elke binding heeft zijn eigen ondersteunde typen. een BLOB-trigger kenmerk kan bijvoorbeeld worden toegepast op een teken reeks parameter, een POCO-para meter, een `CloudBlockBlob` para meter of een van de andere ondersteunde typen. Het [referentie-artikel voor bindingen voor BLOB-bindingen](functions-bindings-storage-blob.md#trigger---usage) bevat een lijst met alle ondersteunde parameter typen. Zie voor meer informatie [Triggers en bindingen](functions-triggers-bindings.md) en de [verwijzings documenten voor bindingen voor elk bindings type](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Binding met retour waarde van methode

U kunt de retour waarde van een methode voor een uitvoer binding gebruiken door het kenmerk toe te passen op de retour waarde van de methode. Zie [Triggers en bindingen](./functions-bindings-return-value.md)voor voor beelden. 

Gebruik de retour waarde alleen als de uitvoering van een geslaagde functie altijd resulteert in een retour waarde die aan de uitvoer binding moet worden door gegeven. Gebruik anders `ICollector` of `IAsyncCollector`, zoals wordt weer gegeven in de volgende sectie.

## <a name="writing-multiple-output-values"></a>Meerdere uitvoer waarden schrijven

Als u meerdere waarden naar een uitvoer binding wilt schrijven, of als het aanroepen van een geslaagde functie ertoe kan leiden dat er niets kan worden door gegeven aan de uitvoer binding, gebruikt u de [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typen. Deze typen zijn alleen-schrijven verzamelingen die naar de uitvoer binding worden geschreven wanneer de methode is voltooid.

In dit voor beeld worden meerdere wachtrij berichten naar dezelfde wachtrij geschreven met behulp van `ICollector`:

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

Als u de uitvoer wilt registreren in C#uw streaming-logboeken, neemt u een argument van het type [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger)op. U wordt aangeraden deze `log`een naam te bieden, zoals in het volgende voor beeld:  

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

Vermijd het gebruik van `Console.Write` in Azure Functions. Zie [Logboeken schrijven in C# functies](functions-monitoring.md#write-logs-in-c-functions) in het artikel **monitor Azure functions** voor meer informatie.

## <a name="async"></a>Asynchroon

Als u een functie [asynchroon](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)wilt maken, gebruikt u het sleutel woord `async` en retourneert u een `Task`-object.

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

U kunt `out` para meters niet gebruiken in async-functies. Voor uitvoer bindingen gebruikt u in plaats daarvan de [functie retour waarde](#binding-to-method-return-value) of een [Collector-object](#writing-multiple-output-values) .

## <a name="cancellation-tokens"></a>Annulerings tokens

Een functie kan een [CancellationToken](/dotnet/api/system.threading.cancellationtoken) -para meter accepteren, waardoor het besturings systeem uw code op de hoogte stelt wanneer de functie wordt beëindigd. U kunt deze melding gebruiken om ervoor te zorgen dat de functie niet onverwacht wordt beëindigd op een manier die gegevens in een inconsistente status laat.

In het volgende voor beeld ziet u hoe u kunt controleren op verdere beëindiging van de functie.

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

Als u een omgevings variabele of een instellings waarde voor een app wilt ophalen, gebruikt u `System.Environment.GetEnvironmentVariable`, zoals wordt weer gegeven in het volgende code voorbeeld:

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

App-instellingen kunnen worden gelezen van omgevings variabelen bij het lokaal ontwikkelen en wanneer ze worden uitgevoerd in Azure. Wanneer u lokaal ontwikkelt, worden de app-instellingen opgehaald uit de verzameling `Values` in het bestand *Local. settings. json* . In beide omgevingen, lokaal en Azure `GetEnvironmentVariable("<app setting name>")`, wordt de waarde van de benoemde app-instelling opgehaald. Wanneer u bijvoorbeeld lokaal uitvoert, wordt ' mijn site naam ' geretourneerd als uw *lokale. settings. json* -bestand `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`bevat.

De eigenschap [System. Configuration. ConfigurationManager. AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) is een alternatieve API voor het ophalen van waarden voor app-instellingen, maar we raden u aan `GetEnvironmentVariable` te gebruiken zoals hier wordt weer gegeven.

## <a name="binding-at-runtime"></a>Binding tijdens runtime

In C# en andere .net-talen kunt u een [dwingend](https://en.wikipedia.org/wiki/Imperative_programming) bindings patroon gebruiken, in plaats van de [*declaratieve*](https://en.wikipedia.org/wiki/Declarative_programming) bindingen in kenmerken. Dwingende binding is handig wanneer bindings parameters tijdens runtime moeten worden berekend in plaats van ontwerp tijd. Met dit patroon kunt u verbinding maken met ondersteunde invoer-en uitvoer bindingen die onderweg zijn in uw functie code.

Definieer als volgt een dwingende binding:

- Neem in de hand tekening van de functie **geen** kenmerk op voor de gewenste dwingende bindingen.
- Geef een invoer parameter op [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) of [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Gebruik het volgende C# patroon om de gegevens binding uit te voeren.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` is het .NET-kenmerk dat uw binding definieert en `T` een invoer-of uitvoer type is dat wordt ondersteund door dat bindings type. `T` kan geen `out` parameter type zijn (zoals `out JObject`). De Mobile Apps tabel uitvoer binding ondersteunt bijvoorbeeld [zes uitvoer typen](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), maar u kunt alleen [ICollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) of [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) met dwingende binding gebruiken.

### <a name="single-attribute-example"></a>Voor beeld van één kenmerk

Met de volgende voorbeeld code wordt een [opslag-BLOB-uitvoer binding](functions-bindings-storage-blob.md#output) gemaakt met een BLOB-pad dat tijdens runtime is gedefinieerd, waarna een teken reeks naar de BLOB wordt geschreven.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definieert de invoer-of uitvoer binding van de [opslag-BLOB](functions-bindings-storage-blob.md) en [TextWriter](/dotnet/api/system.io.textwriter) is een ondersteund type uitvoer binding.

### <a name="multiple-attribute-example"></a>Voor beeld van meerdere kenmerken

In het vorige voor beeld wordt de app-instelling voor het hoofd-opslag account van de functie-app opgehaald connection string (`AzureWebJobsStorage`). U kunt een aangepaste app-instelling opgeven die moet worden gebruikt voor het opslag account door de [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) toe te voegen en de kenmerk matrix door te geven aan `BindAsync<T>()`. Gebruik een `Binder` para meter, niet `IBinder`.  Bijvoorbeeld:

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
> [Meer informatie over Best Practices for Azure Functions](functions-best-practices.md)
