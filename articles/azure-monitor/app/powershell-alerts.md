---
title: Power shell gebruiken om waarschuwingen in te stellen in Application Insights | Microsoft Docs
description: De configuratie van Application Insights automatiseren om e-mails over metrische wijzigingen te ontvangen.
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 00212aa8783a6bfc8e46d325a882781e33b7de51
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117174"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>PowerShell gebruiken om waarschuwingen in te stellen in Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt de configuratie van [waarschuwingen](../../azure-monitor/platform/alerts-log.md) in [Application Insights](../../azure-monitor/app/app-insights-overview.md)automatiseren.

Daarnaast kunt u [webhooks instellen om uw reactie op een waarschuwing te automatiseren](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Als u op hetzelfde moment resources en waarschuwingen wilt maken, kunt u overwegen [een Azure Resource Manager sjabloon te gebruiken](powershell.md).

## <a name="one-time-setup"></a>Eenmalige installatie
Als u Power shell nog niet eerder hebt gebruikt met uw Azure-abonnement:

Installeer de Azure Power shell-module op de computer waarop u de scripts wilt uitvoeren.

* Installeer het [installatie programma voor het micro soft-webplatform (V5 of hoger)](https://www.microsoft.com/web/downloads/platform.aspx).
* Gebruiken om Microsoft Azure Power shell te installeren

## <a name="connect-to-azure"></a>Verbinding maken met Azure
Start Azure PowerShell en [Maak verbinding met uw abonnement](/powershell/azure/):

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Waarschuwingen ophalen

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Waarschuwing toevoegen

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>Voorbeeld 1
Stuur mij een e-mail als het antwoord van de server op HTTP-aanvragen, gemiddeld meer dan vijf minuten, lager is dan 1 seconde. Mijn Application Insights resource wordt IceCreamWebApp genoemd en bevindt zich in de resource groep fabrikam. Ik ben de eigenaar van het Azure-abonnement.

De GUID is de abonnements-ID (niet de instrumentatie sleutel van de toepassing).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>Voorbeeld 2
Ik heb een toepassing waarin ik [TrackMetric ()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) gebruik om een metrische waarde met de naam ' salesPerHour ' te rapporteren. Een e-mail verzenden naar mijn collega's als "salesPerHour" onder 100 daalt, gemiddeld meer dan 24 uur.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

Dezelfde regel kan worden gebruikt voor de metrische gegevens die worden gerapporteerd met behulp van de [para meter meet](../../azure-monitor/app/api-custom-events-metrics.md#properties) van een andere tracerings aanroep, zoals track event of trackPageView.

## <a name="metric-names"></a>Metrische namen
| Naam van meetwaarde | Scherm naam | Beschrijving |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Browseruitzonderingen |Aantal niet-onderschepte uitzonde ringen dat in de browser wordt gegenereerd. |
| `basicExceptionServer.count` |Server uitzonderingen |Aantal niet-verwerkte uitzonde ringen die door de app worden gegenereerd |
| `clientPerformance.clientProcess.value` |Verwerkings tijd van client |Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt. |
| `clientPerformance.networkConnection.value` |Netwerk verbindings tijd voor laden van pagina |De tijd die de browser nodig heeft om verbinding te maken met het netwerk. Kan 0 zijn als de cache is opgeslagen. |
| `clientPerformance.receiveRequest.value` |Reactie tijd van ontvangst |Tijd tussen de verzen ding van de browser om te beginnen met het ontvangen van het antwoord. |
| `clientPerformance.sendRequest.value` |Aanvraag tijd verzenden |De tijd die nodig is voor het verzenden van een aanvraag door de browser. |
| `clientPerformance.total.value` |Laad tijd van browser pagina |Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen. |
| `performanceCounter.available_bytes.value` |Beschikbaar geheugen |Fysiek geheugen dat direct beschikbaar is voor een proces of voor systeem gebruik. |
| `performanceCounter.io_data_bytes_per_sec.value` |I/o-frequentie van processen |Totaal aantal in bestanden, netwerk en apparaten gelezen en geschreven bytes per seconde. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |uitzonderings frequentie |Uitzonde ringen die per seconde worden gegenereerd. |
| `performanceCounter.percentage_processor_time.value` |CPU verwerken |Het percentage verstreken tijd van alle proces threads dat door de processor wordt gebruikt voor het uitvoeren van instructies voor het proces toepassingen. |
| `performanceCounter.percentage_processor_total.value` |Processor tijd |Het percentage tijd dat de processor spendeert aan niet-inactieve threads. |
| `performanceCounter.process_private_bytes.value` |Privé-bytes verwerken |Geheugen dat exclusief wordt toegewezen aan de processen van de bewaakte toepassing. |
| `performanceCounter.request_execution_time.value` |Uitvoerings tijd van de ASP.NET-aanvraag |Uitvoerings tijd van de meest recente aanvraag. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET aanvragen in uitvoerings wachtrij |Lengte van de wachtrij voor toepassings aanvragen. |
| `performanceCounter.requests_per_sec.value` |Frequentie van ASP.NET-aanvragen |Het aantal aanvragen voor de toepassing per seconde van ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Afhankelijkheids fouten |Aantal mislukte aanroepen van de server toepassing naar externe bronnen. |
| `request.duration` |Server reactietijd |Tijd tussen het ontvangen van een HTTP-aanvraag en het volt ooien van het verzenden van het antwoord. |
| `request.rate` |Aanvraag frequentie |Het aantal aanvragen per seconde voor de toepassing. |
| `requestFailed.count` |Mislukte aanvragen |Aantal HTTP-aanvragen dat heeft geresulteerd in een reactie code >= 400 |
| `view.count` |Pagina weergaven |Aantal client gebruikers aanvragen voor een webpagina. Synthetisch verkeer wordt gefilterd. |
| {uw aangepaste metrische naam} |{Uw metrische naam} |Uw metrische waarde die wordt gerapporteerd door [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) of in de [meet parameter van een tracerings aanroep](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

De metrische gegevens worden verzonden door verschillende telemetrie-modules:

| Metrische groep | Verzamel module |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>weergave |[Browser-Java script](../../azure-monitor/app/javascript.md) |
| Performance Counter |[Prestaties](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Afhankelijkheid](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| schot<br/>requestFailed |[Server aanvraag](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
U kunt [uw reactie op een waarschuwing automatiseren](../../azure-monitor/platform/alerts-webhooks.md). Azure roept een webadres van uw keuze aan wanneer een waarschuwing wordt gegenereerd.

## <a name="see-also"></a>Zie tevens
* [Script voor het configureren van Application Insights](./create-new-resource.md#creating-a-resource-automatically)
* [Application Insights-en web-test resources maken op basis van sjablonen](powershell.md)
* [Koppelings Microsoft Azure Diagnostics automatiseren voor Application Insights](powershell-azure-diagnostics.md)
* [Uw reactie op een waarschuwing automatiseren](../../azure-monitor/platform/alerts-webhooks.md)
