---
title: Een R-pakket toevoegen aan Machine Learning Services (preview)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In dit artikel wordt uitgelegd hoe u een R-pakket installeert dat nog niet is geïnstalleerd in Azure SQL Database Machine Learning Services (preview).
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
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 289b1346fce2e79a2c1e546f5e42e98734ef800c
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84024414"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt uitgelegd hoe u een R-pakket toevoegt aan Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Installeer [R](https://www.r-project.org) en [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) op uw lokale computer. R is beschikbaar voor Windows, macOS en Linux. In dit artikel wordt ervan uitgegaan dat u Windows gebruikt.

- Dit artikel bevat een voorbeeld van het gebruik van [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) om een R-script uit te voeren in Azure SQL Database. U kunt R-scripts uitvoeren met andere databasebeheer- of queryhulpprogramma's, maar in dit voorbeeld wordt uitgegaan van Azure Data Studio of SQL Server Management Studio.
   
> [!NOTE]
> U kunt een pakket niet installeren door een R-script uit te voeren met behulp van **sp_execute_external_script** in Azure Data Studio of SQL Server Management Studio. U kunt alleen pakketten installeren en verwijderen met behulp van de R-opdrachtregel en RStudio, zoals beschreven in dit artikel. Zodra het pakket is geïnstalleerd, hebt u toegang tot de pakketfuncties in een R-script via **sp_execute_external_script**.

## <a name="list-r-packages"></a>R-pakketten vermelden

Microsoft biedt een aantal R-pakketten waarin Machine Learning Services vooraf is geïnstalleerd in de Azure SQL-database.
U kunt een lijst met geïnstalleerde R-pakketten weergeven door de volgende opdracht uit te voeren in Azure Data Studio of SQL Server Management Studio.

1. Open Azure Data Studio of SQL Server Management Studio en maak verbinding met uw Azure SQL Database.

1. Voer de volgende opdracht uit:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
WITH RESULT SETS((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            ));
```

De uitvoer moet er ongeveer als volgt uitzien.

**Results**

![Geïnstalleerde pakketten in R](./media/machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Een pakket toevoegen met sqlmlutils

Als u een pakket wilt gebruiken dat nog niet is geïnstalleerd in de Azure SQL Database, kunt u dit installeren met behulp van [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** is een pakket dat is ontworpen om gebruikers te helpen met de interactie met Microsoft Azure SQL- en SQL Server-databases en om R- of Python-code uit te voeren in SQL vanuit een R- of Python-client. Op dit moment wordt alleen de R-versie van **sqlmlutils** ondersteund in Azure SQL Database.

In het volgende voorbeeld installeert u het pakket **[glue](https://cran.r-project.org/web/packages/glue/)** , waarmee u tekenreeksen kunt opmaken en interpoleren. Met deze stappen installeert u **sqlmlutils** en **RODBCext** (een vereiste voor **sqlmlutils**) en voegt u het pakket **glue** toe.

### <a name="install-sqlmlutils"></a>**sqlmlutils** installeren

1. Download het nieuwste ZIP-bestand **sqlmlutils** van https://github.com/Microsoft/sqlmlutils/tree/master/R/dist op uw lokale computer. U hoeft het bestand niet uit te pakken.

1. Open een **opdrachtprompt** en voer de volgende opdrachten uit om **RODBCext** en **sqlmlutils** te installeren op uw lokale computer. Vervang het volledige pad naar het ZIP-bestand **sqlmlutils** dat u hebt gedownload (in het voorbeeld wordt ervan uitgegaan dat het bestand zich in de map Documenten bevindt).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    De uitvoer moet er ongeveer als volgt uitzien.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Als u de foutmelding 'R wordt niet herkend als een interne of externe opdracht, programma of batchbestand' krijgt, betekent dit waarschijnlijk dat het pad naar R.exe niet is opgenomen in de omgevingsvariabele **PATH** in Windows. U kunt het pad toevoegen aan de omgevingsvariabele of u kunt naar de map gaan in de opdrachtprompt (bijvoorbeeld `cd C:\Program Files\R\R-3.5.3\bin`) en vervolgens de opdracht opnieuw uitvoeren.

### <a name="add-the-package"></a>Het pakket toevoegen

1. Open RStudio en maak een nieuw **R Script**-bestand. 

1. Gebruik de volgende R-code om het pakket **glue** te installeren met behulp van **sqlmlutils**. Vervang uw eigen Azure SQL Database-verbindingsgegevens.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > Het **bereik** kan **PUBLIC** of **PRIVATE** zijn. Een openbaar bereik is handig voor de databasebeheerder om pakketten te installeren die alle gebruikers kunnen gebruiken. Bij een privébereik is het pakket alleen beschikbaar voor de gebruiker die het installeert. Als u het bereik niet opgeeft, is het standaardbereik **PRIVATE**.

### <a name="verify-the-package"></a>Het pakket controleren

Controleer of het pakket **glue** is geïnstalleerd door het volgende R-script uit te voeren in RStudio. Gebruik de **verbinding** die u in de vorige stap hebt gedefinieerd.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Inhoud van de RTestData-tabel](./media/machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Het pakket gebruiken

Zodra het pakket is geïnstalleerd, kunt u het gebruiken in een R-script via **sp_execute_external_script**.

1. Open Azure Data Studio of SQL Server Management Studio en maak verbinding met uw Azure SQL Database.

1. Voer de volgende opdracht uit:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    U ziet het volgende resultaat op het tabblad **Berichten**.

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Het pakket verwijderen

Als u het pakket wilt verwijderen, voert u het volgende R-script uit in RStudio. Gebruik de **verbinding** die u eerder hebt gedefinieerd.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Een andere manier om een R-pakket te installeren in uw Azure SQL Database is door het R-pakket te uploaden vanuit een bytestroom met behulp van de T-SQL-instructie **CREATE EXTERNAL LIBRARY**. Zie [Een bibliotheek maken op basis van een bytestroom](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) in de naslagdocumentatie [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview)](machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](machine-learning-services-functions.md)
- [Werken met R en SQL-gegevens in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md)