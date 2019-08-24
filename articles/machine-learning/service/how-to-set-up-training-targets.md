---
title: Maken en gebruiken van compute-doelen voor modeltraining
titleSuffix: Azure Machine Learning service
description: Configureer de trainings omgevingen (Compute-doelen) voor de training van machine learning model. U kunt eenvoudig overschakelen tussen trainings omgevingen. Start training lokaal. Als u wilt uitschalen, gaat u naar een compute-doel op basis van de Cloud.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 06/12/2019
ms.custom: seodec18
ms.openlocfilehash: 7d9d6dc8a980c543aa1a94c0ed4e9ae845769567
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997906"
---
# <a name="set-up-compute-targets-for-model-training"></a>Compute-doelen voor modeltraining instellen 

Met Azure Machine Learning-service kunt u uw model trainen op diverse resources of omgevingen en gezamenlijk ook [__reken doelen__](concept-azure-machine-learning-architecture.md#compute-targets)genoemd. Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.  U kunt ook reken doelen voor model implementatie maken, zoals wordt beschreven in [' waar en hoe u uw modellen implementeert '](how-to-deploy-and-where.md).

U kunt een compute-doel maken en beheren met behulp van de Azure Machine Learning SDK, Azure Portal, Azure CLI of Azure Machine Learning VS code extension. Als u de compute-doelen die zijn gemaakt via een andere service (bijvoorbeeld: een HDInsight-cluster) hebt, kunt u ze kunt gebruiken door ze te koppelen aan uw werkruimte van Azure Machine Learning-service.
 
In dit artikel leert u hoe u verschillende reken doelen kunt gebruiken voor model training.  De stappen voor alle Compute-doelen volgen dezelfde werk stroom:
1. __Maak__ een reken doel als u er nog geen hebt.
2. __Koppel__ het Compute-doel aan uw werk ruimte.
3. __Configureer__ het reken doel zodanig dat het de python-omgeving en pakket afhankelijkheden bevat die nodig zijn voor uw script.


>[!NOTE]
> De code in dit artikel is getest met Azure Machine Learning SDK-versie 1.0.39.

## <a name="compute-targets-for-training"></a>Reken doelen voor training

Azure Machine Learning-service heeft verschillende ondersteuning voor verschillende reken doelen. Een typische model ontwikkelingscyclus begint met dev/experimenten op een kleine hoeveelheid gegevens. In deze fase, wordt u aangeraden een lokale omgeving. Bijvoorbeeld, de lokale computer of een cloud-gebaseerde VM. Als u uw training voor grotere gegevenssets opschalen of gedistribueerde training doen, wordt u aangeraden een één of meerdere node cluster maken dat automatisch wordt geschaald telkens wanneer die u een uitvoering verzenden met Azure Machine Learning-Computing. U kunt ook uw eigen compute-resource koppelen, hoewel ondersteuning voor verschillende scenario's als variëren kunnen hieronder uitgelegd:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning Compute kan worden gemaakt als een permanente resource of dynamisch worden gemaakt wanneer u een uitvoering aanvraagt. Bij het maken van de uitvoering wordt het reken doel verwijderd nadat de training is voltooid, zodat u de reken doelen die op deze manier zijn gemaakt, niet opnieuw kunt gebruiken.

## <a name="whats-a-run-configuration"></a>Wat is een uitvoerings configuratie?

Bij het trainen is het gebruikelijk om te beginnen op de lokale computer en dat trainings script later uit te voeren op een ander reken doel. Met Azure Machine Learning-service kunt u uw script uitvoeren op verschillende reken doelen zonder dat u het script hoeft te wijzigen. 

Alles wat u hoeft te doen, is het definiëren van de omgeving voor elk reken doel met een **uitvoerings configuratie**.  Wanneer u uw trainings experiment wilt uitvoeren op een ander Compute-doel, geeft u de uitvoerings configuratie op voor die reken kracht.

Meer informatie over het [verzenden van experimenten](#submit) aan het einde van dit artikel.

### <a name="manage-environment-and-dependencies"></a>Omgeving en afhankelijkheden beheren

Wanneer u een uitvoerings configuratie maakt, moet u bepalen hoe u de omgeving en afhankelijkheden op het reken doel beheert. 

#### <a name="system-managed-environment"></a>Systeem-beheerde omgeving

Gebruik een door een systeem beheerde omgeving als u wilt dat [Conda](https://conda.io/docs/) de python-omgeving en de script afhankelijkheden voor u beheert. Een door een systeem beheerde omgeving wordt standaard gebruikt en de meest voorkomende keuze. Het is handig voor externe Compute-doelen, met name wanneer u dat doel niet kunt configureren. 

U hoeft alleen maar elke pakket afhankelijkheid op te geven met behulp van de [CondaDependency-klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) . vervolgens maakt Conda een bestand met de naam **conda_dependencies. yml** in de map **aml_config** in uw werk ruimte met de lijst met pakket afhankelijkheden en Hiermee stelt u uw python-omgeving in wanneer u uw trainings experiment verzendt. 

De eerste installatie van een nieuwe omgeving kan enkele minuten duren, afhankelijk van de grootte van de vereiste afhankelijkheden. Zolang de lijst met pakketten ongewijzigd blijft, treedt de instel tijd slechts één keer op.
  
De volgende code toont een voor beeld van een systeem beheerde omgeving waarvoor scikit is vereist:
    
[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_system_managed)]

#### <a name="user-managed-environment"></a>Gebruiker-beheerde omgeving

Voor een door een gebruiker beheerde omgeving bent u verantwoordelijk voor het instellen van uw omgeving en het installeren van elk pakket dat nodig is voor uw trainings script voor het Compute-doel. Als uw trainings omgeving al is geconfigureerd (zoals op uw lokale machine), kunt u de Setup-stap `user_managed_dependencies` overs laan op True. Conda controleert uw omgeving niet en installeert niets voor u.

De volgende code toont een voor beeld van het configureren van trainings uitvoeringen voor een door de gebruiker beheerde omgeving:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/runconfig.py?name=run_user_managed)]

