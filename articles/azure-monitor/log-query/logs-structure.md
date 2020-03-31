---
title: Structuur van Azure-monitorlogboeken | Microsoft Documenten
description: U hebt een logboekquery nodig om logboekgegevens op te halen uit Azure Monitor.  In dit artikel wordt beschreven hoe nieuwe logboekquery's worden gebruikt in Azure Monitor en worden concepten beschreven die u moet begrijpen voordat u er een maakt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 1d647ba7e8d4f0e29252dfff95099e39bab87895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662073"
---
# <a name="structure-of-azure-monitor-logs"></a>Structuur van Azure-monitorlogboeken
De mogelijkheid om snel inzicht te krijgen in uw gegevens met behulp van een [logboekquery](log-query-overview.md) is een krachtige functie van Azure Monitor. Als u efficiënte en nuttige query's wilt maken, moet u een aantal basisconcepten begrijpen, zoals waar de gewenste gegevens zich bevinden en hoe deze zijn gestructureerd. Dit artikel bevat de basisconcepten die u nodig hebt om aan de slag te gaan.

## <a name="overview"></a>Overzicht
Gegevens in Azure Monitor Logs worden opgeslagen in een log Analytics-werkruimte of een Application Insights-toepassing. Beide worden aangedreven door [Azure Data Explorer,](/azure/data-explorer/) wat betekent dat ze gebruikmaken van de krachtige data-engine en querytaal.

Gegevens in zowel werkruimten als toepassingen worden ingedeeld in tabellen, die elk verschillende soorten gegevens opslaan en zijn eigen unieke set eigenschappen hebben. De meeste [gegevensbronnen](../platform/data-sources.md) schrijven naar hun eigen tabellen in een Log Analytics-werkruimte, terwijl Application Insights naar een vooraf gedefinieerde set tabellen in een Application Insights-toepassing schrijft. Logboekquery's zijn zeer flexibel, zodat u eenvoudig gegevens uit meerdere tabellen combineren en zelfs een query met meerdere bronnen gebruiken om gegevens uit tabellen in meerdere werkruimten te combineren of query's te schrijven die werkruimte- en toepassingsgegevens combineren.

In de volgende afbeelding ziet u voorbeelden van gegevensbronnen die naar verschillende tabellen schrijven die worden gebruikt in voorbeeldquery's.

