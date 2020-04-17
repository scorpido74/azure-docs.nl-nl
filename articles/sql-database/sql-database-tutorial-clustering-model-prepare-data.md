---
title: 'Zelfstudie: gegevens voorbereiden om clustering uit te voeren in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel één van deze driedelige zelfstudiereeks bereidt u de gegevens uit een Azure SQL-database voor om clustering uit te voeren in R met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: abe7d5ed1d4ba1308abde04aee32a3ea222456b8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452873"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Gegevens voorbereiden om clustering uit te voeren in R met Azure SQL Database Machine Learning Services (voorbeeld)

In deel één van deze driedelige zelfstudiereeks importeert en bereidt u de gegevens uit een Azure SQL-database met R. Later in deze reeks gebruikt u deze gegevens om een clusteringmodel in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (voorbeeld).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Clustering* kan worden uitgelegd als het organiseren van gegevens in groepen waar leden van een groep op de een of andere manier vergelijkbaar zijn.
U gebruikt het **K-Means-algoritme** om de clustering van klanten uit te voeren in een gegevensset van productaankopen en retourzendingen. Door klanten te clusteren, u uw marketinginspanningen effectiever richten door specifieke groepen te targeten.
K-Means clustering is een *onbewaakt leeralgoritme* dat zoekt naar patronen in gegevens op basis van overeenkomsten.

In deel één en twee van deze serie ontwikkel je een aantal R-scripts in RStudio om je gegevens voor te bereiden en een machine learning-model te trainen. Vervolgens, in deel drie, voer je die R-scripts uit in een SQL-database met behulp van opgeslagen procedures.

In dit artikel leer je hoe je:

> [!div class="checklist"]
> * Een voorbeelddatabase importeren in een Azure SQL-database
> * Afzonderlijke klanten langs verschillende dimensies met Behulp van R
> * De gegevens uit de Azure SQL-database in een R-gegevensframe laden

In [deel twee](sql-database-tutorial-clustering-model-build.md)leer je hoe je een K-Means clustering model maakt en traint in R.

In [deel drie](sql-database-tutorial-clustering-model-deploy.md)leert u hoe u een opgeslagen procedure maakt in een Azure SQL-database die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/) voordat u begint.

* [Azure SQL Database met Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md) ingeschakeld.

* RevoScaleR-pakket - Zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE - Deze zelfstudie maakt gebruik van [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* SQL-querytool - Deze zelfstudie gaat ervan uit dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeelddatabase importeren

De voorbeeldgegevensset die in deze zelfstudie wordt gebruikt, is opgeslagen in een .bacpac-databaseback-upbestand dat u downloaden en gebruiken. **.bacpac** Deze gegevensset is afgeleid van de [tpcx-bb-gegevensset](http://www.tpc.org/tpcx-bb/default.asp) van de [Transaction Processing Performance Council (TPC).](http://www.tpc.org/default.asp)

1. Download het bestand [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Volg de aanwijzingen in [Een BACPAC-bestand importeren om een Azure SQL-database te maken,](https://docs.microsoft.com/azure/sql-database/sql-database-import)met behulp van deze gegevens:

   * Importeren uit het **tpcxbb_1gb.bacpac-bestand** dat u hebt gedownload
   * Kies tijdens de openbare preview de **Gen5/vCore-configuratie** voor de nieuwe database
   * De nieuwe database "tpcxbb_1gb" een naam geven

## <a name="separate-customers"></a>Afzonderlijke klanten

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit.
In de SQL-query scheidt u klanten langs de volgende dimensies:

* **orderRatio** = return order ratio (totaal aantal orders gedeeltelijk of volledig geretourneerd ten opzichte van het totale aantal orders)
* **itemsRatio** = verhouding retourobject (totaal aantal geretourneerde objecten versus het aantal gekochte objecten)
* **monetaryRatio** = rendementsratio (totaal bedrag van de geretourneerde posten versus het gekochte bedrag)
* **frequentie** = retourfrequentie

Vervang **server,** **UID**en **PWD** in de **plakfunctie** door uw eigen verbindingsgegevens.

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
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

## <a name="load-the-data-into-a-data-frame"></a>De gegevens in een gegevensframe laden

Gebruik nu het volgende script om de resultaten van de query terug te sturen naar een R-gegevensframe met de **rxSqlServerData-functie.**
Als onderdeel van het proces definieert u het type voor de geselecteerde kolommen (met behulp van colClasses) om ervoor te zorgen dat de typen correct naar R worden overgebracht.

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

U ziet resultaten die vergelijkbaar zijn met het volgende.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verder gaat met deze zelfstudie,*** verwijdert u de tpcxbb_1gb-database van uw Azure SQL Database-server.

Voer de volgende stappen uit vanuit de Azure-portal:

1. Selecteer alle **bronnen** of **SQL-databases**in het linkermenu in de Azure-portal.
1. Voer in het veld **Filteren op naam...** **tpcxbb_1gb**in en selecteer uw abonnement.
1. Selecteer uw **tpcxbb_1gb** database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze zelfstudiereeks hebt u de volgende stappen voltooid:

* Een voorbeelddatabase importeren in een Azure SQL-database
* Afzonderlijke klanten langs verschillende dimensies met Behulp van R
* De gegevens uit de Azure SQL-database in een R-gegevensframe laden

Als u een machine learning-model wilt maken dat deze klantgegevens gebruikt, volgt u deel twee van deze zelfstudiereeks:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-tutorial-clustering-model-build.md)
