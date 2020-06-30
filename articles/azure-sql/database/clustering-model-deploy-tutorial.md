---
title: 'Zelfstudie: Een clustermodel implementeren in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel drie van deze driedelige serie zelfstudies implementeert u met Azure SQL Database Machine Learning Services (preview) een clustermodel in R.
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
ms.openlocfilehash: 2a90003c9de2cf0e942b518c6d4e9c3569039caf
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251417"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een clustermodel in R implementeren met Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel drie van deze driedelige serie zelfstudies implementeert u een in R ontwikkeld clustermodel in een database in Azure SQL Database met behulp van Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

U maakt een opgeslagen procedure met een ingesloten R-script dat clustering uitvoert. Omdat uw model wordt uitgevoerd in de Azure SQL Database, kan het eenvoudig worden getraind op basis van gegevens die in de database zijn opgeslagen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een opgeslagen procedure maken waarmee het model wordt gegenereerd
> * Clustering uitvoeren in SQL Database
> * De clusterinformatie gebruiken

In [deel één](clustering-model-prepare-data-tutorial.md) hebt u geleerd hoe u de gegevens van een database voorbereidt om clustering uit te voeren.

In [deel twee](clustering-model-build-tutorial.md)hebt u geleerd hoe u een K-means-clustermodel in R maakt en traint.

## <a name="prerequisites"></a>Vereisten

* In deel drie van deze zelfstudie wordt ervan uitgegaan dat u [**deel één**](clustering-model-prepare-data-tutorial.md) en [**deel twee**](clustering-model-build-tutorial.md) hebt voltooid.

## <a name="create-a-stored-procedure-that-generates-the-model"></a>Een opgeslagen procedure maken waarmee het model wordt gegenereerd

Voer het volgende T-SQL-script uit om de opgeslagen procedure te maken. De procedure reconstrueert de stappen die u hebt ontwikkeld in deel één en twee van deze serie zelfstudies:

* klanten classificeren op basis van hun aankoop-en retourgeschiedenis
* vier clusters met klanten genereren met behulp van een K-Means-algoritme

De procedure slaat de resulterende clustertoewijzingen van klanten op in de databasetabel **customer_return_clusters**.

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>Clustering uitvoeren in SQL Database

Nu u de opgeslagen procedure hebt gemaakt, voert u het volgende script uit om clustering te verrichten.

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

Controleer of het werkt en of we de lijst met klanten en hun clustertoewijzingen daadwerkelijk hebben.

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>De clusterinformatie gebruiken

Omdat u de clusterprocedure in de database hebt opgeslagen, kan deze efficiënt clusteren op basis van klantgegevens die in dezelfde database zijn opgeslagen. U kunt de procedure uitvoeren wanneer uw klantgegevens worden bijgewerkt en de bijgewerkte clusterinformatie gebruiken.

Stel dat u een commerciële e-mail wilt sturen naar klanten in cluster 3, ofwel de groep die een actiever retourgedrag laat zien (in [deel twee](clustering-model-build-tutorial.md#analyze-the-results) kunt u zien hoe de vier clusters zijn omschreven). Met de volgende code worden de e-mailadressen van klanten in cluster 3 geselecteerd.

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

U kunt de waarde **r.cluster** wijzigen om e-mailadressen van klanten in andere clusters geretourneerd te krijgen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie kunt u de tpcxbb_1gb-database van uw server verwijderen.

Volg deze stappen vanuit de Microsoft Azure-portal:

1. Selecteer in het linkermenu in de Microsoft Azure-portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** **tpcxbb_1gb** in en selecteer uw abonnement.
1. Selecteer de database **tpcxbb_1gb**.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel drie van deze reeks zelfstudies hebt u de volgende stappen voltooid:

* Een opgeslagen procedure maken waarmee het model wordt gegenereerd
* Clustering uitvoeren in SQL Database
* De clusterinformatie gebruiken

Voor meer informatie over het gebruik van R in Azure SQL Database Machine Learning Services (preview):

* [Zelfstudie: gegevens voorbereiden om een ​​voorspellend model in R te trainen met Azure SQL Database Machine Learning Services (preview)](predictive-model-prepare-data-tutorial.md)
* [Geavanceerde R-functies schrijven in Azure SQL Database met behulp van Machine Learning Services (preview)](machine-learning-services-functions.md)
* [Werken met R en SQL-gegevens in Azure SQL Database Machine Learning Services (preview)](machine-learning-services-data-issues.md)
* [Een R-pakket toevoegen aan Azure SQL Database Machine Learning Services (preview)](machine-learning-services-add-r-packages.md)
