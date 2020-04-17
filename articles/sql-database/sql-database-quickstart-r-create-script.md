---
title: Eenvoudige R-scripts maken en uitvoeren
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Eenvoudige R-scripts uitvoeren in Azure SQL Database Machine Learning Services (voorbeeld).
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
ms.openlocfilehash: bb6cb6d86933166d2427788d697d9cd38cf04bf0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460168"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Snelstart: eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (voorbeeld)

In deze quickstart maakt en voert u een set R-scripts uit met Machine Learning Services (met R) in Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [Azure SQL-database](sql-database-single-database-get-started.md) met een [firewallregel op serverniveau](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

In dit voorbeeld wordt de opgeslagen procedure [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) gebruikt om een goed gevormd R-script te verpakken.

## <a name="run-a-simple-script"></a>Een eenvoudig script uitvoeren

Als u een R-script wilt uitvoeren, geeft u het als argument door aan de systeemopgeslagen procedure, [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

In de volgende stappen voert u dit voorbeeld R-script uit in uw SQL-database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Open **SQL Server Management Studio** en maak verbinding met uw SQL-database.

   Zie [Quickstart: SQL Server Management Studio gebruiken om verbinding te maken en een Azure SQL-database op te vragen](sql-database-connect-query-ssms.md)als u hulp nodig hebt bij het maken van verbinding.

1. Geef het volledige R-script door aan de [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) opgeslagen procedure.

   Het script wordt `@script` doorgegeven door het argument. Alles in `@script` het argument moet een geldige R-code zijn.

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

   Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw SQL-database. Zie [Bovenstaande voorwaarden.](#prerequisites)

   > [!NOTE]
   > Als u een beheerder bent, u de externe code automatisch uitvoeren. U andere gebruikers toestemming geven met de opdracht:
   <br>**GRANT VOERT EEN EXTERN SCRIPT UIT NAAR** * \<gebruikersnaam\>*.

2. Het juiste resultaat wordt `print` berekend en de functie R retourneert het resultaat naar het venster **Berichten.**

   Het zou er ongeveer zo uit moeten zien.

    **Resultaten**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Een Hello World-script uitvoeren

Een typisch voorbeeldscript is er een die alleen de tekenreeks "Hello World" uitvoert. Voer de volgende opdracht uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

De input voor deze opgeslagen procedure omvat:

| | |
|-|-|
| @language | definieert de taalextensie om aan te roepen, in dit geval R |
| @script | definieert de opdrachten die worden doorgegeven aan de R-runtime. Uw hele R-script moet in dit argument zijn ingesloten, als Unicode-tekst. U de tekst ook toevoegen aan een variabele van het type **nvarchar** en vervolgens de variabele |
| @input_data_1 | gegevens die door de query worden geretourneerd, worden doorgegeven aan de R-runtime, die de gegevens als een gegevensframe retourneert |
|MET RESULTAATSETS | clausule definieert het schema van de geretourneerde gegevenstabel voor SQL Server, waarbij "Hello World" wordt toegevoegd als de kolomnaam, **int** voor het gegevenstype |

De opdracht voert de volgende tekst uit:

| Hallo wereld |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Invoer en uitvoer gebruiken

Standaard accepteert [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) één gegevensset als invoer, die u doorgaans levert in de vorm van een geldige SQL-query. Vervolgens wordt een enkel R-gegevensframe geretourneerd als uitvoer.

Laten we voor nu de standaardinvoer- en uitvoervariabelen van [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)gebruiken: **InputDataSet** en **OutputDataSet**.

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

1. Gebruik `SELECT` de instructie om de tabel op te vragen.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Resultaten**

    ![Inhoud van de RTestData-tabel](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Voer het volgende R-script uit. Het haalt de gegevens uit `SELECT` de tabel met behulp van de instructie, passeert het door de R runtime, en retourneert de gegevens als een gegevensframe. De `WITH RESULT SETS` clausule definieert het schema van de geretourneerde gegevenstabel voor SQL Database en voegt de kolomnaam *NewColName*toe.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Resultaten**

    ![Uitvoer van R-script waarmee gegevens uit een tabel worden geretourneerd](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Laten we nu de namen van de invoer- en uitvoervariabelen wijzigen. De standaardnamen invoer- en uitvoervariabelen zijn **InputDataSet** en **OutputDataSet,** dit script wijzigt de namen in **SQL_in** en **SQL_out:**

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Merk op dat R casegevoelig is. De invoer- en uitvoervariabelen die worden gebruikt in het R-script `@input_data_1_name` ( `@output_data_1_name`**SQL_out**, **SQL_in**) moeten overeenkomen met de waarden die zijn gedefinieerd met en , inclusief de case.

   > [!TIP]
   > Er kan maar één invoergegevensset worden doorgegeven als parameter, en u kunt slechts één gegevensset retourneren. U kunt vanuit de R-code wel andere gegevenssets aanroepen en u kunt, naast de gegevensset, ook andere typen uitvoer retourneren. U kunt ook het uitvoertrefwoord toevoegen aan elke willekeurige parameter om deze te laten retourneren met de resultaten.

1. U ook waarden genereren met alleen het`@input_data_1` R-script zonder invoergegevens (is ingesteld op leeg).

   In het volgende script wordt de tekst "hallo" en "wereld" weergegeven.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Resultaten**

    ![Resultaten doorzoeken met @script als invoer](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>R-versie controleren

Als u wilt zien welke versie van R is geïnstalleerd in uw SQL-database, voert u het volgende script uit.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Met de R-functie `print` wordt de versie geretourneerd in het **Berichtvenster**. In het onderstaande voorbeelduitvoer u zien dat SQL Database in dit geval R-versie 3.4.4 heeft geïnstalleerd.

**Resultaten**

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

Microsoft biedt een aantal R-pakketten waarin Machine Learning Services vooraf zijn geïnstalleerd in de SQL-database.

Als u een lijst wilt zien van welke R-pakketten zijn geïnstalleerd, inclusief versie, afhankelijkheden, licentie- en bibliotheekpadgegevens, voert u het volgende script uit.

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

De uitvoer `installed.packages()` is van in R en wordt geretourneerd als gevolg set.

**Resultaten**

![Geïnstalleerde pakketten in R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Volgende stappen

Als u een machine learning-model wilt maken met R in SQL Database, volgt u deze quickstart:

> [!div class="nextstepaction"]
> [Een voorspellend model maken en trainen in R met Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-quickstart-r-train-score-model.md)

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services with R (preview).

- [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-functions.md)
- [Werken met R- en SQL-gegevens in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-data-issues.md)
