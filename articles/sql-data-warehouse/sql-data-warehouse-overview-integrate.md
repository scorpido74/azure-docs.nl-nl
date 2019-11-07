---
title: Geïntegreerde oplossingen bouwen
description: Hulpprogram ma's en partners met oplossingen die zijn geïntegreerd met Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e159e0e254ae8b2515515dfaeb2c514e0f25e0b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685640"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Andere services integreren met SQL Data Warehouse
Naast de kern functionaliteit van SQL Data Warehouse kunnen gebruikers met veel van de andere services integreren in Azure. Enkele van deze services zijn:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse blijft integreren met meer services in Azure en meer [integratie partners](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
Power BI-integratie kunt u de reken kracht van SQL Data Warehouse combi neren met de dynamische rapportage en visualisatie van Power BI. Power BI-integratie bevat momenteel:

* **Direct Connect**: een geavanceerdere verbinding met logische pushdown tegen SQL Data Warehouse. Pushdown biedt snellere analyse op grotere schaal.
* **Open in Power bi**: met de knop openen in Power bi worden instantie gegevens door gegeven aan Power BI voor een vereenvoudigde manier om verbinding te maken.

Zie [integreren met Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md)of de [Power bi documentatie](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)voor meer informatie.

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory biedt gebruikers een beheerd platform voor het maken van complexe uitpakken en laad pijplijnen. De integratie van SQL Data Warehouse met Azure Data Factory omvat:

* **Opgeslagen procedures**: de uitvoering van opgeslagen procedures op SQL Data Warehouse organiseren.
* **Kopiëren**: gebruik ADF om gegevens naar SQL Data Warehouse te verplaatsen. Met deze bewerking kan het standaard mechanisme voor gegevens verplaatsing van ADF of poly base onder de kaften worden gebruikt. 

Zie [integreren met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json)voor meer informatie.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning is een volledig beheerde analyse service waarmee u ingewikkelde modellen kunt maken met behulp van een groot aantal voorspellende hulpprogram ma's. SQL Data Warehouse wordt ondersteund als bron en doel voor deze modellen met de volgende functionaliteit:

* **Gegevens lezen:** Verstuur modellen op schaal met behulp van T-SQL op basis van SQL Data Warehouse.
* **Gegevens schrijven:** Wijzigingen van een model door voeren in SQL Data Warehouse.

Zie [integreren met Azure machine learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)voor meer informatie.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics is een complexe, volledig beheerde infra structuur voor het verwerken en gebruiken van gebeurtenis gegevens die zijn gegenereerd vanuit Azure Event hub.  Dankzij de integratie met SQL Data Warehouse kunnen streaminggegevens worden verwerkt en opgeslagen naast relationele gegevens, waardoor er dieper, geavanceerde analyses mogelijk zijn.  

* **Taak uitvoer:** Verzend uitvoer rechtstreeks van Stream Analytics taken naar SQL Data Warehouse.

Zie [integreren met Azure stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)voor meer informatie.


