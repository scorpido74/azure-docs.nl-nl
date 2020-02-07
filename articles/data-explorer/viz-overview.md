---
title: Gegevens visualisatie van Azure Data Explorer
description: Meer informatie over de verschillende manieren waarop u uw Azure Data Explorer-gegevens kunt visualiseren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 32ccc5ad236c87d3609798a8432db14ee440d067
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064560"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Gegevens visualisatie met Azure Data Explorer 

Azure Data Explorer is een snelle en zeer schaal bare service voor gegevens onderzoek voor logboek-en telemetriegegevens die worden gebruikt voor het bouwen van complexe analyse oplossingen voor enorme hoeveel heden gegevens. Azure Data Explorer kan worden geïntegreerd met verschillende visualisatie hulpprogramma's, zodat u uw gegevens kunt visualiseren en de resultaten van uw organisatie in uw hele bedrijf moet delen. Deze gegevens kunnen worden omgezet in inzichten die op actie kan worden uitgevoerd om een invloed te hebben op uw bedrijf.

Gegevens visualisatie en-rapportage is een belang rijke stap in het proces van gegevens analyse. Azure Data Explorer ondersteunt een groot aantal BI-Services, zodat u het kunt gebruiken dat het beste past bij uw scenario en budget.

## <a name="kusto-query-language-visualizations"></a>Visualisaties van Kusto-query taal

De Kusto-query taal [`render operator`](/azure/kusto/query/renderoperator) biedt verschillende visualisaties, zoals tabellen, cirkel diagrammen en staaf diagrammen, om query resultaten weer te gegeven. Query visualisaties zijn handig in anomalie detectie en prognoses, machine learning en meer.

## <a name="power-bi"></a>Power BI

Azure Data Explorer biedt de mogelijkheid om met behulp van verschillende methoden verbinding te maken met [Power bi](https://powerbi.microsoft.com) : 

  * [Ingebouwde systeem eigen Power BI-connector](/azure/data-explorer/power-bi-connector)

  * [Query's importeren vanuit Azure Data Explorer naar Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [micro soft Excel](https://products.office.com/excel) met behulp van de [ingebouwde systeem eigen Excel-connector](excel-connector.md)of [een query](excel-blank-query.md) vanuit Azure Data Explorer te importeren in Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) biedt een Azure Data Explorer-invoeg toepassing waarmee u gegevens kunt visualiseren vanuit Azure Data Explorer. U hebt [Azure Data Explorer ingesteld als gegevens bron voor Grafana en vervolgens de gegevens visualiseren](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>ODBC-connector

Azure Data Explorer biedt een [Open Database Connectivity (ODBC)-connector](connect-odbc.md) , zodat elke toepassing die ODBC ondersteunt, verbinding kan maken met Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [tableau](https://www.tableau.com) met behulp van de [ODBC-Connector](/azure/data-explorer/connect-odbc) en vervolgens [de gegevens in tableau te visualiseren](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Qlik](https://www.qlik.com) met behulp van de [ODBC-Connector](/azure/data-explorer/connect-odbc) en vervolgens Qlik Sense-Dash boards te maken en de gegevens te visualiseren. Met behulp van de volgende video leert u hoe u Azure Data Explorer-gegevens kunt visualiseren met Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Sisense](https://www.sisense.com) met behulp van de JDBC-connector. U hebt [Azure Data Explorer ingesteld als gegevens bron voor Sisense en vervolgens de gegevens visualiseren](/azure/data-explorer/sisense).

## <a name="redash"></a>Redash

U kunt [restreep](https://redash.io/) gebruiken om Dash boards te maken en gegevens te visualiseren. [Stel Azure Data Explorer in als gegevens bron voor het opnieuw insluiten en Visualiseer vervolgens de gegevens](/azure/data-explorer/redash).