---
title: Aangepaste beschikbaarheids tests maken en uitvoeren met behulp van Azure Functions
description: In dit document wordt beschreven hoe u een Azure-functie maakt met TrackAvailability () die regel matig wordt uitgevoerd op basis van de configuratie gegeven in de functie Timer trigger. De resultaten van deze test worden verzonden naar uw Application Insights-resource, waar u de gegevens van beschikbaarheids resultaten kunt opvragen en waarschuwen. Aangepaste tests bieden u de mogelijkheid om complexere beschikbaarheids tests te schrijven dan mogelijk is met behulp van de portal-gebruikers interface, een app te bewaken in uw Azure VNET, het eindpunt adres te wijzigen of een beschikbaarheids test te maken als deze niet beschikbaar is in uw regio.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665796"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Aangepaste beschikbaarheids tests maken en uitvoeren met behulp van Azure Functions

In dit artikel wordt beschreven hoe u een Azure-functie maakt met TrackAvailability () die regel matig wordt uitgevoerd op basis van de configuratie die is opgegeven in de timer trigger-functie met uw eigen bedrijfs logica. De resultaten van deze test worden verzonden naar uw Application Insights-resource, waar u de gegevens van beschikbaarheids resultaten kunt opvragen en waarschuwen. Zo kunt u aangepaste tests maken die vergelijkbaar zijn met wat u kunt doen met behulp van [beschikbaarheids controle](../../azure-monitor/app/monitor-web-app-availability.md) in de portal. Aangepaste tests bieden u de mogelijkheid om complexere beschikbaarheids tests te schrijven dan mogelijk is met behulp van de portal-gebruikers interface, een app te bewaken in uw Azure VNET, het eindpunt adres te wijzigen of een beschikbaarheids test te maken, zelfs als deze functie niet beschikbaar is in uw regio.

> [!NOTE]
> Dit voor beeld is uitsluitend bedoeld om u te laten zien hoe de API-aanroep van TrackAvailability () in een Azure-functie werkt. Het schrijven van de onderliggende HTTP-test code/bedrijfs logica die is vereist om deze in te scha kelen in een volledig functionele beschikbaarheids test, is niet mogelijk. Als u dit voor beeld doorloopt, maakt u standaard een beschikbaarheids test waarbij er altijd een fout wordt gegenereerd.

## <a name="create-timer-triggered-function"></a>Door timer geactiveerde functie maken

- Als u een Application Insights resource hebt:
    - Azure Functions maakt standaard een Application Insights resource, maar als u een van de al gemaakte resources wilt gebruiken, moet u opgeven dat tijdens het maken.
    - Volg de instructies voor het [maken van een Azure functions resource en timer geactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voor opschonen) met de volgende opties.
        -  Selecteer het tabblad **controle** in de buurt van de bovenkant.

            ![ Een Azure Functions-app maken met uw eigen app Insights-resource](media/availability-azure-functions/create-function-app.png)

        - Selecteer de vervolg keuzelijst Application Insights en typ of selecteer de naam van uw resource.

            ![Bestaande Application Insights resource selecteren](media/availability-azure-functions/app-insights-resource.png)

        - Selecteer **controleren + maken**
- Als u nog geen Application Insights resource hebt gemaakt voor de door de timer geactiveerde functie:
    - Wanneer u uw Azure Functions-toepassing maakt, wordt er standaard een Application Insights resource voor u gemaakt.
    - Volg de instructies voor het [maken van een Azure functions resource en timer geactiveerde functie](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (stoppen voor opschonen).

## <a name="sample-code"></a>Voorbeeldcode

Kopieer de onderstaande code naar het bestand run. CSX (de vooraf bestaande code wordt vervangen). Als u dit wilt doen, gaat u naar uw Azure Functions-toepassing en selecteert u de timer functie aan de linkerkant.

>[!div class="mx-imgBorder"]
>![Azure function run. CSX in Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Voor het eindpunt adres gebruikt u: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Tenzij uw resource zich in een regio bevindt als Azure Government of Azure China in dat geval raadpleegt u dit artikel over [het overschrijven van de standaard eindpunten](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) en het juiste eind punt van het telemetrie-kanaal voor uw regio selecteren.

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

Selecteer aan de rechter kant onder bestanden weer geven de optie **toevoegen**. Roep de nieuwe bestands **functie. proj** aan met de volgende configuratie.

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
>Klik ![aan de rechter kant op toevoegen. Geef een naam op voor de bestands functie. proj](media/availability-azure-functions/addfile.png)

Selecteer aan de rechter kant onder bestanden weer geven de optie **toevoegen**. Roep het nieuwe bestand **runAvailabilityTest. CSX** aan met de volgende configuratie.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Beschik baarheid controleren

Om ervoor te zorgen dat alles werkt, kunt u de grafiek bekijken op het tabblad Beschik baarheid van uw Application Insights-resource.

> [!NOTE]
> Als u uw eigen bedrijfs logica in runAvailabilityTest. CSX hebt geïmplementeerd, ziet u succes volle resultaten als in de onderstaande scherm afbeeldingen. als dat niet het geval is, ziet u niet de resultaten.

>[!div class="mx-imgBorder"]
>![tabblad Beschik baarheid met succes volle resultaten](media/availability-azure-functions/availtab.png)

Wanneer u uw test instelt met behulp van Azure Functions ziet u dat in tegens telling tot het gebruik van **test toevoegen** op het tabblad Beschik baarheid, de naam van uw test niet wordt weer gegeven en u er geen interactie mee kunt doen. De resultaten worden gevisualiseerd, maar er wordt een overzichts weergave weer gegeven in plaats van de gedetailleerde weer gave die u krijgt wanneer u een beschikbaarheids test maakt via de portal.

Als u de details van de end-to-end-trans actie wilt bekijken, selecteert u **geslaagd** of **mislukt** onder inzoomen en selecteert u vervolgens een voor beeld. U kunt ook naar de end-to-end-transactie details gaan door een gegevens punt in de grafiek te selecteren.

>[!div class="mx-imgBorder"]
>![Selecteer een test voor de beschik baarheid van de voor beeld](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![end-to-end-transactie Details](media/availability-azure-functions/end-to-end.png)

Als u alles hebt uitgevoerd als is (zonder bedrijfs logica toe te voegen), ziet u dat de test is mislukt.

## <a name="query-in-logs-analytics"></a>Query in Logboeken (analyse)

U kunt Logboeken (analyse) gebruiken om de beschikbaarheids resultaten, afhankelijkheden en meer weer te geven. Ga voor meer informatie over Logboeken naar het [overzicht van logboek query's](../../azure-monitor/log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>resultaten van ![-Beschik baarheid](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Afhankelijkheden](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Volgende stappen

- [Toepassingskaart](../../azure-monitor/app/app-map.md)
- [Transactie diagnostiek](../../azure-monitor/app/transaction-diagnostics.md)
