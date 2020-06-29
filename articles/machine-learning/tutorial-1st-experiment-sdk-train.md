---
title: 'Zelfstudie: Uw eerste Azure ML-model trainen in Python'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie leert u de basisontwerppatronen in Azure Machine Learning en traint u een eenvoudig scikit-learn-model op basis van de gegevensset voor diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.custom: tracking-python
ms.openlocfilehash: e82ed66240144f94e18c3343dc0559f47722a2c4
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84667085"
---
# <a name="tutorial-train-your-first-ml-model"></a>Zelfstudie: Uw eerste ML-model trainen

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Deze zelfstudie is **deel twee van een tweedelige reeks**. In de vorige zelfstudie hebt u [een werkruimte gemaakt en een ontwikkelomgeving](tutorial-1st-experiment-sdk-setup.md) gekozen. In deze zelfstudie leert u de basisontwerppatronen in Azure Machine Learning en traint u een eenvoudig scikit-learn-model op basis van de gegevensset voor diabetes. Na afronding van deze zelfstudie beschikt u over de praktische kennis over de SDK die u nodig hebt om meer complexe experimenten en werkstromen te gaan ontwikkelen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw werkruimte verbinden en een experiment maken
> * Gegevens laden en scikit-learn-modellen trainen
> * Trainingsresultaten weergeven in de studio
> * Het beste model ophalen

## <a name="prerequisites"></a>Vereisten

Het enige vereiste is om deel een van deze zelfstudie, [Omgeving en werkruimte instellen](tutorial-1st-experiment-sdk-setup.md), uit te voeren.

In dit deel van de zelfstudie voert u de code uit in het voorbeeld van de Jupyter-notebook *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* dat is geopend aan het eind van deel één. In dit artikel wordt dezelfde code besproken als die zich in de notebook bevindt.

## <a name="open-the-notebook"></a>De notebook openen

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com/).

