---
title: Azure Kubernetes-service maken en koppelen
titleSuffix: Azure Machine Learning
description: Azure Kubernetes service (AKS) kan worden gebruikt voor het implementeren van een machine learning model als een webservice. Meer informatie over het maken van een nieuw AKS-cluster via Azure Machine Learning. U leert ook hoe u een bestaand AKS-cluster kunt koppelen aan uw Azure Machine Learning-werk ruimte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: edd4cc28c6d59f1d6e0c9cabfd5855c72bd3fe73
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661842"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes service-cluster maken en koppelen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning kunt getrainde machine learning modellen implementeren in azure Kubernetes service. U moet echter eerst een AKS-cluster (Azure Kubernetes service) __maken__ op basis van uw Azure ml-werk ruimte of een bestaand AKS-cluster __toevoegen__ . Dit artikel bevat informatie over het maken en koppelen van een cluster.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [een Azure machine learning-werk ruimte maken](how-to-manage-workspace.md)voor meer informatie.

- De [Azure cli-extensie voor machine learning service](reference-azure-machine-learning-cli.md), [Azure machine learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)of de [Azure machine learning Visual Studio code extension](tutorial-setup-vscode-extension.md).

- Als u van plan bent om een Azure-Virtual Network te gebruiken om de communicatie tussen uw Azure ML-werk ruimte en het AKS-cluster te beveiligen, leest u de [netwerk isolatie tijdens de training &](how-to-enable-virtual-network.md) artikel dezicht.

## <a name="limitations"></a>Beperkingen

- Als u een **Standard Load Balancer (SLB)** hebt geïmplementeerd in uw cluster in plaats van een Basic-Load BALANCER (BLB), maakt u een cluster in de AKS-Portal/cli/SDK en **koppelt** u het vervolgens aan de werk ruimte AML.

- Als u een Azure Policy hebt waarmee het maken van open bare IP-adressen wordt beperkt, mislukt het maken van een AKS-cluster. AKS vereist een openbaar IP-adres voor uitgaand [verkeer](/azure/aks/limit-egress-traffic). Het artikel uitgangs verkeer bevat ook richt lijnen voor het vergren delen van uitgaand verkeer van het cluster via het open bare IP-adres, met uitzonde ring van een aantal volledig gekwalificeerde domein namen. Er zijn twee manieren om een openbaar IP-adres in te scha kelen:
    - Het cluster kan gebruikmaken van het open bare IP-adres dat standaard is gemaakt met de BLB of SLB, of
    - Het cluster kan worden gemaakt zonder een openbaar IP-adres en vervolgens moet een openbaar IP-adres met een firewall met een door de gebruiker gedefinieerde route worden geconfigureerd. Zie [cluster uitgang aanpassen met een door de gebruiker gedefinieerde route](/azure/aks/egress-outboundtype)voor meer informatie.
    
    Het AML-besturings vlak communiceert niet met dit open bare IP-adres. Het spreekt naar het AKS-besturings vlak voor implementaties. 

- Als u een AKS-cluster **koppelt** , waarvoor een [geautoriseerd IP-bereik is ingeschakeld voor toegang tot de API-server](/azure/aks/api-server-authorized-ip-ranges), schakelt u de IP-ADRESBEREIKEN van het AML-besturings vlak in voor het AKS-cluster. Het AML-besturings vlak wordt geïmplementeerd in gepaarde regio's en er wordt een afnemende samen Stel op het AKS-cluster geïmplementeerd. Zonder toegang tot de API-server kan het maken van de deinterferentie niet worden geïmplementeerd. Gebruik de [IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=56519) voor beide [regio's](/azure/best-practices-availability-paired-regions) bij het inschakelen van de IP-bereiken in een AKS-cluster.

    Geautoriseerde IP-bereiken werken alleen met Standard Load Balancer.

- Als u een persoonlijk AKS-cluster wilt gebruiken (met behulp van een persoonlijke Azure-koppeling), moet u eerst het cluster maken en het vervolgens **koppelen** aan de werk ruimte. Zie [een persoonlijk Azure Kubernetes service-cluster maken](/azure/aks/private-clusters)voor meer informatie.

- De naam van de compute voor het AKS-cluster moet uniek zijn binnen uw Azure ML-werk ruimte.
    - De naam is vereist en moet tussen de 3 en 24 tekens lang zijn.
    - Geldige tekens zijn onder andere hoofd letters, cijfers en het teken.
    - De naam moet beginnen met een letter.
    - De naam moet uniek zijn voor alle bestaande berekeningen binnen een Azure-regio. U ziet een waarschuwing als de naam die u kiest, niet uniek is.
   
 - Als u modellen wilt implementeren op **GPU** -knoop punten of **FPGA** -knoop punten (of een specifieke SKU), moet u een cluster maken met de specifieke SKU. Er is geen ondersteuning voor het maken van een secundaire knooppunt groep in een bestaand cluster en het implementeren van modellen in de secundaire knooppunt groep.
 
- Wanneer u een cluster maakt of koppelt, kunt u selecteren of u het cluster wilt maken voor __dev-test__ of __productie__. Als u een AKS-cluster wilt maken voor __ontwikkeling__, __validatie__en __testen__ in plaats van productie, stelt u het __cluster doel__ in op __dev-test__. Als u het cluster doel niet opgeeft, wordt er een __productie__ cluster gemaakt. 

    > [!IMPORTANT]
    > Een __dev-test-__ cluster is niet geschikt voor verkeer op productie niveau en kan leiden tot meer tijd. Dev/test-clusters garanderen ook geen fout tolerantie.

