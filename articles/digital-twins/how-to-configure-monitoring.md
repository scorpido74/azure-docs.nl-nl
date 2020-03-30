---
title: Controle configureren - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over het configureren van controle- en logboekopties voor Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: e35e18be20af3bd9f6fdc9541f9abfe857a6b87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511855"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Controle configureren in Azure Digital Twins

Azure Digital Twins ondersteunt robuuste logboekregistratie, monitoring en analyse. Ontwikkelaars van oplossingen kunnen Azure Monitor-logboeken, diagnostische logboeken, activiteitslogboekregistratie en andere services gebruiken om de complexe bewakingsbehoeften van een IoT-app te ondersteunen. Logopties kunnen worden gecombineerd om records op te vragen of weer te geven in verschillende services en om gedetailleerde logboekdekking te bieden voor veel services.

In dit artikel worden opties voor logboekregistratie en bewaking samengevat en hoe u deze combineren op een manier die specifiek is voor Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Activiteitslogboeken bekijken

[Azure-activiteitslogboeken](../azure-monitor/platform/platform-logs-overview.md) bieden snel inzicht in gebeurtenis- en bewerkingsgeschiedenissen op abonnementsniveau voor elk Azure-serviceexemplaar.

Evenementen op abonnementsniveau zijn onder andere:

* Resourcecreatie
* Bronverwijderen
* App-geheimen maken
* Integreren met andere diensten

Activiteitslogboekregistratie voor Azure Digital Twins is standaard ingeschakeld en is te vinden via de Azure-portal door:

1. Uw Azure Digital Twins-exemplaar selecteren.
1. Kies **het logboek Activiteit** om het weergavepaneel weer te geven:

    [![Activiteitenlogboek](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Voor geavanceerde activiteitslogboekregistratie:

1. Selecteer de optie **Logboeken** om het **overzicht activiteitslogboekanalyse weer**te geven:

    [![Selectie](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. Het **Overzicht Activiteitslogboekanalyse** bevat een overzicht van essentiële activiteitslogboekgegevens:

    [![Overzicht van analysevan activiteitenlogboeken]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Gebruik **activiteitslogboeken** voor snelle inzichten in gebeurtenissen op abonnementsniveau.

## <a name="enable-customer-diagnostic-logs"></a>Diagnostische logboeken van klanten inschakelen

[Azure-diagnostische instellingen](../azure-monitor/platform/platform-logs-overview.md) kunnen voor elke Azure-instantie worden ingesteld om activiteitslogboekregistratie aan te vullen. Hoewel activiteitslogboeken betrekking hebben op gebeurtenissen op abonnementsniveau, biedt diagnostische logboekregistratie inzicht in de operationele geschiedenis van de resources zelf.

Voorbeelden van diagnostische logging zijn:

* De uitvoeringstijd voor een door de gebruiker gedefinieerde functie
* De antwoordstatuscode van een succesvolle API-aanvraag
* Een app-sleutel ophalen uit een kluis

Ga als volgende voor het inschakelen van diagnostische logboeken voor een instantie:

1. Breng de bron naar boven in azure-portal.
1. Selecteer **Diagnostische instellingen:**

    [![Diagnostische instellingen één](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selecteer **Diagnostische gegevens inschakelen** om gegevens te verzamelen (als deze niet eerder zijn ingeschakeld).
1. Vul de gevraagde velden in en selecteer hoe en waar gegevens worden opgeslagen:

    [![Diagnostische instellingen twee](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Diagnostische logboeken worden vaak opgeslagen met [Azure File Storage](../storage/files/storage-files-deployment-guide.md) en gedeeld met Azure [Monitor-logboeken.](../azure-monitor/log-query/get-started-portal.md) Beide opties kunnen worden geselecteerd.

>[!TIP]
>Gebruik **diagnostische logboeken** voor inzichten in resourcebewerkingen.

## <a name="azure-monitor-and-log-analytics"></a>Azure-monitor- en logboekanalyses

IoT-toepassingen verenigen verschillende bronnen, apparaten, locaties en gegevens in één. Fijnmazige logboekregistratie biedt gedetailleerde informatie over elk specifiek stuk, service of onderdeel van de algehele toepassingsarchitectuur, maar een uniform overzicht is vaak vereist voor onderhoud en foutopsporing.

Azure Monitor bevat de krachtige log-analyseservice, waarmee logboekregistratiebronnen op één locatie kunnen worden bekeken en geanalyseerd. Azure Monitor is daarom zeer nuttig voor het analyseren van logboeken binnen geavanceerde IoT-apps.

Voorbeelden van gebruik zijn:

* Meerdere diagnostische logboekgeschiedenissen opvragen
* Logboeken zien voor verschillende door de gebruiker gedefinieerde functies
* Logboeken weergeven voor twee of meer services binnen een specifiek tijdsbestek

Volledige logboekquery's worden geleverd via [Azure Monitor-logboeken.](../azure-monitor/log-query/log-query-overview.md) Ga als het gaat om het instellen van deze krachtige functies:

1. Zoek naar **Log Analytics** in de Azure-portal.
1. Uw beschikbare **log analytics-werkruimte-exemplaren** worden weergegeven. Kies er een en selecteer **Logboeken** die u wilt opvragen:

    [![Logboekanalyses](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Als u nog geen instantie van een **Log Analytics-werkruimte** hebt, u een werkruimte maken door de knop **Toevoegen te** selecteren:

    [![OMS maken](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Zodra uw **log analytics-werkruimteinstantie** is ingericht, u krachtige query's gebruiken om items in veelvoudenlogboeken te zoeken of te zoeken met behulp van specifieke criteria met behulp van **Logboekbeheer:**

   [![Logboekbeheer](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Lees aan [de slag met query's voor](../azure-monitor/log-query/get-started-queries.md)meer informatie over krachtige querybewerkingen.

> [!NOTE]
> U een vertraging van 5 minuten ondervinden bij het verzenden van gebeurtenissen naar de **werkruimte Log Analytics** voor de eerste keer.

Azure Monitor-logboeken bieden ook krachtige fout- en waarschuwingsmeldingsservices, die kunnen worden bekeken door Diagnose te selecteren **en problemen op te lossen:**

   [![Waarschuwings- en foutmeldingen](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Gebruik **de werkruimte Log Analytics** om logboekgeschiedenissen op te vragen voor meerdere app-functionaliteiten, abonnementen of services.

## <a name="other-options"></a>Andere opties

Azure Digital Twins ondersteunt ook toepassingsspecifieke logboekregistratie en beveiligingscontrole. Lees het [azure-controleartikel](../security/fundamentals/log-audit.md) voor een grondig overzicht van alle Azure-logboekopties die beschikbaar zijn voor uw Azure Digital Twins-exemplaar.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-activiteitslogboeken](../azure-monitor/platform/platform-logs-overview.md).

- Duik dieper in azure diagnostische instellingen door een [overzicht van diagnostische logboeken te](../azure-monitor/platform/platform-logs-overview.md)lezen.

- Lees meer over [Azure Monitor-logboeken](../azure-monitor/log-query/get-started-portal.md).
