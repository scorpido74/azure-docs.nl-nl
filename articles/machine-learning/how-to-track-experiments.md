---
title: ML-experimenten registreren & statistieken
titleSuffix: Azure Machine Learning
description: Houd uw Azure ML-experimenten in de gaten en controleer de uitvoeren van statistieken om het proces voor het maken van modellen te verbeteren. Voeg logboekregistratie toe aan uw trainingsscript en bekijk de geregistreerde resultaten van een run.  Gebruik run.log, Run.start_logging of ScriptRunConfig.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/12/2020
ms.custom: seodec18
ms.openlocfilehash: 0c77e9d0aa4f44f33b1345a6021fc0378459ee85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296962"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Azure ML-experiment-uitvoeringen en -statistieken bewaken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Verbeter het proces voor het maken van modellen door uw experimenten bij te houden en de uitvoerenvan statistieken te monitoren. Lees in dit artikel hoe u logboekcode toevoegt aan uw trainingsscript, een experiment uitvoeren, de resultaten in Azure Machine Learning controleren en inspecteren.

> [!NOTE]
> Azure Machine Learning kan tijdens de training ook gegevens uit andere bronnen registreren, zoals geautomatiseerde machine learning-uitvoeringen of de Docker-container waarin de trainingstaak wordt uitgevoerd. Deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen deze logboeken mogelijk worden gebruikt tijdens het oplossen van problemen.

> [!TIP]
> De informatie in dit document is voornamelijk bedoeld voor gegevenswetenschappers en ontwikkelaars die het modeltrainingsproces willen volgen. Zie Azure [Machine Learning](monitor-azure-machine-learning.md)controleren als u een beheerder bent die het gebruik van resources en gebeurtenissen van Azure Machine Learning wil controleren, zoals quota, voltooide trainingsuitvoeringen of voltooide modelimplementaties.

## <a name="available-metrics-to-track"></a>Beschikbare statistieken om bij te houden

De volgende statistieken kunnen worden toegevoegd aan een run tijdens het trainen van een experiment. Zie de [referentiedocumentatie voor](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)de klasse Uitvoeren om een meer gedetailleerde lijst weer te geven van wat er op een run kan worden bijgehouden.

|Type| Python, functie | Opmerkingen|
|----|:----|:----|
|Scalaire waarden |Functie:<br>`run.log(name, value, description='')`<br><br>Voorbeeld:<br>run.log("nauwkeurigheid", 0,95) |Log een numerieke of tekenreekswaarde op de run met de opgegeven naam. Als u een statistiek registreert voor een run, wordt die statistiek opgeslagen in de runrecord in het experiment.  U dezelfde statistiek meerdere keren binnen een run registreren, waarbij het resultaat wordt beschouwd als een vector van die statistiek.|
|Lijsten|Functie:<br>`run.log_list(name, value, description='')`<br><br>Voorbeeld:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Log een lijst met waarden aan bij de run met de opgegeven naam.|
|Rij|Functie:<br>`run.log_row(name, description=None, **kwargs)`<br>Voorbeeld:<br>run.log_row("Y over X", x=1, y=0.4) | Met behulp van *log_row* een statistiek met meerdere kolommen zoals beschreven in kwargs. Elke benoemde parameter genereert een kolom met de opgegeven waarde.  *log_row* kan één keer worden aangeroepen om een willekeurige tuple of meerdere keren in een lus te registreren om een volledige tabel te genereren.|
|Tabel|Functie:<br>`run.log_table(name, value, description='')`<br><br>Voorbeeld:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0,6, 0,7, 0,89]}) | Log een woordenboekobject aan bij de run met de opgegeven naam. |
|Installatiekopieën|Functie:<br>`run.log_image(name, path=None, plot=None)`<br><br>Voorbeeld:<br>`run.log_image("ROC", plot=plt)` | Log een afbeelding in op de runrecord. Gebruik log_image om een afbeeldingsbestand of een matplotlib-plot op de vlucht te registreren.  Deze beelden zullen zichtbaar en vergelijkbaar zijn in de run record.|
|Een run taggen|Functie:<br>`run.tag(key, value=None)`<br><br>Voorbeeld:<br>run.tag("geselecteerd", "ja") | Tag de run met een tekenreekstoets en optionele tekenreekswaarde.|
|Bestand of map uploaden|Functie:<br>`run.upload_file(name, path_or_stream)`<br> <br> Voorbeeld:<br>run.upload_file("best_model.pkl", "./model.pkl") | Upload een bestand naar de run record. Hiermee wordt automatisch het vastleggen van bestanden uitgevoerd in de opgegeven uitvoermap, die standaard wordt ingesteld op "./uitvoer" voor de meeste uitvoertypen.  Gebruik upload_file alleen wanneer extra bestanden moeten worden geüpload of een uitvoermap niet is opgegeven. We raden `outputs` u aan om de naam aan te voegen, zodat deze wordt geüpload naar de uitvoermap. U alle bestanden die zijn gekoppeld aan deze run record door geroepen`run.get_file_names()`|

