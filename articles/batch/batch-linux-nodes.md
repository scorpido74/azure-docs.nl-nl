---
title: Linux uitvoeren op compute nodes voor virtuele machines - Azure Batch | Microsoft Documenten
description: Meer informatie over het verwerken van uw parallelle rekenworkloads op groepen virtuele Linux-machines in Azure Batch.
services: batch
documentationcenter: python
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977504f41e93e37ae2c5ce9bdb1182a1cfe0a3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252283"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linux-compute-knooppunten in batchgroepen inrichten

U Azure Batch gebruiken om parallelle compute-workloads uit te voeren op zowel Linux- als Windows-virtuele machines. In dit artikel wordt beschreven hoe u groepen Linux-computenodes maakt in de Batch-service met behulp van zowel de [batch-python-][py_batch_package] als [batch.NET-clientbibliotheken.][api_net]

> [!NOTE]
> Toepassingspakketten worden ondersteund in alle Batch-pools die na 5 juli 2017 zijn gemaakt. De pakketten worden ondersteund in Batch-pools die zijn gemaakt tussen 10 maart 2016 en 5 juli 2017, maar alleen als de pool is gemaakt met behulp van een cloudservice-configuratie. Batch-pools die zijn gemaakt vóór 10 maart 2016 bieden geen ondersteuning voor toepassingspakketten. Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over het gebruiken van toepassingspakketten om toepassingen te implementeren naar Batch-knooppunten.
>
>

## <a name="virtual-machine-configuration"></a>Configuratie van virtuele machines
Wanneer u een groep compute nodes maakt in Batch, hebt u twee opties om de grootte van het knooppunt en het besturingssysteem te selecteren: Cloud Services Configuration en Virtual Machine Configuration.

**Cloud Services-configuratie** biedt *alleen* Windows rekenknooppunten. Beschikbare compute node-formaten worden vermeld in [Formaten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md)en beschikbare besturingssystemen worden weergegeven in de Azure Guest [OS-releases en DE SDK-compatibiliteitsmatrix](../cloud-services/cloud-services-guestos-update-matrix.md). Wanneer u een groep maakt die Azure Cloud Services-knooppunten bevat, geeft u de grootte van het knooppunt en de OS-familie op, die worden beschreven in de eerder genoemde artikelen. Voor groepen Windows-compute nodes wordt Cloud Services het meest gebruikt.

**Virtual Machine Configuration** biedt zowel Linux- als Windows-afbeeldingen voor compute nodes. Beschikbare compute node-formaten worden vermeld in [Groottes voor virtuele machines in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) en [Groottes voor virtuele machines in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows). Wanneer u een groep maakt die knooppunten voor virtuele machineconfiguratie bevat, moet u de grootte van de knooppunten, de referentie van de virtuele machineafbeelding en de SKU voor batchknooppunten opgeven die op de knooppunten moet worden geïnstalleerd.

### <a name="virtual-machine-image-reference"></a>Referentie voor virtuele afbeeldingen

De batchservice maakt gebruik van [virtuele machineschaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) om rekenknooppunten in de virtuele machineconfiguratie te bieden. U een afbeelding van de [Azure Marketplace][vm_marketplace]opgeven of een aangepaste afbeelding opgeven die u hebt voorbereid. Zie Een pool maken [met de galerie Voor gedeelde afbeeldingen](batch-sig-images.md)voor meer informatie over aangepaste afbeeldingen.

Wanneer u een verwijzing naar de virtuele machineafbeelding configureert, geeft u de eigenschappen van de virtuele machineafbeelding op. De volgende eigenschappen zijn vereist wanneer u een afbeeldingsverwijzing voor virtuele machines maakt:

| **Eigenschappen voor afbeeldingsverwijzing** | **Voorbeeld** |
| --- | --- |
| Uitgever |Canonical |
| Aanbieding |UbuntuServer |
| SKU |18.04-LTS |
| Versie |meest recente |

