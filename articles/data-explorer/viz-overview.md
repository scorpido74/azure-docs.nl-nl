---
title: Gegevensvisualisatie van Azure Data Explorer
description: Meer informatie over de verschillende manieren waarop u uw Azure Data Explorer-gegevens visualiseren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 7b1c7825beb769b610d661cb9644fc3f3919d548
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139059"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Gegevensvisualisatie met Azure Data Explorer 

Azure Data Explorer is een snelle en zeer schaalbare gegevensverkenningsservice voor logboek- en telemetriegegevens die wordt gebruikt om complexe analyseoplossingen te bouwen voor grote hoeveelheden gegevens. Azure Data Explorer integreert met verschillende visualisatietools, zodat u uw gegevens visualiseren en de resultaten in uw hele organisatie delen. Deze gegevens kunnen worden omgezet in bruikbare inzichten om een impact te maken op uw bedrijf.

Datavisualisatie en -rapportage is een cruciale stap in het data-analyseproces. Azure Data Explorer ondersteunt veel BI-services, zodat u de services gebruiken die het beste bij uw scenario en budget passen.

## <a name="kusto-query-language-visualizations"></a>Kusto query taalvisualisaties

De Kusto-querytaal [`render operator`](/azure/kusto/query/renderoperator) biedt verschillende visualisaties, zoals tabellen, cirkeldiagrammen en staafdiagrammen om queryresultaten weer te geven. Queryvisualisaties zijn handig bij het detecteren en voorspellen van anomalieën, machine learning en meer.

## <a name="power-bi"></a>Power BI

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Power BI](https://powerbi.microsoft.com) met verschillende methoden: 

  * [Ingebouwde native Power BI-connector](/azure/data-explorer/power-bi-connector)

  * [Query importeren uit Azure Data Explorer in Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL-query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Microsoft Excel](https://products.office.com/excel) met de [ingebouwde native Excel-connector](excel-connector.md)of [een query](excel-blank-query.md) importeren uit Azure Data Explorer in Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) biedt een Azure Data Explorer-plug-in waarmee u gegevens uit Azure Data Explorer visualiseren. U [stelt Azure Data Explorer in als gegevensbron voor Grafana en visualiseert de gegevens.](/azure/data-explorer/grafana) 

## <a name="kibana"></a>Kibana

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Kibana (de pagina Ontdekken)](https://www.elastic.co/guide/en/kibana/6.8/discover.html) met K2Bridge, een open source-connector. U [stelt Azure Data Explorer in als gegevensbron voor Kibana en visualiseert de gegevens.](/azure/data-explorer/k2bridge)

## <a name="odbc-connector"></a>ODBC-connector

Azure Data Explorer biedt een [ODBC-connector (Open Database Connectivity),](connect-odbc.md) zodat elke toepassing die ODBC ondersteunt, verbinding kan maken met Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Tableau](https://www.tableau.com) met behulp van de [ODBC-connector](/azure/data-explorer/connect-odbc) en [vervolgens de gegevens in Tableau te visualiseren.](tableau.md)

## <a name="qlik"></a>Qlik

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Qlik](https://www.qlik.com) met behulp van de [ODBC-connector](/azure/data-explorer/connect-odbc) en vervolgens Qlik Sense-dashboards te maken en de gegevens te visualiseren. Met behulp van de volgende video u leren azure data explorer-gegevens te visualiseren met Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer biedt de mogelijkheid om verbinding te maken met [Sisense](https://www.sisense.com) met behulp van de JDBC-connector. U [stelt Azure Data Explorer in als gegevensbron voor Sisense en visualiseert de gegevens.](/azure/data-explorer/sisense)

## <a name="redash"></a>Redash

U [Redash](https://redash.io/) gebruiken om dashboards te bouwen en gegevens te visualiseren. [Stel Azure Data Explorer in als gegevensbron voor Redash en visualiseer de gegevens.](/azure/data-explorer/redash)