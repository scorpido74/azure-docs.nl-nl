---
title: Unified Alerting-& bewaking in Azure Monitor vervangt klassieke waarschuwingen & bewaking
description: Overzicht van het buiten gebruik stellen van klassieke bewakings Services en-functionaliteit, eerder weer gegeven in Azure Portal onder waarschuwingen (klassiek). Klassieke waarschuwingen & bewaking omvat klassieke metrische gegevens voor Azure-resources, klassieke metrische waarschuwingen voor Application Insights, klassieke webtest-waarschuwingen voor Application Insights, klassieke, op basis van aangepaste metrische gegevens gebaseerde waarschuwingen voor Application Insights en klassiek waarschuwingen voor Application Insights SmartDetection v1
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: ea50a4f10aa1d794f875cb1eb9601774bec146cd
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676902"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Unified Alerting-& bewaking in Azure Monitor vervangt klassieke waarschuwingen & bewaking

Azure Monitor is nu een geïntegreerde volledige stack monitoring-service, die nu ondersteuning biedt voor ' één metrische ' en ' One Alerts ' in resources. Zie voor meer informatie ons [blog bericht over nieuwe Azure monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). De nieuwe Azure-bewakings-en waarschuwings platformen zijn gebouwd om sneller, slimmer en uitbreidbaar te zijn, met de groeiende Expanse van Cloud Computing en online met micro soft intelligent Cloud filosofie. 

