---
title: 'Zelf studie: een voorspellend model implementeren in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel drie van deze zelf studie met drie delen implementeert u een voorspellend model in R met Azure SQL Database Machine Learning Services (preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: efcb73866c83dcc03b5db8b7b97f438fb3010511
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452805"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelf studie: een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (preview)

In deel drie van deze zelf studie in drie delen implementeert u een voorspellend model, ontwikkeld in R, in een SQL database met behulp van Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

U maakt een opgeslagen procedure met een Inge sloten R-script waarmee voor spellingen worden gemaakt met behulp van het model. Omdat uw model wordt uitgevoerd in Azure SQL database, kan het eenvoudig worden getraind op basis van gegevens die zijn opgeslagen in de data base.

In dit artikel wordt beschreven hoe u de R-scripts gebruikt die u in de delen één en twee hebt ontwikkeld, kunt u het volgende doen:

> [!div class="checklist"]
> * Een opgeslagen procedure maken waarmee het machine learning model wordt gegenereerd
> * Het model opslaan in een database tabel
> * Een opgeslagen procedure maken die voor spellingen maakt met behulp van het model
> * Het model uitvoeren met nieuwe gegevens

In [deel één](sql-database-tutorial-predictive-model-prepare-data.md)hebt u geleerd hoe u een voorbeeld database importeert en vervolgens de gegevens voorbereidt die moeten worden gebruikt voor het trainen van een voorspellend model in R.

In [deel twee](sql-database-tutorial-predictive-model-build-compare.md)hebt u geleerd hoe u meerdere machine learning modellen maakt en traint in R. vervolgens kiest u de meest nauw keurige versie.

## <a name="prerequisites"></a>Vereisten

* In deel drie van deze zelf studie wordt ervan uitgegaan dat u [**deel één**](sql-database-tutorial-predictive-model-prepare-data.md) en [**deel twee**](sql-database-tutorial-predictive-model-build-compare.md)hebt voltooid.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Een opgeslagen procedure maken waarmee het model wordt gegenereerd

In deel twee van deze reeks zelf studies hebt u besloten dat een beslissings structuur model (dTree) het nauwkeurigst is. Maak nu met behulp van de R-scripts die u hebt ontwikkeld`generate_rental_rx_model`, een opgeslagen procedure () die het dTree-model met rxDTree in het RevoScaleR-pakket contraint en genereert.

Voer de volgende opdrachten uit in Azure Data Studio of SSMS.

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>Het model opslaan in een database tabel

Maak een tabel in de Tutorialdb maakt-data base en sla het model vervolgens op in de tabel.

1. Maak een tabel (`rental_rx_models`) voor het opslaan van het model.

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. Sla het model op in de tabel als een binair object met de naam van het model "rxDTree".

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Een opgeslagen procedure maken die voor spellingen doet

Maak een opgeslagen procedure (`predict_rentalcount_new`) die voor spellingen maakt met behulp van het getrainde model en een set nieuwe gegevens.

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>Het model uitvoeren met nieuwe gegevens

Nu kunt u de opgeslagen procedure `predict_rentalcount_new` gebruiken om het aantal huren van nieuwe gegevens te voors pellen.

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

Er wordt een resultaat weer gegeven dat er ongeveer als volgt uitziet.

```results
RentalCount_Predicted
332.571428571429
```

U hebt een model gemaakt, getraind en geïmplementeerd in een Azure-SQL database. Vervolgens gebruikt u dat model in een opgeslagen procedure om waarden te voors pellen op basis van nieuwe gegevens.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de Tutorialdb maakt-data base, verwijdert u deze van uw Azure SQL Database-Server.

Voer de volgende stappen uit op de Azure Portal:

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** of **SQL-data bases**.
1. Typ **tutorialdb maakt**in het veld **filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw Tutorialdb maakt-data base.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel drie van deze reeks zelf studies hebt u de volgende stappen uitgevoerd:

* Een opgeslagen procedure maken waarmee het machine learning model wordt gegenereerd
* Het model opslaan in een database tabel
* Een opgeslagen procedure maken die voor spellingen maakt met behulp van het model
* Het model uitvoeren met nieuwe gegevens

Zie voor meer informatie over het gebruik van R in Azure SQL Database Machine Learning Services (preview):

* [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
* [Werken met R-en SQL-gegevens in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-machine-learning-services-data-issues.md)
* [Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (preview-versie)](sql-database-machine-learning-services-add-r-packages.md)