> [!NOTE]
> Metriek voor scalairs, lijsten, rijen en tabellen kan tekst hebben: zweven, geheel getal of tekenreeks.

## <a name="choose-a-logging-option"></a>Kies een logboekregistratieoptie

Als u uw experiment wilt bijhouden of controleren, moet u code toevoegen om te beginnen met registreren wanneer u de run verzendt. De volgende manieren zijn om de run indiening trigger:
* __Run.start_logging__ - Voeg logboekfuncties toe aan uw trainingsscript en start een interactieve logboeksessie in het opgegeven experiment. **start_logging** maakt een interactieve run voor gebruik in scenario's zoals notitieblokken. Alle statistieken die tijdens de sessie worden geregistreerd, worden toegevoegd aan de runrecord in het experiment.
* __ScriptRunConfig__ - Voeg logboekfuncties toe aan uw trainingsscript en laad de hele scriptmap met de run.  **ScriptRunConfig** is een klasse voor het instellen van configuraties voor scriptuitvoeringen. Met deze optie u bewakingscode toevoegen om op de hoogte te worden gesteld van de voltooiing of om een visuele widget te laten controleren.

## <a name="set-up-the-workspace"></a>De werkruimte instellen
Voordat u logboekregistratie toevoegt en een experiment indient, moet u de werkruimte instellen.

1. Laad de werkruimte. Zie [configuratiebestand voor werkruimtevoor](how-to-configure-environment.md#workspace)meer informatie over het instellen van de werkruimteconfiguratie .

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_ws)]


## <a name="option-1-use-start_logging"></a>Optie 1: Gebruik start_logging

**start_logging** maakt een interactieve run voor gebruik in scenario's zoals notitieblokken. Alle statistieken die tijdens de sessie worden geregistreerd, worden toegevoegd aan de runrecord in het experiment.

Het volgende voorbeeld traint een eenvoudig sklearn Ridge-model lokaal in een lokaal Jupyter-notitieboek. Zie [Rekendoelen instellen voor modeltraining instellen met Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-set-up-training-targets)voor meer informatie over het indienen van experimenten in verschillende omgevingen.

### <a name="load-the-data"></a>De gegevens laden

In dit voorbeeld wordt gebruik gemaakt van de diabetesdataset, een bekende kleine dataset die wordt geleverd met scikit-learn. Deze cel laadt de gegevensset en splitst deze op in willekeurige trainings- en testsets.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=load_data)]

### <a name="add-tracking"></a>Tracking toevoegen
Voeg experimenttracking toe met de Azure Machine Learning SDK en upload een blijvend model in de experimentrunrecord. In de volgende code worden tags, logboeken en uploadt u een modelbestand naar de experimentrun.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Het script ```run.complete()```eindigt met , die de run markeert als voltooid.  Deze functie wordt meestal gebruikt in interactieve notitieblokscenario's.