1. Open **tutorial-1st-experiment-sdk-train.ipynb** in uw map, zoals is aangegeven in [deel één](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> Maak **niet** een *nieuwe* notebook in de Jupyter-interface! *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* van de notebook bevat **alle code en gegevens die nodig zijn** voor deze zelfstudie.

## <a name="connect-workspace-and-create-experiment"></a>Werkruimte verbinden en experiment maken

> [!Important]
> De rest van dit artikel bevat dezelfde inhoud als die u ziet in de notebook.  
>
> Schakel nu over naar de Jupyter-notebook als u wilt meelezen tijdens het uitvoeren van de code. 
> Als u één codecel in een notebook wilt uitvoeren, klikt u op de codecel en drukt u op **Shift + Enter**. U kunt ook de hele notebook uitvoeren door **Alle uitvoeren** te kiezen op de bovenste werkbalk.

Importeer de `Workspace`-klasse en laad uw abonnementsgegevens vanuit het bestand `config.json` met behulp van de functie `from_config().`. Hiermee wordt standaard gezocht naar het JSON-bestand in de huidige map, maar u kunt ook een padparameter opgeven om naar het bestand te verwijzen met `from_config(path="your/file/path")`. Op een notebookserver in de cloud bevindt het bestand zich automatisch in de hoofdmap.

Als met de volgende code wordt gevraagd om extra verificatie, plakt u de koppeling eenvoudigweg in een browser en voert u het verificatietoken in.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Maak nu een experiment in uw werkruimte. Een experiment is een andere basiscloudresource die een verzameling proeven vertegenwoordigt (afzonderlijke modeluitvoeringen). In deze zelfstudie gebruikt u het experiment om uitvoeringen te maken en uw modeltraining te volgen in de Azure Machine Learning Studio. Parameters bevatten uw werkruimtereferentie en een tekenreeksnaam voor het experiment.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Gegevens laden en voorbereiden voor training

Voor deze zelfstudie gebruikt u de gegevensset voor diabetes, die functies als leeftijd, geslacht en BMI gebruikt om de voortgang van de ziekte diabetes te voorspellen. Laad de gegevens uit de klasse [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) en splits deze in trainings- en testsets met behulp van `train_test_split()`. Met deze functie worden de gegevens gescheiden, zodat het model ongebruikte gegevens heeft om te testen na training.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Een model trainen

Training voor een eenvoudig scikit-learn-model kan eenvoudig lokaal worden gedaan voor kleinschalige trainingen, maar bij het trainen van veel iteraties met tientallen verschillende functiepermutaties en hyperparameterinstellingen verliest u al snel het overzicht van welke modellen u hebt getraind en hoe u deze hebt getraind. In het volgende ontwerppatroon ziet u hoe u de SDK kunt gebruiken om uw training eenvoudig bij te houden in de cloud.

Bouw een script dat ridge-modellen in een lus traint via verschillende hyperparameteralfawaarden.


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

Met de bovenstaande code worden de volgende handelingen uitgevoerd:

1. Voor elke hyperparameteralfawaarde in de matrix `alphas` wordt een nieuwe uitvoering binnen het experiment gemaakt. De alfawaarde wordt vastgelegd om onderscheid te maken tussen elke uitvoering.
1. Bij elke uitvoering wordt een ridge-model geïnstantieerd, getraind en gebruikt voor het uitvoeren van voorspellingen. De gemiddelde kwadratische fout wordt berekend voor de werkelijke versus voorspelde waarden en vervolgens geregistreerd bij de uitvoering. Op dit moment bevat de uitvoering metagegevens die zijn gekoppeld aan de alfawaarde en nauwkeurigheid van de gemiddelde kwadratische fout.
1. Vervolgens wordt het model voor elke uitvoering geserialiseerd en geüpload naar de uitvoering. Zo kunt u het modelbestand downloaden uit de uitvoering in de studio.
1. Aan het einde van elke iteratie wordt de uitvoering voltooid door `run.complete()` aan te roepen.

Nadat de training is voltooid, roept u de `experiment`-variabele aan om een koppeling naar het experiment op te halen in de studio.

```python
experiment
```

<table style="width:100%"><tr><th>Naam</th><th>Werkruimte</th><th>Rapportpagina</th><th>Documentpagina</th></tr><tr><td>diabetes-experiment</td><td>uw-werkruimtenaam</td><td>Koppeling naar Azure Machine Learning-studio</td><td>Koppeling naar documentatie</td></tr></table>

## <a name="view-training-results-in-studio"></a>Trainingsresultaten weergeven in studio

Als u de **Koppeling naar Azure Machine Learning-studio** volgt, gaat u naar de hoofdpagina van het experiment. Hier ziet u alle afzonderlijke uitvoeringen in het experiment. Eventuele aangepaste waarden (in dit geval `alpha_value` en `rmse`) worden velden voor elke uitvoering en komen ook beschikbaar voor de grafieken. Om een ​​nieuwe grafiek met vastgelegde metrische gegevens uit te zetten, klikt u op 'Grafiek toevoegen' en selecteert u de metrische gegevens die u wilt plotten.

Wanneer trainingsmodellen worden geschaald op honderden en duizenden afzonderlijke uitvoeringen, kunt u op deze pagina eenvoudig elk model zien dat u hebt getraind, in het bijzonder hoe ze zijn getraind en hoe uw unieke metrische gegevens in de loop van de tijd zijn veranderd.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Hoofdpagina van het experiment in de studio.":::


Selecteer een koppeling voor het uitvoeringsnummer in de kolom `RUN NUMBER` om de pagina voor een afzonderlijke uitvoering weer te geven. Op het standaardtabblad **Details** wordt meer gedetailleerde informatie weergegeven over elke uitvoering. Ga naar het tabblad **Uitvoer en logboeken**. Hier ziet u het `.pkl`-bestand voor het model dat tijdens elke trainingsiteratie naar de uitvoering is geüpload. Hier kunt u het modelbestand downloaden in plaats van het handmatig opnieuw te moeten trainen.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Pagina met details van de uitvoering in de studio.":::

## <a name="get-the-best-model"></a>Het beste model verkrijgen

Naast het downloaden van modelbestanden van het experiment in de studio, kunt u ze ook programmatisch downloaden. Met de volgende code wordt elke uitvoering in het experiment herhaald en worden de metrische gegevens en de details van de uitvoering (die de run_id bevat) weergegeven. Hiermee wordt de beste uitvoering bijgehouden, in dit geval de uitvoering met de laagste gemiddelde kwadratische fout.

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

Gebruik de id van de beste uitvoering om de afzonderlijke uitvoering op te halen met behulp van de `Run`-constructor samen met het experimentobject. Roep vervolgens `get_file_names()` aan om alle bestanden te zien die via deze uitvoering kunnen worden gedownload. In dit geval hebt u slechts één bestand geüpload voor elke uitvoering tijdens de training.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Roep `download()` aan voor het uitvoeringsobject, waarbij u de naam opgeeft van het modelbestand dat u wilt downloaden. Standaard wordt door deze functie gedownload naar de huidige map.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Resources opschonen

Voer deze sectie niet uit als u van plan bent andere Azure Machine Learning-zelfstudies uit te voeren.

### <a name="stop-the-compute-instance"></a>Het rekenproces stoppen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles verwijderen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

U kunt de resourcegroep ook bewaren en slechts één werkruimte verwijderen. Bekijk de eigenschappen van de werkruimte en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> * Uw werkruimte verbonden en een experiment gemaakt
> * Gegevens geladen en scikit-learn-modellen getraind
> * Trainingsresultaten bekeken in de studio en modellen opgehaald

[Uw model implementeren](tutorial-deploy-models-with-aml.md) met Azure Machine Learning.
Meer informatie over het ontwikkelen van [geautomatiseerde machine learning](tutorial-auto-train-models.md)-experimenten.
