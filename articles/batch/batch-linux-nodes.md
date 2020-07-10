---
title: Linux uitvoeren op reken knooppunten van virtuele machine
description: Meer informatie over het verwerken van uw parallelle Compute-werk belastingen op Pools met virtuele Linux-machines in Azure Batch.
ms.topic: how-to
ms.date: 06/01/2018
ms.custom: H1Hack27Feb2017, tracking-python
ms.openlocfilehash: 05e23093a00eab70db683c10eae59ef27152d2f7
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145035"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linux-reken knooppunten inrichten in batch-Pools

U kunt Azure Batch gebruiken om werk belastingen voor parallelle berekeningen uit te voeren op virtuele machines met Linux en Windows. In dit artikel wordt beschreven hoe u Pools van Linux-reken knooppunten in de batch-service kunt maken met behulp van zowel [batch python][py_batch_package] als [batch .net][api_net] -client bibliotheken.

> [!NOTE]
> Toepassingspakketten worden ondersteund in alle Batch-pools die na 5 juli 2017 zijn gemaakt. De pakketten worden ondersteund in Batch-pools die zijn gemaakt tussen 10 maart 2016 en 5 juli 2017, maar alleen als de pool is gemaakt met behulp van een cloudservice-configuratie. Batch-pools die zijn gemaakt vóór 10 maart 2016 bieden geen ondersteuning voor toepassingspakketten. Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over het gebruiken van toepassingspakketten om toepassingen te implementeren naar Batch-knooppunten.
>
>

## <a name="virtual-machine-configuration"></a>Configuratie van virtuele machine
Wanneer u een pool van reken knooppunten in batch maakt, hebt u twee opties waaruit u de grootte van het knoop punt en het besturings systeem kunt selecteren: Cloud Services configuratie en configuratie van de virtuele machine.

**Cloud Services-configuratie** biedt *alleen* Windows rekenknooppunten. Beschik bare groottes van reken knooppunten worden weer gegeven in [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md)en beschik bare besturings systemen worden vermeld in de [Azure Guest OS releases en SDK Compatibility Matrix](../cloud-services/cloud-services-guestos-update-matrix.md). Wanneer u een pool maakt die Azure-Cloud Services knooppunten bevat, geeft u de knooppunt grootte en de besturingssysteem familie op, die in de eerder genoemde artikelen worden beschreven. Voor Pools van Windows-reken knooppunten wordt Cloud Services het meest gebruikt.

