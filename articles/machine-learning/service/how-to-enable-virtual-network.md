---
title: Beveilig experimenten en demijnen in een virtueel netwerk
titleSuffix: Azure Machine Learning service
description: meer informatie over het beveiligen van experimenten/trainings taken en het afleiden/scoren van taken in Azure Machine Learning binnen een Azure Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/05/2019
ms.openlocfilehash: aab93e1ecd112f7ef9fdb0829469efa14aff2e98
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623992"
---
# <a name="secure-azure-ml-experimentation-and-inference-jobs-within-an-azure-virtual-network"></a>Azure ML-experimenten beveiligen en taken in een Azure-Virtual Network afzorgen

In dit artikel vindt u informatie over het beveiligen van experimenten/trainings taken en de functies voor het afwijzen en bepalen van problemen in Azure Machine Learning binnen een Azure Virtual Network (vnet). 

Een **virtueel netwerk** fungeert als beveiligings grens en isoleert uw Azure-resources van het open bare Internet. U kunt ook een virtueel Azure-netwerk toevoegen aan uw on-premises netwerk. Door netwerken aan te koppelen, kunt u uw modellen veilig trainen en toegang verkrijgen tot uw geïmplementeerde modellen.

De Azure Machine Learning-service is afhankelijk van andere Azure-Services voor reken resources. Reken bronnen of COMPUTE- [doelen](concept-compute-target.md)worden gebruikt voor het trainen en implementeren van modellen. De doelen kunnen worden gemaakt in een virtueel netwerk. U kunt bijvoorbeeld micro soft Data Science Virtual Machine gebruiken om een model te trainen en het model vervolgens te implementeren in azure Kubernetes service (AKS). Zie [overzicht van Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)voor meer informatie over virtuele netwerken.

Dit artikel bevat ook gedetailleerde informatie over *Geavanceerde beveiligings instellingen*, informatie die niet nodig is voor basis-of experimentele gebruiks gevallen. Bepaalde gedeelten van dit artikel bevatten informatie over de configuratie voor diverse scenario's. U hoeft de instructies in de aangegeven volg orde of in hun geheel niet te volt ooien.

## <a name="prerequisites"></a>Vereisten

+ Een Azure Machine Learning Services- [werk ruimte](how-to-manage-workspace.md). 