> [!TIP]
> U meer informatie vinden over deze eigenschappen en hoe u Marketplace-afbeeldingen aanbieden in [Navigeren en Linux-afbeeldingen voor virtuele machines selecteren in Azure met CLI of PowerShell.](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Houd er rekening mee dat momenteel niet alle Marketplace-afbeeldingen compatibel zijn met Batch. Zie [Node agent SKU](#node-agent-sku)voor meer informatie.
>
>

### <a name="node-agent-sku"></a>Knooppuntagent SKU
De batchknooppuntagent is een programma dat op elk knooppunt in de groep wordt uitgevoerd en de interface voor command-and-control biedt tussen het knooppunt en de batchservice. Er zijn verschillende implementaties van de node agent, bekend als SKU's, voor verschillende besturingssystemen. Wanneer u een virtuele machineconfiguratie maakt, geeft u eerst de referentie voor virtuele machineafbeeldingen op en geeft u vervolgens de knooppuntagent op die op de afbeelding moet worden geïnstalleerd. Normaal gesproken is elke node agent SKU compatibel met meerdere virtuele machineafbeeldingen. Hier zijn een paar voorbeelden van node agent SKU's:

* batch.node.ubuntu 18.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> Niet alle afbeeldingen van virtuele machines die beschikbaar zijn op de Marketplace zijn compatibel met de momenteel beschikbare batchknooppuntagents. Gebruik de Batch SDKs om de beschikbare node agent SKU's en de virtuele machine afbeeldingen waarmee ze compatibel zijn. Zie de [lijst met afbeeldingen van virtuele machines](#list-of-virtual-machine-images) later in dit artikel voor meer informatie en voorbeelden van het ophalen van een lijst met geldige afbeeldingen tijdens runtime.
>
>

## <a name="create-a-linux-pool-batch-python"></a>Een Linux-pool maken: Batch Python
In het volgende codefragment ziet u een voorbeeld van het gebruik van de [Microsoft Azure Batch Client Library voor Python][py_batch_package] om een groep ubuntu server compute nodes te maken. Referentiedocumentatie voor de Batch Python-module is te vinden op [azure.batch-pakket][py_batch_docs] op Lees de documenten.

Met dit fragment wordt expliciet een [ImageReference][py_imagereference] gemaakt en worden elk van de eigenschappen ervan opgegeven (uitgever, aanbieding, SKU, versie). In productiecode raden we u echter aan de [methode list_supported_images][py_list_supported_images] te gebruiken om te bepalen en te selecteren uit de beschikbare afbeeldings- en knooppuntagent SKU-combinaties tijdens runtime.

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

Zoals eerder vermeld, raden we u aan om in plaats van de [ImageReference][py_imagereference] expliciet te maken, de [list_supported_images][py_list_supported_images] methode gebruikt om dynamisch te selecteren uit de momenteel ondersteunde node agent/Marketplace-afbeeldingscombinaties. In het volgende Python-fragment ziet u hoe u deze methode gebruiken.

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

## <a name="create-a-linux-pool-batch-net"></a>Een Linux-pool maken: Batch .NET
In het volgende codefragment ziet u een voorbeeld van hoe u de [batch.NET-clientbibliotheek][nuget_batch_net] gebruiken om een groep ubuntu-servercomputeknooppunten te maken. U vindt de [batch .net-referentiedocumentatie][api_net] op docs.microsoft.com.

In het volgende codefragment wordt de [PoolOperations gebruikt.][net_pool_ops] De methode [ListSupportedImages][net_list_supported_images] om te selecteren in de lijst met momenteel ondersteunde Marketplace-afbeeldings- en nodeagent SKU-combinaties. Deze techniek is wenselijk omdat de lijst met ondersteunde combinaties van tijd tot tijd kan veranderen. Meestal worden ondersteunde combinaties toegevoegd.

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

Hoewel het vorige fragment de [PoolOperations][net_pool_ops]gebruikt . De methode [ListSupportedImages][net_list_supported_images] om dynamisch aan te geven en te kiezen uit ondersteunde sku-combinaties van afbeeldingen en knooppuntagents (aanbevolen), u ook een [ImageReference][net_imagereference] expliciet configureren:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>Lijst met afbeeldingen van virtuele machines
Als u de lijst met alle ondersteunde Marketplace-afbeeldingen voor virtuele machines voor de Batch-service en de bijbehorende knooppuntagents wilt verkrijgen, u gebruikmaken van de [list_supported_images][py_list_supported_images] (Python), [ListSupportedImages][net_list_supported_images] (Batch .NET) of de bijbehorende API in de respectievelijke taal SDK van uw keuze.

## <a name="connect-to-linux-nodes-using-ssh"></a>Verbinding maken met Linux-knooppunten met SSH
Tijdens de ontwikkeling of tijdens het oplossen van problemen u zich aanmelden bij de knooppunten in uw groep. In tegenstelling tot Windows-compute nodes u geen Extern bureaublad-protocol (RDP) gebruiken om verbinding te maken met Linux-knooppunten. In plaats daarvan biedt de Batch-service SSH-toegang op elk knooppunt voor externe verbinding.

Met het volgende Python-codefragment wordt een gebruiker gemaakt op elk knooppunt in een groep, wat vereist is voor een externe verbinding. Vervolgens worden de verbindingsgegevens van de beveiligde shell (SSH) voor elk knooppunt afgedrukt.

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

Hier vindt u voorbeelduitvoer voor de vorige code voor een groep die vier Linux-knooppunten bevat:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

In plaats van een wachtwoord u een Openbare SSH-sleutel opgeven wanneer u een gebruiker maakt op een knooppunt. Gebruik in de Python SDK de **parameter ssh_public_key** op [ComputeNodeUser][py_computenodeuser]. Gebruik in .NET de [ComputeNodeUser][net_computenodeuser]. [SshPublicKey][net_ssh_key] eigendom.

## <a name="pricing"></a>Prijzen
Azure Batch is gebouwd op Azure Cloud Services en Azure Virtual Machines-technologie. De Batch-service zelf wordt gratis aangeboden, wat betekent dat u alleen in rekening wordt gebracht voor de rekenresources (en bijbehorende kosten die met zich meebrengen) die uw Batch-oplossingen verbruiken. Wanneer u **cloudservicesconfiguratie kiest,** worden er kosten in rekening gebracht op basis van de [prijsstructuur van Cloud Services.][cloud_services_pricing] Wanneer u **Virtual Machine Configuration**kiest, worden er kosten in rekening gebracht op basis van de [prijsstructuur voor virtuele machines.][vm_pricing]

Als u toepassingen implementeert naar uw Batch-knooppunten met [toepassingspakketten,](batch-application-packages.md)worden er ook kosten in rekening gebracht voor de Azure Storage-bronnen die uw toepassingspakketten verbruiken.

## <a name="next-steps"></a>Volgende stappen

De [Python-codevoorbeelden][github_samples_py] in de [azure-batch-samples repository][github_samples] op GitHub bevatten scripts die u laten zien hoe u algemene batchbewerkingen uitvoeren, zoals pool, taak en taakcreatie. De [README][github_py_readme] die de Python-monsters vergezelt, bevat details over het installeren van de vereiste pakketten.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_supported_images]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure.github.io/azure-sdk-for-python/ref/Batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_supported_images]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