![Tabellen](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Log Analytics-werkruimte
Alle gegevens die worden verzameld door Azure Monitor Logs, behalve Application Insights, worden opgeslagen in een [log analytics-werkruimte.](../platform/manage-access.md) U een of meer werkruimten maken, afhankelijk van uw specifieke vereisten. [Gegevensbronnen](../platform/data-sources.md) zoals activiteitslogboeken en bronlogboeken van Azure-bronnen, agents op virtuele machines en gegevens uit inzichten en bewakingsoplossingen schrijven gegevens naar een of meer werkruimten die u configureert als onderdeel van hun onboarding. Andere services zoals [Azure Security Center](/azure/security-center/) en Azure [Sentinel](/azure/sentinel/) gebruiken ook een Log Analytics-werkruimte om hun gegevens op te slaan, zodat deze kunnen worden geanalyseerd met behulp van logboekquery's, samen met bewakingsgegevens uit andere bronnen.

Verschillende soorten gegevens worden opgeslagen in verschillende tabellen in de werkruimte en elke tabel heeft een unieke set eigenschappen. Er wordt een standaardset tabellen toegevoegd aan een werkruimte wanneer deze wordt gemaakt en er worden nieuwe tabellen toegevoegd voor verschillende gegevensbronnen, oplossingen en services die aan boord zijn. U ook aangepaste tabellen maken met de [API voor gegevensverzamelaar.](../platform/data-collector-api.md)

U door de tabellen in een werkruimte en hun schema bladeren op het tabblad **Schema** in Logboekanalyse voor de werkruimte.

![Werkruimteschema](media/scope/workspace-schema.png)

Gebruik de volgende query om de tabellen in de werkruimte en het aantal records dat in elk van de vorige dag is verzameld, weer te geven. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Zie documentatie voor elke gegevensbron voor meer informatie over de tabellen die ze maken. Voorbeelden hiervan zijn artikelen voor [agentgegevensbronnen,](../platform/agent-data-sources.md) [bronlogboeken](../platform/diagnostic-logs-schema.md)en [bewakingsoplossingen](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Machtigingen voor werkruimtes
Zie [Het ontwerpen van een Azure Monitor Logs-implementatie](../platform/design-logs-deployment.md) om inzicht te krijgen in de strategie en aanbevelingen voor toegangscontrole om toegang te bieden tot gegevens in een werkruimte. Naast het verlenen van toegang tot de werkruimte zelf, u de toegang tot afzonderlijke tabellen beperken met [Behulp van Tabelniveau RBAC](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Toepassing Application Insights
Wanneer u een toepassing maakt in Application Insights, wordt een overeenkomstige toepassing automatisch gemaakt in Azure Monitor Logs. Er is geen configuratie vereist om gegevens te verzamelen en de toepassing schrijft automatisch bewakingsgegevens zoals paginaweergaven, aanvragen en uitzonderingen.

In tegenstelling tot een Log Analytics-werkruimte heeft een toepassing Application Insights een vaste set tabellen. U geen andere gegevensbronnen configureren om naar de toepassing te schrijven, zodat er geen extra tabellen kunnen worden gemaakt. 

| Tabel | Beschrijving | 
|:---|:---|
| beschikbaarheidResultaten | Overzichtsgegevens van beschikbaarheidstests. |
| browserTimings      | Gegevens over de prestaties van de client, zoals de tijd die nodig is om de binnenkomende gegevens te verwerken. |
| aangepaste gebeurtenissen        | Aangepaste gebeurtenissen die door uw toepassing zijn gemaakt. |
| customMetrics (customMetrics)       | Aangepaste statistieken die door uw toepassing zijn gemaakt. |
| Afhankelijkheden        | Oproepen van de toepassing naar externe componenten. |
| Uitzonderingen          | Uitzonderingen die door de runtime van de toepassing worden geworpen. |
| Paginaweergaves           | Gegevens over elke websiteweergave met browserinformatie. |
| prestatietellers | Prestatiemetingen van de rekenbronnen die de toepassing ondersteunen. |
| Verzoeken            | Details van elk aanvraagverzoek.  |
| Sporen              | Resultaten van gedistribueerde tracering. |

U het schema voor elke tabel bekijken op het tabblad **Schema** in Log Analytics voor de toepassing.

![Toepassingsschema](media/scope/application-schema.png)

## <a name="standard-properties"></a>Standaardeigenschappen
Hoewel elke tabel in Azure Monitor Logs een eigen schema heeft, zijn er standaardeigenschappen die door alle tabellen worden gedeeld. Zie [Standaardeigenschappen in Azure Monitor-logboeken](../platform/log-standard-properties.md) voor meer informatie over elk.

| Log Analytics-werkruimte | Toepassing Application Insights | Beschrijving |
|:---|:---|:---|
| TimeGenerated | tijdstempel  | Datum en tijd van het maken van de record. |
| Type          | itemType   | Naam van de tabel waaruit de record is opgehaald. |
| _ResourceId   |            | Unieke id voor de resource waar de record aan is gekoppeld. |
| _IsBillable   |            | Hiermee geeft u op of ingenomen gegevens factureerbaar zijn. |
| _BilledSize   |            | Hiermee geeft u de grootte op in bytes van gegevens die worden gefactureerd. |

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik [van Logboekanalyse om logboekzoekopdrachten te maken en te bewerken.](../log-query/portals.md)
- Bekijk een [zelfstudie over het schrijven van query's](../log-query/get-started-queries.md) met behulp van de nieuwe querytaal.
