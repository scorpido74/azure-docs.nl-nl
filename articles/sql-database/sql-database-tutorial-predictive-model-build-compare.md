---
title: 'Zelfstudie: Voorspellende modellen trainen en vergelijken in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze driedelige zelfstudiereeks maakt u twee voorspellende modellen in R met Azure SQL Database Machine Learning Services (voorbeeld) en selecteert u vervolgens het meest nauwkeurige model.
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
ms.openlocfilehash: 0985b37280e3cd363ba1728a5ec33b0012611ab2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452924"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (voorbeeld)

In deel twee van deze driedelige zelfstudiereeks maak je twee voorspellende modellen in R en selecteer je het meest nauwkeurige model. In het volgende deel van deze reeks implementeert u dit model in een SQL-database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

In dit artikel leer je hoe je:

> [!div class="checklist"]
> * Train twee machine learning-modellen
> * Voorspellingen doen van beide modellen
> * Vergelijk de resultaten om het meest nauwkeurige model te kiezen

In [deel één](sql-database-tutorial-predictive-model-prepare-data.md)hebt u geleerd hoe u een voorbeelddatabase importeren en vervolgens de gegevens voorbereiden die moeten worden gebruikt voor het trainen van een voorspellend model in R.

In [deel drie](sql-database-tutorial-predictive-model-deploy.md)leert u hoe u het model in een database opslaan en vervolgens opgeslagen procedures maakt uit de R-scripts die u in deel één en twee hebt ontwikkeld. De opgeslagen procedures worden uitgevoerd in een SQL-database om voorspellingen te doen op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* Deel twee van deze tutorial gaat ervan uit dat je [**deel een**](sql-database-tutorial-predictive-model-prepare-data.md) en de voorwaarden hebt voltooid.

## <a name="train-two-models"></a>Train twee modellen

Om het beste model voor de skiverhuurgegevens te vinden, maakt u twee verschillende modellen (lineaire regressie en beslissingsstructuur) en ziet u welke nauwkeuriger voorspelt. U gebruikt het gegevensframe `rentaldata` dat u in deel één van deze reeks hebt gemaakt.

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>Voorspellingen doen van beide modellen

Gebruik een voorspellingsfunctie om de huurtellingen te voorspellen met behulp van elk getraind model.

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>Vergelijk de resultaten

Nu wilt u zien welke van de modellen geeft de beste voorspellingen. Een snelle en eenvoudige manier om dit te doen is het gebruik van een basisplottfunctie om het verschil tussen de werkelijke waarden in uw trainingsgegevens en de voorspelde waarden te bekijken.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![De twee modellen vergelijken](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Het lijkt erop dat de beslissing boom model is de nauwkeuriger van de twee modellen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verder gaat met deze zelfstudie, verwijdert u de TutorialDB-database van uw Azure SQL Database-server.

Voer de volgende stappen uit vanuit de Azure-portal:

1. Selecteer alle **bronnen** of **SQL-databases**in het linkermenu in de Azure-portal.
1. Typ **TutorialDB**en selecteer uw abonnement in het veld **Filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze zelfstudiereeks hebt u de volgende stappen voltooid:

* Train twee machine learning-modellen
* Voorspellingen doen van beide modellen
* Vergelijk de resultaten om het meest nauwkeurige model te kiezen

Volg deel drie van deze zelfstudiereeks om het machine learning-model te implementeren dat u hebt gemaakt:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (voorbeeld)](sql-database-tutorial-predictive-model-deploy.md)