## <a name="option-2-use-scriptrunconfig"></a>Optie 2: ScriptRunConfig gebruiken

[**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) is een klasse voor het instellen van configuraties voor scriptuitvoeringen. Met deze optie u bewakingscode toevoegen om op de hoogte te worden gesteld van de voltooiing of om een visuele widget te laten controleren.

Dit voorbeeld breidt uit op de fundamentele sklearn Ridge model van bovenaf. Het doet een eenvoudige parameter sweep te vegen over alpha-waarden van het model om statistieken en getrainde modellen vast te leggen in runs onder het experiment. Het voorbeeld wordt lokaal uitgevoerd tegen een door de gebruiker beheerde omgeving. 

1. Een trainingsscript `train.py`maken .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]

2. De `train.py` scriptverwijzingen `mylib.py` waarmee u de lijst met alfawaarden gebruiken in het nokmodel.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/mylib.py)] 

3. Een door de gebruiker beheerde lokale omgeving configureren.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=user_managed_env)]


4. Verzend ```train.py``` het script dat moet worden uitgevoerd in de door de gebruiker beheerde omgeving. Deze hele scriptmap wordt ingediend ```mylib.py``` voor training, inclusief het bestand.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]




## <a name="manage-a-run"></a>Een run beheren

In het [artikel Trainingsuitvoeringen starten, bewaken en annuleren](how-to-manage-runs.md) worden specifieke Azure Machine Learning-werkstromen uitgelicht voor het beheren van uw experimenten.

## <a name="view-run-details"></a>Voerdetails weergeven

### <a name="view-activequeued-runs-from-the-browser"></a>Actieve/in de wachtrij staande uitvoeringen vanuit de browser weergeven

Rekendoelen die worden gebruikt om modellen te trainen, zijn een gedeelde bron. Als zodanig kunnen ze meerdere runs in de wachtrij of actief op een bepaald moment. Als u de uitvoeringen voor een specifiek rekendoel vanuit uw browser wilt bekijken, voert u de volgende stappen uit:

