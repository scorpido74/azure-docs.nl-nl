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
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: e31f3dc166177ce36289b97d85d90a9582c9cae5
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375987"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Afhankelijkheids injectie gebruiken in .NET Azure Functions

Azure Functions ondersteunt het ' Dependency Injection (DI)-ontwerp patroon voor software. Dit is een techniek voor het bezorgen [van een inversie van het besturings element (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) tussen klassen en hun afhankelijkheden.

Azure Functions bouwt voort op de functies van de ASP.NET Core voor het invoegen van afhankelijkheden. Het is raadzaam om de services, levens duur en ontwerp patronen van [ASP.net core afhankelijkheids injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) te herkennen voordat u de functie di-functies in een Azure functions-app kunt gebruiken.

Ondersteuning voor het invoegen van afhankelijkheden begint met Azure Functions 2. x.

## <a name="prerequisites"></a>Vereisten

Voordat u afhankelijkheids injectie kunt gebruiken, moet u de volgende NuGet-pakketten installeren:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Micro soft. net. SDK. functions pakket](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) versie 1.0.28 of hoger

- Optioneel: [Micro soft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) is alleen vereist voor het registreren van httpclient maakt bij het opstarten

## <a name="register-services"></a>Services registreren

Als u services wilt registreren, kunt u een methode maken om onderdelen te configureren en `IFunctionsHostBuilder` toe te voegen aan een exemplaar.  Op de Azure functions-host wordt een `IFunctionsHostBuilder` exemplaar van gemaakt en direct in uw methode door gegeven.

Als u de methode wilt registreren, `FunctionsStartup` voegt u het kenmerk assembly toe dat de type naam specificeert die wordt gebruikt tijdens het opstarten. Daarnaast verwijst code naar een prerelease van [micro soft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) op Nuget.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Geïnjecteerde afhankelijkheden gebruiken

ASP.NET Core maakt gebruik van constructor-injecties om uw afhankelijkheden beschikbaar te maken voor uw functie. In het volgende voor beeld ziet u `IMyService` hoe `HttpClient` de en-afhankelijkheden worden geïnjecteerd in een functie die door http wordt geactiveerd. 

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

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

Het gebruik van constructor-injectie houdt in dat u geen statische functies moet gebruiken als u gebruik wilt maken van afhankelijkheids injectie. Raadpleeg voor Cosmos-client [Dit](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/CodeSamples/AzureFunctions/AzureFunctionsCosmosClient.cs).

## <a name="service-lifetimes"></a>Levens duur van service

Azure Functions-apps bieden dezelfde service levensduur als [ASP.net-injectie](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): tijdelijk, bereik en Singleton.

In een functions-app komt de levens duur van een scoped service overeen met de levens duur van de functie. Services met een bereik worden eenmaal per uitvoering gemaakt. Latere aanvragen voor die service tijdens het uitvoeren van het bestaande service-exemplaar opnieuw gebruiken. De levens duur van een singleton-service komt overeen met de levens duur van de host en wordt opnieuw gebruikt voor het uitvoeren van functies voor dat exemplaar.

Services voor Singleton-levens duur worden aanbevolen voor verbindingen en clients `SqlConnection`, `CloudBlobClient`bijvoorbeeld, `HttpClient` of exemplaren.

Bekijk of down load een voor [beeld van de verschillende levens duur](https://aka.ms/functions/di-sample) van de service op github.

## <a name="logging-services"></a>Logboek registratie Services

Als u uw eigen logboek registratie provider nodig hebt, kunt u het beste een `ILoggerProvider` exemplaar registreren. Application Insights wordt automatisch toegevoegd door Azure Functions.

> [!WARNING]
> Voeg niets toe `AddApplicationInsightsTelemetry()` aan de services-verzameling omdat hiermee services worden geregistreerd die conflicteren met services die worden meegeleverd door de omgeving.

## <a name="function-app-provided-services"></a>Services die door de functie-app worden meegeleverd

De functie-host registreert veel services. De volgende services zijn veilig als afhankelijkheid in uw toepassing:

|Servicetype|Dood|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Runtime configuratie|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Verantwoordelijk voor het opgeven van de ID van het exemplaar van de host|

Als er andere services zijn waarvoor u een afhankelijkheid wilt maken, [maakt u een probleem en stelt u deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Host-Services overschrijven

Het overschrijven van services die door de host worden geboden, wordt momenteel niet ondersteund.  Als er services zijn die u wilt overschrijven, kunt u [een probleem maken en deze Voorst Ellen op github](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie:

- [Uw functie-app bewaken](functions-monitoring.md)
- [Aanbevolen procedures voor functies](functions-best-practices.md)
