---
title: Nuttige bronnen bij het werken met Azure Sentinel | Microsoft Docs
description: In dit document vindt u een lijst met nuttige resources wanneer u werkt met Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: rkarlin
ms.openlocfilehash: fe0eb6c2305dd01d8ff3df5be7056cbeae92fff2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74762714"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nuttige bronnen voor het werken met Azure Sentinel



Dit artikel bevat een lijst met bronnen die u kunnen helpen om meer informatie te krijgen over het werken met Azure Sentinel.

Azure Logic Apps-connectors: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controle en rapportage
Audit logboeken van Azure Sentinel worden onderhouden in [Azure-activiteiten logboeken](../azure-monitor/platform/activity-logs-overview.md).

De volgende ondersteunde bewerkingen kunnen worden gecontroleerd.

|Naam van bewerking|    Resourcetype|
|----|----|
|Werkmap maken of bijwerken  |Micro soft. Insights/werkmappen|
|Werkmap verwijderen    |Micro soft. Insights/werkmappen|
|Werk stroom instellen   |Micro soft. Logic/werk stromen|
|Werk stroom verwijderen    |Micro soft. Logic/werk stromen|
|Opgeslagen zoek opdracht maken    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Opgeslagen zoek opdracht verwijderen    |Micro soft. OperationalInsights/werk ruimten/savedSearches|
|Dash board instellen  |Micro soft. Portal/Dash boards|
|Dash board verwijderen   |Micro soft. Portal/Dash boards|
|Waarschuwings regels bijwerken |Micro soft. SecurityInsights/alertRules|
|Waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules|
|Reactie acties van waarschuwings regel bijwerken |Micro soft. SecurityInsights/alertRules|
|Reactie acties voor waarschuwings regels verwijderen |Micro soft. SecurityInsights/alertRules|
|Blad wijzers bijwerken   |Micro soft. SecurityInsights/blad wijzers|
|Blad wijzers verwijderen   |Micro soft. SecurityInsights/blad wijzers|
|Cases bijwerken   |Micro soft. SecurityInsights/cases|
|Case-onderzoek bijwerken  |Micro soft. SecurityInsights/cases|
|Case-opmerkingen maken   |Micro soft. SecurityInsights/cases|
|Gegevens connectors bijwerken |Micro soft. SecurityInsights/dataConnectors|
|Gegevens connectors verwijderen |Micro soft. SecurityInsights/dataConnectors|
|Update-instellingen    |Micro soft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Audit-en rapportage gegevens weer geven in azure Sentinel

U kunt deze gegevens weer geven door deze vanuit het Azure-activiteiten logboek te streamen naar Azure Sentinel, waar u vervolgens onderzoek en analyses kunt uitvoeren.

1. Verbind de gegevens bron van de [Azure-activiteit](connect-azure-activity.md) . Nadat u dit hebt gedaan, worden controle gebeurtenissen gestreamd naar een nieuwe tabel in het scherm **Logboeken** met de naam AzureActivity.
2. Vervolgens voert u een query uit op de gegevens met behulp van KQL, zoals u zou doen met een andere tabel.



## <a name="vendor-documentation"></a>Documentatie van de leverancier

| **Leverancier**  | **Incident gebruiken in azure-Sentinel** | **Koppeling**|
|----|----|----|
| GitHub| Wordt gebruikt voor toegang tot de community-pagina| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF configureren| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto-query taal cursus| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs en forums

Plaats uw vragen op de [TechCommunity ruimte](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) voor Azure Sentinel.

Bekijk Azure Sentinel-blog berichten van de [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) en [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u een lijst met resources die handig zijn wanneer u met Azure Sentinel werkt. U vindt aanvullende informatie over de beveiliging en naleving van Azure in de [Microsoft Azure beveiligings-en nalevings blog](https://blogs.msdn.com/b/azuresecurity/).
