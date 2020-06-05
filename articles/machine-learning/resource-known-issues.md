---
title: Bekende problemen bij het oplossen van problemen &
titleSuffix: Azure Machine Learning
description: Ontvang hulp bij het vinden en corrigeren van fouten of fouten in Azure Machine Learning. Meer informatie over bekende problemen, probleem oplossing en tijdelijke oplossingen.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: contperfq4
ms.date: 03/31/2020
ms.openlocfilehash: 36279039bbc63173a4c3c06901a0800a0893cebb
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430868"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Bekende problemen en probleem oplossing in Azure Machine Learning

Dit artikel helpt u bij het oplossen van bekende problemen die kunnen optreden bij het gebruik van Azure Machine Learning. 

Zie [volgende stappen](#next-steps) aan het einde van dit artikel voor meer informatie over het oplossen van problemen.

> [!TIP]
> Fouten of andere problemen kunnen het resultaat zijn van [resource quota](how-to-manage-quotas.md) die u tegen komt bij het werken met Azure machine learning. 

## <a name="access-diagnostic-logs"></a>Toegang tot diagnostische logboeken

Soms kan het nuttig zijn als u Diagnostische gegevens kunt opgeven wanneer u om hulp vraagt. Voor een overzicht van de logboeken: 
1. Ga naar [Azure machine learning Studio](https://ml.azure.com). 
1. Selecteer aan de linkerkant **experimenteren** 
1. Selecteer een experiment.
1. Selecteer een run.
1. Selecteer bovenaan **uitvoer en logboeken**.

> [!NOTE]
> Azure Machine Learning registreert gegevens uit verschillende bronnen tijdens de training, zoals AutoML of de docker-container die de trainings taak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.


## <a name="installation-and-import"></a>Installatie en import
                           
* **PIP-installatie: afhankelijkheden zijn niet gegarandeerd consistent met de installatie van één regel:** 

   Dit is een bekende beperking van PIP, omdat er geen werkende afhankelijkheids conflict Oplosser is wanneer u als één regel installeert. De eerste unieke afhankelijkheid is de enige die er wordt weer gegeven. 

   In de volgende code `azure-ml-datadrift` en `azureml-train-automl` worden beide geïnstalleerd met behulp van een PIP-installatie van één regel. 
     ```
       pip install azure-ml-datadrift, azureml-train-automl
     ```
   Voor dit voor beeld `azure-ml-datadrift` is versie > 1,0 vereist en `azureml-train-automl` is versie < 1,2 vereist. Als de meest recente versie van `azure-ml-datadrift` is 1,3, worden beide pakketten bijgewerkt naar 1,3, ongeacht de vereiste van het `azureml-train-automl` pakket voor een oudere versie. 

   Als u ervoor wilt zorgen dat de juiste versies voor uw pakketten zijn geïnstalleerd, installeert u met meerdere regels, zoals in de volgende code. Volg orde is hier geen probleem, omdat PIP expliciet downgradet als onderdeel van de volgende regel aanroep. Dit betekent dat de juiste versie afhankelijkheden worden toegepast.
    
     ```
        pip install azure-ml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Het uitleg pakket wordt niet gegarandeerd geïnstalleerd tijdens de installatie van de azureml-Train-automl-client:** 
   
   Wanneer u een externe AutoML uitvoert met model uitleg is ingeschakeld, wordt er een fout bericht weer gegeven: ' Installeer het pakket met de azureml-uitleg-model voor de beschrijving van het model '. Dit is een bekend probleem. Volg een van de onderstaande stappen als tijdelijke oplossing:
  
  1. Installeer het model voor azureml-uitleg-lokaal.
   ```
      pip install azureml-explain-model
   ```
  2. Schakel de uitleg functie volledig uit door model_explainability = False door te geven in de configuratie van AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda-fouten: normaal gezien tijdens het AutoML-experiment:**
   
   Wanneer u uw omgeving hand matig kunt instellen met behulp van PIP, kunt u kenmerk fouten (met name van Panda) ondervinden omdat er niet-ondersteunde pakket versies worden geïnstalleerd. Als u dergelijke fouten wilt voor komen, [installeert u de AUTOML SDK met behulp van de automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Open een Anaconda-prompt en kloon de GitHub-opslag plaats voor een aantal voorbeeld notitieblokken.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. cd naar de map How-to-use-azureml/automated machine learning waarin de voorbeeld notitieblokken zijn geëxtraheerd en vervolgens worden uitgevoerd:
    
    ```bash
    automl_setup
    ```
  
* **Fout bericht: kan PyYAML niet verwijderen**

    Azure Machine Learning SDK voor python: PyYAML is een `distutils` geïnstalleerd project. Daarom kunnen we niet nauw keurig bepalen welke bestanden er bij gedeeltelijk verwijderen horen. Gebruik het volgende om door te gaan met het installeren van de SDK en het negeren van deze fout:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Databricks-fout bij het installeren van pakketten**

    Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, zoals `psutil` , kunnen conflicten veroorzaken. Installeer pakketten door de bibliotheek versie te blok keren om installatie fouten te voor komen. Dit probleem heeft betrekking op Databricks en niet op de Azure Machine Learning SDK. Dit probleem kan ook optreden met andere bibliotheken. Voorbeeld:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    U kunt ook init-scripts gebruiken als u problemen ondervindt bij de installatie van python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie voor meer informatie [cluster-scoped init-scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Databricks import-fout: kan de naam ' timedelta ' niet importeren uit ' Pandas. _libs. tslibs '**: als u deze fout ziet wanneer u automatische machine learning gebruikt, voert u de twee volgende regels uit in uw notitie blok:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks-import fout: geen module met de naam ' Pandas. core. indices '**: als u deze fout ziet wanneer u gebruikmaakt van automatische machine learning:

    1. Voer deze opdracht uit om twee pakketten te installeren in uw Azure Databricks cluster:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Ontkoppel het cluster en koppel het vervolgens opnieuw aan uw notitie blok.
    
    Als u met deze stappen het probleem niet kunt oplossen, probeert u het cluster opnieuw op te starten.

* **Databricks FailToSendFeather**: als er een `FailToSendFeather` fout wordt weer gegeven bij het lezen van gegevens op Azure Databricks cluster, raadpleegt u de volgende oplossingen:
    
    * Upgrade `azureml-sdk[automl]` pakket naar de nieuwste versie.
    * Voeg `azureml-dataprep` versie 1.1.8 of hoger toe.
    * Voeg `pyarrow` versie 0,11 of hoger toe.
    
## <a name="create-and-manage-workspaces"></a>Werk ruimten maken en beheren

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werk ruimte naar een ander abonnement of het verplaatsen van het abonnement dat eigenaar is naar een nieuwe Tenant, wordt niet ondersteund. Dit kan fouten veroorzaken.

* **Azure Portal**: als u rechtstreeks gaat om uw werk ruimte weer te geven vanaf een koppeling voor delen vanuit de SDK of de portal, kunt u de pagina met het normale **overzicht** niet weer geven met abonnements gegevens in de uitbrei ding. U kunt ook niet overschakelen naar een andere werk ruimte. Als u een andere werk ruimte wilt weer geven, gaat u rechtstreeks naar [Azure machine learning Studio](https://ml.azure.com) en zoekt u naar de naam van de werk ruimte.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

* **Problemen**met het maken van AmlCompute: er is een zeldzame kans dat sommige gebruikers die hun Azure machine learning werk ruimte hebben gemaakt vanaf de Azure Portal vóór de Ga-release mogelijk geen AmlCompute in die werk ruimte kunnen maken. U kunt een ondersteunings aanvraag voor de service verhogen of een nieuwe werk ruimte maken via de portal of de SDK om uzelf direct te deblokkeren.

## <a name="work-with-data"></a>Werken met gegevens

### <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als er een fout bericht wordt weer gegeven `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , moet u de volgende tijdelijke oplossingen Toep assen.

Als u bestands share gebruikt voor andere werk belastingen, zoals gegevens overdracht, is de aanbeveling om blobs te gebruiken zodat de bestands share gratis kan worden gebruikt voor het verzenden van uitvoeringen. U kunt de werk belasting ook splitsen tussen twee verschillende werk ruimten.

### <a name="passing-data-as-input"></a>Gegevens door geven als invoer

*  **TypeError: FileNotFound: bestand of map**: deze fout treedt op als het bestandspad dat u opgeeft niet waar het bestand zich bevindt. U moet ervoor zorgen dat de manier waarop u naar het bestand verwijst consistent is met de locatie waar u uw gegevensset op het reken doel hebt gekoppeld. We raden u aan het abstracte pad te gebruiken bij het koppelen van een gegevensset aan een reken doel om te zorgen voor een deterministische status. In de volgende code koppelen we bijvoorbeeld de gegevensset aan onder de hoofdmap van het bestands systeem van het berekenings doel `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Als u de voorloop back slash (/) niet opneemt, moet u voor voegsel van de werkmap opgeven, bijvoorbeeld `/mnt/batch/.../tmp/dataset` op het berekenings doel om aan te geven waar u de gegevensset wilt koppelen.

### <a name="data-labeling-projects"></a>Gegevens label projecten

|Probleem  |Oplossing  |
|---------|---------|
|Alleen gegevens sets die zijn gemaakt op BLOB-gegevens opslag kunnen worden gebruikt.     |  Dit is een bekende beperking van de huidige release.       |
|Nadat het project is gemaakt, wordt de ' initialisatie ' gedurende lange tijd weer gegeven.     | Vernieuw de pagina hand matig. De initialisatie moet ongeveer 20 data Points per seconde door lopen. Het ontbreken van automatisch vernieuwen is een bekend probleem.         |
|Bij het controleren van afbeeldingen worden nieuwe gelabelde afbeeldingen niet weer gegeven.     |   Als u alle gelabelde afbeeldingen wilt laden, kiest u de **eerste** knop. Met de **eerste** knop gaat u terug naar de voor kant van de lijst, maar worden alle gelabelde gegevens geladen.      |
|Druk op ESC-toets terwijl labels voor object detectie een label met een grootte van nul maken in de linkerbovenhoek. Het verzenden van labels met deze status mislukt.     |   Verwijder het label door te klikken op de kruis markering ernaast.  |

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

Bekende problemen:

* **Tijd van lange Compute-voor bereiding**: het kan enkele minuten of zelfs langer duren wanneer u voor het eerst verbinding maakt of een compute-doel maakt. 

## <a name="train-models"></a>Modellen trainen

* **ModuleErrors (geen module met de naam)**: als u in ModuleErrors uitvoert terwijl experimenten in azure ml worden ingediend, betekent dit dat het trainings script verwacht dat er een pakket wordt geïnstalleerd, maar dit wordt niet toegevoegd. Wanneer u de naam van het pakket opgeeft, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor de uitvoering van uw training. 

    Als u [schattingen](concept-azure-machine-learning-architecture.md#estimators) gebruikt om experimenten in te dienen, kunt u een pakket naam opgeven via `pip_packages` of `conda_packages` para meter in de Estimator op basis van de bron die u wilt installeren van het pakket. U kunt ook een yml-bestand met al uw afhankelijkheden opgeven `conda_dependencies_file` of al uw PIP-vereisten in een txt-bestand met behulp van `pip_requirements_file` para meter. Als u uw eigen Azure ML-omgevings object hebt dat u de standaard installatie kopie wilt overschrijven die wordt gebruikt door de Estimator, kunt u die omgeving opgeven via de `environment` para meter van de Estimator-constructor.

    Azure ML biedt ook Framework-specifieke schattingen voor tensor flow, PyTorch, Chainer en SKLearn. Door deze ramingen te gebruiken, moet u ervoor zorgen dat de basis raamwerk afhankelijkheden namens u zijn geïnstalleerd in de omgeving die wordt gebruikt voor de training. U hebt de optie om extra afhankelijkheden op te geven zoals hierboven wordt beschreven. 
 
    Azure ML bewaart docker-installatie kopieën en de inhoud ervan kunnen worden weer gegeven in [AzureML-containers](https://github.com/Azure/AzureML-Containers).
    Framework-specifieke afhankelijkheden worden weer gegeven in de bijbehorende Framework-documentatie- [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Als u denkt dat een bepaald pakket algemeen genoeg is om te worden toegevoegd aan de bewaarde afbeeldingen en omgevingen van Azure ML, kunt u een GitHub-probleem veroorzaken in [AzureML-containers](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (naam niet gedefinieerd), AttributeError (object heeft geen kenmerk)**: Deze uitzonde ring moet afkomstig zijn uit uw trainings scripts. U kunt de logboek bestanden van Azure Portal bekijken voor meer informatie over de specifieke naam niet gedefinieerd of kenmerk fout. U kunt de SDK gebruiken `run.get_details()` om het fout bericht te bekijken. Hiermee worden ook alle logboek bestanden weer geven die zijn gegenereerd voor de uitvoering. Bekijk uw trainings script en los het probleem op voordat u de uitvoering opnieuw verzendt. 

* **Horovod is uitgeschakeld**: in de meeste gevallen is het mogelijk dat er een onderliggende uitzonde ring is opgetreden in een van de processen waardoor Horovod werd afgesloten als u problemen ondervindt met ' AbortedError: Horovod is afgesloten '. Elke positie in de MPI-taak krijgt een eigen toegewezen logboek bestand in azure ML. Deze logboeken hebben de naam `70_driver_logs` . In het geval van gedistribueerde trainingen worden de namen van de logboeken in een achtervoegsel opgenomen, `_rank` zodat het eenvoudiger is om de logboeken te onderscheiden. Als u de exacte fout wilt vinden die ervoor heeft gezorgd dat Horovod wordt afgesloten, gaat u naar alle logboek bestanden en zoekt u `Traceback` aan het einde van de driver_log bestanden. Met een van deze bestanden krijgt u de daad werkelijke onderliggende uitzonde ring. 

* **Verwijdering van de uitvoering of het experiment**: experimenten kunnen worden gearchiveerd met behulp van de methode [experiment. Archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) of vanuit de weer gave van het tabblad experiment in azure machine learning Studio-client via de knop ' Archief experiment '. Met deze actie wordt het experiment verborgen in lijst query's en weer gaven, maar wordt het niet verwijderd.

    Het permanent verwijderen van afzonderlijke experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie [uw machine learning service-werkruimte gegevens exporteren of verwijderen](how-to-export-delete-data.md)voor meer informatie over het verwijderen van werk ruimte-assets.

* Het **metrieke document is te groot**: Azure machine learning heeft interne limieten voor de grootte van metrische objecten die tegelijk kunnen worden geregistreerd vanuit een training-uitvoering. Als er een fout bericht wordt weer gegeven dat het metrische document te groot is bij het vastleggen van een metrische waarde voor een lijst, kunt u de lijst in kleinere segmenten splitsen, bijvoorbeeld:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Intern voegt Azure ML de blokken met dezelfde metrische naam toe aan een aaneengesloten lijst.

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

* **Tensor flow**: geautomatiseerde machine learning biedt momenteel geen ondersteuning voor tensor flow-versie 1,13. Als u deze versie installeert, werken de pakket afhankelijkheden niet meer. We werken in een toekomstige release aan het oplossen van dit probleem.

* **Experimenteer grafieken**: binaire classificatie grafieken (Precision-intrekken, Roc, winst curve enz.) die worden weer gegeven in automatische ml-experimenten, worden niet correct gerenderd in de gebruikers interface sinds 4/12. In grafiek grafieken worden momenteel inverse resultaten weer gegeven, waarbij betere modellen worden weer gegeven met lagere resultaten. Een oplossing wordt onderzocht.

* **Databricks annuleren automatische uitvoering van een machine learning**: wanneer u gebruikmaakt van geautomatiseerde machine learning mogelijkheden op Azure Databricks, kunt u een uitvoering annuleren en een nieuwe uitvoering van het experiment starten door het Azure Databricks-cluster opnieuw op te starten.

* **Databricks >tien herhalingen voor automatische machine learning**machine learning: als u meer dan 10 iteraties hebt, moet u instellen `show_output` op `False` Wanneer u de uitvoering verzendt.

* **Databricks-widget voor de Azure machine learning SDK en geautomatiseerde machine learning**: de SDK-widget Azure machine learning wordt niet ondersteund in een Databricks-notebook omdat de notebooks geen HTML-widgets kunnen parseren. U kunt de widget in de portal weer geven met behulp van deze python-code in uw Azure Databricks notebook-cel:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Modellen implementeren & bedienen

Voer deze acties uit voor de volgende fouten:

|Fout  | Oplossing  |
|---------|---------|
|Fout bij het maken van de installatie kopie bij het implementeren van de webservice     |  Voeg ' pynacl = = 1.2.1 ' toe als een PIP-afhankelijkheid voor het Conda-bestand voor installatie kopie configuratie       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Wijzig de SKU voor virtuele machines die in uw implementatie worden gebruikt naar een met meer geheugen. |
|FPGA-fout     |  U kunt geen modellen implementeren op Fpga's totdat u hebt aangevraagd en goedgekeurd voor FPGA-quotum. Vul het quotum aanvraag formulier in om toegang aan te vragen:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning-onderdelen in AKS-cluster bijwerken

Updates voor Azure Machine Learning onderdelen die zijn geïnstalleerd in een Azure Kubernetes service-cluster, moeten hand matig worden toegepast. 

U kunt deze updates Toep assen door het cluster te ontkoppelen van de Azure Machine Learning-werk ruimte en vervolgens het cluster opnieuw te koppelen aan de werk ruimte. Als TLS is ingeschakeld in het cluster, moet u het TLS/SSL-certificaat en de persoonlijke sleutel opgeven wanneer u het cluster opnieuw koppelt. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Als u het TLS/SSL-certificaat en de persoonlijke sleutel niet meer hebt of als u een certificaat gebruikt dat is gegenereerd door Azure Machine Learning, kunt u de bestanden ophalen voordat u het cluster ontkoppelt door verbinding te maken met het cluster met behulp `kubectl` van en het geheim op te halen `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in de indeling basis-64-code ring. U moet base-64 decoderen van de- `cert.pem` en- `key.pem` onderdelen van de geheimen voordat u deze opgeeft `attach_config.enable_ssl` . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in azure Kubernetes-service fouten

Veel webservice-fouten in de Azure Kubernetes-service kunnen worden opgespoord door verbinding te maken met het cluster met behulp van `kubectl` . U kunt het `kubeconfig.json` voor een Azure Kubernetes-service cluster verkrijgen door het uit te voeren

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheer bewerking uitvoert op een reken doel van een externe taak, wordt een van de volgende fouten weer gegeven: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout melding als u een reken doel probeert te maken of koppelen van een ML-pijp lijn die wordt ingediend voor externe uitvoering.

## <a name="next-steps"></a>Volgende stappen

Meer artikelen over het oplossen van problemen met Azure Machine Learning bekijken:

* [Problemen met docker-implementatie oplossen met Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Fout opsporing machine learning pijp lijnen](how-to-debug-pipelines.md)
* [Fouten opsporen in de ParallelRunStep-klasse vanuit de Azure Machine Learning SDK](how-to-debug-parallel-run-step.md)
* [Interactieve fout opsporing van een machine learning Compute-exemplaar met VS-code](how-to-set-up-vs-code-remote.md)
* [Application Insights gebruiken om fouten in machine learning pijp lijnen op te sporen](how-to-debug-pipelines-application-insights.md)
