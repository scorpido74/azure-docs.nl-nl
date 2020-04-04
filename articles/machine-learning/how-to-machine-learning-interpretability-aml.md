---
title: Model interpreteerbaarheid voor lokale en externe uitvoeringen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uitleg krijgt over hoe uw machine learning-model het belang van de functie bepaalt en voorspellingen doet bij het gebruik van de Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: 1ff42149ccb629a0a7094e6dfede422d4dd7f61f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632031"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Model interpreteerbaarheid voor lokale en externe uitvoeringen

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In deze handleiding leert u het interpreteerbaarheidspakket van de Azure Machine Learning Python SDK te gebruiken om de volgende taken uit te voeren:


* Leg het volledige modelgedrag of individuele voorspellingen op uw persoonlijke machine lokaal uit.

* Maak interpreteerbaarheidstechnieken mogelijk voor ontworpen functies.

* Leg het gedrag voor het hele model en individuele voorspellingen in Azure uit.

 
* Gebruik een visualisatiedashboard om te communiceren met de uitleg van uw model.

* Implementeer een scoreuitleg naast uw model om uitleg te observeren tijdens het volgen.



Zie [Interpreteerbaarheid modelleren in Azure Machine Learning](how-to-machine-learning-interpretability.md) en voorbeeldnotitieblokken voor meer informatie over de ondersteunde interpreteerbaarheidstechnieken en machine [learning-modellen.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Genereer functiebelangwaarde op uw persoonlijke machine 
In het volgende voorbeeld ziet u hoe u het interpreteerpakket op uw persoonlijke machine gebruiken zonder contact op te nemen met Azure-services.

1. Installeren `azureml-interpret` `azureml-interpret-contrib` en pakketten.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```

2. Train een voorbeeldmodel in een lokaal Jupyter-notitieboek.

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

3. Bel de uitleg lokaal.
   * Als u een explainerobject wilt initialiseren, geeft u uw model en enkele trainingsgegevens door aan de constructor van de uitleg.
   * Om uw uitleg en visualisaties informatiever te maken, u ervoor kiezen om functienamen en namen van uitvoerklassen door te geven als u classificatie doet.

   De volgende codeblokken laten zien hoe u een `TabularExplainer` `MimicExplainer`explainerobject instantiëren met, en `PFIExplainer` lokaal.
   * `TabularExplainer`noemt een van de drie`TreeExplainer`SHAP-explainers eronder ( , `DeepExplainer`, of `KernelExplainer`).
   * `TabularExplainer`selecteert automatisch de meest geschikte voor uw use case, maar u elk van de drie onderliggende explainers rechtstreeks bellen.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Het volledige modelgedrag verklaren (globale verklaring) 

Raadpleeg het volgende voorbeeld om u te helpen de algemene waarden voor het aantal functies (globale) te krijgen.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Leg een individuele voorspelling uit (lokale uitleg)
Download de individuele functiebelangwaarden van verschillende gegevenspunten door uitleg te geven voor een afzonderlijke instantie of een groep instanties.
> [!NOTE]
> `PFIExplainer`ondersteunt geen lokale uitleg.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Raw-functietransformaties

U ervoor kiezen om uitleg te krijgen in termen van ruwe, niet-getransformeerde functies in plaats van ontworpen functies. Voor deze optie geeft u de pijplijn voor `train_explain.py`functietransformatie door aan de uitleg in . Anders geeft de uitleger uitleg over ontworpen functies.

Het formaat van ondersteunde transformaties is hetzelfde als beschreven in [sklearn-panda's.](https://github.com/scikit-learn-contrib/sklearn-pandas) Over het algemeen worden transformaties ondersteund zolang ze op één kolom werken, zodat het duidelijk is dat ze één op één zijn.

Krijg een uitleg voor ruwe `sklearn.compose.ColumnTransformer` functies met behulp van een of met een lijst van ingerichte transformator tuples. In het `sklearn.compose.ColumnTransformer`volgende voorbeeld wordt gebruik gebruikt .

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

Als u het voorbeeld wilt uitvoeren met de lijst met ingebouwde transformatortuples, gebruikt u de volgende code:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Functiebelangwaarden genereren via externe oplages

In het volgende voorbeeld ziet `ExplanationClient` u hoe u de klasse gebruiken om modelinterpreteerbaarheid voor externe uitvoeringen in te schakelen. Het is conceptueel vergelijkbaar met het lokale proces, behalve u:

* Gebruik `ExplanationClient` de in de remote run om de interpretatiecontext te uploaden.
* Download de context later in een lokale omgeving.

1. Installeren `azureml-interpret` `azureml-interpret-contrib` en pakketten.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```
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

1. Stel een Azure Machine Learning Compute in als uw rekendoel en dien uw trainingsrun in. Zie [het instellen van rekendoelen voor modeltraining](how-to-set-up-training-targets.md#amlcompute) voor instructies. Mogelijk vindt u de [voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) ook nuttig.

1. Download de uitleg in uw lokale Jupyter notebook.

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


## <a name="visualizations"></a>Visualisaties

Nadat u de uitleg in uw lokale Jupyter-notitieblok hebt gedownload, u het visualisatiedashboard gebruiken om uw model te begrijpen en te interpreteren.

### <a name="understand-entire-model-behavior-global-explanation"></a>Volledig modelgedrag begrijpen (globale uitleg) 

De volgende percelen geven een algemeen beeld van het getrainde model, samen met de voorspellingen en uitleg.

|Plot|Beschrijving|
|----|-----------|
|Gegevensverkenning| Hiermee geeft u een overzicht van de gegevensset weer, samen met voorspellingswaarden.|
|Mondiaal belang|Aggregaten zijn voorzien van belangrijke waarden van het belang van afzonderlijke gegevenspunten om de algemene top K (configureerbare K) belangrijke functies van het model weer te geven. Helpt inzicht te krijgen in het algemene gedrag van het onderliggende model.|
|Uitleg Verkenning|Laat zien hoe een functie van invloed is op een wijziging in de voorspellingswaarden van het model of de waarschijnlijkheid van voorspellingswaarden. Toont de impact van functieinteractie.|
|Samenvatting Belang|Gebruikt individuele functiebelangwaarden over alle gegevenspunten om de verdeling van de impact van elke functie op de voorspellingswaarde weer te geven. Met dit diagram onderzoekt u in welke richting de functiewaarden van invloed zijn op de voorspellingswaarden.
|

[![Visualisatiedashboard Globaal](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Individuele voorspellingen begrijpen (lokale uitleg) 

U de afzonderlijke functie belang plot voor een gegevenspunt door te klikken op een van de afzonderlijke gegevens punten in een van de totale percelen.

|Plot|Beschrijving|
|----|-----------|
|Lokaal belang|Toont de belangrijkste functies van de bovenste K (configureerbare K) voor een individuele voorspelling. Helpt het lokale gedrag van het onderliggende model op een specifiek gegevenspunt te illustreren.|
|Verstoring Exploratie (wat als analyse)|Hiermee kunnen wijzigingen worden aangebracht om waarden van het geselecteerde gegevenspunt weer te geven en resulterende wijzigingen in de voorspellingswaarde waar te nemen.|
|Individuele voorwaardelijke verwachting (ICE)| Hiermee u functiewaardewijzigingen toevoegen van een minimumwaarde naar een maximale waarde. Hiermee u illustreren hoe de voorspelling van het gegevenspunt verandert wanneer een functie verandert.|

[![Lokaal functiebelang van visualisatiedashboard](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Verstoring van de functie Visualisatiedashboard](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Visualisatiedashboard ICE-plots](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Voordat de Jupyter-kernel wordt gestart, moet u widget-extensies inschakelen voor het visualisatiedashboard.

* Jupyter-notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Als u het visualisatiedashboard wilt laden, gebruikt u de volgende code.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisatie in Azure Machine Learning-studio

Als u de stappen [voor interpreteerbaarheid op afstand](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) uitvoert (gegenereerde uitleg uploaden naar Azure Machine Learning Run History), u het visualisatiedashboard bekijken in Azure Machine [Learning-studio.](https://ml.azure.com) Dit dashboard is een eenvoudigere versie van het visualisatiedashboard dat hierboven wordt uitgelegd (uitlegverkenning en ICE-plots zijn uitgeschakeld omdat er geen actieve compute in studio is die hun realtime berekeningen kan uitvoeren).

Als de gegevensset, globale en lokale uitleg beschikbaar zijn, worden alle tabbladen gevuld (behalve Verkenning per verstoring en ICE). Als er alleen een algemene uitleg beschikbaar is, zijn het tabblad Overzichtsbelang en alle lokale uitlegtabbladen uitgeschakeld.

Volg een van deze paden om toegang te krijgen tot het visualisatiedashboard in Azure Machine Learning-studio:

* **Deelvenster Experimenten** (voorbeeld)
  1. Selecteer **Experimenten** in het linkerdeelvenster om een lijst met experimenten te zien die u hebt uitgevoerd op Azure Machine Learning.
  1. Selecteer een bepaald experiment om alle uitvoeringen in dat experiment weer te geven.
  1. Selecteer een run en vervolgens het tabblad **Uitleg** naar het dashboard voor uitlegvisualisatie.

   [![Lokaal functiebelang van visualisatiedashboard](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Deelvenster Modellen**
  1. Als u uw oorspronkelijke model hebt geregistreerd door de stappen in [Deploy-modellen met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)te volgen, u **Modellen** in het linkerdeelvenster selecteren om het te bekijken.
  1. Selecteer een model en vervolgens het tabblad **Uitleg** om het dashboard voor uitlegvisualisatie weer te geven.

## <a name="interpretability-at-inference-time"></a>Interpreteerbaarheid op inference tijd

U de uitleg samen met het oorspronkelijke model implementeren en deze op inference tijd gebruiken om de individuele waarden voor functiebelang (lokale uitleg) te bieden voor nieuwe nieuwe gegevenspunten. We bieden ook lichtere score-explainers om de interpreteerbaarheid prestaties te verbeteren op inference tijd. Het proces van het implementeren van een lichtere score-explainer is vergelijkbaar met het implementeren van een model en bevat de volgende stappen:

1. Maak een uitlegobject. U bijvoorbeeld `TabularExplainer`gebruik maken van:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een scoreexplainer met het uitlegobject.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Een afbeelding configureren en registreren die het score-explainermodel gebruikt.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Als optionele stap u de scoreexplainer uit de cloud halen en de uitleg testen.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Implementeer de afbeelding naar een compute-doel door de volgende stappen te volgen:

   1. Registreer indien nodig uw oorspronkelijke voorspellingsmodel door de stappen in [Deploy-modellen te](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)volgen met Azure Machine Learning.

   1. Maak een scorebestand.

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
   1. De implementatieconfiguratie configureren.

         Deze configuratie is afhankelijk van de vereisten van uw model. In het volgende voorbeeld wordt een configuratie gedefinieerd die één CPU-kern en één GB geheugen gebruikt.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Maak een bestand met omgevingsafhankelijkheden.

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

   1. Maak een aangepast dockerbestand met g++ geïnstalleerd.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. De gemaakte afbeelding implementeren.
   
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

1. Test de implementatie.

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

1. Opruimen.

   Als u een geïmplementeerde `service.delete()`webservice wilt verwijderen, gebruikt u .

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over de interpreteerbaarheid van het model](how-to-machine-learning-interpretability.md)

[Bekijk voorbeeldnotitieblokken voor azure machine learning-interpretatienotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

