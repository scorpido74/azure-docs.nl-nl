---
title: Model interpreteren voor lokale en externe uitvoeringen
titleSuffix: Azure Machine Learning
description: Meer informatie over het verkrijgen van uitleg over hoe het machine learning-model bepaalt het belang van de functie en voor spellingen wanneer u de Azure Machine Learning SDK gebruikt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: ffb9e0547c44ee47a43de00e51933ce7d0584759
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158727"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Model interpreteren voor lokale en externe uitvoeringen

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u het vertolkings pakket van de Azure Machine Learning python SDK kunt gebruiken om te begrijpen waarom de voor spellingen van het model zijn gemaakt. Procedures voor:

* Interpreteer machine learning modellen die zowel lokaal als op externe reken resources zijn getraind.
* Sla lokale en wereld wijde uitleg op voor de Azure-uitvoerings geschiedenis.
* Bekijk de visuele elementen voor interpretaties in [Azure machine learning Studio](https://ml.azure.com).
* Implementeer een beoordelings verklaring met uw model.

Zie voor meer informatie [model interpreteert in azure machine learning service](how-to-machine-learning-interpretability.md).

## <a name="local-interpretability"></a>Lokale interpretbaarheid

In het volgende voor beeld ziet u hoe u het pakket met interpreters lokaal kunt gebruiken zonder contact op te nemen met Azure-Services.

1. Gebruik, indien nodig, `pip install azureml-interpret` om het interpreter-pakket te verkrijgen.

1. Train een voorbeeld model in een lokale Jupyter-notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

1. Roep de uitleg lokaal aan.
   * Als u een uitleg object wilt initialiseren, geeft u uw model en enkele trainings gegevens door aan de constructor van de uitleger.
   * Als u uw uitleg en visualisaties meer informatiever wilt maken, kunt u ervoor kiezen om de namen van onderdelen en uitvoer klassen door te geven als u de classificatie uitvoert.

   In de volgende code blokken ziet u hoe u een object van een uitleg maakt met `TabularExplainer`, `MimicExplainer`en lokaal `PFIExplainer`.
   * `TabularExplainer` roept een van de drie SHAP-uitleg onder (`TreeExplainer`, `DeepExplainer`of `KernelExplainer`) aan.
   * `TabularExplainer` selecteert automatisch het meest geschikte deel voor uw use-case, maar u kunt elk van de drie onderliggende uitlegers rechtstreeks aanroepen.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    of

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    of

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Algemene prioriteits waarden van globale onderdelen

Raadpleeg het volgende voor beeld om u te helpen de belang rijke waarden van de globale functie te verkrijgen.

```python

# you can use the training data or the test data here
global_explanation = explainer.explain_global(x_train)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_test)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="instance-level-local-feature-importance-values"></a>Prioriteits waarden van lokale onderdelen op exemplaar niveau

Haal de prioriteits waarden van de lokale functie op door uitleg voor een afzonderlijk exemplaar of een groep exemplaren aan te roepen.
> [!NOTE]
> `PFIExplainer` biedt geen ondersteuning voor lokale toelichtingen.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretiteit voor externe uitvoeringen

In het volgende voor beeld ziet u hoe u de klasse `ExplanationClient` kunt gebruiken om de vertolking van modellen voor externe uitvoeringen in te scha kelen. Het is conceptueel vergelijkbaar met het lokale proces, met uitzonde ring van het volgende:

* Gebruik de `ExplanationClient` in de externe uitvoering om de interpretatie context te uploaden.
* Down load de context later in een lokale omgeving.

1. Gebruik, indien nodig, `pip install azureml-contrib-interpret` om het benodigde pakket op te halen.

1. Maak een trainingsscript in een lokaal Jupyter-notitieblok. Bijvoorbeeld `train_explain.py`.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Stel een Azure Machine Learning Compute in als uw reken doel en verzend uw trainings uitvoering. Zie [Compute-doelen voor model training instellen](how-to-set-up-training-targets.md#amlcompute) voor instructies. Mogelijk vindt u ook de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) nuttig.

1. Down load de uitleg in uw lokale Jupyter-notebook.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="raw-feature-transformations"></a>Trans formaties onbewerkte onderdelen

U kunt ervoor kiezen om uitleg te krijgen over onbewerkte, niet-getransformeerde functies in plaats van functies die zijn ontworpen. Voor deze optie geeft u uw functie transformatie pijplijn door aan de uitleg bij `train_explain.py`. Anders biedt de uitleg uitleg over de functies die zijn ontworpen voor de functie.

De indeling van ondersteunde trans formaties is hetzelfde als beschreven in [sklearn-Panda](https://github.com/scikit-learn-contrib/sklearn-pandas). Over het algemeen worden trans formaties ondersteund zolang ze worden toegepast op één kolom, zodat deze duidelijk een-op-veel zijn.

Lees een uitleg voor onbewerkte functies met behulp van een `sklearn.compose.ColumnTransformer` of met een lijst met de bijpassende Tuples. In het volgende voor beeld wordt `sklearn.compose.ColumnTransformer`gebruikt.

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Als u het voor beeld wilt uitvoeren met de lijst met ingebouwde Tuples, gebruikt u de volgende code:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="visualizations"></a>Visualisaties

Nadat u de uitleg in uw lokale Jupyter-notebook hebt gedownload, kunt u het visualisatie dashboard gebruiken om uw model te begrijpen en te interpreteren.

### <a name="global-visualizations"></a>Globale visualisaties

De volgende grafieken bieden een globaal overzicht van het getrainde model samen met de voor spellingen en toelichtingen.

|Ontwerp|Beschrijving|
|----|-----------|
|Gegevens verkennen| Geeft een overzicht van de gegevensset samen met Voorspellings waarden.|
|Wereld wijd belang|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Hiermee wordt het algemene gedrag van het onderliggende model duidelijker.|
|Uitleg over verkennen|Laat zien hoe een functie van invloed is op een wijziging in de Voorspellings waarden van het model of de waarschijnlijkheid van voorspellings waarden. Hiermee wordt de impact van de functie interactie weer gegeven.|
|Prioriteit van samen vatting|Gebruikt lokale, urgentie waarden voor alle gegevens punten om de distributie van de impact van elke functie op de Voorspellings waarde weer te geven.|

[![visualisatie dashboard globaal](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokale visualisaties

U kunt de lokale grafiek van de functie urgentie voor elk gegevens punt laden door het afzonderlijke gegevens punt in het plot te selecteren.

|Ontwerp|Beschrijving|
|----|-----------|
|Lokale urgentie|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Helpt het lokale gedrag van het onderliggende model op een specifiek gegevens punt te illustreren.|
|Perturbation-exploratie|Hiermee kunnen de functie waarden van het geselecteerde gegevens punt worden gewijzigd en worden de resulterende wijzigingen in de Voorspellings waarde geobserveerd.|
|Afzonderlijke Voorwaardelijke verwachting (ijs)| Hiermee wordt de functie waarde gewijzigd van een minimum waarde naar een maximum waarde. Beschrijft hoe de voor spelling van het gegevens punt wordt gewijzigd wanneer een functie wordt gewijzigd.|

[Prioriteit van lokale functie van ![visualisatie dashboard](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![visualisatie dashboard functie perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[ICE-grafieken van ![visualisatie-dash board](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

> [!NOTE]
> Voordat de Jupyter-kernel wordt gestart, zorg ervoor dat u widget uitbreidingen voor het visualisatie dashboard inschakelt.

* Jupyter-notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* Jjupyterlab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Gebruik de volgende code om het visualisatie dashboard te laden:

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisatie in Azure Machine Learning Studio

Als u de stappen voor het [extern interpreteren](#interpretability-for-remote-runs) hebt voltooid, kunt u het visualisatie dashboard weer geven in [Azure machine learning Studio](https://ml.azure.com). Dit dash board is een eenvoudigere versie van het visualisatie dashboard dat hierboven wordt beschreven. Er worden alleen twee tabbladen ondersteund:

|Ontwerp|Beschrijving|
|----|-----------|
|Wereld wijd belang|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Hiermee wordt het algemene gedrag van het onderliggende model duidelijker.|
|Prioriteit van samen vatting|Gebruikt lokale, urgentie waarden voor alle gegevens punten om de distributie van de impact van elke functie op de Voorspellings waarde weer te geven.|

Als zowel globale als lokale uitleg beschikbaar zijn, worden beide tabbladen gevuld met gegevens. Als er alleen een globale uitleg beschikbaar is, is het tabblad urgentie prioriteit uitgeschakeld.

Volg een van deze paden om toegang te krijgen tot het visualisatie dashboard in Azure Machine Learning studio:

* Het deel venster **experimenten** (preview)
  1. Selecteer **experimenten** in het linkerdeel venster om een lijst met experimenten te bekijken die u op Azure machine learning-service hebt uitgevoerd.
  1. Selecteer een bepaald experiment om alle uitvoeringen in dat experiment weer te geven.
  1. Selecteer een run en klik vervolgens op het tabblad **uitleg** voor het visualisatie-dash board.

   [Prioriteit van lokale functie van ![visualisatie dashboard](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)

* Deel venster **modellen**
  1. Als u uw oorspronkelijke model hebt geregistreerd door de stappen in [modellen implementeren met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)te volgen, kunt u **modellen** selecteren in het linkerdeel venster om de app weer te geven.
  1. Selecteer een model en klik vervolgens op het tabblad **uitleg** om het visualisatie dashboard te bekijken.

## <a name="interpretability-at-inference-time"></a>Interpreteer tijd

U kunt de uitleger samen met het oorspronkelijke model implementeren en deze gebruiken op het moment dat u de informatie over de lokale uitleg verstrekt. We bieden ook licht gewichten uitleg over scores om de prestaties van de interacties te verbeteren. Het proces voor het implementeren van een lichtere Score uitleg is vergelijkbaar met het implementeren van een model en bevat de volgende stappen:

1. Maak een uitleg-object. U kunt bijvoorbeeld `TabularExplainer`gebruiken:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een beoordelings uitleg met het object uitleg.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configureer en Registreer een installatie kopie die gebruikmaakt van het scoreer uitleg model.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Als optionele stap kunt u de uitleg van de Score van de Cloud ophalen en de uitleg testen.

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implementeer de installatie kopie op een reken doel door de volgende stappen uit te voeren:

   1. Als dat nodig is, registreert u uw oorspronkelijke Voorspellings model door de stappen te volgen in [modellen implementeren met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where).

   1. Een score bestand maken.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. De implementatieconfiguratie definiëren.

         Deze configuratie is afhankelijk van de vereisten van uw model. In het volgende voor beeld wordt een configuratie gedefinieerd die gebruikmaakt van één CPU-kern en één GB aan geheugen.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Maak een bestand met omgevings afhankelijkheden.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Maak een aangepaste dockerfile met g + + geïnstalleerd.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Implementeer de gemaakte installatie kopie.
   
         Dit proces duurt ongeveer vijf minuten.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. De implementatie testen.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Opschonen.

   Als u wilt verwijderen van een geïmplementeerde webservice, gebruikt u `service.delete()`.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het vertolken van modellen](how-to-machine-learning-interpretability.md)