---
title: Wat is er nieuw in de release?
titleSuffix: Azure Machine Learning
description: Meer informatie over de nieuwste updates voor Azure Machine Learning en de machine learning en de python-Sdk's voor gegevens voorbereiding.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 11b8ade765a2b1c1ee25421073983b96c34e5d15
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462170"
---
# <a name="azure-machine-learning-release-notes"></a>Opmerkingen bij de release Azure Machine Learning

In dit artikel vindt u meer informatie over Azure Machine Learning releases.  Ga voor de volledige SDK-referentie-inhoud naar de hoofd pagina van de hand leiding van de Azure Machine Learning van de [**SDK voor python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .

Zie [de lijst met bekende problemen](resource-known-issues.md) met bekende bugs en tijdelijke oplossingen.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0"></a>Azure Machine Learning SDK voor python v 1.1.1 RC0

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-automl-core**
    + De logboek registratie van fouten is verbeterd.
  + **azureml-automl-runtime**
    + U hebt het probleem opgelost met prognoses wanneer de gegevensset korte korrels bevat met lange tijds intervallen.
    + Het probleem is opgelost wanneer de automatische maximum horizon is ingeschakeld en de datum kolom bevat datums in een vorm van teken reeksen. Er is een juiste conversie en een goede fout toegevoegd als de conversie op datum niet mogelijk is
    + Systeem eigen NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor file cache Store (gebruikt voor lokale AutoML-uitvoeringen)
    + Er is een fout opgelost waarbij mislukte onderliggende uitvoeringen in de actieve status kunnen raken.
  + **azureml-cli-algemeen**
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
  + **azureml-core**
    + `--grant-workspace-msi-access` toegevoegd als een extra para meter voor de Data Store CLI voor het registreren van de Azure Blob-container, waarmee u een BLOB-container kunt registreren die zich achter een VNet bevindt.
    + Profileren van één exemplaar is opgelost om een aanbeveling te doen en is beschikbaar gesteld in de core-SDK.
    + Het probleem is opgelost in aks.py _deploy
    + Valideert de integriteit van modellen die worden geüpload om problemen met de Stille opslag te voor komen.
  + **azureml-interpreteren**
    + uitzonde ringen voor azureml-stijl toegevoegd aan azureml-interprete
    + vaste DeepScoringExplainer-serialisatie voor Keras-modellen
  + **azureml-pijp lijn-kern**
    + Pipeline batch Score notebook maakt nu gebruik van ParallelRunStep
  + **azureml-pijp lijn-stappen**
    + De `AutoMLStep` in het `azureml-pipeline-steps` pakket is verplaatst. De `AutoMLStep` in `azureml-train-automl-runtime`is afgeschaft.
  + **azureml-contrib-pijp lijn-stappen**
    + Optionele para meter side_inputs toegevoegd aan ParallelRunStep. Deze para meter kan worden gebruikt om de map in de container te koppelen. De momenteel ondersteunde typen zijn DataReference en PipelineData.
  + **azureml-tensorboard**
    + update voor azureml-tensorboard om tensor Flow 2,0 te ondersteunen
  + **azureml-Train-automl-client**
    + Er is een vast FeaturizationConfig-probleem opgetreden waardoor aangepaste parametrisatie-configuratie wordt gefilterd.
  + **azureml-Train-automl-runtime**
    + De `AutoMLStep` in het `azureml-pipeline-steps` pakket is verplaatst. De `AutoMLStep` in `azureml-train-automl-runtime`is afgeschaft.
  + **azureml-Train-core**
    + Ondersteuning voor PyTorch-versie 1,4 in de PyTorch-estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0"></a>Azure Machine Learning SDK voor python v 1.1.0 RC0

+ **Wijzigingen afbreken**
  + **2.0.0 van semantische versie**
    + Vanaf versie 1,1 Azure ML python SDK worden semantische versie beheer 2.0.0. [Hier vindt u meer informatie](https://semver.org/). Alle volgende versies volgen een nieuw Nummerings schema en een contract voor semantische versie. 
  
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Verhoogde snelheid van parametrisatie.
    + De frequentie controle tijdens de score is opgelost. in de prognose taken is geen strikte frequentie-equivalentie vereist tussen de trein-en testset.
  + **azureml-core**
    + De gebruiker kan nu een waarde opgeven voor de verificatie sleutel bij het opnieuw genereren van sleutels voor webservices.
  + **azureml-interpreteren**
    + Bijgewerkt azureml-interpreteren om afhankelijk te zijn van interprete-Community 0.5.0
  + **azureml-pijp lijn-kern**
    + Er is een fout opgelost waarbij PythonScriptStep resultaten onjuist opnieuw kunnen worden gebruikt, ondanks het wijzigen van de lijst met argumenten
  + **azureml-pijp lijn-stappen**
    + Voor beeld van toegevoegde documentatie voor gegevensset als PythonScriptStep-invoer
  + **azureml-contrib-pijp lijn-stappen**
    + Para meters die in ParallelRunConfig worden door gegeven, kunnen worden overschreven door de pijplijn parameters nu door te geven. Nieuwe pijplijn parameters die worden ondersteund aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van een eerdere versie).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK voor python v-1.0.85

+ **Nieuwe functies**
  + **azureml-core**
    + Het huidige kern gebruik en de quotum limiet voor AmlCompute-resources in een bepaalde werk ruimte en een abonnement ophalen
  
  + **azureml-contrib-pijp lijn-stappen**
    + Gebruikers toestaan om tabellaire gegevensset door te geven als een tussentijds resultaat van de vorige stap naar parallelrunstep

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + De vereiste van y_query kolom in de aanvraag voor de geïmplementeerde Forecasting-service is verwijderd. 
    + De y_query is verwijderd uit de sectie Dominick van de oranje sap-Notebook Service.
    + Er is een probleem opgelost met de fout bij het voor komen van prognoses op de geïmplementeerde modellen, het werken met gegevens sets met datum en tijd kolommen.
    + Er is een Matthews-correlatie coëfficiënt toegevoegd als classificatie-metriek voor zowel binaire als multi klassen classificatie.
  + **azureml-contrib-interpreteren**
    + Verwijderde tekst uitleg van azureml-contrib-interpreteren als tekst uitleg is verplaatst naar de opslag plaats van het interpreteren van tekst die binnenkort worden vrijgegeven.
  + **azureml-core**
    + DataSet: het gebruik van een bestands gegevensset is niet langer afhankelijk van numpy en Pandas die in de python-env moeten worden geïnstalleerd.
    + LocalWebservice. wait_for_deployment () is gewijzigd om de status van de lokale docker-container te controleren voordat u probeert het status eindpunt te pingen, waardoor de hoeveelheid tijd die nodig is om een mislukte implementatie te melden, aanzienlijk wordt gereduceerd.
    + De initialisatie van een interne eigenschap die wordt gebruikt in LocalWebservice. Reload () is opgelost wanneer het Service object wordt gemaakt op basis van een bestaande implementatie met behulp van de LocalWebservice ()-constructor.
    + Bewerkt fout bericht voor een toelichting.
    + Er is een nieuwe methode met de naam get_access_token () toegevoegd aan AksWebservice die een AksServiceAccessToken-object retourneert dat een toegangs token bevat, vernieuwt na tijds tempel, verval datum van het tijds tempel en het token type. 
    + De bestaande methode get_token () in AksWebservice is afgeschaft, omdat alle informatie die deze methode retourneert, wordt geretourneerd.
    + Gewijzigde uitvoer van AZ ml service Get-access-token opdracht. De naam van het token is gewijzigd in accessToken en refreshBy naar refreshAfter. De eigenschappen expiryOn en Token type zijn toegevoegd.
    + Vaste get_active_runs
  + **azureml-uitleg-model**
    + Shap bijgewerkt naar 0.33.0 en interpreteert-community naar 0,4. *
  + **azureml-interpreteren**
    + Shap bijgewerkt naar 0.33.0 en interpreteert-community naar 0,4. *
  + **azureml-Train-automl-runtime**
    + Er is een Matthews-correlatie coëfficiënt toegevoegd als classificatie-metriek voor zowel binaire als multi klassen classificatie.
    + Preproces-markering van code uitdrukken en vervangen door parametrisatie-parametrisatie is standaard ingeschakeld

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK voor python v-1.0.83

+ **Nieuwe functies**
  + Gegevensset: Voeg twee opties `on_error` en `out_of_range_datetime` toe voor `to_pandas_dataframe` wanneer gegevens fout waarden bevat in plaats van ze te vullen met `None`.
  + Werk ruimte: de `hbi_workspace` markering is toegevoegd voor werk ruimten met gevoelige gegevens die verdere versleuteling mogelijk maken en geavanceerde diagnoses in werk ruimten uitschakelen. Er is ook ondersteuning toegevoegd voor het maken van uw eigen sleutels voor de bijbehorende Cosmos DB-instantie door de para meters `cmk_keyvault` en `resource_cmk_uri` op te geven bij het aanmaken van een werk ruimte, waardoor er een Cosmos DB-exemplaar in uw abonnement wordt gemaakt tijdens het inrichten van uw werk ruimte. [Meer informatie vindt u hier.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Er is een regressie opgelost waardoor een TypeError wordt geactiveerd wanneer AutoML wordt uitgevoerd op python-versies onder 3.5.4.
  + **azureml-core**
    + Er is een probleem opgelost in `datastore.upload_files` waarbij relatief pad dat niet is gestart met `./` niet kan worden gebruikt.
    + Er zijn afschaffing-berichten toegevoegd voor alle installatie kopie klasse codepaths
    + Vaste Modelbeheer URL-constructie voor Moon cake-regio.
    + Er is een probleem opgelost waarbij modellen die gebruikmaken van source_dir, niet kunnen worden verpakt voor Azure Functions.    
    + Er is een optie toegevoegd aan [environment. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) voor het pushen van een installatie kopie naar het container register voor de AzureML-werk ruimte
    + De SDK is bijgewerkt met het gebruik van nieuwe token bibliotheek op Azure Synapse op een achtergrond die daarmee compatibel is.
  + **azureml-interpreteren**
    + Er is een probleem opgelost waarbij geen fout is geretourneerd toen geen verklaringen kunnen worden gedownload. Genereert nu een uitzonde ring, overeenkomend gedrag elders.
  + **azureml-pijp lijn-stappen**
    + Het door geven van `DatasetConsumptionConfig`s aan de `inputs`-para meter van `Estimator`is niet toegestaan wanneer de `Estimator` in een `EstimatorStep`wordt gebruikt.
  + **azureml-SDK**
    + De AutoML-client is toegevoegd aan het azureml-SDK-pakket, waardoor externe AutoML-uitvoeringen worden verzonden zonder het volledige AutoML-pakket te installeren.
  + **azureml-Train-automl-client**
    + Gecorrigeerde uitlijning van console-uitvoer voor automl-uitvoeringen
    + Er is een fout opgelost waarbij een onjuiste versie van Pandas kan worden geïnstalleerd op de externe amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK voor python v-1.0.81

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-contrib-interpreteren**
    + Shap-afhankelijkheid uitstellen om te interpreteren-community van azureml-interpret
  + **azureml-core**
    + Compute target kan nu worden opgegeven als een para meter voor de bijbehorende Deployment config-objecten. Dit is in het bijzonder de naam van het reken doel om te implementeren, niet voor het SDK-object.
    + De CreatedBy-informatie is toegevoegd aan model-en service-objecten. Is mogelijk toegankelijk via <var>. created_by
    + Fixed ContainerImage. run (), die de HTTP-poort van de docker-container niet juist heeft ingesteld.
    + `azureml-dataprep` optioneel maken voor `az ml dataset register` cli-opdracht
    + Er is een fout opgelost waarbij `TabularDataset.to_pandas_dataframe` onterecht zou terugvallen op een andere lezer en een waarschuwing afdrukt.
  + **azureml-uitleg-model**
    + Shap-afhankelijkheid uitstellen om te interpreteren-community van azureml-interpret
  + **azureml-pijp lijn-kern**
    + Nieuwe stap voor de pijp lijn toegevoegd `NotebookRunnerStep`, om een lokaal notitie blok uit te voeren als een stap in de pijp lijn.
    + Afgeschafte get_all functies verwijderd voor PublishedPipelines, planningen en PipelineEndpoints
  + **azureml-Train-automl-client**
    + De afschaffing van data_script is gestart als invoer voor AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK voor python v-1.0.79

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + FeaturizationConfig verwijderd om te worden geregistreerd
      + De logboek registratie is bijgewerkt zodat alleen ' auto '/' uit '/' is aangepast '.
  + **azureml-automl-runtime**
    + Er is ondersteuning toegevoegd voor Pandas. Reeksen en Pandas. Categorische voor het detecteren van kolom gegevens type. Voorheen alleen ondersteund numpy. ndarray
      + Er zijn gerelateerde code wijzigingen toegevoegd aan de categorische-dtype op de juiste manier.
    + De functie-interface voor de Forecaster is verbeterd: de para meter y_pred is optioneel gemaakt. -De docstrings zijn verbeterd.
  + **azureml-contrib-DataSet**
    + Er is een fout opgelost waarbij het labelen van gegevens sets niet kan worden gekoppeld.
  + **azureml-core**
    + Fout oplossing voor `Environment.from_existing_conda_environment(name, conda_environment_name)`. Gebruiker kan een instantie van een omgeving maken die een exacte replica is van de lokale omgeving
    + Er zijn standaard methoden voor time series-gerelateerde gegevens sets gewijzigd in `include_boundary=True`.
  + **azureml-Train-automl-client**
    + Er is een probleem opgelost waarbij de validatie resultaten niet worden afgedrukt wanneer de uitvoer weer geven is ingesteld op ONWAAR.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK voor python v-1.0.76

+ **Wijzigingen afbreken**
  + Upgrade problemen voor Azureml-Train-AutoML
    + Upgraden naar azureml-Train-automl > = 1.0.76 van azureml-Train-automl < 1.0.76 kan gedeeltelijke installaties veroorzaken, waardoor sommige automl-import bewerkingen mislukken. U kunt dit probleem oplossen door het installatie script uit te voeren dat is gevonden op https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd. Of als u PIP direct gebruikt, kunt u het volgende doen:
      + "PIP-installatie--upgrade voor azureml-Train-automl"
      + "PIP-installatie--ignore-installed azureml-Train-automl-client"
    + of u kunt de oude versie verwijderen voordat u een upgrade uitvoert
      + "PIP verwijderen azureml-Train-automl"
      + "PIP-installatie van azureml-Train-automl"

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-runtime**
    + Voor AutoML wordt nu rekening gehouden met de klassen True en False bij het berekenen van de gemiddelde scalaire metrische gegevens voor binaire classificatie taken.
    + Geplaatste machine learning-en trainings code in AzureML-AutoML-core naar een nieuw pakket AzureML-AutoML-runtime.
  + **azureml-contrib-DataSet**
    + Wanneer u `to_pandas_dataframe` aanroept voor een gegevensset met een label met de optie downloaden, kunt u nu opgeven of bestaande bestanden moeten worden overschreven.
    + Bij het aanroepen van `keep_columns` of `drop_columns` dat resulteert in een tijds Erie-, label-of image-kolom die wordt verwijderd, worden ook de bijbehorende mogelijkheden voor de gegevensset verwijderd.
    + Er is een probleem opgelost met het laad programma pytorch voor de object detectie taak.
  + **azureml-contrib-interpreteren**
    + De object beschrijving van het uitleg Dashboard van het azureml-contrib-interpreteren, gewijzigde pakket dat verwijst naar het nieuwe item in interpret_community
    + Bijgewerkte versie van interprete-community naar 0.2.0
  + **azureml-core**
    + Verbeter de prestaties van `workspace.datasets`.
    + De mogelijkheid om Azure SQL Database gegevens opslag te registreren met behulp van gebruikers naam-en wachtwoord verificatie is toegevoegd
    + Oplossing voor het laden van RunConfigurations vanaf relatieve paden.
    + Bij het aanroepen van `keep_columns` of `drop_columns` dat resulteert in een kolom tijds Erie die wordt verwijderd, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
  + **azureml-interpreteren**
    + Bijgewerkte versie van interprete-community naar 0.2.0
  + **azureml-pijp lijn-stappen**
    + Gedocumenteerde ondersteunde waarden voor `runconfig_pipeline_params` voor Azure machine learning pijplijn stappen.
  + **azureml-pijp lijn-kern**
    + De CLI-optie is toegevoegd om de uitvoer in JSON-indeling voor pijplijn opdrachten te downloaden.
  + **azureml-Train-automl**
    + Gesplitste-Train-AutoML in twee pakketten, een client pakket AzureML-Train-AutoML-client en een ML-trainings pakket AzureML-Train-AutoML-runtime
  + **azureml-Train-automl-client**
    + Er is een thin client toegevoegd voor het verzenden van AutoML experimenten zonder dat machine learning afhankelijkheden lokaal moeten worden geïnstalleerd.
    + Vaste logboek registratie van automatisch gedetecteerde lags, grootte van het rollend venster en maximum aantal Horizons in de externe uitvoeringen.
  + **azureml-Train-automl-runtime**
    + Er is een nieuw AutoML-pakket toegevoegd om machine learning-en runtime-onderdelen van de client te isoleren.
  + **azureml-contrib-Train-RL**
    + Extra ondersteuning voor het leren van de versterking in SDK.
    + Er is ondersteuning toegevoegd voor AmlWindowsCompute in de RL-SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK voor python v-1.0.74

  + **Preview-functies**
    + **azureml-contrib-DataSet**
      + Nadat u azureml-contrib-dataset hebt geïmporteerd, kunt u `Dataset.Labeled.from_json_lines` aanroepen in plaats van `._Labeled` om een gegevensset met een label te maken.
      + Wanneer u `to_pandas_dataframe` aanroept voor een gegevensset met een label met de optie downloaden, kunt u nu opgeven of bestaande bestanden moeten worden overschreven.
      + Bij het aanroepen van `keep_columns` of `drop_columns` dat resulteert in een time series-, label-of image-kolom die wordt neergezet, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
      + Opgeloste problemen met PyTorch loader bij het aanroepen van `dataset.to_torchvision()`.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + Model Profiler toegevoegd aan de preview-versie van de CLI.
    + Opgeloste wijzigingen in Azure Storage waardoor de AzureML CLI mislukt.
    + Load Balancer type is toegevoegd aan MLC voor AKS-typen
  + **azureml-automl-core**
    + Er is een probleem opgelost met de detectie van een maximum periode van een bepaalde periode, met ontbrekende waarden en meerdere korrels.
    + Het probleem is opgelost doordat er fouten zijn opgetreden tijdens het genereren van splitsingen tussen meerdere validaties.
    + Vervang deze sectie door een bericht met een kortings indeling om te worden weer gegeven in de release opmerkingen:-verbeterde verwerking van korte korrels in de prognose van gegevens sets.
    + Het probleem met maskering van sommige gebruikers gegevens tijdens de logboek registratie is opgelost. -Verbeterde logboek registratie van de fouten tijdens de uitvoering van prognoses.
    + Psutil toevoegen als een Conda-afhankelijkheid voor het automatisch gegenereerde yml-implementatie bestand.
  + **azureml-contrib-Mir**
    + Opgeloste wijzigingen in Azure Storage waardoor de AzureML CLI mislukt.
  + **azureml-core**
    + Hiermee wordt een bug opgelost, waardoor modellen zijn geïmplementeerd op Azure Functions om 500 te produceren.
    + Er is een probleem opgelost waarbij het amlignore-bestand niet op moment opnamen werd toegepast.
    + Er is een nieuwe API-amlcompute toegevoegd. get_active_runs die een generator retourneert voor het uitvoeren en in de wachtrij plaatsen van een bepaalde amlcompute.
    + Load Balancer type is toegevoegd aan MLC voor AKS-typen.
    + Append_prefix BOOL-para meter is toegevoegd aan download_files in run.py en download_artifacts_from_prefix in artifacts_client. Deze markering wordt gebruikt om het oorspronkelijke bestandspad op te platen zodat alleen de naam van het bestand of de map wordt toegevoegd aan de output_directory
    + Oplossen van een probleem met het deserialiseren van `run_config.yml` met het gebruik van gegevensset.
    + Bij het aanroepen van `keep_columns` of `drop_columns` dat resulteert in een time series-kolom die wordt verwijderd, worden ook de bijbehorende mogelijkheden voor de gegevensset verwijderd.
  + **azureml-interpreteren**
    + Bijgewerkte interpretatie-Community-versie naar 0.1.0.3
  + **azureml-Train-automl**
    + Er is een probleem opgelost waarbij automl_step mogelijk geen validatie problemen afdrukt.
    + Er is een vaste register_model, zelfs als voor de omgeving van het model lokaal afhankelijkheden ontbreken.
    + Er is een probleem opgelost waarbij op externe uitvoeringen geen docker is ingeschakeld.
    + Voeg logboek registratie toe van de uitzonde ring die ervoor zorgt dat een lokale uitvoering voor tijdig mislukt.
  + **azureml-Train-core**
    + U kunt resume_from uitvoeren in de berekening van de best onderliggende uitvoeringen van automatische afstemming-afstemming.
  + **azureml-pijp lijn-kern**
    + Vaste parameter verwerking in pijplijn argument constructie.
    + De pijplijn beschrijving en de yaml-para meter voor het stap type zijn toegevoegd.
    + Nieuwe YAML-indeling voor pijplijn stap en toegevoegde waarschuwing voor afschaffing van oude indeling.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webervaring

De landings pagina voor de werk ruimte voor samen werking op [https://ml.azure.com](https://ml.azure.com) is verbeterd en is opnieuw ingesteld als Azure machine learning Studio (preview).

Vanuit de Studio kunt u Azure Machine Learning assets trainen, testen, implementeren en beheren, zoals gegevens sets, pijp lijnen, modellen, eind punten en meer.

Toegang tot de volgende webgebaseerde hulp middelen voor ontwerpen vanuit Studio:

| Webgebaseerd hulp programma | Beschrijving | Editie |
|-|-|-|
| VM van notebook (preview-versie) | Volledig beheerd werk station in de Cloud | Basis & Enter prise |
| [Automatische machine learning](tutorial-first-experiment-automated-ml.md) (preview-versie) | Geen code-ervaring voor het automatiseren van de ontwikkeling van machine learning modellen | Enterprise |
| [Designer](concept-designer.md) (preview-versie) | Hulp programma voor het machine learning modellen model slepen en neerzetten voorheen bekend als de ontwerp functie | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Verbeteringen in Azure Machine Learning Designer

+ Voorheen bekend als de visuele interface 
+   11 nieuwe [modules](algorithm-module-reference/module-reference.md) , waaronder aanbevolen procedures, classificaties en trainings hulpprogramma's, waaronder functie techniek, kruis validatie en gegevens transformatie.

### <a name="r-sdk"></a>R SDK 
 
Gegevens wetenschappers en AI-ontwikkel aars gebruiken de [Azure machine learning SDK voor R](tutorial-1st-r-experiment.md) om machine learning werk stromen met Azure machine learning te bouwen en uit te voeren.

De Azure Machine Learning SDK voor R maakt gebruik van het `reticulate`-pakket om een verbinding te maken met de python-SDK. Door rechtstreeks aan python te binden, kunt u met de SDK voor R toegang krijgen tot kern objecten en methoden die zijn geïmplementeerd in de python-SDK vanuit elke R-omgeving die u kiest.

De belangrijkste mogelijkheden van de SDK zijn:

+   Cloudresources voor het bewaken, loggen en organiseren van uw machine-leerexperimenten beheren.
+   Train modellen met behulp van cloud resources, waaronder GPU-versneld model training.
+   Implementeer uw modellen als webservices op Azure Container Instances (ACI) en Azure Kubernetes service (AKS).

Raadpleeg de [pakket website](https://azure.github.io/azureml-sdk-for-r) voor volledige documentatie.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integratie met Event Grid Azure Machine Learning 

Azure Machine Learning is nu een resource provider voor Event Grid, kunt u machine learning gebeurtenissen configureren via de Azure Portal of Azure CLI. Gebruikers kunnen gebeurtenissen maken voor het volt ooien van de uitvoering, model registratie, model implementatie en gegevens drift gedetecteerd. Deze gebeurtenissen kunnen worden doorgestuurd naar gebeurtenis-handlers die worden ondersteund door Event Grid voor verbruik. Zie machine learning gebeurtenis [schema](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning), [concepten](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) en [zelf studie](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) artikelen voor meer informatie.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK voor python v-1.0.72

+ **Nieuwe functies**
  + Er zijn gegevensset-monitors toegevoegd via het [**azureml-datadrift-** ](https://docs.microsoft.com/python/api/azureml-datadrift) pakket, waardoor het mogelijk is om gegevens reeksen van tijd Series te bewaken voor gegevensdrift of andere statistische wijzigingen gedurende een bepaalde periode. Waarschuwingen en gebeurtenissen kunnen worden geactiveerd als er een drift wordt gedetecteerd of als aan andere voor waarden voor de gegevens wordt voldaan. Zie [onze documentatie](https://aka.ms/datadrift) voor meer informatie.
  + Het aankondigen van twee nieuwe edities (ook wel een andere SKU genoemd) in Azure Machine Learning. Met deze versie kunt u nu een Basic-of ENTER prise Azure Machine Learning-werk ruimte maken. Alle bestaande werk ruimten worden standaard in de Basic-editie gezet en u kunt naar de Azure Portal of naar de Studio gaan om de werk ruimte op elk gewenst moment bij te werken. U kunt een Basic-of ENTER prise-werk ruimte maken op basis van de Azure Portal. Raadpleeg [onze documentatie](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) voor meer informatie. In de SDK kunt u de editie van uw werk ruimte bepalen met behulp van de eigenschap SKU van uw werkruimte object.
  + We hebben ook verbeteringen aangebracht in Azure Machine Learning Compute: u kunt nu metrische gegevens weer geven voor uw clusters (zoals het totale aantal knoop punten, actieve knoop punten, het totale kern quotum) in Azure Monitor, naast het weer geven van Diagnostische logboeken voor fout opsporing. Daarnaast kunt u nu actieve of in de wachtrij geplaatste uitvoeringen weer geven op uw cluster en Details, zoals de IP-adressen van de verschillende knoop punten in uw cluster. U kunt deze weer geven in de portal of met behulp van de bijbehorende functies in de SDK of CLI.

  + **Preview-functies**
    + We geven preview-ondersteuning uit voor schijf versleuteling van uw lokale SSD in Azure Machine Learning compute. Verhoog het technische ondersteunings ticket om uw abonnement white list te krijgen om deze functie te gebruiken.
    + Open bare preview van Azure Machine Learning batch deinterferentie. Azure Machine Learning batch-demijner doelen verkleint grote interferentie taken die niet tijd gevoelig zijn. Batch-demijnen bieden rendabele berekenings berekeningen, met een niet-parallelle door Voer voor asynchrone toepassingen. De oplossing is geoptimaliseerd voor een hoge door Voer, brand-en-vergeet om grote verzamelingen gegevens te deactiveren.
    + [**azureml-contrib-DataSet**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Ingeschakelde functionaliteiten voor gegevensset met labels
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Oplossingen en verbeteringen voor oplossingen**
  + **Azure-cli-ml**
    + CLI ondersteunt nu model pakketten.
    + De gegevensset-CLI is toegevoegd. Voor meer informatie: `az ml dataset --help`
    + Er is ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-Learn en tensor flow) zonder een InferenceConfig-exemplaar.
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow. -Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
  + **azureml-automl-core**
    + Training een herhaling wordt alleen uitgevoerd in een onderliggend proces als er runtime-beperkingen worden ingesteld.
    + Er is een guardrail toegevoegd voor prognose taken om te controleren of een opgegeven max_horizon een geheugen probleem op de desbetreffende computer veroorzaakt. Als dat het geval is, wordt er een guardrail-bericht weer gegeven.
    + Toegevoegde ondersteuning voor complexe frequenties, zoals 2 jaar en 1 maand. -Er is een begrijpelijk fout bericht toegevoegd als de frequentie niet kan worden bepaald.
    + Voeg de standaard waarden van azureml toe aan automatisch gegenereerde Conda env om het model implementatie fout op te lossen
    + Toestaan dat tussenliggende gegevens in Azure Machine Learning pijp lijn worden geconverteerd naar tabellaire gegevensset en worden gebruikt in `AutoMLStep`.
    + Geïmplementeerde update van het kolom doel voor streaming.
    + De para meter update voor de trans formatie is geïmplementeerd voor de toerekenings-en HashOneHotEncoder voor streaming.
    + De huidige gegevens grootte en de mini maal vereiste gegevens grootte zijn toegevoegd aan de validatie fout berichten.
    + De minimale vereiste gegevens grootte voor kruis validatie is bijgewerkt om mini maal twee steek proeven te garanderen in elke validatie vouw.
  + **azureml-cli-algemeen**
    + CLI ondersteunt nu model pakketten.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow.
    + Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
  + **azureml-contrib-GBDT**
    + het release kanaal voor het notitie blok is opgelost
    + Er is een waarschuwing toegevoegd voor een niet-AmlCompute Compute-doel dat niet wordt ondersteund
    + LightGMB Estimator toegevoegd aan het contrib-GBDT-pakket
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI ondersteunt nu model pakketten.
    + Waarschuwing voor afschaffing toevoegen voor afgeschafte gegevensset-Api's. Zie de wijzigings melding voor de gegevensset-API op https://aka.ms/tabular-dataset.
    + Wijzig [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) om de registratie naam en-versie te retour neren als de gegevensset is geregistreerd.
    + Een bug oplossen die ScriptRunConfig met dataset as-argument niet herhaaldelijk kan worden gebruikt om de uitvoering van het experiment te verzenden.
    + Gegevens sets die zijn opgehaald tijdens een uitvoering, worden bijgehouden en kunnen worden weer gegeven op de detail pagina voor uitvoering of door [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) aan te roepen nadat de uitvoering is voltooid.
    + Toestaan dat tussenliggende gegevens in Azure Machine Learning pijp lijn worden geconverteerd naar tabellaire gegevensset en worden gebruikt in [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep).
    + Er is ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-Learn en tensor flow) zonder een InferenceConfig-exemplaar.
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
    +  Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en tensor flow. Model registratie accepteert voorbeeld invoer gegevens, voorbeeld uitvoer gegevens en resource configuratie voor het model.
    + Het nieuwe gegevens Archief voor de Azure Database for MySQL is toegevoegd. Er is een voor beeld toegevoegd voor het gebruik van Azure Database for MySQL in DataTransferStep in Azure Machine Learning pijp lijnen.
    + Functionaliteit toegevoegd om tags toe te voegen aan of te verwijderen uit de functionaliteit voor het toevoegen van experimenten om tags uit uitvoeringen te verwijderen
    + Overschrijvings vlag toegevoegd voor service-Implementatie (ACI en AKS) in SDK en CLI. Als dit wordt gegeven, wordt de bestaande service overschreven als er al een service met de naam bestaat. Als de service niet bestaat, wordt er een nieuwe service gemaakt.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Verplaatst van `azureml-contrib-datadrift` naar `azureml-datadrift`
    + Er is ondersteuning toegevoegd voor het bewaken van Time Series-gegevens sets voor drift en andere statistische metingen
    + Nieuwe methoden `create_from_model()` en `create_from_dataset()` aan de [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) -klasse. De `create()` methode wordt afgeschaft.
    + Aanpassingen van de visualisaties in Python en gebruikers interface in de Azure Machine Learning Studio.
    + Ondersteuning voor wekelijkse en maandelijkse monitor planning, naast dagelijks voor gegevensset-monitors.
    + Ondersteuning voor backfill voor het analyseren van historische gegevens voor gegevensset-monitors.
    + Verschillende oplossingen voor oplossingen
  + [**azureml-pijp lijn-kern**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep is niet meer nodig om een Azure Machine Learning pijplijn uit te voeren vanuit het `yaml` bestand van de pijp lijn.
  + [**azureml-Train-automl**](/python/api/azureml-train-automl-runtime/)
    + Voeg de standaard waarden van azureml toe aan automatisch gegenereerde Conda env om het model implementatie fout op te lossen
    + AutoML remote training bevat nu de standaard waarden van azureml om hergebruik van training env voor te stellen.
  + **azureml-Train-core**
    + PyTorch 1,3-ondersteuning is toegevoegd in [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimator

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuele interface (preview-versie)

+ De Azure Machine Learning Visual Interface (preview) is ingezien om te worden uitgevoerd op [Azure machine learning pijp lijnen](concept-ml-pipelines.md). Pijp lijnen (voorheen bekend als experimenten) die zijn gemaakt in de visuele interface, zijn nu volledig geïntegreerd met de kern Azure Machine Learning-ervaring.
  + Unified management-ervaring met SDK-assets
  + Versie beheer en het bijhouden van visuele interface modellen, pijp lijnen en eind punten
  + Nieuwe gebruikers interface
  + Implementatie voor batch-demijnen is toegevoegd
  + De ondersteuning voor Azure Kubernetes service (AKS) is toegevoegd voor de doel stellingen voor de inschiet kracht
  + Nieuwe werk stroom voor het ontwerpen van een python-stap
  + Nieuwe [landings pagina](https://ml.azure.com) voor visuele hulpprogram ma's voor ontwerpen

+ **Nieuwe modules**
  + Wiskundige bewerking Toep assen
  + SQL-trans formatie Toep assen
  + Clip waarden
  + Gegevens samenvatten
  + Importeren uit SQL database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK voor python v-1.0.69

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Het beperken van de model verklaringen voor de beste uitvoering in plaats van het berekenen van de uitleg voor elke uitvoering. U kunt dit gedrag wijzigen voor lokale, externe en ADB.
    + Er is ondersteuning toegevoegd voor de uitleg van het on-demand-model voor de gebruikers interface
    + Psutil is toegevoegd als een afhankelijkheid van `automl` en bevat psutil als een Conda-afhankelijkheid in amlcompute.
    + Het probleem met heuristische lags-en Rolling venster grootten is opgelost in de Voorspellings gegevens sets waarvan sommige reeksen een lineaire algebra fouten kunnen veroorzaken
      + Er is afdrukken toegevoegd voor de heuristisch verbepaald para meters in de prognoses worden uitgevoerd.
  + **azureml-contrib-datadrift**
    + Er is beveiliging toegevoegd tijdens het maken van metrische gegevens voor uitvoer als DataSet level drift zich niet in de eerste sectie bevindt.
  + **azureml-contrib-interpreteren**
    + de naam van het pakket voor azureml-contrib-uitleg-model is gewijzigd in azureml-contrib-interpreteren
  + **azureml-core**
    + API is toegevoegd om de registratie van gegevens sets op te heffen. `dataset.unregister_all_versions()`
    + de naam van het pakket voor azureml-contrib-uitleg-model is gewijzigd in azureml-contrib-interpreteren.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + API is toegevoegd om de registratie van gegevens sets op te heffen. sets. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + De gegevensset-API is toegevoegd om de gewijzigde tijd van de gegevens te controleren. `dataset.data_changed_time`.
    + `FileDataset` en `TabularDataset` als invoer voor `PythonScriptStep`, `EstimatorStep`en `HyperDriveStep` in Azure Machine Learning pijp lijn kunnen worden gebruikt
    + De prestaties van `FileDataset.mount` zijn verbeterd voor mappen met een groot aantal bestanden
    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) en [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) kunnen als invoer worden gebruikt voor [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)en [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in de Azure machine learning-pijp lijn.
    + Prestaties van FileDataset. [Mount ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none-) is verbeterd voor mappen met een groot aantal bestanden
    + De URL is toegevoegd aan bekende fout aanbevelingen in de details van de uitvoering.
    + Er is een fout opgetreden tijdens de uitvoering. get_metrics waar aanvragen zouden mislukken als een uitvoering te veel onderliggende items heeft
    + Er is een fout opgetreden tijdens de [uitvoering. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) waar aanvragen zouden mislukken als een uitvoering te veel onderliggende items heeft
    + Er is ondersteuning toegevoegd voor verificatie op het Arcadia-cluster.
    + Als u een experiment object maakt, wordt het experiment in de Azure Machine Learning-werk ruimte gemaakt, zodat het bijhouden van de uitvoerings geschiedenis kan worden uitgevoerd. Het experiment-ID en de gearchiveerde tijd worden gevuld in het object experiment bij het maken. Voor beeld: experiment = experiment (werk ruimte, "nieuw experiment") experiment_id = experiment.id Archive () en reactivate () zijn functies die kunnen worden aangeroepen in een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weer gegeven in de UX of standaard wordt geretourneerd in een gesprek om experimenten weer te geven. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voor beeld: experiment1 = experiment (werk ruimte, "actief experiment") experiment1. Archive () # nieuw actief experiment maken met dezelfde naam als de gearchiveerde. experiment2. = Experiment (werk ruimte, "actief experiment") experiment1. reactivate (new_name = "vorig actief experiment") de lijst met statische methoden () op het experiment kan een naam filter en een View type-filter hebben. View type-waarden zijn "ACTIVE_ONLY", "ARCHIVED_ONLY" en "alle" voor beeld: archived_experiments = experiment. List (workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = experiment. List (werk ruimte, naam = "eerste experiment", view_type = "ALL")
    + Ondersteuning voor het gebruik van een omgeving voor model implementatie en service-update
  + **azureml-datadrift**
    + Het kenmerk show van de klasse DataDriftDector ondersteunt geen optioneel argument with_details meer. Bij het kenmerk show wordt alleen de bijdrage van de functie kolommen gegevens drift en gegevens drift weer gegeven.
    + Gedrag van het DataDriftDetector-kenmerk ' get_output ' wordt gewijzigd:
      + Invoer parameter start_time, end_time optioneel zijn in plaats van verplicht.
      + De invoer van specifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroepen leidt ertoe dat er een fout uitzonde ring optreedt omdat deze elkaar wederzijds uitsluiten
      + Als er specifieke start_time en/of end_time worden opgegeven, worden alleen de resultaten van geplande uitvoeringen geretourneerd.
      + De para meter daily_latest_only is afgeschaft.
    + Ondersteuning bij het ophalen van op gegevensset gebaseerde gegevens-drift-uitvoer.
  + **azureml-uitleg-model**
    + De naam van het pakket voor AzureML-uitleggen-model wordt gewijzigd in AzureML-interpreteren, waarbij het oude pakket wordt gehandhaafd voor achterwaartse compatibiliteit voor nu
    + vaste `automl` bug met onbewerkte uitleg ingesteld op classificatie taak in plaats van regressie standaard bij downloaden vanuit ExplanationClient
    + Voeg ondersteuning toe voor `ScoringExplainer` die rechtstreeks kunnen worden gemaakt met behulp van `MimicWrapper`
  + **azureml-pijp lijn-kern**
    + Verbeterde prestaties voor het maken van een grote pijp lijn
  + **azureml-Train-core**
    + Tensor Flow 2,0-ondersteuning is toegevoegd in tensor flow estimator
  + **azureml-Train-automl**
    + Als u een [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) object maakt, wordt het experiment in de Azure machine learning-werk ruimte gemaakt, zodat het bijhouden van de uitvoerings geschiedenis kan worden uitgevoerd. Het experiment-ID en de gearchiveerde tijd worden gevuld in het object experiment bij het maken. Voorbeeld:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Archive ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) en [reactivate ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen, zodat het niet wordt weer gegeven in de UX of standaard wordt geretourneerd in een aanroep naar lijst experimenten. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, kunt u de naam van het gearchiveerde experiment wijzigen door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voorbeeld:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        De [lijst met statische methoden ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) in experiment kan een naam filter en een View type-filter hebben. View type-waarden zijn ' ACTIVE_ONLY ', ' ARCHIVED_ONLY ' en ' ALL '. Voorbeeld:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Ondersteuning voor het gebruik van een omgeving voor model implementatie en service-update.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Het kenmerk show van de klasse [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) ondersteunt geen optioneel argument with_details meer. Bij het kenmerk show wordt alleen de bijdrage van de functie kolommen gegevens drift en gegevens drift weer gegeven.
    + DataDriftDetector-functie [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) gedrags wijzigingen:
      + Invoer parameter start_time, end_time optioneel zijn in plaats van verplicht.
      + De invoer van specifieke start_time en/of end_time met een specifieke run_id in hetzelfde aanroepen leidt ertoe dat er een fout uitzonde ring optreedt omdat deze elkaar wederzijds uitsluiten.
      + Als er specifieke start_time en/of end_time worden opgegeven, worden alleen de resultaten van geplande uitvoeringen geretourneerd.
      + De para meter daily_latest_only is afgeschaft.
    + Ondersteuning bij het ophalen van op gegevensset gebaseerde gegevens-drift-uitvoer.
  + **[azureml-uitleg-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + De naam van het pakket voor AzureML-uitleggen-model wordt gewijzigd in AzureML-interpreteren, waarbij het oude pakket wordt gehandhaafd voor achterwaartse compatibiliteit.
    + Er is een vaste AutoML-fout opgesteld met onbewerkte uitleg, ingesteld op classificatie taak in plaats van regressie, standaard bij het downloaden van ExplanationClient.
    + Voeg ondersteuning toe voor [ScoringExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.scoring.scoring_explainer.scoringexplainer) die rechtstreeks worden gemaakt met behulp van [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pijp lijn-kern](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Verbeterde prestaties voor het maken van grote pijp lijnen.
  + **[azureml-Train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Tensor Flow 2,0-ondersteuning is toegevoegd in [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + De bovenliggende uitvoering wordt niet meer uitgevoerd wanneer de installatie herhaling is mislukt, omdat deze al wordt gebruikt door de indeling.
    + Lokale-docker-en Local-Conda-ondersteuning toegevoegd voor AutoML-experimenten
    + Lokale-docker-en Local-Conda-ondersteuning toegevoegd voor AutoML experimenten.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview-versie) voor Azure Machine Learning-werk ruimten

Het tabblad experiment in de [nieuwe werkruimte Portal](https://ml.azure.com) is bijgewerkt, zodat gegevens wetenschappers op een effectievere manier experimenten kunnen bewaken. U kunt de volgende functies verkennen:
+ Experimenteer met de meta gegevens om uw lijst met experimenten eenvoudig te filteren en te sorteren
+ Vereenvoudigde en uitvoerende Details pagina's voor het experiment waarmee u uw uitvoeringen kunt visualiseren en vergelijken
+ Nieuw ontwerp voor het uitvoeren van informatie pagina's om uw trainings uitvoeringen te begrijpen en te controleren

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK voor python v-1.0.65

  + **Nieuwe functies**
    + Er zijn gecuratorde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning taken en zijn vooraf gemaakt en in cache geplaatst als docker-installatie kopieën voor een snellere uitvoering. Ze worden standaard weer gegeven in de lijst met omgevingen van de werk ruimte, met het voor voegsel ' AzureML '.
    + Er zijn gecuratorde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor algemene machine learning taken en zijn vooraf gemaakt en in cache geplaatst als docker-installatie kopieën voor een snellere uitvoering. Ze worden standaard weer gegeven in de lijst met omgevingen van de [werk ruimte](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29), met het voor voegsel ' AzureML '.

  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + De ondersteuning voor ONNX-conversie is toegevoegd voor de ADB en HDI

+ **Preview-functies**
  + **azureml-Train-automl**
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde BERT en BiLSTM als tekst featurizer (alleen preview-versie)
    + Ondersteunde parametrisatie aanpassing voor de para meters voor het kolom doel en de transformator (alleen preview-versie)
    + Ondersteunde onbewerkte uitleg wanneer de gebruiker tijdens de training model uitleg maakt (alleen preview-versie)
    + Prophet voor `timeseries`-prognose toegevoegd als een trained pipeline (alleen preview-versie)

  + **azureml-contrib-datadrift**
    + Pakketten die zijn verplaatst van azureml-contrib-datadrift naar azureml-datadrift; het `contrib`-pakket wordt in een toekomstige versie verwijderd

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + FeaturizationConfig geïntroduceerd op AutoMLConfig en AutoMLBaseSettings
    + FeaturizationConfig geïntroduceerd op [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) en AutoMLBaseSettings
      + Kolom doel negeren voor parametrisatie met de opgegeven kolom en het functie type
      + Para meters voor transformator overschrijven
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ()
    + Prophet toegevoegd als een trained-pijp lijn (alleen preview-versie)
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ().
    + Prophet toegevoegd als een trained-pijp lijn (alleen preview-versie).
    + Er is ondersteuning toegevoegd voor automatische detectie van de doel-lags, de grootte van het rollen venster en de maximale waarde. Als een van target_lags, target_rolling_window_size of max_horizon is ingesteld op ' auto ', worden de heuristiek toegepast om de waarde van de overeenkomstige para meter te schatten op basis van de trainings gegevens.
    + Als er sprake is van een vaste prognose in het geval wanneer gegevensset één kolom korrel bevat, is deze korrel van een numeriek type en is er een hiaat tussen de trein-en testset
    + Het fout bericht over de gedupliceerde index in de externe uitvoering in prognose taken is opgelost
    + Als er sprake is van een vaste prognose in het geval dat de gegevensset één kolom bevat, is deze korrel van een numeriek type en is er een hiaat tussen de trein-en testset.
    + Het fout bericht over de gedupliceerde index in de externe uitvoering in prognose taken is opgelost.
    + Er is een guardrail toegevoegd om te controleren of een gegevensset niet is gebalanceerd of niet. Als dat het geval is, wordt er een guardrail-bericht naar de-console geschreven.
  + **azureml-core**
    + De mogelijkheid om SAS-URL op te halen in het model-object wordt toegevoegd aan de opslag. Bijvoorbeeld: model. get_sas_url ()
    + `run.get_details()['datasets']` introduceren voor het ophalen van gegevens sets die zijn gekoppeld aan de ingediende uitvoering
    + Voeg API-`Dataset.Tabular.from_json_lines_files` toe om een TabularDataset te maken op basis van JSON-lijn bestanden. Ga voor meer informatie over deze tabellaire gegevens in JSON-regel bestanden op TabularDataset naar https://aka.ms/azureml-data voor documentatie.
    + Er zijn extra VM-grootte velden (besturingssysteem schijf, aantal Gpu's) toegevoegd aan de functie supported_vmsizes ()
    + Er zijn extra velden toegevoegd aan de functie list_nodes () om de uitvoering, het persoonlijke en het open bare IP-adres, de poort, enzovoort weer te geven.
    + De mogelijkheid om tijdens het inrichten van het cluster een nieuw veld op te geven: remotelogin_port_public_access dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment van het maken van het cluster. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van het feit of u het cluster in een VNet implementeert.
  + **azureml-uitleg-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + De mogelijkheid om SAS-URL op te halen in het model-object wordt toegevoegd aan de opslag. Ex: model. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Voer uit om uit te voeren. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' gegevens sets '] ophalen van gegevens sets die zijn gekoppeld aan de ingediende uitvoering
    + Voeg API-`Dataset.Tabular`toe. [from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) om een TabularDataset te maken op basis van JSON-lijn bestanden. Ga voor meer informatie over deze tabellaire gegevens in JSON-regel bestanden op TabularDataset naar https://aka.ms/azureml-data voor documentatie.
    + Er zijn extra VM-grootte velden (besturingssysteem schijf, aantal Gpu's) toegevoegd aan de functie [supported_vmsizes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Er zijn extra velden toegevoegd aan de functie [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) om de uitvoering, het persoonlijke en het open bare IP-adres, de poort, enzovoort weer te geven.
    + De mogelijkheid om een nieuw veld op te geven tijdens het [inrichten](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) van een cluster `--remotelogin_port_public_access` dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment van het maken van het cluster. Als u deze niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van het feit of u het cluster in een VNet implementeert.
  + **[azureml-uitleg-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Verbeterde documentatie voor uitleg over uitvoer in het classificatie scenario.
    + De mogelijkheid voor het uploaden van de voorspelde y-waarden is toegevoegd voor de uitleg voor de evaluatie voorbeelden. Hiermee ontgrendelt u nuttige visualisaties.
    + Er is een uitleg eigenschap toegevoegd aan MimicWrapper om het ophalen van de onderliggende MimicExplainer in te scha kelen.
  + **azureml-pijp lijn-kern**
    + Er is een notitie blok toegevoegd om module, ModuleVersion en ModuleStep te beschrijven
  + **azureml-pijp lijn-stappen**
    + RScriptStep toegevoegd ter ondersteuning van het uitvoeren van R-script via AML-pijp lijn.
    + Er zijn geen vaste meta data-para meters voor het parseren in AzureBatchStep die het fout bericht ' toewijzing voor para meter SubscriptionId ' heeft veroorzaakt, niet opgegeven.
  + **azureml-Train-automl**
    + Ondersteund training_data, validation_data, label_column_name, weight_column_name als invoer indeling voor gegevens
    + Er is een afschaffing bericht toegevoegd voor explain_model () en retrieve_model_explanations ()
  + **[azureml-pijp lijn-kern](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Er is een [notitie blok](https://aka.ms/pl-modulestep) toegevoegd voor het beschrijven van [module](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) en [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pijp lijn-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) toegevoegd ter ondersteuning van het uitvoeren van R-script via AML-pijp lijn.
    + Er zijn geen vaste meta data-para meters voor het parseren in [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) die het fout bericht ' toewijzing voor para meter SubscriptionId ' heeft veroorzaakt, niet opgegeven.
  + **[azureml-Train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteunde training_data, validation_data, label_column_name, weight_column_name als invoer indeling voor gegevens.
    + Er is een afschaffing bericht toegevoegd voor [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) en [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK voor python v-1.0.62

+ **Nieuwe functies**
  + Heeft de `timeseries` eigenschappen geïntroduceerd op TabularDataset. Met deze eigenschappen kunt u eenvoudig tijds tempel filteren op gegevens een TabularDataset, zoals het nemen van alle gegevens tussen een tijd bereik of de meest recente gegevens. Ga voor meer informatie over deze `timeseries` eigenschappen op TabularDataset naar https://aka.ms/azureml-data voor documentatie of https://aka.ms/azureml-tsd-notebook voor een voor beeld van een notebook.
  + Ingeschakelde training met TabularDataset en FileDataset. Ga naar https://aka.ms/dataset-tutorial voor een voor beeld van een notebook.

  + **azureml-Train-core**
    + `Nccl` en `Gloo`-ondersteuning toegevoegd in PyTorch estimator

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + De AutoML-instelling lag_length en de LaggingTransformer is afgeschaft.
    + De juiste validatie van invoer gegevens is hersteld als deze zijn opgegeven in een gegevensstroom indeling
    + De fit_pipeline. py is gewijzigd om de JSON van de grafiek te genereren en te uploaden naar artefacten.
    + De grafiek wordt weer gegeven onder `userrun` met behulp van `Cytoscape`.
  + **azureml-core**
    + Gestart de verwerking van uitzonde ringen in de ADB-code op en breng wijzigingen aan op basis van de nieuwe fout afhandeling
    + Automatische MSI-verificatie is toegevoegd voor VM-Vm's.
    + Hiermee wordt een bug opgelost waarbij beschadigde of lege modellen kunnen worden geüpload vanwege mislukte pogingen.
    + Er is een fout opgelost waarbij `DataReference` naam verandert wanneer de `DataReference` modus verandert (bijvoorbeeld bij het aanroepen van `as_upload`, `as_download`of `as_mount`).
    + Maak `mount_point` en `target_path` optioneel voor `FileDataset.mount` en `FileDataset.download`.
    + Uitzonde ring dat de time stamp-kolom niet kan worden gevonden, wordt gegenereerd als de gerelateerde time-out van de API wordt aangeroepen zonder dat de toegewezen time stamp-kolommen worden verwijderd.
    + Tijdgebonden kolommen moeten worden toegewezen aan een kolom waarvan het type datum is, anders wordt er een uitzonde ring verwacht
    + Tijdgebonden kolommen voor het toewijzen van de API ' with_timestamp_columns ' kunnen geen waarde krijgen de kolom naam van de verfijnd/ruwe Time Stamp, waardoor eerder toegewezen time stamp-kolommen worden gewist.
    + Er wordt een uitzonde ring gegenereerd wanneer een ruwe korrel of een verfijnde time stamp-kolom wordt verwijderd met de melding dat de gebruiker die het object verwijdert, kan worden uitgevoerd na een time stamp-kolom in de lijst of with_time_stamp met geen waarde voor release-time stamp kolommen
    + Er wordt een uitzonde ring gegenereerd wanneer een ruwe korrel of een verfijnde time stamp-kolom niet is opgenomen in de lijst kolommen behouden, met vermelding van de gebruiker die kan worden uitgevoerd na de time stamp-kolom in de kolom lijst behouden of de aanroep with_time_stamp met geen de waarde voor het vrijgeven van Time Stamp-kolommen.
    + Logboek registratie toegevoegd voor de grootte van een geregistreerd model.
  + **azureml-uitleg-model**
    + Er is een vaste waarschuwing afgedrukt op de console wanneer het python-pakket ' Pack ' niet is geïnstalleerd: ' ouder dan de ondersteunde versie van lightgbm, een upgrade uitvoeren naar een versie die groter is dan 2.2.1 '
    + Verklaring van een vast Download model met sharding voor globale uitleg met veel functies
    + Er ontbreken voor beelden van de initialisatie van een vaste naimiteerer voor de uitvoer uitleg
    + Vast onveranderbare fout bij het instellen van eigenschappen bij het uploaden met een uitleg-client met behulp van twee verschillende typen modellen
    + Er is een get_raw para meter toegevoegd aan scoreer uitleg. Leg uit dat een beoordelings uitleg zowel de gestuurde als de onbewerkte waarden kan retour neren.
  + **azureml-Train-automl**
    + Geïntroduceerde open bare Api's van AutoML voor het ondersteunen van uitleg van `automl` uitleg over SDK: een nieuwere manier van ondersteuning van AutoML-uitleg door ontkoppeling van AutoML parametrisatie en uitleg over door SDK geïntegreerde onbewerkte uitleg van de SDK voor AutoML modellen.
    + Het verwijderen van azureml-standaard waarden van een externe trainings omgeving.
    + De standaard locatie van het cache archief is gewijzigd van file cache Store op basis van een AzureFileCacheStore voor AutoML op Azure Databricks codepad.
    + De juiste validatie van invoer gegevens is hersteld als deze zijn opgegeven in een gegevensstroom indeling
  + **azureml-Train-core**
    + Geannuleerd source_directory_data_store afschaffing.
    + De mogelijkheid om geïnstalleerde pakket versies van azureml te negeren is toegevoegd.
    + Er is ondersteuning toegevoegd voor dockerfile in `environment_definition` para meter in schattingen.
    + Vereenvoudigde gedistribueerde trainings parameters in schattingen.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview-versie) voor Azure Machine Learning-werk ruimten
Met de nieuwe webervaring kunnen gegevens wetenschappers en gegevens technici hun end-to-end machine learning levenscyclus volt ooien van bereidt en gegevens visualiseren om modellen op één locatie te trainen en te implementeren.

![Gebruikers interface van Azure Machine Learning werk ruimte (preview)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Belangrijkste functies:**

Met deze nieuwe Azure Machine Learning interface kunt u nu het volgende doen:
+ Uw notitie blokken beheren of een koppeling naar Jupyter
+ [Automatische ML experimenten uitvoeren](tutorial-first-experiment-automated-ml.md)
+ [Gegevens sets maken op basis van lokale bestanden, gegevens opslag, &-Webbe standen](how-to-create-register-datasets.md)
+ Gegevens sets verkennen & voorbereiden voor het maken van modellen
+ Gegevens drift voor uw modellen bewaken
+ Recente resources van een dash board weer geven

Op het moment van deze release worden de volgende browsers ondersteund: Chrome, Firefox, Safari en micro soft Edge Preview.

**Bekende problemen:**

1. Vernieuw uw browser als u ziet dat er iets mis is gegaan. Fout bij het laden van segment bestanden wanneer de implementatie wordt uitgevoerd.

1. Kan het bestand in notitie blokken en bestanden niet verwijderen of een andere naam geven. Tijdens de open bare preview kunt u de Jupyter-gebruikers interface of-terminal in de notebook-VM gebruiken om update Bestands bewerkingen uit te voeren. Omdat het een gekoppeld netwerk bestands systeem is, worden alle wijzigingen die u aanbrengt op de VM van het notebook direct weer gegeven in de werk ruimte notebook.

1. SSH in de VM van het notebook:
   1. De SSH-sleutels zoeken die zijn gemaakt tijdens de installatie van de virtuele machine. U kunt ook de sleutels in het Azure Machine Learning werk ruimte vinden > tabblad Compute openen > zoek naar de VM van het notebook in de lijst > de eigenschappen ervan te openen: Kopieer de sleutels uit het dialoog venster.
   1. Importeer deze open bare en persoonlijke SSH-sleutels op uw lokale machine.
   1. Gebruik ze om te SSHen in de VM van het notebook.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK voor python v-1.0.60

+ **Nieuwe functies**
  + Geïntroduceerde FileDataset, die verwijst naar één of meer bestanden in uw gegevens opslag of open bare url's. De bestanden kunnen elk een wille keurige indeling hebben. FileDataset biedt u de mogelijkheid om de bestanden te downloaden of te koppelen aan uw compute. Ga voor meer informatie over FileDataset naar https://aka.ms/file-dataset.
  + Ondersteuning voor pijplijn yaml toegevoegd voor PythonScript stap, Adla stap, Databricks stap, DataTransferStep en AzureBatch stap

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + AutoArima is nu een suggestie voor een voor beeld van een pijp lijn.
    + Verbeterde fout rapportage voor prognoses.
    + De logboek registratie is verbeterd door aangepaste uitzonde ringen te gebruiken in plaats van algemeen in de prognose taken.
    + De controle op max_concurrent_iterations is verwijderd zodat deze kleiner is dan het totale aantal herhalingen.
    + AutoML-modellen retour neren nu AutoMLExceptions
    + Met deze release worden de uitvoerings prestaties van automatische machine learning lokale uitvoeringen verbeterd.
  + **azureml-core**
    + Geef DataSet. get_all (werk ruimte) op die een woorden lijst van `TabularDataset` en `FileDataset` objecten retourneert op basis van hun registratie naam.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + `parition_format` als argument voor `Dataset.Tabular.from_delimited_files` en `Dataset.Tabular.from_parquet.files`introduceren. De partitie gegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name} maakt een teken reeks kolom, en {column_name: JJJJ/MM/DD/uu/mm/SS} maakt de kolom datetime, waarbij ' jjjj ', ' MM ', ' DD ', ' HH ', ' mm ' en ' ss ' worden gebruikt voor het uitpakken van jaar, maand, dag, uur, minuut en seconde voor het type datum/tijd. Het partition_format moet beginnen met de positie van de eerste partitie sleutel tot het einde van het bestandspad. Bijvoorbeeld, op basis van het pad '.. /USA/2019/01/01/data.csv ' waarbij de partitie op land en tijd staat, partition_format = '/{Country}/{PartitionDate: JJJJ/MM/DD}/data. csv ' wordt de teken reeks kolom ' Country ' met de waarde ' USA ' en de datetime-kolom ' PartitionDate ' met de waarde ' 2019-01-01 '.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + `partition_format` als argument voor `Dataset.Tabular.from_delimited_files` en `Dataset.Tabular.from_parquet.files`introduceren. De partitie gegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. {column_name} maakt een teken reeks kolom, en {column_name: JJJJ/MM/DD/uu/mm/SS} maakt de kolom datetime, waarbij ' jjjj ', ' MM ', ' DD ', ' HH ', ' mm ' en ' ss ' worden gebruikt voor het uitpakken van jaar, maand, dag, uur, minuut en seconde voor het type datum/tijd. Het partition_format moet beginnen met de positie van de eerste partitie sleutel tot het einde van het bestandspad. Bijvoorbeeld, op basis van het pad '.. /USA/2019/01/01/data.csv ' waarbij de partitie op land en tijd staat, partition_format = '/{Country}/{PartitionDate: JJJJ/MM/DD}/data. csv ' wordt de teken reeks kolom ' Country ' met de waarde ' USA ' en de datetime-kolom ' PartitionDate ' met de waarde ' 2019-01-01 '.
    + `to_csv_files`-en `to_parquet_files`-methoden zijn toegevoegd aan `TabularDataset`. Met deze methoden wordt de conversie tussen een `TabularDataset` en een `FileDataset` ingeschakeld door de gegevens te converteren naar bestanden met de opgegeven indeling.
    + Automatisch aanmelden bij het REGI ster van de basis installatie kopie bij het opslaan van een Dockerfile die is gegenereerd door model. pakket ().
    + ' gpu_support ' is niet langer nodig; Met AML wordt nu automatisch de NVIDIA docker-extensie gedetecteerd en gebruikt wanneer deze beschikbaar is. Deze wordt in een toekomstige release verwijderd.
    + Er is ondersteuning toegevoegd om PipelineDrafts te maken, bij te werken en te gebruiken.
    + Met deze release worden de uitvoerings prestaties van automatische machine learning lokale uitvoeringen verbeterd.
    + Gebruikers kunnen metrische gegevens opvragen uit de uitvoerings geschiedenis op naam.
    + De logboek registratie is verbeterd door aangepaste uitzonde ringen te gebruiken in plaats van algemeen in de prognose taken.
  + **azureml-uitleg-model**
    + Feature_maps para meter toegevoegd aan de nieuwe MimicWrapper, zodat gebruikers de onbewerkte functie verklaringen kunnen ophalen.
    + Gegevensset-uploads zijn nu standaard uitgeschakeld voor het uploaden van uitleg en kunnen opnieuw worden ingeschakeld met upload_datasets = True
    + De para meters voor ' is_law ' zijn toegevoegd aan de uitleg lijst en de functies worden gedownload.
    + Voegt methode `get_raw_explanation(feature_maps)` toe aan zowel globale als lokale uitleg objecten.
    + Versie controle toegevoegd aan lightgbm met afgedrukte waarschuwing als onderstaande ondersteunde versie
    + Geoptimaliseerd geheugen gebruik bij de batch verwerking van uitleg
    + AutoML-modellen retour neren nu AutoMLExceptions
  + **azureml-pijp lijn-kern**
    + Er is ondersteuning toegevoegd om PipelineDrafts te maken, bij te werken en te gebruiken-kan worden gebruikt om onveranderlijke pijplijn definities te onderhouden en ze interactief te gebruiken om uit te voeren
  + **azureml-Train-automl**
    + Er is een functie gemaakt voor het installeren van specifieke versies van GPU-capable pytorch v 1.1.0, :::no-loc text="cuda"::: Toolkit 9,0, pytorch-trans formaties, die vereist zijn om BERT/XLNet in te scha kelen in de externe python-runtime omgeving.
  + **azureml-Train-core**
    + Vroegtijdige uitval van sommige afstemming definitie fouten rechtstreeks in de SDK in plaats van aan de server zijde.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning data prep SDK v 1.1.14
+ **Oplossingen en verbeteringen voor oplossingen**
  + Schrijven naar ADLS/ADLSGen2 is ingeschakeld met onbewerkte paden en referenties.
  + Er is een fout opgelost waardoor `include_path=True` niet werkt voor `read_parquet`.
  + De vaste `to_pandas_dataframe()`-fout is veroorzaakt door een uitzonde ring, ongeldige eigenschaps waarde: hostSecret.
  + Er is een fout opgelost waarbij bestanden niet kunnen worden gelezen op DBFS in de modus Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK voor python v-1.0.57
+ **Nieuwe functies**
  + `TabularDataset` ingeschakeld om te worden gebruikt door AutomatedML. Ga voor meer informatie over `TabularDataset`naar https://aka.ms/azureml/howto/createdatasets.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **automl-client-core-nativeclient**
    + Er is een fout opgelost die optreedt wanneer training en/of validerings labels (y en y_valid) zijn opgenomen in de vorm van Pandas data frame, maar niet als numpy-matrix.
    + Bijgewerkte interface voor het maken van een `RawDataContext`, zodat alleen de gegevens en het `AutoMLBaseSettings` object nodig zijn.
    +  Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen. -Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komt in de Trainingsset tijdens het maken van prognoses.
  + **Azure-cli-ml**
    + U kunt nu het SSL-certificaat voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster bijwerken voor zowel het micro soft-certificaat dat is gegenereerd door een klant.
  + **azureml-automl-core**
    + Er is een probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet correct zijn verwijderd.
    + Verbeterde logboek registratie van fouten in AutoML; Er worden nu altijd volledige fout berichten naar het logboek bestand geschreven.
    + AutoML heeft het vastmaken van het pakket bijgewerkt zodat het `azureml-defaults`, `azureml-explain-model`en `azureml-dataprep`bevat. AutoML waarschuwt niet meer bij niet-overeenkomende pakket (met uitzonde ring van `azureml-train-automl`-pakket).
    + Er is een probleem opgelost in `timeseries` waarbij CV-splitsingen een ongelijke grootte hebben waardoor de berekening van de opslag locatie mislukt.
    + Bij het uitvoeren van een ensemble-iteratie voor het multi validatie type training, als we de modellen die zijn getraind op de hele gegevensset niet kunnen downloaden, hebben we een inconsistentie aangetroffen tussen de model gewichten en de modellen die in de stemming werden ingevoerd dienen.
    + Er is een fout opgelost die optreedt wanneer training en/of validerings labels (y en y_valid) zijn opgenomen in de vorm van Pandas data frame, maar niet als numpy-matrix.
    + Het probleem is opgelost met de prognose taken wanneer er geen is aangetroffen in de Boole-kolommen van de invoer tabellen.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen. -Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komt in de Trainingsset tijdens het maken van prognoses.
  + **azureml-core**
    + Probleem opgelost met de volg orde van blob_cache_timeout-para meters.
    + De uitzonderings typen voor externe aanpassing en trans formatie zijn toegevoegd aan systeem fouten.
    + Er is ondersteuning toegevoegd voor Key Vault geheimen voor externe uitvoeringen. Voeg een azureml. core. class kluis. klasse-kluis toe om geheimen toe te voegen, op te halen en weer te geven van de sleutel kluis die is gekoppeld aan uw werk ruimte. Ondersteunde bewerkingen zijn:
      + azureml. core. workspace. workspace. get_default_keyvault ()
      + azureml. core. de sleutel kluis. sleutel kluis. set_secret (naam, waarde)
      + azureml. core. de sleutel kluis. de sleutel kluis. set_secrets (secrets_dict)
      + azureml. core. de sleutel kluis. sleutel kluis. get_secret (naam)
      + azureml. core. de sleutel kluis. de sleutel kluis. get_secrets (secrets_list)
      + azureml. core. de sleutel kluis. de sleutel kluis. list_secrets ()
    + Aanvullende methoden voor het verkrijgen van de standaard-sleutel kluis en het ophalen van geheimen tijdens externe uitvoering:
      + azureml. core. workspace. workspace. get_default_keyvault ()
      + azureml. core. run. run. get_secret (naam)
      + azureml. core. run. run. get_secrets (secrets_list)
    + Er zijn extra onderdrukkings parameters toegevoegd voor verzenden-Hyperdrive CLI-opdracht.
    + Verbeter de betrouw baarheid van API-aanroepen om nieuwe pogingen uit te breiden naar algemene aanvragen bibliotheek uitzonderingen.
    + Voeg ondersteuning toe voor het verzenden van uitvoeringen vanuit een ingediende uitvoering.
    + Probleem met een vast verlopen SAS-token in FileWatcher, waardoor bestanden niet meer worden geüpload nadat het oorspronkelijke token is verlopen.
    + Ondersteunde HTTP CSV/TSV-bestanden importeren in dataset python SDK.
    + De methode Workspace. Setup () is afgeschaft. Er wordt een waarschuwings bericht weer gegeven aan gebruikers met de suggesties Create () of Get ()/from_config ().
    + Environment. add_private_pip_wheel () is toegevoegd, waarmee het uploaden van persoonlijke aangepaste Python-pakketten kan worden `whl`naar de werk ruimte en het veilig gebruiken van ze om de omgeving te bouwen/realiseren.
    + U kunt nu het SSL-certificaat voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster bijwerken voor zowel het micro soft-certificaat dat is gegenereerd door een klant.
  + **azureml-uitleg-model**
    + Para meter toegevoegd om een model-ID toe te voegen aan uitleg bij het uploaden.
    + `is_raw` Tags toevoegen aan uitleg in het geheugen en uploads.
    + Er zijn pytorch-ondersteuning en tests toegevoegd voor het pakket voor de azureml-uitleg-model.
  + **azureml-opengegevenssets**
    + Ondersteuning voor het detecteren en registreren van automatische test omgevingen.
    + U hebt klassen toegevoegd om Amerikaanse populatie per regio en post te krijgen.
  + **azureml-pijp lijn-kern**
    + De eigenschap label is toegevoegd aan de definitie van de invoer-en uitvoer poort.
  + **azureml-telemetrie**
    + Er is een onjuiste telemetrie-configuratie hersteld.
  + **azureml-Train-automl**
    + Er is een fout opgetreden bij het uitvoeren van een probleem waarbij tijdens de installatie fouten geen logboek wordt ontvangen van het veld ' Errors ' voor de installatie en daarom niet is opgeslagen in de bovenliggende run ' Errors '.
    + Er is een probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet correct zijn verwijderd.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen.
    + Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komen in de Trainingsset tijdens het voors pellen.
    + AutoMLStep wordt nu door `automl` configuratie naar de back-end geleid om problemen met wijzigingen of toevoegingen van nieuwe configuratie parameters te voor komen.
    + AutoML data Guardrail is nu beschikbaar als open bare preview. De gebruiker ziet een Data Guardrail-rapport (voor classificatie/regressie taken) na de training en is ook toegankelijk via SDK API.
  + **azureml-Train-core**
    + Torch 1,2-ondersteuning is toegevoegd in PyTorch Estimator.
  + **azureml-widgets**
    + Verbeterde matrix diagrammen voor Verwar ring voor classificatie trainingen.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data prep SDK v 1.1.12
+ **Nieuwe functies**
  + Een lijst met teken reeksen kan nu worden door gegeven als invoer voor `read_*`-methoden.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De prestaties van `read_parquet` zijn aanzienlijk verbeterd wanneer ze in Spark worden uitgevoerd.
  + Er is een probleem opgelost waarbij `column_type_builder` mislukt in het geval van één kolom met dubbel zinnige datum notaties.

### <a name="azure-portal"></a>Azure-portal
+ **Preview-functie**
  + Logboek-en uitvoer bestands streaming is nu beschikbaar voor pagina's met details van de uitvoering. De bestanden streamen updates in realtime wanneer de schakel optie voor preview is ingeschakeld.
  + De mogelijkheid om een quotum op een werkruimte niveau in te stellen, wordt vrijgegeven in de preview-versie. AmlCompute quota worden toegewezen op abonnements niveau, maar we bieden u de mogelijkheid om dat quotum tussen werk ruimten te verdelen en toe te wijzen voor eerlijk delen en governance. Klik op de Blade gebruiks namen en **quota's** in de linkernavigatiebalk van uw werk ruimte en selecteer het tabblad **Quota's configureren** . Houd er rekening mee dat u een abonnements beheerder moet zijn om quota's op het niveau van de werk ruimte te kunnen instellen, omdat dit een bewerking in meerdere werk ruimten is.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK voor python v-1.0.55

+ **Nieuwe functies**
  + Verificatie op basis van tokens wordt nu ondersteund voor de aanroepen van het Score-eind punt dat is geïmplementeerd op AKS. De huidige op sleutels gebaseerde authenticatie blijven wel worden ondersteund en gebruikers kunnen een van deze verificatie mechanismen tegelijk gebruiken.
  + De mogelijkheid om een Blob-opslag te registreren die zich achter het virtuele netwerk (VNet) bevindt als een gegevens opslag.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Hiermee wordt een bug opgelost waarbij de validatie grootte voor het verg Roten van de AVK klein is en resulteert in beschadigde, gedicteerde en ware grafieken voor regressie en prognose.
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Vaste fouten van `Timeseries` als de preproces-vlag is ingesteld op True.
    + Er zijn bepaalde fout berichten voor prognose gegevens validatie meer actie mogelijk.
    + Gereduceerd geheugen gebruik van AutoML wordt uitgevoerd door gegevens sets te verwijderen en/of langzaam te laden, met name tussen het proces
  + **azureml-contrib-uitleg-model**
    + Er is model_task vlag toegevoegd aan uitlegers, zodat de gebruiker de standaard logica voor automatische interferentie voor het model type kan overschrijven
    + Wijzigingen in de widget: automatisch geïnstalleerd met `contrib`, geen uitleg meer `nbextension` installeren/inschakelen-ondersteuning met alleen de prioriteit van globale functies (bijvoorbeeld Permutative)
    + Wijzigingen in het dash board:-box plots en viools worden naast `beeswarm` plot op een overzichts pagina, veel sneller opnieuw weer geven van `beeswarm` tekening op de schuif regelaar van de hoogste k-verschuiving-nuttig bericht waarin wordt uitgelegd hoe top-k wordt berekend op basis van bruikbare, aanpas bare berichten in plaats van grafieken wanneer er geen gegevens worden geboden
  + **azureml-core**
    + De methode model. package () is toegevoegd om docker-installatie kopieën en Dockerfiles te maken waarmee modellen en hun afhankelijkheden worden ingekapseld.
    + Lokale webservices bijgewerkt om InferenceConfigs te accepteren die omgevings objecten bevatten.
    + Vast model. REGI ster () waarbij ongeldige modellen worden geproduceerd wanneer. (voor de huidige map) wordt door gegeven als de para meter model_path.
    + Run. submit_child toevoegen, de functionaliteit spiegelt experiment. Submit bij het opgeven van de run as the parent van de ingediende onderliggende run.
    + Ondersteuning voor configuratie opties van model. registreren in run. register_model.
    + De mogelijkheid om JAR-taken uit te voeren op een bestaand cluster.
    + Ondersteunt nu instance_pool_id-en cluster_log_dbfs_path-para meters.
    + Er is ondersteuning toegevoegd voor het gebruik van een omgevings object bij het implementeren van een model naar een webservice. Het omgevings object kan nu worden opgenomen als onderdeel van het InferenceConfig-object.
    + Appinsifht toewijzing toevoegen voor nieuwe regio's-centraal-westus-northcentralus
    + Documentatie toegevoegd voor alle kenmerken in alle Data Store-klassen.
    + Blob_cache_timeout para meter toegevoegd aan `Datastore.register_azure_blob_container`.
    + Save_to_directory-en load_from_directory-methoden toegevoegd aan azureml. core. Environment. Environment.
    + De opdrachten "AZ ml Environment down load" en "AZ ml Environment REGI ster" zijn toegevoegd aan de CLI.
    + De methode environment. add_private_pip_wheel is toegevoegd.
  + **azureml-uitleg-model**
    + Het bijhouden van gegevensset is toegevoegd aan uitleg met behulp van de DataSet-service (preview).
    + De standaard Batch grootte is verminderd bij het streamen van globale uitleg van 10k tot 100.
    + Er is model_task markering toegevoegd aan uitlegers, zodat de gebruiker de standaard logica voor automatische interferentie voor het model type kan onderdrukken.
  + **azureml-mlflow**
    + Er is een fout opgelost in mlflow. azureml. build_image waar geneste mappen worden genegeerd.
  + **azureml-pijp lijn-stappen**
    + De mogelijkheid om JAR-taken uit te voeren op bestaande Azure Databricks cluster is toegevoegd.
    + Er zijn ondersteunings instance_pool_id en cluster_log_dbfs_path-para meters voor de stap DatabricksStep toegevoegd.
    + Er is ondersteuning toegevoegd voor pijplijn parameters in de stap DatabricksStep.
  + **azureml-Train-automl**
    + `docstrings` toegevoegd voor de aan ensemble gerelateerde bestanden.
    + Documenten bijgewerkt naar een meer geschikte taal voor `max_cores_per_iteration` en `max_concurrent_iterations`
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Get_data verwijderd uit pijp lijn `automlstep` notitie blok.
    + De ondersteuning van `dataprep` in `automlstep`is gestart.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data prep SDK v 1.1.10

+ **Nieuwe functies**
  + U kunt nu aanvragen voor het uitvoeren van specifieke controles (zoals histogram, spreidings plot enz.) op specifieke kolommen.
  + Er is een parallelliseren-argument toegevoegd aan `append_columns`. Indien waar, worden gegevens in het geheugen geladen, maar wordt de uitvoering parallel uitgevoerd. Als deze eigenschap onwaar is, wordt de uitvoering streaming, maar met één thread.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK voor python v-1.0.53

+ **Nieuwe functies**
  + Automatische Machine Learning ondersteunt nu ONNX modellen voor training op het externe Compute-doel
  + Azure Machine Learning biedt nu de mogelijkheid om de training te hervatten vanuit een eerder uitgevoerde, controle punt-of model bestand.
    + Meer informatie over het [gebruik van schattingen om de training van een vorige uitvoering te hervatten](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **automl-client-core-nativeclient**
    + De bug over verbroken-kolom typen na de trans formatie oplossen (bug gekoppeld);
    + Y_query mag een object type zijn dat geen (e) (of) bevat aan het begin (#459519).
  + **Azure-cli-ml**
    + CLI-opdrachten ' model implementeren ' en ' service-update ' accepteren nu para meters, configuratie bestanden of een combi natie van beide. Para meters hebben voor rang op kenmerken in bestanden.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
  + **azureml-automl-core**
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + Ondersteuning voor NimbusML-schattingen toevoegen & pijp lijnen die moeten worden gebruikt binnen AutoML-schattingen.
    + Het oplossen van een bug in de selectie procedure van de ensemble die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores constant bleven.
    + Schakel het hergebruik van een aantal featurizations in voor de voor spellingen van taken. Dit versnelt de uitvoerings tijd van de installatie die wordt uitgevoerd door een factor n_cross_validations voor dure featurizations zoals lags en Rolling Windows.
    + Er wordt een probleem opgelost als de tijd buiten de Panda-ondersteunde tijds bereik wordt ondersteund. We veroorzaken nu een DataException als de tijd minder is dan pd. Time Stamp. min of groter dan pd. Time Stamp. Max
    + Door prognoses te maken, kunnen verschillende frequenties worden ingesteld in de trein-en test sets als deze kunnen worden uitgelijnd. Zo kan ' kwar taal vanaf januari ' en op ' kwar taal vanaf oktober ' worden uitgelijnd.
    + De eigenschap para meters is toegevoegd aan de TimeSeriesTransformer.
    + Oude uitzonderings klassen verwijderen.
    + In prognose taken accepteert de para meter `target_lags` nu één geheel getal of een lijst met gehele getallen. Als het gehele getal is gegeven, wordt er slechts één vertraging gemaakt. Als er een lijst wordt weer gegeven, worden de unieke waarden van lags genomen. target_lags = [1, 2, 2, 4] maakt lags van 1, 2 en 4 Peri Oden.
    + Los de fout op over het verliezen van kolom typen na de trans formatie (bug gekoppeld);
    + In `model.forecast(X, y_query)`kunt u y_query een object type hebben dat geen (en) bevat aan het begin (#459519).
    + Verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-contrib-datadrift**
    +  Verbeteringen in het voor beeld van een notebook, inclusief de overstap naar azureml-open datasets in plaats van contrib-open datasets en prestatie verbeteringen bij het verrijken van gegevens
  + **azureml-contrib-uitleg-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + Er zijn segmenten toegevoegd aan de afbeeldings uitleg in de afbeeldings uitleg voor het pakket AzureML-contrib-uitleg-model
    + Scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + `batch_size` is toegevoegd om de uitleger te simuleren wanneer `include_local=False`, voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
  + **azureml-contrib-featureengineering**
    + Oplossing voor het aanroepen van set_featurizer_timeseries_params (): wijziging van het type dict-waarde en null-controle: notitie blok toevoegen voor `timeseries` featurizer
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd
    + Er is een fout opgelost met het uploaden van de Data Store waar een lege map wordt gemaakt als `target_path` met `/` is gestart
    + Probleem met vaste `deepcopy` in ServicePrincipalAuthentication.
    + De opdrachten "AZ ml Environment show" en "AZ ml Environment List" zijn toegevoegd aan de CLI.
    + Omgevingen bieden nu ondersteuning voor het opgeven van een base_dockerfile als alternatief voor een al gebouwd base_image.
    + De niet-gebruikte RunConfiguration-instelling auto_prepare_environment is gemarkeerd als afgeschaft.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
    + Bugfix: voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van het verwijderen als de verwijdering mislukt vanwege een upstream-afhankelijkheid.
    + Er is een probleem opgelost waarbij een lege duur is afgedrukt voor implementaties die optreden bij het maken van een werk ruimte voor sommige omgevingen.
    + Verbeterde werk ruimte fout uitzonderingen maken. Zo kunnen gebruikers niet zien dat de werk ruimte niet kan worden gemaakt. Kan... niet vinden Als het bericht en wordt in plaats daarvan de daad werkelijke aanmaak fout weer gegeven.
    + Voeg ondersteuning toe voor token verificatie in AKS-webservices.
    + Voeg `get_token()` methode toe aan `Webservice`-objecten.
    + CLI-ondersteuning is toegevoegd om machine learning gegevens sets te beheren.
    + `Datastore.register_azure_blob_container` hebt nu optioneel een `blob_cache_timeout` waarde (in seconden) waarmee de koppel parameters van de blobfuse worden geconfigureerd om de verval datum van de cache voor dit gegevens archief in te scha kelen. De standaard waarde is geen time-out, d.w.z. Wanneer een BLOB wordt gelezen, blijft deze in de lokale cache totdat de taak is voltooid. De meeste taken geven de voor keur aan deze instelling, maar sommige taken moeten meer gegevens lezen uit een grote gegevensset dan op hun knoop punten passen. Voor deze taken helpt deze bij het afstemmen van deze para meter. Wees voorzichtig bij het afstemmen van deze para meter: als u de waarde te laag instelt, kan dit leiden tot slechte prestaties, omdat de gegevens die in een epoche worden gebruikt, mogelijk verlopen voordat ze opnieuw worden gebruikt. Dit betekent dat alle Lees bewerkingen worden uitgevoerd vanuit de Blob-opslag (dat wil zeggen het netwerk) in plaats van de lokale cache, die een negatieve invloed heeft op de opleidings tijden.
    + De model beschrijving kan nu na de registratie op de juiste wijze worden bijgewerkt
    + Het model en de installatie kopie bieden nu meer informatie over de upstream-objecten die afhankelijk zijn van de items die ervoor zorgen dat het verwijderen mislukt
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
  + **azureml-uitleg-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + vorm van lineaire uitleg weergave is toegevoegd, evenals een ander niveau voor de uitleg over lineaire modellen in tabel vorm
    + voor een gesimuleerde uitleg van de uitleg model bibliotheek is een vaste fout opgetreden wanneer include_local = False voor sparse gegevens invoer
    + Verwachte waarden toevoegen aan `automl` uitvoer
    + het is belang rijk dat het probleem is opgelost door het argument van de transformaties van de functie RAW te verkrijgen
    + `batch_size` is toegevoegd om de uitleger te simuleren wanneer `include_local=False`, voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
    + voor de model verklarende bibliotheek is een vast blackbox-uitleg waarbij Pandas data frame invoer vereist is voor de voor spelling
    + Er is een fout opgelost waarbij `explanation.expected_values` soms een float zou retour neren in plaats van een lijst met een zwevende waarde.
  + **azureml-mlflow**
    + Verbeter de prestaties van mlflow. set_experiment (experiment_name)
    + Bug corrigeren in het gebruik van InteractiveLoginAuthentication voor mlflow-tracking_uri
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow verbeteren
    + Patch bug waarbij mlflow. log_artifacts (' my_dir ') artefacten opslaat onder ' my_dir/< artefact-paden > ' in plaats van ' < artefact-paden > '
  + **azureml-opengegevenssets**
    + Vastmaken `pyarrow` van `opendatasets` aan oude versies (< 0.14.0) vanwege geheugen problemen die hier nieuw zijn geïntroduceerd.
    + Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevensset-klassen kunnen worden geregistreerd voor Azure Machine Learning werk ruimte en gebruikmaken van de functies van AML-gegevensset naadloos.
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-pijp lijn-stappen**
    + DBFS Data Store wordt nu ondersteund voor invoer en uitvoer in DatabricksStep.
    + Bijgewerkte documentatie voor Azure Batch stap met betrekking tot invoer/uitvoer.
    + In AzureBatchStep is *delete_batch_job_after_finish* standaard waarde gewijzigd in *True*.
  + **azureml-telemetrie**
    +  Verplaats azureml-contrib-opendatasets naar azureml-opendatasets.
    + Toestaan dat open gegevensset-klassen kunnen worden geregistreerd voor Azure Machine Learning werk ruimte en gebruikmaken van de functies van AML-gegevensset naadloos.
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-Train-automl**
    + Bijgewerkte documentatie over get_output om het werkelijke retour type weer te geven en aanvullende notities te geven over het ophalen van de sleutel eigenschappen.
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + Verwachte waarden toevoegen aan `automl` uitvoer
  + **azureml-Train-core**
    + Teken reeksen worden nu geaccepteerd als Compute target voor automatische afstemming-afstemming
    + De niet-gebruikte RunConfiguration-instelling auto_prepare_environment is gemarkeerd als afgeschaft.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning data prep SDK v 1.1.9

+ **Nieuwe functies**
  + Er is ondersteuning toegevoegd voor het lezen van een bestand rechtstreeks vanuit een HTTP-of HTTPS-URL.

+ **Oplossingen en verbeteringen voor oplossingen**
  + Er is een verbeterd fout bericht bij het lezen van een Parquet-gegevensset van een externe bron (die momenteel niet wordt ondersteund).
  + Er is een fout opgelost bij het schrijven naar de Parquet-bestands indeling in ADLS gen 2, en het bijwerken van de naam van de ADLS gen 2-container in het pad.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuele interface
+ **Preview-functies**
  + De module voor het uitvoeren van een R-script is toegevoegd in de visuele interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK voor python v-1.0.48

+ **Nieuwe functies**
  + **azureml-opengegevenssets**
    + **azureml-contrib-opengegevenssets** is nu beschikbaar als **azureml-opendatasets**. Het oude pakket kan nog steeds werken, maar we raden u aan om gebruik te maken van **azureml-opengegevenssets** voor geavanceerde mogelijkheden en verbeteringen.
    + Met dit nieuwe pakket kunt u open gegevens sets als gegevensset registreren in Azure Machine Learning werk ruimte en gebruikmaken van de functies die door de gegevensset worden aangeboden.
    + Het omvat ook bestaande mogelijkheden, zoals het gebruik van open gegevens sets als Panda-en SPARK-dataframes en de locatie koppeling voor een bepaalde gegevensset, zoals weer.

+ **Preview-functies**
    + HyperDriveConfig kan pijplijn object nu accepteren als een para meter ter ondersteuning van het afstemmen van afstemming met behulp van een pijp lijn.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-Train-automl**
    + De fout voor het verliezen van kolom typen na de trans formatie is opgelost.
    + De bug is opgelost zodat y_query een object type is dat aan het begin geen (en) bevat.
    + Het probleem is opgelost in de procedure voor het selecteren van ensembles die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores constant bleven.
    + Het probleem met whitelist_models-en blacklist_models-instellingen in AutoMLStep is opgelost.
    + Het probleem is opgelost waardoor het gebruik van voor verwerking niet mogelijk is wanneer AutoML werd gebruikt in de context van Azure ML-pijp lijnen.
  + **azureml-opengegevenssets**
    + Azureml-contrib-opendatasets is verplaatst naar azureml-opendatasets.
    + Toegestane open gegevensset-klassen moeten worden geregistreerd voor Azure Machine Learning werk ruimte en kunnen naadloos gebruikmaken van de functies van AML-gegevensset.
    + Verbeterde NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-uitleg-model**
    + Bijgewerkte online documentatie voor Objects voor interpretaties.
    + `batch_size` is toegevoegd om de uitleger te simuleren wanneer `include_local=False`, voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel voor de bibliotheek van de model uitleg te verbeteren.
    + Het probleem opgelost waarbij `explanation.expected_values` soms een float zou retour neren in plaats van een lijst met een zwevend item.
    + De verwachte waarden zijn toegevoegd aan `automl` uitvoer voor de uitleg van de gesimuleerde uitleg van de model bibliotheek.
    + Het kenmerk van de vaste permutatie functie wanneer trans formaties argument worden geleverd om het belang van de onbewerkte functie te verkrijgen.
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd.
    + Het probleem met de Data Store upload is opgelost, waarbij een lege map wordt gemaakt als `target_path` met `/`is gestart.
    + Vergelijking van twee gegevens sets is ingeschakeld.
    + Voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van de methode voor het verwijderen als gevolg van een upstream-afhankelijkheid.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
  + **azureml-mlflow**
    + Verbeterd resource gebruik van externe uitvoeringen die gebruikmaken van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow is verbeterd.
    + Er is een probleem opgelost waarbij mlflow. log_artifacts (' my_dir ') artefacten opslaat onder ' my_dir/artifact-paths ' in plaats van ' artefact paden '.
  + **azureml-pijp lijn-kern**
    + De para meter hash_paths voor alle pijplijn stappen is afgeschaft en wordt in de toekomst verwijderd. Standaard wordt de inhoud van de source_directory gehasht (met uitzonde ring van bestanden die worden vermeld in. amlignore of. gitignore)
    + Verbeterde module-en ModuleStep voor het ondersteunen van berekenings type-specifieke modules, voor bereiding op RunConfiguration-integratie en andere wijzigingen om het gebruik van het gerekende type specifieke module in pijp lijnen te ontgrendelen.
  + **azureml-pijp lijn-stappen**
    + AzureBatchStep: verbeterde documentatie met betrekking tot invoer/uitvoer.
    + AzureBatchStep: delete_batch_job_after_finish standaard waarde is gewijzigd in waar.
  + **azureml-Train-core**
    + Teken reeksen worden nu geaccepteerd als Compute target voor automatische afstemming-afstemming.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
    + Afgeschafte para meters `conda_dependencies_file_path` en `pip_requirements_file_path` voor respectievelijk `conda_dependencies_file` en `pip_requirements_file`.
  + **azureml-opengegevenssets**
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning data prep SDK v 1.1.8

+ **Nieuwe functies**
 + Gegevensstroom objecten kunnen nu worden herhaald, waardoor er een reeks records wordt geproduceerd. Raadpleeg de documentatie voor `Dataflow.to_record_iterator`.
  + Gegevensstroom objecten kunnen nu worden herhaald, waardoor er een reeks records wordt geproduceerd. Raadpleeg de documentatie voor `Dataflow.to_record_iterator`.

+ **Oplossingen en verbeteringen voor oplossingen**
 + Verbeterde DataPrep-SDK.
 + Verbeterde verwerking van Panda-DataFrames met niet-teken reeks kolom indexen.
 + Verbeterde prestaties van `to_pandas_dataframe` in gegevens sets.
 + Er is een fout opgelost waarbij Spark-uitvoering van gegevens sets niet kan worden uitgevoerd in een omgeving met meerdere knoop punten.
  + Verbeterde DataPrep-SDK.
  + Verbeterde verwerking van Panda-DataFrames met niet-teken reeks kolom indexen.
  + Verbeterde prestaties van `to_pandas_dataframe` in gegevens sets.
  + Er is een fout opgelost waarbij Spark-uitvoering van gegevens sets niet kan worden uitgevoerd in een omgeving met meerdere knoop punten.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning data prep SDK v 1.1.7

Er is een wijziging doorgegaan die de prestaties verbeterd, omdat deze problemen voor sommige klanten met Azure Databricks veroorzaken. Als er een probleem is opgetreden op Azure Databricks, kunt u een upgrade uitvoeren naar versie 1.1.7 met behulp van een van de volgende methoden:
1. Voer dit script uit om een upgrade uit te voeren: `%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Maak het cluster opnieuw, waardoor de meest recente SDK-versie voor gegevens voorbereiding wordt geïnstalleerd.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK voor python v-1.0.45

+ **Nieuwe functies**
  + Een surrogaat model voor de beslissings structuur toevoegen om uitleg te geven in het pakket voor azureml-uitleg-model
  + De mogelijkheid om een CUDA-versie op te geven die moet worden geïnstalleerd op installatie kopieën. Ondersteuning voor CUDA 9,0, 9,1 en 10,0.
  + Informatie over Azure ML-trainings basis installatie kopieën zijn nu beschikbaar in [Azure ml-containers github repository](https://github.com/Azure/AzureML-Containers) en [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + CLI-ondersteuning voor pijplijn schema toegevoegd. Voer ' AZ ml pipeline-h ' uit voor meer informatie
  + De aangepaste Kubernetes-naam ruimte parameter is toegevoegd aan de AKS-configuratie voor de webservice-implementatie en de CLI.
  + Afgeschafte hash_paths-para meter voor alle pijplijn stappen
  + Model. registreren ondersteunt nu het registreren van meerdere afzonderlijke bestanden als één model met het gebruik van de para meter `child_paths`.

+ **Preview-functies**
    + Score uitlegers kunnen nu optioneel Conda-en PIP-gegevens opslaan voor betrouwbaardere serialisatie en deserialisatie.
    + Fout oplossing voor automatische functie selectie.
    + Mlflow. azureml. build_image bijgewerkt naar de nieuwe API, patches die worden weer gegeven door de nieuwe implementatie.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De `paramiko` afhankelijkheid is verwijderd uit de azureml-kern. Waarschuwingen voor afschaffing zijn toegevoegd voor verouderde methoden voor het koppelen van het doel.
  + Verbeter de prestaties van run. create_children
  + In de gesimuleerde uitleg met een binaire classificatie kunt u de volg orde van kansen oplossen wanneer de kans op docenten wordt gebruikt voor het schalen van vorm waarden.
  + Verbeterde fout afhandeling en-berichten voor automatische machine learning.
  + Het probleem met de herhalings fout is opgelost voor automatische machine learning.
  + De trans formatie prestaties van de tijd reeks zijn verbeterd voor automatische machine learning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning data prep SDK v 1.1.6

+ **Nieuwe functies**
  + Er zijn samenvattings functies toegevoegd voor de hoogste waarden (`SummaryFunction.TOPVALUES`) en de laagste waarden (`SummaryFunction.BOTTOMVALUES`).

+ **Oplossingen en verbeteringen voor oplossingen**
  + De prestaties van `read_pandas_dataframe`aanzienlijk verbeteren.
  + Er is een fout opgelost die ertoe leidt dat `get_profile()` op een gegevensstroom die naar binaire bestanden verwijst, mislukt.
  + Beschik bare `set_diagnostics_collection()` om de telemetrie-verzameling in-of uit te scha kelen.
  + Het gedrag van `get_profile()`is gewijzigd. De NaN-waarden worden nu genegeerd voor min, gemiddelde, STDEV en Sum, die worden uitgelijnd met het gedrag van Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK voor python v-1.0.43

+ **Nieuwe functies**
  + Azure Machine Learning biedt nu eersteklas ondersteuning voor populaire machine learning en gegevens analyse Framework Scikit-meer informatie. Met [`SKLearn` Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)kunnen gebruikers eenvoudig Scikit-leer modellen trainen en implementeren.
    + Meer informatie over het [uitvoeren van afstemming-afstemming met Scikit-informatie met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Er is ondersteuning toegevoegd voor het maken van ModuleStep in pijp lijnen, samen met module-en ModuleVersion-klassen voor het beheren van herbruikbare reken eenheden.
  + ACI webservices ondersteunen nu permanente scoring_uri via updates. De scoring_uri wordt gewijzigd van IP naar FQDN. Het DNS-naam label voor FQDN kan worden geconfigureerd door de dns_name_label op deploy_configuration in te stellen.
  + Automatische machine learning nieuwe functies:
    + STL featurizer voor prognose
    + KMeans-Clustering is ingeschakeld voor het opruimen van functies
  + AmlCompute quota goedkeuringen zijn nog sneller geworden. We hebben nu het proces voor het goed keuren van uw quotum aanvragen binnen een drempel waarde geautomatiseerd. Meer informatie over de werking van quota's vindt [u in het beheren van quota's](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Preview-functies**
    + Integratie met [MLflow](https://mlflow.org) 1.0.0 tracking via het MLflow-pakket ([voor beeld-notebooks](https://aka.ms/azureml-mlflow-examples)).
    + Verzend Jupyter notebook als een run. [API-referentie documentatie](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Open bare preview-versie van de [gegevens drift](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) via het contrib-datadrift-pakket ([voorbeeld notitieblokken](https://aka.ms/azureml-datadrift-example)). Data gradatie is een van de belangrijkste redenen waarbij de nauw keurigheid van het model in de loop van de tijd verloopt. Het gebeurt wanneer gegevens die worden geleverd aan model in productie verschillen van de gegevens waarop het model is getraind. AML data drift detector helpt klanten bij het bewaken van gegevens drift en bij het verzenden van een waarschuwing wanneer er een drift wordt gedetecteerd.

+ **Wijzigingen afbreken**

+ **Oplossingen en verbeteringen voor oplossingen**
  + RunConfiguration load en Save ondersteunt het opgeven van een volledig bestandspad met volledige back-compat voor vorig gedrag.
  + De cache is toegevoegd in ServicePrincipalAuthentication, is standaard uitgeschakeld.
  + Logboek registratie inschakelen voor meerdere grafieken onder dezelfde metrische naam.
  + Model klasse kan nu goed worden overdraagbaar vanuit azureml. core (`from azureml.core import Model`).
  + In de stappen van de pijp lijn is de para meter `hash_path` nu afgeschaft. Het nieuwe gedrag is om een hash te volt ooien source_directory, behalve bestanden die worden vermeld in. amlignore of. gitignore.
  + In pijplijn pakketten zijn verschillende `get_all`-en `get_all_*` methoden vervangen door respectievelijk `list` en `list_*`.
  + azureml. core. get_run vereist niet langer dat klassen worden geïmporteerd voordat het oorspronkelijke run-type wordt geretourneerd.
  + Er is een probleem opgelost waarbij een aantal aanroepen naar de update voor de WebService geen update heeft geactiveerd.
  + Een score-time-out op AKS webservices moet tussen 5ms en 300000ms zijn. Maxi maal toegestane scoring_timeout_ms voor Score aanvragen is van 1 min tot 5 minuten gedalend.
  + LocalWebservice-objecten hebben nu `scoring_uri` en `swagger_uri` eigenschappen.
  + Tijdens het maken van de uitvoer van de map en uitvoer van de Directory wordt het gebruikers proces niet meer verzonden. De SDK voor de uitvoerings geschiedenis is ingeschakeld om in elk gebruikers proces te worden uitgevoerd. Hiermee worden enkele synchronisatie problemen opgelost die zijn ondervonden door gedistribueerde trainings uitvoeringen.
  + De naam van het azureml-logboek dat is geschreven van de naam van het gebruikers proces bevat nu de naam van het proces (alleen voor gedistribueerde trainingen) en de PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning data prep SDK v 1.1.5

+ **Oplossingen en verbeteringen voor oplossingen**
  + Voor geïnterpreteerde datetime-waarden met een jaar notatie van twee cijfers is het bereik van geldige jaren bijgewerkt zodat dit overeenkomt met Windows-versies. Het bereik is gewijzigd van 1930-2029 naar 1950-2049.
  + Bij het lezen van een bestand en het instellen van `handleQuotedLineBreaks=True`wordt `\r` beschouwd als een nieuwe regel.
  + Er is een fout opgelost waardoor `read_pandas_dataframe` in sommige gevallen een fout heeft veroorzaakt.
  + Verbeterde prestaties van `get_profile`.
  + Verbeterde fout berichten.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning data prep SDK v 1.1.4

+ **Nieuwe functies**
  + U kunt nu de volgende expressie taal functies gebruiken om datetime-waarden te extra heren en parseren in nieuwe kolommen.
    + `RegEx.extract_record()` haalt datetime-elementen op in een nieuwe kolom.
    + `create_datetime()` maakt datetime-objecten van afzonderlijke datetime-elementen.
  + Wanneer u `get_profile()`aanroept, kunt u zien dat quantile-kolommen worden aangeduid als (EST.) om duidelijk aan te geven dat de waarden benaderingen zijn.
  + U kunt nu * * globbing gebruiken bij het lezen van Azure Blob Storage.
    + bijvoorbeeld `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Oplossingen voor oplossingen**
  + Er is een fout met betrekking tot het lezen van een Parquet-bestand van een externe bron (Azure-Blob) opgelost.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK voor python v-1.0.39
+ **Gewijzigde**
  + De auto_prepare_environment-optie voor het uitvoeren van de configuratie wordt afgeschaft, waarbij automatisch voorbereiden de standaard instelling wordt.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning data prep SDK v 1.1.3

+ **Nieuwe functies**
  + Er is ondersteuning toegevoegd om te lezen uit een PostgresSQL-data base, door read_postgresql aan te roepen of door gebruik te maken van een gegevens opslag.
    + Voor beelden bekijken in hand leidingen:
      + [Notitie blok voor gegevens opname](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Data Store-notebook](https://aka.ms/aml-data-prep-datastore-nb)

+ **Oplossingen en verbeteringen voor oplossingen**
  + Opgeloste problemen met kolom Type conversie:
  + Een Boole-of numerieke kolom wordt nu correct geconverteerd naar een Booleaanse kolom.
  + Treedt nu niet op wanneer wordt geprobeerd een datum kolom in te stellen op datum type.
  + Verbeterde JoinType-typen en bijbehorende naslag documentatie. Wanneer u twee gegevens stromen koppelt, kunt u nu een van deze typen joins opgeven:
    + GEEN, MATCH, INNER, UNMATCHLEFT, LEFTANTI, LEFTOUTER, UNMATCHRIGHT, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Verbeterd gegevens type om meer datum notaties te herkennen.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure-portal

In Azure Portal kunt u nu het volgende doen:
+ Automatische ML experimenten maken en uitvoeren
+ Maak een notebook-VM voor het uitproberen van voor beelden van Jupyter-notebooks of uw eigen virtuele machine.
+ De sectie gloed nieuwe ontwerpen (preview) in de werk ruimte Azure Machine Learning, waaronder geautomatiseerde Machine Learning, visuele interface en gehoste laptop-Vm's
    + Automatisch een model maken met geautomatiseerde machine learning
    + Een visuele interface voor slepen en neerzetten gebruiken om experimenten uit te voeren
    + Maak een notebook-VM om gegevens te verkennen, modellen te maken en services te implementeren.
+ Live diagram en metrische gegevens bijwerken in rapporten uitvoeren en detail pagina's uitvoeren
+ De bestands viewer voor logboeken, uitvoer en moment opnamen is bijgewerkt op de pagina's met details van de uitvoering.
+ Nieuwe en verbeterde ervaring bij het maken van rapporten op het tabblad experimenten.
+ De mogelijkheid om het bestand config. json te downloaden van de pagina overzicht van de werk ruimte Azure Machine Learning is toegevoegd.
+ Ondersteuning voor het maken van Azure Machine Learning werk ruimte vanuit de Azure Databricks-werk ruimte.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK voor python v-1.0.33
+ **Nieuwe functies**
  + De methode _Workspace. Create_ accepteert nu standaard cluster configuraties voor CPU-en GPU-clusters.
  + Als het maken van de werk ruimte mislukt, zijn afhankelijk van de gereinigde resources.
  + Standaard Azure Container Registry SKU is overgeschakeld naar Basic.
  + Azure Container Registry wordt vertraagd gemaakt, wanneer dit nodig is voor het uitvoeren of maken van een installatie kopie.
  + Ondersteuning voor omgevingen voor trainings uitvoeringen.

### <a name="notebook-virtual-machine"></a>Virtuele machine van notebook 

Gebruik een laptop-VM als een veilige, bedrijfsgeschikte hosting omgeving voor Jupyter-notebooks waarin u machine learning experimenten kunt Program meren, modellen als web-eind punten implementeert en alle andere bewerkingen uitvoert die door Azure Machine Learning SDK worden ondersteund met behulp van python. Het biedt verschillende mogelijkheden:
+ [Maak snel een vooraf geconfigureerde VM van een notebook](tutorial-1st-experiment-sdk-setup.md) met de nieuwste versie van Azure machine learning SDK en gerelateerde pakketten.
+ De toegang wordt beveiligd via bewezen technologieën, zoals HTTPS, Azure Active Directory verificatie en autorisatie.
+ Betrouw bare Cloud opslag van notitie blokken en code in uw Azure Machine Learning-werkruimte Blob Storage-account. U kunt de VM van uw notebook veilig verwijderen zonder dat uw werk verloren gaat.
+ Vooraf geïnstalleerde voorbeeld notitieblokken om Azure Machine Learning-functies te verkennen en te experimenteren.
+ Volledige aanpassings mogelijkheden van virtuele Azure-machines, elk VM-type, alle pakketten, alle Stuur Programma's. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK voor python v 1.0.33 uitgebracht.

+ Azure ML Modellen met hardwareversnelling op [fpga's](how-to-deploy-fpga-web-service.md) is algemeen beschikbaar.
  + U kunt nu [het pakket voor azureml-accel.-modellen gebruiken voor het](how-to-deploy-fpga-web-service.md) volgende:
    + Train de gewichten van een ondersteund diepe Neural-netwerk (ResNet 50, ResNet 152, DenseNet-121, VGG-16 en SSD-VGG)
    + Overboeking Learning gebruiken met de ondersteunde DNN
    + Het model registreren bij Modelbeheer-service en het model container plaatsen
    + Het model implementeren in een Azure-VM met een FPGA in een Azure Kubernetes service-cluster (AKS)
  + De container implementeren op een [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server-apparaat
  + Uw gegevens beoordelen met het gRPC-eind punt met dit voor [beeld](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

+ Het opruimen van functies voor het dynamisch toevoegen van :::no-loc text="featurizers"::: voor prestatie optimalisatie. Nieuw :::no-loc text="featurizers":::: werk insluitingen, gewicht van bewijs materiaal, doel codering, tekst doel codering, cluster afstand
+ Slimme CV voor het afhandelen van Train/geldige splitsingen binnen automatische ML
+ Weinig geheugen optimalisatie wijzigingen en prestaties verbeteren
+ Prestatie verbetering in model uitleg
+ ONNX model conversie voor lokale uitvoering
+ Ondersteuning voor subsampling toegevoegd
+ Intelligent stoppen wanneer er geen afsluit criteria zijn gedefinieerd
+ Gestapelde ensembles

+ Tijd reeks prognose
  + Nieuwe voor spelling van voor spel functie
  + U kunt nu een cross-Origin-Kruis validatie op tijdreeks gegevens gebruiken
  + Nieuwe functionaliteit toegevoegd om tijd Series lags te configureren
  + Nieuwe functionaliteit toegevoegd ter ondersteuning van statistische functies van het Rolling venster
  + Nieuwe kerst detectie en featurizer wanneer land code is gedefinieerd in de instellingen van het experiment

+ Azure Databricks
  + Ingeschakelde time series-prognose en model explainabilty/interpretatie mogelijkheden
  + U kunt nu door gaan met automatische ML-experimenten en deze hervatten.
  + Er is ondersteuning toegevoegd voor de verwerking van multicore

### <a name="mlops"></a>MLOps
+ **Lokale implementatie & fout opsporing voor Score containers**<br/> U kunt nu een ML-model lokaal implementeren en snel herhalen op uw score bestand en afhankelijkheden om ervoor te zorgen dat ze werken zoals verwacht.

+ **InferenceConfig & model geïntroduceerd. Deploy ()**<br/> Model implementatie biedt nu ondersteuning voor het opgeven van een bronmap met een invoer script, hetzelfde als een RunConfig.  Daarnaast is model implementatie vereenvoudigd tot één opdracht.

+ **Git-referentie tracering**<br/> Klanten hebben enige tijd de basis mogelijkheden voor git-integratie aangevraagd, waardoor een end-to-end audittrail kan worden bijgehouden. We hebben het volgen van alle belangrijkste entiteiten in azure ML geïmplementeerd voor op git gerelateerde meta gegevens (opslag plaats, commit, clean state). Deze informatie wordt automatisch verzameld door de SDK en CLI.

+ **Model profilering &-validatie service**<br/> Klanten klagen vaak het probleem om de reken kracht die is gekoppeld aan de service voor het afwijzen van de afleiding te verg Roten. Met onze model Profiler-service kan de klant voorbeeld invoer leveren en kunnen we een profiel maken van verschillende CPU-of geheugen configuraties om de optimale grootte van de implementatie te bepalen.

+ **Uw eigen basis installatie kopie maken voor afnemen**<br/> Een andere veelvoorkomende klacht is de moeite bij het overstappen van het afzetten van afhankelijkheden. Met onze nieuwe functie voor het delen van basis afbeeldingen kunt u de basis installatie kopieën, afhankelijkheden en alle onderdelen van de proef installatie nu opnieuw gebruiken. Dit versnelt de implementatie en vermindert de ruimte van de binnenste naar de buitenste lus.

+ **Verbeterde ervaring voor het genereren van Swagger-schema's**<br/> Onze vorige Swagger-generatie methode is fout gevoelig en kan niet worden geautomatiseerd. We hebben een nieuwe in line manier om Swagger-schema's te genereren op basis van een python-functie via versieer. We hebben deze code geopend en het schema voor het genereren van de schema's is niet gekoppeld aan het Azure ML-platform.

+ **Azure ML CLI is algemeen beschikbaar (GA)**<br/> Modellen kunnen nu worden geïmplementeerd met één CLI-opdracht. We hebben veelvoorkomende feedback van klanten, waardoor er geen ML-model van een Jupyter-notebook wordt geïmplementeerd. De [**cli-referentie documentatie**](https://aka.ms/azmlcli) is bijgewerkt.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK voor python v 1.0.30 uitgebracht.

De [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) is geïntroduceerd om een nieuwe versie van een gepubliceerde pijp lijn toe te voegen terwijl hetzelfde eind punt wordt gehandhaafd.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning data prep SDK v 1.1.2

Opmerking: de python-SDK voor gegevens voorbereiding wordt niet meer `numpy` en `pandas` pakketten geïnstalleerd. Zie [bijgewerkte installatie-instructies](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nieuwe functies**
  + U kunt nu de draai transformatie gebruiken.
    + Instructies: [notitie blok draaien](https://aka.ms/aml-data-prep-pivot-nb)
  + U kunt nu reguliere expressies in systeem eigen functies gebruiken.
    + Voorbeelden:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + U kunt nu `to_upper` en `to_lower` -functies gebruiken in de expressie taal.
  + U kunt nu het aantal unieke waarden van elke kolom in een gegevens profiel zien.
  + Voor een aantal veelgebruikte Reader-stappen kunt u nu het argument `infer_column_types` door geven. Als deze is ingesteld op `True`, probeert gegevens voorbereiding automatisch kolom typen te detecteren en te converteren.
    + `inference_arguments` is nu afgeschaft.
  + U kunt `Dataflow.shape`nu aanroepen.

+ **Oplossingen en verbeteringen voor oplossingen**
  + `keep_columns` accepteert nu een extra optioneel argument `validate_column_exists`, waarmee wordt gecontroleerd of het resultaat van `keep_columns` kolommen bevat.
  + Alle stappen van de lezer (die uit een bestand worden gelezen) accepteren nu een extra optioneel argument `verify_exists`.
  + Verbeterde prestaties van lezen van Panda data frame en het ophalen van gegevens profielen.
  + Er is een fout opgelost waarbij het segmenteren van één stap van een gegevens stroom is mislukt met een enkele index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure-portal
  + U kunt nu een bestaand script opnieuw verzenden dat wordt uitgevoerd op een bestaand extern Compute-Cluster.
  + U kunt nu een gepubliceerde pijp lijn uitvoeren met nieuwe para meters op het tabblad pijp lijnen.
  + Details van de uitvoering bieden nu ondersteuning voor een nieuwe viewer voor momentopname bestanden. U kunt een moment opname van de Directory weer geven wanneer u een specifieke uitvoering hebt ingediend. U kunt ook het notitie blok downloaden dat is verzonden om de uitvoering te starten.
  + U kunt de bovenliggende uitvoeringen nu annuleren van de Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK voor python v-1.0.23

+ **Nieuwe functies**
  + De SDK van Azure Machine Learning ondersteunt nu python 3,7.
  + Azure Machine Learning DNN-schattingen bieden nu ingebouwde ondersteuning voor meerdere versies. `TensorFlow` Estimator nu bijvoorbeeld een `framework_version`-para meter accepteren en gebruikers kunnen versie ' 1,10 ' of ' 1,12 ' opgeven. Voor een lijst met de versies die worden ondersteund door uw huidige SDK-versie, roept u `get_supported_versions()` aan op de gewenste Framework-klasse (bijvoorbeeld `TensorFlow.get_supported_versions()`).
  Zie de [DNN Estimator-documentatie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)voor een lijst met de versies die worden ondersteund door de nieuwste SDK-versie.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning data prep SDK v 1.1.1

+ **Nieuwe functies**
  + U kunt meerdere Data Store-DataPath/DataReference-bronnen lezen met read_ *-trans formaties.
  + U kunt de volgende bewerkingen uitvoeren op kolommen om een nieuwe kolom te maken: divisie, vloer, modulo, macht, lengte.
  + Data prep maakt nu deel uit van de Azure ML Diagnostics Suite en registreert standaard diagnostische gegevens.
    + Als u deze optie wilt uitschakelen, stelt u deze omgevings variabele in op True: DISABLE_DPREP_LOGGER

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde code documentatie voor veelgebruikte klassen en functies.
  + Er is een fout opgelost in auto_read_file die Excel-bestanden niet kan lezen.
  + De optie is toegevoegd om de map in read_pandas_dataframe te overschrijven.
  + Verbeterde prestaties van de installatie van dotnetcore2 dependency en toegevoegde ondersteuning voor Fedora 27/28 en Ubuntu 1804.
  + Verbeterde prestaties van het lezen van Azure-blobs.
  + Detectie van kolom type ondersteunt nu kolommen van het type Long.
  + Er is een fout opgelost waarbij sommige datum waarden worden weer gegeven als tijds tempels in plaats van python datetime-objecten.
  + Er is een fout opgelost waarbij sommige typen worden weer gegeven als dubbele waarden in plaats van gehele getallen.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK voor python v-1.0.21

+ **Nieuwe functies**
  + Met de methode *azureml. core. run. create_children* kan het maken van meerdere onderliggende uitvoeringen met een lage latentie met één aanroep worden gemaakt.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning data prep SDK v 1.1.0

+ **Wijzigingen afbreken**
  + Het concept van het gegevens prep-pakket is afgeschaft en wordt niet meer ondersteund. In plaats van meerdere gegevens stromen in één pakket persistent te maken, kunt u gegevens stromen afzonderlijk blijven gebruiken.
    + Instructies: [gegevens stromen notebook openen en opslaan](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nieuwe functies**
  + Gegevens voorbereiding kan nu kolommen herkennen die overeenkomen met een bepaald semantisch type en die dienovereenkomstig worden gesplitst. De STypes die momenteel wordt ondersteund zijn onder andere: e-mail adres, geografische coördinaten (breedte & lengte graad), IPv4-en IPv6-adressen, telefoon nummer van de VS en post code.
    + Hand leiding: [semantische typen notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data prep ondersteunt nu de volgende bewerkingen om een resulterende kolom te genereren op basis van twee numerieke kolommen: aftrekken, vermenigvuldigen, delen en modulo.
  + U kunt `verify_has_data()` aanroepen in een gegevensstroom om te controleren of de gegevensstroom records zou opleveren als ze worden uitgevoerd.

+ **Oplossingen en verbeteringen voor oplossingen**
  + U kunt nu het aantal te gebruiken bakken opgeven in een histogram voor numerieke kolom profielen.
  + Voor de `read_pandas_dataframe` Transform moet de data frame nu een teken reeks-of byte-type kolom namen hebben.
  + Er is een fout opgelost in de `fill_nulls` Transform, waarbij waarden niet correct zijn ingevuld als de kolom ontbreekt.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK voor python v-1.0.18

 + **Gewijzigde**
   + Het azureml-tensorboard-pakket vervangt azureml-contrib-tensorboard.
   + Met deze release kunt u een gebruikers account instellen op uw beheerde Compute-Cluster (amlcompute), terwijl u het maakt. U kunt dit doen door deze eigenschappen door te geven in de inrichtings configuratie. Meer informatie kunt u vinden in de [SDK-referentie documentatie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning data prep SDK v 1.0.17

+ **Nieuwe functies**
  + Biedt nu ondersteuning voor het toevoegen van twee numerieke kolommen om een resulterende kolom te genereren met behulp van de expressie taal.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De documentatie en parameter controle voor random_split zijn verbeterd.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning data prep SDK v 1.0.16

+ **Bug oplossen**
  + Er is een Service-Principal-verificatie probleem opgelost dat is veroorzaakt door een wijziging in de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK voor python v-1.0.17

+ **Nieuwe functies**

  + Azure Machine Learning biedt nu ondersteuning voor de eerste klasse voor populaire DNN Framework-ketens. U kunt met behulp van [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -klasse-gebruikers eenvoudig ketting modellen trainen en implementeren.
    + Meer informatie over het [uitvoeren van gedistribueerde training met ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Meer informatie over het [uitvoeren van afstemming tuning with Chainer met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pijp lijnen hebben de mogelijkheid om een pijplijn uitvoering te activeren op basis van wijzigingen in de gegevens opslag. Het [notitie blok](https://aka.ms/pl-schedule) voor de pijp lijn planning is bijgewerkt om deze functie te presen teren.

+ **Oplossingen en verbeteringen voor oplossingen**
  + We hebben ondersteuning toegevoegd in Azure Machine Learning-pijp lijnen voor het instellen van de eigenschap source_directory_data_store op een gewenste gegevens opslag (zoals een Blob-opslag) op [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) die worden geleverd aan de [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). In de volgende stappen wordt gebruikgemaakt van Azure File Store als back-upgegevens opslag, wat kan leiden tot beperking van problemen wanneer een groot aantal stappen gelijktijdig wordt uitgevoerd.

### <a name="azure-portal"></a>Azure-portal

+ **Nieuwe functies**
  + Nieuwe functie voor slepen en neerzetten van tabel editor voor rapporten. Gebruikers kunnen een kolom van het ene naar het tabel gebied slepen waarin een voor beeld van de tabel wordt weer gegeven. De kolommen kunnen opnieuw worden gerangschikt.
  + Viewer voor nieuwe logboek bestanden
  + Koppelingen naar experiment-uitvoeringen, compute, modellen, afbeeldingen en implementaties op het tabblad activiteiten

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning data prep SDK v 1.0.15

+ **Nieuwe functies**
  + Data prep ondersteunt nu het schrijven van bestands stromen vanuit een gegevensstroom. Biedt ook de mogelijkheid om de naam van de bestands stroom te bewerken om nieuwe bestands namen te maken.
    + Instructies: [werken met een notitie blok met bestands stromen](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde prestaties van t-Digest voor grote gegevens sets.
  + Data prep ondersteunt nu het lezen van gegevens van een DataPath.
  + Eén hot encoding-code ring werkt nu op Booleaanse en numerieke kolommen.
  + Andere diverse fout oplossingen.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK voor python v-1.0.15

+ **Nieuwe functies**
  + Azure Machine Learning pijp lijnen hebben AzureBatchStep ([notebook](https://aka.ms/pl-azbatch)), HyperDriveStep (notebook) en op tijd gebaseerde plannings functionaliteit ([notebook](https://aka.ms/pl-schedule)) toegevoegd.
  +  DataTranferStep is bijgewerkt met Azure SQL Server en Azure data base for PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Gewijzigde**
  + Afgeschafte `PublishedPipeline.get_published_pipeline` voor de `PublishedPipeline.get`.
  + Afgeschafte `Schedule.get_schedule` voor de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning data prep SDK v 1.0.12

+ **Nieuwe functies**
  + Data prep ondersteunt nu het lezen van een Azure-SQL database met behulp van gegevens opslag.

+ **Gewijzigde**
  + Verbeterde geheugen prestaties van bepaalde bewerkingen op grote gegevens.
  + `read_pandas_dataframe()` moet nu `temp_folder` worden opgegeven.
  + De eigenschap `name` op `ColumnProfile` is afgeschaft. gebruik in plaats daarvan `column_name`.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK voor python v-1.0.10

+ **Wijzigingen**:
  + Azure ML SDK heeft geen Azure-cli-pakketten meer als afhankelijkheid. Met name Azure-cli-core en Azure-cli-profiel afhankelijkheden zijn verwijderd uit de azureml-kern. Dit zijn de wijzigingen die invloed hebben op de gebruiker:
    + Als u ' AZ login ' uitvoert en vervolgens met behulp van azureml-SDK, zal de SDK de browser of het apparaat in het logboek nog een keer aanmelden. Er wordt geen referentie status gebruikt die is gemaakt met de melding AZ login.
    + Voor Azure CLI-verificatie, zoals het gebruik van AZ login, gebruikt u de klasse _azureml. core. Authentication. AzureCliAuthentication_ . Voor Azure CLI-verificatie kunt u met _PIP Azure-cli installeren_ in de python-omgeving waar u de azureml-SDK hebt geïnstalleerd.
    + Als u "AZ login" uitvoert met een service-principal voor Automation, raden we u aan om de klasse _azureml. core. Authentication. ServicePrincipalAuthentication_ te gebruiken, aangezien azureml-SDK geen referentie status gebruikt die is gemaakt door Azure cli.

+ **Oplossingen voor fouten**: deze release bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning data prep SDK v 1.0.8

+ **Oplossingen voor oplossingen**
  + De prestaties van het ophalen van gegevens profielen is verbeterd.
  + Vaste kleine fouten met betrekking tot fout rapportage.

### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ Nieuwe grafiek ervaring voor slepen en neerzetten voor rapporten. Gebruikers kunnen een kolom of kenmerk van het ene naar het grafiek gebied slepen, waar het systeem automatisch een geschikt grafiek type voor de gebruiker selecteert op basis van het type gegevens. Gebruikers kunnen het grafiek type wijzigen in andere toepasselijke typen of extra kenmerken toevoegen.

    Ondersteunde grafiek typen:
    - Lijn diagram
    - Histogram
    - Gestapeld staaf diagram
    - Boxplot
    - Spreidings plot
    - Ballon tekening
+ De portal genereert nu dynamisch rapporten voor experimenten. Wanneer een gebruiker een uitvoering naar een experiment verzendt, wordt er automatisch een rapport gegenereerd met vastgelegde metrische gegevens en grafieken om vergelijking te maken tussen de verschillende uitvoeringen.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK voor python v-1.0.8

+ **Oplossingen voor fouten**: deze release bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning data prep SDK v 1.0.7

+ **Nieuwe functies**
  + Verbeteringen in Data Store (beschreven in [Data Store procedures-to-Guide](https://aka.ms/aml-data-prep-datastore-nb))
    + De mogelijkheid om te lezen uit en te schrijven naar een Azure-bestands share en ADLS-gegevens opslag in scale-up.
    + Bij het gebruik van data stores ondersteunt gegevens voorbereiding nu het gebruik van Service-Principal-verificatie in plaats van interactieve authenticatie.
    + Er is ondersteuning toegevoegd voor wasb-en wasbs-url's.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning data prep SDK v 1.0.6

+ **Oplossingen voor oplossingen**
  + Probleem opgelost met het lezen van open bare Azure Blob-containers die zijn gelezen op Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK voor python v-1.0.6
+ **Oplossingen voor fouten**: deze release bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning data prep SDK v 1.0.4

+ **Nieuwe functies**
  + de functie `to_bool` maakt nu het mogelijk dat niet-overeenkomende waarden worden geconverteerd naar fout waarden. Dit is het nieuwe standaard gedrag voor het vergelijken van `to_bool` en `set_column_types`, terwijl het vorige standaard gedrag de niet-overeenkomende waarden converteerde naar onwaar.
  + Bij het aanroepen van `to_pandas_dataframe`is er een nieuwe optie voor het interpreteren van Null/ontbrekende waarden in numerieke kolommen als NaN.
  + De mogelijkheid is toegevoegd om het retour type van sommige expressies te controleren om ervoor te zorgen dat de type consistentie en het vroegst mislukken.
  + U kunt nu `parse_json` aanroepen om waarden in een kolom te parseren als JSON-objecten en deze in meerdere kolommen uit te breiden.

+ **Oplossingen voor oplossingen**
  + Er is een fout opgelost waardoor `set_column_types` is vastgelopen in Python 3.5.2.
  + Er is een fout opgelost die is vastgelopen tijdens het verbinden met de gegevens opslag met behulp van een AML-afbeelding.

+ **Updates**
  * Voor [beelden van notitie blokken](https://aka.ms/aml-data-prep-notebooks) voor het aan de slag gaan met zelf studies, casestudy's en hand leidingen.

## <a name="2018-12-04-general-availability"></a>2018-12-04: algemene beschikbaarheid

Azure Machine Learning is nu algemeen beschikbaar.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning-Computing
In deze versie kondigt wij een nieuwe beheerde reken ervaring aan via de [Azure machine learning Compute](how-to-set-up-training-targets.md#amlcompute). Dit Compute doel vervangt Azure Batch AI Compute for Azure Machine Learning.

Dit Compute-doel:
+ Wordt gebruikt voor model training en batch deinterferentie/Score
+ Is een single-to-Compute op meerdere knoop punten
+ Worden de Cluster beheer-en taak planning voor de gebruiker
+ Standaard automatisch schalen
+ Ondersteuning voor zowel CPU-als GPU-resources
+ Maakt gebruik van Vm's met lage prioriteit mogelijk voor gereduceerde kosten

Azure Machine Learning-Computing kunnen worden gemaakt in Python, met behulp van Azure portal of de CLI. Deze moet worden gemaakt in de regio van uw werkruimte en kan niet worden gekoppeld aan een andere werkruimte. Dit Compute-doel gebruikt een docker-container voor uw uitvoering en pakt uw afhankelijkheden toe om dezelfde omgeving te repliceren op al uw knoop punten.

> [!Warning]
> We raden u aan om een nieuwe werkruimte voor het gebruik van Azure Machine Learning-Computing. Er is een externe kans dat gebruikers bij het maken van Azure Machine Learning-Computing van een bestaande werkruimte ziet mogelijk een fout. Bestaande compute in uw werkruimte moet blijven werken niet beïnvloed.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning-SDK voor Python v1.0.2
+ **Wijzigingen afbreken**
  + Met deze release, worden ondersteuning voor het maken van een virtuele machine van Azure Machine Learning verwijderd. U kunt nog steeds een bestaande VM-cloud koppelen of een externe on-premises server.
  + Ondersteuning voor BatchAI, die allemaal moeten worden ondersteund door Azure Machine Learning-Computing nu worden ook verwijderd.

+ **Nieuw**
  + Voor machine learning-pijplijnen:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Vernieuwd**
  + Voor machine learning-pijplijnen:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepteert nu runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) wordt nu gekopieerd van en naar een SQL-gegevens bron
    + Functionaliteit in SDK maken en bijwerken van schema's voor het uitvoeren van gepubliceerde pijplijnen plannen

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning-Dataprep SDK v0.5.2
+ **Wijzigingen afbreken**
  * de naam van `SummaryFunction.N` is gewijzigd in `SummaryFunction.Count`.

+ **Oplossingen voor oplossingen**
  * Gebruik het meest recente AML run-token bij het lezen van en schrijven naar gegevens opslag op externe uitvoeringen. Voorheen als het Token voor het uitvoeren van AML wordt bijgewerkt in Python, zal de runtime gegevensvoorbereiding niet worden bijgewerkt met de bijgewerkte AML uitvoeren Token.
  * Aanvullende duidelijker foutberichten
  * to_spark_dataframe () zal niet langer vastlopen wanneer Spark `Kryo` serialisatie gebruikt
  * Aantal waarden Inspector kan nu meer dan 1000 unieke waarden weergeven
  * Willekeurige Split mislukt langer als de oorspronkelijke gegevensstroom beschikt niet over een naam

+ **Meer informatie**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs- en -laptops
+ ML-pijplijnen
  + Nieuwe en bijgewerkte notitie blokken om aan de slag te gaan met pijp lijnen, batch-scoping en voor beelden van stijl overdracht: https://aka.ms/aml-pipeline-notebooks
  + Meer informatie over [het maken van uw eerste pijp lijn](how-to-create-your-first-pipeline.md)
  + Meer informatie over het [uitvoeren van batch voorspellingen met behulp van pijp lijnen](how-to-use-parallel-run-step.md)
+ Azure Machine Learning Compute-doel
  + Voor [beelden van notitie blokken](https://aka.ms/aml-notebooks) worden nu bijgewerkt met de nieuwe beheerde reken kracht.
  + [Meer informatie over deze compute](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ [Azure machine learning Compute](how-to-set-up-training-targets.md#amlcompute) types maken en beheren in de portal.
+ Bewaak het quotum gebruik en [vraag het quotum](how-to-manage-quotas.md) voor de Azure machine learning compute.
+ Status van Azure Machine Learning Compute-cluster in realtime weer geven.
+ Virtual network-ondersteuning is toegevoegd voor het maken van Azure Machine Learning-Computing en Azure Kubernetes Service.
+ Voer de gepubliceerde pijp lijnen opnieuw uit met de bestaande para meters.
+ Nieuwe [geautomatiseerde machine learning grafieken](how-to-understand-automated-ml.md) voor classificatie modellen (Lift, winst, kalibratie, grafiek van functie urgentie met model uitleg) en regressie modellen (resten en prioriteits grafiek met model uitleg).
+ Pijplijnen kunnen worden weergegeven in Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning-SDK voor Python v0.1.80

+ **Wijzigingen afbreken**
  * de naam ruimte van *azureml. Train. widgets* is verplaatst naar *azureml. widgets*.
  * *azureml. core. compute. AmlCompute* reafschaffing de volgende klassen: *azureml. core. compute. BatchAICompute* en *azureml. core. compute. DSVMCompute*. De laatste klasse wordt verwijderd in toekomstige releases. De klasse AmlCompute heeft nu een eenvoudiger definitie gewoon moet een vm_size en de max_nodes en wordt uw cluster vanaf 0 naar de max_nodes automatisch schalen wanneer een job wordt verzonden. Onze [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zijn bijgewerkt met deze gegevens en u krijgt voor beelden van gebruik te geven. We hopen u, zoals deze vereenvoudiging en nog veel meer interessante functies komen in een latere versie.

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning-Dataprep SDK v0.5.1

Lees voor meer informatie over de SDK voor gegevens voorbereiding door [referentie documenten](https://aka.ms/data-prep-sdk)te lezen.
+ **Nieuwe functies**
   * Een nieuwe DataPrep CLI DataPrep-pakketten uitvoeren en weergeven van het gegevensprofiel van de voor een gegevensset of gegevensstroom gemaakt
   * Opnieuw ontworpen API SetColumnType de bruikbaarheid verbeteren
   * Hernoemd smart_read_file naar auto_read_file
   * Bevat nu scheeftrekken en kurtosis in het profiel van gegevens
   * Met steekproeven toepassing stratificatie kunt steekproef
   * Van het zip-bestanden met CSV-bestanden kan lezen
   * Kan rijen met gegevens sets opsplitsen met wille keurige splitsing (bijvoorbeeld in de test-trein sets)
   * Kan alle kolom gegevens typen van een gegevens stroom of een Data profiel verkrijgen door `.dtypes` aan te roepen
   * Kan het aantal rijen ophalen van een gegevens stroom of een Data profiel door het aanroepen van `.row_count`

+ **Oplossingen voor oplossingen**
   * Vaste lang naar dubbele conversie
   * Vaste assert nadat een kolom toevoegen
   * Er is een probleem opgelost met FuzzyGrouping, waar groepen niet in sommige gevallen detecteren zou
   * Vaste sorteerfunctie rekening houden met meerdere kolommen sorteervolgorde
   * Vaste en/of uitdrukkingen die vergelijkbaar zijn met de manier waarop `pandas` deze verwerkt
   * Vaste lezen van dbfs pad
   * Begrijpelijker foutberichten
   * Er is nu geen fout meer bij het lezen van het externe Compute-doel met behulp van een AML-token
   * Nu mislukt niet meer op Linux-DSVM
   * Nu niet meer loopt vast bij niet-tekenreekswaarden in tekenreeks predicaten zijn
   * Nu assertion fouten worden verwerkt wanneer gegevensstroom correct moet mislukken
   * Biedt nu ondersteuning voor dbutils gekoppeld opslaglocaties op Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure-portal
De Azure Portal voor Azure Machine Learning heeft de volgende updates:
  * Een nieuw tabblad met **pijp lijnen** voor gepubliceerde pijp lijnen.
  * Er is ondersteuning toegevoegd voor het koppelen van een bestaand HDInsight-cluster als een compute-doel.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning-SDK voor Python v0.1.74

+ **Wijzigingen afbreken**
  * \* Werk ruimte. compute_targets, gegevens opslag, experimenten, installatie kopieën, modellen en *webservices* zijn eigenschappen in plaats van methoden. Vervang bijvoorbeeld *Workspace. compute_targets ()* door *Workspace. compute_targets*.
  * *Run. get_context* afschaffs *Run. get_submitted_run*. De laatste methode wordt verwijderd in toekomstige releases.
  * *PipelineData* -klasse verwacht nu een Data Store-object als para meter in plaats van datastore_name. Op dezelfde manier accepteert de *pijp lijn* default_datastore in plaats van default_datastore_name.

+ **Nieuwe functies**
  * In het [voorbeeld notitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) voor Azure machine learning pijp lijnen worden nu mpi-stappen gebruikt.
  * De widget RunDetails voor Jupyter-notitieblokken wordt bijgewerkt om weer te geven van een visualisatie van de pijplijn.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning-Dataprep SDK v0.4.0

+ **Nieuwe functies**
  * Aantal toegevoegd aan Gegevensprofiel
  * Aantal waarden en het Histogram is nu beschikbaar
  * Meer percentielen in Gegevensprofiel
  * De mediaan is beschikbaar in samenvatten
  * Python 3.7 wordt nu ondersteund.
  * Wanneer u een gegevensstroom met gegevensopslag bij een DataPrep-pakket opslaat, de datastore-gegevens persistent kunnen worden gemaakt als onderdeel van het DataPrep-pakket
  * Schrijven naar het gegevensarchief wordt nu ondersteund.

+ **Bug opgelost**
  * 64-bits niet-ondertekende integers overschrijdingen worden nu goed afgehandeld op Linux
  * Vaste onjuist tekstlabel voor bestanden in smart_read tekst zonder opmaak
  * Kolom van het tekenreekstype wordt nu weergegeven in de weergave voor metrische gegevens
  * Aantal is nu opgelost om ValueKinds toegewezen aan één veldtype in plaats van afzonderlijke bestanden weer te geven
  * Write_to_csv mislukt langer wanneer het pad is opgegeven als een tekenreeks
  * Wanneer u vervangen, mislukken waardoor "zoeken" leeg niet meer

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning-SDK voor Python v0.1.68

+ **Nieuwe functies**
  * Ondersteuning voor meerdere tenants bij het maken van een nieuwe werk ruimte.

+ **Fouten opgelost**
  * U hoeft niet langer de pynacl-bibliotheek versie vast te maken wanneer u de webservice implementeert.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning-Dataprep SDK v0.3.0

+ **Nieuwe functies**
  * Methode transform_partition_with_file(script_path), waarmee gebruikers om door te geven in het pad van een Python-bestand uit te voeren toegevoegd

## <a name="2018-10-01"></a>10-01-2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning-SDK voor Python v0.1.65
[Versie 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) bevat nieuwe functies, meer documentatie, fout oplossingen en meer voor [beelden van notitie blokken](https://aka.ms/aml-notebooks).

Zie [de lijst met bekende problemen](resource-known-issues.md) met bekende bugs en tijdelijke oplossingen.

+ **Wijzigingen afbreken**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services geretourneerde woordenlijst, lijst met eerder zijn geretourneerd. Zie de documentatie van [azureml. core. workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API.

  * Geautomatiseerde Machine Learning verwijderd genormaliseerde gemiddelde vierkant fout van de primaire metrische gegevens.

+ **HyperDrive**
  * Verschillende HyperDrive oplossingen voor Bayesiaanse, prestatieverbeteringen voor het aanroepen van de metrische gegevens ophalen
  * Tensorflow 1.10 upgrade van 1.9
  * Optimalisatie van de docker-installatiekopie voor koude start.
  * Taken rapporteren nu de juiste status, zelfs als ze worden afgesloten met een andere fout code dan 0.
  * RunConfig validatie van het kenmerk in de SDK.
  * HyperDrive uitvoeren object ondersteunt annuleren die vergelijkbaar is met een reguliere uitvoeren: Er is geen hoeven te worden doorgegeven parameters.
  * Widget verbeteringen voor het onderhouden van de status van de waarden van de vervolgkeuzelijst voor gedistribueerde wordt uitgevoerd en HyperDrive wordt uitgevoerd.
  * TensorBoard en andere bestanden bieden ondersteuning voor logboek vast voor de Parameter-server.
  * Intel(R) MPI ondersteuning op servicezijde.
  * Bugfix naar de parameter afstemming voor gedistribueerde oplossing uitvoeren tijdens de validatie in BatchAI.
  * Context Manager identificeert nu het primaire exemplaar.

+ **Azure Portal ervaring**
  * log_table() en log_row() worden ondersteund in de details van de uitvoering.
  * Automatisch grafieken maken voor tabellen en rijen met 1, 2 of 3 numerieke kolommen en een optionele kolom categorische.

+ **Automatische Machine Learning**
  * Verbeterde foutafhandeling en documentatie
  * Voor het uitvoeren van de eigenschap ophalen vast prestatieproblemen.
  * Vaste blijven uitvoeren probleem.
  * Opgeloste problemen met :::no-loc text="ensembling"::: herhalingen.
  * Vaste training verkeerd-om fouten op MAC OS.
  * Downsampling macro gemiddelde pull-aanvraag/ROC-curve in aangepaste validatiescenario.
  * Logica van de extra index verwijderd.
  * Filter verwijderd uit get_output API.

+ **Pijplijnen**
  * Een methode Pipeline.publish() voor het publiceren van een pijplijn rechtstreeks, zonder een uitvoering eerst toegevoegd.
  * Er is een methode PipelineRun. get_pipeline_runs () toegevoegd voor het ophalen van de pijplijn uitvoeringen die zijn gegenereerd op basis van een gepubliceerde pijp lijn.

+ **Project brainwave**
  * Bijgewerkte ondersteuning voor nieuwe AI-modellen op FPGA's beschikbaar.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning-Dataprep SDK v0.2.0
[Versie 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) bevat de volgende functies en oplossingen voor fouten:

+ **Nieuwe functies**
  * Ondersteuning voor het coderen van één hot
  * Ondersteuning voor kwantiel transformeren

+ **Bug opgelost:**
  * Werkt met elke versie Tornado hoeft te downgraden van uw versie Tornado
  * Waarde telt voor alle waarden, niet alleen de top drie

## <a name="2018-09-public-preview-refresh"></a>2018-09 (preview-versie vernieuwen)

Een nieuwe, vernieuwde versie van Azure Machine Learning: meer informatie over deze versie: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Volgende stappen

Lees het overzicht voor [Azure Machine Learning](overview-what-is-azure-ml.md).
