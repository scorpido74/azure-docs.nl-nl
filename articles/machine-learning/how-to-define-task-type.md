---
title: Een machine learning-taak definiëren voor een geautomatiseerde machine learning-run
titleSuffix: Azure Machine Learning
description: Meer informatie over het definiëren van een machine learning-taak voor een geautomatiseerde machine learning-run
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75540527"
---
# <a name="how-to-define-a-machine-learning-task"></a>Een machine learning-taak definiëren 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u de ondersteunde machine learning-taken en hoe u deze definiëren voor een geautomatiseerd e-experiment met machine learning (automated ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Wat is een machine learning-taak?

Een machine learning-taak vertegenwoordigt het type probleem dat wordt opgelost door een voorspellend model te maken. Geautomatiseerde ML ondersteunt drie verschillende soorten taken, waaronder classificatie, regressie en tijdreeksprognoses.

Taaktype| Beschrijving| Voorbeeld
----|----|----
Classificatie | Taak voor het voorspellen van de categorie van een bepaalde rij in een gegevensset. | Fraudedetectie op een creditcard. De doelkolom zou **Fraude gedetecteerd** met categorieën van *Waar* of *Onwaar*. In dit geval classificeren we elke rij in de gegevens als waar of onwaar.
Regressie | Taak voor het voorspellen van een continue hoeveelheidoutput. | Autokosten op basis van de kenmerken, zou de doelkolom **prijs**.
Prognose |Taak voor het maken van geïnformeerde schattingen bij het bepalen van de richting van toekomstige trends.| Voorspellen van de vraag naar energie voor hen de komende 48 uur. De doelkolom zou **de vraag** zijn en de voorspelde waarden zouden worden gebruikt om patronen in de energievraag weer te geven.

Automated ML ondersteunt de volgende algoritmen tijdens het automatiserings- en tuningproces. Als gebruiker hoeft u het algoritme niet op te geven.

Classificatie | Regressie | Tijdreeksvoorspelling
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Lichte GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Verloopboosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissingsboom](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Dichtstbijzijnde buren](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector Classificatie (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extreem gerandomiseerde bomen](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN Lineaire classificatie](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineaire regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastische gradiënt (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Het taaktype instellen
U het taaktype instellen voor uw geautomatiseerde ML-experimenten met de SDK of de Azure Machine Learning-studio.

Gebruik `task` de parameter `AutoMLConfig` in de constructor om het experimenttype op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

U de taak instellen als onderdeel van het maken van een geautomatiseerdML-experiment in de Azure Machine Learning-studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Selectie taaktype](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [geautomatiseerde ml](concept-automated-ml.md) in Azure Machine Learning.
+ Meer informatie over het automatisch trainen van een prognosemodel voor [tijdreeksen](how-to-auto-train-forecast.md) in Azure Machine Learning
+ Probeer de [zelfstudie Voor geautomatiseerde machine learning-classificatie.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)
+ Probeer het voorbeeldnotitieblok [Van Automatische Machine Learning Regressie.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation)

