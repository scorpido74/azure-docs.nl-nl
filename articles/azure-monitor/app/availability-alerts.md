---
title: Beschikbaarheidswaarschuwingen instellen met Azure Application Insights | Microsoft Documenten
description: Stel webtests in Application Insights in. Ontvang een waarschuwing wanneer een website niet meer beschikbaar is of traag reageert.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 5af6aec2267384c37f664522d075bf26c632e7e9
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382885"
---
# <a name="availability-alerts"></a>Beschikbaarheidswaarschuwingen

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) verzendt regelmatig webaanvragen naar uw toepassing vanaf verschillende punten over de hele wereld. Het kan u waarschuwen als uw toepassing niet reageert, of als het te traag reageert.

## <a name="enable-alerts"></a>Waarschuwingen inschakelen

Waarschuwingen worden nu standaard automatisch ingeschakeld, maar om de waarschuwing volledig te configureren moet u eerst uw beschikbaarheidstest maken.

![Ervaring creëren](./media/availability-alerts/create-test.png)

> [!NOTE]
>  Met de [nieuwe uniforme waarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)moeten de ernst van de waarschuwingsregel en de meldingsvoorkeuren met [actiegroepen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) **worden** geconfigureerd in de waarschuwingservaring. Zonder de volgende stappen ontvangt u alleen in-portal meldingen.

1. Na het opslaan van de beschikbaarheidstest, klik je op het tabblad details op de ellips door de test die je net hebt gemaakt. Klik op "waarschuwing bewerken".

   ![Bewerken na opslaan](./media/availability-alerts/edit-alert.png)

2. Stel het gewenste ernstniveau, regelbeschrijving en vooral in - de actiegroep die de meldingsvoorkeuren heeft die u wilt gebruiken voor deze waarschuwingsregel.

   ![Bewerken na opslaan](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Beschikbaarheidswaarschuwingen die via deze ervaring zijn gemaakt, zijn op basis van de status. Dit betekent dat wanneer aan de waarschuwingscriteria wordt voldaan, één waarschuwing wordt gegenereerd wanneer de site wordt gedetecteerd als niet beschikbaar. Als de site de volgende keer dat de waarschuwingscriteria wordt geëvalueerd nog steeds is uitgeschakeld, genereert dit geen nieuwe waarschuwing. Dus als uw site een uur uit viel en u een e-mailwaarschuwing had ingesteld, ontvangt u alleen een e-mail wanneer de site uitviel en een volgende e-mail wanneer de site een back-up maakte. U ontvangt geen doorlopende waarschuwingen om u eraan te herinneren dat de site nog steeds niet beschikbaar was.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Waarschuwing op X van Y-locaties die fouten melden

De waarschuwingsregel X van Y-locaties is standaard ingeschakeld in de [nieuwe uniforme waarschuwingen-ervaring](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), wanneer u een nieuwe beschikbaarheidstest maakt. U zich afmelden door de optie 'klassiek' te selecteren of de waarschuwingsregel uit te schakelen.

> [!NOTE]
> Configureer de actiegroepen om meldingen te ontvangen wanneer de waarschuwing wordt geactiveerd door de bovenstaande stappen te volgen. Zonder deze stap ontvangt u alleen meldingen in de portal wanneer de regel wordt geactiveerd.
>

### <a name="alert-on-availability-metrics"></a>Waarschuwing over beschikbaarheidsstatistieken

Met behulp van de [nieuwe uniforme waarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)u ook waarschuwen voor gesegmenteerde gegevens over de beschikbaarheid van aggregaten en de duur van de test:

1. Selecteer een resource Application Insights in de ervaring Met statistieken en selecteer een statistiek beschikbaarheid:

    ![Selectie van beschikbaarheidsstatistieken](./media/availability-alerts/select-metric.png)

2. De optie Waarschuwingen configureren in het menu brengt u naar de nieuwe ervaring waar u specifieke tests of locaties selecteren om de waarschuwingsregel in te stellen. U de actiegroepen voor deze waarschuwingsregel hier ook configureren.

### <a name="alert-on-custom-analytics-queries"></a>Waarschuwing voor aangepaste analysequery's

Met behulp van de [nieuwe uniforme waarschuwingen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)u waarschuwen voor [aangepaste logboekquery's.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log) Met aangepaste query's u waarschuwen voor elke willekeurige voorwaarde die u helpt het meest betrouwbare signaal van beschikbaarheidsproblemen te krijgen. Dit is ook van toepassing als u aangepaste beschikbaarheidsresultaten verzendt met de TrackAvailability SDK.

> [!Tip]
> De statistieken over beschikbaarheidsgegevens bevatten eventuele aangepaste beschikbaarheidsresultaten die u mogelijk indient door onze TrackAvailability SDK te bellen. U de waarschuwing en ondersteuning voor metrische gegevens gebruiken om te waarschuwen voor aangepaste beschikbaarheidsresultaten.
>

## <a name="automate-alerts"></a>Waarschuwingen automatiseren

Als u dit proces wilt automatiseren met Azure Resource Manager-sjablonen, raadpleegt u de [metrische waarschuwing maken met resourcebeheersjabloondocumentatie.](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="troubleshooting"></a>Problemen oplossen

Specifiek [artikel over probleemoplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Webtests met meerdere stappen](availability-multistep.md)
* [Webtests voor url-ping](monitor-web-app-availability.md)
