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
ms.date: 03/31/2020
ms.openlocfilehash: 22a24d01f606cd3f76a0de950351feb3d964da54
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478916"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Bekende problemen en het oplossen van problemen met Azure Machine Learning

Met dit artikel u fouten of fouten vinden of corrigeren die u tegenkomen bij het gebruik van Azure Machine Learning.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

Soms kan het handig zijn als u diagnostische informatie verstrekken wanneer u om hulp vraagt. Ga als iemand te werk om een aantal logboeken te bekijken: 
1. Ga naar [Azure Machine Learning-studio](https://ml.azure.com). 
1. Selecteer **Experiment** aan de linkerkant 
1. Selecteer een experiment.
1. Selecteer een run.
1. Selecteer bovenaan **Uitvoer + logboeken**.

> [!NOTE]
> Azure Machine Learning registreert informatie uit verschillende bronnen tijdens de training, zoals AutoML of de Docker-container die de trainingstaak uitvoert. Veel van deze logboeken zijn niet gedocumenteerd. Als u problemen ondervindt en contact opneemt met Microsoft-ondersteuning, kunnen deze logboeken mogelijk worden gebruikt tijdens het oplossen van problemen.


## <a name="resource-quotas"></a>Resourcequota

Meer informatie over de [resourcequota](how-to-manage-quotas.md) die u tegenkomen wanneer u met Azure Machine Learning werkt.

## <a name="installation-and-import"></a>Installatie en import

* **Foutbericht: Kan 'PyYAML' niet verwijderen**

    Azure Machine Learning SDK voor Python: `distutils` PyYAML is een geïnstalleerd project. Daarom kunnen we niet nauwkeurig bepalen welke bestanden ertoe behoren als er een gedeeltelijke verwijderen is. Gebruik het als volgt te gaan met het installeren van de SDK terwijl u deze fout negeert:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Foutbericht: Geen overeenkomende distributie gevonden voor azureml-dataprep-native**

    Anaconda's Python 3.7.4 distributie heeft een bug die azureml-sdk install breekt. Dit probleem wordt besproken in deze [GitHub Issue](https://github.com/ContinuumIO/anaconda-issues/issues/11195)
    
    Dit kan worden opgelost door een nieuwe Conda-omgeving te maken met behulp van deze opdracht:
    ```bash
    conda create -n <env-name> python=3.7.3
    ```
    Dat creëert een Conda-omgeving met Python 3.7.3, die niet het installatieprobleem aanwezig is in 3.7.4.

* **Databricks-storing bij het installeren van pakketten**

    Azure Machine Learning SDK-installatie mislukt op Azure Databricks wanneer er meer pakketten zijn geïnstalleerd. Sommige pakketten, `psutil`zoals , kunnen conflicten veroorzaken. Als u installatiefouten wilt voorkomen, installeert u pakketten door de bibliotheekversie te bevriezen. Dit probleem is gerelateerd aan Databricks en niet aan de Azure Machine Learning SDK. Mogelijk u dit probleem ook met andere bibliotheken ervaren. Voorbeeld:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    U ook init-scripts gebruiken als u te maken blijft krijgen met installatieproblemen met Python-bibliotheken. Deze aanpak wordt niet officieel ondersteund. Zie [Init-scripts met clusterscoped voor](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)meer informatie .

* **Databricks import error: cannot import name 'Timedelta' from 'pandas._libs.tslibs'**: Als u deze fout ziet wanneer u geautomatiseerde machine learning gebruikt, voert u de twee volgende regels in uw notitieblok uit:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Databricks import error: No module named 'pandas.core.indexes'**: Als u deze fout ziet wanneer u geautomatiseerde machine learning gebruikt:

    1. Voer deze opdracht uit om twee pakketten in uw Azure Databricks-cluster te installeren:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Maak het cluster los en bevestig het opnieuw aan uw notitieblok.
    
    Als deze stappen het probleem niet oplossen, probeert u het cluster opnieuw op te starten.

* **Databricks FailToSendFeather:** Als `FailToSendFeather` u een fout ziet bij het lezen van gegevens op het Azure Databricks-cluster, raadpleegt u de volgende oplossingen:
    
        * Upgrade `azureml-sdk[automl]` package to the latest version.
        * Add `azureml-dataprep` version 1.1.8 or above.
        * Add `pyarrow` version 0.11 or above.
        `

## <a name="create-and-manage-workspaces"></a>Werkruimten maken en beheren

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.

* **Azure-portal:** Als u uw werkruimte rechtstreeks vanuit een koppeling voor delen van de SDK of de portal bekijkt, u de normale **overzichtspagina** met abonnementsgegevens in de extensie niet bekijken. U ook niet overschakelen naar een andere werkruimte. Als u een andere werkruimte wilt bekijken, gaat u rechtstreeks naar [Azure Machine Learning-studio](https://ml.azure.com) en zoekt u naar de naam van de werkruimte.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

* **Problemen met het maken van AmlCompute:** er is een zeldzame kans dat sommige gebruikers die hun Azure Machine Learning-werkruimte hebben gemaakt vanaf de Azure-portal vóór de GA-release, mogelijk geen AmlCompute in die werkruimte kunnen maken. U een ondersteuningsverzoek indienen tegen de service of een nieuwe werkruimte maken via de portal of de SDK om uzelf onmiddellijk te deblokkeren.

## <a name="work-with-data"></a>Werken met gegevens

### <a name="overloaded-azurefile-storage"></a>Overbelaste AzureFile-opslag

Als u een `Unable to upload project files to working directory in AzureFile because the storage is overloaded`fout ontvangt, past u de volgende tijdelijke oplossingen toe.

Als u bestandsshare gebruikt voor andere workloads, zoals gegevensoverdracht, is de aanbeveling om blobs te gebruiken, zodat bestandsshare gratis kan worden gebruikt voor het verzenden van uitvoeringen. U de werkbelasting ook verdelen over twee verschillende werkruimten.

### <a name="datasets-and-data-preparation"></a>Gegevenssets en gegevensvoorbereiding

Dit zijn bekende problemen voor Azure Machine Learning-gegevenssets.

*  **TypeError: FileNotFound: geen dergelijk bestand of map:** deze fout treedt op als het bestandspad dat u opgeeft niet is waar het bestand zich bevindt. U moet ervoor zorgen dat de manier waarop u naar het bestand verwijst, overeenkomt met de plaats waar u uw gegevensset op uw gegevensset hebt gemonteerd. Om een deterministische toestand te garanderen, raden we u aan het abstracte pad te gebruiken bij het monteren van een gegevensset op een compute-doel. In de volgende code monteren we bijvoorbeeld de gegevensset onder de `/tmp`hoofdmap van het bestandssysteem van het rekendoel. 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Als u de leidende slash naar voren niet opneemt, '/', moet u `/mnt/batch/.../tmp/dataset` de werkmap vooraf maken, bijvoorbeeld op het rekendoel om aan te geven waar u de gegevensset wilt monteren.

### <a name="data-labeling-projects-issues"></a>Problemen met het labelen van gegevens


|Probleem  |Oplossing  |
|---------|---------|
|Alleen gegevenssets die zijn gemaakt op blob-gegevensbestanden kunnen worden gebruikt     |  dit is een bekende beperking van de huidige release.       |
|Na de creatie toont het project lange tijd "Initialiseren"     | De pagina handmatig vernieuwen. Initialisatie moet plaatsvinden op ongeveer 20 datapunten per seconde. Het ontbreken van autorefresh is een bekend probleem.         |
|Bij het bekijken van afbeeldingen worden nieuw gelabelde afbeeldingen niet weergegeven     |   Als u alle gelabelde afbeeldingen wilt laden, kiest u de knop **Eerste.** De **eerste** knop brengt u terug naar de voorkant van de lijst, maar laadt alle gelabelde gegevens.      |
|Als u op de Esc-toets drukt terwijl u labelt voor objectdetectie, wordt een nulformaatlabel in de linkerbovenhoek aangebracht. Het indienen van labels in deze status mislukt.     |   Verwijder het label door op het kruisteken ernaast te klikken.  |

## <a name="azure-machine-learning-designer-issues"></a>Problemen met Azure Machine Learning-ontwerpers

Bekende problemen met de ontwerper.

* **Lange voorbereidingstijd voor berekenen**: Het kan een paar minuten of zelfs langer zijn wanneer u voor het eerst verbinding maakt met of een rekendoel maakt. 

## <a name="train-models"></a>Modellen trainen

* **ModuleFouten (geen module met de naam)**: Als u modulefouten tegenkomt tijdens het indienen van experimenten in Azure ML, betekent dit dat het trainingsscript verwacht dat een pakket wordt geïnstalleerd, maar niet wordt toegevoegd. Zodra u de pakketnaam hebt opgemaakt, installeert Azure ML het pakket in de omgeving die wordt gebruikt voor uw trainingsrun. 

    Als u [Schatters](concept-azure-machine-learning-architecture.md#estimators) gebruikt om experimenten in te `pip_packages` dienen, u een pakketnaam opgeven via of `conda_packages` parameter in de schatter op basis van de bron waarvan u het pakket wilt installeren. U ook een yml-bestand opgeven `conda_dependencies_file`met al uw afhankelijkheden met `pip_requirements_file` behulp van of een lijst van al uw pip-vereisten in een txt-bestand met behulp van parameter. Als u uw eigen Azure ML-omgevingsobject hebt dat u de standaardafbeelding wilt overschrijven `environment` die door de schatter wordt gebruikt, u die omgeving opgeven via de parameter van de schatterconstructor.

    Azure ML biedt ook kaderspecifieke schatters voor Tensorflow, PyTorch, Chainer en SKLearn. Met behulp van deze schatters zal ervoor zorgen dat de kern kader afhankelijkheden worden geïnstalleerd namens u in de omgeving die wordt gebruikt voor training. U hebt de optie om extra afhankelijkheden op te geven zoals hierboven beschreven. 
 
    Azure ML onderhouden docker afbeeldingen en de inhoud ervan kan worden gezien in [AzureML Containers](https://github.com/Azure/AzureML-Containers).
    Kaderspecifieke afhankelijkheden worden vermeld in de desbetreffende kaderdocumentatie - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

    > [!Note]
    > Als u denkt dat een bepaald pakket vaak genoeg is om te worden toegevoegd in azure ml onderhouden afbeeldingen en omgevingen, u een GitHub-probleem in [AzureML-containers](https://github.com/Azure/AzureML-Containers)opte roepen. 
 
* **NameError (Naam niet gedefinieerd), AttributeError (Object heeft geen attribuut)**: Deze uitzondering moet afkomstig zijn van uw trainingsscripts. U de logboekbestanden van azure-portal bekijken om meer informatie te krijgen over de specifieke naam die niet is gedefinieerd of een kenmerkfout. Vanuit de SDK kunt `run.get_details()` u de foutmelding bekijken. Dit bevat ook een lijst van alle logboekbestanden die voor uw run zijn gegenereerd. Zorg ervoor dat u uw trainingsscript bekijkt en de fout oplost voordat u de run opnieuw indient. 

* **Horovod is stilgelegd**: In de meeste gevallen als je tegenkomt "AbortedError: Horovod is stilgelegd" deze uitzondering betekent dat er een onderliggende uitzondering in een van de processen die horovod veroorzaakt af te sluiten. Elke rang in de MPI-taak krijgt een eigen specifiek logboekbestand in Azure ML. Deze logboeken `70_driver_logs`hebben de naam . In het geval van gedistribueerde training, `_rank` zijn de log namen achtervoegsel met om het gemakkelijker te maken om de logs te onderscheiden. Om de exacte fout die horovod veroorzaakt af te sluiten, `Traceback` ga door alle log bestanden en zoeken aan het einde van de driver_log bestanden. Een van deze bestanden geeft u de werkelijke onderliggende uitzondering. 

* **Verwijdering of verwijdering van het experiment uitvoeren of experimenteren**: Experimenten kunnen worden gearchiveerd met behulp van de methode [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) of via de tabbladweergave Experiment in azure Machine Learning-studioclient via de knop 'Archiefexperiment'. Met deze actie wordt het experiment verborgen in lijstquery's en weergaven, maar wordt het niet verwijderd.

    Permanente verwijdering van individuele experimenten of uitvoeringen wordt momenteel niet ondersteund. Zie Uw [Machine Learning-servicewerkruimtegegevens exporteren of verwijderen](how-to-export-delete-data.md)voor meer informatie over het verwijderen van workspace-elementen van Workspace.

* **Metrische document is te groot:** Azure Machine Learning heeft interne limieten voor de grootte van metrische objecten die in één keer kunnen worden geregistreerd vanaf een trainingsrun. Als u een fout 'Metrisch document is te groot' tegenkomt bij het registreren van een statistiek met een lijstwaarde, probeert u de lijst op te splitsen in kleinere segmenten, bijvoorbeeld:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Intern voegt Azure ML de blokken met dezelfde metrische naam samen in een aaneengesloten lijst.

## <a name="automated-machine-learning"></a>Geautomatiseerde Machine Learning

* **Tensor Flow**: Automated machine learning ondersteunt momenteel geen tensorflowversie 1.13. Als u deze versie installeert, worden pakketafhankelijkheden niet meer werkt. We werken eraan om dit probleem op te lossen in een toekomstige release.

* **Experiment grafieken:** Binaire classificatie grafieken (precisie-recall, ROC, gain curve etc.) weergegeven in geautomatiseerde ML experiment iteraties zijn niet correct rendering in user interface sinds 4/12. Grafiekplots tonen momenteel omgekeerde resultaten, waarbij beter presterende modellen worden weergegeven met lagere resultaten. Een resolutie wordt onderzocht.

* **Databricks annuleert een geautomatiseerde machine learning-run:** wanneer u geautomatiseerde machine learning-mogelijkheden op Azure Databricks gebruikt om een run te annuleren en een nieuw experiment te starten, start u uw Azure Databricks-cluster opnieuw.

* **Databricks >10 iteraties voor geautomatiseerde machine learning:** In geautomatiseerde machine learning-instellingen, als u meer dan 10 iteraties hebt, ingesteld `show_output` op `False` wanneer u de run indient.

* **Databricks-widget voor de Azure Machine Learning SDK en geautomatiseerde machine learning:** de Azure Machine Learning SDK-widget wordt niet ondersteund in een Databricks-notitieblok omdat de notitieblokken geen HTML-widgets kunnen ontleden. U de widget in de portal bekijken met deze Python-code in uw Azure Databricks-notitieblokcel:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```

## <a name="deploy--serve-models"></a>Modellen implementeren & bedienen

Volg de volgende acties voor de volgende fouten:

|Fout  | Oplossing  |
|---------|---------|
|Imagebuilding-fout bij het implementeren van webservice     |  Voeg 'pynacl==1.2.1' toe als pip-afhankelijkheid van Conda-bestand voor afbeeldingsconfiguratie       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Wijzig de SKU voor VM's die in uw implementatie worden gebruikt in een implementatie met meer geheugen. |
|FPGA-storing     |  U geen modellen implementeren op FPGA's totdat u hebt aangevraagd en goedgekeurd voor FPGA-quota. Als u toegang wilt aanvragen, vult u het formulier voor het quotumaanvraagformulier in:https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Azure Machine Learning-componenten bijwerken in AKS-cluster

Updates voor Azure Machine Learning-componenten die zijn geïnstalleerd in een Azure Kubernetes Service-cluster, moeten handmatig worden toegepast. 

U deze updates toepassen door het cluster los te koppelen van de Azure Machine Learning-werkruimte en het cluster vervolgens opnieuw aan de werkruimte te koppelen. Als TLS is ingeschakeld in het cluster, moet u het TLS/SSL-certificaat en de privésleutel leveren bij het opnieuw koppelen van het cluster. 

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

Als u het TLS/SSL-certificaat en de privésleutel niet meer hebt of als u een certificaat gebruikt dat is gegenereerd `kubectl` door Azure Machine `azuremlfessl`Learning, u de bestanden ophalen voordat u het cluster loskoppelt door verbinding te maken met het cluster met behulp van en het geheime op te halen.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes slaat de geheimen op in het gecodeerde formaat base-64. U moet basis-64 decoderen `cert.pem` `key.pem` van de en onderdelen van `attach_config.enable_ssl`de geheimen voorafgaand aan het verstrekken van hen aan . 

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservices in Azure Kubernetes Service-fouten

Veel webservicefouten in Azure Kubernetes Service kunnen worden gedebugged door verbinding te maken met het cluster met behulp van `kubectl`. U `kubeconfig.json` het voor een Azure Kubernetes Service Cluster krijgen door

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Verificatiefouten

Als u een beheerbewerking uitvoert op een rekendoel vanuit een externe taak, ontvangt u een van de volgende fouten:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

U ontvangt bijvoorbeeld een fout als u een compute-doel probeert te maken of koppelen aan een ML-pijplijn die is ingediend voor uitvoering op afstand.
