---
title: Duurzame functies uitvoeren als WebJobs - Azure
description: Meer informatie over het coderen en configureren van duurzame functies om uit te voeren in WebJobs met behulp van de WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232746"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Duurzame functies uitvoeren als WebJobs

Standaard gebruikt Duurzame functies de runtime van Azure-functies om orkestraties te hosten. Er kunnen echter bepaalde scenario's zijn waarin u meer controle nodig hebt over de code die naar gebeurtenissen luistert. In dit artikel ziet u hoe u uw orkestratie implementeert met behulp van de WebJobs SDK. Zie [Functies en WebJobs vergelijken](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)voor een meer gedetailleerde vergelijking tussen functies en WebJobs.

[Azure-functies](../functions-overview.md) en de extensie [Duurzame functies](durable-functions-overview.md) zijn gebouwd op de [WebJobs SDK.](../../app-service/webjobs-sdk-how-to.md) De taakhost in de WebJobs SDK is de runtime in Azure-functies. Als u gedrag moet beheren op manieren die niet mogelijk zijn in Azure-functies, u duurzame functies ontwikkelen en uitvoeren met behulp van de WebJobs SDK zelf.

In versie 3.x van de WebJobs SDK `IHost`is de host een implementatie `JobHost` van , en in versie 2.x gebruikt u het object.

