---
title: Modellen implementeren voor Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Meer informatie over het implementeren van uw Azure Machine Learning-modellen als webservice met Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: aec1b7f7bf60be34d21d52ca652a776cf3275fe8
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811762"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Een model implementeren in een Azure Kubernetes Service-cluster
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het gebruik van Azure Machine Learning om een model te implementeren als webservice op Azure Kubernetes Service (AKS). Azure Kubernetes Service is goed voor grootschalige productie-implementaties. Gebruik azure Kubernetes-service als u een of meer van de volgende mogelijkheden nodig hebt:

- __Snelle responstijd.__
- __Automatisch schalen__ van de geïmplementeerde service.
- __Hardwareversnellingsopties__ zoals GPU en veldprogrammeerbare gatearrays (FPGA).

> [!IMPORTANT]
> Clusterschaling wordt niet geleverd via de Azure Machine Learning SDK. Zie Het aantal knooppunten schalen in een [AKS-cluster](../aks/scale-cluster.md)voor meer informatie over het schalen van de knooppunten in een AKS-cluster.

Wanneer u implementeert voor Azure Kubernetes Service, implementeert u een AKS-cluster dat is __verbonden met uw werkruimte.__ Er zijn twee manieren om een AKS-cluster aan uw werkruimte te koppelen:

