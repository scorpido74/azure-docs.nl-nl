---
title: Wat is er nieuw in de release?
titleSuffix: Azure Machine Learning
description: Meer informatie over de nieuwste updates voor Azure Machine Learning en de Python SDK's voor machine learning en data prep.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: b55c351927a56afce697d07f41bfbe668144d68d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475515"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning release notes Azure Machine Learning release notes Azure Machine Learning release notes Azure Machine

Lees in dit artikel meer over Azure Machine Learning-releases.  Ga voor de volledige SDK-referentie-inhoud naar de belangrijkste SDK voor Python-referentiepagina [**van**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Azure Machine Learning.

Bekijk [de lijst met bekende problemen](resource-known-issues.md) om meer te weten te komen over bekende bugs en tijdelijke oplossingen.

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK voor Python v1.2.0

+ **Wijzigingen doorbreken**
  + Drop ondersteuning voor python 2.7

+ **Bug fixes en verbeteringen**
  + **azure-cli-ml**
    + Voegt '--abonnement-id' `az ml model/computetarget/service` toe aan opdrachten in de CLI
    + Ondersteuning toevoegen voor het passeren van cmk-vault_url, key_name en key_version voor de implementatie van door de klant beheerde sleutel voor
  + **azureml-automl-core** 
    + Ingeschakeldaangepaste toerekening met constante waarde voor zowel X- als y-gegevensprognosetaken.
    + Fixed the issue in with showing error messages to user.    
  + **azureml-automl-runtime**
    + Het probleem opgelost met prognoses op de gegevenssets, die korrels met slechts één rij bevatten
    + Verminderde de hoeveelheid geheugen die nodig is voor de prognosetaken.
    + Betere foutberichten toegevoegd als de tijdkolom een onjuiste indeling heeft.
    + Ingeschakeldaangepaste toerekening met constante waarde voor zowel X- als y-gegevensprognosetaken.
  + **azureml-core**
    + Ondersteuning toevoegen voor het laden van ServicePrincipal op omgevingsvariabelen: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID en AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introduceerde een nieuwe `support_multi_line` `Dataset.Tabular.from_delimited_files`parameter in`support_multi_line=False`: Standaard ( ), alle regeleinden, met inbegrip van die in geciteerde veldwaarden, worden geïnterpreteerd als een recordbreak. Het lezen van gegevens op deze manier is sneller en meer geoptimaliseerd voor parallelle uitvoering op meerdere CPU-cores. Het kan echter resulteren in het zwijgen produceren van meer records met verkeerd uitgelijnde veldwaarden. Dit moet worden `True` ingesteld op wanneer bekend is dat de afgebakende bestanden geciteerde regeleinden bevatten.
    + De mogelijkheid toegevoegd om ADLS Gen2 te registreren in de Azure Machine Learning CLI
    + Omgedoopt tot parameter 'fine_grain_timestamp' naar 'timestamp' en parameter 'coarse_grain_timestamp' naar 'partition_timestamp' voor de methode with_timestamp_columns() in TabularDataset om het gebruik van de parameters beter weer te geven.
    + Verhoogde maximale lengte van de experimentnaam tot 255.
  + **azureml-interpret**
    + Bijgewerkt azureml-interpret om de interpretatie-community 0.7.*
  + **azureml-sdk**
    + Overstappen naar afhankelijkheden met compatibele versie Tilde voor de ondersteuning van patchen in pre-release en stabiele releases.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK voor Python v1.1.5

+ **Functieafschaffing**
  + **Python 2.7**
    + Laatste versie ter ondersteuning van python 2.7

+ **Wijzigingen doorbreken**
  + **Semantische versie2.0.0**
    + Beginnend met versie 1.1 Azure ML Python SDK neemt Semantische Versioning 2.0.0. [Hier vindt u meer informatie](https://semver.org/). Alle volgende versies volgen een nieuw nummeringsschema en een semantisch versiecontract. 

+ **Bug fixes en verbeteringen**
  + **azure-cli-ml**
    + Wijzig de naam van het eindpunt CLI van 'az ml endpoint aks' naar 'az ml endpoint realtime' voor consistentie.
    + update CLI-installatie-instructies voor stabiele en experimentele tak CLI
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
  + **azureml-automl-core**
    + De batchmodus-gevolgtrekking (meerdere rijen één keer nemen) ingeschakeld voor automl ONNX-modellen
    + Verbeterde detectie van de frequentie op de gegevenssets, zonder gegevens of met onregelmatige gegevenspunten
    + Toegevoegd de mogelijkheid om gegevenspunten niet voldoen aan de dominante frequentie te verwijderen.
    + Wijzigde de invoer van de constructor om een lijst met opties te nemen om de toerekeningsopties voor overeenkomstige kolommen toe te passen.
    + De foutregistratie is verbeterd.
  + **azureml-automl-runtime**
    + Het probleem opgelost met de fout die werd gegenereerd als de korrel die niet aanwezig was in de trainingsset in de testset verscheen
    + De y_query vereiste verwijderd tijdens het scoren op de prognoseservice
    + Fixed the issue with forecasting when the data set contains short grains with long time gaps.
    + Fixed the issue when the auto max horizon is turned on and the date column contains dates in form of strings. Er zijn juiste conversie- en foutberichten toegevoegd voor wanneer conversie tot op heden niet mogelijk is
    + Native NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor FileCacheStore (gebruikt voor lokale AutoML-uitvoeringen)
    + Fixed a bug where failed child runs could get stuck in Running state.
    + Verhoogde snelheid van featurization.
    + Vaste de frequentie controle tijdens het scoren, nu de prognoses taken vereisen geen strikte frequentie gelijkwaardigheid tussen trein en test set.
    + Wijzigde de invoer van de constructor om een lijst met opties te nemen om de toerekeningsopties voor overeenkomstige kolommen toe te passen.
    + Vaste fouten met betrekking tot lag type selectie.
    + Fixed the unclassified error raised on the data sets, having grains with the single row Fixed the unclassified error raised on the data sets, having grains with the single row
    + Fixed the issue with frequency detection slowness.
    + Hiermee wordt een bug in AutoML-uitzonderingsverwerking verholpen waardoor de werkelijke reden voor het niet vervangen van een training door een AttributeError is veroorzaakt.
  + **azureml-cli-common**
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
  + **azureml-contrib-mir**
    + Voegt functionaliteit toe in de klasse MirWebservice om het Access-token op te halen
    + Token auth voor MirWebservice standaard gebruiken tijdens MirWebservice.run() call - Alleen vernieuwen als aanroep mislukt
    + Mir webservice implementatie vereist nu een goede Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] in plaats van [Ds2v2, A2v2, en F16] respectievelijk.
  + **azureml-contrib-pipeline-steps**
    + Optionele parameter side_inputs toegevoegd aan ParallelRunStep. Deze parameter kan worden gebruikt om de map op de container te monteren. Momenteel ondersteunde typen zijn DataReference en PipelineData.
    + Parameters die in ParallelRunConfig worden doorgegeven, kunnen nu worden overschreven door pijplijnparameters door te geven. Nieuwe pijplijnparameters die worden ondersteund aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van eerdere release).
  + **azureml-core**
    + Geïmplementeerde AzureML Webservices wordt `INFO` nu standaard logboekregistratie uitgevoerd. Dit kan worden geregeld `AZUREML_LOG_LEVEL` door de omgevingsvariabele in te stellen in de geïmplementeerde service.
    + Python sdk maakt gebruik van discovery service om 'api' endpoint te gebruiken in plaats van 'pipelines'.
    + Swap naar de nieuwe routes in alle SDK-gesprekken
    + Wijzigt de routering van oproepen naar de ModelManagementService naar een nieuwe uniforme structuur
      + Werkruimte-updatemethode openbaar gemaakt.
      + Toegevoegd image_build_compute parameter in werkruimte update methode om de gebruiker het bijwerken van de compute voor image build
    +  Afschrijvingsberichten toegevoegd aan de oude profileringsworkflow. Vaste profilering cpu en geheugen grenzen
    + RSection toegevoegd als onderdeel van Milieu om R-taken uit te voeren
    +  Toegevoegd validatie `Dataset.mount` om fouten te maken wanneer de bron van de gegevensset niet toegankelijk is of geen gegevens bevat.
    + Toegevoegd `--grant-workspace-msi-access` als extra parameter voor de Datastore CLI voor het registreren van Azure Blob Container waarmee u Blob Container registreren die zich achter een VNet bevindt
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
    + Het probleem opgelost in aks.py _deploy
    + Valideert de integriteit van modellen die worden geüpload om problemen met de stille opslag te voorkomen.
    + De gebruiker kan nu een waarde opgeven voor de auth-toets bij het regenereren van sleutels voor webservices.
    + Fixed bug where hoofdletters cannot be used as dataset's input name Fixed bug where hoofdletters cannot be used as dataset's input name Fixed bug where hoofdletters cannot be used as dataset's input name Fixed bug where hoofdletters
  + **azureml-standaardinstellingen**
    + `azureml-dataprep`zal nu worden geïnstalleerd `azureml-defaults`als onderdeel van . Het is niet langer nodig om dataprep[fuse] handmatig te installeren op compute targets om datasets te monteren.
  + **azureml-interpret**
    + Bijgewerkt azureml-interpret om de interpretatie-community 0.6.*
    + Bijgewerkt azureml-interpreteren afhankelijk van interpret-community 0.5.0
    + Azureml-stijl uitzonderingen toegevoegd op azureml-interpretatie
    + Fixed DeepScoringExplainer serialization for keras models Fixed DeepScoringExplainer serialization for keras models Fixed DeepScoringExplainer serialization for keras models Fixed Deep
  + **azureml-mlflow**
    + Ondersteuning voor soevereine wolken toevoegen aan azureml.mlflow
  + **azureml-pipeline-core**
    + Het scorenotitieblok van pijplijnbatchen maakt nu gebruik van ParallelRunStep
    + Een bug opgelost waarbij de resultaten van PythonScriptStep onjuist kunnen worden hergebruikt, ondanks het wijzigen van de lijst met argumenten
    + Toegevoegd de mogelijkheid om het type kolommen in te stellen bij het aanroepen van de parse_* methoden op`PipelineOutputFileDataset`
  + **azureml-pipeline-stappen**
    + Verplaatst `AutoMLStep` naar `azureml-pipeline-steps` het pakket. Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
    + Documentbeschrijving somwerksetvoor gegevensset als PythonScriptStep-invoer
  + **azureml-tensorboard**
    + bijgewerkt azureml-tensorboard ter ondersteuning van tensorflow 2.0
    + Het juiste poortnummer weergeven wanneer u een aangepaste Tensorboard-poort gebruikt op een Compute Instance
  + **azureml-train-automl-client**
    + Probleem opgelost waarbij bepaalde pakketten op externe versies kunnen worden geïnstalleerd.
    + fixed FeaturizationConfig overschrijvend probleem dat aangepaste featurization config filtert.
  + **azureml-train-automl-runtime**
    + Het probleem met frequentiedetectie in de externe uitvoeringen opgelost
    + Ik `AutoMLStep` heb `azureml-pipeline-steps` de in het pakket verplaatst. Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Ondersteuning van PyTorch versie 1.4 in de PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.2rc0 (Pre-release)

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + De batchmodus-gevolgtrekking (meerdere rijen één keer nemen) ingeschakeld voor automl ONNX-modellen
    + Verbeterde detectie van de frequentie op de gegevenssets, zonder gegevens of met onregelmatige gegevenspunten
    + Toegevoegd de mogelijkheid om gegevens punten niet voldoen aan de dominante frequrncy te verwijderen.
  + **azureml-automl-runtime**
    + Het probleem opgelost met de fout die werd gegenereerd als de korrel die niet aanwezig was in de trainingsset in de testset verscheen
    + De y_query vereiste verwijderd tijdens het scoren op de prognoseservice
  + **azureml-contrib-mir**
    + Voegt functionaliteit toe in de klasse MirWebservice om het Access-token op te halen
  + **azureml-core**
    + Geïmplementeerde AzureML Webservices wordt `INFO` nu standaard logboekregistratie uitgevoerd. Dit kan worden geregeld `AZUREML_LOG_LEVEL` door de omgevingsvariabele in te stellen in de geïmplementeerde service.
    + Fix iterating `Dataset.get_all` on to return all datasets registered with the workspace.
    + Foutbericht verbeteren wanneer ongeldig `path` type wordt doorgegeven aan argument van API's voor het maken van gegevenssets.
    + Python sdk maakt gebruik van discovery service om 'api' endpoint te gebruiken in plaats van 'pipelines'.
    + Swap naar de nieuwe routes in alle SDK-gesprekken
    + Wijzigt de routering van oproepen naar de ModelManagementService naar een nieuwe uniforme structuur
      + Werkruimte-updatemethode openbaar gemaakt.
      + Toegevoegd image_build_compute parameter in werkruimte update methode om de gebruiker het bijwerken van de compute voor image build
    +  Afschrijvingsberichten toegevoegd aan de oude profileringsworkflow. Vaste profilering cpu en geheugen grenzen
  + **azureml-interpret**
    + azureml-interpret bijwerken om de interpretatie-community 0.6.*
  + **azureml-mlflow**
    + Ondersteuning voor soevereine wolken toevoegen aan azureml.mlflow
  + **azureml-pipeline-stappen**
    + Verplaatst `AutoMLStep` naar `azureml-pipeline-steps package`de . Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
  + **azureml-train-automl-client**
    + Probleem opgelost waarbij bepaalde pakketten op externe versies kunnen worden geïnstalleerd.
  + **azureml-train-automl-runtime**
    + Het probleem met frequentiedetectie in de externe uitvoeringen opgelost
    + Verplaatst `AutoMLStep` naar `azureml-pipeline-steps package`de . Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Verplaatst `AutoMLStep` naar `azureml-pipeline-steps package`de . Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.1rc0 (Pre-release)

+ **Bug fixes en verbeteringen**
  + **azure-cli-ml**
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
  + **azureml-automl-core**
    + De foutregistratie is verbeterd.
  + **azureml-automl-runtime**
    + Fixed the issue with forecasting when the data set contains short grains with long time gaps.
    + Fixed the issue when the auto max horizon is turned on and the date column contains dates in form of strings. We hebben een juiste conversie en verstandige fout toegevoegd als conversie tot op heden niet mogelijk is
    + Native NumPy en SciPy gebruiken voor het serialiseren en deserialiseren van tussenliggende gegevens voor FileCacheStore (gebruikt voor lokale AutoML-uitvoeringen)
    + Fixed a bug where failed child runs could get stuck in Running state.
  + **azureml-cli-common**
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
  + **azureml-core**
    + Toegevoegd `--grant-workspace-msi-access` als extra parameter voor de Datastore CLI voor het registreren van Azure Blob Container waarmee u Blob Container registreren die zich achter een VNet bevindt
    + Single instance profiling werd vastgesteld om een aanbeveling te produceren en werd beschikbaar gesteld in core sdk.
    + Het probleem opgelost in aks.py _deploy
    + Valideert de integriteit van modellen die worden geüpload om problemen met de stille opslag te voorkomen.
  + **azureml-interpret**
    + azureml-stijl uitzonderingen toegevoegd op azureml-interpretatie
    + vaste DeepScoringExplainer serialisatie voor keras modellen
  + **azureml-pipeline-core**
    + Het scorenotitieblok van pijplijnbatchen maakt nu gebruik van ParallelRunStep
  + **azureml-pipeline-stappen**
    + Ik `AutoMLStep` heb `azureml-pipeline-steps` de in het pakket verplaatst. Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
  + **azureml-contrib-pipeline-steps**
    + Optionele parameter side_inputs toegevoegd aan ParallelRunStep. Deze parameter kan worden gebruikt om de map op de container te monteren. Momenteel ondersteunde typen zijn DataReference en PipelineData.
  + **azureml-tensorboard**
    + bijgewerkt azureml-tensorboard ter ondersteuning van tensorflow 2.0
  + **azureml-train-automl-client**
    + fixed FeaturizationConfig overschrijvend probleem dat aangepaste featurization config filtert.
  + **azureml-train-automl-runtime**
    + Ik `AutoMLStep` heb `azureml-pipeline-steps` de in het pakket verplaatst. Afgeschaft de `AutoMLStep` binnen `azureml-train-automl-runtime`.
  + **azureml-train-core**
    + Ondersteuning van PyTorch versie 1.4 in de PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK voor Python v1.1.0rc0 (Pre-release)

+ **Wijzigingen doorbreken**
  + **Semantische versie2.0.0**
    + Beginnend met versie 1.1 Azure ML Python SDK neemt Semantische Versioning 2.0.0. [Hier vindt u meer informatie](https://semver.org/). Alle volgende versies volgen een nieuw nummeringsschema en een semantisch versiecontract. 
  
+ **Bug fixes en verbeteringen**
  + **azureml-automl-runtime**
    + Verhoogde snelheid van featurization.
    + Fixed the frequency check during scoring, now in the forecasting tasks we do not require strict frequency equivalent between train and test set.
  + **azureml-core**
    + De gebruiker kan nu een waarde opgeven voor de auth-toets bij het regenereren van sleutels voor webservices.
  + **azureml-interpret**
    + Bijgewerkt azureml-interpreteren afhankelijk van interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Een bug opgelost waarbij de resultaten van PythonScriptStep onjuist kunnen worden hergebruikt, ondanks het wijzigen van de lijst met argumenten
  + **azureml-pipeline-stappen**
    + Documentbeschrijving somwerksetvoor gegevensset als PythonScriptStep-invoer
  + **azureml-contrib-pipeline-steps**
    + Parameters die in ParallelRunConfig worden doorgegeven, kunnen nu worden overschreven door pijplijnparameters door te geven. Nieuwe pijplijnparameters die worden ondersteund aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count en aml_process_count_per_node maken al deel uit van eerdere release).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK voor Python v1.0.85

+ **Nieuwe functies**
  + **azureml-core**
    + De huidige kerngebruik s- en quotabeperking voor AmlCompute-resources in een bepaalde werkruimte en abonnement
  
  + **azureml-contrib-pipeline-steps**
    + Gebruiker in staat stellen om tabelgegevensset door te geven als tussenresultaat van vorige stap naar parallelloopstap

