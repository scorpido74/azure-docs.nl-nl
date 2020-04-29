---
title: 'Zelf studie: gegevens voorbereiden om een voorspellend model in R te trainen'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel één van deze reeks met drie zelf studies gaat u de gegevens voorbereiden van een Azure-SQL database om een voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: bf69d2963c74723cb3fea542e28288e4f136d5c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81458757"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelf studie: gegevens voorbereiden om een voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (preview-versie)

In deel één van deze reeks met drie delen zelf studies kunt u gegevens uit een Azure-SQL database importeren en voorbereiden met behulp van R. Verderop in deze reeks gebruikt u deze gegevens om een voorspellend machine learning model in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Stel dat u voor deze reeks zelf studies eigenaar bent van een ski-huur bedrijf en u het aantal huren wilt voors pellen dat u in een toekomstige datum hebt. Deze informatie helpt u bij het voorbereiden van uw voor Raad, personeel en faciliteiten.

In delen één en twee van deze reeksen ontwikkelt u enkele R-scripts in RStudio om uw gegevens voor te bereiden en een machine learning model te trainen. In deel drie voert u de R-scripts in een SQL database uit met behulp van opgeslagen procedures.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een voorbeeld database importeren in een Azure-SQL database met R
> * De gegevens van de Azure-SQL database in een R-gegevens frame laden
> * De gegevens voorbereiden in R door bepaalde kolommen te identificeren als categorische

In [deel twee](sql-database-tutorial-predictive-model-build-compare.md)leert u hoe u meerdere machine learning modellen maakt en traint in R. vervolgens kiest u de meest nauw keurige versie.

In [deel drie](sql-database-tutorial-predictive-model-deploy.md)leert u hoe u het model opslaat in een Data Base en vervolgens opgeslagen procedures maakt vanuit de R-scripts die u hebt ontwikkeld in delen één en twee. De opgeslagen procedures worden in een SQL database uitgevoerd om voor spellingen te maken op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u nog geen abonnement op Azure hebt, moet u [een account maken](https://azure.microsoft.com/free/) voordat u begint.

* [Azure SQL database met Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md) ingeschakeld.

* RevoScaleR-pakket: Zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE: in deze zelf studie wordt [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)gebruikt.

* Hulp programma voor SQL-query's-in deze zelf studie wordt ervan uitgegaan dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeeld database importeren

De voor beeld-gegevensset die in deze zelf studie wordt gebruikt, is opgeslagen in een **. Bacpac** -back-upbestand dat u kunt downloaden en gebruiken.

1. Down load het bestand [tutorialdb maakt. Bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Volg de instructies in [een BACPAC-bestand importeren om een Azure SQL database te maken](https://docs.microsoft.com/azure/sql-database/sql-database-import)met behulp van de volgende gegevens:

   * Importeren uit het **tutorialdb maakt. Bacpac** -bestand dat u hebt gedownload
   * Kies tijdens de open bare Preview de configuratie van de **GEN5-vCore** voor de nieuwe data base
   * Naam van de nieuwe data base ' Tutorialdb maakt '

## <a name="load-the-data-into-a-data-frame"></a>De gegevens in een gegevens frame laden

Als u de gegevens in R wilt gebruiken, laadt u de gegevens van de Azure-SQL database in een`rentaldata`gegevens frame ().

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit. Vervang **Server**, **UID**en **pwd** door uw eigen verbindings gegevens.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

De resultaten moeten er ongeveer als volgt uitzien.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>De gegevens voorbereiden

In deze voorbeeld database is het grootste deel van de voor bereiding al gedaan, maar u kunt hier nog een voor bereiding doen.
Gebruik het volgende R-script om drie kolommen als *Categorieën* te identificeren door de gegevens typen te wijzigen in *factor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

De resultaten moeten er ongeveer als volgt uitzien.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

De gegevens worden nu voor bereid voor de training.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verder gaat met deze zelf studie, verwijdert u de Tutorialdb maakt-data base van uw Azure SQL Database-Server.

Voer de volgende stappen uit op de Azure Portal:

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** of **SQL-data bases**.
1. Typ **tutorialdb maakt**in het veld **filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw Tutorialdb maakt-data base.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze reeks zelf studies hebt u de volgende stappen uitgevoerd:

* Een voorbeeld database importeren in een Azure-SQL database met R
* De gegevens van de Azure-SQL database in een R-gegevens frame laden
* De gegevens voorbereiden in R door bepaalde kolommen te identificeren als categorische

Als u een machine learning model wilt maken dat gebruikmaakt van gegevens uit de Tutorialdb maakt-data base, volgt u deel twee van deze reeks zelf studies:

> [!div class="nextstepaction"]
> [Zelf studie: een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-build-compare.md)
