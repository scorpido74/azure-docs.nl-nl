---
title: Een R-pakket toevoegen aan Machine Learning Services (preview-versie)
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819871"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (preview-versie)

In dit artikel wordt uitgelegd hoe u een R-pakket toevoegt aan Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Installeer [R](https://www.r-project.org) en [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) op uw lokale computer. R is beschikbaar voor Windows, MacOS en Linux. In dit artikel wordt ervan uitgegaan dat u Windows gebruikt.

- Dit artikel bevat een voor beeld van het gebruik van [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) om een R-script in Azure SQL database uit te voeren. U kunt R-scripts uitvoeren met andere database beheer-of query hulpprogramma's, maar in dit voor beeld wordt ervan uitgegaan dat Azure Data Studio of SSMS.
   
> [!NOTE]
> U kunt een pakket niet installeren door een R-script uit te voeren met behulp van **sp_execute_external_script** in azure Data Studio of SSMS. U kunt alleen pakketten installeren en verwijderen met de R-opdracht regel en RStudio zoals beschreven in dit artikel. Zodra het pakket is geïnstalleerd, kunt u met behulp van **sp_execute_external_script**toegang krijgen tot de pakket functies in een R-script.

## <a name="list-r-packages"></a>R-pakketten vermelden

Micro soft biedt een aantal R-pakketten die vooraf zijn geïnstalleerd met Machine Learning Services in uw Azure SQL database.
U kunt een lijst met geïnstalleerde R-pakketten weer geven door de volgende opdracht uit te voeren in Azure Data Studio of SSMS.

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

De uitvoer moet er ongeveer als volgt uitzien.

**Results**

![Geïnstalleerde pakketten in R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Een pakket met sqlmlutils toevoegen

Als u een pakket moet gebruiken dat nog niet in uw Azure SQL Database is geïnstalleerd, kunt u het installeren met behulp van [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** is een pakket dat is ontworpen om gebruikers te helpen bij het werken met SQL-data bases (SQL Server en Azure SQL database) en om r-of python-code uit te voeren in SQL vanuit een R-of python-client. Op dit moment wordt alleen de R-versie van **sqlmlutils** ondersteund in Azure SQL database.

In het volgende voor beeld installeert u het **[lijm](https://cran.r-project.org/web/packages/glue/)** pakket dat teken reeksen kan Format teren en interpoleren. Met deze stappen installeert u **sqlmlutils** en **RODBCext** (een vereiste voor **sqlmlutils**) en voegt u het **lijm** pakket toe.

### <a name="install-sqlmlutils"></a>**Sqlmlutils** installeren

1. Down load het meest recente **sqlmlutils** zip-bestand van https://github.com/Microsoft/sqlmlutils/tree/master/R/dist naar uw lokale computer. U hoeft het bestand niet uit te pakken.

1. Open een **opdracht prompt** en voer de volgende opdrachten uit om **RODBCext** en **sqlmlutils** te installeren op uw lokale computer. Vervang het volledige pad naar het **sqlmlutils** zip-bestand dat u hebt gedownload (in het voor beeld wordt ervan uitgegaan dat het bestand zich in de map documenten bevindt).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    De uitvoer die u ziet, moet er ongeveer als volgt uitzien.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Als u de fout melding krijgt, wordt ' R ' niet herkend als een interne of externe opdracht, programma of batch bestand '. Dit betekent waarschijnlijk dat het pad naar R. exe niet is opgenomen in de omgevings variabele **Path** in Windows. U kunt het pad toevoegen aan de omgevings variabele of naar de map in de opdracht prompt gaan (bijvoorbeeld `cd C:\Program Files\R\R-3.5.3\bin`) en de opdracht vervolgens opnieuw proberen.

### <a name="add-the-package"></a>Het pakket toevoegen

1. Open RStudio en maak een nieuw **R-script** bestand. 

1. Gebruik de volgende R-code om het **lijm** pakket te installeren met behulp van **sqlmlutils**. Vervang uw eigen Azure SQL Database verbindings gegevens.

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
    > Het **bereik** kan **openbaar** of **privé**zijn. Een openbaar bereik is handig voor de databasebeheerder om pakketten te installeren die alle gebruikers kunnen gebruiken. Met een persoonlijk bereik is het pakket alleen beschikbaar voor de gebruiker die de app installeert. Als u het bereik niet opgeeft, is het standaardbereik **PRIVATE**.

### <a name="verify-the-package"></a>Het pakket verifiëren

Controleer of het **lijm** pakket is geïnstalleerd door het volgende R-script uit te voeren in RStudio. Gebruik dezelfde **verbinding** die u in de vorige stap hebt gedefinieerd.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Results**

![Inhoud van de RTestData-tabel](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Het pakket gebruiken

Zodra het pakket is geïnstalleerd, kunt u het gebruiken in een R-script via **sp_execute_external_script**.

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

    U ziet het volgende resultaat op het tabblad **berichten** .

    **Results**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Het pakket verwijderen

Als u het pakket wilt verwijderen, voert u het volgende R-script uit in RStudio. Gebruik dezelfde **verbinding** die u eerder hebt gedefinieerd.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Een andere manier om een R-pakket te installeren op uw Azure-SQL database is het verzenden van het R-pakket vanuit een byte stroom met behulp van de T-SQL-instructie **Create External Library** . Zie [een bibliotheek maken op basis van een byte stroom](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) in de referentie documentatie voor het [maken van externe bibliotheken](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) .

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure SQL Database Machine Learning Services met R (preview).

- [Azure SQL Database Machine Learning Services met R (preview-versie)](sql-database-machine-learning-services-overview.md)
- [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](sql-database-machine-learning-services-functions.md)
- [Werken met R-en SQL-gegevens in Azure SQL Database Machine Learning Services (preview-versie)](sql-database-machine-learning-services-data-issues.md)