---
title: Gegevens visualiseren van Azure Monitor | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de beschik bare methoden voor het visualiseren van metrische gegevens en logboeken die zijn opgeslagen in Azure Monitor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/24/2018
ms.openlocfilehash: c350cb1f8e7343f6d8e7a416aa0bba71388eaa86
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597839"
---
# <a name="visualizing-data-from-azure-monitor"></a>Gegevens visualiseren van Azure Monitor
Dit artikel bevat een overzicht van de beschik bare methoden voor het visualiseren van logboek-en metrische gegevens die zijn opgeslagen in Azure Monitor.

Visualisaties zoals grafieken en grafieken kunnen u helpen bij het analyseren van uw bewakings gegevens om in te zoomen op problemen en patronen te identificeren. Afhankelijk van het hulp programma dat u gebruikt, hebt u mogelijk ook de mogelijkheid om visualisaties te delen met andere gebruikers binnen en buiten uw organisatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-dashboards"></a>Azure-Dash boards
[Azure-Dash boards](../azure-portal/azure-portal-dashboards.md) zijn de primaire Dashboard technologie voor Azure. Ze zijn vooral handig voor het leveren van een enkel glas venster over uw Azure-infra structuur en-services, zodat u snel belang rijke problemen kunt identificeren.

![Dashboard](media/visualizations/dashboard.png)

