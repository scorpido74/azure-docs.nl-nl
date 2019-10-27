---
title: Bekende problemen bij het oplossen van problemen &
titleSuffix: Azure Machine Learning
description: Een lijst met bekende problemen, tijdelijke oplossingen en probleem oplossing voor Azure Machine Learning ophalen.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 0dd0b8cf39da8039b3a59bf243284e0d5062bd78
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965600"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Bekende problemen en Azure Machine Learning voor probleem oplossing

Dit artikel helpt u bij het zoeken naar en corrigeren van fouten of fouten die zijn opgetreden bij het gebruik van Azure Machine Learning.

## <a name="upcoming-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Aanstaande SR-IOV-upgrade naar NCv3 machines in AmlCompute

Azure Compute werkt de NCv3-Sku's bij vanaf begin november ter ondersteuning van alle MPI-implementaties en-versies en RDMA-werk woorden voor met InfiniBand uitgeruste virtuele machines. Hiervoor is een korte downtime vereist. [Lees meer informatie over de SR-IOV-upgrade](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Als klant van Azure Machine Learning Managed Compute-aanbieding (AmlCompute), hoeft u op dit moment geen wijzigingen aan te brengen. Op basis van de [Update planning](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) moet u een korte breuk in uw training plannen. De service neemt de verantwoordelijkheid voor het bijwerken van de VM-installatie kopieën op uw cluster knooppunten en het automatisch schalen van uw cluster. Zodra de upgrade is voltooid, kunt u alle andere MPI-discibutions gebruiken (zoals OpenMPI met Pytorch), naast een hogere InfiniBand-band breedte, lagere latenties en betere prestaties van gedistribueerde toepassingen.

## <a name="visual-interface-issues"></a>Problemen met de Visual-Interface

Visuele interface voor problemen met de machine learning-service.

### <a name="long-compute-preparation-time"></a>Tijd van lange reken voorbereiding

Het maken van een nieuwe Compute-of aanroepen duurt een tijd, kan een paar minuten of zelfs langer duren. Het team werkt voor Optima Lise ring.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Kan geen experiment uitvoeren alleen gegevensset bevat 

Mogelijk wilt u een experiment uitvoeren met alleen gegevensset voor het visualiseren van de gegevensset. Het is echter niet toegestaan om een experiment uit te voeren, maar nog geen gegevensset bevat. Dit probleem wordt momenteel opgelost.
 
Vóór de oplossing kunt u de gegevensset koppelen aan een module voor gegevens transformatie (Selecteer kolommen in gegevensset, bewerk de meta gegevens, splits gegevens, enzovoort) en voer het experiment uit. Vervolgens kunt u de gegevensset visualiseren. 

Onder afbeelding ziet u hoe: ![data](./media/resource-known-issues/aml-visualize-data.png) visulize

## <a name="sdk-installation-issues"></a>Problemen met de SDK-installatie

**Fout bericht: kan PyYAML niet verwijderen**

Azure Machine Learning SDK voor python: PyYAML is een distutils-geïnstalleerd project. Daarom kunnen we niet nauw keurig bepalen welke bestanden er bij gedeeltelijk verwijderen horen. Gebruik het volgende om door te gaan met het installeren van de SDK en het negeren van deze fout:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Foutbericht: `ERROR: No matching distribution found for azureml-dataprep-native`**

