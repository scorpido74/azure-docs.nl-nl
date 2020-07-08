---
title: Azure-toepassing Insights-Azure Functions ondersteunde functies
description: Ondersteunde functies Application Insights voor Azure Functions
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77655647"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights voor Azure Functions ondersteunde functies

Azure Functions biedt [ingebouwde integratie](../../azure-functions/functions-monitoring.md) met Application Insights, die beschikbaar is via de ILogger-interface. Hieronder ziet u de lijst met momenteel ondersteunde functies. Raadpleeg de hand leiding van Azure Functions om [aan](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)de slag te gaan.

Zie [hier](../../azure-functions/functions-versions.md)voor meer informatie over runtime versies van functies.

Zie [afhankelijkheden](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)voor meer informatie over compatibele versies van Application Insights.

## <a name="supported-features"></a>Ondersteunde functies

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatische verzameling van**        |                 |                   |               
| &bull;Aanvragen                     | Ja             | Ja               | 
| &bull;Uitzonderingen                   | Ja             | Ja               | 
| &bull;Prestatie meter items         | Ja             | Ja               |
| &bull;Elkaar                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;HTTP      |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBus|                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub  |                 | Yes               | 
| &nbsp;&nbsp;&nbsp;&mdash;SQL       |                 | Yes               | 
| | | | 
| **Ondersteunde functies**                |                   |                   |               
| &bull;QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;Kanaal voor beveiligde besturing|                 | Yes               | 
| &bull;Proef                     | Ja             | Ja               | 
| &bull;Heartbeats                   |                 | Yes               | 
| | | | 
| **Correlatie**                       |                   |                   |               
| &bull;ServiceBus                     |                   | Yes               | 
| &bull;EventHub                       |                   | Yes               | 
| | | | 
| **Configureer bare**                      |                   |                   |           
| &bull;Volledig configureerbaar.<br/>Zie [Azure functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) voor instructies.<br/>Zie [ASP.net core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) voor alle opties.               |                   | Yes                   | 


## <a name="performance-counters"></a>Prestatiemeteritems

Het automatisch verzamelen van prestatie meter items werkt alleen op Windows-computers.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics & kanaal voor beveiligd beheer

De criteria voor aangepaste filters die u opgeeft, worden teruggestuurd naar het onderdeel Live Metrics in de SDK van Application Insights. De filters kunnen mogelijk gevoelige informatie bevatten, zoals customerIDs. U kunt het kanaal veilig maken met een geheime API-sleutel. Zie [het besturings kanaal beveiligen](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) voor instructies.

## <a name="sampling"></a>Steekproeven

Met Azure Functions worden steek proeven standaard in hun configuratie ingeschakeld. Zie [sampling configureren](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)voor meer informatie.

Als uw project afhankelijk is van de Application Insights SDK om hand matige telemetrie-tracering uit te voeren, kunnen er vreemde problemen optreden als uw sampling configuratie verschilt van de sampling configuratie van de functies. 

We raden u aan dezelfde configuratie als-functies te gebruiken. Met **functions v2**kunt u dezelfde configuratie ophalen met behulp van afhankelijkheids injectie in uw constructor:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
