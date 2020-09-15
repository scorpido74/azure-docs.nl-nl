---
title: Training maken & computes implementeren (python)
titleSuffix: Azure Machine Learning
description: Gebruik de Azure Machine Learning python-SDK voor het maken van trainings-en implementatie-reken resources (reken doelen) voor machine learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: af912838e99e7b36cb29695758108f0a9efeb8ea
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561632"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Reken doelen maken voor model training en-implementatie met python SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel gebruikt u de Azure Machine Learning python SDK om reken doelen te maken en te beheren. U kunt ook Compute-doelen maken en beheren met:
* [Azure machine learning Studio](how-to-create-attach-compute-studio.md), 
* De [cli-extensie](reference-azure-machine-learning-cli.md#resource-management) voor Azure machine learning
* De [VS code-extensie](how-to-manage-resources-vscode.md#compute-clusters) voor Azure machine learning.


## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maak dan een gratis account voordat u begint. Probeer vandaag nog de [gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree)
* De [Azure machine learning SDK voor python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md)

## <a name="limitations"></a>Beperkingen

* **Maak niet meerdere gelijktijdige bijlagen met dezelfde reken kracht** vanuit uw werk ruimte. U kunt bijvoorbeeld één Azure Kubernetes service-cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

    Als u een reken doel opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere configuratie-instelling voor het cluster te wijzigen, moet u eerst de bestaande bijlage verwijderen.

* Enkele van de scenario's die in dit document worden vermeld, zijn gemarkeerd als __Preview__. De Preview-functionaliteit wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="whats-a-compute-target"></a>Wat is een reken doel?

Met Azure Machine Learning kunt u uw model trainen op diverse resources of omgevingen, die gezamenlijk worden aangeduid als [__Compute-doelen__](concept-azure-machine-learning-architecture.md#compute-targets). Een compute-doel kan een lokale machine of een Cloud resource zijn, zoals een Azure Machine Learning compute, Azure HDInsight of een externe virtuele machine.  U kunt ook reken doelen voor model implementatie maken, zoals wordt beschreven in [' waar en hoe u uw modellen implementeert '](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Reken doelen voor training

Azure Machine Learning heeft verschillende ondersteuning voor verschillende Compute-doelen. Een typische levens cyclus voor model ontwikkeling begint met het ontwikkelen/experimenteren van een kleine hoeveelheid gegevens. In deze fase raden we u aan om een lokale omgeving te gebruiken. Bijvoorbeeld uw lokale computer of een VM op basis van de Cloud. Wanneer u uw training op grotere gegevens sets schaalt of gedistribueerde trainingen uitvoert, raden we u aan Azure Machine Learning Compute te gebruiken voor het maken van een cluster met één of meerdere knoop punten dat automatisch wordt geschaald wanneer u een uitvoering verzendt. U kunt ook uw eigen reken resource koppelen, maar de ondersteuning voor verschillende scenario's kan variëren zoals hieronder wordt beschreven:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Azure Machine Learning compute-clusters kunnen worden gemaakt als een permanente resource of dynamisch worden gemaakt wanneer u een uitvoering aanvraagt. Bij het maken van de uitvoering wordt het reken doel verwijderd nadat de training is voltooid, zodat u de reken doelen die op deze manier zijn gemaakt, niet opnieuw kunt gebruiken.

Gebruik de volgende secties om deze reken doelen te configureren:

* [Lokale computer](#local)
* [Azure Machine Learning-rekenclusters](#amlcompute)
* [Azure Machine Learning-rekeninstantie](#instance)
* [Externe virtuele machines](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Reken doelen voor demijnen

Bij het uitvoeren van een dematiging maakt Azure Machine Learning een docker-container die het model host en de bijbehorende resources die nodig zijn om het te gebruiken. Deze container wordt vervolgens gebruikt in een van de volgende implementatie scenario's:

* Als een __webservice__ die wordt gebruikt voor realtime-interferentie. Implementaties van webservices gebruiken een van de volgende Compute-doelen:

    * [Lokale computer](#local)
    * [Azure Machine Learning-rekeninstantie](#instance)
    * [Azure Container Instances](#aci)
    * [Azure Kubernetes Services](how-to-create-attach-kubernetes.md)
    * Azure Functions (preview-versie). Implementatie naar Azure Functions is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Van daaruit wordt geïmplementeerd met behulp van Azure Functions. Zie [een machine learning model implementeren op Azure functions (preview)](how-to-deploy-functions.md)voor meer informatie.

* Als een __batch-interferentie__ -eind punt dat wordt gebruikt om periodiek batches gegevens te verwerken. Voor batch-interferenties wordt [Azure machine learning Compute-Cluster](#amlcompute)gebruikt.

* Naar een __IOT-apparaat__ (preview-versie). Implementatie naar een IoT-apparaat is alleen afhankelijk van Azure Machine Learning voor het bouwen van de docker-container. Van daaruit wordt geïmplementeerd met behulp van Azure IoT Edge. Zie [Deploy as a IOT Edge module (preview)](/azure/iot-edge/tutorial-deploy-machine-learning)voor meer informatie.

## <a name="local-computer"></a><a id="local"></a>Lokale computer

Wanneer u uw lokale computer gebruikt voor **trainingen**, hoeft u geen reken doel te maken.  U hoeft alleen maar [de training](how-to-set-up-training-targets.md) uit te voeren vanaf uw lokale computer.

Wanneer u uw lokale computer **gebruikt voor**demijnen, moet docker zijn geïnstalleerd. Als u de implementatie wilt uitvoeren, gebruikt u [LocalWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) om de poort te definiëren die wordt gebruikt door de webservice. Gebruik vervolgens het normale implementatie proces zoals beschreven in [modellen implementeren met Azure machine learning](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning Compute-Cluster

Azure Machine Learning Compute-Cluster is een beheerde infra structuur voor beheer, waarmee u eenvoudig een berekening met één of meerdere knoop punten kunt maken. De berekening wordt binnen uw werkruimte regio gemaakt als een resource die kan worden gedeeld met andere gebruikers in uw werk ruimte. De berekening wordt automatisch omhoog geschaald wanneer een taak wordt verzonden en kan in een Azure-Virtual Network worden geplaatst. De berekening wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een [docker-container](https://www.docker.com/why-docker).

U kunt Azure Machine Learning Compute gebruiken voor het distribueren van een training of batch-deprocessor proces over een cluster van CPU-of GPU-reken knooppunten in de Cloud. Zie [grootten geoptimaliseerd voor virtuele machines](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)voor meer informatie over de VM-grootten die GPU bevatten. 

Azure Machine Learning Compute heeft standaard limieten, zoals het aantal kernen dat kan worden toegewezen. Zie voor meer informatie [beheer en aanvragen van quota's voor Azure-resources](how-to-manage-quotas.md).

> [!TIP]
> Clusters kunnen over het algemeen tot 100 knoop punten worden geschaald zolang u voldoende quota hebt voor het vereiste aantal kernen. Standaard worden clusters ingesteld met communicatie tussen knoop punten die is ingeschakeld tussen de cluster knooppunten voor de ondersteuning van MPI-taken. U kunt uw clusters echter naar duizenden knoop punten schalen door simpelweg [een ondersteunings ticket te verhogen](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)en u aan te vragen om een lijst met uw abonnement of werk ruimte of een specifiek cluster te maken voor het uitschakelen van communicatie tussen knoop punten. 

Azure Machine Learning Compute kan worden hergebruikt in uitvoeringen. De compute kan worden gedeeld met andere gebruikers in de werk ruimte en wordt bewaard tussen uitvoeringen, waarbij knoop punten automatisch omhoog of omlaag worden geschaald op basis van het aantal verzonden uitvoeringen en de max_nodes die in uw cluster zijn ingesteld. Met de instelling min_nodes bepaalt u de mini maal beschik bare knoop punten.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Maken en bijvoegen**: als u een permanente Azure machine learning Compute-resource in python wilt maken, geeft u de **vm_size** en **max_nodes** eigenschappen op. Azure Machine Learning gebruikt vervolgens slimme standaard instellingen voor de andere eigenschappen. De berekening wordt automatisch geschaald naar nul knoop punten wanneer deze niet wordt gebruikt.   Er zijn specifieke Vm's gemaakt om uw taken naar behoefte uit te voeren.
    
    * **vm_size**: de VM-familie van de knoop punten die door Azure machine learning Compute zijn gemaakt.
    * **max_nodes**: het maximum aantal knoop punten waarmee automatisch kan worden geschaald wanneer u een taak op Azure machine learning Compute uitvoert.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   U kunt ook verschillende geavanceerde eigenschappen configureren wanneer u Azure Machine Learning Compute maakt. Met de eigenschappen kunt u een permanent cluster met een vaste grootte of binnen een bestaand Azure-Virtual Network in uw abonnement maken.  Zie de [klasse AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) voor meer informatie.

    U kunt ook een permanente Azure Machine Learning Compute-bron maken en koppelen in [Azure machine learning Studio](how-to-create-attach-compute-studio.md#portal-create).

Nu u de reken kracht hebt gekoppeld, is de volgende stap [het verzenden van de trainings uitvoering of het](how-to-set-up-training-targets.md) uitvoeren van een [batch-deinterferentie](how-to-use-parallel-run-step.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> De kosten voor reken clusters verlagen

U kunt ook [virtuele machines met lage prioriteit](concept-plan-manage-cost.md#low-pri-vm) gebruiken om enkele of alle werk belastingen uit te voeren. Deze Vm's hebben geen gegarandeerde Beschik baarheid en kunnen worden afgebroken tijdens het gebruik. Een afgebroken taak wordt opnieuw gestart, niet hervat. 

Gebruik een van de volgende manieren om een VM met lage prioriteit op te geven:
    
* Kies in de Studio **lage prioriteit** bij het maken van een virtuele machine.
    
* Stel in de python-SDK het `vm_priority` kenmerk in uw inrichtings configuratie in.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Stel met behulp van de CLI het `vm-priority` volgende in:
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Beheerde identiteit instellen

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Beheerde identiteit configureren in uw inrichtings configuratie:  
    
* Door het systeem toegewezen beheerde identiteit:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Door de gebruiker toegewezen beheerde identiteit: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Beheerde identiteit toevoegen aan een bestaand reken cluster:  
    
* Door het systeem toegewezen beheerde identiteit:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Door de gebruiker toegewezen beheerde identiteit:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Gebruik van beheerde identiteit

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Reken instantie Azure Machine Learning

[Azure machine learning Compute-instantie](concept-compute-instance.md) is een infra structuur voor beheerde berekeningen waarmee u eenvoudig één virtuele machine kunt maken. De berekening wordt gemaakt in uw werkruimte regio, maar in tegens telling tot een reken cluster kan een exemplaar niet worden gedeeld met andere gebruikers in uw werk ruimte. Het exemplaar wordt ook niet automatisch omlaag geschaald.  U moet de resource stoppen om lopende kosten te voor komen.

Een reken instantie kan meerdere taken parallel uitvoeren en heeft een taak wachtrij. 

Reken instanties kunnen taken veilig uitvoeren in een [virtuele netwerk omgeving](how-to-enable-virtual-network.md#compute-instance), zonder dat ondernemingen de SSH-poorten hoeven te openen. De taak wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een docker-container. 

1. **Maken en koppelen**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering of het](how-to-set-up-training-targets.md) [implementeren van een model voor](how-to-deploy-local-container-notebook-vm.md)demijnen.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container Instance

Azure Container Instances (ACI) worden dynamisch gemaakt wanneer u een model implementeert. U kunt geen ACI op een andere manier maken of koppelen aan uw werk ruimte. Zie [een model implementeren op Azure container instances](how-to-deploy-azure-container-instance.md)voor meer informatie.

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes service (AKS) biedt diverse configuratie opties voor het gebruik van Azure Machine Learning. Zie de [Azure Kubernetes-service maken en koppelen](how-to-create-attach-kubernetes.md)voor meer informatie.

## <a name="remote-virtual-machines"></a><a id="vm"></a>Externe virtuele machines

Azure Machine Learning biedt ook ondersteuning voor het meebrengen van uw eigen reken resource en het koppelen aan uw werk ruimte. Een van deze bron typen is een wille keurige externe virtuele machine, zolang deze toegankelijk is vanaf Azure Machine Learning. De resource kan een Azure-VM, een externe server in uw organisatie of on-premises zijn. Met name op basis van het IP-adres en de referenties (gebruikers naam en wacht woord of SSH-sleutel) kunt u elke toegankelijke virtuele machine gebruiken voor externe uitvoeringen.

U kunt een door het systeem gemaakte Conda-omgeving, een al bestaande python-omgeving of een docker-container gebruiken. Als u wilt uitvoeren op een docker-container, moet er een docker-engine op de VM worden uitgevoerd. Deze functionaliteit is vooral nuttig wanneer u een flexibele, Cloud ontwikkeling/experimentatie omgeving wilt gebruiken dan uw lokale computer.

Gebruik Azure Data Science Virtual Machine (DSVM) als de Azure-VM van de keuze voor dit scenario. Deze VM is een vooraf geconfigureerde data Science-en AI-ontwikkel omgeving in Azure. De virtuele machine biedt een geruime keuze aan hulpprogram ma's en frameworks voor een volledige levenscyclus machine learning ontwikkeling. Zie [Configure a Development Environment](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)(Engelstalig) voor meer informatie over het gebruik van de DSVM met Azure machine learning.

1. **Maken**: Maak een DSVM voordat u het gebruikt om het model te trainen. Zie [de data Science virtual machine inrichten voor Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)als u deze resource wilt maken.

    > [!WARNING]
    > Azure Machine Learning ondersteunt alleen virtuele machines waarop **Ubuntu**wordt uitgevoerd. Wanneer u een virtuele machine maakt of een bestaande virtuele machine kiest, moet u een virtuele machine selecteren die gebruikmaakt van Ubuntu.
    > 
    > Azure Machine Learning moet de virtuele machine ook een __openbaar IP-adres__hebben.

1. **Bijvoegen**: als u een bestaande virtuele machine als een reken doel wilt koppelen, moet u de resource-id, gebruikers naam en het wacht woord voor de virtuele machine opgeven. De resource-ID van de virtuele machine kan worden samengesteld met behulp van de abonnements-ID, naam van de resource groep en de naam van de virtuele machine met de volgende teken reeks notatie: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   U kunt de DSVM ook aan uw werk ruimte koppelen [met behulp van Azure machine learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Maak niet meerdere gelijktijdige bijlagen met dezelfde DSVM in uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

1. **Configureren**: een uitvoerings configuratie voor het DSVM Compute-doel maken. Docker en Conda worden gebruikt om de trainings omgeving op de DSVM te maken en te configureren.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight is een populair platform voor Big data-analyses. Het platform biedt Apache Spark, dat kan worden gebruikt voor het trainen van uw model.

1. **Maken**: Maak het HDInsight-cluster voordat u het gebruikt om het model te trainen. Zie [een Spark-cluster maken in hdinsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)voor informatie over het maken van een Spark in hdinsight-cluster. 

    > [!WARNING]
    > Voor Azure Machine Learning moet het HDInsight-cluster een __openbaar IP-adres__hebben.

    Wanneer u het cluster maakt, moet u een SSH-gebruikers naam en-wacht woord opgeven. Noteer deze waarden, omdat u ze nodig hebt om HDInsight als reken doel te gebruiken.
    
    Nadat het cluster is gemaakt, maakt u een verbinding met de hostnaam \<clustername> -SSH.azurehdinsight.net, waarbij \<clustername> de naam is die u voor het cluster hebt ingesteld. 

1. **Bijvoegen**: als u een HDInsight-cluster als een reken doel wilt koppelen, moet u de resource-id, gebruikers naam en het wacht woord voor het HDInsight-cluster opgeven. De resource-ID van het HDInsight-cluster kan worden samengesteld met behulp van de abonnements-ID, naam van de resource groep en het HDInsight-cluster met de volgende teken reeks indeling: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

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

   U kunt het HDInsight-cluster ook koppelen aan uw werk ruimte [met behulp van Azure machine learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Maak niet meerdere gelijktijdige bijlagen met dezelfde HDInsight vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

1. **Configureren**: een uitvoerings configuratie voor het HDI Compute-doel maken. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu u de reken kracht hebt gekoppeld en de uitvoering hebt geconfigureerd, is de volgende stap [het verzenden van de trainings uitvoering](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch wordt gebruikt voor het efficiënt uitvoeren van grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de Cloud. AzureBatchStep kan worden gebruikt in een Azure Machine Learning-pijp lijn om taken naar een Azure Batch groep machines te verzenden.

Als u Azure Batch als een reken doel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie opgeven:

-    **Azure batch Compute name**: een beschrijvende naam die moet worden gebruikt voor de compute in de werk ruimte
-    **Azure batch account naam**: de naam van het Azure batch account
-    **Resource groep**: de resource groep die het Azure batch-account bevat.

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

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen op hetzelfde Azure Batch vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks is een op Apache Spark gebaseerde omgeving in de Azure-Cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Databricks-werk ruimte voordat u deze gebruikt. Als u een werkruimte resource wilt maken, raadpleegt u de [taak een Spark uitvoeren op Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) document.

Als u Azure Databricks als een reken doel wilt koppelen, geeft u de volgende informatie op:

* __Databricks Compute name__: de naam die u wilt toewijzen aan deze Compute-resource.
* __Naam van de Databricks-werk ruimte__: de naam van de Azure Databricks-werk ruimte.
* __Databricks-toegangs token__: het toegangs token dat wordt gebruikt voor het verifiëren van Azure Databricks. Als u een toegangs token wilt genereren, raadpleegt u het [verificatie](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) document.

De volgende code laat zien hoe u Azure Databricks als een compute-doel koppelt aan de Azure Machine Learning SDK (__de werk ruimte Databricks moet aanwezig zijn in hetzelfde abonnement als uw AML-werk ruimte__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Bekijk een [voor beeld](https://aka.ms/pl-databricks) van een notebook op github voor een meer gedetailleerd voor beeld.

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen op hetzelfde Azure Databricks vanuit uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics is een big data Analytics-platform in de Azure-Cloud. Het kan worden gebruikt als een reken doel met een Azure Machine Learning-pijp lijn.

Maak een Azure Data Lake Analytics-account voordat u het gebruikt. Zie aan de [slag met Azure data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal) document om deze resource te maken.

Als u Data Lake Analytics als een reken doel wilt koppelen, moet u de Azure Machine Learning SDK gebruiken en de volgende informatie opgeven:

* __Compute name__: de naam die u wilt toewijzen aan deze Compute-resource.
* __Resource groep__: de resource groep die het data Lake Analytics-account bevat.
* __Account naam__: de naam van het data Lake Analytics-account.

De volgende code laat zien hoe u Data Lake Analytics als een reken doel koppelt:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Bekijk een [voor beeld](https://aka.ms/pl-adla) van een notebook op github voor een meer gedetailleerd voor beeld.

> [!WARNING]
> Maak niet meerdere gelijktijdige bijlagen met dezelfde ADLA in uw werk ruimte. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).

> [!TIP]
> Azure Machine Learning pijp lijnen kunnen alleen worden gebruikt voor gegevens die zijn opgeslagen in de standaard gegevens opslag van het Data Lake Analytics-account. Als de gegevens waarmee u wilt werken, zich in een niet-standaard archief bevindt, kunt u een gebruiken [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) om de gegevens te kopiëren voor de training.

## <a name="notebook-examples"></a>Voor beelden van notebooks

Bekijk deze notebooks voor voor beelden van training met verschillende Compute-doelen:
* [procedures voor het gebruik van azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* Gebruik de compute-resource om [een trainings uitvoering te verzenden](how-to-set-up-training-targets.md).
* [Zelf studie: een model trainen](tutorial-train-models-with-aml.md) maakt gebruik van een beheerd Compute-doel om een model te trainen.
* Meer informatie over hoe u [Hyper parameters efficiënt kunt afstemmen](how-to-tune-hyperparameters.md) om betere modellen te bouwen.
* Wanneer u een getraind model hebt, leert u [hoe en waar u modellen kunt implementeren](how-to-deploy-and-where.md).
* [Azure Machine Learning gebruiken met virtuele netwerken van Azure](how-to-enable-virtual-network.md)
