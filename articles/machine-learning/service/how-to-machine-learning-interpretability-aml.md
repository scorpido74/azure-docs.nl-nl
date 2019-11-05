---
title: Model interpreteren voor lokale en externe uitvoeringen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw model voorspellingen maakt met behulp van de Azure Machine Learning SDK. Het kan worden gebruikt tijdens training en demijnen om te begrijpen hoe het model van de functie belang rijk is en voor spellingen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515302"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Model interpreteren voor lokale en externe uitvoeringen

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u kunt uitleggen waarom uw model de voor spellingen heeft gemaakt met het vertolkings pakket van de Azure Machine Learning python SDK. U leert de volgende taken:

* machine learning modellen interpreteren die zowel lokaal als op externe reken resources zijn getraind
* Lokale en wereld wijde uitleg over Azure-uitvoerings geschiedenis opslaan
* Visuele elementen voor interpretaties weer geven in [Azure machine learning Studio](https://ml.azure.com)
* Een beoordelings uitleg implementeren met uw model

Zie het [concept artikel](how-to-machine-learning-interpretability.md)voor meer informatie over het vertolken van modellen.

## <a name="local-interpretability"></a>Lokale interpretbaarheid

In het volgende voor beeld ziet u hoe u het pakket interpreteert, zonder dat u contact hoeft op te nemen met Azure-Services. Voer `pip install azureml-interpret` uit om het interpreter-pakket te verkrijgen.

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

2. De uitleg lokaal aanroepen: als u een uitleg object wilt initialiseren, moet u uw model en enkele trainings gegevens door geven aan de constructor van de uitleg. U kunt eventueel ook namen van functies en namen van uitvoer klassen door geven (als de classificatie wordt uitgevoerd), die wordt gebruikt om uw uitleg en visualisaties meer informatieend te maken. Hier wordt beschreven hoe u een object van een uitleg maakt met behulp van `TabularExplainer`, `MimicExplainer`en lokaal `PFIExplainer`. `TabularExplainer` roept een van de drie SHAP-uitleg (`TreeExplainer`, `DeepExplainer`of `KernelExplainer`) aan en selecteert automatisch het meest geschikte voor uw use-case. U kunt echter elk van de drie onderliggende uitlegers rechtstreeks aanroepen.

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

### <a name="overall-global-feature-importance-values"></a>Algehele prioriteits waarden van functie (Global)

De belang rijke waarden van de globale functie ophalen.
    
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

### <a name="instance-level-local-feature-importance-values"></a>Urgentie waarden van de functie op exemplaar niveau (lokaal)

De prioriteits waarden van de lokale functie ophalen: gebruik de volgende functie aanroepen om een afzonderlijk exemplaar of een groep instanties te verklaren. Houd er rekening mee dat PFIExplainer geen lokale uitleg ondersteunt.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretiteit voor externe uitvoeringen

In dit voor beeld ziet u hoe u de `ExplanationClient`-klasse gebruikt voor het inschakelen van de vertolking van modellen voor externe uitvoeringen. Het concept is vergelijkbaar met de vorige sectie, maar u kunt de `ExplanationClient` in de externe uitvoering gebruiken om de interinterpreter-context te uploaden, en de context vervolgens later in een lokale omgeving downloaden. Gebruik `pip install azureml-contrib-interpret` om het benodigde pakket op te halen.

1. Een trainings script maken in een lokale Jupyter-Notebook (bijvoorbeeld train_explain. py).

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

1. Volg de instructies voor het [instellen van reken doelen voor model training](how-to-set-up-training-targets.md#amlcompute) voor meer informatie over het instellen van een Azure machine learning berekenen als uw reken doel en het verzenden van uw trainings uitvoering. U kunt ook de [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)bekijken.

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

Desgewenst kunt u uw functie transformatie pijplijn door geven aan de uitleger (in train_explain. py) om uitleg te ontvangen over de onbewerkte functies vóór de trans formatie (in plaats van de functies die zijn ontworpen). Als u dit overs laat, biedt de uitleg uitleg over de functies die zijn ontworpen voor de functie.

De indeling van ondersteunde trans formaties is hetzelfde als de notatie die wordt beschreven in [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Over het algemeen worden trans formaties ondersteund zolang ze worden toegepast op één kolom en daarom duidelijk een van de vele. 

Leg onbewerkte functies uit door gebruik te maken van een `sklearn.compose.ColumnTransformer` of een lijst met de gepaste transformator-Tuples. De onderstaande code maakt gebruik van `sklearn.compose.ColumnTransformer`. 


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

Gebruik de volgende code om het voor beeld uit te voeren met de lijst met de ingebouwde Tuples.

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

Zodra u de uitleg in uw lokale Jupyter-notebook hebt gedownload, kunt u het visualisatie dashboard gebruiken om uw model te begrijpen en te interpreteren.

### <a name="global-visualizations"></a>Globale visualisaties

De volgende grafieken bieden een globaal overzicht van het getrainde model samen met de voor spellingen en toelichtingen.

|Ontwerp|Beschrijving|
|----|-----------|
|Gegevens verkennen| Een overzicht van de gegevensset samen met de Voorspellings waarden.|
|Wereld wijd belang|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Deze grafiek is handig als u wilt weten wat het globale gedrag van het onderliggende model is.|
|Uitleg over verkennen|Laat zien hoe een functie verantwoordelijk is voor het maken van een wijziging in de Voorspellings waarden van het model (of waarschijnlijkheid van voorspellings waarden). Er wordt ook gedemonstreerd hoe twee functies de voor spellingen beïnvloeden.|
|Prioriteit van samen vatting| Gebruikt een ondertekende lokale functie belang rijke waarden voor alle gegevens punten om de distributie van de impact van elke functie op de Voorspellings waarde weer te geven.|

[![visualisatie dashboard globaal](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokale visualisaties

Klik op elk gewenst moment in de voor gaande grafieken op elk wille keurig gegevens punt om het belang van de lokale functie te laden voor het gegeven gegevens punt.

|Ontwerp|Beschrijving|
|----|-----------|
|Lokale urgentie|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Deze grafiek is handig om het lokale gedrag van het onderliggende model op een specifiek gegevens punt te weten te komen.|
|Perturbation-exploratie|Hiermee kunt u de functie waarden van het geselecteerde gegevens punt wijzigen en bekijken hoe deze wijzigingen van invloed zijn op de Voorspellings waarde.|
|Afzonderlijke Voorwaardelijke verwachting (ijs)| Hiermee kunt u een onderdeel waarde van een minimum waarde wijzigen in een maximum waarde om te zien hoe de voor spelling van het gegevens punt wordt gewijzigd wanneer een functie wordt gewijzigd.|

[Prioriteit van lokale functie van ![visualisatie dashboard](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![visualisatie dashboard functie perturbation](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[ICE-grafieken van ![visualisatie-dash board](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Opmerking: u moet beschikken over widget extensies van het visualisatie dashboard dat is ingeschakeld voordat de Jupyter-kernel wordt gestart.

* Jupyter-notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Gebruik de volgende code om het visualisatie dashboard te laden.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisatie in Azure Machine Learning Studio

Door de stappen in de sectie [externe interpretatie](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) te volt ooien, kunt u het visualisatie dashboard controleren in [Azure machine learning Studio](https://ml.azure.com). Het dash board dat wordt weer gegeven in Azure Machine Learning Studio, is een eenvoudigere versie van het visualisatie dashboard dat hierboven is beschreven en ondersteunt alleen de volgende twee tabbladen.

|Ontwerp|Beschrijving|
|----|-----------|
|Wereld wijd belang|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Deze grafiek is handig als u wilt weten wat het globale gedrag van het onderliggende model is.|
|Prioriteit van samen vatting| Gebruikt een ondertekende lokale functie belang rijke waarden voor alle gegevens punten om de distributie van de impact van elke functie op de Voorspellings waarde weer te geven.|

Als zowel globale als lokale uitleg beschikbaar zijn, worden beide tabbladen gevuld met gegevens. Als er alleen globale uitleg beschikbaar is, wordt het tweede tabblad uitgeschakeld.

Om toegang te krijgen tot het visualisatie dashboard in Azure Machine Learning Studio, kunt u een van de volgende paden door lopen:

1. Het tabblad experimenten (preview): door te klikken op het tabblad experimenten ziet u een lijst met experimenten die u hebt uitgevoerd op Azure Machine Learning service. In deze lijst kunt u een bepaald experiment selecteren dat wordt omgeleid naar een pagina met alle uitvoeringen onder de geselecteerde naam van het experiment. Als u op elke uitvoering en op het tabblad uitleg wordt geklikt, wordt het visualisatie dashboard voor uitleg weer geven.


[Prioriteit van lokale functie van ![visualisatie dashboard](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Tabblad modellen: als u uw oorspronkelijke model hebt geregistreerd met behulp van de stappen in [modellen implementeren met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), wordt uw model weer gegeven in de lijst van het tabblad ' modellen '. Als u op elk model klikt en op het tabblad uitleg wordt uitgelegd, ziet u het visualisatie dashboard voor uitleg.

## <a name="interpretability-at-inference-time"></a>Interpreteer tijd

De uitleg kan samen met het oorspronkelijke model worden geïmplementeerd en kan worden gebruikt om de informatie over de lokale uitleg te verstrekken. We bieden ook licht gewichten uitleg over scores om de prestaties van de interpretaties te verbeteren. Het proces voor het implementeren van een lichtere Score uitleg is vergelijkbaar met het implementeren van een model en bevat de volgende stappen:


1. Een object uitleg maken (bijvoorbeeld met behulp van TabularExplainer):

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een uitleg over scores met behulp van het uitleg-object:

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

1. Beschrijving Haal de uitleg over scores in de Cloud op en test de uitleg

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

1. Implementeer de installatie kopie naar een berekenings doel:

   1. Een score bestand maken (voordat u deze stap uitvoert, volgt u de stappen in [modellen implementeren met Azure machine learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) om uw oorspronkelijke Voorspellings model te registreren)

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

   1. Definieer de implementatie configuratie (deze configuratie is afhankelijk van de vereisten van uw model. In het volgende voor beeld wordt een configuratie gedefinieerd die gebruikmaakt van één CPU-kern en 1 GB geheugen)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Een bestand met omgevings afhankelijkheden maken

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

   1. Een aangepaste dockerfile maken met g + + geïnstalleerd

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. De gemaakte installatie kopie implementeren (geschatte tijd: 5 minuten)

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

1. De implementatie testen

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

1. Opschonen: als u een geïmplementeerde webservice wilt verwijderen, gebruikt u `service.delete()`.

## <a name="next-steps"></a>Volgende stappen

Zie het [conceptuele artikel](how-to-machine-learning-interpretability.md)voor meer informatie over het vertolken van modellen.
