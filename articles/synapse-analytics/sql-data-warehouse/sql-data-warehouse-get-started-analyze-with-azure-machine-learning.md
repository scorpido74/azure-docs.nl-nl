---
title: Gegevens analyseren met Azure Machine Learning
description: Gebruik Azure Machine Learning om een voorspellend machine learning-model te bouwen op basis van gegevens die zijn opgeslagen in Azure Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-Synapse
ms.openlocfilehash: 07570fd456d7f5a75a6b5a3ee635605a5d40072a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350577"
---
# <a name="analyze-data-with-azure-machine-learning"></a>Gegevens analyseren met Azure Machine Learning
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [Ssms](sql-data-warehouse-query-ssms.md)
> 
> 

Deze zelfstudie maakt gebruik van Azure Machine Learning om een voorspellend machine learning-model te bouwen op basis van gegevens die zijn opgeslagen in Azure Synapse. U maakt een gerichte marketingcampagne voor Adventure Works, de fietsenwinkel, door te voorspellen of een klant een fiets waarschijnlijk wel of niet zal kopen.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een SQL-groep die vooraf is geladen met AdventureWorksDW-voorbeeldgegevens. Zie [Een SQL-groep maken](create-data-warehouse-portal.md) en ervoor kiezen om de voorbeeldgegevens te laden als u dit wilt inrichten. Als u wel een datawarehouse hebt maar nog geen voorbeeldgegevens, kunt u [voorbeeldgegevens handmatig laden](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="1-get-the-data"></a>1. Haal de gegevens
De gegevens bevinden zich in de weergave dbo.vTargetMail in de AdventureWorksDW-database. Deze gegevens lezen:

1. Meld u aan bij [Azure Machine Learning Studio](https://studio.azureml.net/) en klik op My experiments (Mijn experimenten).
2. Klik **op +NIEUW** linksonder in het scherm en selecteer **Leeg experiment**.
3. Voer een naam in voor uw experiment: Targeted Marketing.
4. Sleep de **gegevensmodule importeren** onder **Gegevensinvoer en uitvoer** vanuit het deelvenster modules naar het canvas.
5. Geef de details van uw SQL-groep op in het deelvenster Eigenschappen.
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

## <a name="2-clean-the-data"></a>2. De gegevens schoonmaken
Als u de gegevens wilt opschonen, verwijdert u enkele kolommen die niet relevant zijn voor het model. Om dit te doen:

1. Sleep de module **Kolommen selecteren in gegevensset** onder **Gegevenstransformatie < Manipulatie** naar het canvas. Sluit deze module aan op de module **Gegevens importeren.**
2. Klik in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten) om de kolommen op te geven die u wilt verwijderen.

   ![Projectkolommen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4-projectcolumns-new.png)
3. Sluit twee kolommen uit: CustomerAlternateKey en GeographyKey.

   ![Overbodige kolommen verwijderen](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5-columnselector-new.png)

## <a name="3-build-the-model"></a>3. Bouw het model
U gaat de gegevens 80-20 splitsen: 80% om een Machine Learning-model te trainen en 20% om het model te testen. We zullen gebruik maken van de "Two-Class" algoritmen voor dit binaire classificatieprobleem.

1. Sleep de module **Split** (Splitsen) naar het canvas.
2. Voer in het deelvenster Eigenschappen 0,8 in voor een fractie van rijen in de eerste uitvoergegevensset.

   ![Gegevens splitsen in trainings- en testset](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6-split-new.png)
3. Sleep de module **Two-Class Boosted Decision Tree** (Beslissingsstructuur op basis van twee klassen) naar het canvas.
4. Sleep de **module Treinmodel** in het canvas en geef ingangen op door deze aan te sluiten op de **Tweeklassen Boosted Decision Tree (ML-algoritme)** en **Split** (gegevens om het algoritme op te trainen). 

     ![Verbinding maken met de module Train Model (Model trainen)](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7-train-new.png)
5. Klik vervolgens in het deelvenster Properties (Eigenschappen) op **Launch column selector** (Kolomselectie starten). Selecteer de kolom **BikeBuyer** als de kolom die u wilt voorspellen.

   ![Te voorspellen kolom selecteren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8-traincolumnselector-new.png)

## <a name="4-score-the-model"></a>4. Scoor het model
Nu gaat u testen hoe het model functioneert met testgegevens. U gaat het gekozen algoritme vergelijken met een ander algoritme om te zien welk algoritme de beste prestaties levert.

1. Sleep de module **Scoremodel** in het canvas en sluit deze aan op **modules Train Model** en Split **Data.**

   ![Het model scoren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9-score-new.png)
2. Sleep de **Two-Class Bayes Point Machine** naar het experimentencanvas. U gaat dit algoritme vergelijken met de Two-Class Boosted Decision Tree (Beslissingsstructuur met twee klassen).
3. Kopieer en plak de modules Train Model en Score Model naar het canvas.
4. Sleep het model **Evaluate Model** (Model evalueren) naar het canvas om de twee algoritmen te vergelijken.
5. Voer het experiment **uit.**

   ![Het experiment uitvoeren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10-evaluate-new.png)
6. Klik op de uitvoerpoort onder in de module Evaluate Model (Model evalueren) en klik op Visualize (Visualiseren).

   ![Evaluatieresultaten visualiseren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11-evalresults-new.png)

De statistieken zijn de ROC-curve, precisie-recall diagram, en lift curve. Als u deze metrische gegevens bekijkt, ziet u dat het eerste model beter presteert dan het tweede. Als u wilt kijken naar wat het eerste model voorspelde, klikt u op de uitvoerpoort van het scoremodel en klikt u op Visualiseren.

![Scoreresultaten visualiseren](./media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12-scoreresults-new.png)

U ziet dat er twee of meer kolommen aan de testgegevensset zijn toegevoegd.

* Scored Probabilities (Berekende kansen): de waarschijnlijkheid dat een klant een fiets koopt.
* Scored Labels (Berekende Labels): de classificatie die door het model is uitgevoerd: fietskoper (1) of niet (0). Deze drempelwaarde voor waarschijnlijkheid voor labeling is ingesteld op 50% en kan worden aangepast.

Door de kolom BikeBuyer (werkelijk) te vergelijken met de kolom Scored Labels (voorspelling), ziet u hoe goed het model heeft gepresteerd. Vervolgens u dit model gebruiken om voorspellingen te doen voor nieuwe klanten en dit model te publiceren als een webservice of resultaten terug te schrijven naar Azure Synapse.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg [Inleiding tot Machine Learning in Azure](https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/) voor meer informatie over het bouwen van voorspellende Machine Learning-modellen.