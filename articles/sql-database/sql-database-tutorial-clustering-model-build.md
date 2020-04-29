---
title: 'Zelf studie: een cluster model bouwen in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze reeks met drie zelf studies bouwt u een model van K-it voor het uitvoeren van clusters in R met Azure SQL Database Machine Learning Services (preview).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453094"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelf studie: een cluster model bouwen in R met Azure SQL Database Machine Learning Services (preview)

In deel twee van deze reeks met drie zelf studies bouwt u een model van K-it in R om clustering uit te voeren. In het volgende deel van deze reeks implementeert u dit model in een SQL database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Definieer het aantal clusters voor een K-betekent-algoritme
> * Clustering uitvoeren
> * De resultaten analyseren

In [deel één](sql-database-tutorial-clustering-model-prepare-data.md)hebt u geleerd hoe u de gegevens van een Azure-SQL database voorbereidt voor het uitvoeren van een cluster.

In [deel drie](sql-database-tutorial-clustering-model-deploy.md)leert u hoe u een opgeslagen procedure maakt in een Azure-SQL database die clustering in R kan uitvoeren op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* In deel twee van deze zelf studie wordt ervan uitgegaan dat u [**deel één**](sql-database-tutorial-clustering-model-prepare-data.md) en de bijbehorende vereisten hebt voltooid.

## <a name="define-the-number-of-clusters"></a>Het aantal clusters definiëren

Als u uw klant gegevens wilt clusteren, gebruikt u het cluster algoritme **K-betekent** , een van de eenvoudigste en meest bekende manieren om gegevens te groeperen.
Meer informatie over K: in [een volledige gids vindt u k-means clustering algoritme](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html).

Het algoritme accepteert twee invoer: de gegevens zelf en een vooraf gedefinieerd nummer "*k*" vertegenwoordigen het aantal clusters dat moet worden gegenereerd.
De uitvoer is een *k* -cluster met de invoer gegevens die zijn gepartitioneerd tussen de clusters.

Om het aantal clusters te bepalen voor het algoritme dat moet worden gebruikt, gebruikt u een plot van de som van de kwadraten van de groepen, op basis van het aantal uitgepakte clusters. Het juiste aantal clusters dat moet worden gebruikt, bevindt zich op de bocht of ' Elle ' hoek van het plot.

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![Grafiek, gebogen](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

Op basis van de grafiek ziet het eruit als *k = 4* een goede waarde om te proberen. Met de waarde *k* worden de klanten gegroepeerd in vier clusters.

## <a name="perform-clustering"></a>Clustering uitvoeren

In het volgende R-script gebruikt u de functie **rxKmeans**, die de functie K-houdt in het RevoScaleR-pakket is.

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

Nu u klaar bent met het clusteren met behulp van K, is de volgende stap het resultaat te analyseren en te zien of u informatie kunt vinden die kan worden gebruikt.

Het **clust** -object bevat de resultaten van de K: Clustering.

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

De vier clusters worden aangegeven met behulp van de variabelen die zijn gedefinieerd in [deel één](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers):

* *orderRatio* = verhouding van retour orders (het totale aantal orders is gedeeltelijk of volledig geretourneerd versus het totale aantal orders)
* *itemsRatio* = geretourneerde item verhouding (het totale aantal geretourneerde items versus het aantal gekochte items)
* *monetaryRatio* = verhouding retour bedrag (totale monetaire hoeveelheid items die wordt geretourneerd versus het aangeschafte bedrag)
* *frequentie* = retour frequentie

Gegevens analyse met behulp van K: vereist vaak verdere analyse van de resultaten en verdere stappen voor een beter inzicht in elk cluster, maar het kan ook een goede lead bieden.
Hier volgen enkele manieren waarop u deze resultaten kunt interpreteren:

* Cluster 1 (het grootste cluster) lijkt een groep klanten te zijn die niet actief zijn (alle waarden zijn nul).
* Cluster 3 lijkt een groep te zijn die in termen van retour gedrag opvalt.

## <a name="clean-up-resources"></a>Resources opschonen

***Als u niet verder gaat met deze zelf studie***, verwijdert u de tpcxbb_1gb-data base van de Azure SQL database-server.

Voer de volgende stappen uit op de Azure Portal:

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** of **SQL-data bases**.
1. In het veld **filteren op naam...** voert u **tpcxbb_1gb**in en selecteert u uw abonnement.
1. Selecteer uw **tpcxbb_1gb** -data base.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze reeks zelf studies hebt u de volgende stappen uitgevoerd:

* Definieer het aantal clusters voor een K-betekent-algoritme
* Clustering uitvoeren
* De resultaten analyseren

Voor het implementeren van het machine learning model dat u hebt gemaakt, volgt u deel drie van deze reeks zelf studies:

> [!div class="nextstepaction"]
> [Zelf studie: een cluster model implementeren in R met Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-clustering-model-deploy.md)