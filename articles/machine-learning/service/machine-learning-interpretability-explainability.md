---
title: Interpreteerbaarheid van modellen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw model voorspellingen maakt met behulp van de Azure Machine Learning SDK. Het kan worden gebruikt tijdens de training en om te begrijpen hoe uw model voor spellingen doet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 703416d2df103c1ac8db06cbef43600058c250ef
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350465"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>Modellen interpreteren met Azure Machine Learning

In dit artikel leert u hoe u kunt uitleggen waarom uw model de voor spellingen heeft gemaakt met de verschillende interpreteer bare pakketten van de Azure Machine Learning python SDK.

Met de klassen en methoden in de SDK kunt u het volgende doen:
+ Belang rijke waarden van de functie voor zowel onbewerkte als ontworpen functies
+ Interpreteer baarheid voor de gegevens sets op schaal tijdens de training en de interferentie.
+ Interactieve visualisaties die u helpen bij het ontdekken van patronen in gegevens en uitleg tijdens de trainings tijd

Tijdens de trainings fase van de ontwikkelings cyclus kunnen model ontwerpers en evaluatorers gebruikmaken van de interpretatieve uitvoer van een model om hypo Thesen te controleren en om vertrouwen te bouwen met belanghebbenden.  Ze gebruiken ook de inzichten in het model voor het opsporen van fouten, het valideren van het model gedrag aan hun doel stellingen en om te controleren op bias.

In machine learning zijn **functies** de gegevens velden die worden gebruikt om een doel gegevens punt te voors pellen. Bijvoorbeeld, om het krediet risico te voors pellen, kunnen gegevens velden voor leeftijd, account grootte en account leeftijd worden gebruikt. In dit geval zijn de leeftijd, de account grootte en de account duur **functies**. Functie belang vertelt u hoe elk gegevens veld de voor spellingen van het model beïnvloedt. Leeftijd kan bijvoorbeeld intensief worden gebruikt in de voor spelling, terwijl de grootte van het account en de leeftijd geen invloed hebben op de nauw keurigheid van de voor spelling. Met dit proces kunnen gegevens wetenschappers de resulterende voor spellingen uitleggen, zodat de belanghebbenden inzicht hebben in welke gegevens punten het belangrijkst zijn in het model.

Met deze hulpprogram ma's kunt u machine learning modellen **globaal op alle gegevens**of **lokaal op een specifiek gegevens punt** uitleggen met behulp van de geavanceerde technologieën in een gemakkelijk te gebruiken en schaal bare manier.

De interpreters klassen worden beschikbaar gesteld via meerdere SDK-pakketten. Meer informatie over het [installeren van SDK-pakketten voor Azure machine learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), het hoofd pakket, dat functionaliteiten bevat die door micro soft worden ondersteund.

* `azureml.contrib.explain.model`, de preview-versie en experimentele functionele functies die u kunt proberen.

* `azureml.train.automl.automlexplainer`pakket voor het interpreteren van automatische machine learning modellen.

> [!IMPORTANT]
> Inhoud in de `contrib` naam ruimte wordt niet volledig ondersteund. Naarmate de experimentele functionele onderdelen rijp worden, worden ze geleidelijk verplaatst naar de hoofd naam ruimte.

## <a name="how-to-interpret-your-model"></a>Het model interpreteren

U kunt de klassen voor interpretaties en methoden Toep assen om inzicht te krijgen in het globale gedrag van het model of specifieke voor spellingen. De eerste wordt globale uitleg genoemd en de laatste wordt een lokale uitleg genoemd.

De methoden kunnen ook worden gecategoriseerd op basis van het feit of de methode model neutraal of model specifiek is. Sommige methoden richten zich op een bepaald type modellen. De boom uitleg van SHAP is bijvoorbeeld alleen van toepassing op op structuur gebaseerde modellen. Sommige methoden behandelen het model als een zwart vak, zoals nabootsen of de kernel-uitleg van SHAP. Het `explain` pakket maakt gebruik van deze verschillende benaderingen op basis van gegevens sets, model typen en use cases.

De uitvoer is een set informatie over hoe een bepaald model de voor spelling maakt, zoals:
* Globaal/lokaal relatief belang rijk onderdeel

* Globale/lokale functie en Voorspellings relatie

### <a name="explainers"></a>Uitleg

Er zijn twee sets met uitleg: Directe uitleg en uitleg van Metaers in de SDK.

__Directe uitleg__ is afkomstig van geïntegreerde bibliotheken. De SDK verpakt alle uitleg zodat deze een gemeen schappelijke API-en uitvoer indeling beschikbaar stelt. Als u direct vertrouwd bent met deze uitleg, kunt u ze rechtstreeks aanroepen in plaats van de gemeen schappelijke API-en uitvoer indeling te gebruiken. Hieronder vindt u een lijst met de beschik bare directe uitleg voor de SDK:

