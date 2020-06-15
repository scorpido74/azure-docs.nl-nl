---
title: Eenvoudige R-scripts maken en uitvoeren
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Voer eenvoudige R-scripts in Azure SQL Database Machine Learning Services (preview) uit.
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
ms.openlocfilehash: 9b78b696b42431c744c30c91a730fdc7ec8c1032
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324603"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Quickstart: Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze quickstart maakt u een set van R-scripts en voert u deze uit met Machine Learning Services (met R) in Azure SQL Database.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [database in Azure SQL Database](single-database-create-quickstart.md) met een [firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)
- [Machine Learning Services](machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

In dit voorbeeld wordt de opgeslagen procedure [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) gebruikt om een goed gevormd R-script te verpakken.

## <a name="run-a-simple-script"></a>Een eenvoudig script uitvoeren

Als u een R-script wilt uitvoeren, geeft u het door als een op het systeem opgeslagen procedure, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

In de volgende stappen voert u dit voorbeeld van een R-script uit in uw database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Open **SQL Server Management Studio** en maak verbinding met de database.

   Als u hulp nodig hebt bij het tot stand brengen van een verbinding, raadpleegt u [quickstart: SQL Server Management Studio gebruiken om verbinding te maken en query's uit te voeren op een database in Azure SQL Database](connect-query-ssms.md).

1. Geef het volledige R-script door aan de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) opgeslagen procedure.

   Het script wordt doorgegeven via het argument `@script`. Alles binnen het argument `@script` moet geldige R-code zijn.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw database. Zie het gedeelte [Vereisten](#prerequisites) hierboven.

   > [!NOTE]
   > Als u een beheerder bent, kunt u automatisch externe code uitvoeren. U kunt machtigingen verlenen aan andere gebruikers met behulp van de opdracht:
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>* .

2. Het juiste resultaat wordt berekend en via de R-functie `print` wordt het resultaat geretourneerd in het venster **Berichten**.

   Het ziet er ongeveer als volgt uit:

    **Results**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Een Hallo wereld-script maken

Een typisch voorbeeld is een script dat alleen de tekenreeks Hallo wereld uitvoert. Voer de volgende opdracht uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

De invoer van deze opgeslagen procedure omvat:

| | |
|-|-|
| @language | Hiermee definieert u de taalextensie die moet worden aangeroepen. In dit geval R. |
| @script | Hiermee worden de opdrachten gedefinieerd die worden doorgegeven aan de R-runtime. Het hele R-script moet als Unicode-tekst in dit argument worden ingesloten. U kunt ook de tekst toevoegen aan een variabele van het type **nvarchar** en de variabele vervolgens aanroepen. |
| @input_data_1 | De gegevens die worden geretourneerd met de query, worden doorgegeven aan de R-runtime. Vervolgens worden de gegevens als een gegevensframe geretourneerd. |
|WITH RESULT SETS | Met deze component definieert u het schema van de geretourneerde gegevenstabel en voegt u Hallo wereld toe als kolomnaam en **int** voor het gegevenstype. |

De opdracht voert de volgende tekst uit:

| Hallo wereld |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Invoer en uitvoer gebruiken

[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) accepteert standaard één gegevensset als invoer. Deze levert u meestal in de vorm van een geldige SQL-query. Vervolgens wordt er één R-gegevensframe als uitvoer geretourneerd.

Laten we voor nu alleen de standaardinvoer- en uitvoervariabelen van sp_execute_external_script: [ en ](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) gebruiken. **InputDataSet** en **OutputDataSet**.

1. Maak een kleine tabel met testgegevens.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Gebruik de instructie `SELECT` om de tabel op te vragen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Results**

    ![Inhoud van de RTestData-tabel](./media/r-script-create-quickstart/select-rtestdata.png)

1. Voer het volgende R-script uit. De gegevens worden opgehaald uit de tabel met behulp van de instructie `SELECT` en doorgegeven aan de R-runtime. Vervolgens worden de gegevens geretourneerd als een gegevensframe. Met de component `WITH RESULT SETS` wordt het schema van de geretourneerde gegevenstabel voor SQL Database gedefinieerd en wordt de kolomnaam *NewColName* toegevoegd.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Results**

    ![Uitvoer van R-script waarmee gegevens uit een tabel worden geretourneerd](./media/r-script-create-quickstart/r-output-rtestdata.png)

1. Laten we nu de naam van de invoer- en uitvoervariabelen wijzigen. De standaardnamen voor de invoer- en uitvoervariabele zijn **InputDataSet** en **OutputDataSet**. Met dit script worden de namen gewijzigd in **SQL_in** en **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Houd er rekening mee dat R hoofdlettergevoelig is. De invoer- en uitvoervariabelen die worden gebruikt in het R-script (**SQL_out** **SQL_in**) moeten overeenkomen met de waarden die zijn gedefinieerd met `@input_data_1_name` en `@output_data_1_name` (hoofdlettergevoelig).

   > [!TIP]
   > Er kan maar één invoergegevensset worden doorgegeven als parameter, en u kunt slechts één gegevensset retourneren. U kunt vanuit de R-code wel andere gegevenssets aanroepen en u kunt, naast de gegevensset, ook andere typen uitvoer retourneren. U kunt ook het uitvoertrefwoord toevoegen aan elke willekeurige parameter om deze te laten retourneren met de resultaten.

1. U kunt ook waarden genereren met alleen het R-script zonder invoergegevens (`@input_data_1` is ingesteld op leeg).

   Met het volgende script wordt de tekst hallo en wereld uitgevoerd.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Results**

    ![Resultaten doorzoeken met @script als invoer](./media/r-script-create-quickstart/r-data-generated-output.png)

## <a name="check-r-version"></a>R-versie controleren

Als u wilt zien welke versie van R is geïnstalleerd in uw database, voert u het volgende script uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Met de R-functie `print` wordt de versie geretourneerd in het **Berichtvenster**. In de voorbeelduitvoer hieronder ziet u dat R-versie 3.4.4 is geïnstalleerd in de SQL-database.

**Results**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>R-pakketten vermelden

Microsoft biedt een aantal R-pakketten waarin Machine Learning Services vooraf zijn geïnstalleerd in de database.

Voer het volgende script uit om een lijst met de geïnstalleerde R-pakketten te zien, inclusief informatie over de versie, de afhankelijkheden, de licentie en het bibliotheekpad.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

De uitvoer komt uit `installed.packages()` in R en wordt geretourneerd als een resultatenset.

**Results**

![Geïnstalleerde pakketten in R](./media/r-script-create-quickstart/r-installed-packages.png)

## <a name="next-steps"></a>Volgende stappen

Als u een Machine Learning-model met R in SQL Database wilt maken, volgt u deze quickstart:

> [!div class="nextstepaction"]
> [Een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (preview)](r-train-score-model-create-quickstart.md)

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview)](machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](machine-learning-services-functions.md)
- [Werken met R en SQL-gegevens in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md)
