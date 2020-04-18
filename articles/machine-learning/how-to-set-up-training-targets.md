---
title: Compute targets gebruiken voor modeltraining
titleSuffix: Azure Machine Learning
description: Configureer de trainingsomgevingen (rekendoelen) voor machine learning-modeltraining. U eenvoudig schakelen tussen trainingsomgevingen. Begin lokaal met trainen. Als u wilt uitschalen, schakelt u over naar een op de cloud gebaseerd compute-doel.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 625efcce7305cd7b1dd415a286e6b1e92682cc0a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616849"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Rekendoelen instellen en gebruiken voor modeltraining 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met Azure Machine Learning u uw model trainen op verschillende resources of omgevingen, gezamenlijk [__compute targets__](concept-azure-machine-learning-architecture.md#compute-targets)genoemd. Een rekendoel kan een lokale computer of een cloudresource zijn, zoals een Azure Machine Learning Compute, Azure HDInsight of een externe virtuele machine.  U ook rekendoelen maken voor modelimplementatie zoals beschreven in ['Waar en hoe u uw modellen implementeert'.](how-to-deploy-and-where.md)

U een compute target maken en beheren met de Azure Machine Learning SDK, Azure Machine Learning studio, Azure CLI of Azure Machine Learning VS Code extensie. Als u rekendoelen hebt die zijn gemaakt via een andere service (bijvoorbeeld een HDInsight-cluster), u deze gebruiken door ze aan uw Azure Machine Learning-werkruimte te koppelen.
 
In dit artikel leert u hoe u verschillende rekendoelen gebruiken voor modeltraining.  De stappen voor alle compute targets volgen dezelfde workflow:
1. __Maak__ een compute target als je er nog geen hebt.
2. __Voeg__ het rekendoel toe aan uw werkruimte.
3. __Configureer__ het rekendoel zodat het de Python-omgeving en pakketafhankelijkheden bevat die nodig zijn voor uw script.


>[!NOTE]
> Code in dit artikel is getest met Azure Machine Learning SDK versie 1.0.74.

## <a name="compute-targets-for-training"></a>Rekendoelen voor training

Azure Machine Learning heeft verschillende ondersteuning voor verschillende rekendoelen. Een typische levenscyclus van modelontwikkeling begint met dev/experimenten op een kleine hoeveelheid gegevens. In dit stadium raden we aan om een lokale omgeving te gebruiken. Bijvoorbeeld uw lokale computer of een vm in de cloud. Terwijl u uw training opschaalt op grotere gegevenssets of gedistribueerde training uitvoert, raden we u aan Azure Machine Learning Compute te gebruiken om een cluster met één of meerdere clients te maken dat automatisch wordt geschaald telkens wanneer u een run verzendt. U ook uw eigen rekenbron toevoegen, hoewel de ondersteuning voor verschillende scenario's kan variëren zoals hieronder wordt beschreven:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning Compute kan worden gemaakt als een permanente bron of dynamisch worden gemaakt wanneer u een run aanvraagt. Run-based creation verwijdert het rekendoel nadat de trainingsrun is voltooid, zodat u compute targets die op deze manier zijn gemaakt niet opnieuw gebruiken.

## <a name="whats-a-run-configuration"></a>Wat is een run configuratie?

Bij het trainen is het gebruikelijk om op uw lokale computer te starten en later dat trainingsscript uit te voeren op een ander rekendoel. Met Azure Machine Learning u uw script uitvoeren op verschillende compute targets zonder dat u uw script hoeft te wijzigen.

Het enige wat u hoeft te doen is de omgeving definiëren voor elk compute-doel binnen een **run-configuratie.**  Wanneer u vervolgens uw trainingsexperiment op een ander rekendoel wilt uitvoeren, geeft u de runconfiguratie voor die berekening op. Zie [Omgevingen maken en beheren voor training en implementatie voor](how-to-use-environments.md)meer informatie over het opgeven van een omgeving en deze binden om configuratie uit te voeren.

