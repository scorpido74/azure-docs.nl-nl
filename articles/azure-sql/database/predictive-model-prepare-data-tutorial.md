---
title: 'Zelfstudie: Gegevens voorbereiden om een voorspellend model te trainen in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel één van deze reeks met drie zelfstudies gaat u de gegevens van een database in Azure SQL Database voorbereiden om een voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: 698cc089f770d60b6399864c9832fbc8d104c16f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253797"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Gegevens voorbereiden om een voorspellend model te trainen in R met Azure SQL Database Machine Learning Services (preview)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel één van deze reeks met drie zelfstudies gaat u gegevens uit een database in Azure SQL Database importeren en voorbereiden met behulp van R. Verderop in deze reeks gebruikt u deze gegevens om een voorspellend machine learning-model in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Stel u voor deze reeks zelfstudies voor dat u eigenaar bent van een skiverhuurbedrijf en dat u de huuropbrengsten wilt voorspellen die u op een toekomstige datum hebt. Deze informatie helpt u bij de voorbereiding v an uw voorraad, medewerkers en faciliteiten.

In delen één en twee van deze reeks ontwikkelt u enkele R-scripts in RStudio om uw gegevens voor te bereiden en een machine learning-model te trainen. In deel drie voert u deze R-scripts uit in een database met behulp van opgeslagen procedures.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een voorbeelddatabase importeren in een database in Azure SQL Database met R
> * De gegevens laden van een database naar een R-gegevensframe
> * De gegevens voorbereiden in R door bepaalde kolommen te identificeren als categorisch

In [deel twee](predictive-model-build-compare-tutorial.md) leert u hoe u meerdere machine learning-modellen maakt en traint in R en hoe u vervolgens de meest nauwkeurige kiest.

In [deel drie](predictive-model-deploy-tutorial.md) leert u hoe u het model opslaat in een database en vervolgens opgeslagen procedures maakt vanuit de R-scripts die u hebt ontwikkeld in delen één en twee. De opgeslagen procedures worden in een database uitgevoerd om voorspellingen te doen op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

* [Azure SQL Database met Machine Learning Services (met R)](machine-learning-services-overview.md) ingeschakeld.

* RevoScaleR-pakket: zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE: in deze zelfstudie wordt gebruikgemaakt van [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-query-hulpprogramma: in deze zelfstudie wordt ervan uitgegaan dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeelddatabase importeren

De voorbeeldgegevensset die in deze zelfstudie wordt gebruikt, is opgeslagen in een **.bacpac**-databaseback-upbestand dat u kunt downloaden en gebruiken.

1. Download het bestand [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Volg de instructies in [Een BACPAC-bestand importeren naar een database in Azure SQL Database of Azure SQL Managed Instance](../../azure-sql/database/database-import.md) met behulp van de volgende gegevens:

   * Import vanuit het bestand **TutorialDB.bacpac** dat u hebt gedownload
   * Kies tijdens de openbare preview de configuratie **GEN5/vCore** voor de nieuwe database
   * Geef de nieuwe database de naam TutorialDB

## <a name="load-the-data-into-a-data-frame"></a>Laad de gegevens in een gegevensframe

Als u de gegevens wilt gebruiken in R, laadt u de gegevens vanuit de database naar een gegevensframe (`rentaldata`).

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit. Vervang **Server**, **UID** en **PWD** door uw eigen verbindingsgegevens.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
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

U ziet als het goed is resultaten die vergelijkbaar zijn met de volgende:

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

In deze voorbeelddatabase is het grootste deel van de voorbereiding al gedaan, maar u gaat u nog één andere voorbereiding doen.
Gebruik het volgende R-script om drie kolommen aan te duiden als *categorieën* door de gegevenstypen te wijzigen in *factor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

U ziet als het goed is resultaten die vergelijkbaar zijn met de volgende:

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

De gegevens zijn nu voorbereid voor de training.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verdergaat met deze zelfstudie, kunt u de database TutorialDB verwijderen van uw server.

Volg deze stappen vanuit Azure Portal:

1. Selecteer in het linkermenu in Azure Portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** **TutorialDB** in en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze reeks zelfstudies hebt u deze stappen voltooid:

* Een voorbeelddatabase importeren in een database in Azure SQL Database met R
* De gegevens laden van een database naar een R-gegevensframe
* De gegevens voorbereiden in R door bepaalde kolommen te identificeren als categorisch

Volg deel twee van deze reeks zelfstudies om een machine learning-model te maken dat gegevens uit de TutorialDB-database gebruikt:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)](predictive-model-build-compare-tutorial.md)