## <a name="whats-an-estimator"></a>Wat is een estimator?

Om model training te vergemakkelijken met behulp van populaire frameworks, biedt de Azure Machine Learning python SDK een alternatieve abstractie op hoger niveau, de Estimator-klasse. Met deze klasse kunt u eenvoudig uitvoerings configuraties bouwen. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings scripts te verzenden die gebruikmaken van een door u gekozen trainings raamwerk (zoals scikit-leren).

Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de geraamde [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

Meer informatie vindt u [in Train ml-modellen met schattingen](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Wat is een ML-pijp lijn?

Met ML-pijp lijnen kunt u uw werk stroom optimaliseren met eenvoud, snelheid, portabiliteit en hergebruik. Wanneer u pijp lijnen met Azure Machine Learning bouwt, kunt u zich richten op uw expertise, machine learning in plaats van de infra structuur en Automation.

ML-pijp lijnen worden uit meerdere **stappen**gemaakt. Dit zijn afzonderlijke reken kundige eenheden in de pijp lijn. Elke stap kan onafhankelijk worden uitgevoerd en geïsoleerde reken bronnen gebruiken. Op deze manier kunnen meerdere gegevens wetenschappers tegelijkertijd op dezelfde pijp lijn werken zonder dat er meer belasting bronnen hoeft te worden gebruikt, en kunt u voor elke stap eenvoudig verschillende reken typen of-grootten gebruiken.

> [!TIP]
> ML-pijp lijnen kunnen gebruikmaken van configuratie of schattingen wanneer trainings modellen worden uitgevoerd.

Hoewel ML-pijp lijnen modellen kunnen trainen, kunnen ze ook gegevens voorbereiden voordat ze na de training worden getraind en geïmplementeerd. Een van de primaire gebruiks cases voor pijp lijnen is batch scores. Zie [pijp lijnen voor meer informatie: Optimaliseer machine learning werk](concept-ml-pipelines.md)stromen.

## <a name="set-up-in-python"></a>Ingesteld in python

Gebruik de volgende secties om deze reken doelen te configureren:

* [Lokale computer](#local)
* [Azure Machine Learning-Computing](#amlcompute)
* [Externe virtuele machines](#vm)
* [Azure HDInsight](#hdinsight)


### <a id="local"></a>Lokale computer

1. **Maken en koppelen**: U hoeft geen Compute-doel te maken of koppelen om uw lokale computer te gebruiken als de trainings omgeving.  

1. **Configureren**:  Wanneer u uw lokale computer als reken doel gebruikt, wordt de trainings code uitgevoerd in uw [ontwikkel omgeving](how-to-configure-environment.md).  Als deze omgeving al de Python-pakketten bevat die u nodig hebt, gebruikt u de door de gebruiker beheerde omgeving.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](#submit).

### <a id="amlcompute"></a>Azure Machine Learning-Computing

Azure Machine Learning Compute is een infra structuur voor beheerde berekeningen waarmee de gebruiker eenvoudig een reken proces met één of meerdere knoop punten kan maken. De berekening wordt binnen uw werkruimte regio gemaakt als een resource die kan worden gedeeld met andere gebruikers in uw werk ruimte. De berekening wordt automatisch omhoog geschaald wanneer een taak wordt verzonden en kan in een Azure-Virtual Network worden geplaatst. De berekening wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een docker- [container](https://www.docker.com/why-docker).

U kunt Azure Machine Learning-Computing gebruiken voor het distribueren van het trainingsproces in een cluster van de CPU of GPU-computerknooppunten in de cloud. Zie [grootten geoptimaliseerd voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)voor meer informatie over de VM-grootten die GPU bevatten.

Azure Machine Learning Compute heeft standaard limieten, zoals het aantal kernen dat kan worden toegewezen. Zie voor meer informatie [beheer en aanvragen van quota's voor Azure-resources](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).


U kunt op aanvraag een Azure Machine Learning Compute-omgeving maken wanneer u een uitvoering plant of als een permanente resource.

#### <a name="run-based-creation"></a>Uitvoeren op basis van het maken

Tijdens runtime kunt u Azure Machine Learning Compute als een reken doel maken. De berekening wordt automatisch gemaakt voor de uitvoering. De berekening wordt automatisch verwijderd zodra de uitvoering is voltooid. 

> [!NOTE]
> Als u het maximum aantal knoop punten wilt opgeven dat u wilt gebruiken, `node_count` stelt u normaal gesp roken het aantal knoop punten in. Er is momenteel (04/04/2019) een bug waarmee wordt voor komen dat dit werkt. Gebruik de `amlcompute._cluster_max_node_count` eigenschap van de uitvoerings configuratie als tijdelijke oplossing. Bijvoorbeeld `run_config.amlcompute._cluster_max_node_count = 5`.

> [!IMPORTANT]
> Het maken van Azure Machine Learning Compute is momenteel beschikbaar als preview-versie. Gebruik niet maken op basis van de uitvoering als u gebruikmaakt van automatische afstemming-afstemming of geautomatiseerde machine learning. Als u afstemming-tuning of geautomatiseerde machine learning wilt gebruiken, maakt u in plaats daarvan een [persistent reken](#persistent) doel.

1.  **Maken, koppelen en configureren**: Met het maken van de uitvoering voert u alle benodigde stappen uit om het reken doel te maken, te koppelen en te configureren met de uitvoerings configuratie.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](#submit).

#### <a id="persistent"></a>Permanente compute

Een permanente Azure Machine Learning Compute kan opnieuw worden gebruikt voor verschillende taken. De compute kan worden gedeeld met andere gebruikers in de werk ruimte en tussen taken worden bewaard.

1. **Maken en koppelen**: Als u een permanente Azure Machine Learning Compute-resource in python wilt maken, geeft u de eigenschappen **vm_size** en **max_nodes** op. Azure Machine Learning gebruikt vervolgens slimme standaard instellingen voor de andere eigenschappen. De berekening wordt automatisch geschaald naar nul knoop punten wanneer deze niet wordt gebruikt.   Er zijn specifieke Vm's gemaakt om uw taken naar behoefte uit te voeren.
    
    * **vm_size**: De VM-familie van de knoop punten die zijn gemaakt door Azure Machine Learning compute.
    * **max_nodes**: Het maximum aantal knoop punten waarmee automatisch kan worden geschaald wanneer u een taak uitvoert op Azure Machine Learning compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   U kunt ook verschillende geavanceerde eigenschappen configureren wanneer u Azure Machine Learning Compute maakt. Met de eigenschappen kunt u een permanent cluster met een vaste grootte of binnen een bestaand Azure-Virtual Network in uw abonnement maken.  Zie de [klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) AmlCompute voor meer informatie.
    
   U kunt ook een permanente Azure Machine Learning Compute-bron maken en koppelen [in de Azure Portal](#portal-create).

1. **Configureren**: Maak een uitvoerings configuratie voor het permanente Compute-doel.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](#submit).


### <a id="vm"></a>Externe virtuele machines

Azure Machine Learning ondersteunt ook uw eigen compute-resource halen en deze te koppelen aan uw werkruimte. Een van deze bron typen is een wille keurige externe virtuele machine, zolang deze toegankelijk is vanaf Azure Machine Learning service. De resource kan een Azure-VM, een externe server in uw organisatie of on-premises zijn. Met name op basis van het IP-adres en de referenties (gebruikers naam en wacht woord of SSH-sleutel) kunt u elke toegankelijke virtuele machine gebruiken voor externe uitvoeringen.

U kunt een systeem gebouwd conda-omgeving, een al bestaande Python-omgeving of een Docker-container. Als u wilt uitvoeren op een docker-container, moet er een docker-engine op de VM worden uitgevoerd. Deze functionaliteit is vooral nuttig als u wilt dat een meer flexibele, cloud-gebaseerde dev/experimentele omgeving dan uw lokale computer.

Gebruik Azure Data Science Virtual Machine (DSVM) als de Azure-VM van de keuze voor dit scenario. Deze VM is een vooraf geconfigureerde data Science-en AI-ontwikkel omgeving in Azure. De virtuele machine biedt een geruime keuze aan hulpprogram ma's en frameworks voor een volledige levenscyclus machine learning ontwikkeling. Zie [Configure a Development Environment](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm)(Engelstalig) voor meer informatie over het gebruik van de DSVM met Azure machine learning.

1. **Maken**: Maak een DSVM voordat u het gebruikt om het model te trainen. Zie [de data Science virtual machine inrichten voor Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)als u deze resource wilt maken.

    > [!WARNING]
    > Azure Machine Learning ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd. Wanneer u een virtuele machine maakt of een bestaande virtuele machine kiest, moet u een virtuele machine selecteren die gebruikmaakt van Ubuntu.

1. Bijvoegen: Als u een bestaande virtuele machine als een reken doel wilt koppelen, moet u de Fully Qualified Domain Name (FQDN), de gebruikers naam en het wacht woord voor de virtuele machine opgeven. Vervang \<in het voor beeld FQDN-> door de open bare FQDN van de virtuele machine of het open bare IP-adres. Vervang \<username > en \<wacht woord > door de gebruikers naam en het wacht woord van de SSH-gebruiker voor de virtuele machine.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
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

   U kunt de DSVM ook aan uw werk ruimte koppelen [met behulp van de Azure Portal](#portal-reuse).

1. **Configureren**: Een uitvoerings configuratie voor het DSVM Compute-doel maken. Docker en Conda worden gebruikt om de trainings omgeving op de DSVM te maken en te configureren.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight is een populair platform voor Big data-analyses. Het platform biedt Apache Spark, dat kan worden gebruikt voor het trainen van uw model.

1. **Maken**:  Maak het HDInsight-cluster voordat u het gebruikt om het model te trainen. Zie [een Spark-cluster maken in hdinsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)voor informatie over het maken van een Spark in hdinsight-cluster. 

    Wanneer u het cluster maakt, moet u een SSH-gebruikers naam en-wacht woord opgeven. Noteer deze waarden, omdat u ze nodig hebt om HDInsight als reken doel te gebruiken.
    
    Nadat het cluster is gemaakt, maakt u een verbinding met de \<hostnaam clustername >-ssh.azurehdinsight.net, waarbij \<clustername > de naam is die u voor het cluster hebt ingesteld. 

1. Bijvoegen: Als u een HDInsight-cluster als een reken doel wilt koppelen, moet u de hostnaam, de gebruikers naam en het wacht woord voor het HDInsight-cluster opgeven. Het volgende voorbeeld wordt de SDK te koppelen van een cluster aan uw werkruimte. Vervang \<in het voor beeld clustername > door de naam van uw cluster. Vervang \<username > en \<wacht woord > door de gebruikers naam en het wacht woord van de SSH voor het cluster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azureinsight.net', 
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

   Of u kunt het HDInsight-cluster aan uw werk ruimte koppelen [met behulp van de Azure Portal](#portal-reuse).

1. **Configureren**: Een uitvoerings configuratie voor het HDI Compute-doel maken. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch wordt gebruikt voor het efficiënt uitvoeren van grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de Cloud. AzureBatchStep kan worden gebruikt in een Azure Machine Learning-pijp lijn om taken naar een Azure Batch groep machines te verzenden.

Als u Azure Batch als een reken doel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie opgeven:

-   **Azure batch Compute-naam**: Een beschrijvende naam die moet worden gebruikt voor de compute in de werk ruimte
-   **Azure batch account naam**: De naam van het Azure Batch-account
-   **Resourcegroep**: De resource groep die het Azure Batch-account bevat.

De volgende code laat zien hoe u Azure Batch als een reken doel koppelt:

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

## <a name="set-up-in-azure-portal"></a>Ingesteld in Azure Portal

U kunt toegang krijgen tot de reken doelen die zijn gekoppeld aan uw werk ruimte in de Azure Portal.  U kunt de portal gebruiken om het volgende te doen:

* [Reken doelen weer geven](#portal-view) die zijn gekoppeld aan uw werk ruimte
* [Een compute-doel](#portal-create) in uw werk ruimte maken
* [Een reken doel toevoegen](#portal-reuse) dat buiten de werk ruimte is gemaakt

Nadat een doel is gemaakt en aan uw werk ruimte is gekoppeld, gebruikt u dit in uw uitvoerings configuratie met `ComputeTarget` een-object: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Reken doelen weer geven


Als u de reken doelen voor uw werk ruimte wilt zien, gebruikt u de volgende stappen:

1. Ga naar het [Azure Portal](https://portal.azure.com) en open uw werk ruimte. 
1. Selecteer __berekenen__onder __toepassingen__.

    [![Tabblad Compute weer geven](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Een reken doel maken

Volg de vorige stappen om de lijst met Compute-doelen weer te geven. Gebruik vervolgens de volgende stappen om een compute-doel te maken: 

1. Selecteer het plus teken (+) om een reken doel toe te voegen.

    ![Een reken doel toevoegen](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Voer een naam in voor het berekenings doel. 

1. Selecteer **machine learning Compute** als het type Compute dat moet worden gebruikt voor de __training__. 

    >[!NOTE]
    >Azure Machine Learning Compute is de enige beheerde-reken resource die u kunt maken in de Azure Portal.  Alle andere reken bronnen kunnen worden bijgevoegd nadat ze zijn gemaakt.

1. Vul het formulier in. Geef waarden op voor de vereiste eigenschappen, met name voor de **VM-familie**, en het **maximum aantal knoop punten** dat moet worden gebruikt om de berekening uit te voeren.  

1. Selecteer __Maken__.


1. Bekijk de status van de maak bewerking door het doel van de berekening te selecteren in de lijst:

    ![Selecteer een compute-doel om de status van de aanmaak bewerking weer te geven](./media/how-to-set-up-training-targets/View_list.png)

1. Vervolgens ziet u de details van het berekenings doel: 

    ![De details van de computer doel weer geven](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Reken doelen koppelen

Als u Compute-doelen wilt gebruiken die buiten de Azure Machine Learning service werkruimte zijn gemaakt, moet u ze koppelen. Als u een reken doel koppelt, wordt het beschikbaar voor uw werk ruimte.

Volg de stappen die eerder zijn beschreven om de lijst met Compute-doelen weer te geven. Gebruik vervolgens de volgende stappen om een reken doel te koppelen: 

1. Selecteer het plus teken (+) om een reken doel toe te voegen. 
1. Voer een naam in voor het berekenings doel. 
1. Selecteer het type berekening dat moet worden gekoppeld voor de __training__:

    > [!IMPORTANT]
    > Niet alle reken typen kunnen worden bijgevoegd vanuit het Azure Portal. De berekenings typen die momenteel aan de training kunnen worden gekoppeld, zijn:
    >
    > * Een externe VM
    > * Azure Databricks (voor gebruik in machine learning pijp lijnen)
    > * Azure Data Lake Analytics (voor gebruik in machine learning pijp lijnen)
    > * Azure HDInsight

1. Vul het formulier in en geef waarden op voor de vereiste eigenschappen.

    > [!NOTE]
    > Micro soft raadt u aan om SSH-sleutels te gebruiken, die veiliger zijn dan wacht woorden. Wacht woorden zijn gevoelig voor beveiligings aanvallen. SSH-sleutels zijn afhankelijk van cryptografische hand tekeningen. Raadpleeg de volgende documenten voor informatie over het maken van SSH-sleutels voor gebruik met Azure Virtual Machines:
    >
    > * [Maken en gebruiken van SSH-sleutels in Linux of macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Maken en gebruiken van SSH-sleutels op Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecteer __koppelen__. 
1. Bekijk de status van de koppelings bewerking door het berekenings doel te selecteren in de lijst.

## <a name="set-up-with-cli"></a>Instellen met CLI

U kunt toegang krijgen tot de reken doelen die aan uw werk ruimte zijn gekoppeld met de [cli-extensie](reference-azure-machine-learning-cli.md) voor Azure machine learning service.  U kunt de CLI gebruiken voor het volgende:

* Een beheerd reken doel maken
* Een beheerd reken doel bijwerken
* Een onbeheerd reken doel koppelen

Zie [resource management](reference-azure-machine-learning-cli.md#resource-management)voor meer informatie.

## <a name="set-up-with-vs-code"></a>Met VS code instellen

U kunt de berekenings doelen die aan uw werk ruimte zijn gekoppeld, openen, maken en beheren met de [VS code-extensie](how-to-vscode-tools.md#create-and-manage-compute-targets) voor Azure machine learning service.

## <a id="submit"></a>Trainings uitvoering verzenden

Nadat u een uitvoerings configuratie hebt gemaakt, kunt u deze gebruiken om uw experiment uit te voeren.  Het code patroon voor het verzenden van een trainings uitvoering is hetzelfde voor alle typen reken doelen:

1. Een experiment maken om uit te voeren
1. De uitvoering verzenden.
1. Wacht totdat de uitvoering om te voltooien.

> [!IMPORTANT]
> Wanneer u de trainings uitvoering verzendt, wordt een moment opname gemaakt van de map die uw trainings scripts bevat en verzonden naar het doel van de berekening. Het wordt ook opgeslagen als onderdeel van het experiment in uw werk ruimte. Als u bestanden wijzigt en de uitvoering opnieuw verzendt, worden alleen de gewijzigde bestanden geüpload.
>
> Als u wilt voor komen dat bestanden worden opgenomen in de moment opname, maakt `.amlignore` u een [. gitignore](https://git-scm.com/docs/gitignore) -of-bestand in de map en voegt u de bestanden hieraan toe. Het `.amlignore` bestand gebruikt dezelfde syntaxis en patronen als het [. gitignore](https://git-scm.com/docs/gitignore) -bestand. Als beide bestanden bestaan, heeft `.amlignore` het bestand voor rang.
> 
> Zie [moment opnamen](concept-azure-machine-learning-architecture.md#snapshots)voor meer informatie.

### <a name="create-an-experiment"></a>Een experiment maken

Maak eerst een experiment in uw werk ruimte.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Het experiment verzenden

Verzend het experiment met een `ScriptRunConfig` object.  Dit object bevat:

* **bronmap**: De bronmap die uw trainings script bevat
* **script**: Het trainings script identificeren
* **run_config**: De uitvoerings configuratie, die op zijn beurt bepaalt waar de training plaatsvindt.

Als u bijvoorbeeld [de lokale doel](#local) configuratie wilt gebruiken:

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Hetzelfde experiment wisselen om uit te voeren in een ander reken doel door gebruik te maken van een andere uitvoerings configuratie, zoals het [amlcompute-doel](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

U kunt ook het volgende doen:

* Dien het experiment in met `Estimator` een object zoals wordt weer gegeven in de [trein ml-modellen met schattingen](how-to-train-ml-models.md).
* Dien een experiment [in met de CLI-extensie](reference-azure-machine-learning-cli.md#experiments).
* Een experiment verzenden via de [VS code-extensie](how-to-vscode-tools.md#train-and-tune-models).

## <a name="git-tracking-and-integration"></a>Git-tracking en-integratie

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. De huidige doorvoer-ID voor de opslag plaats wordt bijvoorbeeld vastgelegd als onderdeel van de geschiedenis.

## <a name="notebook-examples"></a>Voor beelden van notebooks

Bekijk deze notebooks voor voor beelden van training met verschillende Compute-doelen:
* [procedure-naar-gebruik-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [zelfstudies/img-classificatie-deel 1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Voor het trainen](tutorial-train-models-with-aml.md) van een model wordt gebruikgemaakt van een beheerd reken doel om een model te trainen.
* Meer informatie over hoe u [Hyper parameters efficiënt](how-to-tune-hyperparameters.md) kunt afstemmen om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* Bekijk de [RunConfiguration class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) SDK-referentie.
* [Azure Machine Learning-service gebruiken met virtuele Azure-netwerken](how-to-enable-virtual-network.md)
