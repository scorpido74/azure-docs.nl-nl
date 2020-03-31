---
title: Update van klassieke waarschuwingen &-monitoring in Azure Monitor
description: Beschrijving van de pensionering van klassieke bewakingsservices en -functionaliteit, eerder weergegeven in Azure-portal onder Waarschuwingen (klassiek).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659472"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Unified alerting & monitoring in Azure Monitor vervangt klassieke waarschuwingen & monitoring

Azure Monitor is nu uitgegroeid tot een uniforme full stack monitoring service, die nu 'One Metric' en 'One Alerts' ondersteunt tussen resources; voor meer informatie, zie onze [blogpost op nieuwe Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nieuwe Azure-monitoring- en waarschuwingsplatforms zijn gebouwd om sneller, slimmer en uitbreidbaar te zijn - gelijke tred houden met de groeiende uitgestrektheid van cloud computing en in lijn met de Microsoft Intelligent Cloud-filosofie. 

Met het nieuwe Azure-monitoring- en waarschuwingsplatform op zijn plaats, zullen we het "klassieke" monitoring- en waarschuwingsplatform met pensioen gaan - gehost in *de sectie klassieke waarschuwingen* van Azure-waarschuwingen, **worden vóóv augustus 2019 afgeschaft in Azure public clouds**. [Azure Government cloud](../../azure-government/documentation-government-welcome.md) en [Azure China 21Vianet](https://docs.azure.cn/) worden niet beïnvloed.

> [!NOTE]
> Vanwege vertraging bij de uitrol van migratietool is de pensioendatum voor klassieke waarschuwingen verlengd [tot 31 augustus 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) vanaf de oorspronkelijk aangekondigde datum van 30 juni 2019.

 ![Klassieke waarschuwing in Azure-portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

We raden u aan om aan de slag te gaan en uw waarschuwingen opnieuw te maken in het nieuwe platform. Voor klanten die een groot aantal waarschuwingen hebben, zijn we [gefaseerd aan het uitrollen](alerts-understand-migration.md#rollout-phases), een [vrijwillige migratietool](alerts-using-migration-tool.md) om bestaande klassieke waarschuwingen zonder onderbreking of extra kosten naar het nieuwe waarschuwingssysteem te verplaatsen.

> [!IMPORTANT]
> Klassieke waarschuwingsregels die zijn gemaakt op het activiteitenlogboek, worden niet afgeschaft of gemigreerd. Alle klassieke waarschuwingsregels die zijn gemaakt in het activiteitenlogboek, kunnen worden geopend en gebruikt zoals deze afkomstig zijn uit de nieuwe Azure Monitor - Alerts. Zie [Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md)voor meer informatie. Op dezelfde manier kunnen waarschuwingen over servicestatus worden geopend en gebruikt zoals deze zijn uitgevoerd in de nieuwe sectie Servicestatus. Zie [waarschuwingen over meldingen over de status van de service](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)voor meer informatie .

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Uniforme statistieken en waarschuwingen in toepassingsstatistieken

Het nieuwere metrische platform van Azure Monitor wordt nu van power monitoring vanuit Application Insights verwijderd. Deze stap betekent dat Application Insights zal aansluiten bij actiegroepen, waardoor veel meer opties dan alleen de vorige e-mail en webhook gesprekken. Waarschuwingen kunnen nu spraakoproepen, Azure-functies, logische apps, sms- en ITSM-hulpprogramma's zoals ServiceNow en Automation Runbooks activeren. Met bijna realtime monitoring en waarschuwingen stelt het nieuwe platform Gebruikers van Application Insights in staat om dezelfde technologie te gebruiken die bewaking van andere Azure-bronnen mogelijk maakt en de monitoring van Microsoft-producten ondersteunt.

De nieuwe uniforme monitoring en waarschuwing voor application insights omvat:

- **Application Insights Platform metrics** – die populaire vooraf gebouwde metrics van Application Insights product biedt. Zie dit artikel over het gebruik van [Platformmetrics voor toepassingsinzichten op nieuwe Azure Monitor voor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)meer informatie.
- **Beschikbaarheid van applicatie-inzichten en webtest** - waarmee u de responsiviteit en beschikbaarheid van uw web-app of -server beoordelen. Zie dit artikel over het gebruik van [beschikbaarheidstests en waarschuwingen voor toepassingsinzichten op nieuwe Azure-monitor voor](../../azure-monitor/app/monitor-web-app-availability.md)meer informatie.
- **Application Insights Custom metrics** – waarmee je hun eigen metrics definiëren en uitzenden voor monitoring en waarschuwingen. Zie dit artikel over het gebruik van [aangepaste metrische gegevens voor toepassingsinzichten op nieuwe Azure-monitor voor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)meer informatie.
- **Foutafwijkingen van application Insights (onderdeel van Slimme detectie)** – die u automatisch in bijna realtime op de hoogte stelt als uw web-app een abnormale stijging ervaart van het aantal mislukte HTTP-aanvragen of afhankelijkheidsoproepen. Zie dit artikel over het gebruik van [smart detection - Failure Anomalies](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)voor meer informatie.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Uniforme statistieken en waarschuwingen voor andere Azure-bronnen

Sinds maart 2018 is de volgende generatie waarschuwingen en multidimensionale bewaking voor Azure-resources beschikbaar. Nu is het nieuwere metrische platform en de waarschuwingen sneller met near-real-time mogelijkheden. Wat nog belangrijker is, de nieuwere metrische platformwaarschuwingen bieden meer granulariteit, omdat het nieuwere platform de optie van dimensies bevat, waarmee u snijden en filteren op specifieke waardecombinatie, -voorwaarde of -bewerking. Net als alle waarschuwingen in de nieuwe Azure Monitor zijn de nieuwere metrische waarschuwingen meer uitbreidbaar met het gebruik van ActionGroups , zodat meldingen verder kunnen worden uitgebreid dan e-mail of webhook naar SMS, Voice, Azure Function, Automation Runbook en meer. Zie [Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie.
Nieuwere statistieken voor Azure-bronnen zijn beschikbaar als:

- **Azure Monitor Standard-platformstatistieken** – die populaire vooraf ingevulde statistieken van verschillende Azure-services en -producten biedt. Zie dit artikel over [ondersteunde statistieken over](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) [metrische waarschuwingen](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)voor Azure Monitor en Ondersteuning op Azure Monitor voor meer informatie.
- **Azure Monitor Custom-statistieken** – met statistieken van door gebruikers gestuurde bronnen, waaronder de Azure Diagnostics-agent. Zie dit artikel over [aangepaste statistieken in Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md)voor meer informatie. Met behulp van aangepaste statistieken u ook statistieken publiceren die zijn verzameld door [windows Azure Diagnostics-agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) en [InfluxData Telegraf-agent.](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Pensionering van Classic monitoring en alerting platform

Zoals eerder vermeld, zal het klassieke monitoring- en waarschuwingsplatform dat momenteel bruikbaar is vanuit het [gedeelte Waarschuwingen (klassieke) van](../../azure-monitor/platform/alerts-classic.overview.md) Azure-portal in de komende maanden worden verwijderd, aangezien ze zijn vervangen door het nieuwere systeem.
Oudere klassieke monitoring en signalering wordt op 31 augustus 2019 met pensioen gegaan; inclusief de sluiting van gerelateerde API's, Azure-portalinterface en Services erin. In het bijzonder zullen deze functies worden afgeschaft:

- Oudere (klassieke) statistieken en waarschuwingen voor Azure-bronnen die momenteel beschikbaar zijn via [het gedeelte Waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van azure-portal; toegankelijk als [microsoft.insights/alertrules-bron](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Oudere (klassieke) platform- en aangepaste statistieken voor Application Insights en waarschuwen deze op de huidige beschikbare [site alerts (klassieke) sectie](../../azure-monitor/platform/alerts-classic.overview.md) van Azure portal en toegankelijk als [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) resource
- Oudere (klassieke) foutafwijkingen waarschuwing momenteel beschikbaar als [Smart Detection binnen Application Insights](../../azure-monitor/app/proactive-diagnostics.md) in de Azure-portal; met waarschuwingen geconfigureerd in [het gedeelte Waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van Azure-portal

Alle klassieke bewakings- en waarschuwingssystemen, waaronder de bijbehorende [API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [Azure-portalpagina](../../azure-monitor/platform/alerts-classic-portal.md)en [Resource template,](../../azure-monitor/platform/alerts-enable-template.md) blijven bruikbaar tot eind augustus 2019. 

Eind augustus 2019 in Azure Monitor:

- De service voor klassieke bewaking en waarschuwingen wordt buiten gebruik gesteld en is niet langer beschikbaar voor het maken van nieuwe waarschuwingsregels.
- Alle waarschuwingsregels die ook na augustus 2019 in Alerts (klassiek) blijven bestaan, blijven meldingen uitvoeren en afvuren, maar zijn niet beschikbaar voor wijziging.
- Vanaf september 2019 worden waarschuwingsregels in klassieke monitoring & waarschuwingen die kunnen worden gemigreerd, automatisch door Microsoft naar hun equivalent verplaatst in het nieuwe Azure-monitorplatform in fasen van enkele weken. Het proces zal naadloos verlopen zonder downtime en klanten zullen geen verlies in monitoring dekking hebben.
- Waarschuwingsregels gemigreerd naar het nieuwe waarschuwingsplatform bieden monitoringdekking als voorheen, maar zullen meldingen met nieuwe payloads afvuren. Elk e-mailadres, webhook-eindpunt of logische app-koppeling in verband met de klassieke waarschuwingsregel wordt overgedragen wanneer deze wordt gemigreerd, maar gedraagt zich mogelijk niet correct, omdat het laadvermogen van waarschuwingen in het nieuwe platform anders zal zijn.
- Sommige [klassieke waarschuwingsregels die niet automatisch kunnen worden gemigreerd](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) en handmatige actie van gebruikers vereisen, blijven tot juni 2020 worden uitgevoerd.

> [!IMPORTANT]
> Microsoft Azure Monitor is gefaseerd uitgerold om vrijwillig hun klassieke waarschuwingsregels binnenkort naar het nieuwe platform te [migreren.](alerts-using-migration-tool.md) En voer het met geweld uit voor alle klassieke waarschuwingsregels die nog steeds bestaan en kunnen worden gemigreerd, vanaf september 2019. Klanten moeten ervoor zorgen dat de automatisering die klassieke waarschuwingsregelpayload verbruikt, is aangepast om de nieuwe payload van [Unified Metrics en Alerts in Application Insights](#unified-metrics-and-alerts-in-application-insights) of Unified Metrics and Alerts voor andere [Azure-bronnen](#unified-metrics-and-alerts-for-other-azure-resources)te verwerken, na de migratie van de klassieke waarschuwingsregels. Zie [voorbereiden op migratie van klassieke waarschuwingsregels voor](alerts-prepare-migration.md) meer informatie

Dit artikel wordt voortdurend bijgewerkt met koppelingen & details over de nieuwe Azure-monitoring-& waarschuwingsfunctionaliteit, evenals de beschikbaarheid van hulpprogramma's om gebruikers te helpen bij de goedkeuring van het nieuwe Azure Monitor-platform.

## <a name="pricing-for-migrated-alert-rules"></a>Prijzen voor gemigreerde waarschuwingsregels

We implementeren een migratietool om u te helpen uw [klassieke Azure Monitor-waarschuwingen](../../azure-monitor/platform/alerts-classic.overview.md) te migreren naar de nieuwe waarschuwingservaring. De gemigreerde waarschuwingsregels en bijbehorende gemigreerde actiegroepen (e-mail, webhook of LogicApp) blijven gratis. De functionaliteit die u had met klassieke waarschuwingen, waaronder de mogelijkheid om de drempelwaarde, aggregatietype en de aggregatiegranulariteit te bewerken, blijft gratis beschikbaar met uw gemigreerde waarschuwingsregel. Als u echter de gemigreerde waarschuwingsregel bewerkt om een van de nieuwe waarschuwingsplatformfuncties, meldingen of actietypen te gebruiken, zijn er een overeenkomstige kosten van toepassing. Zie [Azure Monitor Pricing](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie over de prijzen voor waarschuwingsregels en meldingen.

Hieronder volgen voorbeelden van gevallen waarin u kosten in rekening brengt voor uw waarschuwingsregel:

- Elke nieuwe (niet-gemigreerde) waarschuwingsregel die is gemaakt buiten gratis eenheden, op het nieuwe Azure Monitor-platform
- Alle gegevens die worden ingenomen en bewaard buiten gratis eenheden die zijn opgenomen door Azure Monitor
- Alle multi-test webtests uitgevoerd door Application Insights
- Aangepaste statistieken die zijn opgeslagen buiten gratis eenheden die zijn opgenomen in Azure Monitor
- Gemigreerde waarschuwingsregels die worden bewerkt om nieuwere metrische waarschuwingsfuncties te gebruiken, zoals frequentie, meerdere resources/dimensies, [dynamische drempelwaarden,](alerts-dynamic-thresholds.md)het wijzigen van resource/signaal, enzovoort.
- Gemigreerde actiegroepen die zijn bewerkt om nieuwere meldingen te gebruiken, of actietypen zoals SMS, Voice Call en/of ITSM-integratie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [nieuwe uniforme Azure-monitor](../../azure-monitor/overview.md).
* Meer informatie over de nieuwe [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
