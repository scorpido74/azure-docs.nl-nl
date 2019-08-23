---
title: Wat is er nieuw in de release?
titleSuffix: Azure Machine Learning service
description: Meer informatie over de nieuwste updates voor Azure Machine Learning-service en de machine learning-en gegevens voorbereiding python-Sdk's.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 1e35baf24b59e7864982d131f44f79458e0d9015
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971492"
---
# <a name="azure-machine-learning-service-release-notes"></a>Releaseopmerkingen Azure Machine Learning-service

In dit artikel meer informatie over de versies van de Azure Machine Learning-service.  Ga voor de volledige SDK-referentie-inhoud naar de hoofd pagina van de hand leiding van de Azure Machine Learning van de [**SDK voor python**](https://aka.ms/aml-sdk) . 

Zie [de lijst met bekende problemen](resource-known-issues.md) voor meer informatie over bekende problemen en oplossingen.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK voor python v-1.0.57
+ **Nieuwe functies**
  + Ingeschakeld `TabularDataset` om te worden gebruikt door AutomatedML. `TabularDataset` Ga https://aka.ms/azureml/howto/createdatasets voor meer informatie naar.
  
+ **Oplossingen en verbeteringen voor oplossingen**
  + **automl-client-core-nativeclient**
    + Er is een fout opgelost die optreedt wanneer training en/of validerings labels (y en y_valid) zijn opgenomen in de vorm van Pandas data frame, maar niet als numpy-matrix.
    + De interface voor het maken `RawDataContext` van een kan alleen worden gemaakt als `AutoMLBaseSettings` de gegevens en het object zijn vereist.
    +  Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen. -Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komen in de Trainingsset tijdens het voors pellen.
  + **azure-cli-ml**
    + U kunt nu het SSL-certificaat voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster bijwerken voor zowel het micro soft-certificaat dat is gegenereerd door een klant.
  + **azureml-automl-core**
    + Er is een probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet correct zijn verwijderd.
    + Verbeterde logboek registratie van fouten in AutoML; Er worden nu altijd volledige fout berichten naar het logboek bestand geschreven.
    + AutoML heeft het vastmaken van het pakket `azureml-defaults`zodanig `azureml-explain-model`bijgewerkt dat `azureml-dataprep`het include,, en. AutoML ontvangt geen waarschuwing meer over niet-overeenkomende pakket (behalve voor `azureml-train-automl` het pakket).
    + Er is een probleem opgelost in tijds Erie, waarbij de AVK gesplitst zijn van ongelijke omvang, waardoor de berekening van de opslag locatie mislukt.
    + Bij het uitvoeren van een ensemble-iteratie voor het multi validatie type training, als we de modellen die zijn getraind op de hele gegevensset niet kunnen downloaden, hebben we een inconsistentie aangetroffen tussen de model gewichten en de modellen die in de stemming werden ingevoerd dienen.
    + Er is een fout opgelost die optreedt wanneer training en/of validerings labels (y en y_valid) zijn opgenomen in de vorm van Pandas data frame, maar niet als numpy-matrix.
    + Het probleem is opgelost met de prognose taken wanneer er geen is aangetroffen in de Boole-kolommen van de invoer tabellen.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen. -Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komen in de Trainingsset tijdens het voors pellen.
  + **azureml-core**
    + Probleem opgelost met het rangschikken van de blob_cache_timeout-para meter.
    + De uitzonderings typen voor externe aanpassing en trans formatie zijn toegevoegd aan systeem fouten.
    + Er is ondersteuning toegevoegd voor Key Vault geheimen voor externe uitvoeringen. Voeg een azureml. core. class kluis. klasse-kluis toe om geheimen toe te voegen, op te halen en weer te geven van de sleutel kluis die is gekoppeld aan uw werk ruimte. Ondersteunde bewerkingen zijn:
      + azureml. core. workspace. workspace. Get _default_keyvault ()
      + azureml. core. de sleutel kluis. _secret (naam, waarde)
      + azureml. core. de sleutel kluis. _secrets (secrets_dict)
      + azureml. core. de sleutel kluis. de sleutel kluis. Get _secret (naam)
      + azureml. core. de sleutel kluis. de sleutel kluis. Get _secrets (secrets_list)
      + azureml. core. de sleutel kluis. de sleutel kluis. list_secrets ()
    + Aanvullende methoden voor het verkrijgen van de standaard-sleutel kluis en het ophalen van geheimen tijdens externe uitvoering:
      + azureml. core. workspace. workspace. Get _default_keyvault ()
      + azureml. core. run. run. Get _secret (naam)
      + azureml. core. run. run. Get _secrets (secrets_list)
    + Er zijn extra onderdrukkings parameters toegevoegd voor verzenden-Hyperdrive CLI-opdracht.
    + Verbeter de betrouw baarheid van API-aanroepen om nieuwe pogingen uit te breiden naar algemene aanvragen bibliotheek uitzonderingen.
    + Voeg ondersteuning toe voor het verzenden van uitvoeringen vanuit een ingediende uitvoering.
    + Er is een vast verlopen SAS-token probleem opgetreden in FileWatcher dat ertoe heeft geleid dat bestanden niet meer worden geüpload nadat het oorspronkelijke token is verlopen.
    + Ondersteunde HTTP CSV/TSV-bestanden importeren in dataset python SDK.
    + De methode Workspace. Setup () is afgeschaft. Er wordt een waarschuwings bericht weer gegeven dat gebruikers Create () of Get ()/from_config () gebruiken.
    + Er is een omgeving toegevoegd. Voeg _private_pip_wheel () toe, waarmee u persoonlijke aangepaste Python-pakketten (. WHL) kunt uploaden naar de werk ruimte en deze veilig met behulp van ze kunt gebruiken om de omgeving te bouwen/te realiseren.
    + U kunt nu het SSL-certificaat voor het Score-eind punt dat is geïmplementeerd op het AKS-cluster bijwerken voor zowel het micro soft-certificaat dat is gegenereerd door een klant.
  + **azureml-explain-model**
    + Para meter toegevoegd om een model-ID toe te voegen aan uitleg bij het uploaden.
    + Labels `is_raw` zijn toegevoegd aan uitleg in het geheugen en uploads.
    + Er zijn pytorch-ondersteuning en tests toegevoegd voor het pakket voor de azureml-uitleg-model.
  + **azureml-opendatasets**
    + Ondersteuning voor het detecteren en registreren van automatische test omgevingen.
    + U hebt klassen toegevoegd om Amerikaanse populatie per regio en post te krijgen.
  + **azureml-pipeline-core**
    + De eigenschap label is toegevoegd aan de definitie van de invoer-en uitvoer poort.
  + **azureml-telemetrie**
    + Er is een onjuiste telemetrie-configuratie hersteld.
  + **azureml-train-automl**
    + Er is een fout opgetreden bij het uitvoeren van een probleem waarbij tijdens de installatie fouten geen logboek wordt ontvangen van het veld ' Errors ' voor de installatie en daarom niet is opgeslagen in de bovenliggende run ' Errors '.
    + Er is een probleem opgelost in AutoML waarbij rijen met ontbrekende labels niet correct zijn verwijderd.
    + Sta AutoML-gebruikers toe om trainings reeksen te verwijderen die niet lang genoeg zijn bij het voors pellen.
    + Sta AutoML-gebruikers toe om korrels te verwijderen uit de testset die niet voor komen in de Trainingsset tijdens het voors pellen.
    + AutoMLStep gaat nu door naar de back-end van automl om te voor komen dat er problemen zijn met wijzigingen of toevoegingen van nieuwe configuratie parameters.
  + **azureml-train-core**
    + Torch 1,2-ondersteuning is toegevoegd in PyTorch Estimator.
  + **azureml-widgets**
    + Verbeterde matrix diagrammen voor Verwar ring voor classificatie trainingen.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data prep SDK v 1.1.12
+ **Nieuwe functies**
  + Een lijst met teken reeksen kan nu worden door gegeven als `read_*` invoer voor-methoden.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De prestaties van `read_parquet` zijn aanzienlijk verbeterd wanneer ze in Spark worden uitgevoerd.
  + Er is een probleem `column_type_builder` opgelost waarbij een fout is opgetreden in een enkele kolom met dubbel zinnige datum notaties.

### <a name="azure-portal"></a>Azure-portal
+ **Preview-functie**
  + Logboek-en uitvoer bestands streaming is nu beschikbaar voor pagina's met details van de uitvoering. De bestanden streamen updates in realtime wanneer de schakel optie voor preview is ingeschakeld.
  + De mogelijkheid om een quotum op een werkruimte niveau in te stellen, wordt vrijgegeven in de preview-versie. AmlCompute quota worden toegewezen op abonnements niveau, maar we bieden u de mogelijkheid om dat quotum tussen werk ruimten te verdelen en toe te wijzen voor eerlijk delen en governance. Klik op de Blade gebruiks namen en **quota's** in de linkernavigatiebalk van uw werk ruimte en selecteer het tabblad **quota's configureren** . Houd er rekening mee dat u een abonnements beheerder moet zijn om quota's op het niveau van de werk ruimte te kunnen instellen, omdat dit een bewerking in meerdere werk ruimten is.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK voor python v-1.0.55

+ **Nieuwe functies**
  + Verificatie op basis van tokens wordt nu ondersteund voor de aanroepen van het Score-eind punt dat is geïmplementeerd op AKS. De huidige op sleutels gebaseerde authenticatie blijven wel worden ondersteund en gebruikers kunnen een van deze verificatie mechanismen tegelijk gebruiken.
  + De mogelijkheid om een Blob-opslag te registreren die zich achter het virtuele netwerk (VNet) bevindt als een gegevens opslag.
  
+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-automl-core**
    + Hiermee wordt een bug opgelost waarbij de validatie grootte voor het verg Roten van de AVK klein is en resulteert in beschadigde, gedicteerde en ware grafieken voor regressie en prognose.
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Problemen met de tijds Erie die zijn opgelost als de preprocess-vlag is ingesteld op True.
    + Er zijn bepaalde fout berichten voor prognose gegevens validatie meer actie mogelijk.
    + Gereduceerd geheugen gebruik van AutoML wordt uitgevoerd door gegevens sets te verwijderen en/of langzaam te laden, met name tussen het proces
  + **azureml-contrib-uitleg-model**
    + Er is een model_task-vlag toegevoegd aan uitleg om toe te staan dat gebruikers standaard automatische interferentie logica voor model type overschrijven
    + Wijzigingen in de widget: Wordt automatisch geïnstalleerd met contrib, niet meer nbextension installeren/inschakelen-ondersteuning met alleen de prioriteit van globale functies (bijvoorbeeld Permutative)
    + Wijzigingen in het dash board:-box plots en viools worden weer gegeven naast beeswarm plot op overzichts pagina-veel sneller opnieuw samen stellen van beeswarme tekening op de schuif regelaar van de top-k-verschuiving-nuttig bericht waarin wordt uitgelegd hoe top-k is berekend: bruikbare, aanpas bare berichten in plaats van grafieken wanneer niet-beschik bare gegevens
  + **azureml-core**
    + De methode model. package () is toegevoegd om docker-installatie kopieën en Dockerfiles te maken waarmee modellen en hun afhankelijkheden worden ingekapseld.
    + Lokale webservices bijgewerkt om InferenceConfigs te accepteren die omgevings objecten bevatten.
    + Vast model. REGI ster () waarbij ongeldige modellen worden geproduceerd wanneer. (voor de huidige map) wordt door gegeven als de para meter model_path.
    + Run. submit_child toevoegen, de functionaliteit weerspiegelt experiment. Submit tijdens het opgeven van de run as the parent van de ingediende onderliggende run.
    + Ondersteuning voor configuratie opties van model. REGI ster in run. register_model.
    + De mogelijkheid om JAR-taken uit te voeren op een bestaand cluster.
    + De instance_pool_id-en cluster_log_dbfs_path-para meters worden nu ondersteund.
    + Er is ondersteuning toegevoegd voor het gebruik van een omgevings object bij het implementeren van een model naar een webservice. Het omgevings object kan nu worden opgenomen als onderdeel van het InferenceConfig-object.
    + Appinsifht toewijzing toevoegen voor nieuwe regio's-centraal-westus-northcentralus
    + Documentatie toegevoegd voor alle kenmerken in alle Data Store-klassen.
    + De blob_cache_timeout-para `Datastore.register_azure_blob_container`meter is toegevoegd aan.
    + Save_to_directory-en load_from_directory-methoden zijn toegevoegd aan azureml. core. Environment. Environment.
    + De opdrachten "AZ ml Environment down load" en "AZ ml Environment REGI ster" zijn toegevoegd aan de CLI.
    + De methode environment. add _private_pip_wheel is toegevoegd.
  + **azureml-explain-model**
    + Het bijhouden van gegevensset is toegevoegd aan uitleg met behulp van de DataSet-service (preview).
    + De standaard Batch grootte is verminderd bij het streamen van globale uitleg van 10k tot 100.
    + Er is een model_task-vlag toegevoegd aan uitleg om gebruikers toe te staan de standaard logica voor automatische interferentie voor het model type te negeren.
  + **azureml-mlflow**
    + Er is een fout opgelost in mlflow. azureml. build_image waarbij geneste mappen worden genegeerd.
  + **azureml-pipeline-steps**
    + De mogelijkheid om JAR-taken uit te voeren op bestaande Azure Databricks cluster is toegevoegd.
    + Ondersteuning voor instance_pool_id-en cluster_log_dbfs_path-para meters voor DatabricksStep-stap toegevoegd.
    + Er is ondersteuning toegevoegd voor pijplijn parameters in de stap DatabricksStep.
  + **azureml-train-automl**
    + Docstrings toegevoegd voor de aan ensemble gerelateerde bestanden.
    + Documenten bijgewerkt naar een meer geschikte taal `max_cores_per_iteration` voor en`max_concurrent_iterations`
    + De logboek registratie van taken op de externe uitvoering is verbeterd. de gebruiker wordt nu uitgebreid met het fout bericht weer gegeven als de uitvoering is mislukt.
    + Get_data verwijderd uit pijp lijn automlstep-notebook.
    + De ondersteuning van dataprep in automlstep is gestart.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data prep SDK v 1.1.10

+ **Nieuwe functies**
  + U kunt nu een aanvraag indienen om specifieke controles uit te voeren (bijvoorbeeld een histogram, een spreidings tekening enzovoort) op specifieke kolommen.
  + Het argument parallelliseren is toegevoegd `append_columns`aan. Indien waar, worden gegevens in het geheugen geladen, maar wordt de uitvoering parallel uitgevoerd. Als deze eigenschap onwaar is, wordt de uitvoering streaming, maar met één thread.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK voor python v-1.0.53

+ **Nieuwe functies**
  + Automatische Machine Learning ondersteunt nu ONNX modellen voor training op het externe Compute-doel
  + Azure Machine Learning biedt nu de mogelijkheid om de training te hervatten vanuit een eerder uitgevoerde, controle punt-of model bestand.
    + Meer informatie over het [gebruik van schattingen om de training van een vorige uitvoering te hervatten](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Oplossingen en verbeteringen voor oplossingen**
  + **automl-client-core-nativeclient**
    + De bug over verbroken-kolom typen na de trans formatie oplossen (bug gekoppeld); 
    + Toestaan dat y_query een object type bevat aan het begin (#459519).
  + **azure-cli-ml**
    + CLI-opdrachten ' model implementeren ' en ' service-update ' accepteren nu para meters, configuratie bestanden of een combi natie van beide. Para meters hebben voor rang op kenmerken in bestanden.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
  + **azureml-automl-core**
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + Het toevoegen van ondersteuning voor Nimbus ML-schattingen & pijp lijnen die moeten worden gebruikt binnen AutoML-schattingen.
    + Het oplossen van een bug in de selectie procedure van de ensemble die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores constant bleven.
    + Schakel het hergebruik van een aantal featurizations in voor de voor spellingen van taken. Dit versnelt het uitvoeren van de uitvoering van het installatie programma door een factor n_cross_validations te gebruiken voor dure featurizations zoals lags en Rolling Windows.
    + Er wordt een probleem opgelost als de tijd buiten de Panda-ondersteunde tijds bereik wordt ondersteund. We veroorzaken nu een DataException als de tijd minder is dan pd. Time Stamp. min of groter dan pd. Time Stamp. Max
    + Door prognoses te maken, kunnen verschillende frequenties worden ingesteld in de trein-en test sets als deze kunnen worden uitgelijnd. Zo kan ' kwar taal vanaf januari ' en op ' kwar taal vanaf oktober ' worden uitgelijnd.
    + De eigenschap para meters is toegevoegd aan de TimeSeriesTransformer.
    + Oude uitzonderings klassen verwijderen.
    + In prognose taken accepteert de `target_lags` para meter nu één geheel getal of een lijst met gehele getallen. Als het gehele getal is gegeven, wordt er slechts één vertraging gemaakt. Als er een lijst wordt weer gegeven, worden de unieke waarden van lags genomen. target_lags = [1, 2, 2, 4] maakt lags van één, 2 en 4 Peri Oden.
    + Los de fout op over het verliezen van kolom typen na de trans formatie (bug gekoppeld);
    + In `model.forecast(X, y_query)`kan y_query een object type bevatten dat geen (en) bevat aan het begin (#459519).
    + Verwachte waarden toevoegen aan automl-uitvoer
  + **azureml-contrib-datadrift**
    +  Verbeteringen in het voor beeld van een notebook, inclusief de overstap naar azureml-open datasets in plaats van contrib-open datasets en prestatie verbeteringen bij het verrijken van gegevens
  + **azureml-contrib-uitleg-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + Er zijn segmenten toegevoegd aan de afbeeldings uitleg in de afbeeldings uitleg voor het pakket voor AzureML-contrib-uitleg-model
    + scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + batch_size toevoegen om uitleg te geven wanneer include_local = False voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
  + **azureml-contrib-featureengineering**
    + Oplossing voor het aanroepen van set_featurizer_timeseries_params (): wijziging van het type dict-waarde en null-controle-toevoegen van een notitie blok voor tijds Erie featurizer
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd 
    + De fout opgelost met de Data Store-upload waar een lege map `target_path` wordt gemaakt als wordt gestart met`/`
    + Probleem met deepcopy opgelost in ServicePrincipalAuthentication.
    + De opdrachten "AZ ml Environment show" en "AZ ml Environment List" zijn toegevoegd aan de CLI.
    + Omgevingen bieden nu ondersteuning voor het opgeven van een base_dockerfile als een alternatief voor een al gebouwde base_image.
    + De niet-gebruikte RunConfiguration-instelling auto_prepare_environment is gemarkeerd als afgeschaft.
    + De model beschrijving kan nu worden bijgewerkt na de registratie
    + Bugfix: Voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van de methode voor het verwijderen als gevolg van een upstream-afhankelijkheid.
    + Er is een probleem opgelost waarbij een lege duur is afgedrukt voor implementaties die optreden bij het maken van een werk ruimte voor sommige omgevingen.
    + Verbeterde werk ruimte fout uitzonderingen maken. Zo kunnen gebruikers niet zien dat de werk ruimte niet kan worden gemaakt. Kan... niet vinden Als het bericht en wordt in plaats daarvan de daad werkelijke aanmaak fout weer gegeven.
    + Voeg ondersteuning toe voor token verificatie in AKS-webservices. 
    + Methode `get_token()` toevoegen aan `Webservice` objecten.
    + CLI-ondersteuning is toegevoegd om machine learning gegevens sets te beheren.
    + `Datastore.register_azure_blob_container`u kunt nu optioneel een `blob_cache_timeout` waarde (in seconden) opgeven waarmee de koppel parameters van de blobfuse worden geconfigureerd om de verval datum van de cache voor dit gegevens archief in te scha kelen. De standaard waarde is geen time-out, d.w.z. Wanneer een BLOB wordt gelezen, blijft deze in de lokale cache totdat de taak is voltooid. De meeste taken geven de voor keur aan deze instelling, maar sommige taken moeten meer gegevens lezen uit een grote gegevensset dan op hun knoop punten passen. Voor deze taken helpt deze bij het afstemmen van deze para meter. Wees voorzichtig bij het afstemmen van deze para meter: als u de waarde te laag instelt, kan dit leiden tot slechte prestaties, omdat de gegevens die in een epoche worden gebruikt, mogelijk verlopen voordat ze opnieuw worden gebruikt. Dit betekent dat alle Lees bewerkingen worden uitgevoerd vanuit de Blob-opslag (dat wil zeggen het netwerk) in plaats van de lokale cache, die een negatieve invloed heeft op de opleidings tijden.
    + De model beschrijving kan nu na de registratie op de juiste wijze worden bijgewerkt
    + Het model en de installatie kopie bieden nu meer informatie over de upstream-objecten die afhankelijk zijn van de items die ervoor zorgen dat het verwijderen mislukt
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
  + **azureml-explain-model**
    + Argument voor vaste trans formaties voor de licht gewicht uitleg van de onbewerkte functie in het contrib-uitleg-model pakket
    + scipy sparse-ondersteuning voor LimeExplainer toevoegen
    + Er is een Shap-lineaire uitleg weergave toegevoegd, evenals een ander niveau voor de uitleg van lineaire modellen in tabel vorm
    + voor een gesimuleerde uitleg van de uitleg model bibliotheek is een vaste fout opgetreden wanneer include_local = False voor de sparse gegevens invoer
    + verwachte waarden toevoegen aan automl-uitvoer
    + het is belang rijk dat het probleem is opgelost door het argument van de transformaties van de functie RAW te verkrijgen
    + batch_size toevoegen om uitleg te geven wanneer include_local = False voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren
    + voor de model verklarende bibliotheek is een vast blackbox-uitleg waarbij Pandas data frame invoer vereist is voor de voor spelling
    + Er is een fout `explanation.expected_values` opgelost waarbij soms een zwevende waarde wordt geretourneerd in plaats van een lijst met een zwevende functie.
  + **azureml-mlflow**
    + Verbeter de prestaties van mlflow. set _experiment (experiment_name)
    + Fout bij het gebruik van InteractiveLoginAuthentication voor mlflow-tracking_uri oplossen
    + Het resource gebruik van externe uitvoeringen verbeteren met behulp van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow verbeteren
    + Patch bug waarbij mlflow. log _artifacts ("my_dir") artefacten opslaat onder "my_dir/< artefact-paden >" in plaats van < artefact-paden > "
  + **azureml-opendatasets**
    + Pyarrow van opengegevenssets aan oude versies (< 0.14.0) vastmaken vanwege geheugen problemen die hier zijn geïntroduceerd.
    +  Verplaats azureml-contrib-opendatasets naar azureml-opendatasets. -Open dataset-klassen mogen worden geregistreerd voor de AML-werk ruimte en maakt naadloos gebruik van de functies van AML-gegevensset. -Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-pipeline-steps**
    + DBFS Data Store wordt nu ondersteund voor invoer en uitvoer in DatabricksStep.
    + Bijgewerkte documentatie voor Azure Batch stap met betrekking tot invoer/uitvoer.
    + In AzureBatchStep is de standaard waarde *delete_batch_job_after_finish* gewijzigd in *True*.
  + **azureml-telemetrie**
    +  Verplaats azureml-contrib-opendatasets naar azureml-opendatasets. -Open dataset-klassen mogen worden geregistreerd voor de AML-werk ruimte en maakt naadloos gebruik van de functies van AML-gegevensset. -Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-train-automl**
    + Bijgewerkte documentatie op get_output om het werkelijke retour type weer te geven en aanvullende notities te geven over het ophalen van de sleutel eigenschappen.
    + NimbusML-afhankelijkheid bijwerken naar 1.2.0-versie (meest recent).
    + verwachte waarden toevoegen aan automl-uitvoer
  + **azureml-train-core**
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
  + **azureml-opendatasets**
    + **azureml-contrib-** opengegevenssets is nu beschikbaar als **azureml-** opendatasets. Het oude pakket kan nog steeds werken, maar we raden u aan om gebruik te maken van **azureml-** opengegevenssets voor geavanceerde mogelijkheden en verbeteringen.
    + Met dit nieuwe pakket kunt u open gegevens sets als gegevensset registreren in de AML-werk ruimte en gebruikmaken van de functies die door gegevensset worden geboden.
    + Het omvat ook bestaande mogelijkheden, zoals het gebruik van open gegevens sets als Panda-en SPARK-dataframes en de locatie koppeling voor een bepaalde gegevensset, zoals weer.

+ **Preview-functies**
    + HyperDriveConfig kan pijplijn object nu accepteren als een para meter ter ondersteuning van het afstemmen van afstemming met behulp van een pijp lijn.

+ **Oplossingen en verbeteringen voor oplossingen**
  + **azureml-train-automl**
    + De fout voor het verliezen van kolom typen na de trans formatie is opgelost.
    + De bug is opgelost om y_query toe te staan een object type te maken dat geen (e) (en) bevat. 
    + Het probleem is opgelost in de procedure voor het selecteren van ensembles die de resulterende ensemble niet noodzakelijkerwijs groeit, zelfs als de scores constant bleven.
    + Het probleem met de instellingen whitelist_models en blacklist_models in AutoMLStep is opgelost.
    + Het probleem is opgelost waardoor het gebruik van voor verwerking niet mogelijk is wanneer AutoML werd gebruikt in de context van Azure ML-pijp lijnen.
  + **azureml-opendatasets**
    + Azureml-contrib-opendatasets is verplaatst naar azureml-opendatasets.
    + Toegestane open gegevensset klassen worden geregistreerd voor de AML-werk ruimte en maken gebruik van de functies van AML-gegevensset naadloos.
    + Verbeterde NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.
  + **azureml-explain-model**
    + Bijgewerkte online documentatie voor Objects voor interpretaties.
    + Er is batch_size toegevoegd om de uitleger te simuleren wanneer include_local = False voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel te verbeteren.
    + Het probleem opgelost waarbij `explanation.expected_values` soms een float retourneert in plaats van een lijst met een zwevend item.
    + De verwachte waarden zijn toegevoegd aan automl-uitvoer voor nabooters in de uitleg model-bibliotheek.
    + Het kenmerk van de vaste permutatie functie wanneer trans formaties argument worden geleverd om het belang van de onbewerkte functie te verkrijgen.
    + Batch_size is toegevoegd aan de uitleg bij include_local = False voor het streamen van globale toelichtingen in batches om de uitvoerings tijd van DecisionTreeExplainableModel voor de bibliotheek van de model verklaring te verbeteren.
  + **azureml-core**
    + De mogelijkheid om DBFS-gegevens opslag in de AzureML-CLI te koppelen, is toegevoegd.
    + Het probleem met de upload Data Store is opgelost, waarbij een lege `target_path` map wordt `/`gemaakt als u begint met.
    + Vergelijking van twee gegevens sets is ingeschakeld.
    + Voor het verwijderen van modellen en installatie kopieën wordt nu meer informatie geboden over het ophalen van upstream-objecten die afhankelijk zijn van de methode voor het verwijderen als gevolg van een upstream-afhankelijkheid.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
  + **azureml-mlflow**
    + Verbeterd resource gebruik van externe uitvoeringen die gebruikmaken van azureml. mlflow.
    + De documentatie van het pakket voor de azureml-mlflow is verbeterd.
    + Het probleem is opgelost waarbij mlflow. log _artifacts ("my_dir") artefacten opslaat onder "my_dir/artefact-paths" in plaats van "artefact paden".
  + **azureml-pipeline-core**
    + De para meter hash_paths voor alle pijplijn stappen is afgeschaft en wordt in de toekomst verwijderd. Standaard wordt de inhoud van de bronmap gehasht (met uitzonde ring van bestanden die worden vermeld in. amlignore of. gitignore)
    + Door gaan met het verbeteren van module en ModuleStep ter ondersteuning van reken type-specifieke modules, in voor bereiding op RunConfiguration-integratie en verdere wijzigingen om het gebruik ervan in pijp lijnen te ontgrendelen.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Verbeterde documentatie met betrekking tot invoer/uitvoer.
    + AzureBatchStep: De delete_batch_job_after_finish-standaard waarde is gewijzigd in True.
  + **azureml-train-core**
    + Teken reeksen worden nu geaccepteerd als Compute target voor automatische afstemming-afstemming.
    + De niet-gebruikte RunConfiguration-instelling is afgeschaft in auto_prepare_environment.
    + Afgeschafte `pip_requirements_file_path` para meters `conda_dependencies_file` `conda_dependencies_file_path` en `pip_requirements_file` ter voor keur van en respectievelijk.
  + **azureml-opendatasets**
    + Verbeter de NoaaIsdWeather-verrijkings prestaties in een niet-SPARK-versie aanzienlijk.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning data prep SDK v 1.1.8

+ **Nieuwe functies**
 + Gegevensstroom objecten kunnen nu worden herhaald, waardoor er een reeks records wordt geproduceerd. Zie de documentatie `Dataflow.to_record_iterator`voor.

+ **Oplossingen en verbeteringen voor oplossingen**
 + Verbeterde DataPrep-SDK.
 + Verbeterde verwerking van Panda-DataFrames met niet-teken reeks kolom indexen.
 + Verbeterde prestaties van `to_pandas_dataframe` in gegevens sets.
 + Er is een fout opgelost waarbij Spark-uitvoering van gegevens sets niet kan worden uitgevoerd in een omgeving met meerdere knoop punten.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning data prep SDK v 1.1.7

Er is een wijziging doorgegaan die de prestaties verbeterd, omdat deze problemen voor sommige klanten met Azure Databricks veroorzaken. Als er een probleem is opgetreden op Azure Databricks, kunt u een upgrade uitvoeren naar versie 1.1.7 met behulp van een van de volgende methoden:
1. Voer dit script uit om een upgrade uit te voeren:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
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
  + Model. registreren ondersteunt nu het registreren van meerdere afzonderlijke bestanden als één model met behulp `child_paths` van de para meter.
  
+ **Preview-functies**
    + Score uitlegers kunnen nu optioneel Conda-en PIP-gegevens opslaan voor betrouwbaardere serialisatie en deserialisatie.
    + Fout oplossing voor automatische functie selectie.
    + De mlflow. azureml. build_image is bijgewerkt naar de nieuwe API, patched bugs die worden weer gegeven door de nieuwe implementatie.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De paramiko-afhankelijkheid is verwijderd uit de azureml-kern. Waarschuwingen voor afschaffing zijn toegevoegd voor verouderde methoden voor het koppelen van het doel.
  + Verbeter de prestaties van run. create_children
  + In de gesimuleerde uitleg met een binaire classificatie kunt u de volg orde van kansen vaststellen wanneer de kans op docenten wordt gebruikt voor het schalen van Shap-waarden
  + Verbeterde fout afhandeling en-berichten voor automatische machine learning. 
  + Het probleem met de herhalings fout is opgelost voor automatische machine learning.
  + De trans formatie prestaties van de tijd reeks zijn verbeterd voor automatische machine learning.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning data prep SDK v 1.1.6

+ **Nieuwe functies**
  + Samenvattings functies voor top values`SummaryFunction.TOPVALUES`() en bottom Values (`SummaryFunction.BOTTOMVALUES`) zijn toegevoegd.

+ **Oplossingen en verbeteringen voor oplossingen**
  + De prestaties van `read_pandas_dataframe`worden aanzienlijk verbeterd.
  + Er is een fout opgelost waardoor `get_profile()` een gegevensstroom die naar binaire bestanden verwijst, niet kan worden uitgevoerd.
  + Mogelijk `set_diagnostics_collection()` gemaakt om de telemetrie-verzameling in-of uit te scha kelen.
  + Het gedrag van `get_profile()`is gewijzigd. De NaN-waarden worden nu genegeerd voor min, gemiddelde, STDEV en Sum, die worden uitgelijnd met het gedrag van Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK voor python v-1.0.43

+ **Nieuwe functies**
  + Azure Machine Learning biedt nu eersteklas ondersteuning voor populaire machine learning en gegevens analyse Framework Scikit-meer informatie. Met Estimator kunnen gebruikers eenvoudig Scikit-leer modellen trainen en implementeren. [ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)
    + Meer informatie over het [uitvoeren van afstemming-afstemming met Scikit-informatie met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Er is ondersteuning toegevoegd voor het maken van ModuleStep in pijp lijnen, samen met module-en ModuleVersion-klassen voor het beheren van herbruikbare reken eenheden.
  + ACI webservices bieden nu ondersteuning voor permanente scoring_uri via updates. De scoring_uri wordt gewijzigd van IP in FQDN. Het DNS-naam label voor FQDN kan worden geconfigureerd door de dns_name_label in deploy_configuration in te stellen. 
  + Automatische machine learning nieuwe functies:
    + STL featurizer voor prognose
    + KMeans-Clustering is ingeschakeld voor het opruimen van functies
  + AmlCompute quota goedkeuringen zijn nog sneller geworden. We hebben nu het proces voor het goed keuren van uw quotum aanvragen binnen een drempel waarde geautomatiseerd. Meer informatie over de werking van quota's vindt [u in het beheren van quota's](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

+ **Preview-functies**
    + Integratie met [MLflow](https://mlflow.org) 1.0.0 tracking via het MLflow-pakket ([voor beeld](https://aka.ms/azureml-mlflow-examples)-notebooks).
    + Verzend Jupyter notebook als een run. [API-referentie documentatie](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Open bare preview-versie van de [gegevens drift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift?view=azure-ml-py) via het contrib-datadrift-pakket ([voorbeeld notitieblokken](https://aka.ms/azureml-datadrift-example)). Data gradatie is een van de belangrijkste redenen waarbij de nauw keurigheid van het model in de loop van de tijd verloopt. Het gebeurt wanneer gegevens die worden geleverd aan model in productie verschillen van de gegevens waarop het model is getraind. AML data drift detector helpt klanten bij het bewaken van gegevens drift en bij het verzenden van een waarschuwing wanneer er een drift wordt gedetecteerd. 

+ **Belangrijke wijzigingen**

+ **Oplossingen en verbeteringen voor oplossingen**
  + RunConfiguration load en Save ondersteunt het opgeven van een volledig bestandspad met volledige back-compat voor vorig gedrag.
  + De cache is toegevoegd in ServicePrincipalAuthentication, is standaard uitgeschakeld.
  + Logboek registratie inschakelen voor meerdere grafieken onder dezelfde metrische naam.
  + Model klasse kan nu goed worden overdraag bare van azureml.`from azureml.core import Model`core ().
  + In pijplijn stappen `hash_path` is de para meter nu afgeschaft. Het nieuwe gedrag is om een hash te volt ooien, met uitzonde ring van bestanden die worden vermeld in. amlignore of. gitignore.
  + In pijplijn pakketten zijn `get_all` verschillende en `get_all_*` methoden vervangen `list` door respectievelijk en `list_*`.
  + azureml. core. Get _run vereist niet langer dat klassen worden geïmporteerd voordat het oorspronkelijke run-type wordt geretourneerd.
  + Er is een probleem opgelost waarbij een aantal aanroepen naar de update voor de WebService geen update heeft geactiveerd.
  + Een score-time-out op AKS webservices moet tussen 5ms en 300000ms zijn. Het Maxi maal toegestane aantal scoring_timeout_ms voor Score aanvragen is van 1 min naar 5 minuten.
  + LocalWebservice-objecten hebben `scoring_uri` nu `swagger_uri` en eigenschappen.
  + Tijdens het maken van de uitvoer van de map en uitvoer van de Directory wordt het gebruikers proces niet meer verzonden. De SDK voor de uitvoerings geschiedenis is ingeschakeld om in elk gebruikers proces te worden uitgevoerd. Hiermee worden enkele synchronisatie problemen opgelost die zijn ondervonden door gedistribueerde trainings uitvoeringen.
  + De naam van het azureml-logboek dat is geschreven van de naam van het gebruikers proces bevat nu de naam van het proces (alleen voor gedistribueerde trainingen) en de PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning data prep SDK v 1.1.5

+ **Oplossingen en verbeteringen voor oplossingen**
  + Voor geïnterpreteerde datetime-waarden met een jaar notatie van twee cijfers is het bereik van geldige jaren bijgewerkt zodat dit overeenkomt met Windows-versies. Het bereik is gewijzigd van 1930-2029 naar 1950-2049.
  + Wanneer u een bestand en instelling `handleQuotedLineBreaks=True`leest, `\r` wordt dit beschouwd als een nieuwe regel.
  + Er is een probleem opgelost `read_pandas_dataframe` dat in sommige gevallen een fout heeft veroorzaakt.
  + Verbeterde prestaties van `get_profile`.
  + Verbeterde fout berichten.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning data prep SDK v 1.1.4

+ **Nieuwe functies**
  + U kunt nu de volgende expressie taal functies gebruiken om datetime-waarden te extra heren en parseren in nieuwe kolommen.
    + `RegEx.extract_record()`haalt datetime-elementen op in een nieuwe kolom.
    + `create_datetime()`maakt datetime-objecten van afzonderlijke datetime-elementen.
  + Wanneer u `get_profile()`aanroept, kunt u nu zien dat quantile-kolommen worden aangeduid als (EST.) om duidelijk aan te geven dat de waarden benaderingen zijn.
  + U kunt nu * * globbing gebruiken bij het lezen van Azure Blob Storage.
    + zoals.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Oplossingen voor problemen**
  + Er is een fout met betrekking tot het lezen van een Parquet-bestand van een externe bron (Azure-Blob) opgelost.

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK voor python v-1.0.39
+ **Gewijzigde**
  + De auto_prepare_environment-optie voor het uitvoeren van de configuratie wordt afgeschaft, waarbij automatisch voorbereiden de standaard instelling wordt.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning data prep SDK v 1.1.3

+ **Nieuwe functies**
  + Er is ondersteuning toegevoegd om te lezen uit een PostgresSQL-data base, door het aanroepen van read_postgresql of het gebruik van een gegevens opslag.
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

### <a name="azure-portal"></a>Azure Portal

In Azure Portal kunt u nu het volgende doen:
+ Automatische ML experimenten maken en uitvoeren 
+ Maak een notebook-VM voor het uitproberen van voor beelden van Jupyter-notebooks of uw eigen virtuele machine.
+ De sectie gloed nieuwe ontwerpen (preview) in de werk ruimte van de Machine Learning-service, waaronder geautomatiseerde Machine Learning, visuele interface en gehoste laptop-Vm's
    + Automatisch een model maken met geautomatiseerde machine learning 
    + Een visuele interface voor slepen en neerzetten gebruiken om experimenten uit te voeren
    + Maak een notebook-VM om gegevens te verkennen, modellen te maken en services te implementeren.
+ Live diagram en metrische gegevens bijwerken in rapporten uitvoeren en detail pagina's uitvoeren
+ De bestands viewer voor logboeken, uitvoer en moment opnamen is bijgewerkt op de pagina's met details van de uitvoering.
+ Nieuwe en verbeterde ervaring bij het maken van rapporten op het tabblad experimenten. 
+ De mogelijkheid om het bestand config. json te downloaden van de pagina overzicht van de werk ruimte van de Azure Machine Learning-service is toegevoegd.
+ Ondersteuning voor het maken van Machine Learning Services-werk ruimte vanuit Azure Databricks werk ruimte 

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
+ [U kunt snel een vooraf geconfigureerde notebook-VM](tutorial-1st-experiment-sdk-setup.md) maken met de nieuwste versie van Azure machine learning SDK en gerelateerde pakketten.
+ De toegang wordt beveiligd via bewezen technologieën, zoals HTTPS, Azure Active Directory verificatie en autorisatie.
+ Betrouw bare Cloud opslag van notitie blokken en code in uw Azure Machine Learning-werkruimte Blob Storage-account. U kunt de VM van uw notebook veilig verwijderen zonder dat uw werk verloren gaat.
+ Vooraf geïnstalleerde voorbeeld notitieblokken om te verkennen en experimenteren met Azure Machine Learning service-functies.
+ Volledige aanpassings mogelijkheden van virtuele Azure-machines, elk VM-type, alle pakketten, alle Stuur Programma's. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK voor python v 1.0.33 uitgebracht.

+ Azure ML Modellen met hardwareversnelling op [fpga's](concept-accelerate-with-fpgas.md) is algemeen beschikbaar.
  + U kunt nu [het pakket voor azureml-accel.-modellen gebruiken voor het](how-to-deploy-fpga-web-service.md) volgende:
    + Train de gewichten van een ondersteund diepe Neural-netwerk (ResNet 50, ResNet 152, DenseNet-121, VGG-16 en SSD-VGG)
    + Overboeking Learning gebruiken met de ondersteunde DNN
    + Het model registreren bij Modelbeheer-service en het model container plaatsen
    + Het model implementeren in een Azure-VM met een FPGA in een Azure Kubernetes service-cluster (AKS)
  + De container implementeren op een [Azure data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server-apparaat
  + Uw gegevens beoordelen met het gRPC-eind punt met dit voor [beeld](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

+ Het opruimen van functies om het dynamisch toevoegen van featurizers voor prestatie optimalisatie mogelijk te maken. Nieuwe featurizers: werk insluitingen, gewicht van bewijs materiaal, doel codering, tekst doel codering, cluster afstand
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

+ **Introduced InferenceConfig & Model.deploy()**<br/> Model implementatie biedt nu ondersteuning voor het opgeven van een bronmap met een invoer script, hetzelfde als een RunConfig.  Daarnaast is model implementatie vereenvoudigd tot één opdracht.

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

Opmerking: De python-SDK voor gegevens voorbereiding wordt `numpy` niet `pandas` meer geïnstalleerd en verpakt. Zie [bijgewerkte installatie-instructies](https://aka.ms/aml-data-prep-installation).

+ **Nieuwe functies**
  + U kunt nu de draai transformatie gebruiken.
    + Instructies: [Notitie blok voor draai diagram](https://aka.ms/aml-data-prep-pivot-nb)
  + U kunt nu reguliere expressies in systeem eigen functies gebruiken.
    + Voorbeelden:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + U kunt nu en `to_upper`   `to_lower` functionsgebruikenin de expressie taal.
  + U kunt nu het aantal unieke waarden van elke kolom in een gegevens profiel zien.
  + Voor een aantal veelgebruikte Reader-stappen kunt u nu het `infer_column_types` argument door geven. Als deze is ingesteld op `True`, wordt door het voorbereiden van gegevens geprobeerd kolom typen te detecteren en automatisch te converteren.
    + `inference_arguments`is nu afgeschaft.
  + U kunt nu bellen `Dataflow.shape`.

+ **Oplossingen en verbeteringen voor oplossingen**
  + `keep_columns` accepteert nu een extra optioneel argument `validate_column_exists`, waarmee wordt gecontroleerd of het resultaat `keep_columns` van een kolom bevat.
  + Alle stappen van de lezer (die uit een bestand worden gelezen) accepteren nu een `verify_exists`extra optioneel argument.
  + Verbeterde prestaties van lezen van Panda data frame en het ophalen van gegevens profielen.
  + Er is een fout opgelost waarbij het segmenteren van één stap van een gegevens stroom is mislukt met een enkele index.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + U kunt nu een bestaand script opnieuw verzenden dat wordt uitgevoerd op een bestaand extern Compute-Cluster. 
  + U kunt nu een gepubliceerde pijp lijn uitvoeren met nieuwe para meters op het tabblad pijp lijnen. 
  + Details van de uitvoering bieden nu ondersteuning voor een nieuwe viewer voor momentopname bestanden. U kunt een moment opname van de Directory weer geven wanneer u een specifieke uitvoering hebt ingediend. U kunt ook het notitie blok downloaden dat is verzonden om de uitvoering te starten.
  + U kunt de bovenliggende uitvoeringen nu annuleren van de Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK voor python v-1.0.23

+ **Nieuwe functies**
  + De SDK van Azure Machine Learning ondersteunt nu python 3,7.
  + Azure Machine Learning DNN-schattingen bieden nu ingebouwde ondersteuning voor meerdere versies. Zo `TensorFlow` `framework_version` accepteert Estimator nu een para meter en kunnen gebruikers versie ' 1,10 ' of ' 1,12 ' opgeven.   Voor een lijst met de versies die worden ondersteund door uw huidige SDK- `get_supported_versions()` versie, roept u de gewenste Framework-klasse `TensorFlow.get_supported_versions()`op (bijvoorbeeld).
  Zie de [DNN Estimator-documentatie](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py)voor een lijst met de versies die worden ondersteund door de nieuwste SDK-versie.

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning data prep SDK v 1.1.1

+ **Nieuwe functies**
  + U kunt meerdere Data Store-DataPath/DataReference-bronnen lezen met behulp van read_ * trans formats.
  + U kunt de volgende bewerkingen uitvoeren op kolommen om een nieuwe kolom te maken: divisie, vloer, modulo, macht, lengte.
  + Data prep maakt nu deel uit van de Azure ML Diagnostics Suite en registreert standaard diagnostische gegevens.
    + Als u deze optie wilt uitschakelen, stelt u deze omgevings variabele in op True: DISABLE_DPREP_LOGGER

+ **Oplossingen en verbeteringen voor oplossingen**
  + Verbeterde code documentatie voor veelgebruikte klassen en functies.
  + Er is een fout opgelost in auto_read_file die geen Excel-bestanden kan lezen.
  + De optie is toegevoegd om de map te overschrijven in read_pandas_dataframe.
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

+ **Belangrijke wijzigingen**
  + Het concept van het gegevens prep-pakket is afgeschaft en wordt niet meer ondersteund. In plaats van meerdere gegevens stromen in één pakket persistent te maken, kunt u gegevens stromen afzonderlijk blijven gebruiken.
    + Instructies: [Gegevens stromen-notebook openen en opslaan](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nieuwe functies**
  + Gegevens voorbereiding kan nu kolommen herkennen die overeenkomen met een bepaald semantisch type en die dienovereenkomstig worden gesplitst. De STypes die momenteel wordt ondersteund zijn onder andere: e-mail adres, geografische coördinaten (breedte & lengte graad), IPv4-en IPv6-adressen, telefoon nummer van de VS en post code.
    + Instructies: [Semantische typen notebook](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Data prep ondersteunt nu de volgende bewerkingen om een resulterende kolom te genereren op basis van twee numerieke kolommen: aftrekken, vermenigvuldigen, delen en modulo.
  + U kunt een `verify_has_data()` gegevensstroom oproep aanroepen om te controleren of de gegevensstroom records zou opleveren als ze worden uitgevoerd.

+ **Oplossingen en verbeteringen voor oplossingen**
  + U kunt nu het aantal te gebruiken bakken opgeven in een histogram voor numerieke kolom profielen.
  + Voor `read_pandas_dataframe` de trans formatie moet de data frame nu een teken reeks-of byte kolom namen hebben.
  + Er is een fout opgelost `fill_nulls` in de trans formatie, waarbij de waarden niet correct zijn ingevuld als de kolom ontbreekt.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK voor python v-1.0.18

 + **Gewijzigde**
   + Het azureml-tensorboard-pakket vervangt azureml-contrib-tensorboard.
   + Met deze release kunt u een gebruikers account instellen op uw beheerde Compute-Cluster (amlcompute), terwijl u het maakt. U kunt dit doen door deze eigenschappen door te geven in de inrichtings configuratie. Meer informatie kunt u vinden in de [SDK-referentie documentatie](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

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

  + Azure Machine Learning biedt nu ondersteuning voor de eerste klasse voor populaire DNN Framework-ketens. Met [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) behulp van klasse-gebruikers kunnen ketting modellen eenvoudig worden getraind en geïmplementeerd.
    + Meer informatie over het [uitvoeren van gedistribueerde training met ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Meer informatie over het [uitvoeren van afstemming tuning with Chainer met behulp van HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Met de functie voor het toevoegen van Azure Machine Learning pijp lijnen wordt een pijplijn uitvoering geactiveerd op basis van wijzigingen in het gegevens archief. Het [notitie blok](https://aka.ms/pl-schedule) voor de pijp lijn planning is bijgewerkt om deze functie te presen teren.

+ **Oplossingen en verbeteringen voor oplossingen**
  + Er is ondersteuning toegevoegd Azure Machine Learning pijp lijnen voor het instellen van de eigenschap source_directory_data_store op een gewenste gegevens opslag (zoals een Blob-opslag) op [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) die worden geleverd aan de [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). In de volgende stappen wordt gebruikgemaakt van Azure File Store als back-upgegevens opslag, wat kan leiden tot beperking van problemen wanneer een groot aantal stappen gelijktijdig wordt uitgevoerd.

### <a name="azure-portal"></a>Azure Portal

+ **Nieuwe functies**
  + Nieuwe functie voor slepen en neerzetten van tabel editor voor rapporten. Gebruikers kunnen een kolom van het ene naar het tabel gebied slepen waarin een voor beeld van de tabel wordt weer gegeven. De kolommen kunnen opnieuw worden gerangschikt.
  + Viewer voor nieuwe logboek bestanden
  + Koppelingen naar experiment-uitvoeringen, compute, modellen, afbeeldingen en implementaties op het tabblad activiteiten

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning data prep SDK v 1.0.15

+ **Nieuwe functies**
  + Data prep ondersteunt nu het schrijven van bestands stromen vanuit een gegevensstroom. Biedt ook de mogelijkheid om de naam van de bestands stroom te bewerken om nieuwe bestands namen te maken.
    + Instructies: [Werken met notitie blokken voor bestands stromen](https://aka.ms/aml-data-prep-file-stream-nb)

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
  + Afgeschaft `PublishedPipeline.get_published_pipeline` ten gunste van `PublishedPipeline.get`.
  + Afgeschaft `Schedule.get_schedule` ten gunste van `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Azure Machine Learning data prep SDK v 1.0.12

+ **Nieuwe functies**
  + Data prep ondersteunt nu het lezen van een Azure-SQL database met behulp van gegevens opslag.
 
+ **Gewijzigde**
  + Verbeterde geheugen prestaties van bepaalde bewerkingen op grote gegevens.
  + `read_pandas_dataframe()`u moet `temp_folder` nu opgeven.
  + De `name` eigenschap op `ColumnProfile` is afgeschaft: gebruik `column_name` in plaats daarvan.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning SDK voor python v-1.0.10

+ **Wijzigingen**: 
  + Azure ML SDK heeft geen Azure-cli-pakketten meer als afhankelijkheid. Met name Azure-cli-core en Azure-cli-profiel afhankelijkheden zijn verwijderd uit de azureml-kern. Dit zijn de wijzigingen die invloed hebben op de gebruiker:
    + Als u ' AZ login ' uitvoert en vervolgens met behulp van azureml-SDK, zal de SDK de browser of het apparaat in het logboek nog een keer aanmelden. Er wordt geen referentie status gebruikt die is gemaakt met de melding AZ login.
    + Voor Azure CLI-verificatie, zoals het gebruik van AZ login, gebruikt u de klasse _azureml. core. Authentication. AzureCliAuthentication_ . Voor Azure CLI-verificatie kunt u met _PIP Azure-cli installeren_ in de python-omgeving waar u de azureml-SDK hebt geïnstalleerd.
    + Als u "AZ login" uitvoert met een service-principal voor Automation, raden we u aan om de klasse _azureml. core. Authentication. ServicePrincipalAuthentication_ te gebruiken, aangezien azureml-SDK geen referentie status gebruikt die is gemaakt door Azure cli. 

+ **Oplossingen voor fouten**: Deze versie bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Azure Machine Learning data prep SDK v 1.0.8

+ **Oplossingen voor problemen**
  + De prestaties van het ophalen van gegevens profielen is verbeterd.
  + Vaste kleine fouten met betrekking tot fout rapportage.
  
### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ Nieuwe grafiek ervaring voor slepen en neerzetten voor rapporten. Gebruikers kunnen een kolom of kenmerk van het ene naar het grafiek gebied slepen, waar het systeem automatisch een geschikt grafiek type voor de gebruiker selecteert op basis van het type gegevens. Gebruikers kunnen het grafiek type wijzigen in andere toepasselijke typen of extra kenmerken toevoegen.

    Ondersteunde grafiek typen:
    - Lijndiagram
    - Histogram
    - Gestapeld staaf diagram
    - Boxplot
    - Spreidingsplot
    - Ballon tekening
+ De portal genereert nu dynamisch rapporten voor experimenten. Wanneer een gebruiker een uitvoering naar een experiment verzendt, wordt er automatisch een rapport gegenereerd met vastgelegde metrische gegevens en grafieken om vergelijking te maken tussen de verschillende uitvoeringen. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning SDK voor python v-1.0.8

+ **Oplossingen voor fouten**: Deze versie bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Azure Machine Learning data prep SDK v 1.0.7

+ **Nieuwe functies**
  + Verbeteringen in Data Store (beschreven in [Data Store procedures-to-Guide](https://aka.ms/aml-data-prep-datastore-nb))
    + De mogelijkheid om te lezen uit en te schrijven naar een Azure-bestands share en ADLS-gegevens opslag in scale-up.
    + Bij het gebruik van data stores ondersteunt gegevens voorbereiding nu het gebruik van Service-Principal-verificatie in plaats van interactieve authenticatie.
    + Er is ondersteuning toegevoegd voor wasb-en wasbs-url's.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Azure Machine Learning data prep SDK v 1.0.6

+ **Oplossingen voor problemen**
  + Probleem opgelost met het lezen van open bare Azure Blob-containers die zijn gelezen op Spark

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning SDK voor python v-1.0.6
+ **Oplossingen voor fouten**: Deze versie bevat voornamelijk kleine oplossingen voor fouten

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Azure Machine Learning data prep SDK v 1.0.4

+ **Nieuwe functies**
  + `to_bool`de functie kan nu niet-overeenkomende waarden worden geconverteerd naar fout waarden. Dit is het nieuwe standaard gedrag voor niet `to_bool` - `set_column_types`overeenkomend voor en, terwijl het vorige standaard gedrag de niet-overeenkomende waarden converteert naar onwaar.
  + Bij het `to_pandas_dataframe`aanroepen is er een nieuwe optie voor het interpreteren van Null/ontbrekende waarden in numerieke kolommen als Nan.
  + De mogelijkheid is toegevoegd om het retour type van sommige expressies te controleren om ervoor te zorgen dat de type consistentie en het vroegst mislukken.
  + U kunt nu oproepen `parse_json` voor het parseren van waarden in een kolom als JSON-objecten en deze uitbreiden in meerdere kolommen.

+ **Oplossingen voor problemen**
  + Er is een fout opgelost die `set_column_types` is vastgelopen in Python 3.5.2.
  + Er is een fout opgelost die is vastgelopen tijdens het verbinden met de gegevens opslag met behulp van een AML-afbeelding.

+ **Updates**
  * Voor [beelden van notitie blokken](https://aka.ms/aml-data-prep-notebooks) voor het aan de slag gaan met zelf studies, casestudy's en hand leidingen.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Algemene beschikbaarheid

Azure Machine Learning-service is nu algemeen beschikbaar.

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
+ **Belangrijke wijzigingen**
  + Met deze release, worden ondersteuning voor het maken van een virtuele machine van Azure Machine Learning verwijderd. U kunt nog steeds een bestaande VM-cloud koppelen of een externe on-premises server. 
  + Ondersteuning voor BatchAI, die allemaal moeten worden ondersteund door Azure Machine Learning-Computing nu worden ook verwijderd.

+ **Nieuw**
  + Voor machine learning-pijplijnen:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **bijgewerkt**
  + Voor machine learning-pijplijnen:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) accepteert nu runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nu gekopieerd naar en van een SQL-gegevensbron
    + Functionaliteit in SDK maken en bijwerken van schema's voor het uitvoeren van gepubliceerde pijplijnen plannen

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Azure Machine Learning-Dataprep SDK v0.5.2
+ **Belangrijke wijzigingen** 
  * `SummaryFunction.N` is gewijzigd in `SummaryFunction.Count`.
  
+ **Oplossingen voor problemen**
  * Gebruik nieuwste AML uitvoeren Token bij het lezen van en schrijven naar gegevensopslag op afstand wordt uitgevoerd. Voorheen als het Token voor het uitvoeren van AML wordt bijgewerkt in Python, zal de runtime gegevensvoorbereiding niet worden bijgewerkt met de bijgewerkte AML uitvoeren Token.
  * Aanvullende duidelijker foutberichten
  * to_spark_dataframe () zal niet langer vastlopen wanneer Spark `Kryo` gebruikmaakt van serialisatie
  * Aantal waarden Inspector kan nu meer dan 1000 unieke waarden weergeven
  * Willekeurige Split mislukt langer als de oorspronkelijke gegevensstroom beschikt niet over een naam  

+ **Meer informatie**
  * [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Docs- en -laptops
+ ML-pijplijnen
  + Nieuwe en bijgewerkte laptops voor aan de slag met pijplijnen, batch scoping en stijl verplaatsen voorbeelden: https://aka.ms/aml-pipeline-notebooks
  + Meer informatie over het [uw eerste pijplijn maken](how-to-create-your-first-pipeline.md)
  + Meer informatie over het [voorspellingen van batch met behulp van pijplijnen worden uitgevoerd](how-to-run-batch-predictions.md)
+ Azure Machine Learning Compute-doel
  + Voor [beelden van notitie blokken](https://aka.ms/aml-notebooks) worden nu bijgewerkt met de nieuwe beheerde reken kracht.
  + [Meer informatie over deze rekenkracht](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure-portal: nieuwe functies
+ Maken en beheren van [Azure Machine Learning-Computing](how-to-set-up-training-targets.md#amlcompute) typen in de portal.
+ Gebruik van netwerkquota's bewaken en [aanvraag quotum](how-to-manage-quotas.md) voor Azure Machine Learning-Computing.
+ Status van Azure Machine Learning Compute-cluster in realtime weer geven.
+ Virtual network-ondersteuning is toegevoegd voor het maken van Azure Machine Learning-Computing en Azure Kubernetes Service.
+ Voer de gepubliceerde pijp lijnen opnieuw uit met de bestaande para meters.
+ Nieuwe [machine learning grafieken geautomatiseerde](how-to-understand-automated-ml.md) voor modellen voor tekstclassificatie (lift, verkrijgt, kalibreren, functie belang grafiek met model explainability) en regressiemodellen (dit en functie belang grafiek met model explainability). 
+ Pijplijnen kunnen worden weergegeven in Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning-SDK voor Python v0.1.80

+ **Belangrijke wijzigingen** 
  * *azureml.Train.widgets* naamruimte is verplaatst naar *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* Hiermee wordt de volgende klassen - vervangen *azureml.core.compute.BatchAICompute* en *azureml.core.compute.DSVMCompute*. De laatste klasse wordt verwijderd in toekomstige releases. De klasse AmlCompute heeft nu een eenvoudiger definitie gewoon moet een vm_size en de max_nodes en wordt uw cluster vanaf 0 naar de max_nodes automatisch schalen wanneer een job wordt verzonden. Onze [voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zijn bijgewerkt met deze gegevens en u krijgt voor beelden van gebruik te geven. We hopen u, zoals deze vereenvoudiging en nog veel meer interessante functies komen in een latere versie.

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Azure Machine Learning-Dataprep SDK v0.5.1 

Meer informatie over de SDK voor Data Prep door te lezen [verwijzen naar docs](https://aka.ms/data-prep-sdk).
+ **Nieuwe functies**
   * Een nieuwe DataPrep CLI DataPrep-pakketten uitvoeren en weergeven van het gegevensprofiel van de voor een gegevensset of gegevensstroom gemaakt
   * Opnieuw ontworpen API SetColumnType de bruikbaarheid verbeteren
   * Hernoemd smart_read_file naar auto_read_file
   * Bevat nu scheeftrekken en kurtosis in het profiel van gegevens
   * Met steekproeven toepassing stratificatie kunt steekproef
   * Van het zip-bestanden met CSV-bestanden kan lezen
   * Kan rijen met gegevens sets opsplitsen met wille keurige splitsing (bijvoorbeeld in de test-trein sets)
   * Kan alle kolom gegevens typen ophalen uit een gegevens stroom of een Data profiel door het aanroepen van`.dtypes`
   * Kan het aantal rijen ophalen van een gegevens stroom of een Data profiel door het aanroepen van`.row_count`

+ **Oplossingen voor problemen**
   * Vaste lang naar dubbele conversie 
   * Vaste assert nadat een kolom toevoegen 
   * Er is een probleem opgelost met FuzzyGrouping, waar groepen niet in sommige gevallen detecteren zou
   * Vaste sorteerfunctie rekening houden met meerdere kolommen sorteervolgorde
   * Vast en/of uitdrukkingen die vergelijkbaar zijn met `pandas` de manier waarop ze worden verwerkt
   * Vaste lezen van dbfs pad
   * Begrijpelijker foutberichten 
   * Nu niet meer mislukt bij het lezen van op externe compute-doel met AML-token
   * Nu mislukt niet meer op Linux-DSVM
   * Nu niet meer loopt vast bij niet-tekenreekswaarden in tekenreeks predicaten zijn
   * Nu assertion fouten worden verwerkt wanneer gegevensstroom correct moet mislukken
   * Biedt nu ondersteuning voor dbutils gekoppeld opslaglocaties op Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal 
De Azure-portal voor de Azure Machine Learning-service heeft de volgende updates:
  * Een nieuwe **pijplijnen** tabblad voor gepubliceerde pijplijnen.
  * Er is ondersteuning toegevoegd voor het koppelen van een bestaand HDInsight-cluster als een compute-doel.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning-SDK voor Python v0.1.74

+ **Belangrijke wijzigingen** 
  * \* Werk ruimte. compute_targets, gegevens opslag, experimenten, afbeeldingen, modellen en webservices zijn eigenschappen in plaats van methoden. Vervang bijvoorbeeld *Workspace.compute_targets()* met *Workspace.compute_targets*.
  * *Run.get_context* Hiermee wordt vervangen *Run.get_submitted_run*. De laatste methode wordt verwijderd in toekomstige releases.
  * *PipelineData* klasse verwacht nu een datastore-object als een parameter in plaats van datastore_name. Op deze manier *pijplijn* default_datastore in plaats van default_datastore_name accepteert.

+ **Nieuwe functies**
  * De Azure Machine Learning-pijplijnen [voorbeeld notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) gebruikt nu MPI stappen.
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

+ **Fouten die zijn verholpen**
  * U hoeft niet langer de pynacl-bibliotheek versie vast te maken wanneer u de webservice implementeert.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Azure Machine Learning-Dataprep SDK v0.3.0

+ **Nieuwe functies**
  * Methode transform_partition_with_file(script_path), waarmee gebruikers om door te geven in het pad van een Python-bestand uit te voeren toegevoegd

## <a name="2018-10-01"></a>10-01-2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning-SDK voor Python v0.1.65
[Versie 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) bevat nieuwe functies, meer documentatie en oplossingen voor problemen [voorbeeld notitieblokken](https://aka.ms/aml-notebooks).

Zie [de lijst met bekende problemen](resource-known-issues.md) voor meer informatie over bekende problemen en oplossingen.

+ **Belangrijke wijzigingen**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services geretourneerde woordenlijst, lijst met eerder zijn geretourneerd. Zie [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) API-documentatie.

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

+ **Azure portal-ervaring**
  * log_table() en log_row() worden ondersteund in de details van de uitvoering. 
  * Automatisch grafieken maken voor tabellen en rijen met 1, 2 of 3 numerieke kolommen en een optionele kolom categorische.

+ **Geautomatiseerde Machine Learning**
  * Verbeterde foutafhandeling en documentatie 
  * Voor het uitvoeren van de eigenschap ophalen vast prestatieproblemen. 
  * Vaste blijven uitvoeren probleem. 
  * Ensembling iteratie problemen opgelost.
  * Vaste training verkeerd-om fouten op MAC OS.
  * Downsampling macro gemiddelde pull-aanvraag/ROC-curve in aangepaste validatiescenario.
  * Logica van de extra index verwijderd.
  * Filter verwijderd uit get_output API.

+ **Pijplijnen**
  * Een methode Pipeline.publish() voor het publiceren van een pijplijn rechtstreeks, zonder een uitvoering eerst toegevoegd.   
  * Er is een methode PipelineRun. Get _pipeline_runs () toegevoegd voor het ophalen van de pijplijn uitvoeringen die zijn gegenereerd vanuit een gepubliceerde pijp lijn.

+ **Project Brainwave**
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

Een nieuwe, vernieuwde versie van Azure Machine Learning: Meer informatie over deze release: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Volgende stappen

Lees het overzicht voor [Azure machine learning service](../service/overview-what-is-azure-ml.md).
