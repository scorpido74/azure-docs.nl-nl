---
title: 'Zelf studie: gegevens voorbereiden voor het uitvoeren van clusters in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel één van deze reeks met drie zelf studies gaat u de gegevens voorbereiden van een Azure-SQL database voor het uitvoeren van clusters in R met Azure SQL Database Machine Learning Services (preview).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452873"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelf studie: gegevens voorbereiden voor het uitvoeren van clusters in R met Azure SQL Database Machine Learning Services (preview-versie)

In deel één van deze reeks met drie delen zelf studies importeert u de gegevens uit een Azure-SQL database met R en bereidt u deze voor. Verderop in deze reeks gebruikt u deze gegevens om een cluster model in R te trainen en te implementeren met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

*Clustering* kan worden uitgelegd als het organiseren van gegevens in groepen waar leden van een groep op een of andere manier op elkaar lijken.
U gebruikt het **-algoritme K-geeft** om het clusteren van klanten uit te voeren in een gegevensset van product aankopen en retourneert. Door klanten te clusteren, kunt u uw marketing inspanningen effectiever richten door specifieke groepen te richten.
K: clusteren is een algoritme voor het leren van een niet- *Super visie* waarmee naar patronen in gegevens wordt gezocht op basis van overeenkomsten.

In delen één en twee van deze reeksen ontwikkelt u enkele R-scripts in RStudio om uw gegevens voor te bereiden en een machine learning model te trainen. In deel drie voert u de R-scripts in een SQL database uit met behulp van opgeslagen procedures.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een voorbeeld database importeren in een Azure-SQL database
> * Afzonderlijke klanten langs verschillende dimensies met R
> * De gegevens van de Azure-SQL database in een R-gegevens frame laden

In [deel twee](sql-database-tutorial-clustering-model-build.md)leert u hoe u een K-cluster model maakt en traint in R.

In [deel drie](sql-database-tutorial-clustering-model-deploy.md)leert u hoe u een opgeslagen procedure maakt in een Azure-SQL database die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u nog geen abonnement op Azure hebt, moet u [een account maken](https://azure.microsoft.com/free/) voordat u begint.

* [Azure SQL database met Machine Learning Services (met R)](sql-database-machine-learning-services-overview.md) ingeschakeld.

* RevoScaleR-pakket: Zie [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) voor opties om dit pakket lokaal te installeren.

* R IDE: in deze zelf studie wordt [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)gebruikt.

* Hulp programma voor SQL-query's-in deze zelf studie wordt ervan uitgegaan dat u [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) of [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>De voorbeeld database importeren

De voor beeld-gegevensset die in deze zelf studie wordt gebruikt, is opgeslagen in een **. Bacpac** -back-upbestand dat u kunt downloaden en gebruiken. Deze gegevensset is afgeleid van de [tpcx-BB-](http://www.tpc.org/tpcx-bb/default.asp) gegevensset die is verschaft door de [Trans Action processing pregent (TPC)](http://www.tpc.org/default.asp).

1. Down load het bestand [tpcxbb_1gb. Bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac).

1. Volg de instructies in [een BACPAC-bestand importeren om een Azure SQL database te maken](https://docs.microsoft.com/azure/sql-database/sql-database-import)met behulp van de volgende gegevens:

   * Importeren uit het **tpcxbb_1gb. Bacpac** -bestand dat u hebt gedownload
   * Kies tijdens de open bare Preview de configuratie van de **GEN5-vCore** voor de nieuwe data base
   * Geef de nieuwe data base de naam tpcxbb_1gb

## <a name="separate-customers"></a>Afzonderlijke klanten

Maak een nieuw RScript-bestand in RStudio en voer het volgende script uit.
In de SQL-query scheidt u klanten langs de volgende dimensies:

* **orderRatio** = verhouding van retour orders (het totale aantal orders is gedeeltelijk of volledig geretourneerd versus het totale aantal orders)
* **itemsRatio** = geretourneerde item verhouding (het totale aantal geretourneerde items versus het aantal gekochte items)
* **monetaryRatio** = verhouding retour bedrag (totale monetaire hoeveelheid items die wordt geretourneerd versus het aangeschafte bedrag)
* **frequentie** = retour frequentie

Vervang in de functie **Paste** **Server**, **UID**en **pwd** door uw eigen verbindings gegevens.

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

## <a name="load-the-data-into-a-data-frame"></a>De gegevens in een gegevens frame laden

Gebruik nu het volgende script om de resultaten van de query te retour neren naar een R-gegevens frame met behulp van de functie **rxSqlServerData** .
Als onderdeel van het proces definieert u het type voor de geselecteerde kolommen (met behulp van colClasses) om ervoor te zorgen dat de typen correct worden overgebracht naar R.

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

De resultaten moeten er ongeveer als volgt uitzien.

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verder gaat met deze zelf studie***, verwijdert u de tpcxbb_1gb-data base van de Azure SQL database-server.

Voer de volgende stappen uit op de Azure Portal:

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** of **SQL-data bases**.
1. In het veld **filteren op naam...** voert u **tpcxbb_1gb**in en selecteert u uw abonnement.
1. Selecteer uw **tpcxbb_1gb** -data base.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel één van deze reeks zelf studies hebt u de volgende stappen uitgevoerd:

* Een voorbeeld database importeren in een Azure-SQL database
* Afzonderlijke klanten langs verschillende dimensies met R
* De gegevens van de Azure-SQL database in een R-gegevens frame laden

Als u een machine learning model wilt maken dat gebruikmaakt van deze klant gegevens, volgt u deel twee van deze reeks zelf studies:

> [!div class="nextstepaction"]
> [Zelf studie: een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-build.md)
