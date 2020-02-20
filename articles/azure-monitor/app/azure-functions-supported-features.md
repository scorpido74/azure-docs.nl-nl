---
title: Azure-toepassing Insights-Azure Functions ondersteunde functies
description: Ondersteunde functies Application Insights voor Azure Functions
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: ce8fcc827afdf0102a459f2c6b58db45eff72fa0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467384"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Application Insights voor Azure Functions ondersteunde functies

Azure Functions biedt [ingebouwde integratie](../../azure-functions/functions-monitoring.md) met Application Insights, die beschikbaar is via de ILogger-interface. Hieronder ziet u de lijst met momenteel ondersteunde functies. Raadpleeg de hand leiding van Azure Functions om [aan](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)de slag te gaan.

Zie [hier](../../azure-functions/functions-versions.md)voor meer informatie over runtime versies.

## <a name="supported-features"></a>Ondersteunde functies

| Azure Functions                       | V1                | V2 & v3   | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Automatische verzameling van**        |                 |                   |               
| &bull; aanvragen                     | Ja             | Ja               | 
| &bull; uitzonde ringen                   | Ja             | Ja               | 
| Prestatie meter items &bull;         | Ja             | Ja               |
| &bull; afhankelijkheden                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Ja               | 
| | | | 
| **Ondersteunde functies**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash; beveiligd-besturings kanaal|                 | Ja               | 
| &bull; steekproef                     | Ja             | Ja               | 
| &bull; heartbeats                   |                 | Ja               | 
| | | | 
| **Verband**                       |                   |                   |               
| &bull; ServiceBus                     |                   | Ja               | 
| &bull; EventHub                       |                   | Ja               | 
| | | | 
| **Configureer bare**                      |                   |                   |           
| &bull;volledig geconfigureerd.<br/>Zie [Azure functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) voor instructies.<br/>Zie [ASP.net core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) voor alle opties.               |                   | Ja                   | 


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
