---
title: Bekende problemen & probleemoplossing
titleSuffix: Azure Machine Learning
description: Hier vindt u een lijst met de bekende problemen, tijdelijke oplossingen en probleemoplossing voor Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455720"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Bekende problemen en het oplossen van problemen met Azure Machine Learning

In dit artikel u fouten of fouten vinden en corrigeren die zijn opgetreden bij het gebruik van Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problemen met de installatie van SDK's

**Foutbericht: Kan 'PyYAML' niet verwijderen**

Azure Machine Learning SDK voor Python: PyYAML is een distutils geïnstalleerd project. Daarom kunnen we niet nauwkeurig bepalen welke bestanden ertoe behoren als er een gedeeltelijke verwijderen is. Gebruik het als volgt te gaan met het installeren van de SDK terwijl u deze fout negeert:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Foutbericht:`ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda's Python 3.7.4 distributie heeft een bug die azureml-sdk install breekt. Dit probleem wordt besproken in dit [GitHub-probleem](https://github.com/ContinuumIO/anaconda-issues/issues/11195) Dit kan worden opgelost door een nieuwe Conda-omgeving te maken met behulp van deze opdracht:
```bash
conda create -n <env-name> python=3.7.3
```
Dat creëert een Conda-omgeving met Python 3.7.3, die niet het installatieprobleem aanwezig is in 3.7.4.

## <a name="training-and-experimentation-issues"></a>Problemen met opleiding en experimenten

### <a name="metric-document-is-too-large"></a>Metriek document is te groot
Azure Machine Learning heeft interne limieten voor de grootte van metrische objecten die in één keer kunnen worden geregistreerd vanaf een trainingsrun. Als u een fout 'Metrisch document is te groot' tegenkomt bij het registreren van een statistiek met een lijstwaarde, probeert u de lijst op te splitsen in kleinere segmenten, bijvoorbeeld:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Intern voegt Azure ML de blokken met dezelfde metrische naam samen in een aaneengesloten lijst.

### <a name="moduleerrors-no-module-named"></a>Modulefouten (geen benoemde module)
Als u modulefouten tegenkomt tijdens het indienen van experimenten in Azure ML, betekent dit dat het trainingsscript verwacht dat een pakket wordt geïnstalleerd, maar niet wordt toegevoegd. Zodra u de pakketnaam hebt opgemaakt, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor uw trainingsrun. 

Als u [Schatters](concept-azure-machine-learning-architecture.md#estimators) gebruikt om experimenten in te `pip_packages` dienen, u een pakketnaam opgeven via of `conda_packages` parameter in de schatter op basis van de bron waarvan u het pakket wilt installeren. U ook een yml-bestand opgeven `conda_dependencies_file`met al uw afhankelijkheden met `pip_requirements_file` behulp van of een lijst van al uw pip-vereisten in een txt-bestand met behulp van parameter. Als u uw eigen Azure ML-omgevingsobject hebt dat u de standaardafbeelding wilt overschrijven `environment` die door de schatter wordt gebruikt, u die omgeving opgeven via de parameter van de schatterconstructor.

Azure ML biedt ook kaderspecifieke schatters voor Tensorflow, PyTorch, Chainer en SKLearn. Met behulp van deze schatters zal ervoor zorgen dat de kern kader afhankelijkheden worden geïnstalleerd namens u in de omgeving die wordt gebruikt voor training. U hebt de optie om extra afhankelijkheden op te geven zoals hierboven beschreven. 
 
Azure ML onderhouden docker afbeeldingen en de inhoud ervan kan worden gezien in [AzureML Containers](https://github.com/Azure/AzureML-Containers).
Kaderspecifieke afhankelijkheden worden vermeld in de desbetreffende kaderdocumentatie - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Als u denkt dat een bepaald pakket vaak genoeg is om te worden toegevoegd in azure ml onderhouden afbeeldingen en omgevingen, u een GitHub-probleem in [AzureML-containers](https://github.com/Azure/AzureML-Containers)opte roepen. 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (Naam niet gedefinieerd), AttributeError (object heeft geen attribuut)
Deze uitzondering moet afkomstig zijn van uw trainingsscripts. U de logboekbestanden van azure-portal bekijken om meer informatie te krijgen over de specifieke naam die niet is gedefinieerd of een kenmerkfout. Vanuit de SDK kunt `run.get_details()` u de foutmelding bekijken. Dit bevat ook een lijst van alle logboekbestanden die voor uw run zijn gegenereerd. Zorg ervoor dat u uw trainingsscript bekijkt en de fout oplost voordat u de run opnieuw indient. 

### <a name="horovod-has-been-shut-down"></a>Horovod is gesloten
In de meeste gevallen als u "AbortedError: Horovod is stilgelegd" deze uitzondering betekent dat er een onderliggende uitzondering in een van de processen die horovod veroorzaakt af te sluiten. Elke rang in de MPI-taak krijgt een eigen specifiek logboekbestand in Azure ML. Deze logboeken `70_driver_logs`hebben de naam . In het geval van gedistribueerde training, `_rank` zijn de log namen achtervoegsel met om het gemakkelijker te maken om de logs te onderscheiden. Om de exacte fout die horovod veroorzaakt af te sluiten, `Traceback` ga door alle log bestanden en zoeken aan het einde van de driver_log bestanden. Een van deze bestanden geeft u de werkelijke onderliggende uitzondering. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>SR-IOV beschikbaarheid op NCv3 machines in AmlCompute voor gedistribueerde training
Azure Compute heeft een [SR-IOV-upgrade](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) van NCv3-machines uitgerold, die klanten kunnen gebruiken met het managed compute offering (AmlCompute) van Azure ML. De updates zullen de ondersteuning van de gehele MPI-stack en het gebruik van het Infiniband RDMA-netwerk mogelijk maken voor verbeterde gedistribueerde trainingsprestaties met meerdere nodes, met name voor deep learning.

Bekijk het [updateschema](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) om te zien wanneer ondersteuning voor uw regio wordt uitgerold.

### <a name="run-or-experiment-deletion"></a>Verwijdering uitvoeren of experimenteren
Experimenten kunnen worden gearchiveerd met behulp van de methode [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) of via de tabbladweergave Experiment in azure Machine Learning-studioclient via de knop 'Archiefexperiment'. Met deze actie wordt het experiment verborgen in lijstquery's en weergaven, maar wordt het niet verwijderd.

Permanente verwijdering van individuele experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie Uw [Machine Learning-servicewerkruimtegegevens exporteren of verwijderen](how-to-export-delete-data.md)voor meer informatie over het verwijderen van workspace-elementen van Workspace.

## <a name="azure-machine-learning-compute-issues"></a>Azure Machine Learning Compute-problemen
Bekende problemen met het gebruik van Azure Machine Learning Compute (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Problemen met het maken van AmlCompute

Er is een zeldzame kans dat sommige gebruikers die hun Azure Machine Learning-werkruimte hebben gemaakt vanuit de Azure-portal vóór de GA-release, mogelijk geen AmlCompute in die werkruimte kunnen maken. U een ondersteuningsverzoek indienen tegen de service of een nieuwe werkruimte maken via de portal of de SDK om uzelf onmiddellijk te deblokkeren.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Storing: SR-IOV upgrade naar NCv3-machines in AmlCompute

Azure Compute werkt de NCv3 SKU's vanaf begin november 2019 bij om alle MPI-implementaties en -versies te ondersteunen, en RDMA-werkwoorden voor virtuele machines met InfiniBand. Dit vereist een korte downtime - [lees meer over de SR-IOV upgrade](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Als klant van het managed compute offering (AmlCompute) van Azure Machine Learning hoeft u op dit moment geen wijzigingen aan te brengen. Op basis van het [updateschema](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) moet u een korte pauze in uw training plannen. De service neemt de verantwoordelijkheid om de VM-afbeeldingen op uw clusterknooppunten bij te werken en uw cluster automatisch op te schalen. Zodra de upgrade is voltooid, u mogelijk alle andere MPI-distributies (zoals OpenMPI met Pytorch) gebruiken, naast het verkrijgen van een hogere InfiniBand-bandbreedte, lagere latencies en beter gedistribueerde toepassingsprestaties.

## <a name="azure-machine-learning-designer-issues"></a>Problemen met Azure Machine Learning-ontwerpers

Bekende problemen met de ontwerper.

### <a name="long-compute-preparation-time"></a>Lange voorbereidingstijd voor berekenen

Het maken van nieuwe compute of evoke verlaten compute kost tijd, kan een paar minuten of zelfs langer. Het team werkt aan optimalisatie.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Een experiment kan niet alleen een gegevensset uitvoeren 

Misschien wilt u een experiment uitvoeren dat alleen gegevensset bevat om de gegevensset te visualiseren. Het is echter niet toegestaan om een experiment uit te voeren bevat vandaag de dag alleen gegevensset. We zijn actief bezig met het oplossen van dit probleem.
 
Voordat de oplossing is opgelost, u de gegevensset verbinden met een gegevenstransformatiemodule (Kolommen selecteren in gegevensset, Metagegevens bewerken, Gegevens splitsen enz.) en het experiment uitvoeren. Vervolgens u de dataset visualiseren. 

Onderstaande afbeelding ![laat zien hoe: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Image building failure

Image building failure bij het implementeren van webservice. Tijdelijke oplossing is om "pynacl==1.2.1" toe te voegen als een pip-afhankelijkheid aan het Conda-bestand voor afbeeldingsconfiguratie.

## <a name="deployment-failure"></a>Implementatiefout

Als u `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`de SKU voor VM's die in uw implementatie worden gebruikt, wijzigt, wijzigt u deze in een implementatie die meer geheugen heeft.

## <a name="fpgas"></a>FPGAs

U geen modellen implementeren op FPGA's totdat u hebt aangevraagd en goedgekeurd voor FPGA-quota. Als u toegang wilt aanvragen, vult u het formulier voor het quotumaanvraagformulier in:https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

Tensor Flow Automated machine learning ondersteunt momenteel geen tensorflowversie 1.13. Als u deze versie installeert, worden pakketafhankelijkheden niet meer werkt. We werken eraan om dit probleem op te lossen in een toekomstige release.

### <a name="experiment-charts"></a>Experimentgrafieken

Binaire classificatiegrafieken (precisie-recall, ROC, winstcurve enz.) die worden weergegeven in geautomatiseerde ML-experimentiteraties worden sinds 4/12 niet correct weergegeven in de gebruikersinterface. Grafiekplots tonen momenteel omgekeerde resultaten, waarbij beter presterende modellen worden weergegeven met lagere resultaten. Een resolutie wordt onderzocht.

## <a name="datasets-and-data-preparation"></a>Gegevenssets en gegevensvoorbereiding

Dit zijn bekende problemen voor Azure Machine Learning-gegevenssets.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Geen dergelijk bestand of map

Deze fout treedt op als het bestandspad dat u opgeeft niet de plaats is waar het bestand zich bevindt. U moet ervoor zorgen dat de manier waarop u naar het bestand verwijst, overeenkomt met de plaats waar u uw gegevensset op uw gegevensset hebt gemonteerd. Om een deterministische toestand te garanderen, raden we u aan het abstracte pad te gebruiken bij het monteren van een gegevensset op een compute-doel. In de volgende code monteren we bijvoorbeeld de gegevensset onder de `/tmp`hoofdmap van het bestandssysteem van het rekendoel. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Als u de leidende slash naar voren niet opneemt, '/', moet u `/mnt/batch/.../tmp/dataset` de werkmap vooraf maken, bijvoorbeeld op het rekendoel om aan te geven waar u de gegevensset wilt monteren.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Niet om parketbestand van HTTP of ADLS Gen 2 te lezen

Er is een bekend probleem in de AzureML DataPrep SDK-versie 1.1.25 dat een fout veroorzaakt bij het maken van een gegevensset door parketbestanden van HTTP of ADLS Gen 2 te lezen. Het zal `Cannot seek once reading started.`mislukken met . Als u dit probleem `azureml-dataprep` wilt oplossen, u upgraden naar een versie hoger dan 1.1.26 of downgraden naar een versie die lager is dan 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() kreeg een onverwacht trefwoordargument 'invocation_id'

Deze fout treedt op als u `azureml-core` `azureml-dataprep`een onverenigbare versie hebt tussen en . Als u deze fout `azureml-dataprep` ziet, upgradet u het pakket naar een nieuwere versie (groter dan of gelijk aan 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Databricks en Azure Machine Learning-problemen.

### <a name="failure-when-installing-packages"></a>Fout bij het installeren van pakketten

Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, `psutil`zoals , kunnen conflicten veroorzaken. Als u installatiefouten wilt voorkomen, installeert u pakketten door de bibliotheekversie te bevriezen. Dit probleem is gerelateerd aan Databricks en niet aan de Azure Machine Learning SDK. Mogelijk u dit probleem ook met andere bibliotheken ervaren. Voorbeeld:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

U ook init-scripts gebruiken als u te maken blijft krijgen met installatieproblemen met Python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie [Init-scripts met clusterscoped voor](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)meer informatie .

### <a name="cancel-an-automated-machine-learning-run"></a>Een geautomatiseerde machine learning-run annuleren

Wanneer u geautomatiseerde machine learning-mogelijkheden op Azure Databricks gebruikt om een run te annuleren en een nieuw experiment te starten, start u uw Azure Databricks-cluster opnieuw.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iteraties voor geautomatiseerde machine learning

Als u in geautomatiseerde machine learning-instellingen meer `show_output` dan `False` 10 iteraties hebt, stelt u in op wanneer u de run indient.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget voor de Azure Machine Learning SDK en geautomatiseerde machine learning

De AZURE Machine Learning SDK-widget wordt niet ondersteund in een Databricks-notitieblok omdat de notitieblokken geen HTML-widgets kunnen ontleden. U de widget in de portal bekijken met deze Python-code in uw Azure Databricks-notitieblokcel:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Importfout: mag naam 'Timedelta' niet importeren van 'panda's._libs.tslibs'

Als u deze fout ziet wanneer u geautomatiseerde machine learning gebruikt, voert u de twee volgende regels uit in uw notitieblok:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Importfout: Geen module met de naam 'pandas.core.indexes'

Als u deze fout ziet wanneer u geautomatiseerde machine learning gebruikt:

1. Voer deze opdracht uit om twee pakketten in uw Azure Databricks-cluster te installeren:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Maak het cluster los en bevestig het opnieuw aan uw notitieblok.

Als deze stappen het probleem niet oplossen, probeert u het cluster opnieuw op te starten.

### <a name="failtosendfeather"></a>FailToSendFeather FailToSendFeather FailToSendFeather FailTo

Als u `FailToSendFeather` een fout ziet bij het lezen van gegevens in het Azure Databricks-cluster, raadpleegt u de volgende oplossingen:

* Upgradepakket `azureml-sdk[automl]` naar de nieuwste versie.
* Voeg `azureml-dataprep` versie 1.1.8 of hoger toe.
* Voeg `pyarrow` versie 0.11 of hoger toe.

## <a name="azure-portal"></a>Azure Portal

Als u rechtstreeks uw werkruimte bekijkt via een koppeling voor delen van de SDK of de portal, u de normale overzichtspagina met abonnementsgegevens in de extensie niet bekijken. U ook niet overschakelen naar een andere werkruimte. Als u een andere werkruimte moet weergeven, moet u rechtstreeks naar [Azure Machine Learning-studio](https://ml.azure.com) gaan en naar de naam van de werkruimte zoeken.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Soms kan het handig zijn als u diagnostische informatie verstrekken wanneer u om hulp vraagt. Als u enkele logboeken wilt bekijken, gaat u naar [Azure Machine Learning-studio](https://ml.azure.com) en gaat u naar uw werkruimte en selecteert u **Werkruimte > Experiment > Uitvoeren > logboeken**.  

> [!NOTE]
> Azure Machine Learning registreert informatie uit verschillende bronnen tijdens de training, zoals AutoML of de Docker-container die de trainingstaak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen deze logboeken mogelijk worden gebruikt tijdens het oplossen van problemen.

## <a name="activity-logs"></a>Activiteitenlogboeken

Sommige acties in de Azure Machine Learning-werkruimte registreren geen gegevens in het __logboek Activiteit__. Bijvoorbeeld het starten van een training run of het registreren van een model.

Sommige van deze acties worden weergegeven in het gebied __Activiteiten__ van uw werkruimte, maar ze geven niet aan wie de activiteit heeft geïnitieerd.

## <a name="resource-quotas"></a>Resourcequota

Meer informatie over de [resourcequota](how-to-manage-quotas.md) die u tegenkomen wanneer u met Azure Machine Learning werkt.

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheerbewerking uitvoert op een rekendoel vanuit een externe taak, ontvangt u een van de volgende fouten:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout als u een compute-doel probeert te maken of koppelen aan een ML-pijplijn die is ingediend voor uitvoering op afstand.

## <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als u een `Unable to upload project files to working directory in AzureFile because the storage is overloaded`fout ontvangt, past u de volgende tijdelijke oplossingen toe.

Als u bestandsshare gebruikt voor andere workloads, zoals gegevensoverdracht, is de aanbeveling om blobs te gebruiken, zodat bestandsshare gratis kan worden gebruikt voor het verzenden van uitvoeringen. U de werkbelasting ook verdelen over twee verschillende werkruimten.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in Azure Kubernetes Service-fouten

Veel webservicefouten in Azure Kubernetes Service kunnen worden gedebugged door verbinding te maken met het cluster met behulp van `kubectl`. U `kubeconfig.json` het voor een Azure Kubernetes Service Cluster krijgen door

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning-componenten bijwerken in AKS-cluster

Updates voor Azure Machine Learning-componenten die zijn geïnstalleerd in een Azure Kubernetes Service-cluster, moeten handmatig worden toegepast. 

U deze updates toepassen door het cluster los te koppelen van de Azure Machine Learning-werkruimte en het cluster vervolgens opnieuw aan de werkruimte te koppelen. Als SSL is ingeschakeld in het cluster, moet u het SSL-certificaat en de privésleutel leveren wanneer u het cluster opnieuw koppelt. 

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

Als u het SSL-certificaat en de privésleutel niet meer hebt of als u een certificaat gebruikt dat is gegenereerd `kubectl` door Azure Machine `azuremlfessl`Learning, u de bestanden ophalen voordat u het cluster loskoppelt door verbinding te maken met het cluster met behulp van en het geheime op te halen.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in het gecodeerde formaat base-64. U moet basis-64 decoderen `cert.pem` `key.pem` van de en onderdelen van `attach_config.enable_ssl`de geheimen voorafgaand aan het verstrekken van hen aan . 

## <a name="labeling-projects-issues"></a>Problemen met projecten labelen

Bekende problemen met etiketteringsprojecten.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Alleen gegevenssets die zijn gemaakt op blob-gegevensbestanden kunnen worden gebruikt

Dit is een bekende beperking van de huidige release.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Na de creatie toont het project lange tijd "Initialiseren"

De pagina handmatig vernieuwen. Initialisatie moet plaatsvinden op ongeveer 20 datapunten per seconde. Het ontbreken van autorefresh is een bekend probleem. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Bij het bekijken van afbeeldingen worden nieuw gelabelde afbeeldingen niet weergegeven

Als u alle gelabelde afbeeldingen wilt laden, kiest u de knop **Eerste.** De **eerste** knop brengt u terug naar de voorkant van de lijst, maar laadt alle gelabelde gegevens.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Als u op de Esc-toets drukt terwijl u labelt voor objectdetectie, wordt een nulformaatlabel in de linkerbovenhoek aangebracht. Het indienen van labels in deze status mislukt.

Verwijder het label door op het kruisteken ernaast te klikken.

## <a name="moving-the-workspace"></a>De werkruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.