* **Uitleg**over de Shap-structuur: De boom uitleg van de SHAP, die gericht is op een polynomiale, snelle SHAP waarde schattings algoritme die specifiek is voor bomen en ensembles van structuren.
* **Shap diepe uitleg**: Op basis van de uitleg van SHAP is diepe uitleg een uiterst snelle benaderings algoritme voor SHAP-waarden in diepe leer modellen die zijn gebaseerd op een verbinding met DeepLIFT beschreven in het SHAP NIPS-papier. Tensor flow-modellen en Keras-modellen met behulp van de tensor flow-backend worden ondersteund (er is ook voorlopige ondersteuning voor PyTorch) '.
* **Shap-kernel-uitleg**: De kernel-uitleg van SHAP maakt gebruik van een speciaal gewogen lokale lineaire regressie om SHAP-waarden voor elk model te schatten.
* **Uitleg uitleggen**: Nabooter is gebaseerd op het idee van globale surrogaat modellen. Een globaal surrogaat model is een intrinsiek interpretable model dat is getraind om de voor spellingen van een Black Box-model zo nauw keurig mogelijk te benaderen. Data wetenschapper kan het surrogaat model interpreteren om conclusies over het zwarte box-model te tekenen. U kunt een van de volgende verwerkte modellen gebruiken als surrogaat model: LightGBM (LinearExplainableModel), lineaire regressie (LinearExplainableModel), stochastische kleur overgang Daal verklarend model (SGDExplainableModel) en beslissings structuur (DecisionTreeExplainableModel).