+ **Bug fixes en verbeteringen**
  + **azureml-automl-runtime**
    + Verwijderde de vereiste van y_query kolom in de aanvraag voor de geïmplementeerde prognoseservice. 
    + De 'y_query' werd verwijderd uit de Dominick's Orange Juice notebook service request sectie.
    + Fixed the bug preventing forecasting on the ployed models, operating on data sets with date time columns.
    + Matthews Correlatiecoëfficiënt toegevoegd als classificatiestatistiek, voor zowel binaire als meerklasseclassificatie.
  + **azureml-contrib-interpret**
    + Verwijderde tekst explainers van azureml-contrib-interpreteren als tekst uitleg is verplaatst naar de interpretatie-tekst repo die binnenkort zal worden vrijgegeven.
  + **azureml-core**
    + Gegevensset: het gebruik voor bestandsgegevensset is niet langer afhankelijk van numpy en panda's die in de python env moeten worden geïnstalleerd.
    + LocalWebservice.wait_for_deployment() gewijzigd om de status van de lokale Docker-container te controleren voordat u het eindpunt van de status probeert te pingen, waardoor de hoeveelheid tijd die nodig is om een mislukte implementatie te melden, aanzienlijk wordt verminderd.
    + Fixed the initialization of an internal property used in LocalWebservice.reload() when the service object is created from an existing deployment using the LocalWebservice() constructor.
    + Bewerkte foutmelding voor verduidelijking.
    + Added a new method called get_access_token() to AksWebservice that will return AksServiceAccessToken object, which contains access token, refresh after timestamp, expiry on timestamp and token type. 
    + Afgeschaft e.e.d. bestaande get_token() methode in AksWebservice als de nieuwe methode retourneert alle informatie die deze methode retourneert.
    + Gewijzigde uitvoer van az ml service get-access-token opdracht. Omgedoopt tot token naar accessToken en refreshBy om na te vernieuwen. Added expiryOn and tokenType properties.
    + Vaste get_active_runs
  + **azureml-explain-model**
    + bijgewerkte vormgeving naar 0,33.0 en tolk-community naar 0,4.*
  + **azureml-interpret**
    + bijgewerkte vormgeving naar 0,33.0 en tolk-community naar 0,4.*
  + **azureml-train-automl-runtime**
    + Matthews Correlatiecoëfficiënt toegevoegd als classificatiestatistiek, voor zowel binaire als meerklasseclassificatie.
    + Deprecate preprocess vlag van code en vervangen door featurization -featurization is standaard ingeschakeld

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK voor Python v1.0.83

