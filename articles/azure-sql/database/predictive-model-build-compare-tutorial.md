---
title: 'Zelfstudie: Voorspellende modellen in R trainen en vergelijken'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In deel twee van deze driedelige zelfstudie maakt u twee voorspellende modellen in R met Azure SQL Database Machine Learning Services (preview) en selecteert u het meest accurate model.
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
ms.openlocfilehash: 4a5936501f45694febe284d9747bdefdfeae6aeb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84023985"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Zelfstudie: Een voorspellend model maken in R met Azure SQL Database Machine Learning Services (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deel twee van deze driedelige zelfstudie maakt u twee voorspellende modellen in R en selecteert u het meest accurate model. In het volgende deel implementeert u dit model in een SQL-database met Azure SQL Database Machine Learning Services (preview).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Twee machine learning-modellen trainen
> * Voorspellingen maken vanuit beide modellen
> * De resultaten vergelijken om het meest accurate model te kunnen kiezen

In [deel een](predictive-model-prepare-data-tutorial.md) hebt u geleerd hoe u een voorbeelddatabase importeert en vervolgens de gegevens voorbereidt om te worden gebruikt voor het trainen van een voorspellend model in R.

In [deel drie](predictive-model-deploy-tutorial.md) leert u hoe u het model opslaat in een database en vervolgens opgeslagen procedures maakt vanuit de R-scripts die u hebt ontwikkeld in delen een en twee. De opgeslagen procedures worden in een SQL-database uitgevoerd om voorspellingen te doen op basis van nieuwe gegevens.

## <a name="prerequisites"></a>Vereisten

* In deel twee van deze zelfstudie wordt ervan uitgegaan dat u [**deel een**](predictive-model-prepare-data-tutorial.md) hebt voltooid en aan en de bijbehorende vereisten hebt voldaan.

## <a name="train-two-models"></a>Twee modellen trainen

Maak twee verschillende modellen (lineaire regressie en beslissingsstructuur) en kijk welk model het meest accuraat voorspelt om het beste model te vinden voor de skiverhuurgegevens. U gebruikt het gegevensframe `rentaldata` dat u hebt gemaakt in deel een.

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

## <a name="make-predictions-from-both-models"></a>Voorspellingen maken vanuit beide modellen

Gebruik een voorspellingsfunctie om het aantal verhuren te voorspellen met elk getraind model.

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

## <a name="compare-the-results"></a>Resultaten vergelijken

Nu wilt u zien welk model de beste voorspelling geeft. Een snelle en eenvoudige manier om dit te doen is een diagramfunctie te gebruiken om het verschil te zien tussen de werkelijke waarden in uw trainingsgegevens en de voorspelde waarden.

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![De twee modellen vergelijken](./media/predictive-model-build-compare-tutorial/compare-models.png)

Het lijkt erop dat het beslissingsstructuurmodel het meest accuraat is.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet verdergaat met deze zelfstudie, kunt u de database TutorialDB verwijderen van uw server.

Volg deze stappen vanuit Azure Portal:

1. Selecteer in het linkermenu in Azure Portal **Alle resources** of **SQL-databases**.
1. Voer in het veld **Filteren op naam...** **TutorialDB** in en selecteer uw abonnement.
1. Selecteer uw TutorialDB-database.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze reeks zelfstudies hebt u deze stappen voltooid:

* Twee Machine Learning-modellen trainen
* Voorspellingen maken vanuit beide modellen
* De resultaten vergelijken om het meest accurate model te kunnen kiezen

Volg deel drie van deze zelfstudiereeks om het machine learning-model te implementeren:

> [!div class="nextstepaction"]
> [Zelfstudie: Een voorspellend model implementeren in R met Azure SQL Database Machine Learning Services (preview)](predictive-model-deploy-tutorial.md)
