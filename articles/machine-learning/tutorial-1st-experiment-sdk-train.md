---
title: 'Zelfstudie: Train uw eerste Azure ML-model in Python'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie leert u de fundamentele ontwerppatronen in Azure Machine Learning en traint u een eenvoudig scikit-learn-model op basis van de gegevensset voor diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: c8f259d2d4df46470a042c3f65ac1b8e1f66b1dd
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546025"
---
# <a name="tutorial-train-your-first-ml-model"></a>Zelfstudie: Train je eerste ML-model

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Deze zelfstudie is **deel twee van een tweedelige reeks**. In de vorige zelfstudie hebt u [een werkruimte gemaakt en een ontwikkelomgeving gekozen.](tutorial-1st-experiment-sdk-setup.md) In deze zelfstudie leert u de fundamentele ontwerppatronen in Azure Machine Learning en traint u een eenvoudig scikit-learn-model op basis van de gegevensset voor diabetes. Na het voltooien van deze tutorial heb je de praktische kennis van de SDK om op te schalen naar het ontwikkelen van complexere experimenten en workflows.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw werkruimte verbinden en een experiment maken
> * Gegevens laden en scikit-learn-modellen trainen
> * Bekijk de trainingsresultaten in de studio
> * Het beste model ophalen

## <a name="prerequisites"></a>Vereisten

De enige voorwaarde is om deel één van deze zelfstudie, [setup-omgeving en werkruimte](tutorial-1st-experiment-sdk-setup.md)uit te voeren.

In dit deel van de zelfstudie voert u de code uit in de *voorbeeldhandleiding en jupyter-notebooktutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* die aan het einde van deel één is geopend. Dit artikel loopt door dezelfde code die in het notitieblok staat.

## <a name="open-the-notebook"></a>Het notitieblok openen

1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com/).

