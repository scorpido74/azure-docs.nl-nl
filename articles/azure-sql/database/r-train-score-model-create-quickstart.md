---
title: Een voorspellend model maken en trainen in R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Maak een eenvoudig voorspellend model in R met behulp van Azure SQL Database Machine Learning Services (preview) en voorspel vervolgens een resultaat met nieuwe gegevens.
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
ms.openlocfilehash: 6dee5d6e1bb2802114d7bba14a57b91dbab09e19
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053242"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Quickstart: Een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze quickstart maakt en traint u een voorspellend model met R, slaat u het model op in een tabel in uw database en gebruikt u het model om met behulp van Machine Learning Services (met R) in Azure SQL Database waarden te voorspellen op basis van nieuwe gegevens.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [database in Azure SQL Database](single-database-create-quickstart.md) met een [firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

In dit voorbeeld wordt een eenvoudig regressiemodel gebruikt voor het voorspellen van de stopafstand van een auto op basis van snelheid met behulp van de gegevensset **cars** die is opgenomen in R.

> [!TIP]
> Er zijn veel gegevenssets in de runtime van R opgenomen. Typ `library(help="datasets")` achter de opdrachtprompt van R om een lijst van geïnstalleerde gegevenssets op te halen.

## <a name="create-and-train-a-predictive-model"></a>Een voorspellend model maken en trainen

De gegevens over autosnelheden in de gegevensset **cars** bestaan uit twee kolommen, beide numeriek: **dist** en **speed**. De gegevens bevatten meerdere stopobserveringen bij verschillende snelheden. Op basis van deze gegevens maakt u een lineair regressiemodel dat de relatie beschrijft tussen de autosnelheid en de afstand die de auto nodig heeft om tot stilstand te komen.

De vereisten van een lineair model zijn eenvoudig:
- Definieer een formule die de relatie beschrijft tussen de afhankelijke variabele *speed* en de onafhankelijke variabele *distance*.
- Bied de invoergegevens die moeten worden gebruikt om het model te trainen.

> [!TIP]
> Als u uw kennis over lineaire modellen wilt opfrissen, probeer dan deze zelfstudie, waarin wordt beschreven hoe u een model aanpast met rxLinMod: [Lineaire modellen aanpassen](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

In de volgende stappen stelt u de trainingsgegevens in, maakt u een regressiemodel en traint u het met de trainingsgegevens. Vervolgens slaat u het model op in een SQL-tabel.

1. Open **SQL Server Management Studio** en maak verbinding met de SQL-database.

   Als u hulp nodig hebt bij het tot stand brengen van een verbinding, raadpleegt u [Quickstart: SQL Server Management Studio gebruiken om verbinding te maken en query's uit te voeren op een Azure SQL database](connect-query-ssms.md).

1. Maak de tabel **CarSpeed** om de trainingsgegevens op te slaan.

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

1. Maak een regressiemodel met `rxLinMod`. 

   Als u het model wilt bouwen, definieert u de formule in de R-code en geeft u de trainingsgegevens **CarSpeed** door als een invoerparameter.

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

1. Maak een tabel waarin u het model opslaat, zodat u het later kunt gebruiken voor voorspellingen. 

   De uitvoer van een R-pakket dat een model maakt, is doorgaans een **binair object**. Daarom moet de tabel een kolom bevatten van het type **VARBINARY (max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Roep nu de opgeslagen procedure aan, genereer het model en sla het op in een tabel.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Opmerking: als u deze code een tweede keer uitvoert, krijgt u deze foutmelding:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Eén manier om deze fout te vermijden, is om de naam bij te werken voor elk nieuw model. U kunt de naam bijvoorbeeld wijzigen in een meer beschrijvende naam, en extra info opnemen, zoals het modeltype, de dag waarop het model is gemaakt, enzovoort.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>De tabel met coëfficiënten weergeven

Over het algemeen is de uitvoer van R van de opgeslagen procedure [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) beperkt tot één gegevensframe. U kunt echter naast het gegevensframe ook uitvoer van andere typen retourneren, zoals scalaire waarden.

Stel bijvoorbeeld, u wilt een model trainen, maar onmiddellijk de tabel met coëfficiënten van het model bekijken. Daartoe maakt u de tabel met coëfficiënten als de hoofdresultatenset en voert u het getrainde model uit in een SQL-variabele. U kunt het model onmiddellijk opnieuw hergebruiken door de variabele aan te roepen, of u kunt het model opslaan zoals hier wordt weergegeven.

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

![Getraind model met extra uitvoer](./media/r-train-score-model-create-quickstart/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Nieuwe gegevens score met behulp van het getrainde model

*Scoren* is een term die in de informatiewetenschap wordt gebruikt voor het genereren van voorspellingen, waarschijnlijkheden of andere waarden, op basis van nieuwe gegevens die worden ingevoerd in een getraind model. U gebruikt het model dat u in de vorige sectie hebt gemaakt om voorspellingen te scoren met nieuwe gegevens.

Hebt u opgemerkt dat de oorspronkelijke trainingsgegevens stoppen bij een snelheid van 25 mijl per uur? Dit komt omdat de oorspronkelijke gegevens zijn gebaseerd op een experiment uit 1920. U vraagt zich misschien af hoe lang het duurt voordat een auto uit de jaren '20 tot stilstand komt als deze 60 mph of zelfs 100 mph rijdt. Om deze vraag te beantwoorden, kunt u nieuwe snelheidswaarden in uw model invoeren.

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

2. Voorspel de stopafstand op basis van deze nieuwe snelheidswaarden.

   Omdat het model is gebaseerd op het algoritme **rxLinMod** dat is opgegeven als onderdeel van het **RevoScaleR**-pakket, roept u de functie [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) aan in plaats van de generieke R-functie `predict`.

   Dit voorbeeldscript:
   - gebruikt de SELECT-instructie om een enkel model op te halen uit de tabel.
   - geeft dit model door als invoerparameter.
   - roept de functie `unserialize` aan voor het model.
   - past de functie `rxPredict` met de juiste argumenten toe op het model.
   - verstrekt de nieuwe invoergegevens.

   > [!TIP]
   > Zie [Serialisatiefuncties](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) van RevoScaleR voor informatie over realtime scoring.

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

   ![Resultatenset voor het voorspellen van de remafstand](./media/r-train-score-model-create-quickstart/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> In dit voorbeeldscript wordt de functie `str` toegevoegd in de testfase om het schema te controleren van de gegevens die worden geretourneerd met R. U kunt deze instructie later verwijderen.
>
> De kolomnamen die worden gebruikt in het R-script, worden niet per se doorgegeven aan de uitvoer van de opgeslagen procedure. Hier definieert het WITH RESULTS-component een aantal nieuwe kolomnamen.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview)](machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](r-script-create-quickstart.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](machine-learning-services-functions.md)
- [Quickstart: Werken met R en SQL-gegevens in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md)