* Maak het AKS-cluster met de Azure Machine Learning SDK, de Machine Learning CLI of [Azure Machine Learning-studio.](https://ml.azure.com) Dit proces verbindt het cluster automatisch met de werkruimte.
* Voeg een bestaand AKS-cluster toe aan uw Azure Machine Learning-werkruimte. Er kan een cluster worden gekoppeld met de Azure Machine Learning SDK, Machine Learning CLI of Azure Machine Learning studio.

> [!IMPORTANT]
> Het creatie- of bevestigingsproces is een eenmalige taak. Zodra een AKS-cluster is verbonden met de werkruimte, u het gebruiken voor implementaties. U het AKS-cluster loskoppelen of verwijderen als u het niet meer nodig hebt. Eenmaal losgemaakt of verwijderd, u niet meer worden geïmplementeerd in het cluster.

## <a name="prerequisites"></a>Vereisten

- Een Azure Machine Learning-werkruimte. Zie [Een Azure Machine Learning-werkruimte maken](how-to-manage-workspace.md)voor meer informatie.

- Een machine learning-model dat in uw werkruimte is geregistreerd. Als u geen geregistreerd model hebt, raadpleegt u [Hoe en waar u modellen implementeren.](how-to-deploy-and-where.md)

- De [Azure CLI-extensie voor Machine Learning-service](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de Azure Machine Learning Visual Studio [Code-extensie](tutorial-setup-vscode-extension.md).

- De __Python-codefragmenten__ in dit artikel gaan ervan uit dat de volgende variabelen zijn ingesteld:

    * `ws`- Ingesteld op uw werkruimte.
    * `model`- Stel in op uw geregistreerde model.
    * `inference_config`- Stel in op de conclusieconfiguratie voor het model.

    Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie over het instellen van deze variabelen.

- De __CLI-fragmenten__ in dit artikel gaan `inferenceconfig.json` ervan uit dat u een document hebt gemaakt. Zie Hoe en waar u [modellen implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van dit document.

## <a name="create-a-new-aks-cluster"></a>Een nieuw AKS-cluster maken

**Tijdsschatting**: Ongeveer 20 minuten.

Het maken of koppelen van een AKS-cluster is een eenmalig proces voor uw werkruimte. U dit cluster opnieuw gebruiken voor meerdere implementaties. Als u het cluster of de brongroep die het bevat verwijdert, moet u de volgende keer dat u het moet implementeren een nieuw cluster maken. U meerdere AKS-clusters aan uw werkruimte koppelen.

> [!TIP]
> Als u uw AKS-cluster wilt beveiligen met een Azure Virtual Network, moet u eerst het virtuele netwerk maken. Zie [Veilig experimenteren en gevolgtrekking met Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet)voor meer informatie.

Als u een AKS-cluster wilt maken voor __ontwikkeling,__ __validatie__en __testen__ in plaats van productie, u het __clusterdoel__ opgeven om __dev-test__te dev.

> [!WARNING]
> Als u `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`het cluster dat wordt gemaakt, instelt, is het niet geschikt voor het verkeer op productieniveau en kan het aantal gevolgtrekkingstijden toenemen. Dev/testclusters garanderen ook geen fouttolerantie. Wij raden ten minste 2 virtuele CPU's voor dev / test clusters.

In de volgende voorbeelden wordt uitgelegd hoe u een nieuw AKS-cluster maakt met de SDK en CLI:

**De SDK gebruiken**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Want [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)als u aangepaste `agent_count` waarden `vm_size`kiest `cluster_purpose` voor `DEV_TEST`en , en `agent_count` dat niet `vm_size` is, dan moet u ervoor zorgen dat vermenigvuldigd met groter is dan of gelijk is aan 12 virtuele CPU's. Als u bijvoorbeeld een `vm_size` van "Standard_D3_v2" gebruikt, die 4 virtuele `agent_count` CPU's heeft, moet u een van 3 of meer kiezen.
>
> De Azure Machine Learning SDK biedt geen ondersteuning voor het schalen van een AKS-cluster. Als u de knooppunten in het cluster wilt schalen, gebruikt u de gebruikersinterface voor uw AKS-cluster in de Azure Machine Learning-studio. U alleen het aantal knooppunten wijzigen, niet de VM-grootte van het cluster.

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Met behulp van de CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Zie voor meer informatie de [az ml computetarget aks-referentie maken.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

## <a name="attach-an-existing-aks-cluster"></a>Een bestaand AKS-cluster koppelen

**Tijdsschatting:** Ongeveer 5 minuten.

Als u al een AKS-cluster in uw Azure-abonnement hebt en versie 1.17 of lager is, u het gebruiken om uw afbeelding te implementeren.

> [!TIP]
> Het bestaande AKS-cluster kan zich in een andere Azure-regio bevinden dan uw Azure Machine Learning-werkruimte.
>
> Als u uw AKS-cluster wilt beveiligen met een Azure Virtual Network, moet u eerst het virtuele netwerk maken. Zie [Veilig experimenteren en gevolgtrekking met Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet)voor meer informatie.

Wanneer u een AKS-cluster aan een werkruimte koppelt, u `cluster_purpose` bepalen hoe u het cluster gebruikt door de parameter in te stellen.

Als u de `cluster_purpose` parameter niet `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`instelt of instelt, moet het cluster ten minste 12 virtuele CPU's beschikbaar hebben.

Als u `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`dit instelt, hoeft het cluster geen 12 virtuele CPU's te hebben. Wij raden ten minste 2 virtuele CPU's voor dev / test. Een cluster dat is geconfigureerd voor dev/test is echter niet geschikt voor het verkeer op productieniveau en kan de gevolgtrekkingstijden verhogen. Dev/testclusters garanderen ook geen fouttolerantie.

> [!WARNING]
> Maak niet meerdere, gelijktijdige bijlagen in hetzelfde AKS-cluster vanuit uw werkruimte. Bijvoorbeeld het koppelen van één AKS-cluster aan een werkruimte met twee verschillende namen. Elke nieuwe bijlage breekt de vorige bestaande bijlage(en).
>
> Als u een AKS-cluster opnieuw wilt koppelen, bijvoorbeeld om TLS of andere clusterconfiguratie-instellingen te wijzigen, moet u eerst de bestaande bijlage verwijderen met [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Zie de volgende artikelen voor meer informatie over het maken van een AKS-cluster met de Azure CLI of portal:

* [Een AKS-cluster maken (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Een AKS-cluster maken (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

In de volgende voorbeelden wordt uitgelegd hoe u een bestaand AKS-cluster aan uw werkruimte koppelt:

**De SDK gebruiken**

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
```

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Met behulp van de CLI**

Als u een bestaand cluster wilt koppelen met de CLI, moet u de bron-id van het bestaande cluster opte' s. Gebruik de volgende opdracht om deze waarde te krijgen. Vervang `myexistingcluster` door de naam van uw AKS-cluster. Vervang `myresourcegroup` de brongroep die het cluster bevat:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met de volgende tekst:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Als u het bestaande cluster aan uw werkruimte wilt koppelen, gebruikt u de volgende opdracht. Vervang `aksresourceid` de waarde die door de vorige opdracht wordt geretourneerd. Vervang `myresourcegroup` de resourcegroep die uw werkruimte bevat. Vervang `myworkspace` door de naam van uw werkruimte.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Zie voor meer informatie de [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) referentie.

## <a name="deploy-to-aks"></a>Implementeren naar AKS

Als u een model wilt implementeren voor Azure Kubernetes Service, maakt u een __implementatieconfiguratie__ waarin de benodigde rekenbronnen worden beschreven. Bijvoorbeeld het aantal cores en geheugen. U hebt ook een __gevolgtrekkingsconfiguratie__nodig, waarin de omgeving wordt beschreven die nodig is om het model en de webservice te hosten. Zie Hoe en waar u modellen [kunt implementeren](how-to-deploy-and-where.md)voor meer informatie over het maken van de conclusieconfiguratie.

### <a name="using-the-sdk"></a>De SDK gebruiken

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Zie de volgende referentiedocumenten voor meer informatie over de klassen, methoden en parameters die in dit voorbeeld worden gebruikt:

* [AksCompute (AksCompute)](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Met behulp van de CLI

Als u wilt implementeren met de CLI, gebruikt u de volgende opdracht. Vervang `myaks` door de naam van het AKS-rekendoel. Vervang `mymodel:1` door de naam en versie van het geregistreerde model. Vervang `myservice` door de naam om deze service te geven:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Zie voor meer informatie de referentie voor het [implementeren van het AZ ML-model.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

### <a name="using-vs-code"></a>VS Code gebruiken

Zie [Implementeren naar AKS via de VS Code-extensie](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)voor informatie over het gebruik van VS-code.

> [!IMPORTANT]
> Voor het implementeren via VS-code moet het AKS-cluster vooraf worden gemaakt of gekoppeld aan uw werkruimte.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Modellen implementeren op AKS met gecontroleerde uitrol (voorbeeld)

Modelversies op een gecontroleerde manier analyseren en promoten met behulp van eindpunten. U maximaal zes versies achter één eindpunt implementeren. Eindpunten bieden de volgende mogelijkheden:

* Configureer het __percentage scoreverkeer dat naar elk eindpunt wordt verzonden__. Bijvoorbeeld route 20% van het verkeer naar eindpunt 'test' en 80% naar 'productie'.

    > [!NOTE]
    > Als u niet 100% van het verkeer voor zijn rekening __default__ neemt, wordt het resterende percentage doorgestuurd naar de standaardeindpuntversie. Als u bijvoorbeeld eindpuntversie 'test' configureert om 10% van het verkeer te genereren en 'prod' voor 30%, wordt de resterende 60% naar de standaardeindpuntversie verzonden.
    >
    > De eerste eindpuntversie die is gemaakt, wordt automatisch geconfigureerd als standaardversie. U dit `is_default=True` wijzigen door in te stellen bij het maken of bijwerken van een eindpuntversie.
     
* Tag een eindpuntversie als __controle__ of __behandeling__. De huidige eindpuntversie van de productie kan bijvoorbeeld het besturingselement zijn, terwijl potentiële nieuwe modellen worden geïmplementeerd als behandelingsversies. Na evaluatie van de prestaties van de behandelingsversies, als men beter presteert dan de huidige controle, kan het worden bevorderd tot de nieuwe productie / controle.

    > [!NOTE]
    > Je maar __één__ controle hebben. U meerdere behandelingen hebben.

U app-inzichten inschakelen om operationele statistieken van eindpunten en geïmplementeerde versies weer te geven.

### <a name="create-an-endpoint"></a>Een eindpunt maken
Zodra u klaar bent om uw modellen te implementeren, maakt u een scoreeindpunt en implementeert u uw eerste versie. In het volgende voorbeeld ziet u hoe u het eindpunt implementeert en maakt met de SDK. De eerste implementatie wordt gedefinieerd als de standaardversie, wat betekent dat niet-gespecificeerd verkeerspercentiel in alle versies naar de standaardversie gaat.  

> [!TIP]
> In het volgende voorbeeld stelt de configuratie de oorspronkelijke eindpuntversie in om 20% van het verkeer te verwerken. Aangezien dit het eerste eindpunt is, is het ook de standaardversie. En omdat we geen andere versies hebben voor de andere 80% van het verkeer, wordt het ook naar de standaard doorgestuurd. Totdat andere versies die een percentage van het verkeer nemen worden ingezet, deze ontvangt effectief 100% van het verkeer.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Versies bijwerken en toevoegen aan een eindpunt

Voeg een andere versie toe aan uw eindpunt en configureer het scoreverkeer percentiel naar de versie. Er zijn twee soorten versies, een controle en een behandeling versie. Er kunnen meerdere behandelingsversies zijn om te helpen vergelijken met één controleversie.

> [!TIP]
> De tweede versie, gemaakt door het volgende codefragment, accepteert 10% van het verkeer. De eerste versie is geconfigureerd voor 20%, dus slechts 30% van het verkeer is geconfigureerd voor specifieke versies. De resterende 70% wordt verzonden naar de eerste eindpuntversie, omdat het ook de standaardversie is.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Bestaande versies bijwerken of verwijderen in een eindpunt. U het standaardtype, het besturingselementtype en het verkeerspercentiel van de versie wijzigen. In het volgende voorbeeld verhoogt de tweede versie het verkeer tot 40% en is het nu de standaardinstelling.

> [!TIP]
> Na het volgende codefragment is de tweede versie nu standaard. Het is nu geconfigureerd voor 40%, terwijl de oorspronkelijke versie is nog steeds geconfigureerd voor 20%. Dit betekent dat 40% van het verkeer niet wordt verantwoord door versieconfiguraties. Het overgebleven verkeer wordt doorgestuurd naar de tweede versie, omdat deze nu standaard is. Het ontvangt effectief 80% van het verkeer.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Webserviceverificatie

Bij het implementeren naar Azure Kubernetes Service is __verificatie op basis van sleutels__ standaard ingeschakeld. U verificatie __op basis van tokens__ ook inschakelen. Verificatie op basis van tokens vereist dat clients een Azure Active Directory-account gebruiken om een verificatietoken aan te vragen, dat wordt gebruikt om aanvragen te doen voor de geïmplementeerde service.

Als u verificatie `auth_enabled=False` __wilt uitschakelen,__ stelt u de parameter in bij het maken van de implementatieconfiguratie. In het volgende voorbeeld wordt verificatie met de SDK uitgeschakeld:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Zie het [Model Gebruiken voor een Azure Machine Learning dat is geïmplementeerd als webservice voor](how-to-consume-web-service.md)informatie over het verifiëren van een clienttoepassing.

### <a name="authentication-with-keys"></a>Verificatie met sleutels

Als sleutelverificatie is ingeschakeld, kunt `get_keys` u de methode gebruiken om een primaire en secundaire verificatiesleutel op te halen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Als u een sleutel moet regenereren, gebruikt u[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Verificatie met tokens

Als u tokenverificatie `token_auth_enabled=True` wilt inschakelen, stelt u de parameter in wanneer u een implementatie maakt of bijwerkt. In het volgende voorbeeld wordt tokenverificatie met de SDK mogelijk:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Als tokenverificatie is ingeschakeld, kunt `get_token` u de methode gebruiken om een JWT-token op te halen en de vervaldatum van dat token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Je moet na de tijd van `refresh_by` het token een nieuw token aanvragen.
>
> Microsoft raadt u ten zeerste aan om uw Azure Machine Learning-werkruimte te maken in dezelfde regio als uw Azure Kubernetes Service-cluster. Als u wilt verifiëren met een token, belt de webservice naar de regio waarin uw Azure Machine Learning-werkruimte is gemaakt. Als de regio van uw werkruimte niet beschikbaar is, u zelfs geen token voor uw webservice ophalen als uw cluster zich in een andere regio bevindt dan uw werkruimte. Dit resulteert er effectief in dat verificatie op basis van tokens niet beschikbaar is totdat de regio van uw werkruimte weer beschikbaar is. Bovendien geldt dat hoe groter de afstand tussen de regio van uw cluster en de regio van uw werkruimte, hoe langer het duurt om een token op te halen.

## <a name="update-the-web-service"></a>De webservice bijwerken

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Volgende stappen

* [Veilige experimenten en gevolgtrekking in een virtueel netwerk](how-to-enable-virtual-network.md)
* [Een model implementeren met een aangepaste Docker-afbeelding](how-to-deploy-custom-docker-image.md)
* [Problemen met implementatie](how-to-troubleshoot-deployment.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Een ML-model gebruiken dat is geïmplementeerd als webservice](how-to-consume-web-service.md)
* [Uw Azure Machine Learning-modellen controleren met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
