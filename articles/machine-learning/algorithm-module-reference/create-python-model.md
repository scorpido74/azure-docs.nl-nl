---
title: 'Python-model maken: naslaginformatie over module'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Python-model maken in Azure Machine Learning om een aangepaste modellerings- of gegevensverwerkingsmodule te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682806"
---
# <a name="create-python-model-module"></a>Python-modelmodule maken

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Meer informatie over het gebruik van de module Python-model maken om een ongetraind model te maken op basis van een Python-script. U het model baseren op elke leerling die is opgenomen in een Python-pakket in de Azure Machine Learning-ontwerpomgeving. 

Nadat u het model hebt gemaakt, u [Train Model](train-model.md) gebruiken om het model te trainen op een gegevensset, net als elke andere leerling in Azure Machine Learning. Het getrainde model kan worden doorgegeven aan [scoremodel](score-model.md) om voorspellingen te doen. U vervolgens het getrainde model opslaan en de scorewerkstroom publiceren als een webservice.

> [!WARNING]
> Momenteel is het niet mogelijk om de gescoorde resultaten van een Python-model door te geven aan [Het Model evalueren.](evaluate-model.md) Als u een model wilt evalueren, u een aangepast Python-script schrijven en uitvoeren met de [Python Script-module uitvoeren.](execute-python-script.md)  


## <a name="configure-the-module"></a>De module configureren

Het gebruik van deze module vereist tussentijdse of deskundige kennis van Python. De module ondersteunt het gebruik van elke leerling die is opgenomen in de Python-pakketten die al zijn geïnstalleerd in Azure Machine Learning. Zie de lijst met vooraf geïnstalleerde Python-pakketten in [Python Script uitvoeren](execute-python-script.md).

> [!NOTE]
> Wees erg voorzichtig bij het schrijven van uw script en zorgt ervoor dat er geen syntaxisfout optreedt, zoals het gebruik van een niet-gedeclareerd object of een niet-geïmporteerde module.

> [!NOTE]
Besteed ook extra aandacht aan de vooraf geïnstalleerde moduleslijst in [Execute Python Script.](execute-python-script.md) Importeer alleen vooraf geïnstalleerde modules. Installeer geen extra pakketten zoals "pip install xgboost" in dit script, anders worden er fouten gemaakt bij het lezen van modellen in downstreammodules.
  
In dit artikel ziet u hoe **u Python-model maken** met een eenvoudige pijplijn gebruiken. Hier is een diagram van de pijplijn:

![Diagram van Python-model maken](./media/module/create-python-model.png)

1. Selecteer **Python-model maken**en bewerk het script om uw modellerings- of gegevensbeheerproces te implementeren. U het model baseren op elke leerling die is opgenomen in een Python-pakket in de Azure Machine Learning-omgeving.

> [!NOTE]
> Let extra op de opmerkingen in voorbeeldcode van het script en zorg ervoor dat uw script strikt voldoet aan de vereiste, inclusief de klassenaam, methoden en methodehandtekening. Overtreding zal leiden tot uitzonderingen. 

   De volgende voorbeeldcode van de twee-klasse Naïeve Bayes classifier maakt gebruik van de populaire *sklearn* pakket:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Sluit de **module Python-model maken** die u zojuist hebt gemaakt om model en **scoremodel**te **trainen.**

1. Als u het model wilt evalueren, voegt u een [Python Script-module uitvoeren](execute-python-script.md) toe en bewerkt u het Python-script.

   Het volgende script is voorbeeldevaluatiecode:

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

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 