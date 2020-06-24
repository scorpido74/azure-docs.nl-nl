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
ms.date: 06/17/2020
ms.openlocfilehash: 51b95255139a3c5c4d3b522b24144e289b233199
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944250"
---
# <a name="how-to-define-a-machine-learning-task"></a>Een machine learning taak definiëren 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel vindt u informatie over de ondersteunde machine learning taken en hoe u deze kunt definiëren voor een experiment voor automatische machine learning (automatisch ML).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Wat is een machine learning-taak?

Een machine learning taak vertegenwoordigt het type probleem dat wordt opgelost door een voorspellend model te maken. Automatische ML ondersteunt drie verschillende typen taken, waaronder classificatie, regressie en time series-prognose.

Taak type| Beschrijving| Voorbeeld
----|----|----
Classificatie | Taak voor het voors pellen van de categorie van een bepaalde rij in een gegevensset. | Fraude detectie op een credit card. De doel kolom zou **fraude kunnen detecteren** met de categorieën *waar* of *Onwaar*. In dit geval classificeren we elke rij in de gegevens als waar of onwaar.
Regressie | Taak voor het voors pellen van een doorlopende hoeveelheid uitvoer. | De **prijs**van de auto is afhankelijk van de functies van de doel kolom.
Prognose |Taak voor het maken van gefundeerde schattingen bij het bepalen van de richting van toekomstige trends.| Prognose van de energie vraag voor hen om de komende 48 uur. De doel kolom is **vraag** en de voorspelde waarden worden gebruikt om patronen in de energie vraag weer te geven.

Automatische ML ondersteunt de volgende algoritmen tijdens het automatiserings-en afstemmings proces. Als gebruiker hoeft u het algoritme niet op te geven.

Classificatie | Regressie | Tijdreeksvoorspelling
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Licht GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Kleur overgang verhogen](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#regression)|[Beslissings structuur](https://scikit-learn.org/stable/modules/tree.html#regression)
[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K dichtstbijzijnde neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Lineair-SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-Support Vector classificatie (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Zeer wille keurige structuren](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Stochastische kleur overgang Daal (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Het taak type instellen
U kunt het taak type voor uw geautomatiseerde ML experimenten instellen met de SDK of de Azure Machine Learning Studio.

Gebruik de `task` para meter in de `AutoMLConfig` constructor om uw type experiment op te geven.

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
+ Probeer de zelf studie voor [geautomatiseerde machine learning classificatie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) .
+ Probeer de [geautomatiseerde machine learning regressie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) -voorbeeld notitieblok.

