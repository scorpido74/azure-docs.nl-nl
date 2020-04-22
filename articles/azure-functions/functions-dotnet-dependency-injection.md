---
title: Afhankelijkheidsinjectie gebruiken in .NET Azure Functions
description: Meer informatie over het gebruik van afhankelijkheidsinjectie voor het registreren en gebruiken van services in .NET-functies
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678445"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Afhankelijkheidsinjectie gebruiken in .NET Azure Functions

Azure Functions ondersteunt het modelontwerpvan de afhankelijkheidsinjectie (DI), een techniek om [Inversion of Control (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tussen klassen en hun afhankelijkheden te bereiken.

- Afhankelijkheidsinjectie in Azure-functies is gebaseerd op de functies .NET Core Dependency Injection. Vertrouwdheid met [.NET Core afhankelijkheidsinjectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) wordt aanbevolen. Er zijn verschillen in de manier waarop u afhankelijkheden overschrijft en hoe configuratiewaarden worden gelezen met Azure-functies op het beheerplan.

- Ondersteuning voor afhankelijkheidsinjectie begint met Azure Functions 2.x.

## <a name="prerequisites"></a>Vereisten

Voordat u een afhankelijkheidsinjectie gebruiken, moet u de volgende NuGet-pakketten installeren:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions pakket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versie 1.0.28 of hoger

## <a name="register-services"></a>Diensten registreren

Als u services wilt registreren, maakt u `IFunctionsHostBuilder` een methode om onderdelen te configureren en toe te voegen aan een instantie.  De Azure Functions-host `IFunctionsHostBuilder` maakt een exemplaar van en geeft deze rechtstreeks door aan uw methode.

Als u de methode `FunctionsStartup` wilt registreren, voegt u het kenmerk van de verzameling toe dat de typenaam opgeeft die tijdens het opstarten wordt gebruikt.

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

Een reeks registratiestappen die voor en na de runtime worden uitgevoerd, verwerkt de opstartklasse. Houd daarom rekening met de volgende items:

- *De opstartklasse is alleen bedoeld voor het instellen en registreren.* Vermijd het gebruik van services die tijdens het opstartproces zijn geregistreerd bij het opstarten. Probeer bijvoorbeeld niet een bericht in te loggen in een logger die wordt geregistreerd tijdens het opstarten. Dit punt van het registratieproces is te vroeg om uw diensten beschikbaar te maken voor gebruik. Nadat `Configure` de methode is uitgevoerd, blijft de runtime Van Functies extra afhankelijkheden registreren, wat van invloed kan zijn op de werking van uw services.

- *De afhankelijkheidsinjectiecontainer bevat alleen expliciet geregistreerde typen*. De enige services die beschikbaar zijn als `Configure` injecteerbare typen zijn wat er in de methode wordt ingesteld. Als gevolg hiervan zijn functiesspecifieke typen zoals `BindingContext` en `ExecutionContext` zijn ze niet beschikbaar tijdens de installatie of als injecteerbare typen.

## <a name="use-injected-dependencies"></a>Geïnjecteerde afhankelijkheden gebruiken

Constructor injectie wordt gebruikt om uw afhankelijkheden beschikbaar te maken in een functie. Het gebruik van constructeurinjectie vereist dat u geen statische klassen gebruikt.

In het volgende voorbeeld `IMyService` `HttpClient` wordt aangetoond hoe de afhankelijkheden en afhankelijkheden worden geïnjecteerd in een HTTP-geactiveerde functie. In dit voorbeeld wordt het [Microsoft.Extensions.Http-pakket](https://www.nuget.org/packages/Microsoft.Extensions.Http/) gebruikt dat nodig is om een `HttpClient` bij het opstarten te registreren.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
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

## <a name="service-lifetimes"></a>Levensduur van de levensduur

Azure Functions-apps bieden dezelfde servicelevensduur als [ASP.NET afhankelijkheidsinjectie.](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Voor een functie-app gedragen de verschillende levensduuren zich als volgt:

- **Tijdelijk:** tijdelijke services worden gemaakt op elk verzoek van de service.
- **Scoped**: De levensduur van de scope's komt overeen met de levensduur van de functieuitvoering. Scoped-services worden eenmaal per uitvoering gemaakt. Latere aanvragen voor die service tijdens de uitvoering hergebruiken het bestaande service-exemplaar.
- **Singleton**: De levensduur van de singleton-service komt overeen met de levensduur van de host en wordt opnieuw gebruikt voor functieuitvoeringen op die instantie. Singleton lifetime services worden aanbevolen voor verbindingen `SqlConnection` `HttpClient` en clients, bijvoorbeeld of instanties.

Bekijk of download een [voorbeeld van verschillende servicelevensduuren](https://aka.ms/functions/di-sample) op GitHub.

## <a name="logging-services"></a>Logging-services

Als u uw eigen logboekprovider nodig hebt, registreert u een aangepast type als `ILoggerProvider` instantie. Application Insights wordt automatisch toegevoegd door Azure Functions.

> [!WARNING]
> - Niet toevoegen `AddApplicationInsightsTelemetry()` aan de diensten collectie als het registreert diensten die in strijd zijn met diensten die door het milieu.
> - Registreer uw eigen `TelemetryConfiguration` `TelemetryClient` functionaliteit niet of gebruik de ingebouwde Application Insights-functionaliteit. Als u uw eigen `TelemetryClient` exemplaar moet configureren, `TelemetryConfiguration` maakt u er een via de geïnjecteerde zoals weergegeven in [Azure-functies van de monitor.](./functions-monitoring.md#version-2x-and-later-2)

### <a name="iloggert-and-iloggerfactory"></a>ILogger<T> en ILoggerFactory

De gastheer `ILogger<T>` zal `ILoggerFactory` injecteren en diensten in constructors.  Deze nieuwe logboekfilters worden echter standaard uit de functielogboeken gefilterd.  U moet het `host.json` bestand wijzigen om u aan te melden voor extra filters en categorieën.  In het volgende voorbeeld `ILogger<HttpTrigger>` wordt een met logboeken toegevoegd die door de host worden weergegeven.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

En `host.json` een bestand dat het logboekfilter toevoegt.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>Functie-app aangeboden diensten

De functiehost registreert veel services. De volgende services zijn veilig om als afhankelijkheid in uw toepassing te nemen:

|Servicetype|Levensduur|Beschrijving|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime-configuratie|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwoordelijk voor het verstrekken van de ID van de host instance|

Als er andere services zijn waar u afhankelijk van wilt zijn, [maakt u een probleem en stelt u deze voor op GitHub.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>Allesoverheersende hostdiensten

Overheersende diensten die door de host worden geleverd, worden momenteel niet ondersteund.  Als er services zijn die u wilt overschrijven, [maakt u een probleem en stelt u deze voor op GitHub.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>Werken met opties en instellingen

Waarden die zijn gedefinieerd in `IConfiguration` [app-instellingen](./functions-how-to-use-azure-function-app-settings.md#settings) zijn beschikbaar in een instantie, waarmee u app-instellingenwaarden in de opstartklasse lezen.

U waarden `IConfiguration` uit de instantie extraheren tot een aangepast type. Als u de waarden van de app-instellingen kopieert naar een aangepast type, u uw services eenvoudig testen door deze waarden injecteerbaar te maken. Instellingen die in de configuratie-instantie worden gelezen, moeten eenvoudige sleutel-/waardeparen zijn.

Houd rekening met de volgende klasse met een eigenschap met de naam consistent met een app-instelling:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

En `local.settings.json` een bestand dat de aangepaste instelling als volgt kan structureren:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Vanuit de `Startup.Configure` methode u waarden `IConfiguration` uit de instantie extraheren in uw aangepaste type met behulp van de volgende code:

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

Het `Bind` aanroepen van kopieën van waarden met overeenkomende eigenschapsnamen uit de configuratie naar de aangepaste instantie. De optieinstantie is nu beschikbaar in de IoC-container om in een functie te injecteren.

Het optieobject wordt in de functie geïnjecteerd `IOptions` als instantie van de generieke interface. Gebruik `Value` de eigenschap om toegang te krijgen tot de waarden in uw configuratie.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Raadpleeg [het patroon Opties in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) voor meer informatie over het werken met opties.

> [!WARNING]
> Probeer geen waarden te lezen uit bestanden zoals *local.settings.json* of *appsettings.{ milieu}.json* op het verbruiksplan. Waarden die worden gelezen uit deze bestanden met betrekking tot triggerverbindingen zijn niet beschikbaar omdat de app wordt geschaald omdat de hostinginfrastructuur geen toegang heeft tot de configuratie-informatie.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

- [Uw functie-app controleren](functions-monitoring.md)
- [Aanbevolen procedures voor functies](functions-best-practices.md)
