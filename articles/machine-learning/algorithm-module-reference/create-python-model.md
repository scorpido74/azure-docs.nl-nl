---
title: 'Python-model maken: module verwijzing'
titleSuffix: Azure Machine Learning
description: Leer hoe u het python-model model maken in Azure Machine Learning kunt gebruiken om een aangepaste module voor model lering of gegevens verwerking te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: c07ae779334c97b28803d77d2ce4a1b5b7962b48
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314416"
---
# <a name="create-python-model"></a>Python-model maken

In dit artikel wordt een module in Azure Machine Learning Designer beschreven.

Meer informatie over het gebruik van de module **python-model maken** voor het maken van een niet-traind model van een python-script. U kunt het model baseren op alle informatie die is opgenomen in een python-pakket in de Azure Machine Learning Designer-omgeving. 

Nadat u het model hebt gemaakt, kunt u [Train model](train-model.md) gebruiken om het model op een gegevensset te trainen, zoals elke andere informatieer in azure machine learning. Het getrainde model kan worden door gegeven aan het [score model](score-model.md) voor het gebruik van het model voor het maken van voor spellingen. Het getrainde model kan vervolgens worden opgeslagen en de Score werk stroom kan worden gepubliceerd als een webservice.

> [!WARNING]
> Het is momenteel niet mogelijk om de gescoorde resultaten van een python-model door te geven om het [model te evalueren](evaluate-model.md). Als u een model moet evalueren, kunt u aangepaste python-scripts schrijven en deze uitvoeren met behulp van de script module voor het [uitvoeren van python](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Het maken van een python-model configureren

Het gebruik van deze module vereist tussenliggende of deskundige kennis van python. De module ondersteunt het gebruik van alle informatie die is opgenomen in de Python-pakketten die al in Azure Machine Learning zijn geïnstalleerd. Zie de lijst met vooraf geïnstalleerde Python-pakketten in een [python-script uitvoeren](execute-python-script.md).
  

In dit artikel wordt uitgelegd hoe u het **python-model maken** gebruikt met een eenvoudige pijp lijn. Hieronder ziet u de grafiek van de pijp lijn.

![Create-python-model](./media/module/aml-create-python-model.png)

1.  Klik op **python-model maken**, bewerk het script om uw model of gegevens beheer proces te implementeren. U kunt het model baseren op een informatieer die deel uitmaakt van een python-pakket in de Azure Machine Learning omgeving.


    Hieronder ziet u een voor beeld van een Naive Bayes-classificatie met twee klassen door gebruik te maken van het populaire *sklearn* -pakket.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Verbind de module **python-model maken** die u zojuist hebt gemaakt in een **Train model** en- **score model**

3. Als u het model wilt evalueren, voegt u een [python-script uitvoeren](execute-python-script.md) toe en bewerkt u het python-script om de evaluatie uit te voeren.

Hieronder ziet u een voor beeld van een evaluatie code.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
