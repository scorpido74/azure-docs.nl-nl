---
title: 'Zelfstudie: Een clustering-model in R bouwen'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze driedelige zelfstudie bouwt u een K-Means-model om clustering in R uit te voeren met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: a7d0bc787f1ae4444849a1483d6323f8a098ae1e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84024128"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een clustering-model maken in R met Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel twee van deze driedelige zelfstudie bouwt u een K-Means-model in R om clustering uit te voeren. In het volgende deel implementeert u dit model in een SQL-database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Het aantal clusters definiëren voor een K-Means-algoritme
> * Clustering uitvoeren
> * Resultaten analyseren

In [deel een](clustering-model-prepare-data-tutorial.md) hebt u geleerd hoe u de gegevens van een Azure SQL-database voorbereidt om clustering uit te voeren.

In [deel drie](clustering-model-deploy-tutorial.md) leert u hoe u een opgeslagen procedure maakt in een Azure SQL-database die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* In deel twee van deze zelfstudie wordt ervan uitgegaan dat u [**deel een**](clustering-model-prepare-data-tutorial.md) hebt voltooid en aan de bijbehorende vereisten hebt voldaan.

## <a name="define-the-number-of-clusters"></a>Het aantal clusters definiëren

Om uw klantgegevens te clusteren, gebruikt u het **K-Means**-clusteringalgoritme. Dit is een van de eenvoudigste en best bekende manieren om gegevens te groeperen.
U kunt in [Een volledige gids voor het K-means clusteringalgoritme](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html) meer informatie vinden over K-Means.

In het algoritme worden twee invoeren geaccepteerd: De gegevens zelf en een vooraf vastgesteld aantal *'k'* , het aantal clusters dat moet worden gegenereerd.
De uitvoer is *k* clusters met de invoergegevens gepartitioneerd in de clusters.

Maak een grafiek van de som van de kwadraten van de groepen op basis van het aantal uitgepakte clusters om het aantal clusters dat het algoritme gebruikt, vast te stellen. Het juiste aantal clusters dat wordt gebruikt is in de bocht of 'elleboog' van de grafiek.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted.
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Ellebooggrafiek](./media/clustering-model-build-tutorial/elbow-graph.png)

Op basis van de grafiek lijk het erop dat *k=4* een goede waarde is om te proberen. Die *k*-waarde groepeert de klanten in vier clusters.

## <a name="perform-clustering"></a>Clustering uitvoeren

In het volgende R-script gebruikt u de functie **rxKmeans**, de K-Means-functie in het RevoScaleR-pakket.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in a database in SQL Database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>Resultaten analyseren

Nu u de clustering hebt uitgevoerd met K-Means, is de volgende stap het analyseren van het resultaat en zien of u praktische informatie kunt vinden.

Het object **clust** bevat de resultaten van de K-Means-clustering.

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio +
    frequency, data = customer_returns, outFile = return_cluster,
    outColName = "cluster", extraVarsToWrite = c("customer"),
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

De gemiddelden van de vier clusters worden aangegeven met de variabelen die zijn gedefinieerd in [deel een](clustering-model-prepare-data-tutorial.md#separate-customers):

* *orderRatio* = verhouding van retourbestellingen (totaal aantal bestellingen gedeeltelijk of geheel geretourneerd ten opzichte van het totaal aantal bestellingen)
* *itemsRatio* = verhouding van retourartikelen (totaal aantal geretourneerde artikelen ten opzichte van het aantal gekochte artikelen)
* *monetaryRatio* = verhouding geretourneerd bedrag (totaalbedrag van geretourneerde artikelen ten opzichte van het aankoopbedrag)
* *frequency* = retourfrequentie

Datamining met K-Means vereist vaak verdere analyse van de resultaten en volgende stappen om elk cluster beter te begrijpen, maar het kan goede verkoopkansen bieden.
Hier zijn een paar manieren waarop u deze resultaten zou kunnen interpreteren:

* Cluster 1 (het grootste cluster) lijkt uit een groep klanten te bestaan die niet actief zijn (alle waarden zijn nul).
* Cluster 3 lijkt een groep te zijn die opvalt door het retourgedrag.

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verdergaat met deze zelfstudie***, kunt u de database tpcxbb_1gb verwijderen van uw server.

Volg deze stappen vanuit Azure Portal:

1. Selecteer in het linkermenu in de Azure-portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** , **tpcxbb_1gb** in en selecteer uw abonnement.
1. Selecteer de database **tpcxbb_1gb**.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze reeks zelfstudies hebt u deze stappen voltooid:

* Het aantal clusters definiëren voor een K-Means-algoritme
* Clustering uitvoeren
* Resultaten analyseren

Volg deel drie van deze zelfstudiereeks om het machine learning-model te implementeren:

> [!div class="nextstepaction"]
> [Zelfstudie: Een clustering-model in R implementeren met Azure SQL Database Machine Learning Services (preview)](clustering-model-deploy-tutorial.md)
