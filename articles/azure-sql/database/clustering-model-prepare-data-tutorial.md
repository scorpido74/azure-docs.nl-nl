---
title: 'Zelfstudie: Gegevens voorbereiden om clustering in R uit te voeren'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel een van deze reeks met drie zelfstudies gaat u de gegevens van een database in Azure SQL Database voorbereiden om clustering in R te uit te voeren met Azure SQL Database Machine Learning Services (preview).
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
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a23dbd150dbe8ab05e0d4cf1f3decd67a856cbf4
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251247"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Gegevens voorbereiden om clustering in R uit te voeren met Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel een van deze reeks met drie zelfstudies gaat u de gegevens uit een database in Azure SQL Database importeren en voorbereiden met behulp van R. Verderop in deze reeks gebruikt u deze gegevens om een clustering-model in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

*Clustering* is het organiseren van gegevens in groepen waarvan de groepsleden op een of andere manier op elkaar lijken.
U gebruikt het **K-Means**-algoritme om klanten te groeperen in een gegevensset van productaankopen en -retouren. Door klanten te groeperen (clustering) kunt u uw marketinginspanningen effectiever richten op specifieke groepen.
K-Means-clustering is een *leeralgoritme zonder toezicht* dat naar patronen zoekt in gegevens op basis van overeenkomsten.

In delen één en twee van deze reeks ontwikkelt u enkele R-scripts in RStudio om uw gegevens voor te bereiden en een machine learning-model te trainen. In deel drie voert u deze R-scripts uit in de database met behulp van opgeslagen procedures.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een voorbeelddatabase importeren in Azure SQL Database
> * Klanten differentiëren op verschillende aspecten met R
> * De gegevens laden van een database naar een R-gegevensframe

In [deel twee](clustering-model-build-tutorial.md) leert u hoe u een K-means-clusteringmodel in R maakt en traint.

In [deel drie](clustering-model-deploy-tutorial.md) leert u hoe u een opgeslagen procedure maakt die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u nog geen abonnement op Azure hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

* [Azure SQL Database met Machine Learning Services (met R)](machine-learning-services-overview.md) ingeschakeld.

* RevoScaleR-pakket: zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE: in deze zelfstudie wordt gebruikgemaakt van [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-query-hulpprogramma: in deze zelfstudie wordt ervan uitgegaan dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeelddatabase importeren

De voorbeeldgegevensset die in deze zelfstudie wordt gebruikt, is opgeslagen in een **.bacpac**-databaseback-upbestand dat u kunt downloaden en gebruiken. Deze gegevensset is afgeleid van de door de [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp) geboden gegevensset [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp).

1. Download het bestand [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Volg de instructies in [Een BACPAC-bestand importeren naar een database in Azure SQL Database of Azure SQL Managed Instance](../../azure-sql/database/database-import.md) met behulp van de volgende gegevens:

   * Importeer vanuit het bestand **tpcxbb_1gb.bacpac** dat u hebt gedownload
   * Kies tijdens de openbare preview de configuratie **GEN5/vCore** voor de nieuwe database
   * Geef de nieuwe database de naam 'tpcxbb_1gb'

## <a name="separate-customers"></a>Klanten differentiëren

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit.
U onderscheidt klanten in de SQL-query volgens de volgende aspecten:

* **orderRatio** = verhouding van retourbestellingen (totaal aantal bestellingen gedeeltelijk of geheel geretourneerd ten opzichte van het totaal aantal bestellingen)
* **itemsRatio** = verhouding van retourartikelen (totaal aantal geretourneerde artikelen ten opzichte van het aantal gekochte artikelen)
* **monetaryRatio** = verhouding geretourneerd bedrag (totaalbedrag van geretourneerde artikelen ten opzichte van het aankoopbedrag)
* **frequency** = retourfrequentie

Vervang in de functie **plakken**, **Server**, **UID** en **PWD** door uw eigen verbindingsgegevens.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>Laad de gegevens in een gegevensframe

Gebruik nu het volgende script om de resultaten van de query te retourneren naar een R-gegevensframe met de functie **rxSqlServerData**.
Een onderdeel van het proces is het definiëren van het type voor de geselecteerde kolommen (met colClasses) om ervoor te zorgen dat de typen correct worden overgebracht naar R.

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

U ziet als het goed is resultaten die vergelijkbaar zijn met het volgende.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verdergaat met deze zelfstudie***, kunt u de database tpcxbb_1gb verwijderen van uw server.

Volg deze stappen vanuit Azure Portal:

1. Selecteer in het linkermenu in de Microsoft Azure-portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** **tpcxbb_1gb** in en selecteer uw abonnement.
1. Selecteer de database **tpcxbb_1gb**.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze reeks zelfstudies hebt u deze stappen voltooid:

* Een voorbeelddatabase importeren in een database in Azure SQL Database met R
* Klanten differentiëren op verschillende aspecten met R
* De gegevens laden van een database naar een R-gegevensframe

Volg deel twee van deze reeks zelfstudies om een machine learning-model te maken dat deze klantgegevens gebruikt:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)](clustering-model-build-tutorial.md)
