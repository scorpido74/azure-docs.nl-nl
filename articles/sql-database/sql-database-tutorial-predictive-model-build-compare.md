---
title: 'Zelf studie: voorspellende modellen trainen en vergelijken in R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze reeks met drie zelf studies maakt u twee voorspellende modellen in R met Azure SQL Database Machine Learning Services (preview) en selecteert u vervolgens het meest nauw keurige model.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452924"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelf studie: een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)

In deel twee van deze reeks met drie zelf studies maakt u twee voorspellende modellen in R en selecteert u het meest nauw keurige model. In het volgende deel van deze reeks implementeert u dit model in een SQL database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Twee machine learning modellen trainen
> * Voor spellingen van beide modellen maken
> * De resultaten vergelijken om het meest nauw keurige model te kiezen

In [deel één](sql-database-tutorial-predictive-model-prepare-data.md)hebt u geleerd hoe u een voorbeeld database importeert en vervolgens de gegevens voorbereidt die moeten worden gebruikt voor het trainen van een voorspellend model in R.

In [deel drie](sql-database-tutorial-predictive-model-deploy.md)leert u hoe u het model opslaat in een Data Base en vervolgens opgeslagen procedures maakt vanuit de R-scripts die u hebt ontwikkeld in delen één en twee. De opgeslagen procedures worden in een SQL database uitgevoerd om voor spellingen te maken op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* In deel twee van deze zelf studie wordt ervan uitgegaan dat u [**deel één**](sql-database-tutorial-predictive-model-prepare-data.md) en de bijbehorende vereisten hebt voltooid.

## <a name="train-two-models"></a>Twee modellen trainen

Als u het beste model voor de Ski-Huur gegevens wilt vinden, maakt u twee verschillende modellen (lineaire regressie en beslissings structuur) en ziet u hoe nauw keuriger een voor spelling wordt gemaakt. U gebruikt het gegevens frame `rentaldata` dat u in deel één van deze reeks hebt gemaakt.

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

## <a name="make-predictions-from-both-models"></a>Voor spellingen van beide modellen maken

Gebruik een voor spel functie om het huur aantal te voors pellen met behulp van elk getraind model.

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

## <a name="compare-the-results"></a>De resultaten vergelijken

Nu wilt u zien welke modellen de beste voor spellingen bieden. Een snelle en eenvoudige manier om dit te doen, is door een eenvoudige uitzet functie te gebruiken om het verschil tussen de werkelijke waarden in uw trainings gegevens en de voorspelde waarden weer te geven.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![De twee modellen vergelijken](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

Het lijkt alsof het model van de beslissings structuur de nauw keurigste van de twee modellen is.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verder gaat met deze zelf studie, verwijdert u de Tutorialdb maakt-data base van uw Azure SQL Database-Server.

Voer de volgende stappen uit op de Azure Portal:

1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** of **SQL-data bases**.
1. Typ **tutorialdb maakt**in het veld **filteren op naam...** en selecteer uw abonnement.
1. Selecteer uw Tutorialdb maakt-data base.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze reeks zelf studies hebt u de volgende stappen uitgevoerd:

* Twee machine learning modellen trainen
* Voor spellingen van beide modellen maken
* De resultaten vergelijken om het meest nauw keurige model te kiezen

Voor het implementeren van het machine learning model dat u hebt gemaakt, volgt u deel drie van deze reeks zelf studies:

> [!div class="nextstepaction"]
> [Zelf studie: een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-predictive-model-deploy.md)
