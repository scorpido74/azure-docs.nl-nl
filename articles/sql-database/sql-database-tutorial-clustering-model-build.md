---
title: 'Zelfstudie: Een clustermodel bouwen in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze driedelige zelfstudiereeks bouwt u een K-Means-model om clustering uit te voeren in R met Azure SQL Database Machine Learning Services (preview).
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
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453094"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een clustermodel bouwen in R met Azure SQL Database Machine Learning Services (voorbeeld)

In deel twee van deze driedelige zelfstudiereeks bouw je een K-Means-model in R om clustering uit te voeren. In het volgende deel van deze reeks implementeert u dit model in een SQL-database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

In dit artikel leer je hoe je:

> [!div class="checklist"]
> * Het aantal clusters voor een K-Means-algoritme definiëren
> * Clustering uitvoeren
> * De resultaten analyseren

In [deel één](sql-database-tutorial-clustering-model-prepare-data.md)hebt u geleerd hoe u de gegevens uit een Azure SQL-database voorbereiden om clustering uit te voeren.

In [deel drie](sql-database-tutorial-clustering-model-deploy.md)leert u hoe u een opgeslagen procedure maakt in een Azure SQL-database die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Deel twee van deze tutorial gaat ervan uit dat je [**deel een**](sql-database-tutorial-clustering-model-prepare-data.md) en de voorwaarden hebt voltooid.

## <a name="define-the-number-of-clusters"></a>Het aantal clusters definiëren

Als u uw klantgegevens wilt clusteren, gebruikt u het clusteralgoritme **K-Means,** een van de eenvoudigste en meest bekende manieren om gegevens te groeperen.
U meer lezen over K-Means in [Een complete gids voor K-means clustering algoritme](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Het algoritme accepteert twee ingangen: de gegevens zelf en een vooraf gedefinieerd getal "*k*" dat het aantal te genereren clusters weergeeft.
De uitvoer is *k-clusters* met de invoergegevens verdeeld over de clusters.

Als u het aantal clusters wilt bepalen dat het algoritme moet gebruiken, gebruikt u een plot van de groepbinnen de som van vierkanten, op aantal geëxtraheerde clusters. Het juiste aantal te gebruiken clusters is bij de bocht of "elleboog" van het perceel.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![De grafiek van de elleboog](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Op basis van de grafiek, het lijkt erop dat *k = 4* zou een goede waarde om te proberen. Die *k-waarde* zal de klanten groeperen in vier clusters.

## <a name="perform-clustering"></a>Clustering uitvoeren

In het volgende R-script gebruikt u de functie **rxKmeans**, de K-Means-functie in het RevoScaleR-pakket.

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
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

## <a name="analyze-the-results"></a>De resultaten analyseren

Nu u de clustering met K-Means hebt gedaan, is de volgende stap het analyseren van het resultaat en kijk of u bruikbare informatie vinden.

Het **clust-object** bevat de resultaten van de K-Means clustering.

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

De vier clustermiddelen worden gegeven aan de hand van de in [deel 1](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)omschreven variabelen:

* *orderRatio* = return order ratio (totaal aantal orders gedeeltelijk of volledig geretourneerd ten opzichte van het totale aantal orders)
* *itemsRatio* = verhouding retourobject (totaal aantal geretourneerde objecten versus het aantal gekochte objecten)
* *monetaryRatio* = rendementsratio (totaal bedrag van de geretourneerde posten versus het gekochte bedrag)
* *frequentie* = retourfrequentie

Datamining met Behulp van K-Means vereist vaak verdere analyse van de resultaten, en verdere stappen om elk cluster beter te begrijpen, maar het kan een aantal goede leads bieden.
Hier zijn een paar manieren waarop u deze resultaten zou kunnen interpreteren:

* Cluster 1 (het grootste cluster) lijkt een groep klanten te zijn die niet actief zijn (alle waarden zijn nul).
* Cluster 3 lijkt een groep te zijn die opvalt in termen van retourgedrag.

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verder gaat met deze zelfstudie,*** verwijdert u de tpcxbb_1gb-database van uw Azure SQL Database-server.

Voer de volgende stappen uit vanuit de Azure-portal:

1. Selecteer alle **bronnen** of **SQL-databases**in het linkermenu in de Azure-portal.
1. Voer in het veld **Filteren op naam...** **tpcxbb_1gb**in en selecteer uw abonnement.
1. Selecteer uw **tpcxbb_1gb** database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze zelfstudiereeks hebt u de volgende stappen voltooid:

* Het aantal clusters voor een K-Means-algoritme definiëren
* Clustering uitvoeren
* De resultaten analyseren

Volg deel drie van deze zelfstudiereeks om het machine learning-model te implementeren dat u hebt gemaakt:

> [!div class="nextstepaction"]
> [Zelfstudie: Een clusteringmodel implementeren in R met Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-tutorial-clustering-model-deploy.md)