### <a name="advantages"></a>Voordelen
- Diep gaande integratie in Azure. Visualisaties kunnen worden vastgemaakt aan dash boards van meerdere Azure-pagina's, waaronder Metrics Explorer, Log Analytics en Application Insights.
- Ondersteunt metrische gegevens en Logboeken.
- Combi neer gegevens uit meerdere bronnen, inclusief uitvoer van [Metrics Explorer](platform/metrics-charts.md), [logboek query's](log-query/log-query-overview.md)en [kaarten](app/app-map.md) en beschik baarheid in Application Insights.
- Optie voor persoonlijke of gedeelde Dash boards. Geïntegreerd met Azure [Role Based Authentication (RBAC)](../role-based-access-control/overview.md).
- Automatisch vernieuwen. Het vernieuwen van metrische gegevens is afhankelijk van het tijds bereik met mini maal vijf minuten. Logboeken worden elk uur vernieuwd, met een optie voor hand matige vernieuwing op aanvraag door te klikken op het pictogram Vernieuwen op een bepaalde visualisatie of door het volledige dash board te vernieuwen.
- Parametrized metrische Dash boards met tijds tempel en aangepaste para meters.
- Flexibele indelings opties.
- Modus volledig scherm.


### <a name="limitations"></a>Beperkingen
- Beperkte controle over logboek visualisaties zonder ondersteuning voor gegevens tabellen. Het totale aantal gegevens reeksen is beperkt tot 10 met verdere gegevens reeksen die zijn gegroepeerd onder een _andere_ Bucket.
- Er zijn geen aangepaste parameter ondersteuning voor logboek grafieken.
- Logboek grafieken zijn beperkt tot de afgelopen 30 dagen.
- Logboek grafieken kunnen alleen worden vastgemaakt aan gedeelde Dash boards.
- Geen interactiviteit met Dashboard gegevens.
- Beperkt context-inzoomen.

## <a name="azure-monitor-views"></a>Azure Monitor weergaven
Met [weer gaven in azure monitor](platform/view-designer.md) kunt u aangepaste visualisaties met logboek gegevens maken. Deze worden gebruikt door [bewakings oplossingen](insights/solutions.md) om de verzamelde gegevens te presen teren.

![Weergeven](media/visualizations/view.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties voor logboek gegevens.
- Weer gaven exporteren en importeren om ze over te dragen naar andere resource groepen en-abonnementen.
- Kan worden geïntegreerd in Azure Monitor-beheer model met werk ruimten en bewakings oplossingen.
- [Filters](platform/view-designer-filters.md) voor aangepaste para meters.
- Interactief, ondersteunt inzoomen op meerdere niveaus (weer gave van inzoomen in een andere weer gave)

### <a name="limitations"></a>Beperkingen
- Ondersteunt logboeken maar geen metrische gegevens.
- Geen persoonlijke weer gaven. Beschikbaar voor alle gebruikers met toegang tot de werk ruimte.
- Geen automatische vernieuwing.
- Beperkte indelings opties.
- Geen ondersteuning voor het uitvoeren van query's in meerdere werk ruimten of Application Insights toepassingen.
- Query's zijn beperkt tot de antwoord grootte tot 8 MB en de uitvoer tijd van de query van 110 seconden.


## <a name="workbooks"></a>Werkmappen
[Werkmappen](../azure-monitor/app/usage-workbooks.md) zijn interactieve documenten die diep inzicht geven in uw gegevens, onderzoek en samen werking binnen het team. Specifieke voor beelden waarin werkmappen handig zijn, zijn probleemoplossings richtlijnen en incident postmortem.

![Werkmap](media/visualizations/workbook.png)

### <a name="advantages"></a>Voordelen
- Ondersteunt metrische gegevens en Logboeken.
- Biedt ondersteuning voor para meters die interactieve rapporten inschakelen waarbij een element in een tabel wordt geselecteerd, worden gekoppelde grafieken en visualisaties dynamisch bijgewerkt.
- Document-achtige stroom.
- Optie voor persoonlijke of gedeelde werkmappen.
- Eenvoudige, gebruiks vriendelijke ontwerp ervaring.
- Sjablonen ondersteunen galerie met open bare GitHub-sjablonen.

### <a name="limitations"></a>Beperkingen
- Geen automatische vernieuwing.
- Geen compacte indeling zoals Dash boards, waardoor werkmappen minder nuttig zijn als één glas venster. Meer bedoeld voor meer inzicht.


## <a name="power-bi"></a>Power BI
[Power bi](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) is vooral handig voor het maken van zakelijke Dash boards en rapporten, evenals rapporten waarmee kpi's trends op lange termijn worden geanalyseerd. U kunt [de resultaten van een logboek query importeren](platform/powerbi.md) in een Power bi-gegevensset, zodat u kunt profiteren van de functies, zoals het combi neren van gegevens uit verschillende bronnen en het delen van rapporten op de web-en mobiele apparaten.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties.
- Uitgebreide interactiviteit, inclusief inzoomen en kruislings filteren.
- Eenvoudig te delen binnen uw organisatie.
- Integratie met andere gegevens uit meerdere gegevens bronnen.
- Betere prestaties met de resultaten in de cache van een kubus.


### <a name="limitations"></a>Beperkingen
- Ondersteunt logboeken maar geen metrische gegevens.
- Geen Azure-integratie. Kan geen Dash boards en modellen beheren via Azure Resource Manager.
- Query resultaten moeten worden geïmporteerd in Power BI model om te configureren. Beperking van de resultaat grootte en het vernieuwen.
- Beperkte gegevens vernieuwing van acht keer per dag.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) is een open platform dat Excel in operationele Dash boards bevat. Dit is met name handig voor het detecteren en isoleren en uitsorteren van operationele incidenten. U kunt [Grafana Azure monitor gegevens bron-invoeg toepassing](platform/grafana-plugin.md) toevoegen aan uw Azure-abonnement om uw Azure-metrische gegevens te visualiseren.

![Grafana](media/visualizations/grafana.png)

### <a name="advantages"></a>Voordelen
- Uitgebreide visualisaties.
- Uitgebreid ecosysteem van gegevens bronnen.
- Gegevens interactiviteit, inclusief inzoomen.
- Ondersteunt para meters.

### <a name="limitations"></a>Beperkingen
- Geen Azure-integratie. Kan geen Dash boards en modellen beheren via Azure Resource Manager.
- Kosten ter ondersteuning van extra Grafana-infra structuur of extra kosten voor Grafana-Cloud.


## <a name="build-your-own-custom-application"></a>Uw eigen aangepaste toepassing bouwen
U kunt toegang krijgen tot gegevens in logboek-en metrische gegevens in Azure Monitor via hun API met behulp van elke REST-client, waarmee u uw eigen aangepaste websites en toepassingen kunt bouwen.

### <a name="advantages"></a>Voordelen
- Volledige flexibiliteit in de gebruikers interface, visualisatie, interactiviteit en functies.
- Metrische gegevens combi neren en gegevens in een logboek vastleggen met andere bronnen.

### <a name="disadvantages"></a>Nadelen
- Aanzienlijke technische inspanning vereist.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [gegevens die worden verzameld door Azure monitor](platform/data-platform.md).
- Meer informatie over [Azure-Dash boards](../azure-portal/azure-portal-dashboards.md).
- Meer informatie over [weer gaven in azure monitor](platform/view-designer.md).
- Meer informatie over [werkmappen](../azure-monitor/app/usage-workbooks.md).
- Meer informatie over het [importeren van logboek gegevens in Power bi](../azure-monitor/platform/powerbi.md).
- Meer informatie over de [Grafana Azure monitor-invoeg toepassing voor gegevens bronnen](../azure-monitor/platform/grafana-plugin.md).

