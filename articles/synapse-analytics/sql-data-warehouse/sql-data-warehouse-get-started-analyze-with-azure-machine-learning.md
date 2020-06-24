---
title: Gegevens analyseren met Azure Machine Learning
description: Gebruik Azure Machine Learning om een voorspellend machine learning model te maken op basis van gegevens die zijn opgeslagen in azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 2f69ece56ebd17bcf012fdbc2c1d8da7a020be66
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85212323"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Gegevens analyseren met Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

In deze zelf studie wordt Azure Machine Learning gebruikt voor het bouwen van een voorspellend machine learning model op basis van gegevens die zijn opgeslagen in azure Synapse. U maakt een gerichte marketingcampagne voor Adventure Works, de fietsenwinkel, door te voorspellen of een klant een fiets waarschijnlijk wel of niet zal kopen.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een SQL-pool vooraf geladen met AdventureWorksDW-voorbeeld gegevens. Zie voor het inrichten van [een SQL-groep](create-data-warehouse-portal.md) en kies ervoor om de voorbeeld gegevens te laden. Als u wel een datawarehouse hebt maar nog geen voorbeeldgegevens, kunt u [voorbeeldgegevens handmatig laden](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. de gegevens ophalen
De gegevens bevinden zich in de weergave dbo.vTargetMail in de AdventureWorksDW-database. Deze gegevens lezen:

1. Meld u aan bij [Azure Machine Learning Studio](https://studio.azureml.net/) en klik op My experiments (Mijn experimenten).
2. Klik op **+ Nieuw** linksonder in het scherm en selecteer **leeg experiment**.
3. Voer een naam in voor uw experiment: Targeted Marketing.
4. Sleep de module **gegevens importeren** onder **gegevens invoer en uitvoer** van het deel venster modules naar het canvas.
5. Geef de details op van de SQL-groep in het deel venster Eigenschappen.
6. Geef de database**query** op om de gewenste gegevens te lezen.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Voer het experiment uit door onder experimentencanvas op **RUN** (UITVOEREN) te klikken.

![Het experiment uitvoeren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1-reader-new.png)

Wanneer het experiment is uitgevoerd, klikt u op de uitvoerpoort onder in de module Reader en selecteert u **Visualize** (Visualiseren) om de geïmporteerde gegevens te zien.

![Geïmporteerde gegevens weergeven](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3-readerdata-new.png)

## <a name="2-clean-the-data"></a>2. de gegevens opschonen
Als u de gegevens wilt opschonen, verwijdert u enkele kolommen die niet relevant zijn voor het model. Om dit te doen:

1. Sleep de module **select columns in dataset** onder **Data Transformation < manipulatie** in het canvas. Deze module verbinden met de module **gegevens importeren** .
2. Klik in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten) om de kolommen op te geven die u wilt verwijderen.

   ![Projectkolommen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Sluit twee kolommen uit: CustomerAlternateKey en GeographyKey.

   ![Overbodige kolommen verwijderen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. bouw het model
U gaat de gegevens 80-20 splitsen: 80% om een Machine Learning-model te trainen en 20% om het model te testen. We maken gebruik van de algoritmen met twee klassen voor dit probleem met de binaire classificatie.

1. Sleep de module **Split** (Splitsen) naar het canvas.
2. Voer in het deel venster Eigenschappen 0,8 in voor het gedeelte van rijen in de eerste uitvoer gegevensset.

   ![Gegevens splitsen in trainings- en testset](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Sleep de module **Two-Class Boosted Decision Tree** (Beslissingsstructuur op basis van twee klassen) naar het canvas.
4. Sleep de module **Train model** naar het canvas en geef de invoer op door deze te verbinden met de **twee klasse versterkte beslissings structuur** (ml-algoritme) en **Split** (gegevens voor het trainen van de algoritmen op) modules. 

     ![Verbinding maken met de module Train Model (Model trainen)](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Klik vervolgens in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten). Selecteer de kolom **BikeBuyer** als de kolom die u wilt voorspellen.

   ![Te voorspellen kolom selecteren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. het model beoordelen
Nu gaat u testen hoe het model functioneert met testgegevens. U gaat het gekozen algoritme vergelijken met een ander algoritme om te zien welk algoritme de beste prestaties levert.

1. Sleep de module **score model** naar het canvas en verbind deze met een **Train model** en **Splits gegevens** modules.

   ![Het model scoren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Sleep de **Two-Class Bayes Point Machine** naar het experimentencanvas. U gaat dit algoritme vergelijken met de Two-Class Boosted Decision Tree (Beslissingsstructuur met twee klassen).
3. Kopieer en plak de modules Train Model en Score Model naar het canvas.
4. Sleep het model **Evaluate Model** (Model evalueren) naar het canvas om de twee algoritmen te vergelijken.
5. **Voer** het experiment uit.

   ![Het experiment uitvoeren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Klik op de uitvoerpoort onder in de module Evaluate Model (Model evalueren) en klik op Visualize (Visualiseren).

   ![Evaluatieresultaten visualiseren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

De gegeven metrische gegevens zijn de ROC-curve, het diagram met precisie terughalen en de kromme voor liften. Als u deze metrische gegevens bekijkt, ziet u dat het eerste model beter presteert dan het tweede. Klik op uitvoer poort van het score model en klik op visualiseren om te kijken wat het eerste model is voorspeld.

![Scoreresultaten visualiseren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

U ziet dat er twee of meer kolommen aan de testgegevensset zijn toegevoegd.

* Scored Probabilities (Berekende kansen): de waarschijnlijkheid dat een klant een fiets koopt.
* Scored Labels (Berekende Labels): de classificatie die door het model is uitgevoerd: fietskoper (1) of niet (0). Deze drempelwaarde voor waarschijnlijkheid voor labeling is ingesteld op 50% en kan worden aangepast.

Door de kolom BikeBuyer (werkelijk) te vergelijken met de kolom Scored Labels (voorspelling), ziet u hoe goed het model heeft gepresteerd. Vervolgens kunt u dit model gebruiken om voor spellingen te doen voor nieuwe klanten en dit model als een webservice te publiceren of om resultaten terug te schrijven naar Azure Synapse.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg [Inleiding tot Machine Learning in Azure](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) voor meer informatie over het bouwen van voorspellende Machine Learning-modellen.