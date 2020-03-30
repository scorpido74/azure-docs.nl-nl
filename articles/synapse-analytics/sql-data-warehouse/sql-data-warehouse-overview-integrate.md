---
title: Geïntegreerde oplossingen bouwen
description: Oplossingstools en partners die integreren met een datawarehouse dat is ingericht met SQL Analytics.
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
ms.openlocfilehash: 680e561c08c5113e3d7f26b00422f3696bb133e9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350224"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Andere services integreren met een SQL Analytics-gegevensmagazijn 
De SQL Analytics-mogelijkheid binnen Azure Synapse Analytics stelt gebruikers in staat om te integreren met veel van de andere services in Azure. Met SQL Analytics u een gegevensmagazijn maken via de SQL Pool-bron, die vervolgens verschillende aanvullende services kan gebruiken, waaronder:

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