De python 3.7.4-distributie van Anaconda heeft een fout die de installatie van azureml-SDK verbreekt. Dit probleem wordt besproken in dit [github-probleem](https://github.com/ContinuumIO/anaconda-issues/issues/11195) dat kan worden omzeild door een nieuwe Conda-omgeving te maken met behulp van de volgende opdracht:
```bash
conda create -n <env-name> python=3.7.3
```
Hiermee maakt u een Conda-omgeving met behulp van python 3.7.3, waarvoor het installatie probleem niet aanwezig is in 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemen bij het maken van Azure Machine Learning compute

Er is een zeldzame kans dat sommige gebruikers die hun Azure Machine Learning werk ruimte van de Azure Portal hebben gemaakt vóór de GA-release, mogelijk geen Azure Machine Learning Compute in die werk ruimte kunnen maken. U kunt een ondersteunings aanvraag voor de service verhogen of een nieuwe werk ruimte maken via de portal of de SDK om uzelf direct te deblokkeren.

## <a name="image-building-failure"></a>Fout bij het maken van installatie kopieën

Fout bij het maken van de installatie kopie bij het implementeren van de webservice. Tijdelijke oplossing is om ' pynacl = = 1.2.1 ' toe te voegen als een PIP-afhankelijkheid voor een installatie kopie van het Conda-bestand.

## <a name="deployment-failure"></a>Implementatie fout

Als u `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`ziet, wijzigt u de SKU voor virtuele machines die in uw implementatie worden gebruikt naar een met meer geheugen.

## <a name="fpgas"></a>FPGAs

U kunt geen modellen implementeren op Fpga's totdat u hebt aangevraagd en goedgekeurd voor FPGA-quotum. Vul het quotum aanvraag formulier in om toegang aan te vragen: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

Automatische machine learning flow tensor biedt momenteel geen ondersteuning voor tensor flow-versie 1,13. Als u deze versie installeert, werken de pakket afhankelijkheden niet meer. We werken in een toekomstige release aan het oplossen van dit probleem. 

### <a name="experiment-charts"></a>Grafieken experimenteren

Binaire classificatie grafieken (Precision-intrekken, ROC, winst curve enz.) die worden weer gegeven in automatische ML-experimenten, worden sinds 4/12 niet correct gerenderd in de gebruikers interface. In grafiek grafieken worden momenteel inverse resultaten weer gegeven, waarbij betere modellen worden weer gegeven met lagere resultaten. Een oplossing wordt onderzocht.

## <a name="datasets-and-data-preparation"></a>Data sets en gegevens voorbereiding

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Kan het Parquet-bestand niet lezen uit HTTP of ADLS gen 2

Er is een bekend probleem in de DataPrep SDK-versie 1.1.25 die een fout veroorzaakt bij het maken van een gegevensset door Parquet-bestanden te lezen uit HTTP of ADLS gen 2. Als u dit probleem wilt verhelpen, voert u een upgrade uit naar een versie die hoger is dan 1.1.26 of downgradet u naar een lagere versie dan 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Problemen met Databricks en Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Fout bij het installeren van pakketten

Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, zoals `psutil`, kunnen conflicten veroorzaken. Installeer pakketten door de bibliotheek versie te blok keren om installatie fouten te voor komen. Dit probleem heeft betrekking op Databricks en niet op de Azure Machine Learning SDK. Dit probleem kan ook optreden met andere bibliotheken. Voorbeeld:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

U kunt ook init-scripts gebruiken als u problemen ondervindt bij de installatie van python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie voor meer informatie [cluster-scoped init-scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Een automatische machine learning uitvoering annuleren

Wanneer u gebruikmaakt van geautomatiseerde machine learning mogelijkheden op Azure Databricks, kunt u een uitvoering annuleren en een nieuwe uitvoering van het experiment starten door het Azure Databricks cluster opnieuw op te starten.

### <a name="10-iterations-for-automated-machine-learning"></a>10 herhalingen > voor automatische machine learning

Als u in automatische machine learning instellingen meer dan 10 iteraties hebt, stelt u `show_output` in op `False` wanneer u de uitvoering verzendt.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget voor de Azure Machine Learning SDK/automatische machine learning

De Azure Machine Learning SDK-widget wordt niet ondersteund in een Databricks-notebook omdat de notebooks geen HTML-widgets kunnen parseren. U kunt de widget in de portal weer geven met behulp van deze python-code in uw Azure Databricks notebook-cel:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Import fout: geen module met de naam ' Pandas. core. indices '

Als u deze fout ziet wanneer u automatische machine learning gebruikt:

1. Voer deze opdracht uit om twee pakketten te installeren in uw Azure Databricks cluster: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Ontkoppel het cluster en koppel het vervolgens opnieuw aan uw notitie blok. 

Als u met deze stappen het probleem niet kunt oplossen, probeert u het cluster opnieuw op te starten.

### <a name="failtosendfeather"></a>FailToSendFeather

Als er een fout `FailToSendFeather` wordt weer gegeven bij het lezen van gegevens op Azure Databricks cluster, raadpleegt u de volgende oplossingen:

* Upgrade `azureml-sdk[automl]` pakket naar de nieuwste versie.
* Voeg .1.8-versie of hoger toe.
* Voeg `pyarrow` versie 0,11 of hoger toe.

## <a name="azure-portal"></a>Azure Portal

Als u rechtstreeks gaat om uw werk ruimte weer te geven vanaf een koppeling voor delen vanuit de SDK of de portal, kunt u de pagina met het normale overzicht niet weer geven met abonnements gegevens in de uitbrei ding. U kunt ook niet overschakelen naar een andere werk ruimte. Als u een andere werk ruimte wilt weer geven, kunt u de tijdelijke oplossing rechtstreeks naar de [Azure Portal](https://portal.azure.com) gaan en zoeken naar de naam van de werk ruimte.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Soms kan het nuttig zijn als u Diagnostische gegevens kunt opgeven wanneer u om hulp vraagt. Als u bepaalde logboeken wilt zien, gaat u naar [Azure Portal](https://portal.azure.com) en gaat u naar uw werk ruimte en selecteert u **werk ruimte > experiment > > Logboeken uit te voeren**.  U kunt deze informatie ook vinden in de sectie **experimenten** van de [landings pagina van de werk ruimte (preview)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning registreert gegevens uit verschillende bronnen tijdens de training, zoals AutoML of de docker-container die de trainings taak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met micro soft ondersteuning, kunnen ze deze logboeken gebruiken tijdens het oplossen van problemen.

## <a name="activity-logs"></a>Activiteitenlogboeken

Sommige acties in de Azure Machine Learning-werk ruimte registreren geen gegevens in het __activiteiten logboek__. U kunt bijvoorbeeld een training starten of een model registreren.

Sommige van deze acties worden weer gegeven in het gebied __activiteiten__ van uw werk ruimte, maar ze geven niet aan wie de activiteit heeft gestart.

## <a name="resource-quotas"></a>Resourcequota

Meer informatie over de [resource quota](how-to-manage-quotas.md) die u kunt tegen komen bij het werken met Azure machine learning.

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheer bewerking uitvoert op een reken doel van een externe taak, wordt een van de volgende fouten weer gegeven:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout melding als u een reken doel probeert te maken of koppelen van een ML-pijp lijn die wordt ingediend voor externe uitvoering.

## <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als er een fout `Unable to upload project files to working directory in AzureFile because the storage is overloaded` wordt weer gegeven, moet u de volgende tijdelijke oplossingen Toep assen.

Als u bestands share gebruikt voor andere werk belastingen, zoals gegevens overdracht, is de aanbeveling om blobs te gebruiken zodat de bestands share gratis kan worden gebruikt voor het verzenden van uitvoeringen. U kunt de werk belasting ook splitsen tussen twee verschillende werk ruimten.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in azure Kubernetes-service fouten 

Veel mislukte webservice-fouten in de Azure Kubernetes-service kunnen worden opgespoord door verbinding te maken met het cluster met behulp van `kubectl`. U kunt de `kubeconfig.json` ophalen voor een Azure Kubernetes service-cluster door het uit te voeren

```bash
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning-onderdelen in AKS-cluster bijwerken

Updates voor Azure Machine Learning onderdelen die zijn geïnstalleerd in een Azure Kubernetes service-cluster, moeten hand matig worden toegepast. 

> [!WARNING]
> Controleer voordat u de volgende acties uitvoert de versie van uw Azure Kubernetes service-cluster. Als de Cluster versie gelijk is aan of groter is dan 1,14, kunt u uw cluster niet opnieuw koppelen aan de Azure Machine Learning-werk ruimte.

U kunt deze updates Toep assen door het cluster te ontkoppelen van de Azure Machine Learning-werk ruimte en vervolgens het cluster opnieuw te koppelen aan de werk ruimte. Als SSL is ingeschakeld in het cluster, moet u het SSL-certificaat en de persoonlijke sleutel opgeven wanneer het cluster opnieuw wordt gekoppeld. 

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

Als u het SSL-certificaat en de persoonlijke sleutel niet meer hebt of als u een certificaat gebruikt dat is gegenereerd door Azure Machine Learning, kunt u de bestanden ophalen voordat u het cluster ontkoppelt door verbinding te maken met het cluster met `kubectl` en de geheime `azuremlfessl` op te halen.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in de indeling basis-64-code ring. U moet base-64 decoderen van de `cert.pem` en `key.pem` onderdelen van de geheimen voordat u ze aan de `attach_config.enable_ssl`verschaft. 

## <a name="recommendations-for-error-fix"></a>Aanbevelingen voor probleem oplossing
Op basis van algemene waarneming zijn dit de aanbevelingen van Azure ML voor het oplossen van enkele veelvoorkomende fouten in azure ML.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (geen module met de naam)
Als u in ModuleErrors uitvoert terwijl experimenten in azure ML worden ingediend, betekent dit dat het trainings script verwacht dat er een pakket wordt geïnstalleerd, maar dit wordt niet toegevoegd. Wanneer u de naam van het pakket opgeeft, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor uw training. 

Als u [schattingen](https://docs.microsoft.com/en-us/azure/machine-learning/service/concept-azure-machine-learning-architecture#estimators) gebruikt om experimenten in te dienen, kunt u een pakket naam opgeven via `pip_packages` of `conda_packages` para meter in de Estimator op basis van de bron van waaruit u het pakket wilt installeren. U kunt ook een yml-bestand met al uw afhankelijkheden opgeven met `conda_dependencies_file`of al uw PIP-vereisten in een txt-bestand weer geven met behulp van `pip_requirements_file`-para meter.

Azure ML biedt ook Framework-specifieke schattingen voor tensor flow, PyTorch, Chainer en SKLearn. Door deze ramingen te gebruiken, moet u ervoor zorgen dat de Framework afhankelijkheden namens u zijn geïnstalleerd in de omgeving die wordt gebruikt voor de training. U hebt de optie om extra afhankelijkheden op te geven zoals hierboven wordt beschreven. 
 
 Azure ML bewaart docker-installatie kopieën en de inhoud ervan kunnen worden weer gegeven in [AzureML-containers](https://github.com/Azure/AzureML-Containers).
Specifieke Framework-afhankelijkheden worden weer gegeven in de bijbehorende Framework-documentatie- [Chainer](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [tensor flow](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

>[Opmerking!] Als u denkt dat een bepaald pakket algemeen genoeg is om te worden toegevoegd aan de bewaarde afbeeldingen en omgevingen van Azure ML, kunt u een GitHub-probleem veroorzaken in [AzureML-containers](https://github.com/Azure/AzureML-Containers). 
 
 ### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (naam niet gedefinieerd), AttributeError (object heeft geen kenmerk)
Deze uitzonde ring moet afkomstig zijn uit uw trainings scripts. U kunt de logboek bestanden van Azure Portal bekijken voor meer informatie over de specifieke naam niet gedefinieerd of kenmerk fout. In de SDK kunt u `run.get_details()` gebruiken om het fout bericht te bekijken. Hiermee worden ook alle logboek bestanden weer geven die zijn gegenereerd voor de uitvoering. Bekijk uw trainings script, los het probleem op en probeer het opnieuw. 

### <a name="horovod-is-shutdown"></a>Horovod is afgesloten
In de meeste gevallen betekent deze uitzonde ring dat er een onderliggende uitzonde ring is opgetreden in een van de processen waardoor horovod is afgesloten. Elke positie in de MPI-taak krijgt een eigen toegewezen logboek bestand in azure ML. Deze logboeken hebben de naam `70_driver_logs`. In het geval van gedistribueerde trainingen worden de namen van de logboeken in het achtervoegsel opgenomen met `_rank` zodat de Logboeken eenvoudig kunnen worden onderscheiden. Als u de exacte fout wilt vinden die horovod afsluiten heeft veroorzaakt, gaat u naar alle logboek bestanden en zoekt u naar `Traceback` aan het einde van de driver_log-bestanden. Met een van deze bestanden krijgt u de daad werkelijke onderliggende uitzonde ring. 
