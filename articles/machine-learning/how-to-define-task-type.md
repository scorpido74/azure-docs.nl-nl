---
title: Een machine learning taak definiëren voor een geautomatiseerde machine learning uitvoering
titleSuffix: Azure Machine Learning
description: Meer informatie over het definiëren van een machine learning-taak voor een geautomatiseerde machine learning uitvoering
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: a58959904559cd3b2cec27762f2df93cfea72abd
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75540527"
---
# <a name="how-to-define-a-machine-learning-task"></a>Een machine learning taak definiëren 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over de ondersteunde machine learning taken en hoe u deze kunt definiëren voor een experiment voor automatische machine learning (automatisch ML).


## <a name="What is a machine learning task?"></a>Wat is een machine learning-taak?

Een machine learning taak vertegenwoordigt het type probleem dat wordt opgelost door een voorspellend model te maken. Automatische ML ondersteunt drie verschillende typen taken, waaronder classificatie, regressie en time series-prognose.

Taak type| Beschrijving| Voorbeeld
----|----|----
Classificatie | Taak voor het voors pellen van de categorie van een bepaalde rij in een gegevensset. | Fraude detectie op een credit card. De doel kolom zou **fraude kunnen detecteren** met de categorieën *waar* of *Onwaar*. In dit geval classificeren we elke rij in de gegevens als waar of onwaar.
Regressie | Taak voor het voors pellen van een doorlopende hoeveelheid uitvoer. | De **prijs**van de auto is afhankelijk van de functies van de doel kolom.
Prognoses |Taak voor het maken van gefundeerde schattingen bij het bepalen van de richting van toekomstige trends.| Prognose van de energie vraag voor hen om de komende 48 uur. De doel kolom is **vraag** en de voorspelde waarden worden gebruikt om patronen in de energie vraag weer te geven.

Automatische ML ondersteunt de volgende algoritmen tijdens het automatiserings-en afstemmings proces. Als een gebruiker is er niet nodig voor u het algoritme opgeven.

Classificatie | Regressie | Tijd reeks prognose
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastische Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Versterking van kleurovergang](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissingsstructuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineaire SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector classificatie (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Willekeurige Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer willekeurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Lineaire classificatie DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Lineaire Regressor hierop](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[De toolkit leren met stochastische Gradiëntdaling (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Het taak type instellen
U kunt het taak type voor uw geautomatiseerde ML experimenten instellen met de SDK of de Azure Machine Learning Studio.

Gebruik de para meter `task` in de `AutoMLConfig`-constructor om uw type experiment op te geven.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

U kunt de taak instellen als onderdeel van de automatische ontwikkeling van het experiment voor het automatiseren van ML in de Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Selectie van taak type](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [automatische ml](concept-automated-ml.md) in azure machine learning.
+ Meer informatie over [het automatisch trainen van een time-series-prognose model](how-to-auto-train-forecast.md) in azure machine learning
+ Probeer de zelf studie voor [geautomatiseerde machine learning classificatie](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Probeer de [geautomatiseerde machine learning regressie](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) -voorbeeld notitieblok.