* **Belangrijkste uitleg**van de permutatie-functie: Het belang van de permutatie functie is een techniek die wordt gebruikt om classificatie-en regressie modellen te verklaren die zijn geïnspireerd op [het breiman van een wille keurige bossen](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (zie sectie 10). Op hoog niveau kan de manier waarop het werkt, worden uitgevoerd door in wille keurige volg orde gegevens één functie op te nemen voor de hele gegevensset en te berekenen hoeveel de prestatie metriek van de rente afneemt. Hoe groter de verandering, des te belang rijker deze functie is.

* **Kalk-uitleg** (`contrib`): Op basis van kalk maakt kalk lichter gebruik van het algoritme van de geavanceerde, lokaal interpretable-neutraal verklaring (kalk) om lokale surrogaat modellen te maken. In tegens telling tot de globale surrogaat modellen, is kalk gericht op het trainen van lokale surrogaat modellen om afzonderlijke voor spellingen te verklaren.
* **Han-tekst uitleg** (`contrib`): HAN-tekst uitleg er wordt gebruikgemaakt van een hiërarchische aandacht voor het verkrijgen van model verklaringen van tekst gegevens voor een gegeven zwart vak in een tekst model. We trainen het surrogaat model van het HAN op een bepaald docent model voor spelde uitvoer. Na de wereld wijde training over de tekst verzameling hebben we een nauw keurige stap toegevoegd voor een specifiek document, zodat de nauw keurigheid van de uitleg kan worden verbeterd. HAN maakt gebruik van een bidirectionele RNN met twee attentie lagen, voor de aandacht van zinnen en woorden. Zodra de DNN is getraind op het docenten model en op een specifiek document is afgestemd, kunnen we de woord urgentie uit de attentie lagen extra heren. We hebben een HAN gevonden om nauw keuriger te zijn dan kalk-of SHAP voor tekst gegevens, maar ook meer kosten in het kader van de trainings tijd. We hebben echter verbeteringen aangebracht in de trainings tijd door de gebruiker de optie te geven het netwerk te initialiseren met ondersteunt woord insluitingen, hoewel het nog steeds langzaam is. De trainings tijd kan aanzienlijk worden verbeterd door een HAN uit te voeren op een externe Azure GPU-VM. De implementatie van HAN wordt beschreven in [' hiërarchische aandacht netwerken voor document classificatie (Yang et al., 2016) '](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


__META uitlegers__ selecteren automatisch een geschikte directe uitleg en genereren de informatie over de beste uitleg op basis van de opgegeven model-en gegevens sets. De meta verklarende leden maken gebruik van alle bibliotheken (SHAP, kalk, naboot, enzovoort) die we hebben geïntegreerd of ontwikkeld. Hieronder vindt u de meta-uitleg die beschikbaar is in de SDK:

* **Uitleg in tabel vorm**: Wordt gebruikt met tabellaire gegevens sets.
* **Tekst uitleg**: Wordt gebruikt met tekst gegevens sets.
* **Afbeeldings uitleg**: Wordt gebruikt bij afbeeldings gegevens sets.

Naast het meta-selecteren van de rechtstreekse verklarende onderlichters, ontwikkelen de meta-uitleg voor de hand van de onderliggende bibliotheken aanvullende functies en worden de snelheid en schaal baarheid van de directe uitlegers verbeterd.

Maakt `TabularExplainer` momenteel gebruik van de volgende logica voor het aanroepen van de directe Shap-uitleg:

1. Als het een structuur model is, past u Shap `TreeExplainer`toe, else
2. Als het een DNN-model is, past `DeepExplainer`u Shap toe, else
3. Behandel het als een zwart-box model en pas SHAP toe`KernelExplainer`

De ingebouwde `TabularExplainer` intelligentie wordt Geavanceerd geworden naarmate er meer bibliotheken zijn geïntegreerd in de SDK en we leren over de voor-en nadelen van elke uitleg.

`TabularExplainer`heeft ook aanzienlijke verbeteringen aangebracht in de functie en prestaties ten opzichte van de rechtstreekse uitleg:

* **Samen vatting van de initialisatie-gegevensset**. In gevallen waarin de snelheid van uitleg het belangrijkst is, vatten we de initialisatie-gegevensset samen en genereren ze een kleine set representatieve voor beelden, waardoor zowel globale als lokale uitleg wordt versneld.
* **Bemonstert u de set evaluatie gegevens**. Als de gebruiker een grote set evaluatie voorbeelden heeft door gegeven, maar niet alle moet worden geëvalueerd, kan de para meter sample worden ingesteld op True om de globale uitleg te versnellen.

In het volgende diagram ziet u de huidige structuur van directe en meta-uitleg.

[![Architectuur van Machine Learning-interpretaties](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Ondersteunde modellen

Modellen die zijn getraind op gegevens sets `numpy.array`in `pandas.DataFrame`python `iml.datatypes.DenseData`,, `scipy.sparse.csr_matrix` `explain` of Format worden ondersteund door het interpreter-pakket van de SDK.

De uitleg functies accepteren zowel modellen als pijp lijnen als invoer. Als er een model wordt gegeven, moet het model de Voorspellings functie `predict` implementeren `predict_proba` of voldoet aan de Scikit-Conventie. Als er een pijp lijn (naam van het pijplijn script) wordt gegeven, wordt ervan uitgegaan dat het actieve pijplijn script een voor spelling retourneert. We ondersteunen modellen die zijn getraind via PyTorch, tensor flow en Keras diepe leer frameworks.

### <a name="local-and-remote-compute-target"></a>Lokaal en extern Compute-doel

Het `explain` pakket is ontworpen om te werken met zowel lokale als externe Compute-doelen. Als de SDK-functies lokaal worden uitgevoerd, worden er geen contact opgenomen met Azure-Services. U kunt de uitleg op afstand uitvoeren op Azure Machine Learning Compute en de uitleg informatie vastleggen in Azure Machine Learning uitvoerings geschiedenis Services. Zodra deze informatie is geregistreerd, zijn rapporten en visualisaties van de uitleg beschikbaar op Azure Machine Learning-werkruimte portal voor gebruikers analyse.

## <a name="interpretability-in-training"></a>Interpretiteit in training

### <a name="train-and-explain-locally"></a>Lokaal trainen en uitleggen

1. Train uw model in een lokale Jupyter-notebook.

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

2. De uitleger aanroepen: Als u een uitleg object wilt initialiseren, moet u uw model en enkele trainings gegevens door geven aan de constructor van de uitleg. U kunt eventueel ook namen van functies en namen van uitvoer klassen door geven (als de classificatie wordt uitgevoerd), die wordt gebruikt om uw uitleg en visualisaties meer informatieend te maken. Hier wordt beschreven hoe u een object van een uitleg maakt met behulp van [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)en [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) . `TabularExplainer`roept een van de drie Shap-uitleg onder (`TreeExplainer`, `DeepExplainer`, of `KernelExplainer`) aan en selecteert automatisch het meest geschikte deel voor uw use-case. U kunt echter elk van de drie onderliggende uitlegers rechtstreeks aanroepen.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    of

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel,SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

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
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. De belang rijke waarden van de globale functie ophalen.

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

4. De prioriteits waarden van de lokale functie ophalen: gebruik de volgende functie aanroepen om een afzonderlijk exemplaar of een groep instanties te verklaren. Houd er rekening mee dat PFIExplainer geen lokale uitleg ondersteunt.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    of

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:5])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Train en leg op afstand

Hoewel u kunt trainen op de verschillende reken doelen die door Azure Machine Learning worden ondersteund, wordt in het voor beeld in deze sectie beschreven hoe u dit doet met behulp van een Azure Machine Learning Compute-doel.

1. Een trainings script maken in een lokale Jupyter-Notebook (bijvoorbeeld run_explainer. py).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

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

2. Volg de instructies voor het [instellen van reken doelen voor model training voor](how-to-set-up-training-targets.md#amlcompute) meer informatie over het instellen van een Azure machine learning berekenen als uw reken doel en het verzenden van uw trainings uitvoering.

3. Down load de uitleg in uw lokale Jupyter-notebook.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
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

Gebruik het visualisatie dashboard om uw model te begrijpen en te interpreteren:

### <a name="global-visualizations"></a>Globale visualisaties

De volgende grafieken bieden een globaal overzicht van het getrainde model samen met de voor spellingen en toelichtingen.

|Tekenen|Description|
|----|-----------|
|Gegevens verkennen| Een overzicht van de gegevensset samen met de Voorspellings waarden.|
|Wereld wijd belang|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Deze grafiek is handig als u wilt weten wat het globale gedrag van het onderliggende model is.|
|Uitleg over verkennen|Laat zien hoe een functie verantwoordelijk is voor het maken van een wijziging in de Voorspellings waarden van het model (of waarschijnlijkheid van voorspellings waarden). |
|Samenvatting| Gebruikt een ondertekende lokale functie belang rijke waarden voor alle gegevens punten om de distributie van de impact van elke functie op de Voorspellings waarde weer te geven.|

[![Visualisatie dashboard globaal](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Lokale visualisaties

U kunt op elk gewenst moment op elk wille keurig gegevens punt klikken om het urgentie diagram van de lokale functie te laden voor het gegeven gegevens punt.

|Tekenen|Description|
|----|-----------|
|Lokale urgentie|Hier worden de belangrijkste functies van de K (Configureer bare K) wereld wijd weer gegeven. Deze grafiek is handig om het lokale gedrag van het onderliggende model op een specifiek gegevens punt te weten te komen.|
|Perturbation-exploratie|Hiermee kunt u de functie waarden van het geselecteerde gegevens punt wijzigen en bekijken hoe deze wijzigingen van invloed zijn op de Voorspellings waarde.|
|Afzonderlijke Voorwaardelijke verwachting (ijs)| Hiermee kunt u een onderdeel waarde van een minimum waarde wijzigen in een maximum waarde om te zien hoe de voor spelling van het gegevens punt wordt gewijzigd wanneer een functie wordt gewijzigd.|

[![Prioriteit van lokale functie van visualisatie dashboard](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Functie perturbation voor visualisatie dashboard](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![IJS van visualisatie dashboard](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Opmerking: u moet beschikken over widget extensies van het visualisatie dashboard dat is ingeschakeld voordat de Jupyter-kernel wordt gestart.

* Jupyter-notebooks

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Gebruik de volgende code om het visualisatie dashboard te laden:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Trans formaties onbewerkte onderdelen

Optioneel kunt u uw functie transformatie pijplijn door geven aan de uitleg om uitleg te krijgen over de onbewerkte functies vóór de trans formatie (in plaats van de functies die zijn ontworpen). Als u dit overs laat, biedt de uitleg uitleg over de functies die zijn ontworpen voor de functie.

De indeling van ondersteunde trans formaties is hetzelfde als de notatie die wordt beschreven in [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Over het algemeen worden trans formaties ondersteund zolang ze worden toegepast op één kolom en daarom duidelijk een van de vele. 

We kunnen onbewerkte functies uitleggen door gebruik `sklearn.compose.ColumnTransformer` te maken van een of een lijst met de bijpassende Tuples. De cel hieronder wordt `sklearn.compose.ColumnTransformer`gebruikt. 

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

## <a name="interpretability-at-inferencing-time"></a>Interpretatieve tijd

De uitleg kan samen met het oorspronkelijke model worden geïmplementeerd en kan worden gebruikt op het moment dat de informatie over de lokale uitleg wordt verstrekt. We bieden ook licht gewichten uitleg over scores om interacties te kunnen interpreteren. Het proces voor het implementeren van een lichtere Score uitleg is vergelijkbaar met het implementeren van een model en bevat de volgende stappen:




1. Een object uitleg maken (bijvoorbeeld met behulp van TabularExplainer):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Maak een uitleg over scores met behulp van het uitleg-object:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

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

1. Opschonen: Als u wilt verwijderen van een geïmplementeerde webservice, gebruikt u `service.delete()`.




## <a name="next-steps"></a>Volgende stappen

Als u een verzameling Jupyter-notebooks wilt weer geven met de bovenstaande instructies, raadpleegt u de [Azure machine learning interpretive sample notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
