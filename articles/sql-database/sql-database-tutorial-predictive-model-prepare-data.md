---
title: 'Zelfstudie: Gegevens voorbereiden om een voorspellend model in R te trainen'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel één van deze driedelige zelfstudiereeks bereidt u de gegevens uit een Azure SQL-database voor om een voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: 505f58f13a7186948a228fefe872d74fb98eba33
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345779"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Gegevens voorbereiden om een voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (voorbeeld)

In deel één van deze driedelige zelfstudiereeks importeert en bereidt u gegevens uit een Azure SQL-database met R. Later in deze serie gebruikt u deze gegevens om een voorspellend machine learning-model in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

Voor deze zelfstudieserie u zich voorstellen dat u eigenaar bent van een skiverhuurbedrijf en dat u het aantal huren wilt voorspellen dat u op een toekomstige datum zult hebben. Deze informatie zal u helpen uw voorraad, personeel en faciliteiten klaar te krijgen.

In deel één en twee van deze serie ontwikkel je een aantal R-scripts in RStudio om je gegevens voor te bereiden en een machine learning-model te trainen. Vervolgens, in deel drie, voer je die R-scripts uit in een SQL-database met behulp van opgeslagen procedures.

In dit artikel leer je hoe je:

> [!div class="checklist"]
> * Een voorbeelddatabase importeren in een Azure SQL-database met R
> * De gegevens uit de Azure SQL-database in een R-gegevensframe laden
> * De gegevens in R voorbereiden door sommige kolommen als categorisch te identificeren

In [deel twee](sql-database-tutorial-predictive-model-build-compare.md)leer je hoe je meerdere machine learning-modellen in R maakt en traint en kies je vervolgens de meest nauwkeurige.

In [deel drie](sql-database-tutorial-predictive-model-deploy.md)leert u hoe u het model in een database opslaan en vervolgens opgeslagen procedures maakt uit de R-scripts die u in deel één en twee hebt ontwikkeld. De opgeslagen procedures worden uitgevoerd in een SQL-database om voorspellingen te doen op basis van nieuwe gegevens.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

* Azure SQL Database Server met Machine Learning Services ingeschakeld - Tijdens de openbare preview zal Microsoft u aan boord nemen en machine learning inschakelen voor uw bestaande of nieuwe databases. Volg de stappen in [Meld u aan voor de preview-versie](sql-database-machine-learning-services-overview.md#signup).

* RevoScaleR-pakket - Zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE - Deze zelfstudie maakt gebruik van [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-querytool - Deze zelfstudie gaat ervan uit dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeelddatabase importeren

De voorbeeldgegevensset die in deze zelfstudie wordt gebruikt, is opgeslagen in een .bacpac-databaseback-upbestand dat u downloaden en gebruiken. **.bacpac**

1. Download het bestand [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Volg de aanwijzingen in [Een BACPAC-bestand importeren om een Azure SQL-database te maken,](https://docs.microsoft.com/azure/sql-database/sql-database-import)met behulp van deze gegevens:

   * Importeren uit het **bestand TutorialDB.bacpac** dat u hebt gedownload
   * Kies tijdens de openbare preview de **Gen5/vCore-configuratie** voor de nieuwe database
   * De nieuwe database "TutorialDB" een naam geven

## <a name="load-the-data-into-a-data-frame"></a>De gegevens in een gegevensframe laden

Als u de gegevens in R wilt gebruiken, laadt u de`rentaldata`gegevens uit de Azure SQL-database in een gegevensframe ( ).

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit. Vervang **Server,** **UID**en **PWD** door uw eigen verbindingsgegevens.

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

U ziet resultaten die vergelijkbaar zijn met het volgende.

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

In deze voorbeelddatabase is het grootste deel van de voorbereiding al gedaan, maar je zult hier nog een voorbereiding doen.
Gebruik het volgende R-script om drie kolommen als *categorieën* te identificeren door de gegevenstypen te wijzigen in *factor*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

U ziet resultaten die vergelijkbaar zijn met het volgende.

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

De gegevens zijn nu voorbereid voor training.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verder gaat met deze zelfstudie, verwijdert u de TutorialDB-database van uw Azure SQL Database-server.

Voer de volgende stappen uit vanuit de Azure-portal:

1. Selecteer alle **bronnen** of **SQL-databases**in het linkermenu in de Azure-portal.
1. Typ **TutorialDB**en selecteer uw abonnement in het veld **Filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze zelfstudiereeks hebt u de volgende stappen voltooid:

* Een voorbeelddatabase importeren in een Azure SQL-database met R
* De gegevens uit de Azure SQL-database in een R-gegevensframe laden
* De gegevens in R voorbereiden door sommige kolommen als categorisch te identificeren

Als u een machine learning-model wilt maken dat gegevens uit de TutorialDB-database gebruikt, volgt u deel twee van deze zelfstudiereeks:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-tutorial-predictive-model-build-compare.md)
