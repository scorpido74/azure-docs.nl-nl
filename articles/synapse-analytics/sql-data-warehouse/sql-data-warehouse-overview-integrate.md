---
title: Geïntegreerde oplossingen bouwen
description: Hulp middelen en partners voor oplossingen die zijn geïntegreerd met een Synapse SQL-groep.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85201001"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Andere services integreren met een Synapse SQL-pool (Data Warehouse)

De functionaliteit van de SQL-groep in azure Synapse Analytics stelt gebruikers in staat te integreren met veel van de andere services in Azure. Met Synapse SQL kunt u een Data Warehouse maken met behulp van de resource van de SQL-groep, waarmee vervolgens verschillende extra services kunnen worden gebruikt, waarvan sommige de volgende zijn:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Raadpleeg het artikel [Integration partners](sql-data-warehouse-partner-data-integration.md)voor meer informatie over integratie Services in Azure.

## <a name="power-bi"></a>Power BI

Met Power BI-integratie kunt u de reken kracht van een Data Warehouse combi neren met de dynamische rapportage en visualisatie van Power BI. De Power BI-integratie bevat momenteel:

* **Direct Connect**: een geavanceerdere verbinding met logische pushdown voor een Data Warehouse dat is ingericht met een SQL-pool. Pushdown biedt snellere analyse op grotere schaal.
* **Open in Power bi**: met de knop openen in Power bi worden instantie gegevens door gegeven aan Power BI voor een vereenvoudigde manier om verbinding te maken.

Zie [integreren met Power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md)of de [Power bi documentatie](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)voor meer informatie.

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory biedt gebruikers een beheerd platform voor het maken van complexe uitpakken en laad pijplijnen. Integratie van SQL-groep met Azure Data Factory omvat:

* **Opgeslagen procedures**: de uitvoering van opgeslagen procedures organiseren.
* **Kopiëren**: gebruik ADF om gegevens te verplaatsen naar een SQL-groep. Met deze bewerking kan het standaard mechanisme voor gegevens verplaatsing van ADF of poly base onder de kaften worden gebruikt.

Zie [integreren met Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde analyse service waarmee u ingewikkelde modellen kunt maken met behulp van een groot aantal voorspellende hulpprogram ma's. De SQL-groep wordt ondersteund als bron en doel voor deze modellen en heeft de volgende functionaliteit:

* **Gegevens lezen:** Modellen op schaal schalen met behulp van T-SQL op basis van SQL-pool.
* **Gegevens schrijven:** Wijzigingen door voeren van een model terug naar de SQL-groep.

Zie [integreren met Azure machine learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)voor meer informatie.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics is een complexe, volledig beheerde infra structuur voor het verwerken en gebruiken van gebeurtenis gegevens die zijn gegenereerd vanuit Azure Event hub.  Door integratie met SQL-pool kunnen streaminggegevens worden verwerkt en opgeslagen naast relationele gegevens, waardoor dieper, geavanceerde analyses mogelijk zijn.  

* **Taak uitvoer:** Verzend uitvoer rechtstreeks van Stream Analytics taken naar een SQL-groep.

Zie [integreren met Azure stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)voor meer informatie.