+ **Nieuwe functies**
  + Gegevensset: Voeg `on_error` `out_of_range_datetime` twee `to_pandas_dataframe` opties toe en om te mislukken wanneer gegevens foutwaarden hebben in plaats van ze te vullen met `None`.
  + Werkruimte: De `hbi_workspace` vlag toegevoegd voor werkruimten met gevoelige gegevens die verdere versleuteling mogelijk maakt en geavanceerde diagnoses op werkruimten uitschakelt. We hebben ook ondersteuning toegevoegd voor het meenemen van uw `cmk_keyvault` eigen `resource_cmk_uri` sleutels voor de bijbehorende Cosmos DB-instantie, door de parameters en parameters op te geven bij het maken van een werkruimte, waardoor een Cosmos DB-exemplaar in uw abonnement wordt gemaakt terwijl u uw werkruimte inricht. [Lees hier meer.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Bug fixes en verbeteringen**
  + **azureml-automl-runtime**
    + Fixed a regression that caused a TypeError to be raised when running AutoML on Python versions below 3.5.4.
  + **azureml-core**
    + Fixed bug `datastore.upload_files` in where relative path `./` that didn't start with was not able to be used.
    + Verwijderde afschaffingsberichten toegevoegd voor alle codepaden van de afbeeldingsklasse
    + Fixed Model Management URL constructie for Mooncake region.
    + Probleem opgelost waarbij modellen met source_dir niet konden worden verpakt voor Azure-functies.    
    + Een optie toegevoegd aan [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) om een afbeelding in het containerregister van AzureML-werkruimte te pushen
    + De SDK bijgewerkt om nieuwe tokenbibliotheek op azure synaps op een back-compatibele manier te gebruiken.
  + **azureml-interpret**
    + Fixed bug where None was returned when no explanations were available for download. Nu verhoogt een uitzondering, matching gedrag elders.
  + **azureml-pipeline-stappen**
    + Niet toegestaan `DatasetConsumptionConfig`passeren `Estimator`s `inputs` naar `Estimator` 's parameter `EstimatorStep`wanneer de zal worden gebruikt in een .
  + **azureml-sdk**
    + De AutoML-client is toegevoegd aan het AzureML-sdk-pakket, waardoor externe AutoML-uitvoeringen kunnen worden ingediend zonder het volledige AutoML-pakket te installeren.
  + **azureml-train-automl-client**
    + Gecorrigeerde uitlijning op console-uitvoer voor automl-uitvoeringen
    + Fixed a bug where incorrect version of pandas may be installed on remote amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK voor Python v1.0.81

+ **Bug fixes en verbeteringen**
  + **azureml-contrib-interpret**
    + de vorm van de afhankelijkheid van de interpretatie van de community uitstellen vanuit azureml-interpret
  + **azureml-core**
    + Compute-doel kan nu worden opgegeven als parameter voor de bijbehorende implementatieconfig-objecten. Dit is met name de naam van het compute-doel dat moet worden geïmplementeerd, niet het SDK-object.
    + CreatedBy-informatie toegevoegd aan model- en serviceobjecten. Kan worden benaderd <var>via .created_by
    + Fixed ContainerImage.run(), die de HTTP-poort van de Docker-container niet correct heeft ingesteld.
    + Optioneel `azureml-dataprep` maken `az ml dataset register` voor cli-opdracht
    + Fixed a `TabularDataset.to_pandas_dataframe` bug where would incorrectly fall back to an alternate reader and print out a warning.
  + **azureml-explain-model**
    + de vorm van de afhankelijkheid van de interpretatie van de community uitstellen vanuit azureml-interpret
  + **azureml-pipeline-core**
    + Nieuwe pijplijnstap `NotebookRunnerStep`toegevoegd om een lokaal notitieblok uit te voeren als een stap in de pijplijn.
    + Afgeschafte get_all functies voor gepubliceerde pijplijnen, schema's en pijplijneindpunten verwijderd
  + **azureml-train-automl-client**
    + Begonnen met het afdeisen van data_script als input voor AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK voor Python v1.0.79

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + Verwijderd featurizationConfig te worden geregistreerd
      + Bijgewerkte logboekregistratie om alleen "auto"/"off"/"customized" aan te melden.
  + **azureml-automl-runtime**
    + Extra ondersteuning voor panda's. Series en panda's. Categorisch voor het detecteren van kolomgegevenstype. Voorheen alleen ondersteund numpy.ndarray
      + Gerelateerde codewijzigingen toegevoegd om categorisch dtype correct te verwerken.
    + De interface van de prognosefunctie is verbeterd: de parameter y_pred is optioneel gemaakt. -De docstrings zijn verbeterd.
  + **azureml-contrib-dataset**
    + Fixed a bug where labeled datasets could not be mounted.
  + **azureml-core**
    + Bug fix `Environment.from_existing_conda_environment(name, conda_environment_name)`voor . Gebruiker kan een instantie van omgeving maken die de exacte replica van de lokale omgeving is
    + Gewijzigde methoden voor tijdreeksen `include_boundary=True` standaard.
  + **azureml-train-automl-client**
    + Fixed issue where validation results are not printed when show output is set to false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK voor Python v1.0.76

+ **Wijzigingen doorbreken**
  + Azureml-Train-AutoML upgrade problemen
    + Upgraden naar azureml-train-automl>=1.0.76 van azureml-train-automl<1.0.76 kan leiden tot gedeeltelijke installaties, waardoor sommige automl-importen mislukken. Om dit op te lossen, kunt https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdu het installatiescript uitvoeren dat is gevonden op . Of als u pip direct gebruikt, u:
      + "pip installeren --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + of u de oude versie verwijderen voordat u een upgrade uitvoert
      + "pip verwijderen azureml-train-automl"
      + "pip installeer azureml-train-automl"

+ **Bug fixes en verbeteringen**
  + **azureml-automl-runtime**
    + AutoML houdt nu rekening met zowel echte als valse klassen bij het berekenen van gemiddelde scalaire statistieken voor binaire classificatietaken.
    + Machine learning- en trainingscode in AzureML-AutoML-Core verplaatst naar een nieuw pakket AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Wanneer `to_pandas_dataframe` u een gelabelde gegevensset met de downloadoptie aanroept, u nu opgeven of bestaande bestanden al dan niet moeten worden overschreven.
    + Wanneer `keep_columns` aanbellen of `drop_columns` dat resulteert in een tijdreeks, label of afbeeldingskolom die wordt verwijderd, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
    + Probleem met pytorchloader opgelost voor de objectdetectietaak.
  + **azureml-contrib-interpret**
    + Verwijderde uitleg dashboard widget van azureml-contrib-interpreteren, gewijzigd pakket om te verwijzen naar de nieuwe in interpret_community
    + Bijgewerkte versie van tolk-community naar 0.2.0
  + **azureml-core**
    + Verbeter de `workspace.datasets`prestaties van .
    + De mogelijkheid toegevoegd om Azure SQL Database Datastore te registreren met gebruikersnaam- en wachtwoordverificatie
    + Oplossing voor het laden van runConfigurations van relatieve paden.
    + Wanneer `keep_columns` u `drop_columns` belt of dat resulteert in een tijdreekskolom die wordt verwijderd, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
  + **azureml-interpret**
    + bijgewerkte versie van tolk-community naar 0.2.0
  + **azureml-pipeline-stappen**
    + Gedocumenteerde ondersteunde `runconfig_pipeline_params` waarden voor azure machine learning-pijplijnstappen.
  + **azureml-pipeline-core**
    + Cli-optie toegevoegd om de uitvoer in json-indeling voor pijplijnopdrachten te downloaden.
  + **azureml-train-automl**
    + AzureML-Train-AutoML splitsen in 2 pakketten, een clientpakket AzureML-Train-AutoML-Client en een ML-trainingspakket AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Een thin client toegevoegd voor het indienen van AutoML-experimenten zonder dat u computer learning-afhankelijkheden lokaal hoeft te installeren.
    + Vaste logging van automatisch gedetecteerde vertragingen, rollende vensterformaten en maximale horizonten in de remote runs.
  + **azureml-train-automl-runtime**
    + Een nieuw AutoML-pakket toegevoegd om machine learning- en runtime-componenten van de client te isoleren.
  + **azureml-contrib-train-rl**
    + Ondersteuning voor reinforcement learning toegevoegd in SDK.
    + AmlWindowsCompute-ondersteuning toegevoegd in RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK voor Python v1.0.74

  + **Voorbeeldfuncties**
    + **azureml-contrib-dataset**
      + Nadat u azureml-contrib-dataset hebt `Dataset.Labeled.from_json_lines` geïmporteerd, `._Labeled` u bellen in plaats van een gelabelde gegevensset te maken.
      + Wanneer `to_pandas_dataframe` u een gelabelde gegevensset met de downloadoptie aanroept, u nu opgeven of bestaande bestanden al dan niet moeten worden overschreven.
      + Wanneer `keep_columns` u `drop_columns` belt of dat resulteert in een tijdreeks, label of afbeeldingskolom die wordt verwijderd, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
      + Problemen met PyTorch-lader `dataset.to_torchvision()`opgelost bij het bellen.

+ **Bug fixes en verbeteringen**
  + **azure-cli-ml**
    + Modelprofilering toegevoegd aan de preview CLI.
    + Hiermee worden de wijzigingen in Azure Storage verholpen, waardoor AzureML CLI is mislukt.
    + Type Load Balancer toegevoegd aan MLC voor AKS-typen
  + **azureml-automl-core**
    + Fixed the issue with detection of maximal horizon on time series, having missing values and multiple grains.
    + Fixed the issue with failures during generation of cross validation splits.
    + Vervang deze sectie door een bericht in markdown-indeling dat wordt weergegeven in de releasenotities: -Verbeterde verwerking van korte korrels in de prognosegegevenssets.
    + Fixed the issue with masking of some user information during logging. -Verbeterde logging van de fouten tijdens het voorspellen van runs.
    + Psutil toevoegen als een afhankelijkheid van conda aan het automatisch gegenereerde yml-implementatiebestand.
  + **azureml-contrib-mir**
    + Hiermee worden de wijzigingen in Azure Storage verholpen, waardoor AzureML CLI is mislukt.
  + **azureml-core**
    + Hiermee wordt een bug verholpen waardoor modellen die zijn geïmplementeerd op Azure-functies 500's hebben geproduceerd.
    + Probleem opgelost waarbij het amlignore-bestand niet is toegepast op momentopnamen.
    + Added a new API amlcompute.get_active_runs that returns a generator for running and queued runs on a given amlcompute.
    + Toegevoegd Load Balancer Type naar MLC voor AKS types.
    + Toegevoegd append_prefix bool parameter te download_files in run.py en download_artifacts_from_prefix in artifacts_client. Deze vlag wordt gebruikt om selectief het oorsprongsbestandspad af te vlakken, zodat alleen de bestands- of mapnaam wordt toegevoegd aan de output_directory
    + Los probleem met `run_config.yml` deserialisatie op voor het gebruik van gegevenssets.
    + Wanneer `keep_columns` u `drop_columns` belt of dat resulteert in een tijdreekskolom die wordt verwijderd, worden de bijbehorende mogelijkheden ook voor de gegevensset verwijderd.
  + **azureml-interpret**
    + Bijgewerkte versie van de interpret-community naar 0.1.0.3
  + **azureml-train-automl**
    + Probleem opgelost waarbij automl_step mogelijk geen validatieproblemen afte drukken.
    + Vaste register_model om te slagen, zelfs als de omgeving van het model lokaal afhankelijkheden mist.
    + Probleem opgelost waarbij sommige externe oplages niet zijn ingeschakeld.
    + Voeg logboekregistratie toe van de uitzondering waardoor een lokale run voortijdig mislukt.
  + **azureml-train-core**
    + Houd rekening met resume_from wordt uitgevoerd in de berekening van geautomatiseerde hyperparametertuning beste onderliggende uitvoeringen.
  + **azureml-pipeline-core**
    + Vaste parameterafhandeling in de constructie van pijplijnargumenten.
    + Added pipeline description and step type yaml parameter.
    + Nieuwe yaml-indeling voor pijplijnstap en waarschuwing voor afschaffing toegevoegd voor oude indeling.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webervaring

De bestemmingspagina [https://ml.azure.com](https://ml.azure.com) voor collaboratieve werkruimtes is verbeterd en omgedoopt tot de Azure Machine Learning-studio (preview).

Vanuit de studio u Azure Machine Learning-assets trainen, testen, implementeren en beheren, zoals gegevenssets, pijplijnen, modellen, eindpunten en meer.

Toegang tot de volgende webgebaseerde ontwerptools vanuit de studio:

| Webgebaseerd hulpmiddel | Beschrijving | Editie |
|-|-|-|
| Vm voor notitieblok(voorbeeld) | Volledig beheerd cloudwerkstation | Basis& Onderneming |
| [Geautomatiseerde machine learning](tutorial-first-experiment-automated-ml.md) (preview) | Geen code-ervaring voor het automatiseren van machine learning model ontwikkeling | Enterprise |
| [Ontwerper](concept-designer.md) (preview) | Drag-and-drop machine learning modeling tool voorheen bekend als de ontwerper | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning-ontwerperverbeteringen

+ Voorheen bekend als de visuele interface 
+    11 nieuwe [modules,](algorithm-module-reference/module-reference.md) waaronder aanbevelingen, classificaties en trainingsprogramma's, waaronder functieengineering, crossvalidatie en gegevenstransformatie.

### <a name="r-sdk"></a>R SDK 
 
Gegevenswetenschappers en AI-ontwikkelaars gebruiken de [Azure Machine Learning SDK voor R](tutorial-1st-r-experiment.md) om machine learning-werkstromen te bouwen en uit te voeren met Azure Machine Learning.

De Azure Machine Learning SDK `reticulate` voor R gebruikt het pakket om te binden aan de Python SDK. Door u rechtstreeks aan Python te binden, biedt de SDK voor R u toegang tot kernobjecten en -methoden die in de Python SDK zijn geïmplementeerd vanuit elke R-omgeving die u kiest.

De belangrijkste mogelijkheden van de SDK zijn:

+    Cloudresources voor het bewaken, loggen en organiseren van uw machine-leerexperimenten beheren.
+    Train modellen met behulp van cloudresources, waaronder GPU-versnelde modeltraining.
+    Implementeer uw modellen als webservices op Azure Container Instances (ACI) en Azure Kubernetes Service (AKS).

Zie de [pakketwebsite](https://azure.github.io/azureml-sdk-for-r) voor volledige documentatie.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning-integratie met gebeurtenisraster 

Azure Machine Learning is nu een bronprovider voor gebeurtenisraster, u machine learning-gebeurtenissen configureren via de Azure-portal of Azure CLI. Gebruikers kunnen gebeurtenissen maken voor voltooiing van de uitvoering, modelregistratie, modelimplementatie en gedetecteerde gegevensdrift. Deze gebeurtenissen kunnen worden doorgestuurd naar gebeurtenishandlers die worden ondersteund door Gebeurtenisraster voor verbruik. Zie machine [learning-gebeurtenisschema,](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) [concepten](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) en [zelfstudieartikelen](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) voor meer informatie.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK voor Python v1.0.72

+ **Nieuwe functies**
  + Toegevoegde gegevensset controleert via het [**azureml-datadrift-pakket,**](https://docs.microsoft.com/python/api/azureml-datadrift) waardoor tijdreeksgegevenssets kunnen worden gecontroleerd op gegevensdrift of andere statistische wijzigingen in de loop van de tijd. Waarschuwingen en gebeurtenissen kunnen worden geactiveerd als drift wordt gedetecteerd of andere voorwaarden op de gegevens is voldaan. Zie [onze documentatie](https://aka.ms/datadrift) voor meer informatie.
  + Aankondiging van twee nieuwe edities (ook wel sku door elkaar genoemd) in Azure Machine Learning. Met deze release u nu een Basic- of Enterprise Azure Machine Learning-werkruimte maken. Alle bestaande werkruimten worden standaard weergegeven in de Basic-editie en u naar de Azure-portal of naar de studio gaan om de werkruimte op elk gewenst moment te upgraden. U een Basis- of Enterprise-werkruimte maken vanuit de Azure-portal. Lees [onze documentatie](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) voor meer informatie. Vanuit de SDK kan de editie van uw werkruimte worden bepaald met behulp van de eigenschap 'sku' van uw werkruimteobject.
  + We hebben ook verbeteringen aangebracht in Azure Machine Learning Compute - u nu statistieken voor uw clusters weergeven (zoals totale knooppunten, lopende knooppunten, totaalkernquotum) in Azure Monitor, naast het bekijken van diagnostische logboeken voor foutopsporing. Daarnaast u ook lopende of in de wachtrij staande runs op uw cluster bekijken en details zoals de IP's van de verschillende knooppunten op uw cluster. U deze bekijken in de portal of met behulp van bijbehorende functies in de SDK of CLI.

  + **Voorbeeldfuncties**
    + We geven preview-ondersteuning voor schijfversleuteling van uw lokale SSD vrij in Azure Machine Learning Compute. Verhoog een ticket voor technische ondersteuning om uw abonnement op de witte lijst te krijgen om deze functie te gebruiken.
    + Openbare voorbeeld van Azure Machine Learning Batch Inference. Azure Machine Learning Batch Inference richt zich op grote gevolgtrekkingtaken die niet tijdgevoelig zijn. Batch Inference biedt kosteneffectieve inferentie compute scaling, met een ongeëvenaarde doorvoer voor asynchrone toepassingen. Het is geoptimaliseerd voor high-throughput, fire-and-forget gevolgtrekking over grote verzamelingen van gegevens.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Ingeschakelde functionaliteiten voor gelabelde gegevensset
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

+ **Bug fixes en verbeteringen**
  + **azure-cli-ml**
    + CLI ondersteunt nu modelverpakkingen.
    + Gegevensset CLI toegevoegd. Voor meer informatie:`az ml dataset --help`
    + Ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-learn en TensorFlow) zonder een InferenceConfig-exemplaar.
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. Als deze wordt verstrekt, overschrijft u de bestaande service als de service met naam al bestaat. Als er geen service bestaat, wordt er een nieuwe service gemaakt.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en Tensorflow. - Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
  + **azureml-automl-core**
    + Het trainen van een iteratie wordt alleen uitgevoerd in een onderliggend proces wanneer runtime-beperkingen worden ingesteld.
    + Een vangrail toegevoegd voor het voorspellen van taken, om te controleren of een opgegeven max_horizon een geheugenprobleem op de gegeven machine veroorzaakt of niet. Als dat het wil, wordt er een bericht van de vangrail weergegeven.
    + Extra ondersteuning voor complexe frequenties zoals 2 jaar en 1 maand. -Begrijpelijke foutmelding toegevoegd als de frequentie niet kan worden bepaald.
    + Azureml-standaardinstellingen toevoegen aan automatisch gegenereerde conda env om de fout met de modelimplementatie op te lossen
    + Sta toe dat intermediaire gegevens in Azure Machine Learning `AutoMLStep`Pipeline worden geconverteerd naar tabelgegevenssets en worden gebruikt in .
    + Geïmplementeerde kolomdoelupdate voor streaming.
    + Geïmplementeerde transformatieparameter-update voor Imputer en HashOneHotEncoder voor streaming.
    + De huidige gegevensgrootte en de minimaal vereiste gegevensgrootte toegevoegd aan de foutberichten voor validatie.
    + De minimaal vereiste gegevensgrootte voor crossvalidatie bijgewerkt om een minimum van twee monsters in elke validatievouw te garanderen.
  + **azureml-cli-common**
    + CLI ondersteunt nu modelverpakkingen.
    + Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en Tensorflow.
    + Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
  + **azureml-contrib-gbdt**
    + het releasekanaal voor het notitieblok opgelost
    + Een waarschuwing toegevoegd voor niet AmlCompute compute target die we niet ondersteunen
    + LightGMB Estimator toegevoegd aan azureml-contrib-gbdt pakket
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI ondersteunt nu modelverpakkingen.
    + De waarschuwing voor afschaffing toevoegen voor afgeschafte API's voor gegevenssetgegevens. Zie kennisgeving van https://aka.ms/tabular-datasetwijziging van de gegevensset-API op .
    + Wijzigen [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) in de naam en versie van de registratie als de gegevensset is geregistreerd.
    + Los een bug op die ScriptRunConfig met gegevensset als argument kan niet herhaaldelijk worden gebruikt om experimentvoer in te dienen.
    + Gegevenssets die tijdens een run worden opgehaald, worden bijgehouden en kunnen [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) worden weergegeven op de pagina met hardloopgegevens of door te bellen nadat de run is voltooid.
    + Sta toe dat intermediaire gegevens in Azure Machine Learning [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)Pipeline worden geconverteerd naar tabelgegevenssets en worden gebruikt in .
    + Ondersteuning toegevoegd voor het implementeren en verpakken van ondersteunde modellen (ONNX, scikit-learn en TensorFlow) zonder een InferenceConfig-exemplaar.
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. Als deze wordt verstrekt, overschrijft u de bestaande service als de service met naam al bestaat. Als er geen service bestaat, wordt er een nieuwe service gemaakt.
    +  Modellen kunnen worden geregistreerd met twee nieuwe frameworks, Onnx en Tensorflow. Modelregistratie accepteert voorbeeldinvoergegevens, voorbeelduitvoergegevens en resourceconfiguratie voor het model.
    + Nieuwe datastore toegevoegd voor Azure Database voor MySQL. Bijvoorbeeld toegevoegd voor het gebruik van Azure Database voor MySQL in DataTransferStep in Azure Machine Learning Pipelines.
    + Functionaliteit toegevoegd om tags toe te voegen en te verwijderen uit experimenten Extra functionaliteit om tags uit uitvoeringen te verwijderen
    + Added overwrite flag for service deployment (ACI and AKS) in SDK and CLI. Als deze wordt verstrekt, overschrijft u de bestaande service als de service met naam al bestaat. Als er geen service bestaat, wordt er een nieuwe service gemaakt.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Verplaatst `azureml-contrib-datadrift` van naar`azureml-datadrift`
    + Ondersteuning toegevoegd voor het monitoren van tijdreeksgegevenssets voor drift- en andere statistische metingen
    + Nieuwe methoden `create_from_model()` `create_from_dataset()` en [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) aan de klasse. De `create()` methode wordt afgeschaft.
    + Aanpassingen aan de visualisaties in Python en Gebruikersinterface in de Azure Machine Learning-studio.
    + Ondersteuning voor wekelijkse en maandelijkse monitorplanning, naast de dagelijkse voor gegevenssetmonitors.
    + Ondersteuning voor backfill van gegevensmonitorstatistieken om historische gegevens voor gegevenssetmonitors te analyseren.
    + Verschillende bugfixes
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep is niet langer nodig om een Azure `yaml` Machine Learning Pipeline uit het pijplijnbestand in te dienen.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Azureml-standaardinstellingen toevoegen aan automatisch gegenereerde conda env om de fout met de modelimplementatie op te lossen
    + AutoML remote training omvat nu azureml-defaults om hergebruik van training env voor gevolgtrekking mogelijk te maken.
  + **azureml-train-core**
    + PyTorch 1.3-ondersteuning [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) toegevoegd in schatter

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuele interface (voorbeeld)

+ De visuele interface azure machine learning (preview) is gereviseerd om uit te voeren op [Azure Machine Learning-pijplijnen.](concept-ml-pipelines.md) Pijplijnen (voorheen experimenten genoemd) die in de visuele interface zijn geschreven, zijn nu volledig geïntegreerd met de kernervaring van Azure Machine Learning.
  + Uniforme beheerervaring met SDK-assets
  + Versiebeheer en tracking voor visuele interfacemodellen, pijplijnen en eindpunten
  + Vernieuwde gebruikersinterface
  + Implementatie van batch-gevolgtrekkingen toegevoegd
  + Added Azure Kubernetes Service (AKS) support for inference compute targets Added Azure Kubernetes Service (AKS) support for inference compute targets Added Azure Kubernetes Service (AKS) support for inference compute targets Added Azure
  + Nieuwe python-stap pijplijn authoring workflow
  + Nieuwe [bestemmingspagina](https://ml.azure.com) voor hulpmiddelen voor visuele ontwerpen

+ **Nieuwe modules**
  + Wiskundige bewerking toepassen
  + SQL-transformatie toepassen
  + Clipwaarden
  + Gegevens samenvatten
  + Importeren uit SQL-database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK voor Python v1.0.69

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + Het beperken van modeluitleg tot de beste run in plaats van computeruitleg voor elke run. Het maken van dit gedrag te veranderen voor lokale, externe en ADB.
    + Ondersteuning toegevoegd voor on-demand modeluitleg voor gebruikersinterface
    + Psutil toegevoegd als een `automl` afhankelijkheid van en opgenomen psutil als een conda afhankelijkheid in amlcompute.
    + Fixed the issue with heuristic lags and rolling window sizes on the forecasting data sets some series of which can cause linear algebra errors
      + Toegevoegd print uit voor de heuristically bepaalde parameters in de prognoses loopt.
  + **azureml-contrib-datadrift**
    + Extra bescherming tijdens het maken van uitvoerstatistieken als drift op gegevenssetniveau niet in de eerste sectie staat.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model pakket is omgedoopt tot azureml-contrib-interpret
  + **azureml-core**
    + API toegevoegd om gegevenssets uit te schrijven. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model pakket is omgedoopt tot azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + API toegevoegd om gegevenssets uit te schrijven. Dataset. [unregister_all_versions.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)
    + De API voor gegevenssets is toegevoegd om de gewijzigde tijd van gegevens te controleren. `dataset.data_changed_time`.
    + Kunnen gebruiken `FileDataset` en `TabularDataset` als `PythonScriptStep`ingangen naar , `EstimatorStep`en `HyperDriveStep` in Azure Machine Learning Pipeline
    + Prestaties `FileDataset.mount` van is verbeterd voor mappen met een groot aantal bestanden
    + De mogelijkheid om [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) en [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) te gebruiken als ingangen voor [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)en [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) in de Azure Machine Learning Pipeline.
    + Prestaties van FileDataset. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) is verbeterd voor mappen met een groot aantal bestanden
    + URL toegevoegd aan bekende foutaanbevelingen in rundetails.
    + Fixed a bug in run.get_metrics where requests would fail if a run had too many children
    + Fixed a bug in [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) where requests would fail if a run had too many children
    + Ondersteuning toegevoegd voor verificatie op arcadia-cluster.
    + Als u een experimentobject maakt, wordt of maakt u het experiment in de Azure Machine Learning-werkruimte voor het bijhouden van geschiedenis. De experiment-id en de gearchiveerde tijd worden ingevuld in het object Experiment bij het maken. Voorbeeld: experiment = experiment(werkruimte, "Nieuw experiment") experiment_id = experiment.id archief() en reactiveren() zijn functies die kunnen worden opgeroepen voor een experiment om het experiment te verbergen en te herstellen van wordt weergegeven in de UX of standaard geretourneerd in een aanroep om experimenten weer te geven. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, u de naam van het gearchiveerde experiment wijzigen wanneer u het opnieuw activeert door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voorbeeld: experiment1 = Experiment(werkruimte, 'Actief Experiment') experiment1.archive() # Nieuw actief experiment maken met dezelfde naam als het gearchiveerde. experiment2. = Experiment(werkruimte, "Actief Experiment") experiment1.reactivate(new_name="Previous Active Experiment") De lijst met statische methoden() op Experiment kan een naamfilter en viewtype-filter nemen. ViewType-waarden zijn 'ACTIVE_ONLY', 'ARCHIVED_ONLY' en 'ALLES':archived_experiments = Experiment.list(werkruimte, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(werkruimte, naam="Eerste experiment", view_type="ALLES")
    + Ondersteuning voor het gebruik van de omgeving voor modelimplementatie en service-update
  + **azureml-datadrift**
    + Het kenmerk van De show van de klasse DataDriftDector ondersteunt geen optie 'with_details' meer. Het kenmerk Show presenteert alleen de gegevensdriftcoëfficiënt en de bijdrage van gegevensdrift van functiekolommen.
    + DataDriftDetector kenmerk 'get_output' gedragsveranderingen:
      + Invoerparameter start_time end_time zijn optioneel in plaats van verplicht;
      + Invoer specifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroepen zal resulteren in uitzondering van waardefouten omdat ze elkaar uitsluiten
      + Door specifieke start_time en/of end_time te invoeren, worden alleen de resultaten van geplande runs geretourneerd;
      + Parameter 'daily_latest_only' is afgeschaft.
    + Ondersteuning voor het ophalen van gegevensdrift-uitvoerop basis van gegevensopset.
  + **azureml-explain-model**
    + Hernoemt AzureML-explain-modelpakket naar AzureML-interpret, zodat het oude pakket voorlopig achterwaartse compatibiliteit blijft
    + fixed `automl` bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient fixed bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient fixed bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient fixed bug
    + Ondersteuning toevoegen `ScoringExplainer` om rechtstreeks te worden gemaakt met`MimicWrapper`
  + **azureml-pipeline-core**
    + Verbeterde prestaties voor grote pijplijncreatie
  + **azureml-train-core**
    + TensorFlow 2.0-ondersteuning toegevoegd in TensorFlow Estimator
  + **azureml-train-automl**
    + Als u een [experimentobject](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) maakt, wordt of maakt u het experiment in de Azure Machine Learning-werkruimte voor het bijhouden van geschiedenis. De experiment-id en de gearchiveerde tijd worden ingevuld in het object Experiment bij het maken. Voorbeeld:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archief()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) en [reactiveren()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) zijn functies die kunnen worden aangeroepen voor een experiment om het experiment te verbergen en te herstellen van wordt weergegeven in de UX of standaard terug in een aanroep om experimenten te vermelden. Als een nieuw experiment wordt gemaakt met dezelfde naam als een gearchiveerd experiment, u de naam van het gearchiveerde experiment wijzigen wanneer u het opnieuw activeert door een nieuwe naam door te geven. Er kan slechts één actief experiment met een bepaalde naam zijn. Voorbeeld:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        De lijst met statische [methoden()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) op Experiment kan een naamfilter en viewtype-filter nemen. ViewType-waarden zijn "ACTIVE_ONLY", "ARCHIVED_ONLY" en "ALL". Voorbeeld:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Ondersteuning voor het gebruik van de omgeving voor modelimplementatie en service-update.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Het kenmerk van De show van de klasse [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) ondersteunt geen optioneel argument 'with_details' meer. Het kenmerk Show presenteert alleen de gegevensdriftcoëfficiënt en de bijdrage van gegevensdrift van functiekolommen.
    + DataDriftDetector functie [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) gedragsveranderingen:
      + Invoerparameter start_time end_time zijn optioneel in plaats van verplicht;
      + Invoerspecifieke start_time en/of end_time met een specifieke run_id in dezelfde aanroepen zal resulteren in uitzondering van waardefouten omdat ze elkaar uitsluiten;
      + Door specifieke start_time en/of end_time te invoeren, worden alleen de resultaten van geplande runs geretourneerd;
      + Parameter 'daily_latest_only' is afgeschaft.
    + Ondersteuning voor het ophalen van gegevensdrift-uitvoerop basis van gegevensopset.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Hernoemt azureml-explain-modelpakket naar AzureML-interpret, zodat het oude pakket voorlopig achterwaartse compatibiliteit blijft.
    + fixed AutoML bug with raw explanations set to classification task instead of regression by default on download from ExplanationClient.
    + Ondersteuning toevoegen voor [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) die rechtstreeks kan worden gemaakt met [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Verbeterde prestaties voor grote pipeline creatie.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + TensorFlow 2.0-ondersteuning toegevoegd in [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + De bovenliggende run wordt niet meer mislukt wanneer de installatie-iteratie is mislukt, omdat de orchestration er al voor zorgt.
    + Ondersteuning voor lokale docker en lokaal-conda toegevoegd voor AutoML-experimenten
    + Toegevoegd local-docker en local-conda ondersteuning voor AutoML experimenten.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview) voor Azure Machine Learning-werkruimten

Het tabblad Experiment in de [nieuwe werkruimteportal](https://ml.azure.com) is bijgewerkt, zodat gegevenswetenschappers experimenten op een performantermanier kunnen controleren. U de volgende functies verkennen:
+ Metagegevens experimenteren om uw lijst met experimenten eenvoudig te filteren en te sorteren
+ Vereenvoudigde en performante experiment details pagina's waarmee u uw runs visualiseren en vergelijken
+ Nieuw ontwerp om detailspagina's uit te voeren om uw trainingsruns te begrijpen en te controleren

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK voor Python v1.0.65

  + **Nieuwe functies**
    + Samengestelde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor veelvoorkomende machine learning-taken en zijn vooraf gebouwd en in de cache opgeslagen als Docker-afbeeldingen voor een snellere uitvoering. Ze worden standaard weergegeven in de lijst met omgevingen van Workspace, met voorvoegsel 'AzureML'.
    + Samengestelde omgevingen toegevoegd. Deze omgevingen zijn vooraf geconfigureerd met bibliotheken voor veelvoorkomende machine learning-taken en zijn vooraf gebouwd en in de cache opgeslagen als Docker-afbeeldingen voor een snellere uitvoering. Ze worden standaard weergegeven in de lijst met omgevingen van [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)met voorvoegsel 'AzureML'.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Toegevoegd de ONNX conversie ondersteuning voor de ADB en HDI

+ **Voorbeeldfuncties**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteund BERT en BiLSTM als text featurizer (alleen preview)
    + Ondersteunde prestatieaanpassing voor kolomdoeleinden en transformatieparameters (alleen preview)
    + Ondersteunde ruwe uitleg wanneer de gebruiker modeluitleg inschakelt tijdens de training (alleen preview)
    + Profeet toegevoegd `timeseries` voor prognoses als een trainbare pijplijn (alleen preview)

  + **azureml-contrib-datadrift**
    + Pakketten verplaatst van azureml-contrib-datadrift naar azureml-datadrift; het `contrib` pakket zal worden verwijderd in een toekomstige release

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + FeaturizationConfig geïntroduceerd bij AutoMLConfig en AutoMLBaseSettings
    + FeaturizationConfig geïntroduceerd bij [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) en AutoMLBaseSettings
      + Kolomdoel voor featurisatie overschrijven met een gegeven kolom- en functietype
      + Transformatieparameters overschrijven
    + Het afschrijvingsbericht voor explain_model() en retrieve_model_explanations()
    + Prophet toegevoegd als trainbare pijplijn (alleen preview)
    + Het afkoopbericht voor explain_model() en retrieve_model_explanations() toegevoegd.
    + Prophet toegevoegd als trainbare pijplijn (alleen preview).
    + Extra ondersteuning voor automatische detectie van doelvertragingen, rolvenstergrootte en maximale horizon. Als een van target_lags, target_rolling_window_size of max_horizon is ingesteld op 'auto', worden de heuristieken toegepast om de waarde van de overeenkomstige parameter te schatten op basis van trainingsgegevens.
    + Vaste prognoses in het geval dat de gegevensset één korrelkolom bevat, deze korrel van een numeriek type is en er een kloof is tussen trein en testset
    + Het foutbericht over de gedupliceerde index in de externe uitvoering in prognosetaken opgelost
    + Vaste prognoses in het geval dat de gegevensset één korrelkolom bevat, deze korrel is van een numeriek type en er is een kloof tussen trein en testset.
    + Fixed the error message about the duplicated index in the remote run in forecasting tasks.
    + Een vangrail toegevoegd om te controleren of een gegevensset onevenwichtig is of niet. Als dat zo is, wordt er een bericht over de vangrail naar de console geschreven.
  + **azureml-core**
    + Added ability to retrieve SAS URL to model in storage through the model object. Ex: model.get_sas_url()
    + Gegevenssets introduceren `run.get_details()['datasets']` om gegevenssets te laten bijbehoren aan de ingediende run
    + Voeg `Dataset.Tabular.from_json_lines_files` API toe om een TabularDataset te maken van JSON Lines-bestanden. Ga voor meer informatie over deze tabelgegevens in JSON https://aka.ms/azureml-data Lines-bestanden op TabularDataset naar voor documentatie.
    + Extra vm-groottevelden (OS-schijf, aantal GPU's) toegevoegd aan de supported_vmsizes (functie
    + Extra velden toegevoegd aan de list_nodes () functie om de run, de private en de openbare IP, de poort etc. weer te geven.
    + Mogelijkheid om een nieuw veld op te geven tijdens het inrichten van het cluster --remotelogin_port_public_access dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment van het maken van het cluster. Als u dit niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van of u het cluster in een VNet implementeert.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Added ability to retrieve SAS URL to model in storage through the model object. Ex: model. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduceer run. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] om gegevenssets te laten toevoegen aan de ingediende run
    + API `Dataset.Tabular`toevoegen . [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) om een TabularDataset te maken van JSON Lines-bestanden. Ga voor meer informatie over deze tabelgegevens in JSON https://aka.ms/azureml-data Lines-bestanden op TabularDataset naar voor documentatie.
    + Extra vm-groottevelden (OS-schijf, aantal GPU's) toegevoegd aan de functie [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Extra velden toegevoegd aan de [functie list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) om de run, het privé- en het openbare IP-adres, de poort enz.
    + Mogelijkheid om een nieuw veld op te geven tijdens het [clusterprovisionen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` dat kan worden ingesteld op ingeschakeld of uitgeschakeld, afhankelijk van of u de SSH-poort open of gesloten wilt laten op het moment van het maken van het cluster. Als u dit niet opgeeft, wordt de poort slim geopend of gesloten, afhankelijk van of u het cluster in een VNet implementeert.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Verbeterde documentatie voor uitleguitvoer in het classificatiescenario.
    + Toegevoegd de mogelijkheid om de voorspelde y waarden uploaden op de uitleg voor de evaluatie voorbeelden. Ontgrendelt meer nuttige visualisaties.
    + Added explainer property to MimicWrapper to enable getting the underlying MimicExplainer.
  + **azureml-pipeline-core**
    + Notebook toegevoegd om module, moduleversie en modulestap te beschrijven
  + **azureml-pipeline-stappen**
    + Added RScriptStep to support R script run via AML pipeline.
    + Fixed metadata parameters parsing in AzureBatchStep which was causing the error message "assignment for parameter SubscriptionId is not specified."
  + **azureml-train-automl**
    + Ondersteunde training_data, validation_data, label_column_name, weight_column_name als gegevensinvoerindeling
    + Het afschrijvingsbericht voor explain_model() en retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Een [notitieblok](https://aka.ms/pl-modulestep) toegevoegd om [module,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [moduleversie](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) en [modulestap](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)te beschrijven.
  + **[azureml-pipeline-stappen](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Added [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) to support R script run via AML pipeline.
    + Fixed metadata parameters parsing in [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) which was causing the error message "assignment for parameter SubscriptionId is not specified".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Ondersteund training_data, validation_data, label_column_name, weight_column_name als gegevensinvoerindeling.
    + Het afkoopbericht voor [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) en [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)zijn toegevoegd.


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK voor Python v1.0.62

+ **Nieuwe functies**
  + Introduceerde de `timeseries` eigenschap op TabularDataset. Deze eigenschap maakt het eenvoudig om tijdstempelfiltering op gegevens een TabularDataset, zoals het nemen van alle gegevens tussen een bereik van de tijd of de meest recente gegevens. Ga voor meer `timeseries` informatie over deze eigenschap https://aka.ms/azureml-data op TabularDataset naar voor documentatie of https://aka.ms/azureml-tsd-notebook voor een voorbeeldnotitieblok.
  + Ingeschakelde training met TabularDataset en FileDataset. Ga https://aka.ms/dataset-tutorial naar een voorbeeldnotebook.

  + **azureml-train-core**
      + Toegevoegd `Nccl` `Gloo` en ondersteuning in PyTorch schatter

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + Afgeschaft de AutoML instelling 'lag_length' en de LaggingTransformer.
    + Vaste correcte validatie van invoergegevens als deze zijn opgegeven in een Dataflow-indeling
    + Wijzigde de fit_pipeline.py om de grafiek json te genereren en te uploaden naar artefacten.
    + De grafiek weergeven onder `userrun` gebruik `Cytoscape`.
  + **azureml-core**
    + De uitzonderingsafhandeling in ADB-code opnieuw bekeken en wijzigingen aanbrengen in de nieuwe foutafhandeling
    + Automatische MSI-verificatie toegevoegd voor notebookVM's.
    + Lost bug waar corrupte of lege modellen kunnen worden geüpload als gevolg van mislukte pogingen.
    + Fixed the `DataReference` bug where `DataReference` name changes when the mode `as_upload` `as_download`changes `as_mount`(b.v. when calling , , of).
    + Merk `mount_point` `target_path` en `FileDataset.mount` optioneel `FileDataset.download`voor en .
    + Uitzondering dat de kolom tijdstempel niet kan worden gevonden, wordt weggegooid als de tijdsgerelateerde API wordt aangeroepen zonder dat de stempelkolom met fijne tijdstempel is toegewezen of de toegewezen tijdstempelkolommen worden verwijderd.
    + Kolommen met tijdsseries moeten worden toegewezen met kolom waarvan het type Datum is, anders wordt een uitzondering verwacht
    + Tijdssserialen kolommen toewijzen API 'with_timestamp_columns' kan nemen Geen waarde fijne / grove tijdstempel kolom naam, die eerder toegewezen tijdstempel kolommen zal wissen.
    + Uitzondering wordt weggegooid wanneer grofkorrelige of fijnkorrelige tijdstempelkolom wordt verwijderd met vermelding voor de gebruiker dat laten vallen kan worden gedaan nadat u de tijdstempelkolom in de lijst hoeft uit te sluiten of with_time_stamp met Geen-waarde oproept om tijdstempelkolommen vrij te geven
    + Er wordt een uitzondering gemaakt wanneer grofkorrelige of fijnkorrelige tijdstempelkolom niet is opgenomen in de lijst met keepkolommen met vermelding voor de gebruiker dat het bijhouden kan worden gedaan na het opnemen van tijdstempelkolom in de lijst met kolom bijhouden of with_time_stamp met Geen-waarde om tijdstempelkolommen vrij te geven.
    + Logboekregistratie toegevoegd voor de grootte van een geregistreerd model.
  + **azureml-explain-model**
    + Fixed warning printed to console when "packaging" python package is not installed: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1" Fixed warning printed to console when "packaging" python package is not installed: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1" Fixed warning printed to console when "packaging" python package is not installed: "Using older than supported version of lightgbm, please upgrade to version greater than 2.2.1" Fixed warning
    + Fixed download model explanation with sharding for global explanations with many features Fixed download model explanation with sharding for global explanations with many features Fixed download model explanation with sharding for global explanations with many features Fixed download
    + Fixed mimic explainer missing initialization examples on output explanation Fixed mimic explainer missing initialization examples on output explanation Fixed mimic explainer missing initialization examples on output explanation Fixed mimic
    + Fixed immutable error on set properties when uploading with explanation client using two different types of models Fixed immutable error onset properties when uploading with explanation client using two different types of models Fixed immutable error on set properties when uploading with explanation client using two different types of models Fixed immu
    + Een get_raw param toegevoegd aan scoring explainer .explain() zodat één scoringsexplainer zowel engineered als raw-waarden kan retourneren.
  + **azureml-train-automl**
    + Geïntroduceerde openbare API's van AutoML `automl` voor het ondersteunen van uitleg van explain SDK - Nieuwere manier om AutoML-uitleg te ondersteunen door AutoML-featurization te ontkoppelen en SDK uit te leggen - Geïntegreerde ondersteuning voor ruwe uitleg van azureml legt SDK uit voor AutoML-modellen.
    + Azureml-standaardinstellingen verwijderen uit externe trainingsomgevingen.
    + Standaardcacheopslaglocatie gewijzigd van FileCacheStore op basis van één naar AzureFileCacheStore voor AutoML op Azure Databricks-codepad.
    + Vaste correcte validatie van invoergegevens als deze zijn opgegeven in een Dataflow-indeling
  + **azureml-train-core**
    + Teruggedraaid source_directory_data_store despot.
    + Added mogelijkheid to override azureml installed package versions.
    + Dockerfile-ondersteuning `environment_definition` toegevoegd in parameter in schatters.
    + Vereenvoudigde gedistribueerde trainingsparameters in schatters.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nieuwe webervaring (preview) voor Azure Machine Learning-werkruimten
De nieuwe webervaring stelt data scientists en data engineers in staat om hun end-to-end machine learning levenscyclus te voltooien, van het voorbereiden en visualiseren van gegevens tot het trainen en implementeren van modellen op één locatie.

![Gebruikersinterface van Azure Machine Learning-werkruimte (voorbeeld)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Belangrijkste kenmerken:**

Met deze nieuwe Azure Machine Learning-interface u nu het volgende doen:
+ Uw notitieblokken beheren of een koppeling maken met Jupyter
+ [Geautomatiseerde ML-experimenten uitvoeren](tutorial-first-experiment-automated-ml.md)
+ [Gegevenssets maken uit lokale bestanden, gegevenswinkels, & webbestanden](how-to-create-register-datasets.md)
+ Verken & voor te bereiden gegevenssets voor het maken van modellen
+ Gegevensdrift voor uw modellen controleren
+ Recente bronnen van een dashboard weergeven

Op het moment van deze release worden de volgende browsers ondersteund: Chrome, Firefox, Safari en Microsoft Edge Preview.

**Bekende problemen:**

1. Vernieuw uw browser als u ziet "Er is iets misgegaan! Fout bij het laden van chunk-bestanden" wanneer de implementatie wordt uitgevoerd.

1. Kan het bestand in notitieblokken en bestanden niet verwijderen of de naam wijzigen. Tijdens Public Preview u Jupyter UI of Terminal in Notebook VM gebruiken om bestandsbewerkingen bij te werken. Omdat het een gemonteerd netwerkbestandssysteem is, worden alle wijzigingen die u aanbrengt op notebook-vm onmiddellijk weergegeven in de notebookwerkruimte.

1. Ga als een sh naar de notebook-vm:
   1. Zoek de SSH-sleutels die zijn gemaakt tijdens de installatie van vm's. U ook de sleutels in de Azure Machine Learning-werkruimte zoeken > het tabblad Berekenen opent > de VM van notitieblokken in de lijst zoeken > de eigenschappen ervan openen: kopieer de sleutels uit het dialoogvenster.
   1. Importeer deze openbare en private SSH-sleutels naar uw lokale machine.
   1. Gebruik ze om SSH in de Notebook VM.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK voor Python v1.0.60

+ **Nieuwe functies**
  + Introduceerde FileDataset, dat verwijst naar enkele of meerdere bestanden in uw datastores of openbare url's. De bestanden kunnen van elk formaat zijn. FileDataset biedt u de mogelijkheid om de bestanden te downloaden of te monteren op uw berekening. Ga voor meer informatie https://aka.ms/file-datasetover FileDataset naar .
  + Added Pipeline Yaml Support for PythonScript Step, Adla Step, Databricks Step, DataTransferStep, and AzureBatch Step

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + AutoArima is nu een suggestiebare pijplijn voor preview alleen.
    + Verbeterde foutrapportage voor prognoses.
    + Verbeterde de logboekregistratie met behulp van aangepaste uitzonderingen in plaats van algemeen in de prognosetaken.
    + Verwijderde de controle op max_concurrent_iterations minder dan het totale aantal iteraties.
    + AutoML-modellen retourneren nu AutoMLExceptions
    + Deze release verbetert de uitvoeringsprestaties van geautomatiseerde computer learning lokale uitvoeringen.
  + **azureml-core**
    + Introduceer Dataset.get_all(werkruimte), waarin `TabularDataset` een `FileDataset` woordenboek wordt geretourneerd van en objecten die zijn ingetoetst op hun registratienaam.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introduceer `parition_format` als `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argument aan en . De partitiegegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. '{column_name}' maakt de tekenreekskolom en '{column_name:yyyy/MM/dd/HH/mm/ss}' maakt de kolom datetime, waarbij 'yyyy', 'MM', 'dd', 'HH', 'mm' en 'ss' worden gebruikt om jaar, maand, dag, uur, minuut en tweede voor het type datumtijd te extraheren. De partition_format moet beginnen vanaf de positie van de eerste partitiesleutel tot het einde van het bestandspad. Bijvoorbeeld, gezien het pad '.. /USA/2019/01/01/data.csv' waarbij de partitie per land en tijd is, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' maakt stringkolom 'Land' met waarde 'USA' en datetime column 'PartitionDate' met waarde '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introduceer `partition_format` als `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files`argument aan en . De partitiegegevens van elk gegevenspad worden geëxtraheerd in kolommen op basis van de opgegeven indeling. '{column_name}' maakt de tekenreekskolom en '{column_name:yyyy/MM/dd/HH/mm/ss}' maakt de kolom datetime, waarbij 'yyyy', 'MM', 'dd', 'HH', 'mm' en 'ss' worden gebruikt om jaar, maand, dag, uur, minuut en tweede voor het type datumtijd te extraheren. De partition_format moet beginnen vanaf de positie van de eerste partitiesleutel tot het einde van het bestandspad. Bijvoorbeeld, gezien het pad '.. /USA/2019/01/01/data.csv' waarbij de partitie per land en tijd is, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' maakt stringkolom 'Land' met waarde 'USA' en datetime column 'PartitionDate' met waarde '2019-01-01'.
    + `to_csv_files`en `to_parquet_files` methoden zijn toegevoegd `TabularDataset`aan . Deze methoden maken conversie `TabularDataset` tussen `FileDataset` a en a mogelijk door de gegevens om te zetten naar bestanden van de opgegeven indeling.
    + Log automatisch in bij het basisafbeeldingsregister wanneer u een Dockerfile opslaat dat wordt gegenereerd door Model.package().
    + "gpu_support" is niet langer nodig; AML detecteert en gebruikt nu automatisch de nvidia docker extensie wanneer deze beschikbaar is. Het zal worden verwijderd in een toekomstige release.
    + Ondersteuning toegevoegd voor het maken, bijwerken en gebruiken van PipelineDrafts.
    + Deze release verbetert de uitvoeringsprestaties van geautomatiseerde computer learning lokale uitvoeringen.
    + Gebruikers kunnen statistieken opvragen op run history by name.
    + Verbeterde de logboekregistratie met behulp van aangepaste uitzonderingen in plaats van algemeen in de prognosetaken.
  + **azureml-explain-model**
    + Toegevoegd feature_maps parameter aan de nieuwe MimicWrapper, zodat gebruikers ruwe functie uitleg te krijgen.
    + Het uploaden van gegevenssets is nu standaard uitgeschakeld voor het uploaden van uitleg en kan opnieuw worden ingeschakeld met upload_datasets=True
    + Toegevoegd "is_law" filtering parameters aan uitleg lijst en download functies.
    + Hiermee `get_raw_explanation(feature_maps)` voegt u de methode toe aan zowel globale als lokale uitlegobjecten.
    + Toegevoegde versie controle naar lightgbm met gedrukte waarschuwing als hieronder ondersteunde versie
    + Geoptimaliseerd geheugengebruik bij batching-uitleg
    + AutoML-modellen retourneren nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Added support to create, update, and use PipelineDrafts - can be used to maintain mutable pipeline definitions and use them interactively to run
  + **azureml-train-automl**
    + De functie is gemaakt om specifieke versies van gpu-geschikte :::no-loc text="cuda"::: pytorch v1.1.0, toolkit 9.0, pytorch-transformatoren te installeren, die nodig is om BERT/ XLNet in te schakelen in de externe python runtime-omgeving.
  + **azureml-train-core**
    + Vroege mislukking van sommige hyperparameter ruimte definitie fouten direct in de sdk in plaats van server kant.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Bug fixes en verbeteringen**
  + Ingeschakeld schrijven naar ADLS/ADLSGen2 met behulp van onbewerkte pad en referenties.
  + Fixed a bug `include_path=True` that caused `read_parquet`to not work for .
  + Fixed `to_pandas_dataframe()` failure caused by exception "Invalid property value: hostSecret".
  + Fixed a bug where files could not be read on DBFS in Spark mode.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK voor Python v1.0.57
+ **Nieuwe functies**
  + Ingeschakeld `TabularDataset` om te worden verbruikt door AutomatedML. Voor meer `TabularDataset`informatie over, u terecht op https://aka.ms/azureml/howto/createdatasets.

+ **Bug fixes en verbeteringen**
  + **automl-client-core-nativeclient**
    + Fixed the error, raised when training and/or validation labels (y and y_valid) are provided in the form of pandas dataframe but not as numpy array.
    + Bijgewerkte interface om `RawDataContext` een te maken `AutoMLBaseSettings` om alleen de gegevens en het object nodig te hebben.
    +  Laat AutoML-gebruikers trainingsseries die niet lang genoeg zijn bij het voorspellen, laten vallen. - Laat AutoML-gebruikers korrels uit de testset laten vallen die niet bestaat in de trainingsset bij het voorspellen.
  + **azure-cli-ml**
    + U nu het TLS/SSL-certificaat bijwerken voor het scoreeindpunt dat is geïmplementeerd op het AKS-cluster, zowel voor microsoft gegenereerd als voor het certificaat van de klant.
  + **azureml-automl-core**
    + Probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet goed zijn verwijderd.
    + Verbeterde foutregistratie in AutoML; volledige foutmeldingen worden nu altijd naar het logboekbestand geschreven.
    + AutoML heeft haar pakketvasten `azureml-defaults` `azureml-explain-model`bijgewerkt `azureml-dataprep`met , en . AutoML waarschuwt niet langer voor foutmatches `azureml-train-automl` op pakketten (behalve voor pakket).
    + Fixed an `timeseries` issue in where cv splits are of unequal size causing bin calculation to fail.
    + Bij het uitvoeren van ensemble iteratie voor de Cross-Validation training type, als we uiteindelijk problemen met het downloaden van de modellen getraind op de hele dataset, waren we met een inconsistentie tussen het model gewichten en de modellen die werden ingevoerd in de stemming ensemble.
    + Fixed the error, raised when training and/or validation labels (y and y_valid) are provided in the form of pandas dataframe but not as numpy array.
    + Probleem met de prognosetaken opgelost toen er geen probleem werd aangetroffen in de Booleaanse kolommen met invoertabellen.
    + Laat AutoML-gebruikers trainingsseries die niet lang genoeg zijn bij het voorspellen, laten vallen. - Laat AutoML-gebruikers korrels uit de testset laten vallen die niet bestaat in de trainingsset bij het voorspellen.
  + **azureml-core**
    + Probleem opgelost met blob_cache_timeout parametervolgorde.
    + Externe fit- en transformatieuitzonderingstypen toegevoegd aan systeemfouten.
    + Ondersteuning toegevoegd voor Key Vault-geheimen voor externe uitvoeringen. Voeg een azureml.core.keyvault.Keyvault-klasse toe om geheimen toe te voegen, op te halen en een lijst te maken van geheimen uit de keyvault die aan uw werkruimte is gekoppeld. Ondersteunde bewerkingen zijn:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(naam, waarde)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(naam)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Aanvullende methoden om standaard keyvault te verkrijgen en geheimen te krijgen tijdens remote run:
      + azureml.core.workspace.workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(naam)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Extra overschrijfparameters toegevoegd aan de opdracht CLI-submit-hyperdrive.
    + Het verbeteren van de betrouwbaarheid van API-aanroepen worden uitbreiden van nieuwe pogingen naar algemene aanvragen bibliotheek uitzonderingen.
    + Voeg ondersteuning toe voor het indienen van aanvragen van runs vanaf een ingediende run.
    + Fixed expiring SAS token issue in FileWatcher, which caused files to stop being uploaded after their initial token was expired.
    + Ondersteund bij het importeren van HTTP csv/tsv-bestanden in de gegevensset python SDK.
    + De methode Workspace.setup() afgeschaft. Waarschuwingsbericht weergegeven aan gebruikers stelt voor om create() of get()/from_config() in plaats daarvan te gebruiken.
    + Added Environment.add_private_pip_wheel(), waarmee u privé-aangepaste python-pakketten `whl`naar de werkruimte uploaden en ze veilig gebruiken om de omgeving te bouwen/materialiseren.
    + U nu het TLS/SSL-certificaat bijwerken voor het scoreeindpunt dat is geïmplementeerd op het AKS-cluster, zowel voor microsoft gegenereerd als voor het certificaat van de klant.
  + **azureml-explain-model**
    + Parameter toegevoegd om een model-ID toe te voegen aan uitleg over uploaden.
    + Toegevoegd `is_raw` tagging aan uitleg in het geheugen en uploaden.
    + Toegevoegd pytorch ondersteuning en tests voor azureml-explain-model pakket.
  + **azureml-opendatasets**
    + Ondersteuning voor het detecteren en registreren van de automatische testomgeving.
    + Toegevoegd klassen om de Amerikaanse bevolking te krijgen door de provincie en zip.
  + **azureml-pipeline-core**
    + Labeleigenschap toegevoegd aan invoer- en uitvoerpoortdefinities.
  + **azureml-telemetry**
    + Fixed an incorrect telemetry configuration.
  + **azureml-train-automl**
    + Fixed the bug where on setup failure, error was not getting logged in "errors" field for the setup run and dus was not stored in parent run "errors".
    + Probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet goed zijn verwijderd.
    + Laat AutoML-gebruikers trainingsseries die niet lang genoeg zijn bij het voorspellen, laten vallen.
    + Laat AutoML-gebruikers korrels uit de testset laten vallen die niet bestaan in de trainingsset bij het voorspellen.
    + Nu gaat AutoMLStep door `automl` config naar backend om eventuele problemen met wijzigingen of toevoegingen van nieuwe config-parameters te voorkomen.
    + AutoML Data Guardrail is nu in openbare preview. De gebruiker ziet na de training een rapport Over de vangrail (voor classificatie-/regressietaken) en heeft er ook toegang toe via de SDK API.
  + **azureml-train-core**
    + Added torch 1.2 support in PyTorch Estimator.
  + **azureml-widgets**
    + Verbeterde verwarring matrix grafieken voor classificatie training.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Nieuwe functies**
  + Lijsten met tekenreeksen kunnen nu `read_*` worden doorgegeven als invoer voor methoden.

+ **Bug fixes en verbeteringen**
  + De prestaties `read_parquet` van is aanzienlijk verbeterd bij het uitvoeren in Spark.
  + Fixed an `column_type_builder` issue where failed in case of a single column with ambiguous date formats.

### <a name="azure-portal"></a>Azure Portal
+ **Voorbeeldfunctie**
  + Het streamen van logboek- en uitvoerbestanden is nu beschikbaar voor pagina's met uitvoerendetails. De bestanden streamen updates in realtime wanneer de voorschakelschakelaar is ingeschakeld.
  + De mogelijkheid om quota in te stellen op werkruimteniveau wordt vrijgegeven in preview. AmlCompute-quota worden toegewezen op abonnementsniveau, maar we stellen u nu in staat om dat quotum over werkruimten te verdelen en toe te wijzen voor eerlijk delen en beheren. Klik op het blad **Gebruik+Quota** in de linkernavigatiebalk van uw werkruimte en selecteer het tabblad **Quota configureren.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK voor Python v1.0.55

+ **Nieuwe functies**
  + Tokengebaseerde verificatie wordt nu ondersteund voor de oproepen naar het scoreeindpunt dat op AKS is geïmplementeerd. We blijven de huidige verificatie op basis van sleutels ondersteunen en gebruikers kunnen een van deze verificatiemechanismen tegelijk gebruiken.
  + Mogelijkheid om een blob-opslag te registreren die zich achter het virtuele netwerk (VNet) bevindt als een gegevensarchief.

+ **Bug fixes en verbeteringen**
  + **azureml-automl-core**
    + Lost een bug op waarbij de validatiegrootte voor CV-splitsingen klein is en resulteert in slecht voorspelde versus echte grafieken voor regressie en prognoses.
    + De logboekregistratie van prognosetaken op de afstandsbediening wordt verbeterd, nu wordt de gebruiker voorzien van een uitgebreide foutmelding als de run is mislukt.
    + Fixed failures of `Timeseries` if preprocess flag is True.
    + Sommige foutberichten voor het valideren van prognosegegevens actiever gemaakt.
    + Minder geheugenverbruik van AutoML wordt uitgevoerd door het laten vallen en/of lazy loading van datasets, vooral tussen processpawns
  + **azureml-contrib-explain-model**
    + Toegevoegd model_task vlag aan uitlegers om de gebruiker in staat te stellen standaard automatische gevolgtrekkinglogica voor modeltype te overschrijven
    + Widget wijzigingen: Installeert `contrib`automatisch met `nbextension` , niet meer installeren / inschakelen - ondersteuning uitleg met alleen wereldwijde functie belang (bijvoorbeeld Permutative)
    + Dashboard wijzigingen: - Box percelen en `beeswarm` viool percelen in aanvulling op `beeswarm` plot op samenvatting pagina - Veel sneller rerendering van plot op 'Top-k' slider te veranderen - nuttig bericht uit te leggen hoe top-k wordt berekend - Nuttige aanpasbare berichten in plaats van grafieken wanneer de gegevens niet verstrekt
  + **azureml-core**
    + De methode Model.package() toegevoegd om Docker-afbeeldingen en Dockerbestanden te maken die modellen en hun afhankelijkheden inkapselen.
    + Bijgewerkte lokale webservices om InferenceConfigs te accepteren die omgevingsobjecten bevatten.
    + Fixed Model.register() produceren van ongeldige modellen wanneer '.' (voor de huidige map) wordt doorgegeven als de parameter model_path.
    + Voeg Run.submit_child toe, de functionaliteit die Wordt weergelegd op Experiment.submit terwijl de run wordt opgegeven als de bovenliggende van de ingezonden onderliggende run.
    + Ondersteuningsconfiguratieopties van Model.register in Run.register_model.
    + Mogelijkheid om JAR-taken uit te voeren op een bestaand cluster.
    + Nu ondersteunen instance_pool_id en cluster_log_dbfs_path parameters.
    + Ondersteuning toegevoegd voor het gebruik van een omgevingsobject bij het implementeren van een model in een webservice. Het object Milieu kan nu worden geleverd als onderdeel van het object InferenceConfig.
    + Voeg appinsifht mapping toe voor nieuwe regio's - centralus - westus - northcentralus
    + Documentatie toegevoegd voor alle kenmerken in alle Datastore-klassen.
    + Blob_cache_timeout parameter `Datastore.register_azure_blob_container`toegevoegd aan .
    + Toegevoegd save_to_directory en load_from_directory methoden azureml.core.environment.Environment.
    + De commando's "az ml environment download" en "az ml environment register" toegevoegd aan de CLI.
    + De methode Omgeving.add_private_pip_wheel toegevoegd.
  + **azureml-explain-model**
    + Gegevenssettracking toegevoegd aan Uitleg met behulp van de gegevenssetservice (voorbeeld).
    + Verminderde standaard batchgrootte bij het streamen van globale uitleg van 10k naar 100.
    + Toegevoegd model_task vlag aan uitlegers om de gebruiker in staat te stellen standaard automatische gevolgtrekkinglogica voor modeltype te overschrijven.
  + **azureml-mlflow**
    + Fixed bug in mlflow.azureml.build_image where nested directories are ignored.
  + **azureml-pipeline-stappen**
    + Added mogelijkheid to run JAR-taken op bestaand Azure Databricks-cluster.
    + Ondersteuning toegevoegd instance_pool_id en cluster_log_dbfs_path parameters voor De stap van DatabricksStep.
    + Ondersteuning toegevoegd voor pijplijnparameters in de stap DatabricksStep.
  + **azureml-train-automl**
    + Toegevoegd `docstrings` voor de Ensemble gerelateerde bestanden.
    + Bijgewerkte documenten naar geschiktere taal voor `max_cores_per_iteration` en`max_concurrent_iterations`
    + De logboekregistratie van prognosetaken op de afstandsbediening wordt verbeterd, nu wordt de gebruiker voorzien van een uitgebreide foutmelding als de run is mislukt.
    + Verwijderd get_data `automlstep` uit pijplijnnotitieblok.
    + Gestart `dataprep` met `automlstep`ondersteuning in .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Nieuwe functies**
  + U nu vragen om specifieke inspecteurs (bijvoorbeeld histogram, scatterplot, enz.) uit te voeren op specifieke kolommen.
  + Een parallelargument toegevoegd `append_columns`aan . Als True, worden gegevens in het geheugen geladen, maar wordt de uitvoering parallel uitgevoerd; als False, uitvoering zal worden streaming, maar single-threaded.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK voor Python v1.0.53

+ **Nieuwe functies**
  + Automated Machine Learning ondersteunt nu training ONNX-modellen op het remote compute target
  + Azure Machine Learning biedt nu de mogelijkheid om de training van een eerdere run, controlepunt of modelbestanden te hervatten.
    + Meer informatie over het [gebruik van schatters om de training van een eerdere run te hervatten](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Bug fixes en verbeteringen**
  + **automl-client-core-nativeclient**
    + Fix the bug about lossing columns types after the transformation (bug linked);
    + Laat y_query een objecttype zijn dat geen(en) bevat aan het begin (#459519).
  + **azure-cli-ml**
    + CLI-opdrachten "modeldeploy" en "service-update" accepteren nu parameters, config-bestanden of een combinatie van beide. Parameters hebben voorrang op kenmerken in bestanden.
    + Modelbeschrijving kan nu worden bijgewerkt na registratie
  + **azureml-automl-core**
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (huidige laatste versie).
    + Het toevoegen van ondersteuning voor NimbusML-schatters & pijpleidingen die moeten worden gebruikt binnen AutoML-schatters.
    + Het oplossen van een bug in de Ensemble selectie procedure die onnodig groeide het resulterende ensemble, zelfs als de scores constant bleven.
    + Schakel hergebruik van sommige featurisaties in cv-splitsingen mogelijk in voor het voorspellen van taken. Dit versnelt de run-time van de setup uitgevoerd door ongeveer een factor n_cross_validations voor dure featurizations zoals vertragingen en rollende ramen.
    + Het aanpakken van een probleem als de tijd is uit panda's ondersteund tijdbereik. We verhogen nu een DataException als de tijd minder is dan pd. Timestamp.min of groter dan pd. Tijdstempel.max
    + Voorspellen maakt nu verschillende frequenties in de trein en testsets mogelijk als ze kunnen worden uitgelijnd. Bijvoorbeeld, "kwartaal starten in januari" en bij "driemaandelijkse start in oktober" kan worden uitgelijnd.
    + De eigenschap "parameters" is toegevoegd aan de TimeSeriesTransformer.
    + Oude uitzonderingsklassen verwijderen.
    + Bij prognosetaken accepteert `target_lags` de parameter nu één gehele waarde of een lijst met gehele getallen. Als het gehele getal is verstrekt, wordt er slechts één vertraging gemaakt. Als er een lijst wordt verstrekt, worden de unieke waarden van vertragingen genomen. target_lags=[1, 2, 2, 4] zorgt voor vertragingen van één, 2 en 4 periodes.
    + Fix the bug about losing columns types after the transformation (bug linked);
    + In `model.forecast(X, y_query)`, y_query een objecttype toestaan dat Geen(s) bevat aan het begin (#459519).
    + Verwachte waarden `automl` toevoegen aan uitvoer
  + **azureml-contrib-datadrift**
    +  Verbeteringen aan voorbeeldnotitieblok, waaronder overschakelen naar azureml-opendatasets in plaats van azureml-contrib-opendatasets en prestatieverbeteringen bij het verrijken van gegevens
  + **azureml-contrib-explain-model**
    + Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformation
    + Segmentaties toegevoegd aan afbeeldingsuitleg in afbeeldingsexplainer voor het AzureML-contrib-explain-modelpakket
    + Voeg scipy schaarse ondersteuning voor LimeExplainer toe
    + Toegevoegd `batch_size` aan mimic `include_local=False`explainer wanneer, voor het streamen van wereldwijde uitleg in batches om de uitvoeringstijd van DecisionTreeExplainableModel te verbeteren
  + **azureml-contrib-featureengineering**
    + Oplossing voor bellen set_featurizer_timeseries_params(): wijziging van dict-waardetype en null-controle - Notitieblok toevoegen voor `timeseries` featurizer
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (huidige laatste versie).
  + **azureml-core**
    + Toegevoegd de mogelijkheid om DBFS datastores te koppelen aan de AzureML CLI
    + Fixed the bug with datastore upload where `target_path` a empty folder is created if started with`/`
    + Probleem `deepcopy` opgelost in ServicePrincipalAuthentication.
    + De opdrachten "az ml environment show" en "az ml environment list" toegevoegd aan de CLI.
    + Omgevingen ondersteunen nu het opgeven van een base_dockerfile als alternatief voor een reeds gebouwde base_image.
    + De ongebruikte instelling RunConfiguration auto_prepare_environment is gemarkeerd als afgeschaft.
    + Modelbeschrijving kan nu worden bijgewerkt na registratie
    + Bugfix: Model en Image delete biedt nu meer informatie over het ophalen van upstream-objecten die van hen afhankelijk zijn als verwijderen mislukt als gevolg van een upstream afhankelijkheid.
    + Fixed bug that printed blank duration for deployments that occurs when creating a workspace for some environments.
    + Verbeterde werkruimte maakt uitzonderingen op fouten. Zodanig dat gebruikers niet zien "Niet in staat om werkruimte te maken. Niet in staat om..." als het bericht en in plaats daarvan zie de werkelijke creatie mislukking.
    + Ondersteuning toevoegen voor tokenverificatie in AKS-webservices.
    + Methode `get_token()` toevoegen `Webservice` aan objecten.
    + Cli-ondersteuning toegevoegd voor het beheren van machine learning-gegevenssets.
    + `Datastore.register_azure_blob_container`nu optioneel neemt `blob_cache_timeout` een waarde (in seconden) die blobfuse mount parameters configureert om cache vervaldatum voor deze datastore mogelijk te maken. De standaardinstelling is geen time-out, d.w.z. wanneer een blob wordt gelezen, blijft deze in de lokale cache totdat de taak is voltooid. De meeste taken geven de voorkeur aan deze instelling, maar sommige taken moeten meer gegevens uit een grote gegevensset lezen dan op hun knooppunten passen. Voor deze taken zal het afstemmen van deze parameter hen helpen slagen. Let op bij het afstemmen van deze parameter: het instellen van de waarde te laag kan resulteren in slechte prestaties, omdat de gegevens die in een tijdperk worden gebruikt, kunnen verlopen voordat ze opnieuw worden gebruikt. Dit betekent dat alle reads worden gedaan vanuit blob-opslag (d.w.z. het netwerk) in plaats van de lokale cache, wat een negatieve invloed heeft op de trainingstijden.
    + Modelbeschrijving kan nu correct worden bijgewerkt na registratie
    + Het verwijderen van modellen en afbeeldingen biedt nu meer informatie over upstream-objecten die ervan afhankelijk zijn, waardoor de verwijdering mislukt
    + Het gebruik van resources van externe uitvoeringen verbeteren met azureml.mlflow.
  + **azureml-explain-model**
    + Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformations argument for LIME explainer for raw feature importance in azureml-contrib-explain-model package Fixed transformation
    + voeg scipy schaarse ondersteuning voor LimeExplainer
    + toegevoegde vorm lineaire explainer wrapper, evenals een ander niveau tot tabelvormige explainer voor het uitleggen van lineaire modellen
    + voor mimic explainer in explain model bibliotheek, vaste fout wanneer include_local=False voor schaarse gegevensinvoer
    + verwachte waarden `automl` toevoegen aan de uitvoer
    + vaste permutatie functie belang wanneer transformaties argument geleverd om ruwe functie belang te krijgen
    + toegevoegd `batch_size` aan mimicexplainer na te bootsen wanneer, `include_local=False`voor het streamen van wereldwijde uitleg in batches om de uitvoeringstijd van DecisionTreeExplainableModel te verbeteren
    + voor model explainability bibliotheek, vaste blackbox explainers waar panda's dataframe input nodig is voor voorspelling
    + Fixed a `explanation.expected_values` bug where would sometimes return a float rather than a list with a float in it.
  + **azureml-mlflow**
    + Verbetering van de prestaties van mlflow.set_experiment(experiment_name)
    + Fix bug in gebruik van InteractiveLoginAuthentication voor mlflow tracking_uri
    + Het gebruik van resources van externe uitvoeringen verbeteren met azureml.mlflow.
    + De documentatie van het azureml-mlflow-pakket verbeteren
    + Patchbug waarbij mlflow.log_artifacts("my_dir") artefacten zou opslaan onder "my_dir/<artefact-paden>" in plaats van "<artefact-paden>"
  + **azureml-opendatasets**
    + Pin `pyarrow` `opendatasets` van oude versies (<0.14.0) als gevolg van geheugen probleem onlangs daar geïntroduceerd.
    + Azureml-contrib-opendatasets verplaatsen naar azureml-opendatasets.
    + Sta open datasetklassen toe om te worden geregistreerd op azure machine learning-werkruimte en maak naadloos gebruik van aml-gegevenssets.
    + NoaaIsdWeer verrijken prestaties in niet-SPARK-versie aanzienlijk.
  + **azureml-pipeline-stappen**
    + DBFS Datastore wordt nu ondersteund voor inputs en outputs in DatabricksStep.
    + Bijgewerkte documentatie voor Azure Batch Step met betrekking tot ingangen/uitvoer.
    + In AzureBatchStep gewijzigd *delete_batch_job_after_finish* standaardwaarde *in true*.
  + **azureml-telemetry**
    +  Azureml-contrib-opendatasets verplaatsen naar azureml-opendatasets.
    + Sta open datasetklassen toe om te worden geregistreerd op azure machine learning-werkruimte en maak naadloos gebruik van aml-gegevenssets.
    + NoaaIsdWeer verrijken prestaties in niet-SPARK-versie aanzienlijk.
  + **azureml-train-automl**
    + Bijgewerkte documentatie over get_output om het werkelijke retourtype weer te geven en aanvullende notities te verstrekken over het ophalen van belangrijke eigenschappen.
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (huidige laatste versie).
    + verwachte waarden `automl` toevoegen aan de uitvoer
  + **azureml-train-core**
    + Strings worden nu geaccepteerd als compute target voor Automated Hyperparameter Tuning
    + De ongebruikte instelling RunConfiguration auto_prepare_environment is gemarkeerd als afgeschaft.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Nieuwe functies**
  + Ondersteuning toegevoegd voor het rechtstreeks lezen van een bestand vanaf een http- of https-url.

+ **Bug fixes en verbeteringen**
  + Verbeterde foutmelding wanneer u een parketgegevensset probeert te lezen van een externe bron (die momenteel niet wordt ondersteund).
  + Fixed a bug when writing to Parquet file format in ADLS Gen 2, and updating the ADLS Gen 2 container name in the path.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuele interface
+ **Voorbeeldfuncties**
  + Toegevoegd "Execute R script" module in visuele interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK voor Python v1.0.48

+ **Nieuwe functies**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** is nu beschikbaar als **azureml-opendatasets.** Het oude pakket kan nog steeds werken, maar we raden u aan **azureml-opendatasets** te gebruiken die verder gaan voor rijkere mogelijkheden en verbeteringen.
    + Met dit nieuwe pakket u geopende gegevenssets registreren als gegevensset in azure machine learning-werkruimte en gebruikmaken van alle functionaliteiten die Gegevensset biedt.
    + Het bevat ook bestaande mogelijkheden, zoals het consumeren van open datasets als Panda's / SPARK-gegevensframes, en locatiejoins voor sommige gegevenssets zoals het weer.

+ **Voorbeeldfuncties**
    + HyperDriveConfig kan nu pijplijnobject accepteren als parameter ter ondersteuning van hyperparametertuning via een pijplijn.

+ **Bug fixes en verbeteringen**
  + **azureml-train-automl**
    + Fixed the bug about losing columns types after the transformation.
    + Fixed the bug to allow y_query to be a object type containing None(s) at the beginning.
    + Loste het probleem op in de ensembleselectieprocedure die het resulterende ensemble onnodig liet groeien, zelfs als de partituren constant bleven.
    + Probleem opgelost met whitelist_models- en blacklist_models-instellingen in AutoMLStep.
    + Fixed the issue that prevented the use of preprocessing when AutoML would have used in the context of Azure ML Pipelines.
  + **azureml-opendatasets**
    + Azureml-contrib-opendatasets verplaatst naar azureml-opendatasets.
    + Open datasetklassen konden worden geregistreerd op Azure Machine Learning-werkruimte en maakten naadloos gebruik van AML-gegevenssetmogelijkheden.
    + Verbeterde NoaaIsdWeather verrijken de prestaties in niet-SPARK-versie aanzienlijk.
  + **azureml-explain-model**
    + Bijgewerkte online documentatie voor interpreteerbaarheidsobjecten.
    + Toegevoegd `batch_size` aan mimic `include_local=False`explainer wanneer , voor het streamen van globale uitleg in batches om de uitvoeringstijd van DecisionTreeExplainableModel voor model explainability bibliotheek te verbeteren.
    + Fixed the `explanation.expected_values` issue where would sometimes return a float rather than a list with a float in it.
    + Verwachte waarden `automl` toegevoegd aan uitvoer voor mimic explainer in explainmodelbibliotheek uitleggen.
    + Vaste permutatie functie belang wanneer transformaties argument geleverd om ruwe functie belang te krijgen.
  + **azureml-core**
    + Toegevoegd de mogelijkheid om DBFS datastores te koppelen in de AzureML CLI.
    + Probleem opgelost met het uploaden van gegevensarchief waar een lege map wordt gemaakt als `target_path` deze is gestart met `/`.
    + Ingeschakelde vergelijking van twee gegevenssets.
    + Model- en imagedelete biedt nu meer informatie over het ophalen van upstream-objecten die van deze objecten afhankelijk zijn als verwijderen mislukt als gevolg van een upstreamafhankelijkheid.
    + De instelling voor ongebruikte runconfiguration in auto_prepare_environment afgeschaft.
  + **azureml-mlflow**
    + Verbeterde resourcegebruik van externe uitvoeringen die azureml.mlflow gebruiken.
    + Verbeterde documentatie van het azureml-mlflow pakket.
    + Fixed the issue where mlflow.log_artifacts("my_dir") would save artifacts under "my_dir/artifact-paths" instead of "artifact-paths".
  + **azureml-pipeline-core**
    + Parameter hash_paths voor alle pijplijnstappen wordt afgeschaft en wordt in de toekomst verwijderd. Standaard wordt de inhoud van de source_directory gehasht (met uitzondering van bestanden die worden vermeld in .amlignore of .gitignore)
    + Continue improving Module and ModuleStep to support compute type specific modules, to prepare for RunConfiguration integration and other changes to unlock compute type specific module usage in pipelines.
  + **azureml-pipeline-stappen**
    + AzureBatchStep: Verbeterde documentatie met betrekking tot ingangen/uitvoer.
    + AzureBatchStep: gewijzigd delete_batch_job_after_finish standaardwaarde in true.
  + **azureml-train-core**
    + Strings worden nu geaccepteerd als compute target voor Automated Hyperparameter Tuning.
    + De instelling voor ongebruikte runconfiguration in auto_prepare_environment afgeschaft.
    + Afgeschaftparameters `conda_dependencies_file_path` en `pip_requirements_file_path` in het `conda_dependencies_file` voordeel `pip_requirements_file` van en respectievelijk.
  + **azureml-opendatasets**
    + NoaaIsdWeer verrijken prestaties in niet-SPARK-versie aanzienlijk.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Nieuwe functies**
 + Dataflow-objecten kunnen nu worden overheen geïteerd, waardoor een reeks records ontstaat. Zie documentatie `Dataflow.to_record_iterator`voor .
  + Dataflow-objecten kunnen nu worden overheen geïteerd, waardoor een reeks records ontstaat. Zie documentatie `Dataflow.to_record_iterator`voor .

+ **Bug fixes en verbeteringen**
 + Verhoogde de robuustheid van DataPrep SDK.
 + Verbeterde verwerking van panda's DataFrames met niet-string Kolom Indexen.
 + Verbeterde de `to_pandas_dataframe` prestaties van in datasets.
 + Fixed a bug where Spark execution of Datasets failed when run in a multi-node environment.
  + Verhoogde de robuustheid van DataPrep SDK.
  + Verbeterde verwerking van panda's DataFrames met niet-string Kolom Indexen.
  + Verbeterde de `to_pandas_dataframe` prestaties van in datasets.
  + Fixed a bug where Spark execution of Datasets failed when run in a multi-node environment.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

We hebben een wijziging teruggedraaid die de prestaties verbeterde, omdat deze problemen veroorzaakte voor sommige klanten die Azure Databricks gebruikten. Als u een probleem hebt ondervonden op Azure Databricks, u upgraden naar versie 1.1.7 met een van de onderstaande methoden:
1. Voer dit script uit om te upgraden:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Maak het cluster opnieuw, waarmee de nieuwste Data Prep SDK-versie wordt geïnstalleerd.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK voor Python v1.0.45

+ **Nieuwe functies**
  + Voeg beslissingsboom surrogaatmodel toe om explainer na te bootsen in azureml-explain-modelpakket
  + Mogelijkheid om een CUDA-versie op te geven die moet worden geïnstalleerd op inferencing-afbeeldingen. Ondersteuning voor CUDA 9.0, 9.1 en 10.0.
  + Informatie over Azure ML-trainingsbasisafbeeldingen is nu beschikbaar bij [Azure ML Containers GitHub Repository](https://github.com/Azure/AzureML-Containers) en [DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Cli-ondersteuning toegevoegd voor pijplijnplanning. Run "az ml pipeline -h" voor meer informatie
  + Aangepaste Kubernetes-naamruimteparameter toegevoegd aan de configuratie van aks-webserviceimplementatie en CLI.
  + Afgeschafte parameter hash_paths voor alle pijplijnstappen
  + Model.register ondersteunt nu het registreren van meerdere afzonderlijke `child_paths` bestanden als één model met behulp van de parameter.

+ **Voorbeeldfuncties**
    + Scoring explainers kunnen nu optioneel conda- en pip-informatie opslaan voor betrouwbaardere serialisatie en deserialisatie.
    + Bug fix voor automatische functiekiezer.
    + Bijgewerkt mlflow.azureml.build_image naar de nieuwe api, gepatchte bugs blootgesteld door de nieuwe implementatie.

+ **Bug fixes en verbeteringen**
  + Verwijderde `paramiko` afhankelijkheid uit azureml-core. Waarschuwingen toegevoegd voor het toevoegen van verouderde compute target-methoden.
  + Verbeter de prestaties van run.create_children
  + In mimic explainer met binaire classificatie, de volgorde van waarschijnlijkheden vast te stellen wanneer de kans op de leraar wordt gebruikt voor het schalen van vormwaarden.
  + Verbeterde foutafhandeling en bericht voor geautomatiseerde machine learning.
  + Fixed the iteration time-out issue for Automated machine learning.
  + Verbeterde de transformatieprestaties van de tijdreeksen voor geautomatiseerde machine learning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Nieuwe functies**
  + Overzichtsfuncties toegevoegd voor`SummaryFunction.TOPVALUES`bovenwaarden (`SummaryFunction.BOTTOMVALUES`) en onderste waarden ( ).

+ **Bug fixes en verbeteringen**
  + Aanzienlijk verbeterd de `read_pandas_dataframe`prestaties van .
  + Fixed a bug `get_profile()` that would cause on a Dataflow pointing to binary files to fail.
  + Blootgesteld `set_diagnostics_collection()` om programmatisch in te schakelen/uitschakelen van de telemetrieverzameling mogelijk te maken.
  + Veranderde het `get_profile()`gedrag van . NaN-waarden worden nu genegeerd voor Min, Mean, Std en Sum, die in overeenstemming zijn met het gedrag van Panda's.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK voor Python v1.0.43

+ **Nieuwe functies**
  + Azure Machine Learning biedt nu eersteklas ondersteuning voor het populaire machine learning- en data-analyseframework Scikit-learn. Met behulp van [ `SKLearn` schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)kunnen gebruikers eenvoudig Scikit-learn-modellen trainen en implementeren.
    + Meer informatie over het uitvoeren van [hyperparametertuning met Scikit-learn met HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Added support for creating ModuleStep in pipelines along with Module and ModuleVersion classes to manage reusable compute units.
  + ACI webservices ondersteunen nu aanhoudende scoring_uri via updates. De scoring_uri verandert van IP naar FQDN. Het Dns Name Label voor FQDN kan worden geconfigureerd door de dns_name_label op deploy_configuration in te stellen.
  + Geautomatiseerde machine learning nieuwe functies:
    + STL featurizer voor prognoses
    + KMeans-clustering is ingeschakeld voor het vegen van functies
  + AmlCompute Quota goedkeuringen zijn net sneller geworden! We hebben het proces nu geautomatiseerd om uw quotaaanvragen binnen een drempelwaarde goed te keuren. Voor meer informatie over hoe quota werken, leest u [hoe quota worden beheerd](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Voorbeeldfuncties**
    + Integratie met [MLflow](https://mlflow.org) 1.0.0 tracking via azureml-mlflow pakket[(voorbeeld notebooks).](https://aka.ms/azureml-mlflow-examples)
    + Dien Jupyter notebook als een run. [API-naslagdocumentatie](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Openbare preview van [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) via azureml-contrib-datadrift pakket[(voorbeeld notebooks).](https://aka.ms/azureml-datadrift-example) Data Drift is een van de belangrijkste redenen waarom modelnauwkeurigheid na verloop van tijd degradeert. Het gebeurt wanneer gegevens die worden aangeboden om te modelleren in de productie is anders dan de gegevens die het model is opgeleid. AML Data Drift detector helpt de klant om gegevens drift te controleren en stuurt waarschuwing wanneer drift wordt gedetecteerd.

+ **Wijzigingen doorbreken**

+ **Bug fixes en verbeteringen**
  + RunConfiguration load and save ondersteunt het opgeven van een volledig bestandspad met volledige back-compat voor eerder gedrag.
  + Caching toegevoegd in ServicePrincipalAuthentication, standaard uitgeschakeld.
  + Logboekregistratie van meerdere percelen onder dezelfde metrische naam inschakelen.
  + Modelklasse nu correct geïmporteerd uit azureml.core (`from azureml.core import Model`).
  + In pijplijnstappen `hash_path` wordt de parameter nu afgeschaft. Nieuw gedrag is om hash complete source_directory, met uitzondering van bestanden die in .amlignore of .gitignore.
  + In pijpleidingpakketten `get_all` zijn `get_all_*` verschillende en methoden afgeschaft ten `list` gunste `list_*`van respectievelijk en .
  + azureml.core.get_run vereist niet langer dat klassen worden geïmporteerd voordat het oorspronkelijke runtype wordt teruggegeven.
  + Probleem opgelost waarbij sommige oproepen naar WebService Update geen update hebben geactiveerd.
  + Score time-out op AKS webservices moet tussen 5ms en 300000ms. Max toegestaan scoring_timeout_ms voor het scoren van verzoeken is gestoten van 1 min tot 5 min.
  + LocalWebservice-objecten `scoring_uri` hebben `swagger_uri` nu en eigenschappen.
  + Verplaatste uitvoermapcreatie en uitvoermap uploaden uit het gebruikersproces. Ingeschakeldrungeschiedenis SDK om in elk gebruikersproces uit te voeren. Dit moet een aantal synchronisatieproblemen oplossen die worden ondervonden door gedistribueerde trainingsruns.
  + De naam van het azureml-logboek dat is geschreven vanaf de naam van het gebruikersproces, bevat nu de procesnaam (alleen voor gedistribueerde training) en PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Bug fixes en verbeteringen**
  + Voor geïnterpreteerde datumwaarden met een jaarindeling van 2 cijfers is het bereik van geldige jaren bijgewerkt zodat deze overeenkomen met de release van Windows May. Het assortiment is gewijzigd van 1930-2029 naar 1950-2049.
  + Bij het lezen in `handleQuotedLineBreaks=True` `\r` een bestand en instelling , zal worden behandeld als een nieuwe regel.
  + Fixed a bug `read_pandas_dataframe` that caused to fail in some cases.
  + Verbeterde prestaties `get_profile`van .
  + Verbeterde foutmeldingen.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Nieuwe functies**
  + U nu de volgende expressietaalfuncties gebruiken om datumtijdwaarden in nieuwe kolommen te extraheren en te ontlopen.
    + `RegEx.extract_record()`haalt datumtijdelementen uit in een nieuwe kolom.
    + `create_datetime()`hiermee worden datumtijdobjecten gemaakt van afzonderlijke datumtijdelementen.
  + Wanneer `get_profile()`u aanroept, u nu zien dat quantile kolommen worden aangeduid als (est.) om duidelijk aan te geven dat de waarden benaderingen zijn.
  + U nu ** globbing gebruiken bij het lezen vanuit Azure Blob Storage.
    + Bijvoorbeeld.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Opgeloste fouten**
  + Een bug opgelost met betrekking tot het lezen van een parketbestand van een externe bron (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK voor Python v1.0.39
+ **Wijzigingen**
  + Voer configuratie auto_prepare_environment optie wordt afgeschaft, met auto voor te bereiden steeds de standaard.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Nieuwe functies**
  + Ondersteuning toegevoegd om uit een PostgresSQL-database te lezen, door read_postgresql te bellen of een Datastore te gebruiken.
    + Zie voorbeelden in handleidingen:
      + [Gegevensopnamenotitieblok](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Datastore-notitieblok](https://aka.ms/aml-data-prep-datastore-nb)

+ **Bug fixes en verbeteringen**
  + Vaste problemen met conversie van kolomtype:
  + Converteert nu correct een booleaanse of numerieke kolom naar een booleaanse kolom.
  + Nu mislukt niet wanneer u een datumkolom probeert in te stellen op datumtype.
  + Verbeterde JoinType-typen en bijbehorende referentiedocumentatie. Wanneer u twee gegevensstromen aansluit, u nu een van deze typen join opgeven:
    + GEEN, MATCH, INNER, ONGEËVENAARDLINKS, LINKSANTI, LINKSBUITEN, ONGEËVENAARDRECHTS, RECHTSANTI, RIGHTOUTER, FULLANTI, VOL.
  + Verbeterde gegevenstype-inferencing om meer datumnotaties te herkennen.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

In Azure-portal u nu:
+ Geautomatiseerde ML-experimenten maken en uitvoeren
+ Maak een notebook-vm om voorbeeld-Jupyter-notitieblokken of uw eigen notitieblokuit te proberen.
+ Gloednieuwe sectie Ontwerpen (Preview) in de Azure Machine Learning-werkruimte, die geautomatiseerde machine learning, visuele interface en vm's voor gehostnotitieblokken bevat
    + Automatisch een model maken met behulp van Geautomatiseerde machine learning
    + Een visuele interface slepen en neerzetten gebruiken om experimenten uit te voeren
    + Maak een notebook-vm om gegevens te verkennen, modellen te maken en services te implementeren.
+ Live grafiek en metrische update in run-rapporten en pagina's met details uitvoeren
+ Bijgewerkte bestandsviewer voor logboeken, uitvoer en momentopnamen in Pagina's met details uitvoeren.
+ Nieuwe en verbeterde ervaring met het maken van rapporten op het tabblad Experimenten.
+ Added mogelijkheid to download the config.json file from the Overview page of the Azure Machine Learning workspace.
+ Ondersteuning voor het maken van Azure Machine Learning-werkruimte vanuit de Azure Databricks-werkruimte.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK voor Python v1.0.33
+ **Nieuwe functies**
  + De methode _Workspace.create_ accepteert nu standaardclusterconfiguraties voor CPU- en GPU-clusters.
  + Als het maken van werkruimtes mislukt, worden afhankelijke resources gereinigd.
  + Standaard Azure Container Registry SKU is overgeschakeld naar basic.
  + Azure Container Registry wordt lui gemaakt, wanneer dat nodig is voor het maken van afbeeldingen of het maken van afbeeldingen.
  + Ondersteuning voor omgevingen voor trainingsruns.

### <a name="notebook-virtual-machine"></a>Virtuele notebookmachine 

Gebruik een Notebook VM als een veilige, bedrijfsklare hostingomgeving voor Jupyter-notebooks waarin u machine learning-experimenten programmeren, modellen implementeren als webeindpunten en alle andere bewerkingen uitvoeren die worden ondersteund door Azure Machine Learning SDK met Python.Het biedt verschillende mogelijkheden:
+ [Draai snel een vooraf geconfigureerde notebook-VM](tutorial-1st-experiment-sdk-setup.md) op met de nieuwste versie van Azure Machine Learning SDK en gerelateerde pakketten.
+ Toegang wordt beveiligd via bewezen technologieën, zoals HTTPS, Azure Active Directory-verificatie en autorisatie.
+ Betrouwbare cloudopslag van notitieblokken en code in uw Azure Machine Learning Workspace blob-opslagaccount.U uw notebook-vm veilig verwijderen zonder uw werk te verliezen.
+ Vooraf geïnstalleerde voorbeeldnotitieblokken om azure machine learning-functies te verkennen en te experimenteren.
+ Volledige aanpassingsmogelijkheden van Azure VM's, elk VM-type, pakketten, stuurprogramma's. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK voor Python v1.0.33 uitgebracht.

+ Azure ML Hardware Accelerated Models op [FPGA's](how-to-deploy-fpga-web-service.md) is algemeen beschikbaar.
  + U nu [het azureml-accel-modellenpakket gebruiken](how-to-deploy-fpga-web-service.md) om:
    + Train de gewichten van een ondersteund diep neuraal netwerk (ResNet 50, ResNet 152, DenseNet-121, VGG-16 en SSD-VGG)
    + Gebruik transfer learning met de ondersteunde DNN
    + Registreer het model bij Model Management Service en containeriseer het model
    + Het model implementeren op een Azure VM met een FPGA in een AKS-cluster (Azure Kubernetes Service)
  + De container implementeren op een [Azure Data Box Edge-serverapparaat](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Uw gegevens scoren met het gRPC-eindpunt met dit [voorbeeld](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

+ Functievegen om dynamisch :::no-loc text="featurizers"::: toevoegen voor prestatieoptimalisatie mogelijk te maken. Nieuw :::no-loc text="featurizers":::: werkinbeddingen, gewicht van bewijs, doelcoderingen, tekstdoelcodering, clusterafstand
+ Smart CV voor het verwerken van trein/geldige splitsingen in geautomatiseerde ML
+ Weinig geheugenoptimalisatiewijzigingen en prestatieverbetering runtime
+ Prestatieverbetering in modeluitleg
+ ONNX-modelconversie voor lokale run
+ Ondersteuning voor Subsampling toegevoegd
+ Intelligent stoppen wanneer er geen exitcriteria zijn gedefinieerd
+ Gestapelde ensembles

+ Tijdreeksvoorspelling
  + Nieuwe voorspellingsfunctie
  + U nu cross validatie van rolling-origin gebruiken op tijdreeksgegevens
  + Nieuwe functionaliteit toegevoegd aan vertragingen in tijdreeksen configureren
  + Nieuwe functionaliteit toegevoegd aan rolling window aggregate functies te ondersteunen
  + Nieuwe vakantiedetectie en featurizer wanneer landcode wordt gedefinieerd in experimentinstellingen

+ Azure Databricks
  + Ingeschakelde tijdreeksprognoses en modeluitleg/interpreteerbaarheidsmogelijkheid
  + U nu geautomatiseerde ML-experimenten annuleren en hervatten (doorgaan)
  + Ondersteuning toegevoegd voor multicore-verwerking

### <a name="mlops"></a>MLOps Mlops
+ **Lokale implementatie & foutopsporing voor het scoren van containers**<br/> U nu een ML-model lokaal implementeren en snel herhalen op uw scorebestand en afhankelijkheden om ervoor te zorgen dat ze zich gedragen zoals verwacht.

+ **Geïntroduceerd InferenceConfig & Model.deploy()**<br/> Modelimplementatie ondersteunt nu het opgeven van een bronmap met een invoerscript, hetzelfde als een RunConfig.  Bovendien is de modelimplementatie vereenvoudigd tot één opdracht.

+ **Git-referentietracking**<br/> Klanten vragen al enige tijd om basismogelijkheden voor Git-integratie, omdat het helpt bij het bijhouden van een end-to-end audittrail. We hebben tracking geïmplementeerd in grote entiteiten in Azure ML voor Metagegevens met git (repo, commit, clean state). Deze informatie wordt automatisch verzameld door de SDK en CLI.

+ **Modelprofilering & validatieservice**<br/> Klanten klagen vaak over de moeilijkheid om de compute-waarde van hun inferentieservice goed te vergroten. Met onze modelprofileringsservice kan de klant voorbeeldingangen leveren en zullen we zich profileren in 16 verschillende CPU/geheugenconfiguraties om een optimale grootte voor implementatie te bepalen.

+ **Breng uw eigen basisafbeelding mee voor gevolgtrekking**<br/> Een andere veel voorkomende klacht was de moeilijkheid bij de overgang van experimenten naar gevolgtrekking RE delen afhankelijkheden. Met onze nieuwe basismogelijkheid voor het delen van afbeeldingen u nu uw experimenten opnieuw gebruiken, afhankelijkheden en alles, voor gevolgtrekking. Dit moet de implementaties versnellen en de kloof van de binnenste naar de buitenste lus verkleinen.

+ **Verbeterde Swagger schema generatie ervaring**<br/> Onze vorige swagger generatie methode was foutgevoelig en onmogelijk te automatiseren. We hebben een nieuwe in-line manier van het genereren van branie schema's van elke Python-functie via decorateurs. We hebben deze code open-sourced en ons protocol voor het genereren van schema's is niet gekoppeld aan het Azure ML-platform.

+ **Azure ML CLI is algemeen beschikbaar (GA)**<br/> Modellen kunnen nu worden geïmplementeerd met één CLI-opdracht. We kregen algemene feedback van klanten dat niemand een ML-model implementeert vanuit een Jupyter-laptop. De [**CLI-referentiedocumentatie**](https://aka.ms/azmlcli) is bijgewerkt.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK voor Python v1.0.30 uitgebracht.

Het [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) werd geïntroduceerd om een nieuwe versie van een gepubliceerde pijplijn toe te voegen met behoud van hetzelfde eindpunt.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Opmerking: Data Prep Python SDK `numpy` `pandas` wordt niet meer geïnstalleerd en pakketten. Zie [bijgewerkte installatie-instructies](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nieuwe functies**
  + U nu de pivot-transformatie gebruiken.
    + Handleiding: [Pivot-notitieblok](https://aka.ms/aml-data-prep-pivot-nb)
  + U nu reguliere expressies gebruiken in native functies.
    + Voorbeelden:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + U `to_upper`  nu `to_lower`  gebruik maken en functies in expressietaal.
  + U nu het aantal unieke waarden van elke kolom in een gegevensprofiel zien.
  + Voor sommige van de veelgebruikte leesstappen kunt `infer_column_types` u nu het argument doorgeven. Als dit is `True`ingesteld op , probeert Data Prep kolomtypen te detecteren en automatisch om te zetten.
    + `inference_arguments`is nu afgeschaft.
  + Je `Dataflow.shape`nu bellen.

+ **Bug fixes en verbeteringen**
  + `keep_columns` accepteert nu een extra `validate_column_exists`optioneel argument , `keep_columns` dat controleert of het resultaat van kolommen zal bevatten.
  + Alle leesstappen (die uit een bestand worden `verify_exists`gelezen) accepteren nu een extra optioneel argument .
  + Verbeterde prestaties van het lezen van panda's dataframe en het verkrijgen van gegevensprofielen.
  + Fixed a bug where slicing a single step from a Dataflow failed with a single index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + U nu een bestaand Script opnieuw indienen op een bestaand extern gegevenscluster.
  + U nu een gepubliceerde pijplijn uitvoeren met nieuwe parameters op het tabblad Pijplijnen.
  + Voer details nu ondersteunt een nieuwe Snapshot-bestandsviewer. U een momentopname van de map bekijken wanneer u een specifieke run hebt verzonden. U ook het notitieblok downloaden dat is ingediend om de run te starten.
  + U nu bovenliggende uitvoeringen annuleren vanaf de Azure-portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK voor Python v1.0.23

+ **Nieuwe functies**
  + De Azure Machine Learning SDK ondersteunt nu Python 3.7.
  + Azure Machine Learning DNN Estimators biedt nu ingebouwde ondersteuning voor meerdere versies. `TensorFlow`  Zo accepteert schatter nu `framework_version` een parameter en kunnen gebruikers versie '1.10' of '1.12' opgeven. Voor een lijst met de versies die worden `get_supported_versions()` ondersteund door uw huidige `TensorFlow.get_supported_versions()`SDK-release, u een beroep doen op de gewenste frameworkklasse (bijvoorbeeld).
  Zie de [DNN Estimator-documentatie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)voor een lijst met de versies die worden ondersteund door de nieuwste SDK-release.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Nieuwe functies**
  + U meerdere Datastore/DataPath/DataReference-bronnen lezen met behulp van read_* transformaties.
  + U de volgende bewerkingen uitvoeren op kolommen om een nieuwe kolom te maken: verdeling, vloer, modulo, kracht, lengte.
  + Data Prep maakt nu deel uit van de Azure ML-diagnostische suite en registreert standaard diagnostische gegevens.
    + Om dit uit te schakelen, stelt u deze omgevingsvariabele in op true: DISABLE_DPREP_LOGGER

+ **Bug fixes en verbeteringen**
  + Verbeterde codedocumentatie voor veelgebruikte klassen en functies.
  + Een bug opgelost in auto_read_file die excel-bestanden niet kon lezen.
  + Added option to overwrite the folder in read_pandas_dataframe.
  + Verbeterde prestaties van dotnetcore2 afhankelijkheidinstallatie en ondersteuning toegevoegd voor Fedora 27/28 en Ubuntu 1804.
  + Verbeterde de prestaties van het lezen van Azure Blobs.
  + Kolomtypedetectie ondersteunt nu kolommen van het type Long.
  + Fixed a bug where some date values were being displayed as timestamps instead of Python datetime objects.
  + Fixed a bug where some type counts were being displayed as doubles instead of integers.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK voor Python v1.0.21

+ **Nieuwe functies**
  + De methode *azureml.core.Run.create_children* maakt het mogelijk om meerdere onderliggende onderliggende runs met één gesprek te maken.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Wijzigingen doorbreken**
  + Het concept van het Data Prep Pakket is afgeschaft en wordt niet langer ondersteund. In plaats van meerdere gegevensstromen in één pakket voort te houden, u gegevensstromen afzonderlijk blijven bestaan.
    + Handleiding: Notitieblok [gegevensstromen openen en opslaan](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nieuwe functies**
  + Data Prep kan nu kolommen herkennen die overeenkomen met een bepaald semantisch type en dienovereenkomstig worden gesplitst. De momenteel ondersteunde STypes zijn: e-mailadres, geografische coördinaten (latitude & lengtegraad), IPv4- en IPv6-adressen, Amerikaans telefoonnummer en Amerikaanse postcode.
    + Handleiding: notitieblok [van semantische typen](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data Prep ondersteunt nu de volgende bewerkingen om een resulterende kolom te genereren uit twee numerieke kolommen: aftrekken, vermenigvuldigen, delen en modulo.
  + U kunt `verify_has_data()` een beroep doen op een dataflow om te controleren of de Gegevensstroom records zou produceren als deze wordt uitgevoerd.

+ **Bug fixes en verbeteringen**
  + U nu het aantal opslaglocaties opgeven dat u in een histogram moet gebruiken voor numerieke kolomprofielen.
  + De `read_pandas_dataframe` transformatie vereist nu dat het DataFrame tekenreeks- of byte-getypte kolomnamen heeft.
  + Fixed a bug `fill_nulls` in the transform, where values were not correct filled in if the column was missing.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK voor Python v1.0.18

 + **Wijzigingen**
   + Het azureml-tensorboard-pakket vervangt azureml-contrib-tensorboard.
   + Met deze release u een gebruikersaccount instellen op uw beheerde compute cluster (amlcompute), terwijl u het maakt. Dit kan worden gedaan door deze eigenschappen in de inrichtingsconfiguratie door te geven. Meer informatie vindt u in de [referentiedocumentatie van SDK.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Nieuwe functies**
  + Ondersteunt nu het toevoegen van twee numerieke kolommen om een resulterende kolom te genereren met behulp van de expressietaal.

+ **Bug fixes en verbeteringen**
  + Verbeterde de documentatie en parametercontrole voor random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Bug fix**
  + Fixed a Service Principal authentication issue that was caused by a API change.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK voor Python v1.0.17

+ **Nieuwe functies**

  + Azure Machine Learning biedt nu eersteklas ondersteuning voor populaire DNN-framework Chainer. Met [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) behulp van klasse gebruikers kunnen gemakkelijk trainen en implementeren Chainer modellen.
    + Meer informatie over het [uitvoeren van gedistribueerde training met ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Meer informatie over het uitvoeren van [hyperparametertuning met Chainer met HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines heeft de mogelijkheid toegevoegd om een pijplijnrun te activeren op basis van wijzigingen in het gegevensarchief. Het [notitieblok van de pijplijnplanning](https://aka.ms/pl-schedule) wordt bijgewerkt om deze functie te presenteren.

+ **Bug fixes en verbeteringen**
  + We hebben ondersteuning toegevoegd in Azure Machine Learning-pijplijnen voor het instellen van de eigenschap source_directory_data_store op een gewenste gegevensarchief (zoals een blob-opslag) op [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) die worden geleverd aan de [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) Standaard worden Azure File Store gebruikt als back-upgegevensarchief, dat mogelijk problemen oploopt bij het gelijktijdig uitvoeren van een groot aantal stappen.

### <a name="azure-portal"></a>Azure Portal

+ **Nieuwe functies**
  + Nieuwe ervaring met drag- en droptable-editor voor rapporten. Gebruikers kunnen een kolom van de put naar het tabelgebied slepen waar een voorbeeld van de tabel wordt weergegeven. De kolommen kunnen opnieuw worden gerangschikt.
  + Nieuwe logboekenbestandsviewer
  + Koppelingen naar experimentuitvoeringen, rekenkracht, modellen, afbeeldingen en implementaties vanaf het tabblad Activiteiten

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Nieuwe functies**
  + Data Prep ondersteunt nu het schrijven van bestandsstromen vanuit een dataflow. Biedt ook de mogelijkheid om de bestandsstreamnamen te manipuleren om nieuwe bestandsnamen te maken.
    + Handleiding: werken [met het notitieblok Bestandsstromen](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Bug fixes en verbeteringen**
  + Verbeterde prestaties van t-Digest op grote gegevenssets.
  + Data Prep ondersteunt nu het lezen van gegevens van een DataPath.
  + Een hete codering werkt nu op booleaanse en numerieke kolommen.
  + Andere diverse bug fixes.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning SDK voor Python v1.0.15

+ **Nieuwe functies**
  + Azure Machine Learning Pipelines added AzureBatchStep[(notebook),](https://aka.ms/pl-azbatch)HyperDriveStep (notebook) en time-based scheduling functionality[(notebook).](https://aka.ms/pl-schedule)
  +  DataTranferStep bijgewerkt om te werken met Azure SQL Server en Azure-database voor PostgreSQL[(notebook).](https://aka.ms/pl-data-trans)

+ **Wijzigingen**
  + Afgeschaft `PublishedPipeline.get_published_pipeline` ten gunste van. `PublishedPipeline.get`
  + Afgeschaft `Schedule.get_schedule` ten gunste van. `Schedule.get`

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning Data Prep SDK v1.0.12

+ **Nieuwe functies**
  + Data Prep ondersteunt nu lezen vanuit een Azure SQL-database met Behulp van Datastore.

+ **Wijzigingen**
  + Verbeterde de geheugenprestaties van bepaalde bewerkingen op grote gegevens.
  + `read_pandas_dataframe()``temp_folder` moet nu worden gespecificeerd.
  + De `name` eigenschap `ColumnProfile` op is afgeschaft - `column_name` gebruik in plaats daarvan.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK voor Python v1.0.10

+ **Wijzigingen**:
  + Azure ML SDK heeft geen azure-cli-pakketten meer als afhankelijkheid. Specifiek, azure-cli-core en azure-cli-profile afhankelijkheden zijn verwijderd uit azureml-core. Dit zijn de wijzigingen die van invloed zijn op de gebruiker:
      + Als u "az login" uitvoert en vervolgens azureml-sdk gebruikt, zal de SDK de browser of apparaatcode nog een keer inloggen. Het zal geen gebruik maken van een geloofsbrieven staat gemaakt door "az login".
    + Voor Azure CLI-verificatie, zoals het gebruik van 'az-login', gebruikt u _azureml.core.authentication.AzureCliAuthentication-klasse._ Installeer voor Azure _CLI-verificatie azure-cli_ in de Python-omgeving waar u azureml-sdk hebt geïnstalleerd.
    + Als u 'az-login' doet met behulp van een serviceprincipal voor automatisering, raden we u aan _azureml.core.authentication.ServicePrincipalAuthentication-klasse_ te gebruiken, omdat azureml-sdk geen referentiesstatus gebruikt die is gemaakt door azure CLI.

+ **Bug fixes**: Deze release bevat meestal kleine bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning Data Prep SDK v1.0.8

+ **Opgeloste fouten**
  + Verbeterde de prestaties van het verkrijgen van gegevensprofielen.
  + Kleine bugs opgelost met betrekking tot foutrapportage.

### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ Nieuwe ervaring voor het slepen en neerzetten van grafieken voor rapporten. Gebruikers kunnen een kolom of kenmerk van de bron naar het grafiekgebied slepen, waar het systeem automatisch een geschikt grafiektype voor de gebruiker selecteert op basis van het type gegevens. Gebruikers kunnen het grafiektype wijzigen in andere toepasselijke typen of extra kenmerken toevoegen.

    Ondersteunde grafiektypen:
    - Lijndiagram
    - Histogram
    - Gestapeld staafdiagram
    - Vakplot
    - Spreidingsplot
    - Bellenplot
+ Het portaal genereert nu dynamisch rapporten voor experimenten. Wanneer een gebruiker een run naar een experiment verzendt, wordt een rapport automatisch gegenereerd met geregistreerde statistieken en grafieken om vergelijking tussen verschillende uitvoeringen mogelijk te maken.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK voor Python v1.0.8

+ **Bug fixes**: Deze release bevat meestal kleine bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning Data Prep SDK v1.0.7

+ **Nieuwe functies**
  + Datastore verbeteringen (gedocumenteerd in [Datastore how-to-guide)](https://aka.ms/aml-data-prep-datastore-nb)
    + Added ability to read from and write to Azure File Share and ADLS Datastores in scale-up.
    + Bij het gebruik van Datastores ondersteunt Data Prep nu het gebruik van serviceprincipal-authenticatie in plaats van interactieve verificatie.
    + Toegevoegd ondersteuning voor wasb en wasbs urls.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning Data Prep SDK v1.0.6

+ **Opgeloste fouten**
  + Bug opgelost met lezen uit openbare leesbare Azure Blob-containers op Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK voor Python v1.0.6
+ **Bug fixes**: Deze release bevat meestal kleine bug fixes

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning Data Prep SDK v1.0.4

+ **Nieuwe functies**
  + `to_bool`functie maakt het nu mogelijk dat niet-overeenkomende waarden worden geconverteerd naar foutwaarden. Dit is het nieuwe `to_bool` standaardfoutgedrag voor en `set_column_types`, terwijl het vorige standaardgedrag was om niet-overeenkomende waarden om te zetten in False.
  + Bij `to_pandas_dataframe`het aanroepen is er een nieuwe optie om null/ontbrekende waarden in numerieke kolommen te interpreteren als NaN.
  + Added ability to check the return type of some expressions to ensure type consistency and fail early.
  + U `parse_json` nu aanroepen om waarden in een kolom te ontleden als JSON-objecten en deze uit te vouwen in meerdere kolommen.

+ **Opgeloste fouten**
  + Fixed a bug `set_column_types` that crashed in Python 3.5.2.
  + Fixed a bug that crashed when connecting to Datastore using an AML image.

+ **Updates**
  * [Voorbeeldnotitieblokken](https://aka.ms/aml-data-prep-notebooks) voor aan de slag met zelfstudies, casestudies en handleidingen.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Algemene beschikbaarheid

Azure Machine Learning is nu algemeen beschikbaar.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
Met deze release kondigen we een nieuwe beheerde compute-ervaring aan via de [Azure Machine Learning Compute.](how-to-set-up-training-targets.md#amlcompute) Dit rekendoel vervangt Azure Batch AI compute voor Azure Machine Learning.

Dit rekendoel:
+ Wordt gebruikt voor modeltraining en batch-gevolgtrekking/-score
+ Is single- tot multi-node compute
+ Is het clusterbeheer en de taakplanning voor de gebruiker
+ Automatisch schalen standaard
+ Ondersteuning voor zowel CPU- als GPU-bronnen
+ Maakt het gebruik van VM's met lage prioriteit mogelijk voor lagere kosten

Azure Machine Learning Compute kan worden gemaakt in Python, met azure portal of de CLI. Het moet worden gemaakt in de regio van uw werkruimte en kan niet worden gekoppeld aan een andere werkruimte. Dit rekendoel maakt gebruik van een Docker-container voor uw run en verpakt uw afhankelijkheden om dezelfde omgeving op al uw knooppunten te repliceren.

> [!Warning]
> We raden u aan een nieuwe werkruimte te maken voor het gebruik van Azure Machine Learning Compute. Er is een externe kans dat gebruikers die Azure Machine Learning Compute proberen te maken vanuit een bestaande werkruimte een fout kunnen zien. Bestaande rekenkracht in uw werkruimte moet onaangetast blijven werken.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning SDK voor Python v1.0.2
+ **Wijzigingen doorbreken**
  + Met deze release verwijderen we ondersteuning voor het maken van een VM van Azure Machine Learning. U nog steeds een bestaande cloud-VM of een externe on-premises server toevoegen.
  + We verwijderen ook de ondersteuning voor BatchAI, die nu allemaal moeten worden ondersteund via Azure Machine Learning Compute.

+ **Nieuw**
  + Voor machine learning-pijplijnen:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep (HyperDriveStep)](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Bijgewerkt**
  + Voor machine learning-pijplijnen:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepteert nu runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) kopieert nu van en naar een SQL-gegevensbron
    + Functionaliteit plannen in SDK om schema's te maken en bij te werken voor het uitvoeren van gepubliceerde pijplijnen

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning Data Prep SDK v0.5.2
+ **Wijzigingen doorbreken**
  * `SummaryFunction.N`werd omgedoopt `SummaryFunction.Count`tot .

+ **Insectenmoeilijke**
  * Gebruik het nieuwste AML Run Token bij het lezen van en schrijven naar datastores op externe uitvoeringen. Als het AML Run-token eerder is bijgewerkt in Python, wordt de Runtime Data Prep niet bijgewerkt met het bijgewerkte AML Run-token.
  * Extra duidelijkere foutmeldingen
  * to_spark_dataframe() crasht niet meer `Kryo` wanneer Spark serialisatie gebruikt
  * Value Count Inspector kan nu meer dan 1000 unieke waarden weergeven
  * Random Split mislukt niet meer als de oorspronkelijke Dataflow geen naam heeft

+ **Meer informatie**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documenten en notitieblokken
+ ML-pijpleidingen
  + Nieuwe en bijgewerkte notitieblokken om aan de slag te gaan met voorbeelden van pijplijnen, batchscoping en stijloverdracht:https://aka.ms/aml-pipeline-notebooks
  + Meer informatie over het [maken van uw eerste pijplijn](how-to-create-your-first-pipeline.md)
  + Meer informatie over het [uitvoeren van batchvoorspellingen met pijplijnen](how-to-use-parallel-run-step.md)
+ Azure Machine Learning-rekendoel
  + [Voorbeeldnotitieblokken](https://aka.ms/aml-notebooks) worden nu bijgewerkt om de nieuwe beheerde compute te gebruiken.
  + [Meer informatie over deze rekenkracht](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ Azure [Machine Learning Compute-typen](how-to-set-up-training-targets.md#amlcompute) maken en beheren in de portal.
+ Monitor het quotumgebruik en [aanvraagquotum](how-to-manage-quotas.md) voor Azure Machine Learning Compute.
+ Bekijk de status van het Azure Machine Learning Compute Compute-cluster in realtime.
+ Ondersteuning voor virtueel netwerk is toegevoegd voor Azure Machine Learning Compute en het maken van Azure Kubernetes Service.
+ Voer uw gepubliceerde pijplijnen opnieuw uit met bestaande parameters.
+ Nieuwe [geautomatiseerde machine learning grafieken](how-to-understand-automated-ml.md) voor classificatiemodellen (lift, winsten, kalibratie, functie belang grafiek met model explainability) en regressie modellen (resten en functie belang grafiek met model explainability).
+ Pijplijnen kunnen worden bekeken in Azure-portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning SDK voor Python v0.1.80

+ **Wijzigingen doorbreken**
  * *azureml.train.widgets* namespace is verplaatst naar *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* deprecates the following classes - *azureml.core.compute.BatchAICompute* and *azureml.core.dSVMCompute*. De laatste klasse wordt verwijderd in latere releases. De klasse AmlCompute heeft nu een eenvoudigere definitie en heeft gewoon een vm_size en de max_nodes nodig en schaalt uw cluster automatisch van 0 naar de max_nodes wanneer een taak wordt ingediend. Onze [voorbeeldnotitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zijn bijgewerkt met deze informatie en moeten u voorbeelden van gebruik geven. We hopen dat je deze vereenvoudiging en nog veel meer spannende functies te komen in een latere release!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning Data Prep SDK v0.5.1

Meer informatie over de Data Prep SDK lees [referentiedocumenten](https://aka.ms/data-prep-sdk).
+ **Nieuwe functies**
   * Een nieuwe DataPrep CLI gemaakt om DataPrep-pakketten uit te voeren en het gegevensprofiel voor een gegevensset of gegevensstroom te bekijken
   * Opnieuw ontworpen SetColumnType API om de bruikbaarheid te verbeteren
   * Omgedoopt tot smart_read_file naar auto_read_file
   * Nu inclusief scheeftrekken en kurtose in het dataprofiel
   * Kan monster met gestratificeerde bemonstering
   * Kan lezen uit zip-bestanden die CSV-bestanden bevatten
   * Kan gegevenssets rij-gewijs splitsen met Random Split (bijvoorbeeld in testtreinsets)
   * Kan alle kolomgegevenstypen uit een gegevensstroom of een gegevensprofiel ophalen door`.dtypes`
   * Kan het aantal rijen ophalen uit een gegevensstroom of een gegevensprofiel door`.row_count`

+ **Insectenmoeilijke**
   * Vaste lange tot dubbele conversie
   * Fixed assert after any add column Fixed assert after any add column Fixed assert after any add column Fixed assert
   * Een probleem opgelost met FuzzyGrouping, waarbij het in sommige gevallen geen groepen zou detecteren
   * Vaste sorteerfunctie voor het respecteren van de sorteervolgorde met meerdere kolommen
   * Vaste en/of uitdrukkingen die `pandas` vergelijkbaar zijn met hoe ermee omgaat
   * Vaste lezing van dbfs pad
   * Foutberichten begrijpelijker gemaakt
   * Nu niet meer mislukt bij het lezen op remote compute target met behulp van een AML-token
   * Nu niet langer mislukt op Linux DSVM
   * Nu niet langer vastloopt wanneer niet-tekenreekswaarden in tekenreekspredicaten zijn
   * Verwerkt nu beweringsfouten wanneer Dataflow correct moet mislukken
   * Ondersteunt nu gemonteerde opslaglocaties op Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
De Azure-portal voor Azure Machine Learning heeft de volgende updates:
  * Een nieuw tabblad **Pijplijnen** voor gepubliceerde pijplijnen.
  * Ondersteuning toegevoegd voor het koppelen van een bestaand HDInsight-cluster als rekendoel.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning SDK voor Python v0.1.74

+ **Wijzigingen doorbreken**
  * *Workspace.compute_targets, datastores, experimenten, afbeeldingen, modellen en *webservices* zijn eigenschappen in plaats van methoden. Vervang *workspace.compute_targets()* bijvoorbeeld door *Workspace.compute_targets*.
  * *Run.get_context* deprecates *Run.get_submitted_run*. De laatste methode zal worden verwijderd in de volgende releases.
  * *PipelineData-klasse* verwacht nu een datastore-object als parameter in plaats van datastore_name. Op dezelfde manier accepteert *Pipeline* default_datastore in plaats van default_datastore_name.

+ **Nieuwe functies**
  * Het [voorbeeldnotitieblok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) azure machine learning-pijplijnen maakt nu gebruik van MPI-stappen.
  * De widget RunDetails voor Jupyter-notitieblokken wordt bijgewerkt om een visualisatie van de pijplijn weer te geven.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Azure Machine Learning Data Prep SDK v0.4.0

+ **Nieuwe functies**
  * Aantal typen toegevoegd aan gegevensprofiel
  * Value Count en Histogram is nu beschikbaar
  * Meer percentien in dataprofiel
  * De Mediaan is beschikbaar in Samenvatten
  * Python 3.7 wordt nu ondersteund
  * Wanneer u een dataflow opslaat die datastores bevat in een DataPrep-pakket, blijft de gegevenswinkelinformatie bestaan als onderdeel van het DataPrep-pakket
  * Schrijven naar datastore wordt nu ondersteund

+ **Bug opgelost**
  * 64-bits ongesigneerde gehele overloop worden nu goed behandeld op Linux
  * Onjuist tekstlabel voor platte tekstbestanden in smart_read
  * Tekenreekskolomtype wordt nu weergegeven in de weergave metrische gegevens
  * Typetelling is nu vastgesteld om ValueKinds weer te geven die zijn toegewezen aan één FieldType in plaats van afzonderlijke
  * Write_to_csv mislukt niet meer wanneer pad als tekenreeks wordt verstrekt
  * Bij gebruik van Vervangen, het verlaten van "zoeken" leeg zal niet langer mislukken

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning SDK voor Python v0.1.68

+ **Nieuwe functies**
  * Ondersteuning voor meerdere tenant's bij het maken van nieuwe werkruimte.

+ **Bugs opgelost**
  * U hoeft de pynacl-bibliotheekversie niet langer vast te maken bij het implementeren van de webservice.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning Data Prep SDK v0.3.0

+ **Nieuwe functies**
  * Methode toegevoegd transform_partition_with_file(script_path), waarmee gebruikers het pad van een Python-bestand kunnen doorgeven om uit te voeren

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning SDK voor Python v0.1.65
[Versie 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) bevat nieuwe functies, meer documentatie, bugfixes en meer [voorbeeldnotitieblokken.](https://aka.ms/aml-notebooks)

Bekijk [de lijst met bekende problemen](resource-known-issues.md) om meer te weten te komen over bekende bugs en tijdelijke oplossingen.

+ **Wijzigingen doorbreken**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services return dictionary, eerder geretourneerde lijst. Zie [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-documentatie.

  * Geautomatiseerde Machine Learning verwijderd genormaliseerde gemiddelde vierkante fout van de primaire statistieken.

+ **HyperDrive (HyperDrive)**
  * Verschillende HyperDrive bug fixes voor Bayesian, Prestatieverbeteringen voor get Metrics calls.
  * Tensorflow 1.10 upgrade van 1.9
  * Docker beeldoptimalisatie voor koude start.
  * Taken rapporteren nu de juiste status, zelfs als ze worden afgesloten met een andere foutcode dan 0.
  * RunConfig-attribuutvalidatie in SDK.
  * HyperDrive run object ondersteunt annuleren vergelijkbaar met een gewone run: geen parameters hoeven te passeren.
  * Widgetverbeteringen voor het handhaven van de status van vervolgkeuzewaarden voor gedistribueerde uitvoeringen en HyperDrive-uitvoeringen.
  * TensorBoard en andere logbestanden ondersteunen vaste voor parameterserver.
  * Intel(R) MPI-ondersteuning aan servicezijde.
  * Bugfix naar parametertuning voor gedistribueerde run fix tijdens validatie in BatchAI.
  * Contextmanager identificeert nu de primaire instantie.

+ **Azure-portal-ervaring**
  * log_table() en log_row() worden ondersteund in De details van Run.
  * Maak automatisch grafieken voor tabellen en rijen met 1, 2 of 3 numerieke kolommen en een optionele categorische kolom.

+ **Geautomatiseerde machine learning**
  * Verbeterde foutafhandeling en documentatie
  * Problemen met het ophalen van vaste uitvoeringen.
  * Probleem met blijven uitvoeren opgelost.
  * Fixed :::no-loc text="ensembling"::: iteration issues.
  * Fixed training hanging bug on MAC OS.
  * Downsampling macro gemiddelde PR / ROC curve in aangepaste validatie scenario.
  * Extra indexlogica verwijderd.
  * Verwijderd filter uit get_output API.

+ **Pijplijnen**
  * Added a method Pipeline.publish() to publish a pipeline directly, without requiring a execution run first.
  * Added a method PipelineRun.get_pipeline_runs() to fetch the pipeline runs that were generated from a published pipeline.

+ **Project Brainwave**
  * Bijgewerkte ondersteuning voor nieuwe AI-modellen die beschikbaar zijn op FPGA's.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Azure Machine Learning Data Prep SDK v0.2.0
[Versie 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) bevat de volgende functies en bugfixes:

+ **Nieuwe functies**
  * Ondersteuning voor one-hot codering
  * Ondersteuning voor kwanticeerbare transformatie

+ **Bug opgelost:**
  * Werkt met elke Tornado-versie, je hoeft je Tornado-versie niet te downgraden
  * Waarde telt voor alle waarden, niet alleen de top drie

## <a name="2018-09-public-preview-refresh"></a>2018-09 (Public preview vernieuwen)

Een nieuwe, vernieuwde versie van Azure Machine Learning: Lees meer over deze release:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Volgende stappen

Lees het overzicht voor [Azure Machine Learning](overview-what-is-azure-ml.md).
