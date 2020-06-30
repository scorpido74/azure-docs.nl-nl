---
title: 'Zelfstudie: Een voorspellend model implementeren in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel drie van deze driedelige zelfstudie implementeert u met Azure SQL Database Machine Learning Services (preview) een voorspellend model in R.
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
ms.openlocfilehash: 84c0f6564fc9ab8abc43205b58d9445cda82a2da
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253814"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel drie van deze driedelige zelfstudie implementeert u een in R ontwikkeld voorspellend model in een database in Azure SQL Database met behulp van Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

U maakt een opgeslagen procedure met een ingesloten R-script dat voorspellingen doet met behulp van het model. Omdat uw model wordt uitgevoerd in de database, kan het eenvoudig worden getraind op basis van gegevens die in de database zijn opgeslagen.

In dit artikel leert u hoe u de R-scripts, die u in de delen één en twee hebt ontwikkeld, moet gebruiken om het volgende te doen:

> [!div class="checklist"]
>
> * Een opgeslagen procedure maken waarmee het machine learning-model wordt gegenereerd
> * Het model opslaan in een databasetabel
> * Een opgeslagen procedure maken die voorspellingen doet aan de hand van het model
> * Het model uitvoeren met nieuwe gegevens

In [deel één](predictive-model-prepare-data-tutorial.md) hebt u geleerd hoe u een voorbeelddatabase importeert en vervolgens de gegevens voorbereidt zodat deze kunnen worden gebruikt voor het trainen van een voorspellend model in R.

In [deel twee](predictive-model-build-compare-tutorial.md) hebt u geleerd hoe u meerdere machine learning-modellen maakt en traint in R en hoe u vervolgens de meest nauwkeurige kiest.

## <a name="prerequisites"></a>Vereisten

* In deel drie van deze zelfstudie wordt ervan uitgegaan dat u [**deel één**](predictive-model-prepare-data-tutorial.md) en [**deel twee**](predictive-model-build-compare-tutorial.md) hebt voltooid.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Een opgeslagen procedure maken waarmee het model wordt gegenereerd

In deel twee van deze serie zelfstudies kwam u tot de conclusie dat een beslissingsstructuurmodel (dtree) het meest nauwkeurig was. Maak nu met behulp van de R-scripts die u hebt ontwikkeld een opgeslagen procedure (`generate_rental_rx_model`) die het dtree-model traint en genereert met rxDTree van het RevoScaleR-pakket.

Voer de volgende opdrachten uit in Azure Data Studio of SQL Server Management Studio (SSMS).

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

Maak een tabel in de TutorialDB-database en sla het model vervolgens op in de tabel.

1. Maak een tabel (`rental_rx_models`) om het model op te slaan.

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

1. Sla het model als een binair object op in de tabel, met de modelnaam "rxDTree".

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

Een opgeslagen procedure maken (`predict_rentalcount_new`) die voorspellingen doet met behulp van het getrainde model en een set nieuwe gegevens.

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

Nu kunt u de opgeslagen procedure gebruiken `predict_rentalcount_new` om de huurprijs te voorspellen op basis van nieuwe gegevens.

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

U zou een resultaat moeten zien dat lijkt op het volgende.

```results
RentalCount_Predicted
332.571428571429
```

U hebt met succes een model gemaakt, getraind en geïmplementeerd in een database in Azure SQL Database. Vervolgens heeft u dat model in een opgeslagen procedure gebruikt om waarden te voorspellen op basis van nieuwe gegevens.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de TutorialDB-database niet meer nodig hebt, verwijdert u deze van uw server.

Volg deze stappen vanuit de Microsoft Azure-portal:

1. Selecteer in het linkermenu in Azure Portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** **TutorialDB** in en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel drie van deze reeks zelfstudies hebt u de volgende stappen voltooid:

* Een opgeslagen procedure maken waarmee het machine learning-model wordt gegenereerd
* Het model opslaan in een databasetabel
* Een opgeslagen procedure maken die voorspellingen doet aan de hand van het model
* Het model uitvoeren met nieuwe gegevens

Voor meer informatie over het gebruik van R in Azure SQL Database Machine Learning Services (preview):

* [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](machine-learning-services-functions.md)
* [Werken met R en SQL-gegevens in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md)
* [Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (preview)](machine-learning-services-add-r-packages.md)