Met het nieuwe Azure-bewakings-en waarschuwings platform in de cloud worden de klassieke bewakings-en waarschuwings platform-hosten die worden gehost in de sectie *klassieke waarschuwingen weer geven* van Azure-waarschuwingen, **afgeschaft door augustus 2019 in open bare Azure** . [Azure Government Cloud](../../azure-government/documentation-government-welcome.md) en [Azure China 21vianet](https://docs.azure.cn/) worden niet beïnvloed.

> [!NOTE]
> Als gevolg van een vertraging bij het uitrollen van het migratie hulpprogramma, is de buitengebruikstellings datum voor de migratie van klassieke waarschuwingen [uitgebreid tot en met 31 augustus 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) van de oorspronkelijke datum van 30 juni 2019.

 ![Klassieke waarschuwing in Azure Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

We raden u aan om aan de slag te gaan en uw waarschuwingen opnieuw te maken op het nieuwe platform. Voor klanten die een groot aantal waarschuwingen hebben, [implementeren we in fasen](alerts-understand-migration.md#rollout-phases)een [hulp programma voor vrijwillige migratie](alerts-using-migration-tool.md) om bestaande klassieke waarschuwingen te verplaatsen naar het nieuwe systeem met waarschuwingen zonder onderbrekingen of extra kosten.

> [!IMPORTANT]
> Klassieke waarschuwings regels die zijn gemaakt in het activiteiten logboek, worden niet gedeprecieerd of gemigreerd. Alle klassieke waarschuwings regels die zijn gemaakt in het activiteiten logboek, zijn toegankelijk en worden gebruikt door de nieuwe Azure Monitor-waarschuwingen. Zie [waarschuwingen voor activiteiten logboek maken, weer geven en beheren met Azure monitor](../../azure-monitor/platform/alerts-activity-log.md)voor meer informatie. Waarschuwingen op Service Health zijn ook toegankelijk en kunnen worden gebruikt als-afkomstig uit de sectie nieuw Service Health. Zie [waarschuwingen over service status meldingen](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)voor meer informatie.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Uniforme metrische gegevens en waarschuwingen in Application Insights

Het nieuwere meet platform van Azure Monitor zal nu van Application Insights worden bewaakt. Deze verplaatsing houdt in dat Application Insights wordt toegevoegd aan actie groepen, waardoor veel meer opties worden toegestaan dan alleen de vorige e-mail en webhook-aanroepen. Waarschuwingen kunnen nu gesp rekken, Azure Functions, Logic Apps, SMS en ITSM-Hulpprogram Ma's, zoals ServiceNow en Automation-Runbooks, activeren. Dankzij de bijna realtime bewaking en waarschuwingen, kunnen Application Insights gebruikers met behulp van dezelfde technologie bewaking voor andere Azure-resources gebruiken om de bewaking van micro soft-producten te verbeteren.

De nieuwe geïntegreerde bewakings-en waarschuwings functies voor Application Insights omvatten:

- **Application Insights platform metrieken** : Dit biedt populaire, vooraf gebouwde metrische gegevens van Application Insights product. Zie dit artikel over het gebruik van [platform metrieken voor Application Insights op nieuwe Azure monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)voor meer informatie.
- **Application Insights Beschik baarheid en webtest** , waarmee u de reactie snelheid en beschik baarheid van uw web-app of-server kunt beoordelen. Zie dit artikel over het gebruik van [beschikbaarheids tests en waarschuwingen voor Application Insights op nieuwe Azure monitor](../../azure-monitor/app/monitor-web-app-availability.md)voor meer informatie.
- **Application Insights aangepaste metrische gegevens** , waarmee u hun eigen metrische gegevens kunt definiëren en verzenden voor bewaking en waarschuwingen. Zie dit artikel over het gebruik van [aangepaste metrische gegevens voor Application Insights op nieuwe Azure monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)voor meer informatie.
- **Application Insights fout afwijkingen (onderdeel van Slimme detectie)** : Hiermee wordt u automatisch op de hoogte gebracht als uw web-app een abnormale toename in de frequentie van mislukte HTTP-aanvragen of afhankelijkheids aanroepen voordoet. Zie voor meer informatie dit artikel over het gebruik van [Smart Detection-fout afwijkingen](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Uniforme metrische gegevens en waarschuwingen voor andere Azure-resources

Sinds 2018 maart is de volgende generatie van waarschuwingen en multidimensionale bewaking voor Azure-resources in de beschik baarheid. Nu is het nieuwere meet platform en de waarschuwingen sneller met bijna realtime mogelijkheden. Belang rijker is dat de nieuwere metrische platform waarschuwingen meer granulariteit bieden, omdat het nieuwere platform de optie dimensies bevat, waarmee u de combi natie van specifieke waarden, voor waarde of bewerking kunt segmenteren en filteren. Net als alle waarschuwingen in de nieuwe Azure Monitor, zijn de nieuwere metrische waarschuwingen uitgebreider met het gebruik van ActionGroups, zodat meldingen buiten e-mail of webhooks worden uitgebreid naar SMS, Voice, Azure function, Automation Runbook en meer. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie.
Nieuwe metrische gegevens voor Azure-resources zijn beschikbaar als:

- **Azure monitor standaard platform metrieken** : Dit biedt populaire vooraf gevulde metrische gegevens van verschillende Azure-Services en-producten. Zie dit artikel over [ondersteunde metrische gegevens op Azure monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) en [ondersteuning voor metrische waarschuwingen op Azure monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)voor meer informatie.
- **Azure monitor aangepaste metrische gegevens** , die metrische gegevens van door de gebruiker gestuurde bronnen bevat, waaronder de Azure Diagnostics-agent. Zie dit artikel over [aangepaste metrische gegevens in azure monitor](../../azure-monitor/platform/metrics-custom-overview.md)voor meer informatie. Met aangepaste metrische gegevens kunt u ook metrische gegevens die zijn verzameld door [Windows Azure Diagnostics agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) en [InfluxData-telegrafe-agent](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md), publiceren.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Buiten gebruik stellen van klassiek bewakings-en waarschuwings platform

Zoals eerder vermeld, wordt het klassieke bewakings-en waarschuwings platform dat momenteel kan worden gebruikt via de [sectie waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van Azure Portal, in de komende maanden ingetrokken, omdat deze zijn vervangen door het nieuwere systeem.
Oudere klassieke bewaking en waarschuwingen worden op 31 augustus 2019 buiten gebruik gesteld. inclusief de sluiting van gerelateerde Api's, Azure Portal-interface en de bijbehorende services. Deze functies worden met name afgeschaft:

- Oudere (klassieke) metrische gegevens en waarschuwingen voor Azure-resources als momenteel beschikbaar via [waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van Azure Portal. toegankelijk als [micro soft. Insights/alertrules-](https://docs.microsoft.com/rest/api/monitor/alertrules) resource
- Ouder (klassiek) platform en aangepaste metrische gegevens voor Application Insights, evenals een waarschuwing voor de functies die beschikbaar zijn via [waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van Azure Portal en die toegankelijk zijn als [micro soft. Insights-alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) resource
- Oudere (klassieke) fout afwijkingen die momenteel beschikbaar zijn als [Slimme detectie binnen Application Insights](../../azure-monitor/app/proactive-diagnostics.md) in de Azure Portal; met waarschuwingen geconfigureerd weer gegeven in de [sectie waarschuwingen (klassiek)](../../azure-monitor/platform/alerts-classic.overview.md) van Azure Portal

Alle klassieke bewakings-en waarschuwings systemen met inbegrip van de bijbehorende [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [Power shell](../../azure-monitor/platform/alerts-classic-portal.md), [cli](../../azure-monitor/platform/alerts-classic-portal.md), [Azure portal-pagina](../../azure-monitor/platform/alerts-classic-portal.md)en de [resource sjabloon](../../azure-monitor/platform/alerts-enable-template.md) blijven bruikbaar tot eind augustus 2019. 

Aan het einde van 2019 augustus in Azure Monitor:

- De klassieke bewakings-en waarschuwings service wordt buiten gebruik gesteld en is niet meer beschikbaar voor het maken van nieuwe waarschuwings regels.
- Alle waarschuwings regels die blijven bestaan in waarschuwingen (klassiek) van meer dan augustus 2019, blijven de meldingen uitvoeren en activeren, maar zijn niet beschikbaar voor wijziging.
- Vanaf september 2019 worden waarschuwings regels in klassieke bewaking & waarschuwingen die kunnen worden gemigreerd, automatisch verplaatst naar hun equivalent in het nieuwe Azure monitor-platform in fasen die enkele weken duren. Het proces is naadloos zonder uitval tijd en klanten hebben geen verlies van bewakings dekking.
- Waarschuwings regels die zijn gemigreerd naar het nieuwe waarschuwings platform bieden een bewakings dekking zoals voorheen, maar er wordt een melding met nieuwe nettoladingen geactiveerd. Alle e-mail adressen, webhook-eind punten, of logische app-koppelingen die zijn gekoppeld aan een klassieke waarschuwings regel, worden gemigreerd wanneer de migratie wordt uitgevoerd, maar werkt mogelijk niet goed omdat de nettolading van de waarschuwing afwijkt van het nieuwe platform.
- Sommige [klassieke waarschuwings regels die niet automatisch kunnen worden gemigreerd en die](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) hand matige actie vereisen van gebruikers, blijven actief tot juni 2020.

> [!IMPORTANT]
> Microsoft Azure monitor is uitgerold in het [hulp programma](alerts-using-migration-tool.md) Phase, zodat u snel hun klassieke waarschuwings regels kunt migreren naar het nieuwe platform. En voer deze uit door te forceren dat alle klassieke waarschuwings regels die nog bestaan en kunnen worden gemigreerd, vanaf september 2019. Klanten moeten ervoor zorgen dat de nettolading van de klassieke waarschuwings regel wordt gebruikt voor het afhandelen van de nieuwe payload van [uniforme metrische gegevens en waarschuwingen in Application Insights](#unified-metrics-and-alerts-in-application-insights) of [gecombineerde metrische gegevens en waarschuwingen voor andere Azure-resources](#unified-metrics-and-alerts-for-other-azure-resources), na de migratie van de klassieke waarschuwings regels. Zie voor meer informatie de [migratie van de klassieke waarschuwings regel voorbereiden](alerts-prepare-migration.md)

Dit artikel wordt voortdurend bijgewerkt met koppelingen & Details over de nieuwe functionaliteit van de Azure-bewaking & waarschuwingen en de beschik baarheid van hulpprogram ma's om gebruikers te helpen bij het aannemen van het nieuwe Azure Monitor platform.

## <a name="pricing-for-migrated-alert-rules"></a>Prijzen voor gemigreerde waarschuwings regels

We implementeren een hulp programma voor migratie om u te helpen bij het migreren van uw Azure Monitor [klassieke waarschuwingen](../../azure-monitor/platform/alerts-classic.overview.md) naar de nieuwe waarschuwings ervaring. De gemigreerde waarschuwingsregels en bijbehorende gemigreerde actiegroepen (e-mail, webhook of LogicApp) blijven gratis. De functionaliteit waarover u beschikte met klassieke waarschuwingen, waaronder de mogelijkheid om de drempelwaarde, het aggregatietype en het detailniveau voor de aggregatie te bewerken, blijven gratis voor uw gemigreerde waarschuwingsregel. Als u de gemigreerde waarschuwingsregel echter bewerkt om een van de nieuwe functies, meldingen of actietypen van het waarschuwingsplatform te gebruiken, wordt een bijpassend bedrag in rekening gebracht. Zie voor meer informatie over de prijzen voor waarschuwings regels en meldingen [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).

Hieronder staan voorbeelden van situaties waarin u moet betalen voor het wijzigen van uw waarschuwingsregel:

- Elke nieuwe (niet-gemigreerde) waarschuwingsregel die wordt gemaakt bovenop gratis regels op het nieuwe Azure Monitor-platform
- Alle gegevens die worden gebruikt en behouden bovenop de gratis eenheden die bij Azure Monitor zijn inbegrepen
- Alle webtests die door Application Insights worden uitgevoerd en uit meerdere tests bestaan
- Alle aangepaste metrische gegevens die worden opgeslagen bovenop de gratis eenheden die bij Azure Monitor zijn begrepen
- Alle gemigreerde waarschuwings regels die worden bewerkt om nieuwere metrische waarschuwings functies te gebruiken, zoals frequentie, meerdere resources/dimensies, [Dynamische drempel waarden](alerts-dynamic-thresholds.md), het wijzigen van de bron/het signaal, enzovoort.
- Gemigreerde actie groepen die zijn bewerkt voor het gebruik van nieuwere meldingen of actie typen zoals SMS, spraak oproep en/of ITSM-integratie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [nieuwe geïntegreerde Azure monitor](../../azure-monitor/overview.md).
* Meer informatie over de nieuwe [Azure-waarschuwingen](../../azure-monitor/platform/alerts-overview.md).
