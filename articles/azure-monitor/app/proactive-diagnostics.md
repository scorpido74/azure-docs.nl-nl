---
title: Slimme detectie in Azure-toepassingsinzichten | Microsoft Documenten
description: Application Insights voert automatische diepgaande analyse van uw app telemetrie uit en waarschuwt u voor mogelijke problemen.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: ff9f88e1d2e643d04c4417283420217e7d496caf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536791"
---
# <a name="smart-detection-in-application-insights"></a>Slimme detectie in Application Insights
 Smart Detection waarschuwt u automatisch voor mogelijke prestatieproblemen en foutafwijkingen in uw webtoepassing. Het voert proactieve analyse uit van de telemetrie die uw app naar [Application Insights](../../azure-monitor/app/app-insights-overview.md)verzendt. Als er een plotselinge stijging van het aantal fouten of abnormale patronen in de prestaties van de client of server is, krijgt u een waarschuwing. Deze functie behoeft geen configuratie. Het werkt als uw toepassing stuurt genoeg telemetrie.

U hebt toegang tot de detecties die door Smart Detection zijn uitgegeven, zowel via de e-mails die u ontvangt als via het slimme detectieblad.

## <a name="review-your-smart-detections"></a>Bekijk uw slimme detecties
U detecties op twee manieren ontdekken:

* **U ontvangt een e-mail** van Application Insights. Hier volgt een kenmerkend voorbeeld:
  
    ![E-mailwaarschuwing](./media/proactive-diagnostics/03.png)
  
    Klik op de grote knop om meer details in de portal te openen.
* **Het slimme detectieblad** in Application Insights. Selecteer **Slimme detectie** onder het menu **Onderzoeken** om een lijst met recente detecties te bekijken.

![Recente detecties weergeven](./media/proactive-diagnostics/04.png)

Selecteer een detectie om de details te zien.

## <a name="what-problems-are-detected"></a>Welke problemen worden gedetecteerd?
Slimme detectie detecteert en waarschuwt over verschillende problemen, zoals:

* [Slimme detectie - Foutafwijkingen](../../azure-monitor/app/proactive-failure-diagnostics.md). We gebruiken machine learning om het verwachte aantal mislukte aanvragen voor uw app in te stellen, waarbij we correleren met belasting en andere factoren. Als het percentage mislukkingen buiten de verwachte envelop gaat, sturen we een waarschuwing.
* [Slimme detectie - Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md). U ontvangt meldingen als de reactietijd van een bewerking of afhankelijkheidsduur vertraagt in vergelijking met de historische basislijn of als we een afwijkend patroon identificeren in de reactietijd of de laadtijd van de pagina.   
* Algemene degradaties en problemen, zoals [De degradatie van het spoor](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), Het lek van het [geheugen](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), Abnormale stijging in het volume van [de Uitzondering](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) en [De anti-patronen van de veiligheid](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(De helplinks in elke melding brengen u naar de relevante artikelen.)

## <a name="smart-detection-email-notifications"></a>E-mailmeldingen voor slimme detectie

Alle regels voor slimme detectie, met uitzondering van regels die als _voorbeeld_zijn gemarkeerd, zijn standaard geconfigureerd om e-mailmeldingen te verzenden wanneer detecties worden gevonden.

Het configureren van e-mailmeldingen voor een specifieke smartdetectionregel kan worden gedaan door het blade Slimme **detectie-instellingen** te openen en de regel te selecteren, waarmee het **regelblad bewerken** wordt geopend.

U de configuratie ook wijzigen met Azure Resource Manager-sjablonen. [Zie Slimme detectieregels voor Toepassingsinzichten beheren met Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) voor meer informatie.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Volgende stappen
Met deze diagnostische hulpprogramma's u de telemetrie vanuit uw app inspecteren:

* [Metrische verkenner](../../azure-monitor/platform/metrics-charts.md)
* [Zoekverkenner](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - krachtige querytaal](../../azure-monitor/log-query/get-started-portal.md)

Smart Detection is volledig automatisch. Maar misschien wilt u nog meer waarschuwingen instellen?

* [Handmatig geconfigureerde metrische waarschuwingen](../../azure-monitor/app/alerts.md)
* [Webtests voor beschikbaarheid](../../azure-monitor/app/monitor-web-app-availability.md) 