1. Open de **tutorial-1st-experiment-sdk-train.ipynb** in uw map zoals weergegeven in [deel een](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> Maak **geen** *nieuwe* notebook in de Jupyter-interface! De notebook *tutorials / create-first-ml-experiment / tutorial-1st-experiment-sdk-train.ipynb* is inclusief **alle code en gegevens die nodig zijn** voor deze tutorial.

## <a name="connect-workspace-and-create-experiment"></a>Werkruimte verbinden en experiment maken

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als u in het notitieblok ziet.  
>
> Schakel nu over naar het Jupyter-notitieblok als u wilt meelezen terwijl u de code uitvoert. 
> Als u één codecel in een notitieblok wilt uitvoeren, klikt u op de codecel en klikt u op **Shift+Enter**. Of voer het hele notitieblok uit door **Alles uitvoeren** vanaf de bovenste werkbalk te kiezen.

Importeer `Workspace` de klasse en laad uw `config.json` abonnementsgegevens `from_config().` uit het bestand met de functie Dit zoekt standaard naar het JSON-bestand in `from_config(path="your/file/path")`de huidige map, maar u ook een padparameter opgeven die u naar het bestand wilt aanwijzen met behulp van. In een cloudnotebookserver bevindt het bestand zich automatisch in de hoofdmap.

Als de volgende code om aanvullende verificatie vraagt, plakt u de koppeling in een browser en voert u het verificatietoken in.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Maak nu een experiment in uw werkruimte. Een experiment is een andere fundamentele cloudbron die een verzameling proefversies vertegenwoordigt (afzonderlijke modeluitvoeringen). In deze zelfstudie gebruikt u het experiment om oplages te maken en uw modeltraining bij te houden in de Azure Machine Learning-studio. Parameters omvatten uw werkruimteverwijzing en een tekenreeksnaam voor het experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Gegevens laden en voorbereiden op training

Voor deze zelfstudie gebruikt u de gegevensset voor diabetes, die functies als leeftijd, geslacht en BMI gebruikt om de progressie van diabetesziekten te voorspellen. Laad de gegevens uit de klasse [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) en `train_test_split()`spliter deze op in trainings- en testsets met behulp van . Deze functie scheidt de gegevens, zodat het model heeft onzichtbare gegevens te gebruiken voor het testen na de training.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Een model trainen

Het trainen van een eenvoudig scikit-learn-model kan gemakkelijk lokaal worden gedaan voor kleinschalige training, maar bij het trainen van veel iteraties met tientallen verschillende functiepermutaties en hyperparameter-instellingen, is het gemakkelijk om uit het oog te verliezen welke modellen je hebt getraind en hoe je ze hebt getraind. Het volgende ontwerppatroon laat zien hoe je de SDK gebruiken om je training in de cloud eenvoudig bij te houden.

Bouw een script dat nokmodellen traint in een lus door verschillende hyperparameter alfawaarden.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

De bovenstaande code bereikt het volgende:

1. Voor elke alfahyperparameterwaarde `alphas` in de array wordt een nieuwe run gemaakt binnen het experiment. De alfawaarde wordt geregistreerd om onderscheid te maken tussen elke run.
1. In elke run wordt een Ridge-model geinstantieerd, getraind en gebruikt om voorspellingen uit te voeren. De fout root-gemiddelde-kwadraat wordt berekend voor de werkelijke versus voorspelde waarden en vervolgens aangemeld bij de run. Op dit punt de run heeft metadata bevestigd voor zowel de alfa waarde en de rmse nauwkeurigheid.
1. Vervolgens wordt het model voor elke run geserialiseerd en geüpload naar de run. Hiermee u het modelbestand downloaden van de run in de studio.
1. Aan het einde van elke iteratie `run.complete()`wordt de run voltooid door aan te roepen .

Nadat de training is `experiment` voltooid, belt u de variabele om een koppeling naar het experiment in de studio op te halen.

```python
experiment
```

<table style="width:100%"><tr><th>Name</th><th>Werkruimte</th><th>Rapportpagina</th><th>Documentenpagina</th></tr><tr><td>diabetes-experiment</td><td>uw naam van uw werkruimte</td><td>Koppeling naar Azure Machine Learning-studio</td><td>Koppeling naar documentatie</td></tr></table>

## <a name="view-training-results-in-studio"></a>Bekijk de trainingsresultaten in de studio

Volg de **Koppeling naar Azure Machine Learning-studio** brengt u naar de hoofdexperimentpagina. Hier zie je alle individuele runs in het experiment. Alle op maat`alpha_value` geregistreerde `rmse`waarden (en in dit geval) worden velden voor elke run en worden ook beschikbaar voor de grafieken en tegels boven aan de experimentpagina. Als u een aangemelde statistiek wilt toevoegen aan een grafiek of tegel, plaats je er boven, klik je op de knop Bewerken en zoek je de statistiek die is aangepast.

Wanneer u modellen op schaal traint over honderden en duizenden afzonderlijke uitvoeringen, u met deze pagina eenvoudig elk model zien dat u hebt getraind, met name hoe ze zijn opgeleid en hoe uw unieke statistieken in de loop van de tijd zijn veranderd.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Belangrijkste experiment pagina in de studio.":::


Selecteer een koppeling runnummer in de `RUN NUMBER` kolom om de pagina voor een afzonderlijke run weer te geven. Op het tabblad **Standaardgegevens** vindt u meer gedetailleerde informatie over elke run. Navigeer naar het tabblad **Uitvoer + logboeken** en u ziet het `.pkl` bestand voor het model dat tijdens elke trainingsiteratie naar de run is geüpload. Hier u het modelbestand downloaden, in plaats van het handmatig om te scholen.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Voer details pagina in de studio.":::

## <a name="get-the-best-model"></a>Krijg het beste model

Naast de mogelijkheid om modelbestanden van het experiment in de studio te downloaden, kun je ze ook programmatisch downloaden. De volgende code wordt herhaald door elke run in het experiment, en toegang tot zowel de geregistreerde run metrics en de run details (die de run_id bevat). Dit houdt bij van de beste run, in dit geval de run met de laagste root-mean-kwadraat-fout.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Gebruik de beste run-ID om `Run` de afzonderlijke run op te halen met behulp van de constructor samen met het experimentobject. Bel `get_file_names()` vervolgens om alle bestanden te zien die beschikbaar zijn om te downloaden van deze run. In dit geval hebt u slechts één bestand geüpload voor elke run tijdens de training.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Roep `download()` het run-object aan en geef de modelbestandsnaam op die moet worden gedownload. Standaard wordt deze functie gedownload naar de huidige map.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Resources opschonen

Voltooi deze sectie niet als u van plan bent andere Azure Machine Learning-zelfstudies uit te voeren.

### <a name="stop-the-compute-instance"></a>De rekeninstantie stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Geef de eigenschappen van de werkruimte weer en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> * Uw werkruimte verbonden en een experiment gemaakt
> * Geladen gegevens en getrainde scikit-learn-modellen
> * Bekeken trainingsresultaten in de studio en opgehaalde modellen

[Uw model implementeren](tutorial-deploy-models-with-aml.md) met Azure Machine Learning.
Leer hoe u [geautomatiseerde machine learning-experimenten](tutorial-auto-train-models.md) ontwikkelen.
