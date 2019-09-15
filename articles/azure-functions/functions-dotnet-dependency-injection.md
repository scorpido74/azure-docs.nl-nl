---
title: Afhankelijkheids injectie gebruiken in .NET Azure Functions
description: Meer informatie over het gebruik van afhankelijkheids injectie voor het registreren en gebruiken van services in .NET functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, functions, serverloze architectuur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 09bcce6daf519c7d5e99c7c120064f5c8bb92475
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996881"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Afhankelijkheids injectie gebruiken in .NET Azure Functions

Azure Functions ondersteunt het ' Dependency Injection (DI)-ontwerp patroon voor software. Dit is een techniek voor het bezorgen [van een inversie van het besturings element (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tussen klassen en hun afhankelijkheden.

- Het invoegen van afhankelijkheden in Azure Functions is gebaseerd op de functies van de .NET core-invoeg injectie. Het is raadzaam om vertrouwd te raken met het [invoeren van .net core-afhankelijkheid](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) . Er zijn verschillen, maar in het overschrijven van afhankelijkheden en hoe configuratie waarden worden gelezen met Azure Functions op het verbruiks plan.

- Ondersteuning voor het invoegen van afhankelijkheden begint met Azure Functions 2. x.

## <a name="prerequisites"></a>Vereisten

Voordat u afhankelijkheids injectie kunt gebruiken, moet u de volgende NuGet-pakketten installeren:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Micro soft. net. SDK. functions pakket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versie 1.0.28 of hoger

## <a name="register-services"></a>Services registreren

Als u services wilt registreren, maakt u een methode om onderdelen te configureren `IFunctionsHostBuilder` en toe te voegen aan een exemplaar.  Op de Azure functions-host wordt een `IFunctionsHostBuilder` exemplaar van gemaakt en direct in uw methode door gegeven.

Als u de methode wilt registreren, `FunctionsStartup` voegt u het kenmerk assembly toe dat de type naam specificeert die wordt gebruikt tijdens het opstarten.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>Waarschuwingen

Een reeks registratie stappen die vóór en na de uitvoering van de runtime worden uitgevoerd, wordt de opstart klasse. Daarom houdt u de volgende punten in acht:

- *De klasse Startup is uitsluitend bedoeld voor installatie en registratie.* Vermijd het gebruik van services die bij het opstarten worden geregistreerd tijdens het opstart proces. Probeer bijvoorbeeld niet een bericht in een logboek te registreren dat tijdens het opstarten wordt geregistreerd. Dit moment van het registratie proces is te vroeg om uw services beschikbaar te maken voor gebruik. Nadat de `Configure` methode is uitgevoerd, blijft de runtime van functions extra afhankelijkheden registreren. Dit kan van invloed zijn op de werking van uw services.

- *De container voor de injectie van afhankelijkheden bevat alleen expliciet geregistreerde typen*. De enige services die beschikbaar zijn als Injecteer bare typen, zijn wat is `Configure` ingesteld in de-methode. Als gevolg hiervan zijn functies-specifieke typen zoals `BindingContext` en `ExecutionContext` niet beschikbaar tijdens de installatie of als Injecteer bare typen.

## <a name="use-injected-dependencies"></a>Geïnjecteerde afhankelijkheden gebruiken

Injectie van constructor wordt gebruikt om uw afhankelijkheden beschikbaar te maken in een functie. Voor het gebruik van een constructor-injectie moet u geen statische klassen gebruiken.

In het volgende voor beeld ziet u `IMyService` hoe `HttpClient` de en-afhankelijkheden worden geïnjecteerd in een functie die door http wordt geactiveerd. In dit voor beeld wordt het pakket [micro soft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) gebruikt `HttpClient` om een bij het opstarten te registreren.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>Levens duur van service

Azure Functions-apps bieden dezelfde levens duur van de service als [ASP.net-afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes). Voor een functions-app gedragen de verschillende levens duur van de service er als volgt uit:

- **Tijdelijk**: Tijdelijke services worden op elke aanvraag van de service gemaakt.
- **Bereik**: De levens duur van het bereik van de service komt overeen met de levens duur van de functies. Services met een bereik worden eenmaal per uitvoering gemaakt. Latere aanvragen voor die service tijdens het uitvoeren van het bestaande service-exemplaar opnieuw gebruiken.
- **Singleton**: De levens duur van de singleton-service komt overeen met de levens duur van de host en wordt opnieuw gebruikt voor het uitvoeren van functies voor dat exemplaar. Services voor de singleton-levens duur worden aanbevolen voor verbindingen en `SqlConnection` clients `HttpClient` , bijvoorbeeld of exemplaren.

Bekijk of down load een voor [beeld van de verschillende levens duur](https://aka.ms/functions/di-sample) van de service op github.

## <a name="logging-services"></a>Logboek registratie Services

Als u uw eigen registratie provider nodig hebt, moet u een aangepast type `ILoggerProvider` registreren als een exemplaar. Application Insights wordt automatisch toegevoegd door Azure Functions.

> [!WARNING]
> - Voeg niets toe `AddApplicationInsightsTelemetry()` aan de services-verzameling omdat hiermee services worden geregistreerd die conflicteren met services die worden meegeleverd door de omgeving.
> - Registreer uw eigen `TelemetryConfiguration` functies niet of `TelemetryClient` als u de ingebouwde Application Insights functionaliteit gebruikt.

## <a name="function-app-provided-services"></a>Services die door de functie-app worden meegeleverd

De functie-host registreert veel services. De volgende services zijn veilig als afhankelijkheid in uw toepassing:

|Servicetype|Dood|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime configuratie|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwoordelijk voor het opgeven van de ID van het exemplaar van de host|

Als er andere services zijn waarvoor u een afhankelijkheid wilt maken, [maakt u een probleem en stelt u deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Host-Services overschrijven

Het overschrijven van services die door de host worden geboden, wordt momenteel niet ondersteund.  Als er services zijn die u wilt overschrijven, kunt u [een probleem maken en deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Werken met opties en instellingen

De waarden die zijn gedefinieerd in de [app](./functions-how-to-use-azure-function-app-settings.md#settings) - `IConfiguration` instellingen zijn beschikbaar in een exemplaar, waarmee u waarden voor app-instellingen in de klasse Startup kunt lezen.

U kunt waarden uit het `IConfiguration` exemplaar extra heren in een aangepast type. Als u de waarden van de app-instellingen naar een aangepast type kopieert, kunt u uw services eenvoudig testen door deze waarden Injecteer te maken. Houd rekening met de volgende klasse die een eigenschap bevat met de naam consistent met een app-instelling.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Vanuit de `Startup.Configure` methode kunt u waarden uit het `IConfiguration` exemplaar in uw aangepaste type ophalen met de volgende code:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.Bind(settings);
                                           });
```

Het `Bind` aanroepen van kopieën waarden die overeenkomende eigenschapnamen van eigenschappen van de configuratie in het aangepaste exemplaar hebben. Het optie-exemplaar is nu beschikbaar in de IoC-container om in een functie te injecteren.

Het object Options wordt in de functie als een exemplaar van de algemene `IOptions` interface ingevoegd. Gebruik de `Value` eigenschap om toegang te krijgen tot de waarden die in uw configuratie zijn gevonden.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _service = service;
        _settings = options.Value;
    }
}
```

Raadpleeg het [Opties patroon in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) voor meer informatie over het werken met opties.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

- [Uw functie-app bewaken](functions-monitoring.md)
- [Aanbevolen procedures voor functies](functions-best-practices.md)
