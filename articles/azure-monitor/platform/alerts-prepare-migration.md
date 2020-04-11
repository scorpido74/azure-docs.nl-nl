---
title: Logische apps bijwerken & runbooks voor waarschuwingen migratie
description: Meer informatie over het wijzigen van uw webhooks, logische apps en runbooks ter voorbereiding op vrijwillige migratie.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: f31fcc07bed0287c2f86ca4fe52bf02a2a1d2a71
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114412"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Uw logische apps en runbooks voorbereiden voor de migratie van klassieke waarschuwingsregels

Zoals [eerder aangekondigd](monitoring-classic-retirement.md), worden klassieke waarschuwingen in Azure Monitor in september 2019 (oorspronkelijk juli 2019) in gebruik genomen. Een migratietool is beschikbaar in de Azure-portal voor klanten die klassieke waarschuwingsregels gebruiken en die zelf migratie willen activeren.

> [!NOTE]
> Vanwege vertraging bij de uitrol van migratietool is de pensioendatum voor klassieke waarschuwingen verlengd tot 31 augustus 2019 vanaf de oorspronkelijk aangekondigde datum van 30 juni 2019.

Als u ervoor kiest om vrijwillig uw klassieke waarschuwingsregels te migreren naar nieuwe waarschuwingsregels, moet u zich ervan bewust zijn dat er enkele verschillen zijn tussen de twee systemen. In dit artikel worden deze verschillen uitgelegd en hoe u zich voorbereiden op de wijziging.

## <a name="api-changes"></a>API-wijzigingen

De API's die klassieke waarschuwingsregels maken en beheren (`microsoft.insights/alertrules`) verschillen van`microsoft.insights/metricalerts`de API's die nieuwe metrische waarschuwingen maken en beheren ( ). Als u vandaag de dag programmatisch klassieke waarschuwingsregels maakt en beheert, werkt u uw implementatiescripts bij om te werken met de nieuwe API's.

De volgende tabel is een verwijzing naar de programmatische interfaces voor zowel klassieke als nieuwe waarschuwingen:

|         |Klassieke waarschuwingen  |Nieuwe metrische waarschuwingen |
|---------|---------|---------|
|REST-API     | [microsoft.insights/waarschuwingsregels](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metrische waarschuwingen](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [waarschuwing az-monitorstatistieken](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [Naslaginformatie](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [Naslaginformatie](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager-sjabloon | [Voor klassieke waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[Voor nieuwe metrische waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>Wijzigingen in het laadlaadvermogen van de melding

De payload-indeling van meldingen verschilt enigszins tussen [klassieke waarschuwingsregels](alerts-webhooks.md) en [nieuwe metrische waarschuwingen](alerts-metric-near-real-time.md#payload-schema). Als u webhook-, logic-app- of runbook-acties hebt die worden geactiveerd door klassieke waarschuwingsregels, moet u deze meldingseindpunten bijwerken om de payload-indeling van nieuwe metrische waarschuwingen te accepteren.

Gebruik de volgende tabel om de laadlaadvelden van de webhook van de klassieke indeling naar de nieuwe indeling in kaart te brengen:

|  |Klassieke waarschuwingen  |Nieuwe metrische waarschuwingen |
|---------|---------|---------|
|Is de waarschuwing geactiveerd of opgelost?    | **Status**       | **data.status** |
|Contextuele informatie over de waarschuwing     | **Context**        | **data.context**        |
|Tijdstempel waarop de waarschuwing is geactiveerd of opgelost     | **context.tijdstempel**       | **data.context.timestamp**        |
| Waarschuwingsregel-ID | **context.id** | **data.context.id** |
| Naam van waarschuwingsregel | **context.name** | **data.context.name** |
| Beschrijving van de waarschuwingsregel | **context.description** | **data.context.description** |
| Waarschuwingsregelvoorwaarde | **context.voorwaarde** | **data.context.condition** |
| Naam van meetwaarde | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| Tijdaggregatie (hoe de statistiek wordt samengevoegd over het evaluatievenster)| **context.condition.timeAggregatie** | **context.condition.timeAggregatie** |
| Evaluatieperiode | **context.condition.windowGrootte** | **data.context.condition.windowSize** |
| Operator (hoe de geaggregeerde metrische waarde wordt vergeleken met de drempelwaarde) | **context.condition.operator** | **data.context.condition.operator** |
| Drempelwaarde | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| Metrische waarde | **context.condition.metricWaarde** | **data.context.condition.allOf[0].metricValue** |
| Abonnements-id | **context.subscriptionId** | **data.context.subscriptionId** |
| Resourcegroep van de getroffen resource | **context.resourceGroep** | **data.context.resourceGroep** |
| Naam van de betreffende resource | **context.resourceNaam** | **data.context.resourceNaam** |
| Type van de betreffende resource | **context.resourceType** | **data.context.resourceType** |
| Resource-id van de getroffen resource | **context.resourceId** | **data.context.resourceId** |
| Directe koppeling naar de overzichtspagina van de portalbron | **context.portalLink** | **data.context.portalLink** |
| Aangepaste payloadvelden die moeten worden doorgegeven aan de webhook- of logische app | **Eigenschappen** | **data.eigenschappen** |

De payloads zijn vergelijkbaar, zoals je zien. De volgende sectie biedt:

- Details over het wijzigen van logische apps om met de nieuwe indeling te werken.
- Een voorbeeld van runbook dat de meldingspayload ontneemt voor nieuwe waarschuwingen.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Een logische app wijzigen om een melding met metrische waarschuwing te ontvangen

Als u logische apps met klassieke waarschuwingen gebruikt, moet u uw logische-app-code wijzigen om de nieuwe laadlaadlading met metrische waarschuwingen te ontzien. Volg deze stappen:

1. Maak een nieuwe logische app.

1. Gebruik de sjabloon 'Azure Monitor - Metrics Alert Handler'. Deze sjabloon heeft een **HTTP-aanvraagtrigger** met het juiste schema gedefinieerd.

    ![logic-app-template](media/alerts-migration/logic-app-template.png "Metrische waarschuwingssjabloon")

1. Voeg een actie toe om uw verwerkingslogica te hosten.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Een automatiseringsrunboek gebruiken dat een metrische waarschuwing ontvangt

In het volgende voorbeeld wordt PowerShell-code gebruikt in uw runbook. Deze code kan de payloads ontzetten voor zowel klassieke metrische waarschuwingsregels als nieuwe metrische waarschuwingsregels.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Zie de [azure automation-documentatie](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)voor een volledig voorbeeld van een runbook dat een virtuele machine stopt wanneer een waarschuwing wordt geactiveerd.

## <a name="partner-integration-via-webhooks"></a>Partnerintegratie via webhooks

De meeste van [onze partners die integreren met klassieke waarschuwingen](https://docs.microsoft.com/azure/azure-monitor/platform/partners) ondersteunen al nieuwere metrische waarschuwingen via hun integraties. Bekende integraties die al werken met nieuwe metrische waarschuwingen zijn:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Als u een partnerintegratie gebruikt die hier niet wordt vermeld, bevestigt u bij de integratieprovider dat de integratie werkt met nieuwe metrische waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

- [Het hulpprogramma voor migratie gebruiken](alerts-using-migration-tool.md)
- [Werking van het hulpprogramma voor migratie](alerts-understand-migration.md)