**Virtuele-machine configuratie** voorziet in zowel Linux-als Windows-installatie kopieën voor reken knooppunten. Beschik bare grootten van reken knooppunten worden weer gegeven in [grootten voor virtuele machines in azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) en [grootten voor virtuele machines in azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Wanneer u een pool maakt die virtuele-machine configuratie knooppunten bevat, moet u de grootte van de knoop punten, de verwijzing naar de installatie kopie van de virtuele machine en de SKU van de batch-knooppunt agent opgeven die op de knoop punten moet worden geïnstalleerd.

### <a name="virtual-machine-image-reference"></a>Naslag informatie voor installatie kopieën van virtuele machines

De batch-service maakt gebruik van [schaal sets voor virtuele machines](../virtual-machine-scale-sets/overview.md) voor het leveren van reken knooppunten in de configuratie van de virtuele machine. U kunt een installatie kopie van de [Azure Marketplace][vm_marketplace]opgeven of een aangepaste installatie kopie die u hebt voor bereid. Zie [een groep maken met de galerie gedeelde afbeeldingen](batch-sig-images.md)voor meer informatie over aangepaste installatie kopieën.

Wanneer u een verwijzing naar een installatie kopie van een virtuele machine configureert, geeft u de eigenschappen van de installatie kopie van de virtuele machine op. De volgende eigenschappen zijn vereist wanneer u een verwijzing naar een installatie kopie van een virtuele machine maakt:

| **Eigenschappen van afbeeldings verwijzing** | **Voorbeeld** |
| --- | --- |
| Publisher |Canonical |
| Aanbieding |UbuntuServer |
| SKU |18,04-LTS |
| Versie |meest recente |

> [!TIP]
> Meer informatie over deze eigenschappen en hoe u Marketplace-installatie kopieën kunt weer geven in [navigeren en installatie kopieën van virtuele Linux-machines in azure selecteren met CLI of Power shell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Houd er rekening mee dat niet alle installatie kopieën van Marketplace momenteel compatibel zijn met batch. Zie [SKU van node agent](#node-agent-sku)voor meer informatie.
>
>

### <a name="node-agent-sku"></a>SKU van knoop punt agent
De batch-knooppunt agent is een programma dat wordt uitgevoerd op elk knoop punt in de pool en biedt de opdracht-en besturings interface tussen het knoop punt en de batch-service. Er zijn verschillende implementaties van de knooppunt agent, ook wel Sku's genoemd, voor verschillende besturings systemen. Wanneer u een virtuele-machine configuratie maakt, geeft u eerst de verwijzing naar de installatie kopie van de virtuele machine op en geeft u vervolgens de knooppunt agent op die moet worden geïnstalleerd op de installatie kopie. Normaal gesp roken is elke node agent-SKU compatibel met meerdere installatie kopieën van virtuele machines. Hier volgen enkele voor beelden van node-agent-Sku's:

* batch. node. Ubuntu 18,04
* batch. node. CentOS 7
* batch. node. Windows amd64

> [!IMPORTANT]
> Niet alle installatie kopieën van virtuele machines die beschikbaar zijn op de Marketplace, zijn compatibel met de momenteel beschik bare batch-knooppunt agenten. Gebruik de batch-Sdk's om een lijst weer te geven van de beschik bare Sku's voor knoop punt agent en de installatie kopieën van virtuele machines waarmee ze compatibel zijn. Zie de [lijst met installatie kopieën van virtuele machines](#list-of-virtual-machine-images) verderop in dit artikel voor meer informatie en voor beelden van het ophalen van een lijst met geldige installatie kopieën tijdens runtime.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Een Linux-groep maken: batch python
Het volgende code fragment toont een voor beeld van het gebruik van de [Microsoft Azure batch-client bibliotheek voor python][py_batch_package] om een pool met Ubuntu-Server Compute-knoop punten te maken. Referentie documentatie voor de batch python-module vindt u in [azure.batCH-pakket][py_batch_docs] op Lees de documenten.

Met dit code fragment wordt een [ImageReference][py_imagereference] expliciet gemaakt en worden alle eigenschappen (uitgever, aanbieding, SKU, versie) opgegeven. In productie code wordt u echter aangeraden de methode [list_supported_images][py_list_supported_images] te gebruiken om de beschik bare combi Naties van installatie kopieën en node-agents in runtime te bepalen en te selecteren.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Zoals eerder vermeld, raden we u aan om de [ImageReference][py_imagereference] expliciet te maken met behulp van de methode [list_supported_images][py_list_supported_images] om dynamisch te selecteren uit de momenteel ondersteunde combi Naties van knooppunt agent/Marketplace-installatie kopieën. Het volgende python-code fragment laat zien hoe u deze methode gebruikt.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Een Linux-groep maken: batch .NET
Het volgende code fragment toont een voor beeld van het gebruik van de [batch .net][nuget_batch_net] -client bibliotheek voor het maken van een pool met Ubuntu-Server Compute-knoop punten. U kunt de [naslag documentatie voor batch .net][api_net] vinden op docs.Microsoft.com.

Het volgende code fragment maakt gebruik van de [pool Operations][net_pool_ops]. Methode [ListSupportedImages][net_list_supported_images] om een selectie te selecteren in de lijst met momenteel ondersteunde Marketplace-combi Naties van installatie kopie en node-agent. Deze techniek is wenselijk omdat de lijst met ondersteunde combi Naties van tijd tot tijd kan veranderen. De meeste ondersteunde combi Naties worden toegevoegd.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Hoewel het vorige fragment het [pool Operations][net_pool_ops]gebruikt. Methode [ListSupportedImages][net_list_supported_images] voor het dynamisch weer geven en selecteren van ondersteunde afbeeldings-en knooppunt agent-SKU-combi Naties (aanbevolen). u kunt ook een [ImageReference][net_imagereference] expliciet configureren:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lijst met installatie kopieën van virtuele machines
Als u de lijst met alle ondersteunde installatie kopieën voor virtuele machines van de Marketplace voor de batch-service en de bijbehorende knooppunt agenten wilt ophalen, moet u gebruikmaken van de [list_supported_images][py_list_supported_images] (python), [ListSupportedImages][net_list_supported_images] (batch .net) of de bijbehorende API in de respectieve taal-SDK van uw keuze.

## <a name="connect-to-linux-nodes-using-ssh"></a>Verbinding maken met Linux-knoop punten met SSH
Tijdens het ontwikkelen of tijdens het oplossen van problemen kan het nodig zijn om u aan te melden bij de knoop punten in uw pool. In tegens telling tot Windows-reken knooppunten kunt u Remote Desktop Protocol (RDP) niet gebruiken om verbinding te maken met Linux-knoop punten. In plaats daarvan schakelt de batch-service op elk knoop punt SSH-toegang in voor externe verbinding.

Met het volgende python-code fragment wordt een gebruiker gemaakt op elk knoop punt in een pool, wat vereist is voor externe verbinding. Vervolgens worden de gegevens voor de Secure Shell (SSH)-verbinding voor elk knoop punt afgedrukt.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Hier volgt een voor beeld van uitvoer van de vorige code voor een groep die vier Linux-knoop punten bevat:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

In plaats van een wacht woord kunt u een open bare SSH-sleutel opgeven wanneer u een gebruiker op een knoop punt maakt. Gebruik in de python-SDK de para meter **ssh_public_key** op [ComputeNodeUser][py_computenodeuser]. In .NET gebruikt u de [ComputeNodeUser][net_computenodeuser]. Eigenschap [SshPublicKey][net_ssh_key] .

## <a name="pricing"></a>Prijzen
Azure Batch is gebaseerd op Azure Cloud Services en Azure Virtual Machines technologie. De batch-service zelf wordt gratis aangeboden. Dit betekent dat u alleen kosten in rekening brengt voor de reken resources (en kosten die van kracht zijn) die uw batch-oplossingen verbruiken. Wanneer u **Cloud Services configuratie**kiest, worden kosten in rekening gebracht op basis van de [Cloud Services prijs][cloud_services_pricing] structuur. Wanneer u de **configuratie van de virtuele machine**kiest, worden kosten in rekening gebracht op basis van de [virtual machines prijs][vm_pricing] structuur.

Als u toepassingen implementeert voor uw batch-knoop punten met behulp van [toepassings pakketten](batch-application-packages.md), worden er ook kosten in rekening gebracht voor de Azure storage resources die door uw toepassing worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

De [python-code voorbeelden][github_samples_py] in de opslag plaats [Azure-batch-samples][github_samples] op github bevatten scripts die laten zien hoe u veelvoorkomende batch bewerkingen kunt uitvoeren, zoals pool, taak en het maken van taken. Het [Leesmij-bestand][github_py_readme] bij de python-voor beelden bevat informatie over het installeren van de vereiste pakketten.

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_mgmt]: /dotnet/api/overview/azure/batch
[api_rest]: /rest/api/batchservice/
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: /dotnet/api/microsoft.azure.batch.imagereference
[net_list_supported_images]: /dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: /rest/api/batchservice/pool/add
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: /python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
