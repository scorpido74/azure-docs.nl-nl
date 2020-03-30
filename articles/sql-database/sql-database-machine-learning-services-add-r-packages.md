---
title: Een R-pakket toevoegen aan Machine Learning Services (voorbeeld)
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
ms.openlocfilehash: ce85f45d823df42e70af53824e175968439621d3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73819871"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (voorbeeld)

In dit artikel wordt uitgelegd hoe u een R-pakket toevoegt aan Azure SQL Database Machine Learning Services (voorbeeld).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Installeer [R](https://www.r-project.org) en [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) op uw lokale computer. R is beschikbaar voor Windows, MacOS en Linux. In dit artikel wordt ervan uitgegaan dat u Windows gebruikt.

- Dit artikel bevat een voorbeeld van het gebruik van [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) om een R-script uit te voeren in Azure SQL Database. U R-scripts uitvoeren met andere databasebeheer- of queryhulpprogramma's, maar in dit voorbeeld wordt ervan uitgegaan dat Azure Data Studio of SSMS.
   
> [!NOTE]
> U een pakket niet installeren door een R-script uit te voeren met **sp_execute_external_script** in Azure Data Studio of SSMS. U pakketten alleen installeren en verwijderen met de R-opdrachtregel en RStudio zoals beschreven in dit artikel. Zodra het pakket is geïnstalleerd, hebt u toegang tot de pakketfuncties in een R-script met behulp van **sp_execute_external_script.**

## <a name="list-r-packages"></a>R-pakketten vermelden

Microsoft biedt een aantal R-pakketten die vooraf zijn geïnstalleerd met Machine Learning Services in uw Azure SQL-database.
U een lijst met geïnstalleerde R-pakketten bekijken door de volgende opdracht uit te voeren in Azure Data Studio of SSMS.

1. Open Azure Data Studio of SSMS en maak verbinding met uw Azure SQL Database.

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

De uitvoer moet er hetzelfde uitzien als het volgende.

**Resultaten**

![Geïnstalleerde pakketten in R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Een pakket toevoegen met sqlmlutils

Als u een pakket moet gebruiken dat nog niet is geïnstalleerd in uw Azure SQL Database, u het installeren met [sqlmlutils.](https://github.com/Microsoft/sqlmlutils) **sqlmlutils** is een pakket dat is ontworpen om gebruikers te helpen communiceren met SQL-databases (SQL Server en Azure SQL Database) en R- of Python-code in SQL uit te voeren vanuit een R- of Python-client. Momenteel wordt alleen de **R-versie van sqlmlutils** ondersteund in Azure SQL Database.

In het volgende voorbeeld installeert u het **[lijmpakket](https://cran.r-project.org/web/packages/glue/)** dat tekenreeksen kan opmaken en interpoleren. Deze stappen installeren **sqlmlutils** en **RODBCext** (een voorwaarde voor **sqlmlutils)** en voegen het **lijmpakket** toe.

### <a name="install-sqlmlutils"></a>**Sqlmlutils installeren**

1. Download het nieuwste **sqlmlutils** zip-bestand van https://github.com/Microsoft/sqlmlutils/tree/master/R/dist naar uw lokale computer. U hoeft het bestand niet uit te ritsen.

1. Open een **opdrachtprompt** en voer de volgende opdrachten uit om **RODBCext** en **sqlmlutils** op uw lokale computer te installeren. Vervang het volledige pad naar het **sqlmlutils** zip-bestand dat u hebt gedownload (in het voorbeeld wordt ervan uitgegaan dat het bestand zich in de map Documenten bevindt).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    De uitvoer die u ziet, moet vergelijkbaar zijn met de volgende.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Als u de fout krijgt, wordt "R" niet herkend als een interne of externe opdracht, een operabenbaar programma of batchbestand", dit betekent waarschijnlijk dat het pad naar R.exe niet is opgenomen in de variabele **PATH-omgeving** op Windows. U het pad toevoegen aan de omgevingsvariabele of naar de `cd C:\Program Files\R\R-3.5.3\bin`map in de opdrachtprompt (bijvoorbeeld) navigeren en de opdracht vervolgens opnieuw proberen.

### <a name="add-the-package"></a>Het pakket toevoegen

1. Open RStudio en maak een nieuw **R Script**-bestand. 

1. Gebruik de volgende R-code om het **lijmpakket** te installeren met **sqlmlutils.** Vervang uw eigen Azure SQL Database-verbindingsgegevens.

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
    > Het **toepassingsgebied** kan **openbaar** of **privé**zijn. Een openbaar bereik is handig voor de databasebeheerder om pakketten te installeren die alle gebruikers kunnen gebruiken. Private scope maakt het pakket alleen beschikbaar voor de gebruiker die het installeert. Als u het bereik niet opgeeft, is het standaardbereik **PRIVATE**.

### <a name="verify-the-package"></a>Het pakket verifiëren

Controleer of het **lijmpakket** is geïnstalleerd door het volgende R-script in RStudio uit te voeren. Gebruik dezelfde **verbinding** die u in de vorige stap hebt gedefinieerd.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Resultaten**

![Inhoud van de RTestData-tabel](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Het pakket gebruiken

Zodra het pakket is geïnstalleerd, u het gebruiken in een R-script via **sp_execute_external_script.**

1. Open Azure Data Studio of SSMS en maak verbinding met uw Azure SQL Database.

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

    U ziet het volgende resultaat op het tabblad **Berichten.**

    **Resultaten**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Het pakket verwijderen

Als u het pakket wilt verwijderen, voert u het volgende R-script uit in RStudio. Gebruik dezelfde **verbinding** die u eerder hebt gedefinieerd.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Een andere manier om een R-pakket te installeren in uw Azure SQL-database is door het R-pakket te uploaden vanuit een bytestream met de T-SQL-instructie **VOOR EXTERNE BIBLIOTHEEK MAKEN.** Zie [Een bibliotheek maken vanuit een bytestream](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) in de [referentiedocumentatie VOOR EXTERNE BIBLIOTHEEK MAKEN.](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services with R (preview).

- [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-functions.md)
- [Werken met R- en SQL-gegevens in Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-machine-learning-services-data-issues.md)