+ Algemene werk ervaring van zowel de [Azure Virtual Network-Service](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) als [IP-netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw reken resources. 

## <a name="use-a-storage-account-for-your-workspace"></a>Een opslag account voor uw werk ruimte gebruiken

Ga als volgt te werk om een Azure-opslag account te gebruiken voor de werkruimte in een virtueel netwerk:

1. Maak een experimenten Compute-exemplaar (bijvoorbeeld een Machine Learning Compute exemplaar) achter een virtueel netwerk of koppel een Experimenteer Compute-instantie aan de werk ruimte (bijvoorbeeld een HDInsight-cluster of een virtuele machine). 

   Zie de secties ' een Machine Learning Compute exemplaar gebruiken ' en ' een virtuele machine of een HDInsight-cluster gebruiken ' in dit artikel voor meer informatie.

1. Ga in het Azure Portal naar de opslag die is gekoppeld aan uw werk ruimte. 

   ![De opslag die is gekoppeld aan de Azure Machine Learning service werkruimte](./media/how-to-enable-virtual-network/workspace-storage.png)

1. Selecteer op de pagina **Azure Storage** __firewalls en virtuele netwerken__. 

   ![Het gebied firewalls en virtuele netwerken op de pagina Azure Storage in het Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Ga als volgt te werk op de pagina __firewalls en virtuele netwerken__ :
    - Selecteer __Geselecteerde netwerken__.
    - Selecteer onder __virtuele netwerken__de koppeling __bestaande virtuele netwerk toevoegen__ . Met deze actie wordt het virtuele netwerk toegevoegd waar uw experimenten Compute-instantie zich bevindt (zie stap 1).
    - Schakel het selectie vakje __vertrouwde micro soft-Services toegang geven tot dit opslag account__ in.

   ![Het deel venster firewalls en virtuele netwerken in de Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)

1. Wanneer u het experiment uitvoert, wijzigt u in de code van het experiment het uitvoeren van de configuratie voor het gebruik van Azure Blob Storage:

    ```python
    run_config.source_directory_data_store = "workspaceblobstore"
    ```

> [!IMPORTANT]
> U kunt het _standaard opslag account_ voor de Azure machine learning-service in een virtueel netwerk plaatsen _alleen voor experimenten_.
>
> U kunt _niet-standaard opslag accounts_ in een virtueel netwerk plaatsen _voor alleen experimenten_.
>
> Zowel de standaard-als niet-standaard opslag accounts die worden gebruikt voor het afgeven van de afleiding, moeten _onbeperkte toegang tot het opslag account_hebben.
>
> Als u niet zeker weet of u de instellingen hebt gewijzigd, raadpleegt u de sectie ' de standaard netwerk toegangs regel wijzigen ' in [Azure Storage firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security). Volg de instructies om toegang tot alle netwerken te verlenen tijdens de deinterferentie of het model leren.

## <a name="use-a-key-vault-instance-with-your-workspace"></a>Een sleutel kluis-exemplaar gebruiken met uw werk ruimte

Het sleutel kluis-exemplaar dat is gekoppeld aan de werk ruimte wordt gebruikt door de Azure Machine Learning-service om de volgende referenties op te slaan:
* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

Ga als volgt te werk om Azure Machine Learning experimenten te gebruiken met Azure Key Vault achter een virtueel netwerk:
1. Ga naar de sleutel kluis die is gekoppeld aan de werk ruimte. 

   ![De sleutel kluis die is gekoppeld aan de Azure Machine Learning service-werk ruimte](./media/how-to-enable-virtual-network/workspace-key-vault.png)

1. Selecteer op de pagina **Key Vault** in het linkerdeel venster firewalls __en virtuele netwerken__. 

   ![De sectie firewalls en virtuele netwerken in het deel venster Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. Ga als volgt te werk op de pagina __firewalls en virtuele netwerken__ :
    - Selecteer onder __toegang toestaan vanuit__de optie __geselecteerde netwerken__.
    - Onder __virtuele netwerken__selecteert u __bestaande virtuele netwerken toevoegen__ om het virtuele netwerk toe te voegen waar uw experimenten Compute-instantie zich bevindt.
    - Onder __vertrouwde micro soft-Services toestaan deze firewall te omzeilen__, selecteert u __Ja__.

   ![De sectie firewalls en virtuele netwerken in het deel venster Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)

## <a name="use-a-machine-learning-compute-instance"></a>Een Machine Learning Compute-exemplaar gebruiken

Als u een Azure Machine Learning Compute-exemplaar in een virtueel netwerk wilt gebruiken, moet u rekening houden met de volgende netwerk vereisten:

- Het virtuele netwerk moet zich in hetzelfde abonnement en dezelfde regio bevinden als de Azure Machine Learning service-werk ruimte.

- Het subnet dat is opgegeven voor het berekenings cluster moet voldoende niet-toegewezen IP-adressen hebben om het aantal Vm's dat voor het cluster is bedoeld, te kunnen ondersteunen. Als het subnet onvoldoende niet-toegewezen IP-adressen heeft, wordt het cluster gedeeltelijk toegewezen.

- Als u het virtuele netwerk wilt beveiligen door verkeer te beperken, moet u sommige poorten voor de compute-service geopend laten. Zie de sectie [vereiste poorten](#mlcports) voor meer informatie.

- Controleer of het beveiligings beleid of de vergren delingen in het abonnement of de resource groep van het virtuele netwerk beperkt zijn tot de machtigingen voor het beheren van het virtuele netwerk.

- Als u meerdere reken clusters in één virtueel netwerk wilt plaatsen, moet u mogelijk een quotum verhoging aanvragen voor een of meer van uw resources.

    Het Machine Learning Compute-exemplaar wijst automatisch extra netwerk bronnen toe aan de resource groep die het virtuele netwerk bevat. Voor elk berekenings cluster wijst de service de volgende resources toe:

    - Eén netwerk beveiligings groep

    - Eén openbaar IP-adres

    - Een load balancer

  De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](https://docs.microsoft.com/azure/azure-subscription-service-limits) van het abonnement.

### <a id="mlcports"></a>Vereiste poorten

Machine Learning Compute maakt momenteel gebruik van de Azure Batch-service om virtuele machines in het opgegeven virtuele netwerk in te richten. Het subnet moet inkomende communicatie vanuit de batch-service toestaan. U gebruikt deze communicatie om uitvoeringen uit te voeren op de Machine Learning Compute knooppunten en te communiceren met Azure Storage en andere resources. De batch-service voegt netwerk beveiligings groepen (Nsg's) toe op het niveau van netwerk interfaces (Nic's) die zijn gekoppeld aan Vm's. Met deze netwerkbeveiligingsgroepen worden automatisch binnenkomende en uitgaande regels geconfigureerd om het volgende verkeer toe te staan:

- Binnenkomend TCP-verkeer op de poorten 29876 en 29877 van een __service label__ van __BatchNodeManagement__.

    ![Een regel voor binnenkomende verbindingen die gebruikmaakt van het BatchNodeManagement-service label](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Beschrijving Binnenkomend TCP-verkeer op poort 22 om externe toegang toe te staan. Gebruik deze poort alleen als u verbinding wilt maken met behulp van SSH op het open bare IP-adres.

- Uitgaand verkeer op een willekeurige poort naar het virtuele netwerk.

- Uitgaand verkeer op een willekeurige poort naar internet.

Wees voorzichtig als u binnenkomende of uitgaande regels toevoegt of wijzigt in netwerkbeveiligingsgroepen die door Batch zijn geconfigureerd. Als een NSG communicatie met de reken knooppunten blokkeert, stelt de compute-service de status van de reken knooppunten in op onbruikbaar.

U hoeft Nsg's niet op subnetniveau op te geven omdat de Azure Batch-service zijn eigen Nsg's configureert. Als het opgegeven subnet echter is gekoppeld Nsg's of een firewall, configureert u de regels voor binnenkomende en uitgaande verbindingen zoals eerder beschreven.

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende installatie kopieën:

![De inkomende NSG-regels voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png)

![De regels voor uitgaande NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a id="limiting-outbound-from-vnet"></a>Uitgaande connectiviteit vanuit het virtuele netwerk beperken

Als u de standaard regels voor uitgaande verbindingen niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, gaat u als volgt te werk:

- Uitgaande Internet verbinding weigeren met behulp van de NSG-regels.

- Beperk het uitgaande verkeer tot het volgende:
   - Azure Storage, door gebruik te maken van de __service tag__ __Storage. Region_Name__ (bijvoorbeeld Storage. oostelijke)
   - Azure Container Registry, met behulp van de __service-tag__ __AzureContainerRegistry. Region_Name__ (bijvoorbeeld AzureContainerRegistry. oostelijke)
   - Azure Machine Learning-service, met behulp van het __service label__ __AzureMachineLearning__

De NSG-regel configuratie in de Azure Portal wordt weer gegeven in de volgende afbeelding:

![De regels voor uitgaande NSG voor Machine Learning Compute](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)

### <a name="user-defined-routes-for-forced-tunneling"></a>Door de gebruiker gedefinieerde routes voor geforceerde tunneling

Als u gebruik wilt maken van geforceerde tunneling met Machine Learning Compute, voegt u door de [gebruiker gedefinieerde routes (udr's)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) toe aan het subnet dat de reken resource bevat.

* Stel een UDR in voor elk IP-adres dat wordt gebruikt door de Azure Batch-service in de regio waar uw resources bestaan. Deze Udr's inschakelen de batch-service om te communiceren met reken knooppunten voor het plannen van taken. Neem contact op met de ondersteuning van Azure voor een lijst met IP-adressen van de batch-service.

* Uitgaand verkeer naar Azure Storage mag niet worden geblokkeerd door uw on-premises netwerk apparaat. In het bijzonder zijn de url's in het `<account>.table.core.windows.net`formulier `<account>.queue.core.windows.net`, en `<account>.blob.core.windows.net`.

Wanneer u de Udr's toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het __type volgende hop__ in op __Internet__. In de volgende afbeelding ziet u een voor beeld van deze UDR in de Azure Portal:

![Voor beeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

Zie [een Azure batch groep maken in een virtueel netwerk](../../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)voor meer informatie.

### <a name="create-a-machine-learning-compute-cluster-in-a-virtual-network"></a>Een Machine Learning Compute cluster maken in een virtueel netwerk

Ga als volgt te werk om een Machine Learning Compute cluster te maken:

1. Selecteer in de [Azure Portal](https://portal.azure.com)uw Azure machine learning service-werk ruimte.

1. Selecteer in de sectie __toepassing__ Compute en selecteer vervolgens __Compute toevoegen__.

1. Ga als volgt te werk om deze Compute-resource te configureren voor het gebruik van een virtueel netwerk:

    a. Voor __netwerk configuratie__selecteert u __Geavanceerd__.

    b. Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .

    c. Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.

    d. Selecteer in de vervolg keuzelijst __subnet__ het subnet dat u wilt gebruiken.

   ![De instellingen van het virtuele netwerk voor Machine Learning Compute](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

U kunt ook een Machine Learning Compute cluster maken met behulp van de Azure Machine Learning SDK. Met de volgende code wordt een nieuw machine learning Compute Cluster gemaakt `default` in het subnet van een virtueel `mynetwork`netwerk met de naam:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

Wanneer het maken van het proces is voltooid, traint u uw model met behulp van het cluster in een experiment. Zie voor meer informatie [een berekenings doel selecteren en gebruiken voor training](how-to-set-up-training-targets.md).

## <a name="use-a-virtual-machine-or-hdinsight-cluster"></a>Een virtuele machine of een HDInsight-cluster gebruiken

> [!IMPORTANT]
> De Azure Machine Learning-service ondersteunt alleen virtuele machines waarop Ubuntu wordt uitgevoerd.

Ga als volgt te werk als u een virtuele machine of een Azure HDInsight-cluster in een virtueel netwerk wilt gebruiken in uw werkruimte:

1. Maak een virtuele machine of een HDInsight-cluster met behulp van de Azure Portal of de Azure CLI en plaats het cluster in een virtueel Azure-netwerk. Raadpleeg voor meer informatie de volgende artikelen:
    * [Virtuele Azure-netwerken voor Linux-Vm's maken en beheren](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight uitbreiden met behulp van een virtueel Azure-netwerk](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. Configureer een bron vermelding voor de netwerk beveiligings groep zodat de Azure Machine Learning-service kan communiceren met de SSH-poort op de virtuele machine of het cluster. De SSH-poort is doorgaans poort 22. Ga als volgt te werk om verkeer van deze bron toe te staan:

    * Selecteer in de vervolg keuzelijst __bron__ de optie __service label__.

    * Selecteer __AzureMachineLearning__in de vervolg keuzelijst __bron service label__ .

    * __Selecteer*__ in de vervolg keuzelijst __bron poort bereik__ .

    * Selecteer in de vervolg keuzelijst __bestemming__ __een__.

    * Selecteer __22__in de vervolg keuzelijst __doel poort bereik__ .

    * Onder __protocol__selecteert u __een__.

    * Selecteer onder __actie__ __toestaan__.

   ![Binnenkomende regels voor het experimenteren op een VM-of HDInsight-cluster in een virtueel netwerk](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Behoud de standaard regels voor uitgaande verbindingen voor de netwerk beveiligings groep. Zie de standaard beveiligings regels in [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)voor meer informatie.

    Als u de standaard regels voor uitgaand verkeer niet wilt gebruiken en u de uitgaande toegang van uw virtuele netwerk wilt beperken, raadpleegt u de sectie [uitgaande verbindingen beperken via het virtuele netwerk](#limiting-outbound-from-vnet) .

1. Koppel de virtuele machine of het HDInsight-cluster aan uw Azure Machine Learning service-werk ruimte. Zie COMPUTE- [doelen voor model training instellen](how-to-set-up-training-targets.md)voor meer informatie.

<a id="aksvnet"></a>

## <a name="use-azure-kubernetes-service-aks"></a>Azure Kubernetes service (AKS) gebruiken

Ga als volgt te werk om AKS in een virtueel netwerk toe te voegen aan uw werkruimte:

> [!IMPORTANT]
> Voordat u aan de volgende procedure begint, controleert u de vereisten en plant u de IP-adres sering voor uw cluster. Zie [geavanceerde netwerken configureren in azure Kubernetes service (AKS)](https://docs.microsoft.com/azure/aks/configure-advanced-networking)voor meer informatie.
>
> Het AKS-exemplaar en het virtuele Azure-netwerk moeten zich in dezelfde regio bevinden.

1. Zorg er in het [Azure Portal](https://portal.azure.com)voor dat de NSG die het virtuele netwerk beheert, een binnenkomende regel heeft die is ingeschakeld voor de Azure machine learning-service door __AzureMachineLearning__ als **bron**te gebruiken.

    ![Het deel venster reken Azure Machine Learning-service toevoegen](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-aml.png)

1. Selecteer uw Azure Machine Learning service-werk ruimte.

1. Selecteer in de sectie __toepassing__ Compute en selecteer vervolgens __Compute toevoegen__.

1. Ga als volgt te werk om deze Compute-resource te configureren voor het gebruik van een virtueel netwerk:

    - Voor __netwerk configuratie__selecteert u __Geavanceerd__.

    - Selecteer de resource groep met het virtuele netwerk in de vervolg keuzelijst __resource groep__ .

    - Selecteer in de vervolg keuzelijst __virtueel netwerk__ het virtuele netwerk dat het subnet bevat.

    - Selecteer in de vervolg keuzelijst __subnet__ het subnet.

    - Voer in het vak __Kubernetes service adres bereik__ het adres bereik van de Kubernetes-service in. Dit adres bereik maakt gebruik van een CIDR-notatie (Classless Inter-Domain Routing) IP-bereik voor het definiëren van de IP-adressen die beschikbaar zijn voor het cluster. Het mag niet overlappen met subnet IP-bereiken (bijvoorbeeld 10.0.0.0/16).

    - Voer in het vak __IP-adres van KUBERNETES DNS__ -service het IP-adres van de Kubernetes DNS-service in. Dit IP-adres wordt toegewezen aan de DNS-service Kubernetes. Deze moet zich in het adres bereik van de Kubernetes-service bevallen (bijvoorbeeld 10.0.0.10).

    - Voer in het vak __docker Bridge-adres__ het adres van de docker-brug in. Dit IP-adres wordt toegewezen aan docker-brug. De waarde mag zich niet in een IP-bereik van het subnet of in het Kubernetes (bijvoorbeeld 172.17.0.1/16) bevinden.

   ![Azure Machine Learning-service: Instellingen van het virtuele netwerk Machine Learning Compute](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Zorg ervoor dat de NSG-groep die het virtuele netwerk beheert, een binnenkomende beveiligings regel voor het Score-eind punt heeft ingeschakeld, zodat deze kan worden aangeroepen buiten het virtuele netwerk.
   > [!IMPORTANT]
   > Behoud de standaard regels voor uitgaande verbindingen voor de NSG. Zie de standaard beveiligings regels in [beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)voor meer informatie.
  
   ![Een regel voor binnenkomende beveiliging](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)

U kunt ook de Azure Machine Learning SDK gebruiken om de Azure Kubernetes-service toe te voegen aan een virtueel netwerk. Als u al een AKS-cluster in een virtueel netwerk hebt, koppelt u dit aan de werk ruimte, zoals wordt beschreven in [Deploying to aks](how-to-deploy-to-aks.md). Met de volgende code wordt een nieuw AKS-exemplaar `default` gemaakt in het subnet van een `mynetwork`virtueel netwerk met de naam:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Wanneer het maken van het proces is voltooid, kunt u het decoderen of model leren uitvoeren op een AKS-cluster achter een virtueel netwerk. Zie [implementeren op AKS](how-to-deploy-to-aks.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Trainings omgevingen instellen](how-to-set-up-training-targets.md)
* [Waar u modellen implementeren](how-to-deploy-and-where.md)
* [Modellen veilig implementeren met SSL](how-to-secure-web-service.md)

