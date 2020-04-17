---
title: 'Zelfstudie: Een voorspellend model implementeren in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel drie van deze driedelige zelfstudie implementeert u een voorspellend model in R met Azure SQL Database Machine Learning Services (preview).
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452805"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (voorbeeld)

In deel drie van deze driedelige zelfstudie implementeert u een voorspellend model, ontwikkeld in R, in een SQL-database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

U maakt een opgeslagen procedure met een ingesloten R-script dat voorspellingen doet met behulp van het model. Omdat uw model wordt uitgevoerd in de Azure SQL-database, kan het eenvoudig worden getraind tegen gegevens die zijn opgeslagen in de database.

In dit artikel leert u met behulp van de R-scripts die u in de eerste en tweede delen hebt ontwikkeld:

> [!div class="checklist"]
> * Een opgeslagen procedure maken die het machine learning-model genereert
> * Het model opslaan in een databasetabel
> * Een opgeslagen procedure maken die voorspellingen doet met behulp van het model
> * Het model uitvoeren met nieuwe gegevens

In [deel één](sql-database-tutorial-predictive-model-prepare-data.md)hebt u geleerd hoe u een voorbeelddatabase importeren en vervolgens de gegevens voorbereiden die moeten worden gebruikt voor het trainen van een voorspellend model in R.

In [deel twee](sql-database-tutorial-predictive-model-build-compare.md)heb je geleerd hoe je meerdere machine learning-modellen in R maken en trainen en vervolgens de meest nauwkeurige kiezen.

## <a name="prerequisites"></a>Vereisten

* Deel drie van deze zelfstudieserie gaat ervan uit dat u [**deel één**](sql-database-tutorial-predictive-model-prepare-data.md) en [**deel twee**](sql-database-tutorial-predictive-model-build-compare.md)hebt voltooid.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Een opgeslagen procedure maken die het model genereert

In deel twee van deze tutorial serie, heb je besloten dat een decision tree (dtree) model was de meest nauwkeurige. Nu, met behulp van de R scripts`generate_rental_rx_model`die u hebt ontwikkeld, maak een opgeslagen procedure ( ) die treinen en genereert de dtree model met behulp van rxDTree uit de RevoScaleR pakket.

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

## <a name="store-the-model-in-a-database-table"></a>Het model opslaan in een databasetabel

Maak een tabel in de TutorialDB-database en sla het model op in de tabel.

1. Maak een`rental_rx_models`tabel ( ) voor het opslaan van het model.

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

1. Sla het model op in de tabel als een binair object, met de modelnaam "rxDTree".

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

## <a name="create-a-stored-procedure-that-makes-predictions"></a>Een opgeslagen procedure maken die voorspellingen doet

Maak een opgeslagen`predict_rentalcount_new`procedure ( ) die voorspellingen maakt met behulp van het getrainde model en een set van nieuwe gegevens.

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

Nu u de `predict_rentalcount_new` opgeslagen procedure gebruiken om het aantal huurtevariëren van nieuwe gegevens.

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

U ziet een resultaat dat vergelijkbaar is met het volgende.

```results
RentalCount_Predicted
332.571428571429
```

U hebt een model in een Azure SQL-database gemaakt, getraind en geïmplementeerd. Vervolgens gebruikte u dat model in een opgeslagen procedure om waarden te voorspellen op basis van nieuwe gegevens.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de TutorialDB-database, verwijdert u deze van uw Azure SQL Database-server.

Voer de volgende stappen uit vanuit de Azure-portal:

1. Selecteer alle **bronnen** of **SQL-databases**in het linkermenu in de Azure-portal.
1. Typ **TutorialDB**en selecteer uw abonnement in het veld **Filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel drie van deze zelfstudiereeks hebt u de volgende stappen voltooid:

* Een opgeslagen procedure maken die het machine learning-model genereert
* Het model opslaan in een databasetabel
* Een opgeslagen procedure maken die voorspellingen doet met behulp van het model
* Het model uitvoeren met nieuwe gegevens

Zie voor meer informatie over het gebruik van R in Azure SQL Database Machine Learning Services (voorbeeld):

* [Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-functions.md)
* [Werken met R- en SQL-gegevens in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-data-issues.md)
* [Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-add-r-packages.md)