- Wanneer u een cluster maakt of koppelt en het cluster wordt gebruikt voor __productie__, moet dit ten minste 12 __virtuele cpu's__bevatten. Het aantal virtuele Cpu's kan worden berekend door het __aantal knoop punten__ in het cluster te vermenigvuldigen met het __aantal kernen dat__ is opgegeven door de geselecteerde VM-grootte. Als u bijvoorbeeld een VM-grootte van ' Standard_D3_v2 ' gebruikt, die vier virtuele kernen heeft, moet u 3 of groter selecteren als het aantal knoop punten.

    Voor een __dev-test__ cluster worden de opdracht mini maal twee virtuele cpu's.

- De Azure Machine Learning SDK biedt geen ondersteuning voor het schalen van een AKS-cluster. Als u de knoop punten in het cluster wilt schalen, gebruikt u de gebruikers interface voor uw AKS-cluster in de Azure Machine Learning Studio. U kunt alleen het aantal knoop punten wijzigen, niet de VM-grootte van het cluster. Raadpleeg de volgende artikelen voor meer informatie over het schalen van de knoop punten in een AKS-cluster:

    - [Het aantal knoop punten in een AKS-cluster hand matig schalen](../aks/scale-cluster.md)
    - [Automatische cluster schaal instellen in AKS](../aks/cluster-autoscaler.md)

## <a name="create-a-new-aks-cluster"></a>Een nieuw AKS-cluster maken

**Geschatte tijd**: ongeveer 10 minuten.

Het maken of koppelen van een AKS-cluster is een eenmalig proces voor uw werk ruimte. U kunt dit cluster hergebruiken voor meerdere implementaties. Als u het cluster of de resource groep verwijdert die het bevat, moet u de volgende keer dat u moet implementeren een nieuw cluster maken. Er kunnen meerdere AKS-clusters aan uw werk ruimte zijn gekoppeld.

In het volgende voor beeld ziet u hoe u een nieuw AKS-cluster maakt met behulp van de SDK en CLI:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

Zie voor meer informatie de referentie [AZ ml computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie [Compute-doelen maken in azure machine learning Studio](how-to-create-attach-compute-studio.md#inference-clusters)voor meer informatie over het maken van een AKS-cluster in de portal.

---

## <a name="attach-an-existing-aks-cluster"></a>Een bestaand AKS-cluster koppelen

**Geschatte tijd:** Ongeveer 5 minuten.

Als u al een AKS-cluster in uw Azure-abonnement hebt en versie 1,17 of lager is, kunt u het gebruiken om uw installatie kopie te implementeren.

> [!TIP]
> Het bestaande AKS-cluster kan zich in een andere Azure-regio bevinden dan uw Azure Machine Learning-werk ruimte.


> [!WARNING]
> Maak vanuit uw werk ruimte niet meerdere gelijktijdige bijlagen op hetzelfde AKS-cluster. U kunt bijvoorbeeld een AKS-cluster koppelen aan een werk ruimte met behulp van twee verschillende namen. Elke nieuwe bijlage verbreekt de vorige bestaande bijlage (n).
>
> Als u een AKS-cluster opnieuw wilt koppelen, bijvoorbeeld om TLS of een andere cluster configuratie-instelling te wijzigen, moet u eerst de bestaande bijlage verwijderen met behulp van [AksCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Raadpleeg de volgende artikelen voor meer informatie over het maken van een AKS-cluster met behulp van de Azure CLI of portal:

* [Een AKS-cluster maken (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Een AKS-cluster maken (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Een AKS-cluster maken (ARM-sjabloon in azure Quick Start-sjablonen)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

In het volgende voor beeld ziet u hoe u een bestaand AKS-cluster koppelt aan uw werk ruimte:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Voor meer informatie over de klassen, methoden en para meters die in dit voor beeld worden gebruikt, raadpleegt u de volgende referentie documenten:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute. attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een bestaand cluster wilt koppelen met behulp van de CLI, moet u de bron-ID van het bestaande cluster ophalen. Gebruik de volgende opdracht om deze waarde op te halen. Vervang door `myexistingcluster` de naam van uw AKS-cluster. Vervang door `myresourcegroup` de resource groep die het cluster bevat:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Gebruik de volgende opdracht om het bestaande cluster aan uw werk ruimte te koppelen. Vervang door de `aksresourceid` waarde die door de vorige opdracht is geretourneerd. Vervang door `myresourcegroup` de resource groep die uw werk ruimte bevat. Vervang door `myworkspace` de naam van uw werk ruimte.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Zie voor meer informatie de referentie [AZ ml computetarget attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Zie [Compute-doelen maken in azure machine learning Studio](how-to-create-attach-compute-studio.md#inference-clusters)voor meer informatie over het koppelen van een AKS-cluster in de portal.

---

## <a name="next-steps"></a>Volgende stappen

* [Hoe en waar een model moet worden geïmplementeerd](how-to-deploy-and-where.md)
* [Een model implementeren in een Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md)