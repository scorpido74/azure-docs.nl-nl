---
title: Aangepaste beschikbaarheids tests maken en uitvoeren met behulp van Azure Functions
description: In dit document wordt beschreven hoe u een Azure-functie maakt met TrackAvailability () die regel matig wordt uitgevoerd op basis van de configuratie gegeven in de functie Timer trigger. De resultaten van deze test worden verzonden naar uw Application Insights-resource, waar u de gegevens van beschikbaarheids resultaten kunt opvragen en waarschuwen. Aangepaste tests bieden u de mogelijkheid om complexere beschikbaarheids tests te schrijven dan mogelijk is met behulp van de portal-gebruikers interface, een app te bewaken in uw Azure VNET, het eindpunt adres te wijzigen of een beschikbaarheids test te maken als deze niet beschikbaar is in uw regio.
services: application-insights
documentationcenter: ''
author: morgangrobin
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: mogrobin
ms.openlocfilehash: 38a83169a7d1ffa03416f5947ada703bcba5017a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301371"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Aangepaste beschikbaarheids tests maken en uitvoeren met behulp van Azure Functions

In dit artikel wordt beschreven hoe u een Azure-functie maakt met TrackAvailability () die regel matig wordt uitgevoerd op basis van de configuratie die is opgegeven in de functie Timer trigger. De resultaten van deze test worden verzonden naar uw Application Insights-resource, waar u de gegevens van beschikbaarheids resultaten kunt opvragen en waarschuwen. Zo kunt u aangepaste tests maken die vergelijkbaar zijn met wat u kunt doen met behulp van [beschikbaarheids controle](../../azure-monitor/app/monitor-web-app-availability.md) in de portal. Aangepaste tests bieden u de mogelijkheid om complexere beschikbaarheids tests te schrijven dan mogelijk is met behulp van de portal-gebruikers interface, een app te bewaken in uw Azure VNET, het eindpunt adres te wijzigen of een beschikbaarheids test te maken, zelfs als deze functie niet beschikbaar is in uw regio.


## <a name="create-timer-triggered-function"></a>Door timer geactiveerde functie maken

- Als u een Application Insights resource hebt:
    - Azure Functions maakt standaard een Application Insights resource, maar als u een van de al gemaakte resources wilt gebruiken, moet u opgeven dat tijdens het maken.
    - Volg de instructies voor het [maken van een Azure functions resource en timer geactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voor opschonen) met de volgende opties.
        -  Klik op het gedeelte Application Insights voordat u **maken**selecteert.

            ![ Een Azure Functions-app maken met uw eigen app Insights-resource](media/availability-azure-functions/create-function-app.png)

        - Klik op **bestaande resource selecteren** en typ de naam van uw resource. Selecteer **Toep assen**

            ![Bestaande Application Insights resource selecteren](media/availability-azure-functions/app-insights-resource.png)

        - Selecteer **Maken**
- Als u nog geen Application Insights resource hebt gemaakt voor de door de timer geactiveerde functie:
    - Wanneer u uw Azure Functions-toepassing maakt, wordt er standaard een Application Insights resource voor u gemaakt.
    - Volg de instructies voor het [maken van een Azure functions resource en timer geactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voor opschonen).

## <a name="sample-code"></a>Voorbeeldcode

Kopieer de onderstaande code naar het bestand run. CSX (de vooraf bestaande code wordt vervangen). Als u dit wilt doen, gaat u naar uw Azure Functions-toepassing en selecteert u de timer functie aan de linkerkant.

![Azure function run. CSX in Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Voor het eindpunt adres gebruikt u het volgende: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Tenzij uw resource zich in een regio bevindt als Azure Government of Azure China in dat geval raadpleegt u dit artikel over [het overschrijven van de standaard eindpunten](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) en het juiste eind punt van het telemetrie-kanaal voor uw regio selecteren.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");

    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }

    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";

    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

Selecteer aan de rechter kant onder bestanden weer geven de optie **toevoegen**. Roep de nieuwe bestands **functie. proj** aan met de volgende configuratie.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![Klik aan de rechter kant op toevoegen. Noem de bestands functie. proj](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Beschik baarheid controleren

Om ervoor te zorgen dat alles werkt, kunt u de grafiek bekijken op het tabblad Beschik baarheid van uw Application Insights-resource.

![Tabblad Beschik baarheid met succes volle resultaten](media/availability-azure-functions/availtab.png)

Wanneer u uw test instelt met behulp van Azure Functions ziet u dat in tegens telling tot het gebruik van **test toevoegen** op het tabblad Beschik baarheid, de naam van uw test niet wordt weer gegeven en u er geen interactie mee kunt doen. De resultaten worden gevisualiseerd, maar er wordt een overzichts weergave weer gegeven in plaats van de gedetailleerde weer gave die u krijgt wanneer u een beschikbaarheids test maakt via de portal.

Als u de details van de end-to-end-trans actie wilt bekijken, selecteert u **geslaagd** of **mislukt** onder inzoomen en selecteert u vervolgens een voor beeld. U kunt ook naar de end-to-end-transactie details gaan door een gegevens punt in de grafiek te selecteren.

![Een voor beeld van een beschikbaarheids test selecteren](media/availability-azure-functions/sample.png)

![Details van end-to-end-trans acties](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Query in Logboeken (analyse)

U kunt Logboeken (analyse) gebruiken om de beschikbaarheids resultaten, afhankelijkheden en meer weer te geven. Ga voor meer informatie over Logboeken naar het [overzicht van logboek query's](../../azure-monitor/log-query/log-query-overview.md).

![Beschikbaarheids resultaten](media/availability-azure-functions/availabilityresults.png)

![Afhankelijkheden](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingskaart](../../azure-monitor/app/app-map.md)
- [Transactie diagnostiek](../../azure-monitor/app/transaction-diagnostics.md)
