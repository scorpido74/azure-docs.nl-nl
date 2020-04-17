---
title: Een voorspellend model maken en trainen in R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Maak een eenvoudig voorspellend model in R met Azure SQL Database Machine Learning Services (preview) en voorspel vervolgens een resultaat met behulp van nieuwe gegevens.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3c88bdf141e7784837a89c8104574d97c93296dc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460151"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Snelstart: een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (voorbeeld)

In deze quickstart maakt en traint u een voorspellend model met R, slaat u het model op in een tabel in uw database en gebruikt u het model om waarden uit nieuwe gegevens te voorspellen met Machine Learning Services (met R) in Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [Azure SQL-database](sql-database-single-database-get-started.md) met een [firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

In dit voorbeeld wordt gebruik gemaakt van een eenvoudig regressiemodel om de remafstand van een auto te voorspellen op basis van snelheid met behulp van de **gegevensset van de auto's** die bij R zijn meegeleverd.

> [!TIP]
> Veel gegevenssets zijn opgenomen in de R-runtime, om `library(help="datasets")` een lijst met geïnstalleerde gegevenssets te krijgen, typ van de R-opdrachtprompt.

## <a name="create-and-train-a-predictive-model"></a>Een voorspellend model maken en trainen

De auto snelheid gegevens in de **auto** dataset bevat twee kolommen, zowel numeriek: **dist** en **snelheid**. De gegevens omvatten meerdere stopwaarnemingen met verschillende snelheden. Op basis van deze gegevens maakt u een lineair regressiemodel dat de relatie beschrijft tussen de snelheid van de auto en de afstand die nodig is om een auto te stoppen.

De vereisten van een lineair model zijn eenvoudig:
- Definieer een formule die de relatie beschrijft tussen de afhankelijke variabele *snelheid* en de onafhankelijke variabele *afstand*.
- Bied de invoergegevens die moeten worden gebruikt om het model te trainen.

> [!TIP]
> Als u een opfriscursus nodig hebt voor lineaire modellen, probeer dan deze zelfstudie die het proces beschrijft van het monteren van een model met rxLinMod: [Fitting Linear Models](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

In de volgende stappen stelt u de trainingsgegevens in, maakt u een regressiemodel, traint u het met behulp van de trainingsgegevens en slaat u het model op in een SQL-tabel.

1. Open **SQL Server Management Studio** en maak verbinding met uw SQL-database.

   Zie [Quickstart: SQL Server Management Studio gebruiken om verbinding te maken en een Azure SQL-database op te vragen](sql-database-connect-query-ssms.md)als u hulp nodig hebt bij het maken van verbinding.

1. Maak de **tabel CarSpeed** om de trainingsgegevens op te slaan.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Een regressiemodel `rxLinMod`maken met behulp van . 

   Als u het model wilt bouwen, definieert u de formule in de R-code en geeft u de trainingsgegevens **CarSpeed** door als invoerparameter.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Het eerste argument voor rxLinMod is de *formuleparameter*, waarmee afstand wordt gedefinieerd als afhankelijk van snelheid. De invoergegevens worden opgeslagen in de variabele `CarsData`, die wordt gevuld met de SQL-query.

1. Maak een tabel waar u het model opslaat, zodat u het later gebruiken voor voorspelling. 

   De uitvoer van een R-pakket dat een model maakt, is meestal een **binair object,** dus de tabel moet een kolom **van VARBINARY(max)** type hebben.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Bel nu de opgeslagen procedure, genereer het model en sla het op in een tabel.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Opmerking: als u deze code een tweede keer uitvoert, krijgt u deze foutmelding:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Een optie om deze fout te voorkomen is het bijwerken van de naam voor elk nieuw model. U kunt de naam bijvoorbeeld wijzigen in een meer beschrijvende naam, en extra info opnemen, zoals het modeltype, de dag waarop het model is gemaakt, enzovoort.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Bekijk de tabel met coëfficiënten

Over het algemeen is de uitvoer van R van de opgeslagen procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) beperkt tot één gegevensframe. U kunt echter naast het gegevensframe ook uitvoer van andere typen retourneren, zoals scalaire waarden.

Stel dat u een model wilt trainen, maar onmiddellijk de tabel met coëfficiënten van het model wilt bekijken. Hiervoor maakt u de tabel met coëfficiënten als hoofdresultaatset en produceert u het getrainde model in een SQL-variabele. U het model onmiddellijk opnieuw gebruiken door de variabele aan te roepen, of u het model opslaan in een tabel zoals hier wordt weergegeven.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Resultaten**

![Getraind model met extra uitvoer](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Nieuwe gegevens scoren met behulp van het getrainde model

*Scoren* is een term die in data science wordt gebruikt om voorspellingen, waarschijnlijkheden of andere waarden te genereren op basis van nieuwe gegevens die in een getraind model worden ingevoerd. U gebruikt het model dat u in de vorige sectie hebt gemaakt om voorspellingen te scoren op basis van nieuwe gegevens.

Hebt u opgemerkt dat de oorspronkelijke trainingsgegevens stoppen bij een snelheid van 25 mijl per uur? Dit komt omdat de oorspronkelijke gegevens zijn gebaseerd op een experiment uit 1920. Je je afvragen, hoe lang zou het een auto uit de jaren 1920 om te stoppen als het zou kunnen gaan zo snel als 60 mph of zelfs 100 mph? Om deze vraag te beantwoorden, u een aantal nieuwe snelheidswaarden aan uw model geven.

1. Maak een tabel met nieuwe snelheidsgegevens.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Voorspel de remafstand van deze nieuwe snelheidswaarden.

   Omdat uw model is gebaseerd op het **rxLinMod-algoritme** dat wordt geleverd als onderdeel van het `predict` **RevoScaleR-pakket,** belt u de [rxPredict-functie,](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) in plaats van de generieke R-functie.

   Dit voorbeeldscript:
   - Gebruikt een SELECT-instructie om één model uit de tabel te halen
   - Passeert het als een invoerparameter
   - Roept `unserialize` de functie op het model
   - Past `rxPredict` de functie met de juiste argumenten toe op het model
   - Biedt de nieuwe invoergegevens

   > [!TIP]
   > Zie [Serialisatiefuncties](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) van RevoScaleR voor realtime scoren.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Resultaten**

   ![Resultatenset voor het voorspellen van de remafstand](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In dit voorbeeldscript `str` wordt de functie toegevoegd tijdens de testfase om het schema te controleren van gegevens die van R worden geretourneerd. U de verklaring later verwijderen.
>
> De kolomnamen die worden gebruikt in het R-script, worden niet per se doorgegeven aan de uitvoer van de opgeslagen procedure. Hier definieert de clausule MET RESULTATEN een aantal nieuwe kolomnamen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services with R (preview).

- [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-quickstart-r-create-script.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-functions.md)
- [Werken met R- en SQL-gegevens in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-data-issues.md)
