---
title: Een voorspellend model maken en trainen in R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Maak een eenvoudig voorspellend model in R met behulp van Azure SQL Database Machine Learning Services (preview) en klik vervolgens op een resultaat met behulp van nieuwe gegevens.
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
ms.openlocfilehash: a54d418f668d8c7292c8332c1b14c4df45e59308
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768468"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Quick Start: een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (preview)

In deze Quick Start maakt en traint u een voorspellend model met R, slaat u het model op in een tabel in uw data base en gebruikt u vervolgens het model om waarden van nieuwe gegevens te voors pellen met behulp van Machine Learning Services (met R) in Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een [Azure-SQL database](sql-database-single-database-get-started.md) met een [firewall regel op server niveau](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld. [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!NOTE]
> Na de onboarding voor de openbare preview wordt Machine Learning voor u ingeschakeld voor uw bestaande of nieuwe database.

In dit voor beeld wordt een eenvoudig regressie model gebruikt voor het voors pellen van de stop afstand van een auto op basis van snelheid met behulp van de **auto's** gegevensset die is opgenomen in R.

> [!TIP]
> Veel gegevens sets zijn opgenomen in de R-runtime, om een lijst met geïnstalleerde gegevens sets op te halen, typt u `library(help="datasets")` van de R-opdracht prompt.

## <a name="create-and-train-a-predictive-model"></a>Een voorspellend model maken en trainen

De gegevens van de auto snelheid in de gegevensset **auto's** bevatten twee kolommen, zowel numerieke: **VERD** als **snelheid**. De gegevens bevatten meerdere stoppende waarnemingen met verschillende snelheden. Vanuit deze gegevens maakt u een lineair regressie model dat de relatie beschrijft tussen de snelheid van de auto en de afstand die nodig is om een auto te stoppen.

De vereisten van een lineair model zijn eenvoudig:
- Definieer een formule die de relatie beschrijft tussen de *snelheid* van de afhankelijke variabele en de onafhankelijke variabele *afstand*.
- Bied de invoergegevens die moeten worden gebruikt om het model te trainen.

> [!TIP]
> Als u een refresher op lineaire modellen nodig hebt, kunt u deze zelf studie gebruiken om het proces van het aanpassen van een model te beschrijven met behulp van rxLinMod: [aanpassing van lineaire modellen](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

In de volgende stappen stelt u de trainings gegevens in, maakt u een regressie model, traint u het met de trainings gegevens en slaat u het model op in een SQL-tabel.

1. Open **SQL Server Management Studio** en maak verbinding met de SQL-database.

   Als u hulp nodig hebt bij het verbinding maken, raadpleegt u [Quick Start: SQL Server Management Studio gebruiken om verbinding te maken met een Azure-SQL database](sql-database-connect-query-ssms.md).

1. Maak de **CarSpeed** -tabel om de trainings gegevens op te slaan.

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

1. Maak een regressie model met behulp van `rxLinMod`. 

   Als u het model wilt bouwen, definieert u de formule in de R-code en geeft u de **CarSpeed** voor de training door als een invoer parameter.

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

1. Een tabel maken waarin u het model opslaat, zodat u dit later kunt gebruiken voor voor spellingen. 

   De uitvoer van een R-pakket dat een model maakt, is doorgaans een **binair object**, dus de tabel moet een kolom van het type **varbinary (max)** hebben.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Roep nu de opgeslagen procedure aan, Genereer het model en sla het op in een tabel.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Opmerking: als u deze code een tweede keer uitvoert, krijgt u deze foutmelding:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Een optie om deze fout te voor komen is het bijwerken van de naam voor elk nieuw model. U kunt de naam bijvoorbeeld wijzigen in een meer beschrijvende naam, en extra info opnemen, zoals het modeltype, de dag waarop het model is gemaakt, enzovoort.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>De tabel met coëfficiënten weer geven

Over het algemeen is de uitvoer van R van de opgeslagen procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) beperkt tot één gegevensframe. U kunt echter naast het gegevensframe ook uitvoer van andere typen retourneren, zoals scalaire waarden.

Stel bijvoorbeeld dat u een model wilt trainen, maar direct de tabel met coëfficiënten uit het model wilt weer geven. Hiervoor maakt u de tabel met coëfficiënten als de belangrijkste resultatenset en voert u het getrainde model uit in een SQL-variabele. U kunt het model direct opnieuw gebruiken door de variabele aan te roepen of u kunt het model opslaan in een tabel zoals hier wordt weer gegeven.

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

**Results**

![Getraind model met extra uitvoer](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Nieuwe gegevens scoren met behulp van het getrainde model

*Score* is een term die wordt gebruikt in data Science voor het genereren van voor spellingen, kansen of andere waarden op basis van nieuwe gegevens die worden opgenomen in een getraind model. U gebruikt het model dat u in de vorige sectie hebt gemaakt om voor spellingen te scoren op nieuwe gegevens.

Hebt u opgemerkt dat de oorspronkelijke trainingsgegevens stoppen bij een snelheid van 25 mijl per uur? Dit komt omdat de oorspronkelijke gegevens zijn gebaseerd op een experiment uit 1920. Misschien vraagt u zich af hoe lang het een auto van de 1920s is om te stoppen als de oplossing snel kan worden uitgevoerd als 60 mph of zelfs 100 mph? U kunt deze vraag beantwoorden door enkele nieuwe snelheids waarden aan uw model toe te voegen.

1. Maak een tabel met nieuwe snelheids gegevens.

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

2. Voor speling van de afstand van deze nieuwe snelheids waarden.

   Omdat uw model is gebaseerd op het **rxLinMod** -algoritme dat is opgenomen in het **RevoScaleR** -pakket, roept u de functie [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) aan in plaats van de algemene R `predict`-functie.

   Dit voorbeeld script:
   - Maakt gebruik van een SELECT-instructie om één model uit de tabel op te halen
   - Geeft dit als een invoer parameter
   - Hiermee wordt de functie `unserialize` op het model aangeroepen
   - Hiermee wordt de `rxPredict` functie met de juiste argumenten toegepast op het model
   - Bevat de nieuwe invoer gegevens

   > [!TIP]
   > Zie [serialisatie-functies](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) die worden meegeleverd met RevoScaleR voor in realtime scores.

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

   **Results**

   ![Resultatenset voor het voorspellen van de remafstand](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In dit voorbeeld script wordt de functie `str` toegevoegd tijdens de test fase om het schema van de gegevens die worden geretourneerd vanuit R te controleren. U kunt de instructie later verwijderen.
>
> De kolomnamen die worden gebruikt in het R-script, worden niet per se doorgegeven aan de uitvoer van de opgeslagen procedure. Hier definieert de component WITH RESULTs een aantal nieuwe kolom namen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview-versie)](sql-database-machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-quickstart-r-create-script.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Werken met R-en SQL-gegevens in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-machine-learning-services-data-issues.md)
