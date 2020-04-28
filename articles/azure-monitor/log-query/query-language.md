---
title: Azure Monitor-logboek query's | Microsoft Docs
description: Verwijzingen naar bronnen voor het schrijven van logboek query's in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631785"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor-logboek query's

Azure Monitor-logboeken zijn gebaseerd op Azure Data Explorer en Azure Monitor logboek query's gebruiken een versie van dezelfde Kusto-query taal. De [documentatie voor de Kusto-query taal](/azure/kusto/query) bevat alle Details voor de taal en moet uw primaire resource zijn voor het schrijven van Azure monitor-logboek query's. Deze pagina bevat koppelingen naar andere bronnen voor het leren van query's en over verschillen met de Azure Monitor implementatie van de taal.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Aan de slag

- [Aan de slag met Azure Monitor Log Analytics](get-started-portal.md) is een les voor het schrijven van query's en het werken met resultaten in de Azure Portal.
- [Aan de slag met Azure monitor-logboek query's](get-started-queries.md) is een les voor het schrijven van query's met behulp van Azure monitor-logboek gegevens.

## <a name="concepts"></a>Concepten

- Het [analyseren van logboek gegevens in azure monitor](../../azure-monitor/log-query/log-query-overview.md) biedt een beknopt overzicht van logboek query's en beschrijft hoe Azure monitor logboek gegevens zijn gestructureerd.
- Het [weer geven en analyseren van logboek gegevens in azure monitor](../../azure-monitor/log-query/portals.md) een uitleg van de portals waar u logboek query's maakt en uitvoert.

## <a name="reference"></a>Naslaginformatie

- [Naslag informatie over de query taal](/azure/kusto/query) is de volledige taal verwijzing voor de Kusto-query taal.
- [Azure monitor taal verschillen in de logboek query](data-explorer-difference.md) beschrijft de verschillen tussen versies van de query taal Kusto.
- [In de standaard eigenschappen in azure monitor logboek records](../../azure-monitor/platform/log-standard-properties.md) worden eigenschappen beschreven die standaard zijn voor alle Azure monitor logboek gegevens.
- [Query's voor meerdere resources uitvoeren in azure monitor](../../azure-monitor/log-query/cross-workspace-query.md) beschrijft hoe u logboek query's schrijft die gebruikmaken van gegevens uit meerdere log Analytics-werk ruimten en Application Insights toepassingen.

## <a name="examples"></a>Voorbeelden

- [Azure monitor query-voor beelden van Logboeken](examples.md) bevatten voorbeeld query's met behulp van Azure monitor-logboek gegevens.

## <a name="lessons"></a>Lessen

- [Werken met teken reeksen in azure monitor-logboek query's](string-operations.md) wordt beschreven hoe u met teken reeks gegevens kunt werken.
- [Als u werkt met datum-en tijd waarden in azure monitor-logboek query's](datetime-operations.md) , wordt beschreven hoe u met datum-en tijd gegevens kunt werken.
- [Aggregaties in azure monitor logboek query's](aggregations.md) en [Geavanceerde aggregaties in azure monitor-logboek query's](advanced-aggregations.md) beschrijven hoe u gegevens kunt samen voegen en samenvatten.
- [Samen voegen in azure monitor-logboek query's](joins.md) wordt beschreven hoe u gegevens uit meerdere tabellen samenvoegt.
- [Werken met JSON en gegevens structuren in azure monitor-logboek query's](json-data-structures.md) wordt beschreven hoe u JSON-gegevens kunt parseren.
- [Geavanceerde logboek Query's schrijven in azure monitor](advanced-query-writing.md) beschrijft strategieën voor het maken van complexe query's en het opnieuw gebruiken van code.
- [Maken van grafieken en diagrammen vanuit Azure monitor-logboek query's](charts.md) wordt beschreven hoe u gegevens in een logboek query kunt visualiseren.

## <a name="cheatsheets"></a>Cheatsheets

- [SQL to Azure monitor log query](sql-cheatsheet.md) helpt gebruikers die al bekend zijn met SQL.
- [Splunk Azure monitor-logboek query](splunk-cheatsheet.md) helpt gebruikers die al bekend zijn met Splunk.

## <a name="next-steps"></a>Volgende stappen

- Open de volledige [referentie documentatie voor de Kusto-query taal](/azure/kusto/query/).
