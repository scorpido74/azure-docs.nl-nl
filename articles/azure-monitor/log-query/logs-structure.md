---
title: Structuur van Azure Monitor logboeken | Microsoft Docs
description: U hebt een logboek query nodig om logboek gegevens op te halen van Azure Monitor.  In dit artikel wordt beschreven hoe nieuwe logboek query's worden gebruikt in Azure Monitor en vindt u concepten die u moet begrijpen voordat u er een kunt maken.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 6ce8470da6b444cedb7bff1d14bcc6448b52fe94
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893634"
---
# <a name="structure-of-azure-monitor-logs"></a>Structuur van Azure Monitor logboeken
De mogelijkheid om snel inzicht te krijgen in uw gegevens met behulp van een [logboek query](log-query-overview.md) is een krachtige functie van Azure monitor. Als u efficiënte en nuttige query's wilt maken, moet u een aantal basis concepten begrijpen, zoals waar de gewenste gegevens zich bevinden en hoe deze worden gestructureerd. In dit artikel worden de basis concepten beschreven die u nodig hebt om aan de slag te gaan.

## <a name="overview"></a>Overzicht
Gegevens in Azure Monitor logboeken worden opgeslagen in een Log Analytics-werk ruimte of een Application Insights-toepassing. Beide worden aangedreven door [Azure Data Explorer](/azure/data-explorer/) wat inhoudt dat ze de krachtige gegevens engine en query taal gebruiken.

Gegevens in beide werk ruimten en toepassingen zijn ingedeeld in tabellen, waarmee verschillende soorten gegevens worden opgeslagen en een eigen unieke set eigenschappen heeft. De meeste [gegevens bronnen](../platform/data-sources.md) schrijven naar hun eigen tabellen in een log Analytics-werk ruimte, terwijl Application Insights naar een vooraf gedefinieerde set tabellen in een Application Insights-toepassing wordt geschreven. Logboek query's zijn zeer flexibel, waarmee u eenvoudig gegevens uit meerdere tabellen kunt combi neren en zelfs een query voor meerdere bronnen kunt gebruiken om gegevens uit tabellen in meerdere werk ruimten te combi neren of om query's te schrijven die werk ruimte-en toepassings gegevens combi neren.

In de volgende afbeelding ziet u voor beelden van gegevens bronnen die worden geschreven naar verschillende tabellen die worden gebruikt in voorbeeld query's.