Het voorbeeld van chaining Sustainable Functions is beschikbaar in een WebJobs SDK 2.x-versie: download of kloon de [opslagplaats duurzame functies](https://github.com/azure/azure-functions-durable-extension/)en ga naar de map *webjobssdk-chaining\\\\van samples.*

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de basisprincipes van de WebJobs SDK, C# klassebibliotheekontwikkeling voor Azure-functies en Duurzame functies. Als u een inleiding tot deze onderwerpen nodig hebt, raadpleegt u de volgende bronnen:

* [Aan de slag met de WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Uw eerste functie maken met Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Ga als volgt te werk om de stappen in dit artikel uit te voeren:

* [Installeer Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) met de **Azure-ontwikkelworkload.**

  Als u visual studio al hebt, maar die werkbelasting niet hebt, voegt u de werkbelasting toe door**Hulpmiddelen en functies voor extra's** **Tools** > opdoen te selecteren.

  (U in plaats daarvan [Visual Studio-code](https://code.visualstudio.com/) gebruiken, maar sommige instructies zijn specifiek voor Visual Studio.)

* Installeer en voer [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) versie 5.2 of hoger uit. Een alternatief is het bijwerken van het *App.config-bestand* met een Azure Storage-verbindingstekenreeks.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versies

In dit artikel wordt uitgelegd hoe u een WebJobs SDK 2.x-project ontwikkelt (gelijk aan Versie 1.x van Azure Functions). Zie [WebJobs SDK 3.x](#webjobs-sdk-3x) later in dit artikel voor informatie over versie 3.x.

## <a name="create-a-console-app"></a>Een console-app maken

Als u Duurzame functies als WebJobs wilt uitvoeren, moet u eerst een console-app maken. Een WebJobs SDK-project is slechts een console-appproject waarbij de juiste NuGet-pakketten zijn geïnstalleerd.

Selecteer windows **Classic Desktop** > **Console App (.NET Framework)** in het dialoogvenster Visual Studio **New Project** . In het projectbestand `TargetFrameworkVersion` moet `v4.6.1`de .

Visual Studio heeft ook een WebJob-projectsjabloon, die u gebruiken door **Cloud** > **Azure WebJob (.NET Framework) te**selecteren. Deze sjabloon installeert veel pakketten, waarvan u sommige mogelijk niet nodig hebt.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

U hebt NuGet-pakketten nodig voor de WebJobs SDK, kernbindingen, het logging-framework en de extensie Duurzame taak. Hier zijn **Package Manager Console** opdrachten voor deze pakketten, met de nieuwste stabiele versie nummers vanaf de datum waarop dit artikel is geschreven:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

U hebt ook logging providers nodig. Met de volgende opdrachten worden de `ConfigurationManager`Azure Application Insights-provider en de . Hiermee `ConfigurationManager` u de instrumentatiesleutel Application Insights ophalen via app-instellingen.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Met de volgende opdracht wordt de consoleprovider geïnstalleerd:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-code

Nadat je de console-app hebt gemaakt en de NuGet-pakketten hebt geïnstalleerd die je nodig hebt, ben je klaar voor duurzame functies. Dit doe je door jobhostcode te gebruiken.

Als u de extensie `UseDurableTask` Duurzame `JobHostConfiguration` functies wilt `Main` gebruiken, doet u een beroep op het object in uw methode:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Zie [host.json](../functions-host-json.md#durabletask)voor een `DurableTaskExtension` lijst met eigenschappen die u in het object instellen.

De `Main` methode is ook de plek om logging providers in te stellen. In het volgende voorbeeld worden de console- en Application Insights-providers geconfigureerd.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Duurzame functies in de context van WebJobs verschillen enigszins van duurzame functies in de context van Azure-functies. Het is belangrijk om bewust te zijn van de verschillen als u uw code schrijft.

De WebJobs SDK biedt geen ondersteuning voor de volgende Azure-functies:

* [FunctieNaam-kenmerk](#functionname-attribute)
* [HTTP-trigger](#http-trigger)
* [HTTP-beheer-API voor duurzame functies](#http-management-api)

### <a name="functionname-attribute"></a>FunctieNaam-kenmerk

In een WebJobs SDK-project is de methodenaam van een functie de functienaam. Het `FunctionName` kenmerk wordt alleen gebruikt in Azure-functies.

### <a name="http-trigger"></a>HTTP-trigger

De WebJobs SDK heeft geen HTTP-trigger. De orchestration-client van het voorbeeldproject maakt gebruik van een timertrigger:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP-beheer-API

Omdat er geen HTTP-trigger is, heeft de WebJobs SDK geen [HTTP-beheer-API.](durable-functions-http-api.md)

In een WebJobs SDK-project u methoden aanroepen voor het orchestration-clientobject, in plaats van door HTTP-aanvragen te verzenden. De volgende methoden komen overeen met de drie taken die u uitvoeren met de HTTP-beheer-API:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

De orchestration client functie in het voorbeeld project start de orchestrator `GetStatusAsync` functie, en gaat vervolgens in een lus die aanroept om de 2 seconden:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U hebt duurzame functies die zijn ingesteld om als WebJob uit te voeren en u hebt nu inzicht in hoe dit zal verschillen van het uitvoeren van duurzame functies als zelfstandige Azure-functies. Op dit punt kan het nuttig zijn om het in een voorbeeld te zien werken.

In deze sectie vindt u een overzicht van de wijze waarop het [voorbeeldproject moet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)worden uitgevoerd. Zie [Aan de slag met de WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)voor gedetailleerde instructies waarin wordt uitgelegd hoe u een WebJobs SDK-project lokaal uitvoeren en implementeren in een Azure WebJob.

### <a name="run-locally"></a>Lokaal uitvoeren

1. Controleer of de opslagemulator wordt uitgevoerd (zie [Vereisten).](#prerequisites)

1. Als u logboeken in Application Insights wilt zien wanneer u het project lokaal uitvoert:

    a. Maak een application insights-bron en gebruik **het** algemene app-type ervoor.

    b. Sla de instrumentatiesleutel op in het bestand *App.config.*

1. Voer het project uit.

### <a name="run-in-azure"></a>Uitvoeren in Azure

1. Maak een web-app en een opslagaccount.

1. Sla in de web-app de tekenreeks voor `AzureWebJobsStorage`de opslagverbinding op in een app-instelling met de naam .

1. Maak een application insights-bron en gebruik **het** algemene app-type ervoor.

1. Sla de instrumentatiesleutel op `APPINSIGHTS_INSTRUMENTATIONKEY`in een app-instelling met de naam .

1. Implementeren als webjob.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

In dit artikel wordt uitgelegd hoe u een WebJobs SDK 2.x-project ontwikkelen. Als u een [WebJobs SDK 3.x-project](../../app-service/webjobs-sdk-get-started.md) ontwikkelt, helpt deze sectie u de verschillen te begrijpen.

De belangrijkste wijziging die wordt geïntroduceerd is het gebruik van .NET Core in plaats van .NET Framework. Als u een WebJobs SDK 3.x-project wilt maken, zijn de instructies hetzelfde, met de volgende uitzonderingen:

1. Maak een .NET Core-console-app. Selecteer in het dialoogvenster Visual Studio **New Project** de optie **.NET Core** > **Console App (.NET Core)**. Het projectbestand `TargetFramework` geeft `netcoreapp2.x`aan dat dit is .

1. Kies de releaseversie WebJobs SDK 3.x van de volgende pakketten:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Stel de tekenreeks voor de opslagverbinding en de instrumentatiesleutel Application Insights in een *appsettings.json-bestand* in met behulp van het .NET Core-configuratiekader. Hier volgt een voorbeeld:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Wijzig `Main` hiervoor de methodecode. Hier volgt een voorbeeld:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [De WebJobs SDK gebruiken](../../app-service/webjobs-sdk-how-to.md)voor meer informatie over de WebJobs SDK.
