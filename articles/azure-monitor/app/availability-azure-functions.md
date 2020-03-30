---
title: Aangepaste beschikbaarheidstests maken en uitvoeren met Azure-functies
description: Dit document gaat over het maken van een Azure-functie met TrackAvailability() die periodiek wordt uitgevoerd volgens de configuratie in de functie TimerTrigger. De resultaten van deze test worden verzonden naar uw Application Insights-bron, waar u de gegevens over de beschikbaarheidsresultaten opvragen en erop waarschuwen. Met aangepaste tests u complexere beschikbaarheidstests schrijven dan mogelijk is via de portal-gebruikersinterface, een app in uw Azure VNET controleren, het eindpuntadres wijzigen of een beschikbaarheidstest maken als deze niet beschikbaar is in uw regio.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665796"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Aangepaste beschikbaarheidstests maken en uitvoeren met Azure-functies

In dit artikel wordt ingaan op het maken van een Azure-functie met TrackAvailability() die periodiek wordt uitgevoerd volgens de configuratie die is opgegeven in de functie TimerTrigger met uw eigen bedrijfslogica. De resultaten van deze test worden verzonden naar uw Application Insights-bron, waar u de gegevens over de beschikbaarheidsresultaten opvragen en erop waarschuwen. Hiermee u aangepaste tests maken die vergelijkbaar zijn met wat u doen via [Availability Monitoring](../../azure-monitor/app/monitor-web-app-availability.md) in de portal. Met aangepaste tests u complexere beschikbaarheidstests schrijven dan mogelijk is via de portal-gebruikersinterface, een app in uw Azure VNET controleren, het eindpuntadres wijzigen of een beschikbaarheidstest maken, zelfs als deze functie niet beschikbaar is in uw regio.

> [!NOTE]
> Dit voorbeeld is uitsluitend bedoeld om u de mechanica te laten zien van hoe de API-aanroep TrackAvailability() werkt binnen een Azure-functie. Niet hoe de onderliggende HTTP-testcode/bedrijfslogica te schrijven die nodig zou zijn om dit om te zetten in een volledig functionele beschikbaarheidstest. Als u dit voorbeeld standaard doorloopt, maakt u een beschikbaarheidstest die altijd een fout genereert.

## <a name="create-timer-triggered-function"></a>Timer geactiveerd, functie maken

- Als u een Resource Voor Application Insights hebt:
    - Standaard maakt Azure Functions een Application Insights-bron, maar als u een van uw reeds gemaakte resources wilt gebruiken, moet u dat tijdens het maken opgeven.
    - Volg de instructies voor het [maken van een Azure Functions-bron en timergeactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voordat u opruimt) met de volgende opties.
        -  Selecteer het tabblad **Controle** in de buurt van de bovenkant.

            ![ Een Azure Functions-app maken met uw eigen App Insights-bron](media/availability-azure-functions/create-function-app.png)

        - Selecteer het vervolgkeuzeveld Toepassingsstatistieken en typ of selecteer de naam van uw resource.

            ![Bestaande Application Insights-bron selecteren](media/availability-azure-functions/app-insights-resource.png)

        - Selecteer **Controleren + maken**
- Als u nog geen Application Insights-bron hebt gemaakt voor de geactiveerde functie voor uw timer:
    - Bij het maken van uw Azure Functions-toepassing wordt standaard een Application Insights-bron voor u gemaakt.
    - Volg de instructies voor het [maken van een Azure Functions-bron en timergeactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voordat u wordt opschonen).

## <a name="sample-code"></a>Voorbeeldcode

Kopieer de onderstaande code naar het run.csx-bestand (dit vervangt de reeds bestaande code). Ga hiervoor naar de toepassing Azure Functions en selecteer de functie timertrigger aan de linkerkant.

>[!div class="mx-imgBorder"]
>![Run.csx van azure-functie in Azure-portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Voor het eindpuntadres dat `EndpointAddress= https://dc.services.visualstudio.com/v2/track`u zou gebruiken: . Tenzij uw resource zich bevindt in een regio zoals Azure Government of Azure China, raadpleegt u dit artikel over [het overschrijven van de standaardeindpunten](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) en selecteert u het juiste eindpunt voor telemetriekanaal voor uw regio.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
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
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Selecteer rechts onder Bestanden weergeven de optie **Toevoegen**. Roep de nieuwe **bestandsfunctie.proj** aan met de volgende configuratie.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Voeg aan de rechterkant toe. Geef de bestandsfunctie.proj een naam](media/availability-azure-functions/addfile.png)

Selecteer rechts onder Bestanden weergeven de optie **Toevoegen**. Bel het nieuwe bestand **runAvailabilityTest.csx** met de volgende configuratie.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Beschikbaarheid controleren

Om er zeker van te zijn dat alles werkt, u de grafiek bekijken op het tabblad Beschikbaarheid van uw application insights-bron.

> [!NOTE]
> Als u uw eigen bedrijfslogica in runAvailabilityTest.csx dan hebt uitgevoerd zult u succesvolle resultaten zoals in screenshots hieronder zien, als u niet toen hebt gedaan zult u ontbroken resultaten zien.

>[!div class="mx-imgBorder"]
>![Tabblad Beschikbaarheid met succesvolle resultaten](media/availability-azure-functions/availtab.png)

Wanneer u uw test instelt met Azure-functies, zult u merken dat in tegenstelling tot het gebruik **van Add-test** op het tabblad Beschikbaarheid, de naam van uw test niet wordt weergegeven en dat u er geen interactie mee hebben. De resultaten worden gevisualiseerd, maar u krijgt een overzichtsweergave in plaats van dezelfde gedetailleerde weergave die u krijgt wanneer u een beschikbaarheidstest maakt via de portal.

Als u de end-to-end transactiegegevens wilt bekijken, selecteert u **Geslaagd** of **Mislukt** onder inzoomen en selecteert u een voorbeeld. U ook naar de end-to-end transactiedetails gaan door een gegevenspunt in de grafiek te selecteren.

>[!div class="mx-imgBorder"]
>![Een voorbeeldbeschikbaarheidstest selecteren](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![End-to-end transactiegegevens](media/availability-azure-functions/end-to-end.png)

Als je liep alles zoals is (zonder toevoeging van zakelijke logica), dan zul je zien dat de test is mislukt.

## <a name="query-in-logs-analytics"></a>Query in logboeken (Analytics)

U Logboeken(analyses) gebruiken om u beschikbaarheidsresultaten, afhankelijkheden en meer weer te geven. Ga voor meer informatie over Logboeken naar [Overzicht van querylogboeken](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Beschikbaarheidsresultaten](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Afhankelijkheden](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingskaart](../../azure-monitor/app/app-map.md)
- [Diagnostische gegevens voor transacties](../../azure-monitor/app/transaction-diagnostics.md)