![Tabellen](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Alle gegevens die worden verzameld door Azure Monitor logboeken, met uitzonde ring van Application Insights, worden opgeslagen in een [log Analytics-werk ruimte](../platform/manage-access.md). U kunt een of meer werk ruimten maken, afhankelijk van uw specifieke vereisten. [Gegevens bronnen](../platform/data-sources.md) , zoals activiteiten logboeken en bron logboeken van Azure-resources, agents op virtuele machines en gegevens van inzichten en bewakings oplossingen, schrijven gegevens naar een of meer werk ruimten die u configureert als onderdeel van de onboarding. Andere services, zoals [Azure Security Center](/azure/security-center/) en [Azure-Sentinel](/azure/sentinel/) , gebruiken ook een log Analytics werk ruimte voor het opslaan van gegevens, zodat deze kunnen worden geanalyseerd met behulp van logboek query's en bewakings gegevens van andere bronnen.

Verschillende soorten gegevens worden opgeslagen in verschillende tabellen in de werk ruimte en elke tabel heeft een unieke set eigenschappen. Een standaardset tabellen wordt toegevoegd aan een werk ruimte wanneer deze wordt gemaakt en er worden nieuwe tabellen toegevoegd voor verschillende gegevens bronnen, oplossingen en services wanneer ze onboarded zijn. U kunt ook aangepaste tabellen maken met behulp van de [Data Collector-API](../platform/data-collector-api.md).

U kunt door de tabellen in een werk ruimte en het bijbehorende schema bladeren in het tabblad **schema** in log Analytics voor de werk ruimte.

![Werkruimte schema](media/scope/workspace-schema.png)

Gebruik de volgende query om de tabellen in de werk ruimte weer te geven en het aantal records dat elke vorige dag is verzameld. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Zie de documentatie voor elke gegevens bron voor meer informatie over de tabellen die ze maken. Voor beelden zijn artikelen voor [agent gegevens bronnen](../platform/agent-data-sources.md), [bron logboeken](../platform/diagnostic-logs-schema.md)en [bewakings oplossingen](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Werkruimte machtigingen
Zie de [implementatie van een Azure monitor-logboeken ontwerpen](../platform/design-logs-deployment.md) om inzicht te krijgen in de strategie en aanbevelingen voor het toegangs beheer om toegang te bieden tot gegevens in een werk ruimte. Naast het verlenen van toegang tot de werk ruimte zelf, kunt u de toegang tot afzonderlijke tabellen beperken met behulp van het [tabel niveau RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Application Insights toepassing
Wanneer u een toepassing maakt in Application Insights, wordt er automatisch een bijbehorende toepassing gemaakt in Azure Monitor Logboeken. Er is geen configuratie vereist om gegevens te verzamelen. de toepassing schrijft automatisch bewakings gegevens zoals pagina weergaven, aanvragen en uitzonde ringen.

In tegens telling tot een Log Analytics-werk ruimte heeft een Application Insights-toepassing een vaste set tabellen. U kunt geen andere gegevens bronnen configureren om naar de toepassing te schrijven, zodat er geen extra tabellen kunnen worden gemaakt. 

| Tabel | Beschrijving | 
|:---|:---|
| availabilityResults | Samenvattings gegevens van beschikbaarheids testen. |
| browserTimings      | Gegevens over client prestaties, zoals de tijd die nodig is om de binnenkomende gegevens te verwerken. |
| customEvents        | Aangepaste gebeurtenissen die door uw toepassing zijn gemaakt. |
| customMetrics       | Aangepaste metrische gegevens die door uw toepassing zijn gemaakt. |
| afhankelijkheden        | Aanroepen van de toepassing naar externe onderdelen. |
| Uitzonderingen          | Uitzonde ringen die worden veroorzaakt door de runtime van de toepassing. |
| Page views           | Gegevens over elke website weergave met browser informatie. |
| Performance Counters | Prestatie metingen van de reken resources die de toepassing ondersteunen. |
| aanvragen            | Details van elke toepassings aanvraag.  |
| Traceringen              | Resultaten van gedistribueerde tracering. |

U kunt het schema voor elke tabel weer geven op het tabblad **schema** in log Analytics voor de toepassing.

![Toepassings schema](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standaardeigenschappen
Elke tabel in Azure Monitor logboeken heeft een eigen schema, maar er zijn standaard eigenschappen gedeeld door alle tabellen. Zie de [Standaard eigenschappen in azure monitor logboeken](../platform/log-standard-properties.md) voor meer informatie.

| Log Analytics-werkruimte | Application Insights toepassing | Beschrijving |
|:---|:---|:---|
| TimeGenerated | tijdstempel  | De datum en tijd waarop de record is gemaakt. |
| Type          | itemType   | Naam van de tabel waaruit het record is opgehaald. |
| _ResourceId   |            | De unieke id voor de resource waaraan de record is gekoppeld. |
| _IsBillable   |            | Hiermee geeft u op of opgenomen gegevens Factureerbaar zijn. |
| _BilledSize   |            | Hiermee geeft u de grootte in bytes aan gegevens die worden gefactureerd. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het gebruik van log Analytics om Zoek opdrachten in Logboeken te maken en te bewerken](../log-query/portals.md).
- Bekijk een [zelf studie over het schrijven van query's](../log-query/get-started-queries.md) met behulp van de nieuwe query taal.