1. Selecteer in de [Azure Machine Learning-studio](https://ml.azure.com/)uw werkruimte en selecteer __Vervolgens Berekenen__ aan de linkerkant van de pagina.

1. Selecteer __Clusters trainen__ om een lijst met rekendoelen weer te geven die worden gebruikt voor training. Selecteer vervolgens het cluster.

    ![Het trainingscluster selecteren](./media/how-to-track-experiments/select-training-compute.png)

1. Selecteer __Uitvoeringen__. De lijst met uitvoeringen die dit cluster gebruiken, wordt weergegeven. Als u details voor een specifieke run wilt weergeven, gebruikt u de koppeling in de kolom __Uitvoeren.__ Als u details voor het experiment wilt weergeven, gebruikt u de koppeling in de kolom __Experiment.__

    ![Runs selecteren voor trainingscluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Een run kan onderliggende uitvoeringen bevatten, dus één trainingstaak kan resulteren in meerdere vermeldingen.

Zodra een run is voltooid, wordt deze niet meer weergegeven op deze pagina. Als u informatie over voltooide runs wilt bekijken, gaat u naar de sectie __Experimenten__ van de studio en selecteert u het experiment en voert u het experiment uit. Zie de sectie [Queryrun-statistieken](#queryrunmetrics) voor meer informatie.

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Monitor uitgevoerd met Jupyter-notitieblokwidget
Wanneer u de **ScriptRunConfig-methode** gebruikt om hardloopruns in te dienen, u de voortgang van de run bekijken met een [Jupyter-widget.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) Net als het indienen van de run, is de widget asynchroon en biedt deze elke 10-15 seconden live updates totdat de taak is voltooid.

1. Bekijk de Jupyter widget in afwachting van de run te voltooien.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Schermafbeelding van de widget Jupyter-notitieblok](./media/how-to-track-experiments/run-details-widget.png)

   U ook een koppeling naar hetzelfde scherm in uw werkruimte krijgen.

   ```python
   print(run.get_portal_url())
   ```

2. **[Voor geautomatiseerde machine learning-uitvoeringen]** Om toegang te krijgen tot de grafieken van een vorige run. Vervang `<<experiment_name>>` de juiste experimentnaam:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Jupyter notebook widget voor Automated Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Als u meer details wilt bekijken over een pijplijn, klikt u op de pijplijn die u in de tabel wilt verkennen en worden de grafieken weergegeven in een pop-up van de Azure Machine Learning-studio.

### <a name="get-log-results-upon-completion"></a>Resultaten van logboeken weergeven bij voltooiing

Modeltraining en -bewaking vinden plaats op de achtergrond, zodat u andere taken uitvoeren terwijl u wacht. U ook wachten tot het model de training heeft voltooid voordat u meer code uitvoert. Wanneer u **ScriptRunConfig**gebruikt, ```run.wait_for_completion(show_output = True)``` u deze gebruiken om aan te geven wanneer de modeltraining is voltooid. De ```show_output``` vlag geeft je verbose output. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Statistieken voor query-run

U de statistieken van ```run.get_metrics()```een getraind model bekijken met behulp van. U nu alle statistieken die in het bovenstaande voorbeeld zijn aangemeld, krijgen om het beste model te bepalen.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studio"></a>Het experiment weergeven in uw werkruimte in [Azure Machine Learning-studio](https://ml.azure.com)

Wanneer een experiment is uitgevoerd, u bladeren naar de opgenomen experimentrunrecord. U hebt toegang tot de geschiedenis vanuit de [Azure Machine Learning-studio.](https://ml.azure.com)

Navigeer naar het tabblad Experimenten en selecteer uw experiment. U wordt naar het dashboard voor experimentrun gebracht, waar u bijgehouden statistieken en grafieken zien die voor elke run zijn geregistreerd. In dit geval hebben we MSE en de alfawaarden geregistreerd.

  ![Details uitvoeren in de Azure Machine Learning-studio](./media/how-to-track-experiments/experiment-dashboard.png)

U inzoomen op een specifieke run om de uitvoer of logboeken te bekijken, of de momentopname downloaden van het experiment dat u hebt ingediend, zodat u de experimentmap met anderen delen.

### <a name="viewing-charts-in-run-details"></a>Grafieken weergeven in voerdetails

Er zijn verschillende manieren om de API's voor logboekregistratie te gebruiken om verschillende soorten statistieken op te nemen tijdens een uitvoering en deze te bekijken als grafieken in Azure Machine Learning-studio.

|Aangemelde waarde|Voorbeeldcode| Weergave in portal|
|----|----|----|
|Een array met numerieke waarden registreren| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|lijndiagram met één variabele|
|Eén numerieke waarde met dezelfde metrische naam herhaaldelijk registreren (zoals vanuit een voorlus)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Lijndiagram met één variabele|
|Een rij met 2 numerieke kolommen herhaaldelijk registreren|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Lijndiagram met twee variabelen|
|Logboektabel met 2 numerieke kolommen|`run.log_table(name='Sine Wave', value=sines)`|Lijndiagram met twee variabelen|


## <a name="example-notebooks"></a>Voorbeeldnotitieblokken
De volgende notitieblokken tonen concepten in dit artikel:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

Doe het volgende om te leren hoe u de Azure Machine Learning-SDK voor Python kunt gebruiken:

* Zie een voorbeeld van hoe u het beste model registreren en implementeren in de zelfstudie, [Een afbeeldingsclassificatiemodel trainen met Azure Machine Learning](tutorial-train-models-with-aml.md).

* Meer informatie over het [trainen van PyTorch-modellen met Azure Machine Learning](how-to-train-pytorch.md).
