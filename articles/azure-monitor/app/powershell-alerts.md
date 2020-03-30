---
title: Powershell gebruiken om waarschuwingen in te stellen in Application Insights | Microsoft Documenten
description: Automatiseer de configuratie van Application Insights om e-mails over metrische wijzigingen te ontvangen.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669842"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>PowerShell gebruiken om waarschuwingen in te stellen in Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U de configuratie van [waarschuwingen](../../azure-monitor/app/alerts.md) automatiseren in [Application Insights.](../../azure-monitor/app/app-insights-overview.md)

Bovendien u [webhooks instellen om uw reactie op een waarschuwing te automatiseren.](../../azure-monitor/platform/alerts-webhooks.md)

> [!NOTE]
> Als u tegelijkertijd resources en waarschuwingen wilt maken, u overwegen [een azure resource manager-sjabloon](powershell.md)te gebruiken.

## <a name="one-time-setup"></a>Eenmalige installatie
Als u PowerShell nog niet eerder met uw Azure-abonnement hebt gebruikt:

Installeer de Azure Powershell-module op de machine waar u de scripts wilt uitvoeren.

* [Microsoft Web Platform Installer (v5 of hoger) installeren.](https://www.microsoft.com/web/downloads/platform.aspx)
* Microsoft Azure Powershell installeren

## <a name="connect-to-azure"></a>Verbinding maken met Azure
Start Azure PowerShell en [maak verbinding met uw abonnement:](/powershell/azure/overview)

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Ontvang waarschuwingen
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Waarschuwing toevoegen
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>Voorbeeld 1
Stuur me een e-mail als het antwoord van de server op HTTP-verzoeken, gemiddeld meer dan 5 minuten, langzamer is dan 1 seconde. My Application Insights resource heet IceCreamWebApp, en het is in resource groep Fabrikam. Ik ben de eigenaar van het Azure-abonnement.

De GUID is de abonnements-ID (niet de instrumentatiesleutel van de toepassing).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Voorbeeld 2
Ik heb een toepassing waarin ik [TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) gebruik om een statistiek met de naam 'salesPerHour' te rapporteren. Stuur een e-mail naar mijn collega's als "salesPerHour" daalt tot onder de 100, gemiddeld meer dan 24 uur.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Dezelfde regel kan worden gebruikt voor de statistiek die wordt gerapporteerd met behulp van de [meetparameter](../../azure-monitor/app/api-custom-events-metrics.md#properties) van een andere trackingcall, zoals TrackEvent of trackPageView.

## <a name="metric-names"></a>Metrische namen
| Naam van meetwaarde | Schermnaam | Beschrijving |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Browseruitzonderingen |Aantal niet-gevangen uitzonderingen die in de browser worden gegooid. |
| `basicExceptionServer.count` |Serveruitzonderingen |Aantal niet-afgehandelde uitzonderingen die door de app worden gegooid |
| `clientPerformance.clientProcess.value` |Verwerkingstijd van de client |Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Async-aanvragen worden mogelijk nog steeds verwerkt. |
| `clientPerformance.networkConnection.value` |Verbindingstijd van het laden van pagina's |Tijd die de browser nodig heeft om verbinding te maken met het netwerk. Kan 0 zijn als ze in de cache zijn opgeslagen. |
| `clientPerformance.receiveRequest.value` |Ontvangstvan de reactietijd |Tijd tussen browser verzenden verzoek om te beginnen met een reactie te ontvangen. |
| `clientPerformance.sendRequest.value` |Aanvraagtijd verzenden |Tijd die de browser nodig heeft om een verzoek te verzenden. |
| `clientPerformance.total.value` |Laadtijd van browserpagina's |Tijd vanaf gebruikersaanvraag tot DOM, stylesheets, scripts en afbeeldingen worden geladen. |
| `performanceCounter.available_bytes.value` |Beschikbaar geheugen |Fysiek geheugen onmiddellijk beschikbaar voor een proces of voor systeemgebruik. |
| `performanceCounter.io_data_bytes_per_sec.value` |Io-snelheid verwerken |Totaal aantal bytes per seconde gelezen en geschreven naar bestanden, netwerk en apparaten. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |uitzonderingspercentage |Uitzonderingen gegooid per seconde. |
| `performanceCounter.percentage_processor_time.value` |CPU verwerken |Het percentage verstreken tijd van alle procesthreads die door de processor wordt gebruikt voor uitvoeringsinstructies voor het aanvraagproces. |
| `performanceCounter.percentage_processor_total.value` |Processortijd |Het percentage tijd dat de processor doorbrengt in niet-niet-niet-actieve threads. |
| `performanceCounter.process_private_bytes.value` |Privébytes verwerken |Geheugen uitsluitend toegewezen aan de processen van de bewaakte toepassing. |
| `performanceCounter.request_execution_time.value` |ASP.NET aanvraag uitvoeringstijd |Uitvoeringstijd van het meest recente verzoek. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET aanvragen in uitvoeringswachtrij |Lengte van de wachtrij voor toepassingsaanvragen. |
| `performanceCounter.requests_per_sec.value` |ASP.NET aanvraagtarief |Snelheid van alle aanvragen voor de toepassing per seconde vanaf ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Afhankelijkheidsfouten |Aantal mislukte oproepen van de servertoepassing naar externe bronnen. |
| `request.duration` |Serverresponstijd |Tijd tussen het ontvangen van een HTTP-aanvraag en het voltooien van het verzenden van het antwoord. |
| `request.rate` |Aanvraagtarief |Snelheid van alle aanvragen voor de toepassing per seconde. |
| `requestFailed.count` |Mislukte aanvragen |Aantal HTTP-aanvragen dat resulteerde in een antwoordcode >= 400 |
| `view.count` |Paginaweergaven |Aantal aanvragen van clientgebruikers voor een webpagina. Synthetisch verkeer wordt uitgefilterd. |
| {uw aangepaste metrische naam} |{Uw metrische naam} |Uw metrische waarde gerapporteerd door [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) of in de [parameter metingen van een trackingcall](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

De statistieken worden verzonden door verschillende telemetriemodules:

| Metrische groep | Collector-module |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>weergave |[Browser JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Prestaties](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| afhankelijkheid op afstandMislukt |[Afhankelijkheid](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Verzoek<br/>aanvragen mislukt |[Serveraanvraag](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
U [uw reactie op een waarschuwing automatiseren.](../../azure-monitor/platform/alerts-webhooks.md) Azure belt een webadres naar keuze wanneer een waarschuwing wordt gegenereerd.

## <a name="see-also"></a>Zie ook
* [Script om Toepassingsinzichten te configureren](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Toepassingsinzichten en webtestbronnen maken op basis van sjablonen](powershell.md)
* [Microsoft Azure Diagnostics koppelen aan toepassingsinzichten automatiseren](powershell-azure-diagnostics.md)
* [Automatiseer uw reactie op een waarschuwing](../../azure-monitor/platform/alerts-webhooks.md)
