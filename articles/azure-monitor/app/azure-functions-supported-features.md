---
title: Azure Application Insights - Azure Functions Supported Features
description: Ondersteunde functies voor toepassingsinzichten voor Azure-functies
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655647"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Door Application Insights voor Azure Functions ondersteunde functies

Azure Functions biedt [ingebouwde integratie](../../azure-functions/functions-monitoring.md) met Application Insights, die beschikbaar is via de ILogger-interface. Hieronder vindt u de lijst met momenteel ondersteunde functies. De handleiding van Azure-functies voor [aan de slag .](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)

Zie [hier](../../azure-functions/functions-versions.md)voor meer informatie over versies met runtime-functies.

Zie [Afhankelijkheden](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)voor meer informatie over compatibele versies van Toepassingsinzichten.

## <a name="supported-features"></a>Ondersteunde functies

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **Automatische verzameling van**        |                 |                   |               
| &bull;Verzoeken                     | Ja             | Ja               | 
| &bull;Uitzonderingen                   | Ja             | Ja               | 
| &bull;Prestatiemeteritems         | Ja             | Ja               |
| &bull;Afhankelijkheden                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash;HTTP (HTTP)      |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;ServiceBus (ServiceBus)|                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;EventHub (EventHub)  |                 | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;Sql       |                 | Ja               | 
| | | | 
| **Ondersteunde functies**                |                   |                   |               
| &bull;QuickPulse/LiveMetrics       | Ja             | Ja               | 
| &nbsp;&nbsp;&nbsp;&mdash;Beveiligde besturingskanaal|                 | Ja               | 
| &bull;Bemonstering                     | Ja             | Ja               | 
| &bull;Heartbeats                   |                 | Ja               | 
| | | | 
| **Correlatie**                       |                   |                   |               
| &bull;ServiceBus (ServiceBus)                     |                   | Ja               | 
| &bull;EventHub (EventHub)                       |                   | Ja               | 
| | | | 
| **Configureerbare**                      |                   |                   |           
| &bull;Volledig configureerbaar.<br/>Zie [Azure-functies](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) voor instructies.<br/>Zie [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) voor alle opties.               |                   | Ja                   | 


## <a name="performance-counters"></a>Prestatiemeteritems

Automatische verzameling prestatiemeteritems werkt alleen Windows-machines.


## <a name="live-metrics--secure-control-channel"></a>Live statistieken & Secure Control Channel

De aangepaste filterscriteria die u opgeeft, worden teruggestuurd naar de component Live Metrics in de Application Insights SDK. De filters kunnen mogelijk gevoelige informatie bevatten, zoals klant-id's. U het kanaal beveiligen met een geheime API-sleutel. Zie [Het bedieningskanaal beveiligen](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) voor instructies.

## <a name="sampling"></a>Steekproeven

Azure Functions maakt Sampling standaard mogelijk in hun configuratie. Zie [Sampling configureren voor](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)meer informatie.

Als uw project afhankelijk is van de Application Insights SDK om handmatige telemetrietracking uit te voeren, u vreemd gedrag ervaren als uw bemonsteringsconfiguratie anders is dan de samplingconfiguratie van de functies. 

We raden u aan dezelfde configuratie te gebruiken als Functies. Met **Functies v2**u dezelfde configuratie krijgen met behulp van afhankelijkheidsinjectie in uw constructor:

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