Meer informatie over [het indienen van experimenten](#submit) aan het einde van dit artikel.

## <a name="whats-an-estimator"></a>Wat is een schatter?

Om modeltraining met populaire frameworks te vergemakkelijken, biedt de Azure Machine Learning Python SDK een alternatieve abstractie op een hoger niveau, de schatterklasse.  Met deze klasse u eenvoudig hardloopconfiguraties samenstellen. U een generieke [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainingsscripts in te dienen die gebruikmaken van elk leerkader dat u kiest (zoals scikit-learn). We raden u aan een schatter te gebruiken voor training, omdat deze automatisch ingesloten objecten zoals een omgeving of RunConfiguration-objecten voor u construeert. Als u meer controle wilt hebben over hoe deze objecten worden gemaakt en wilt opgeven welke pakketten u wilt installeren voor uw experiment, voert u [deze stappen](#amlcompute) uit om uw trainingsexperimenten in te dienen met een runConfiguration-object op een Azure Machine Learning Compute.

Voor PyTorch-, TensorFlow- en Chainer-taken biedt Azure Machine Learning ook respectievelijk [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer-schatters](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) om het gebruik van deze frameworks te vereenvoudigen.

Zie [Train ML-modellen met schatters voor](how-to-train-ml-models.md)meer informatie.

## <a name="whats-an-ml-pipeline"></a>Wat is een ML Pipeline?

Met ML-pijplijnen u uw workflow optimaliseren met eenvoud, snelheid, draagbaarheid en hergebruik. Bij het bouwen van pijplijnen met Azure Machine Learning u zich richten op uw expertise, machine learning, in plaats van op infrastructuur en automatisering.

ML-pijplijnen zijn opgebouwd uit meerdere **stappen,** die verschillende computationele eenheden in de pijplijn zijn. Elke stap kan onafhankelijk worden uitgevoerd en geïsoleerde rekenbronnen gebruiken. Hierdoor kunnen meerdere gegevenswetenschappers tegelijkertijd aan dezelfde pijplijn werken zonder rekenresources te overbelasten, en maakt het ook gemakkelijk om verschillende rekentypen/-formaten voor elke stap te gebruiken.

> [!TIP]
> ML-pijplijnen kunnen runconfiguratie of schatters gebruiken bij trainingsmodellen.

Hoewel ML-pijplijnen modellen kunnen trainen, kunnen ze ook gegevens voorbereiden voordat ze worden getraind en modellen implementeren na de training. Een van de primaire gebruiksvoorbeelden voor pijplijnen is batchscore. Zie [Pipelines: Machine learning-workflows optimaliseren](concept-ml-pipelines.md)voor meer informatie.

## <a name="set-up-in-python"></a>Instellen in Python

Gebruik de onderstaande secties om deze rekendoelen te configureren:

* [Lokale computer](#local)
* [Azure Machine Learning Compute](#amlcompute)
* [Externe virtuele machines](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Lokale computer

1. **Maken en koppelen:** het is niet nodig om een rekendoel te maken of toe te voegen om uw lokale computer als trainingsomgeving te gebruiken.  

1. **Configureren:** Wanneer u uw lokale computer als rekendoel gebruikt, wordt de trainingscode uitgevoerd in uw [ontwikkelomgeving.](how-to-configure-environment.md)  Als die omgeving al de Python-pakketten heeft die u nodig hebt, gebruikt u de door de gebruiker beheerde omgeving.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu u de rekenkracht hebt gekoppeld en uw run hebt geconfigureerd, is de volgende stap het [indienen van de trainingsrun.](#submit)

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Azure Machine Learning Compute

Azure Machine Learning Compute is een beheerde compute-infrastructuur waarmee de gebruiker eenvoudig één of meerdere gegevensberekenen kan maken. De compute wordt gemaakt in uw werkruimtegebied als een bron die kan worden gedeeld met andere gebruikers in uw werkruimte. De rekenkracht wordt automatisch geschaald wanneer een taak wordt ingediend en kan in een Azure Virtual Network worden geplaatst. De compute wordt uitgevoerd in een containeromgeving en verpakt uw modelafhankelijkheden in een [Docker-container.](https://www.docker.com/why-docker)

U Azure Machine Learning Compute gebruiken om het trainingsproces te distribueren over een cluster van CPU- of GPU-computenodes in de cloud. Zie [GPU-geoptimaliseerde virtuele machineformaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)voor meer informatie over de VM-formaten met GPU's.

Azure Machine Learning Compute heeft standaardlimieten, zoals het aantal cores dat kan worden toegewezen. Zie [Quota beheren en aanvragen voor Azure-resources voor](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)meer informatie.


U een Azure Machine Learning-compute-omgeving op aanvraag maken wanneer u een run plant of als een permanente bron.

#### <a name="run-based-creation"></a>Run-based creatie

U Azure Machine Learning Compute maken als rekendoel tijdens de oplage. De rekenkracht wordt automatisch gemaakt voor uw run. De compute wordt automatisch verwijderd zodra de run is voltooid. 

> [!IMPORTANT]
> Run-based creatie van Azure Machine Learning compute is momenteel in Preview. Gebruik geen run-based creatie als u geautomatiseerde hyperparameter tuning of geautomatiseerde machine learning gebruikt. Als u hyperparametertuning of geautomatiseerde machine learning wilt gebruiken, maakt u in plaats daarvan een [blijvend rekendoel.](#persistent)

1.  **Maak, voeg toe en configureer:** De run-based creatie voert alle noodzakelijke stappen uit om het rekendoel te maken, toe te voegen en te configureren met de run-configuratie.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nu u de rekenkracht hebt gekoppeld en uw run hebt geconfigureerd, is de volgende stap het [indienen van de trainingsrun.](#submit)

#### <a name="persistent-compute"></a><a id="persistent"></a>Permanente gegevensberekening

Een permanente Azure Machine Learning Compute kan worden hergebruikt voor taken. De compute kan worden gedeeld met andere gebruikers in de werkruimte en wordt tussen taken bewaard.

1. **Maken en koppelen:** Als u een permanente Azure Machine Learning Compute-bron in Python wilt maken, geeft u de **vm_size** en **max_nodes** eigenschappen op. Azure Machine Learning gebruikt vervolgens slimme standaardinstellingen voor de andere eigenschappen. De compute-automatische schalen tot nul knooppunten wanneer deze niet worden gebruikt.   Er worden speciale VM's gemaakt om uw taken uit te voeren als dat nodig is.
    
    * **vm_size:** de VM-familie van de knooppunten die zijn gemaakt door Azure Machine Learning Compute.
    * **max_nodes:** het maximum aantal knooppunten dat u automatisch wilt opschalen wanneer u een taak uitvoert op Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   U ook verschillende geavanceerde eigenschappen configureren wanneer u Azure Machine Learning Compute maakt. Met de eigenschappen u een permanent cluster van vaste grootte maken of binnen een bestaand Azure Virtual Network in uw abonnement.  Zie de [klasse AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) voor meer informatie.
    
   U ook een permanente Azure Machine Learning Compute-bron maken en koppelen in [Azure Machine Learning-studio.](#portal-create)

1. **Configureren:** Een run-configuratie maken voor het permanente compute-doel.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu u de rekenkracht hebt gekoppeld en uw run hebt geconfigureerd, is de volgende stap het [indienen van de trainingsrun.](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Externe virtuele machines

Azure Machine Learning ondersteunt ook het meenemen van uw eigen compute resource en het koppelen ervan aan uw werkruimte. Een dergelijk resourcetype is een willekeurige externe VM, zolang deze toegankelijk is vanuit Azure Machine Learning. De bron kan een Azure VM zijn, een externe server in uw organisatie of on-premises. Gezien het IP-adres en de referenties (gebruikersnaam en wachtwoord of SSH-sleutel) u met name elke toegankelijke VM gebruiken voor externe uitvoeringen.

U een systeem-gebouwde conda-omgeving, een reeds bestaande Python-omgeving of een Docker-container gebruiken. Als u wilt uitvoeren op een Docker-container, moet u een Docker-engine hebben die op de VM draait. Deze functionaliteit is vooral handig wanneer u een flexibelere, cloudgebaseerde dev/experimenteeromgeving wilt dan uw lokale machine.

Gebruik de Azure Data Science Virtual Machine (DSVM) als de Azure VM bij uitstek voor dit scenario. Deze VM is een vooraf geconfigureerde data science en AI-ontwikkelomgeving in Azure. De VM biedt een samengestelde keuze aan tools en frameworks voor de ontwikkeling van machine learning tijdens de volledige levenscyclus. Zie [Een ontwikkelomgeving configureren](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)voor meer informatie over het gebruik van de DSVM met Azure Machine Learning.

1. **Maken:** Maak een DSVM voordat u deze gebruikt om uw model te trainen. Zie De Virtuele [Machine Data Science voor Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)inrichten om deze bron te maken.

    > [!WARNING]
    > Azure Machine Learning ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd. Wanneer u een vm maakt of een bestaande vm kiest, moet u een vm selecteren die Ubuntu gebruikt.

1. **Bijvoegen:** Als u een bestaande virtuele machine als rekendoel wilt koppelen, moet u de bron-id, gebruikersnaam en wachtwoord voor de virtuele machine opgeven. De resource-id van de vm kan worden opgebouwd met de abonnements-ID, de naam van de resourcegroep en de naam VM met de volgende tekenreeksindeling:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Of u de DSVM aan uw werkruimte koppelen [met Azure Machine Learning-studio.](#portal-reuse)

1. **Configureren:** Een runconfiguratie maken voor het DSVM-rekendoel. Docker en conda worden gebruikt om de trainingsomgeving op de DSVM te maken en te configureren.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu u de rekenkracht hebt gekoppeld en uw run hebt geconfigureerd, is de volgende stap het [indienen van de trainingsrun.](#submit)

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight is een populair platform voor big-data analytics. Het platform biedt Apache Spark, die kan worden gebruikt om uw model te trainen.

1. **Maken:** Maak het HDInsight-cluster voordat u het gebruikt om uw model te trainen. Zie [Een sparkcluster maken in HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)als u een Spark-cluster op HDInsight wilt maken. 

    Wanneer u het cluster maakt, moet u een SSH-gebruikersnaam en -wachtwoord opgeven. Let op deze waarden, omdat je ze nodig hebt om HDInsight te gebruiken als een compute target.
    
    Nadat het cluster is gemaakt, maakt \<u verbinding met de \<clusternaam hostnaam>-ssh.azurehdinsight.net, waarbij clusternaam> de naam is die u voor het cluster hebt opgegeven. 

1. **Bijvoegen:** Als u een HDInsight-cluster als rekendoel wilt koppelen, moet u de bron-id, gebruikersnaam en wachtwoord opgeven voor het HDInsight-cluster. De resource-id van het HDInsight-cluster kan worden geconstrueerd met de abonnements-ID, de naam van de brongroep en de clusternaam HDInsight met behulp van de volgende tekenreeksindeling:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Of u het HDInsight-cluster aan uw werkruimte koppelen [met Azure Machine Learning-studio](#portal-reuse).

1. **Configureren:** Een runconfiguratie maken voor het HDI-compute-doel. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu u de rekenkracht hebt gekoppeld en uw run hebt geconfigureerd, is de volgende stap het [indienen van de trainingsrun.](#submit)


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch wordt gebruikt om hpc-toepassingen (large-scale parallel en high-performance computing) efficiënt uit te voeren in de cloud. AzureBatchStep kan worden gebruikt in een Azure Machine Learning-pijplijn om taken in te dienen bij een Azure Batch-groep machines.

Als u Azure Batch als rekendoel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie verstrekken:

-    **Azure Batch compute name:** een vriendelijke naam die moet worden gebruikt voor de compute binnen de werkruimte
-    **Azure Batch-accountnaam:** de naam van het Azure Batch-account
-    **Resourcegroep:** de resourcegroep die het Azure Batch-account bevat.

In de volgende code wordt uitgelegd hoe u Azure Batch als rekendoel koppelen:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Instellen in Azure Machine Learning-studio

U hebt toegang tot de rekendoelen die zijn gekoppeld aan uw werkruimte in de Azure Machine Learning-studio.  U de studio gebruiken om:

* [Compute targets weergeven](#portal-view) die aan uw werkruimte zijn gekoppeld
* [Een rekendoel maken](#portal-create) in uw werkruimte
* [Een rekendoel toevoegen](#portal-reuse) dat buiten de werkruimte is gemaakt


Nadat een doel is gemaakt en aan uw werkruimte is gekoppeld, `ComputeTarget` gebruikt u het doel in uw runconfiguratie met een object: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Compute-doelen weergeven


Als u de rekendoelen voor uw werkruimte wilt bekijken, gebruikt u de volgende stappen:

1. Navigeer naar [Azure Machine Learning-studio](https://ml.azure.com).
 
1. Selecteer __Onder Toepassingen__de optie __Berekenen__.

    [![Tabblad Berekenen weergeven](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Een rekendoel maken

Volg de vorige stappen om de lijst met rekendoelen weer te geven. Gebruik vervolgens de volgende stappen om een rekendoel te maken: 

1. Selecteer het plusteken (+) om een rekendoel toe te voegen.

    ![Een rekendoel toevoegen](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Voer een naam in voor het rekendoel. 

1. Selecteer **Machine Learning Compute** als het type rekenkracht dat u wilt gebruiken voor __training.__ 

    >[!NOTE]
    >Azure Machine Learning Compute is de enige beheerde rekenbron die u maken in Azure Machine Learning-studio.  Alle andere compute resources kunnen worden gekoppeld nadat ze zijn gemaakt.

1. Vul het formulier in. Geef waarden op voor de vereiste eigenschappen, met name **VM-reeksen,** en de **maximale knooppunten** die moeten worden gebruikt om de rekenkracht op te draaien.  

1. Selecteer __Maken__.


1. Bekijk de status van de maakbewerking door het rekendoel in de lijst te selecteren:

    ![Selecteer een rekendoel om de bewerkingsstatus van maken weer te geven](./media/how-to-set-up-training-targets/View_list.png)

1. Vervolgens ziet u de details voor het rekendoel: 

    ![De doelgegevens van de computer weergeven](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Compute-doelen koppelen

Als u rekendoelen wilt gebruiken die buiten de Azure Machine Learning-werkruimte zijn gemaakt, moet u deze toevoegen. Als u een rekendoel koppelt, is deze beschikbaar voor uw werkruimte.

Volg de eerder beschreven stappen om de lijst met rekendoelen weer te geven. Gebruik vervolgens de volgende stappen om een rekendoel toe te voegen: 

1. Selecteer het plusteken (+) om een rekendoel toe te voegen. 
1. Voer een naam in voor het rekendoel. 
1. Selecteer het type rekenkracht dat u wilt koppelen voor __training:__

    > [!IMPORTANT]
    > Niet alle rekentypen kunnen worden gekoppeld vanuit Azure Machine Learning studio. De rekentypen die momenteel voor training kunnen worden gekoppeld, zijn onder andere:
    >
    > * Een externe VM
    > * Azure Databricks (voor gebruik in machine learning-pijplijnen)
    > * Azure Data Lake Analytics (voor gebruik in machine learning-pijplijnen)
    > * Azure HDInsight

1. Vul het formulier in en geef waarden op voor de vereiste eigenschappen.

    > [!NOTE]
    > Microsoft raadt u aan SSH-sleutels te gebruiken, die veiliger zijn dan wachtwoorden. Wachtwoorden zijn kwetsbaar voor brute force-aanvallen. SSH-sleutels zijn afhankelijk van cryptografische handtekeningen. Zie de volgende documenten voor informatie over het maken van SSH-sleutels voor gebruik met Azure Virtual Machines:
    >
    > * [SSH-toetsen maken en gebruiken op Linux of macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [SSH-toetsen maken en gebruiken in Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecteer __Bijvoegen__. 
1. Bekijk de status van de bijvoegbewerking door het rekendoel in de lijst te selecteren.

## <a name="set-up-with-cli"></a>Instellen met CLI

U hebt toegang tot de rekendoelen die aan uw werkruimte zijn gekoppeld met de [CLI-extensie](reference-azure-machine-learning-cli.md) voor Azure Machine Learning.  U de CLI gebruiken om:

* Een beheerd rekendoel maken
* Een beheerd rekendoel bijwerken
* Een onbeheerd rekendoel koppelen

Zie [Resourcebeheer](reference-azure-machine-learning-cli.md#resource-management)voor meer informatie .

## <a name="set-up-with-vs-code"></a>Instellen met VS-code

U de rekendoelen die aan uw werkruimte zijn gekoppeld, openen, maken en beheren met behulp van de [VS-code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) voor Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Trainingsuitvoeren verzenden met Azure Machine Learning SDK

Nadat u een run-configuratie hebt gemaakt, gebruikt u deze om uw experiment uit te voeren.  Het codepatroon voor het indienen van een trainingsrun is hetzelfde voor alle typen rekendoelen:

1. Een experiment maken om uit te voeren
1. Dien de run in.
1. Wacht tot de run is voltooid.

> [!IMPORTANT]
> Wanneer u de trainingsrun indient, wordt een momentopname van de map met uw trainingsscripts gemaakt en naar het rekendoel verzonden. Het wordt ook opgeslagen als onderdeel van het experiment in uw werkruimte. Als u bestanden wijzigt en de run opnieuw verzendt, worden alleen de gewijzigde bestanden geüpload.
>
> Als u wilt voorkomen dat bestanden in de momentopname `.amlignore` worden opgenomen, maakt u een [.gitignore](https://git-scm.com/docs/gitignore) of bestand in de map en voegt u de bestanden eraan toe. Het `.amlignore` bestand gebruikt dezelfde syntaxis en patronen als het [.gitignore-bestand.](https://git-scm.com/docs/gitignore) Als beide bestanden `.amlignore` bestaan, heeft het bestand voorrang.
> 
> Zie [Momentopnamen voor](concept-azure-machine-learning-architecture.md#snapshots)meer informatie.

### <a name="create-an-experiment"></a>Een experiment maken

Maak eerst een experiment in uw werkruimte.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Het experiment verzenden

Stuur het experiment `ScriptRunConfig` met een object in.  Dit object omvat de:

* **source_directory**: De bronmap die uw trainingsscript bevat
* **script**: Het trainingsscript identificeren
* **run_config**: De runconfiguratie, die op zijn beurt bepaalt waar de training zal plaatsvinden.

Bijvoorbeeld om [de lokale doelconfiguratie](#local) te gebruiken:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Schakel hetzelfde experiment om uit te voeren in een ander rekendoel met behulp van een andere run configuratie, zoals de [amlcompute doel:](#amlcompute)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> In dit voorbeeld wordt standaard slechts één knooppunt van het rekendoel gebruikt voor training. Als u meer dan één `node_count` knooppunt wilt gebruiken, stelt u de runconfiguratie in op het gewenste aantal knooppunten. In de volgende code wordt bijvoorbeeld het aantal knooppunten dat voor training wordt gebruikt, ingesteld op vier:
>
> ```python
> src.run_config.node_count = 4
> ```

Of u kunt:

* Stuur het experiment `Estimator` met een object zoals weergegeven in [Train ML-modellen met schatters](how-to-train-ml-models.md).
* Een HyperDrive-run verzenden voor [hyperparametertuning](how-to-tune-hyperparameters.md).
* Dien een experiment in via de [VS Code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Zie de documentatie [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) en [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) voor meer informatie.

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Run-configuratie maken en uitvoeren met Azure Machine Learning CLI

U [azure CLI-](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) en [Machine Learning CLI-extensie](reference-azure-machine-learning-cli.md) gebruiken om runconfiguraties te maken en deze op verschillende rekendoelen in te dienen. In de volgende voorbeelden wordt ervan uitgegaan dat u een bestaande `az login` Azure Machine Learning Workspace hebt en dat u zich met de opdracht CLI hebt aangemeld bij Azure. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Run-configuratie maken

De eenvoudigste manier om run-configuratie te maken, is door de map met uw Python-scripts voor machine learning te navigeren en de opdracht CLI te gebruiken

```azurecli
az ml folder attach
```

Met deze opdracht `.azureml` wordt een submap gemaakt die sjabloonconfiguratiebestanden bevat voor verschillende rekendoelen. U deze bestanden kopiëren en bewerken om uw configuratie aan te passen, bijvoorbeeld om Python-pakketten toe te voegen of docker-instellingen te wijzigen.  

### <a name="structure-of-run-configuration-file"></a>Structuur van het uitloopconfiguratiebestand

Het run-configuratiebestand is YAML-opgemaakt, met volgende secties
 * Het script dat moet worden uitgevoerd en de argumenten
 * Bereken de doelnaam, 'lokaal' of de naam van een rekenkracht onder de werkruimte.
 * Parameters voor het uitvoeren van de run: framework, communicator voor gedistribueerde uitvoeringen, maximale duur en aantal compute nodes.
 * Milieusectie. Zie [Omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md) voor meer informatie over de velden in deze sectie.
   * Als u Python-pakketten wilt opgeven die moeten worden geïnstalleerd voor de run, maakt u [het conda-omgevingsbestand](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)en stelt u het veld __condaDependenciesFile__ in.
 * Voer geschiedenisgegevens uit om de map met logboekbestanden op te geven en uitvoerverzameling in of uit te schakelen en geschiedenismomentopnamen uit te voeren.
 * Configuratiedetails die specifiek zijn voor het geselecteerde framework.
 * Gegevensreferentie en gegevensarchiefdetails.
 * Configuratiedetails die specifiek zijn voor Machine Learning Compute voor het maken van een nieuw cluster.

Zie het voorbeeld [JSON-bestand](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) voor een volledig runconfig-schema.

### <a name="create-an-experiment"></a>Een experiment maken

Maak eerst een experiment voor uw uitvoeringen

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Scriptrun

Als u een scriptrun wilt verzenden, voert u een opdracht uit

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive-run

U HyperDrive met Azure CLI gebruiken om parametertuning-runs uit te voeren. Maak eerst een HyperDrive-configuratiebestand in de volgende indeling. Zie [Hyperparameters afstemmen voor uw modelartikel](how-to-tune-hyperparameters.md) voor meer informatie over hyperparametertuningparameters.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Voeg dit bestand toe naast de run-configuratiebestanden. Dien vervolgens een HyperDrive-run in met:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Let op de sectie *argumenten* in runconfig en *parameterruimte* in HyperDrive config. Ze bevatten de opdrachtregelargumenten die moeten worden doorgegeven aan het trainingsscript. De waarde in runconfig blijft voor elke iteratie gelijk, terwijl het bereik in HyperDrive config wordt overheid. Geef niet hetzelfde argument op in beide bestanden.

Zie de ```az ml``` [referentiedocumentatie](reference-azure-machine-learning-cli.md)voor meer informatie over deze CLI-opdrachten en volledige reeks argumenten.

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git tracking en integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de rungeschiedenis. Zie [Git-integratie voor Azure Machine Learning voor](concept-train-model-git-integration.md)meer informatie.

## <a name="notebook-examples"></a>Voorbeelden van notitieblokken

Bekijk deze notitieblokken voor voorbeelden van training met verschillende rekendoelen:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Train een model](tutorial-train-models-with-aml.md) met behulp van een beheerd rekendoel om een model te trainen.
* Leer hoe u [hyperparameters efficiënt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Zodra u een getraind model hebt, leert u [hoe en waar u modellen implementeren.](how-to-deploy-and-where.md)
* Bekijk de [SDK-referentie van klasse RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Azure Machine Learning gebruiken met Azure Virtual Networks](how-to-enable-virtual-network.md)
