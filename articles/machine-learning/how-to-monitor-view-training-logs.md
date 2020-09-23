---
title: MILLILITERs logboeken & metrische gegevens controleren en weer geven
titleSuffix: Azure Machine Learning
description: Bewaak uw Azure ML experimenten en geef metrische uitvoerings gegevens weer om het proces voor het maken van het model te verbeteren. Gebruik widgets en de Studio Portal om de uitvoerings status te verkennen en uitvoerings records weer te geven.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 6f557ac8476224cdb8414b2a45952d8f7d34513d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904989"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>MILLILITERs logboeken en-metrische gegevens bewaken en weer geven



In dit artikel leert u hoe u Azure Machine Learning uitvoeringen bewaakt en de logboeken bekijkt. Voordat u Logboeken kunt weer geven, moet u deze eerst inschakelen. Zie [logboek registratie inschakelen in azure ml-trainings uitvoeringen](how-to-track-experiments.md)voor meer informatie.

Logboeken kunnen u helpen bij het onderzoeken van fouten en waarschuwingen, of bij het volgen van prestatie gegevens, zoals para meters en model nauwkeurigheid. In dit artikel leert u hoe u Logboeken kunt weer geven met behulp van de volgende methoden:

> [!div class="checklist"]
> * Monitors worden uitgevoerd in de Studio
> * Monitors worden uitgevoerd met behulp van de Jupyter Notebook-widget
> * Automatische machine learning uitvoeringen bewaken
> * Uitvoer logboeken na voltooiing weer geven
> * Uitvoer logboeken weer geven in de Studio

Zie [trainings uitvoeringen starten, controleren en annuleren](how-to-manage-runs.md)voor algemene informatie over het beheren van uw experimenten.

## <a name="monitor-runs-in-the-studio"></a>Monitors worden uitgevoerd in de Studio

Voer de volgende stappen uit om de uitvoering van een specifiek reken doel vanuit uw browser te controleren:

1. Selecteer in de [Azure machine learning Studio](https://ml.azure.com/)uw werk ruimte en selecteer vervolgens __Compute__ in de linkerkant van de pagina.

1. Selecteer __trainings clusters__ om een lijst weer te geven met reken doelen die worden gebruikt voor de training. Selecteer vervolgens het cluster.

    ![Het trainings cluster selecteren](./media/how-to-track-experiments/select-training-compute.png)

1. Selecteer __uitvoeren__. De lijst met uitvoeringen die gebruikmaken van dit cluster wordt weer gegeven. Als u Details voor een specifieke uitvoering wilt weer geven, gebruikt u de koppeling in de kolom __uitvoeren__ . Als u Details voor het experiment wilt weer geven, gebruikt u de koppeling in de kolom __experiment__ .

    ![Uitvoeringen voor trainings cluster selecteren](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Omdat de doelen van de trainings Compute een gedeelde resource zijn, kunnen ze op een bepaald moment meerdere uitvoeringen in de wachtrij plaatsen of actief zijn.
    > 
    > Een run kan onderliggende uitvoeringen bevatten, waardoor één trainings taak kan leiden tot meerdere vermeldingen.

Zodra een uitvoering is voltooid, wordt deze niet meer weer gegeven op deze pagina. Als u informatie over voltooide uitvoeringen wilt bekijken, gaat u naar de sectie __experimenten__ van Studio en selecteert u het experiment en voert u uit. Zie metrische gegevens van de sectie [weergave voor voltooide uitvoeringen](#view-the-experiment-in-the-web-portal)voor meer informatie.

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Monitor uitvoeringen met behulp van de Jupyter notebook-widget

Wanneer u de methode **ScriptRunConfig** gebruikt om uitvoeringen te verzenden, kunt u de voortgang van de uitvoering bekijken met behulp van de [Jupyter-widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true). Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

Bekijk de widget Jupyter tijdens het wachten tot de uitvoering is voltooid.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Scherm opname van Jupyter notebook-widget](./media/how-to-track-experiments/run-details-widget.png)

U kunt ook een koppeling naar dezelfde weer gave in uw werk ruimte ophalen.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Automatische machine learning uitvoeringen bewaken

Voor automatische machine learning uitvoeringen, voor het openen van de grafieken van een vorige uitvoering, vervangt u door `<<experiment_name>>` de juiste naam van het experiment:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter notebook-widget voor automatische Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Uitvoer weer geven na voltooiing

Wanneer u **ScriptRunConfig**gebruikt, kunt u gebruiken ```run.wait_for_completion(show_output = True)``` om weer te geven wanneer de model training is voltooid. De ```show_output``` vlag geeft u uitgebreide uitvoer. Zie de sectie ScriptRunConfig voor meer informatie over het [inschakelen van logboek registratie](how-to-track-experiments.md#scriptrunconfig-logs).

<a id="queryrunmetrics"></a>
## <a name="query-run-metrics"></a>Metrische gegevens van query uitvoeren

U kunt de metrische gegevens van een getraind model weer geven met behulp van ```run.get_metrics()``` . U kunt dit bijvoorbeeld gebruiken met het bovenstaande voor beeld om het beste model te bepalen door te zoeken naar het model met de laagste waarde voor de kwadraat fout (MSE).

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-run-records-in-the-studio"></a>Uitvoerings records weer geven in de Studio

In de [Azure machine learning Studio](https://ml.azure.com)kunt u voltooide records voor uitvoeren bekijken, inclusief vastgelegde metrische gegevens.

Ga naar het tabblad **experimenten** en selecteer uw experiment. In het dash board voor experimenteel uitvoeren kunt u bijgehouden metrische gegevens en logboeken voor elke uitvoering bekijken. 

Inzoomen op een specifieke uitvoering om de bijbehorende uitvoer of logboeken weer te geven, of down load de moment opname van het experiment zodat u de map experiment met anderen kunt delen.

U kunt ook de tabel uitvoerings lijst bewerken om meerdere uitvoeringen te selecteren en de laatste, minimale of maximale vastgelegde waarde voor uw uitvoeringen weer te geven. Pas uw grafieken aan om de vastgelegde metrische waarden en aggregaties over meerdere uitvoeringen te vergelijken.

![Details uitvoeren in de Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="format-charts-in-the-studio"></a>Grafieken opmaken in de Studio

Gebruik de volgende methoden in de logboek registratie-Api's om uw metrische gegevens in de Studio te beïnvloeden.

|Geregistreerde waarde|Voorbeeldcode| Indeling in Portal|
|----|----|----|
|Een matrix met numerieke waarden vastleggen in een logboek| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|lijn diagram met één variabele|
|Een enkele numerieke waarde met dezelfde metrische naam in het logboek vastleggen, herhaaldelijk gebruikt (bijvoorbeeld van binnen een for-lus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Lijn diagram met één variabele|
|Een rij met 2 numerieke kolommen herhaaldelijk vastleggen|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Lijn diagram met twee variabelen|
|Logboek tabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Lijn diagram met twee variabelen|


## <a name="next-steps"></a>Volgende stappen

Voer de volgende stappen uit om te leren hoe u Azure Machine Learning gebruikt:

* Meer informatie over het [bijhouden van experimenten en het inschakelen van Logboeken in de Azure machine learning Designer](how-to-track-designer-experiments.md).

* Bekijk een voorbeeld van hoe u het beste model registreert en implementeert in de zelfstudie [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).

