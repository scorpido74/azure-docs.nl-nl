---
title: Nuttige bronnen bij het werken met Azure Sentinel| Microsoft Documenten
description: Dit document biedt u een lijst met nuttige bronnen wanneer u met Azure Sentinel werkt.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585268"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Nuttige bronnen voor het werken met Azure Sentinel



In dit artikel vindt u bronnen waarmee u meer informatie krijgen over het werken met Azure Sentinel.

Azure Logic Apps-connectors:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controle en rapportage
Controlelogboeken van Azure Sentinel worden bijgehouden in [Azure Activity Logs](../azure-monitor/platform/platform-logs-overview.md).

De volgende ondersteunde bewerkingen kunnen worden gecontroleerd.

|Naam van bewerking|    Resourcetype|
|----|----|
|Werkmap maken of bijwerken  |Microsoft.Insights/werkmappen|
|Werkmap verwijderen    |Microsoft.Insights/werkmappen|
|Werkstroom instellen   |Microsoft.Logic/werkstromen|
|Werkstroom verwijderen    |Microsoft.Logic/werkstromen|
|Opgeslagen zoekopdracht maken    |Microsoft.OperationalInsights/werkruimten/opgeslagenzoekopdrachten|
|Opgeslagen zoekopdracht verwijderen    |Microsoft.OperationalInsights/werkruimten/opgeslagenzoekopdrachten|
|Waarschuwingsregels bijwerken |Microsoft.SecurityInsights/alertRegels|
|Waarschuwingsregels verwijderen |Microsoft.SecurityInsights/alertRegels|
|Reactieacties voor waarschuwingsregels bijwerken |Microsoft.SecurityInsights/alertRegels/acties|
|Reactieacties voor waarschuwingsregels verwijderen |Microsoft.SecurityInsights/alertRegels/acties|
|Bladwijzers bijwerken   |Microsoft.SecurityInsights/bladwijzers|
|Bladwijzers verwijderen   |Microsoft.SecurityInsights/bladwijzers|
|Aanvragen bijwerken   |Microsoft.SecurityInsights/Cases|
|Case-onderzoek bijwerken  |Microsoft.SecurityInsights/Cases/onderzoeken|
|Hoofdletters maken   |Microsoft.SecurityInsights/Cases/comments|
|Gegevensconnectors bijwerken |Microsoft.SecurityInsights/dataConnectors|
|Gegevensconnectors verwijderen |Microsoft.SecurityInsights/dataConnectors|
|Instellingen bijwerken    |Microsoft.SecurityInsights/instellingen|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Controle- en rapportagegegevens weergeven in Azure Sentinel

U deze gegevens bekijken door deze te streamen vanuit het Azure Activity-logboek naar Azure Sentinel, waar u vervolgens onderzoek en analyses uitvoeren.

1. Verbind de [gegevensbron Azure Activity.](connect-azure-activity.md) Hierna worden auditgebeurtenissen gestreamd naar een nieuwe tabel in het **scherm Logboeken** met de naam AzureActivity.
2. Query vervolgens de gegevens met behulp van KQL, zoals u zou elke andere tabel.



## <a name="vendor-documentation"></a>Leveranciersdocumentatie

| **Leverancier**  | **Incident gebruiken in Azure Sentinel** | **Koppeling**|
|----|----|----|
| GitHub| Wordt gebruikt om toegang te krijgen tot communitypagina| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto PaloAlto| CEF configureren| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight (PluralSight) | Cursus over Kusto-querytaal| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs en forums

Plaats uw vragen op de [TechCommunity-ruimte](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) voor Azure Sentinel.

Bekijk Azure Sentinel-blogberichten van de [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) en [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u een lijst met bronnen die handig zijn wanneer u met Azure Sentinel werkt. U vindt aanvullende informatie over Azure-beveiliging en -naleving op het [Microsoft Azure Security and Compliance-blog.](https://blogs.msdn.com/b/azuresecurity/)
