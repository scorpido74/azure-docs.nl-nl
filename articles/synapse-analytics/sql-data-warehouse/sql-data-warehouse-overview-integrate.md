---
title: Geïntegreerde oplossingen bouwen
description: Oplossingstools en partners die integreren met een Synapse SQL-pool.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c8e3598e55d3f90ab2b7401380406677f56c0ce3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586483"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool"></a>Andere services integreren met een Synapse SQL-pool

Met de Synapse SQL-poolmogelijkheid binnen Azure Synapse Analytics kunnen gebruikers integreren met veel van de andere services in Azure. Met Synapse SQL u een datawarehouse maken via de SQL-poolbron, die vervolgens verschillende aanvullende services kan gebruiken, waaronder:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Voor meer informatie over integratieservices in Azure, bekijk het artikel [Integratiepartners.](sql-data-warehouse-partner-data-integration.md)

## <a name="power-bi"></a>Power BI
Met de Integratie van Power BI u de rekenkracht van een datawarehouse combineren met de dynamische rapportage en visualisatie van Power BI. Power BI-integratie omvat momenteel:

* **Direct Connect**: Een meer geavanceerde verbinding met logische pushdown ten opzichte van een datawarehouse dat is ingericht met SQL-pool. Pushdown biedt snellere analyse op grotere schaal.
* **Open in Power BI**: De knop 'Open in Power BI' geeft instantie-informatie door aan Power BI voor een vereenvoudigde manier om verbinding te maken.

Zie [Integreren met Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)of de Power [BI-documentatie](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)voor meer informatie.

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory biedt gebruikers een beheerd platform om complexe extract- en loadpipelines te maken. SQL-poolintegratie met Azure Data Factory omvat:

* **Opgeslagen procedures**: Orkestreren de uitvoering van opgeslagen procedures.
* **Kopiëren:** gebruik ADF om gegevens naar SQL-groep te verplaatsen. Deze bewerking kan het standaard gegevensbewegingsmechanisme van ADF of PolyBase onder de hoezen gebruiken. 

Zie [Integreren met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json)voor meer informatie.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning is een volledig beheerde analyseservice, waarmee u ingewikkelde modellen maken met behulp van een grote reeks voorspellende tools. SQL-pool wordt ondersteund als bron en bestemming voor deze modellen en heeft de volgende functionaliteit:

* **Lees gegevens:** Stimuleer modellen op schaal met Behulp van T-SQL tegen SQL-pool.
* **Gegevens schrijven:** Wijzigingen van elk model terug naar SQL-pool vastleggen.

Zie [Integreren met Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)voor meer informatie.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics is een complexe, volledig beheerde infrastructuur voor het verwerken en consumeren van gebeurtenisgegevens die zijn gegenereerd vanuit Azure Event Hub.  Integratie met SQL-pool maakt het mogelijk om streaminggegevens effectief te verwerken en op te slaan naast relationele gegevens, waardoor diepere, geavanceerdere analyse mogelijk is.  

* **Taakuitvoer:** Verzend uitvoer vanuit Stream Analytics-taken rechtstreeks naar SQL-groep.

Zie [Integreren met Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)voor meer informatie.


