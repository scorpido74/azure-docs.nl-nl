---
title: Containerworkloads - Azure Batch
description: Meer informatie over het uitvoeren en schalen van apps vanuit containerafbeeldingen in Azure Batch. Maak een groep rekenknooppunten die het uitvoeren van containertaken ondersteunen.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/02/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 81f4e753ffbaaefd5761c9396a6533bac9f212c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254844"
---
# <a name="run-container-applications-on-azure-batch"></a>Containertoepassingen uitvoeren op Azure Batch

Met Azure Batch u grote aantallen batchcomputingtaken uitvoeren en schalen op Azure. Batchtaken kunnen rechtstreeks worden uitgevoerd op virtuele machines (knooppunten) in een batchgroep, maar u ook een batchgroep instellen om taken uit te voeren in dockercompatibele containers op de knooppunten. In dit artikel ziet u hoe u een groep rekenknooppunten maakt die het uitvoeren van containertaken ondersteunen en vervolgens containertaken uitvoeren in de groep.

U moet bekend zijn met containerconcepten en hoe u een batchgroep en -taak maken. De codevoorbeelden gebruiken de Batch .NET en Python SDKs. U ook andere Batch-SDK's en hulpprogramma's, waaronder de Azure-portal, gebruiken om batchpools met containerondersteuning te maken en containertaken uit te voeren.

## <a name="why-use-containers"></a>Redenen om containers te gebruiken

Het gebruik van containers biedt een eenvoudige manier om Batch-taken uit te voeren zonder dat u een omgeving en afhankelijkheden hoeft te beheren om toepassingen uit te voeren. Containers implementeren toepassingen als lichtgewicht, draagbare, zelfvoorzienende eenheden die in verschillende omgevingen kunnen worden uitgevoerd. Bijvoorbeeld een container lokaal bouwen en testen en de containerafbeelding uploaden naar een register in Azure of elders. Het containerimplementatiemodel zorgt ervoor dat de runtime-omgeving van uw toepassing altijd correct is geïnstalleerd en geconfigureerd, waar u de toepassing ook host. Containertaken in Batch kunnen ook profiteren van functies van niet-containertaken, waaronder toepassingspakketten en beheer van bronbestanden en uitvoerbestanden.

## <a name="prerequisites"></a>Vereisten

* **SDK-versies**: De Batch SDK's ondersteunen containerafbeeldingen vanaf de volgende versies:
    * Batch REST API versie 2017-09-01.6.0
    * Batch .NET SDK versie 8.0.0
    * Batch Python SDK versie 4.0
    * Batch Java SDK versie 3.0
    * Batch Node.js SDK versie 3.0

* **Accounts:** In uw Azure-abonnement moet u een Batch-account maken en optioneel een Azure Storage-account.

* **Een ondersteunde VM-afbeelding:** containers worden alleen ondersteund in pools die zijn gemaakt met de virtuele machineconfiguratie, van afbeeldingen die zijn beschreven in de volgende sectie 'Ondersteunde afbeeldingen van virtuele machines'. Als u een aangepaste afbeelding opgeeft, raadpleegt u de overwegingen in de volgende sectie en de vereisten in [Een beheerde aangepaste afbeelding gebruiken om een groep virtuele machines te maken.](batch-custom-images.md)

### <a name="limitations"></a>Beperkingen

* Batch biedt ALLEEN RDMA-ondersteuning voor containers die op Linux-pools draaien

* Voor Windows-containerworkloads is het raadzaam om een multicore-vm-grootte voor uw groep te kiezen

## <a name="supported-virtual-machine-images"></a>Ondersteunde afbeeldingen van virtuele machines

Gebruik een van de volgende ondersteunde Windows- of Linux-afbeeldingen om een groep VM-computenodes voor containerworkloads te maken. Zie [lijst met afbeeldingen van virtuele machines](batch-linux-nodes.md#list-of-virtual-machine-images)voor meer informatie over Marketplace-afbeeldingen die compatibel zijn met Batch.

### <a name="windows-support"></a>Windows-ondersteuning

Batch ondersteunt Windows-serverafbeeldingen met containerondersteuningsaanduidingen. Meestal zijn deze afbeelding sku `-with-containers` namen `-with-containers-smalldisk`zijn achtervoegsel met of . Bovendien geeft [de API om alle ondersteunde afbeeldingen in Batch](batch-linux-nodes.md#list-of-virtual-machine-images) weer te geven een `DockerCompatible` mogelijkheid aan als de afbeelding Docker-containers ondersteunt.

U ook aangepaste afbeeldingen maken van VM's met Docker op Windows.

### <a name="linux-support"></a>Linux Support

Voor Linux-containerworkloads ondersteunt Batch momenteel de volgende Linux-afbeeldingen die door Microsoft Azure Batch in de Azure Marketplace zijn gepubliceerd zonder dat er een aangepaste afbeelding nodig is.

#### <a name="vm-sizes-without-rdma"></a>VM-formaten zonder RDMA

- Publisher:`microsoft-azure-batch`
  - Bieden:`centos-container`
  - Bieden:`ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>VM-formaten met RDMA

- Publisher:`microsoft-azure-batch`
  - Bieden:`centos-container-rdma`
  - Bieden:`ubuntu-server-container-rdma`

Deze afbeeldingen worden alleen ondersteund voor gebruik in Azure Batch-pools en zijn afgestemd op de uitvoering van Docker-containers. Ze zijn voorzien van:

* Een vooraf geïnstalleerde [Moby-containerruntime](https://github.com/moby/moby) die compatibel is met Docker

* Vooraf geïnstalleerde NVIDIA GPU-stuurprogramma's en NVIDIA-containerruntime, om de implementatie op Azure N-serie VM's te stroomlijnen

* Vooraf geïnstalleerde/vooraf geconfigureerde afbeelding met ondersteuning voor Infiniband RDMA VM-formaten voor afbeeldingen met het achtervoegsel van `-rdma`. Momenteel ondersteunen deze afbeeldingen geen SR-IOV IB/RDMA VM-formaten.

U ook aangepaste afbeeldingen maken van VM's waarop Docker wordt uitgevoerd op een van de Linux-distributies die compatibel is met Batch. Als u ervoor kiest om uw eigen aangepaste Linux-afbeelding op te geven, raadpleegt u de instructies in [Een beheerde aangepaste afbeelding gebruiken om een pool van virtuele machines te maken.](batch-custom-images.md)

Installeer [Docker Community Edition (CE)](https://www.docker.com/community-edition) of [Docker Enterprise Edition (EE) voor](https://www.docker.com/enterprise-edition)Docker-ondersteuning voor een aangepaste afbeelding.

Aanvullende overwegingen voor het gebruik van een aangepaste Linux-afbeelding:

* Als u wilt profiteren van de GPU-prestaties van Azure N-serie-formaten bij het gebruik van een aangepaste afbeelding, installeert u NVIDIA-stuurprogramma's vooraf. Ook moet u de Docker Engine Utility installeren voor NVIDIA GPU's, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

* Als u toegang wilt krijgen tot het Azure RDMA-netwerk, gebruikt u een VM-grootte met RDMA-formaat. Noodzakelijke RDMA-stuurprogramma's worden geïnstalleerd in de CentOS HPC- en Ubuntu-afbeeldingen die worden ondersteund door Batch. Extra configuratie kan nodig zijn om MPI-workloads uit te voeren. Zie [Exemplaren met RDMA-geschikt of GPU gebruiken in batchgroep](batch-pool-compute-intensive-sizes.md).


## <a name="container-configuration-for-batch-pool"></a>Containerconfiguratie voor batchgroep

Als u een batchgroep wilt inschakelen om containerworkloads uit te voeren, moet u [containerconfiguratie-instellingen](/dotnet/api/microsoft.azure.batch.containerconfiguration) opgeven in het [object VirtualMachineConfiguration van](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) de groep. (In dit artikel vindt u koppelingen naar de batch.NET API-verwijzing. De bijbehorende instellingen bevinden zich in de [Batch Python](/python/api/overview/azure/batch) API.)

U een containerpool maken met of zonder vooraf opgehaalde containerafbeeldingen, zoals in de volgende voorbeelden wordt weergegeven. Met het pull-proces (of prefetch) u containerafbeeldingen vooraf laden vanaf Docker Hub of een ander containerregister op internet. Gebruik voor de beste prestaties een [Azure-containerregister](../container-registry/container-registry-intro.md) in dezelfde regio als het Batch-account.

Het voordeel van het vooraf ophalen van containerafbeeldingen is dat wanneer taken voor het eerst worden uitgevoerd, ze niet hoeven te wachten tot de containerafbeelding is gedownload. De containerconfiguratie trekt containerafbeeldingen naar de VM's wanneer de groep wordt gemaakt. Taken die in de groep worden uitgevoerd, kunnen vervolgens verwijzen naar de lijst met opties voor containerafbeeldingen en containerbeheer.


### <a name="pool-without-prefetched-container-images"></a>Poolzonder vooraf opgehaalde containerafbeeldingen

Als u een groep met containers wilt configureren `ContainerConfiguration` zonder `VirtualMachineConfiguration` vooraf opgehaalde containerafbeeldingen, definieert u en objecten zoals weergegeven in het volgende Python-voorbeeld. In dit voorbeeld wordt de afbeelding van Ubuntu Server voor Azure Batch-containerpools van marketplace gebruikt.


```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


### <a name="prefetch-images-for-container-configuration"></a>Afbeeldingen vooraf ophalen voor containerconfiguratie

Als u containerafbeeldingen in de groep wilt prefetcheren, voegt u de lijst met containerafbeeldingen (`container_image_names`, in Python) toe aan de `ContainerConfiguration`.

In het volgende voorbeeld van Python ziet u hoe u een standaard Ubuntu-containerafbeelding vooraf ophalen vanuit [Docker Hub.](https://hub.docker.com)

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```


In het volgende c#-voorbeeld wordt ervan uitgegaan dat u een TensorFlow-afbeelding vooraf wilt ophalen vanuit [Docker Hub.](https://hub.docker.com) In dit voorbeeld wordt een starttaak uitgevoerd in de VM-host op de poolknooppunten. U bijvoorbeeld een starttaak in de host uitvoeren om een bestandsserver te monteren die vanuit de containers kan worden geopend.

```csharp

ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);
...
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Afbeeldingen vooraf ophalen uit een privécontainerregister

U containerafbeeldingen ook vooraf ophalen door te authenticeren naar een privécontainerregisterserver. In het volgende `ContainerConfiguration` voorbeeld `VirtualMachineConfiguration` halen de objecten een privé TensorFlow-afbeelding vooraf op uit een privé-Azure-containerregister. De afbeeldingsverwijzing is hetzelfde als in het vorige voorbeeld.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Containerinstellingen voor de taak

Als u een containertaak wilt uitvoeren op een containergroep, geeft u containerspecifieke instellingen op. Instellingen omvatten de opties voor het gebruik van afbeeldingen, het register en de containerbeheer.

* Gebruik `ContainerSettings` de eigenschap van de taakklassen om containerspecifieke instellingen te configureren. Deze instellingen worden gedefinieerd door de klasse [TaakcontainerInstellingen.](/dotnet/api/microsoft.azure.batch.taskcontainersettings) Houd er `--rm` rekening mee dat de `--runtime` containeroptie geen extra optie vereist, omdat deze wordt verzorgd door Batch.

* Als u taken uitvoert op containerafbeeldingen, vereisen de [taak voor de cloudtaak](/dotnet/api/microsoft.azure.batch.cloudtask) en [de taak taak voor taakbeheer](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) containerinstellingen. De [starttaak](/dotnet/api/microsoft.azure.batch.starttask), [taakvoorbereidingen](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)en [taakreleasetaak](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) vereisen echter geen containerinstellingen (dat wil zeggen dat ze binnen een containercontext of rechtstreeks op het knooppunt kunnen worden uitgevoerd).

### <a name="container-task-command-line"></a>Opdrachtregel containertaak

Wanneer u een containertaak uitvoert, gebruikt Batch automatisch de opdracht [Docker create](https://docs.docker.com/engine/reference/commandline/create/) om een container te maken met behulp van de afbeelding die in de taak is opgegeven. Batch regelt vervolgens de taakuitvoering in de container.

Net als bij niet-containerbatchtaken stelt u een opdrachtregel in voor een containertaak. Omdat Batch de container automatisch maakt, geeft de opdrachtregel alleen de opdracht of opdrachten op die in de container worden uitgevoerd.

Als de containerafbeelding voor een batchtaak is geconfigureerd met een [ENTRYPOINT-script,](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) u instellen dat de opdrachtregel het standaard ENTRYPOINT gebruikt of overschrijft:

* Als u het standaard item van de containerafbeelding wilt gebruiken, stelt u de taakopdrachtregel in op de lege tekenreeks `""`.

* Als u het standaard ENTRYPOINT wilt overschrijven of als de afbeelding geen ENTRYPOINT heeft, `/app/myapp` `/bin/sh -c python myscript.py`stelt u bijvoorbeeld een opdrachtregel in die geschikt is voor de container of .

Optionele [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) zijn aanvullende argumenten `docker create` die u aangeeft aan de opdracht die Batch gebruikt om de container te maken en uit te voeren. Als u bijvoorbeeld een werkmap voor de `--workdir <directory>` container wilt instellen, stelt u de optie in. Zie de [docker maken](https://docs.docker.com/engine/reference/commandline/create/) referentie voor extra opties.

### <a name="container-task-working-directory"></a>Werkmap containertaak

Een batchcontainertaak wordt uitgevoerd in een werkmap in de container die sterk lijkt op de map Batch die wordt ingesteld voor een normale (niet-container)taak. Houd er rekening mee dat deze werkmap verschilt van de [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) als`C:\` deze is geconfigureerd `/` in de afbeelding of de standaardcontainerwerkmap (op een Windows-container of op een Linux-container).

Voor een batchcontainertaak:

* Alle mappen recursief onder `AZ_BATCH_NODE_ROOT_DIR` het hostknooppunt (de hoofdmap van Azure Batch-mappen) worden in de container in kaart gebracht
* Alle taakomgevingsvariabelen worden in de container in kaart gebracht
* De taakwerkmap `AZ_BATCH_TASK_WORKING_DIR` op het knooppunt wordt hetzelfde ingesteld als voor een gewone taak en in de container in kaart gebracht.

Met deze toewijzingen u op vrijwel dezelfde manier met containertaken werken als niet-containertaken. Installeer bijvoorbeeld toepassingen met toepassingspakketten, krijg toegang tot bronbestanden vanuit Azure Storage, gebruik taakomgevingsinstellingen en volhouden taakuitvoerbestanden nadat de container is gestopt.

### <a name="troubleshoot-container-tasks"></a>Containertaken oplossen

Als uw containertaak niet wordt uitgevoerd zoals verwacht, moet u mogelijk informatie krijgen over de WORKDIR- of ENTRYPOINT-configuratie van de containerafbeelding. Voer de opdracht [Docker Image Inspect uit](https://docs.docker.com/engine/reference/commandline/image_inspect/) om de configuratie te bekijken.

Pas indien nodig de instellingen van de containertaak aan op basis van de afbeelding:

* Geef een absoluut pad op in de opdrachtregel van de taak. Als het standaard ENTRYPOINT van de afbeelding wordt gebruikt voor de taakopdrachtregel, moet u ervoor zorgen dat er een absoluut pad is ingesteld.

* Wijzig in de opties voor containerbeheer van de taak de werkmap die overeenkomt met de WORKDIR in de afbeelding. Stel bijvoorbeeld `--workdir /app`in .

## <a name="container-task-examples"></a>Voorbeelden van containertaken

In het volgende Python-fragment ziet u een basisopdrachtregel die wordt uitgevoerd in een container die is gemaakt van een fictieve afbeelding die is getrokken uit Docker Hub. Hier verwijdert `--rm` de containeroptie de container nadat de `--workdir` taak is voltooid en stelt de optie een werkmap in. De opdrachtregel overschrijft het containerENTRYPOINT met een eenvoudige shell-opdracht die een klein bestand schrijft naar de taakwerkmap op de host.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

In het volgende c#-voorbeeld worden basiscontainerinstellingen voor een cloudtaak weergegeven:

```csharp
// Simple container task command

string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine);
```


## <a name="next-steps"></a>Volgende stappen

* Zie ook de [Batch Shipyard toolkit](https://github.com/Azure/batch-shipyard) voor het eenvoudig implementeren van containerworkloads op Azure Batch through Shipyard [recepten.](https://github.com/Azure/batch-shipyard/tree/master/recipes)

* Zie de [Docker-documentatie](https://docs.docker.com/engine/installation/) voor meer informatie over het installeren en gebruiken van Docker CE op Linux.

* Zie [Een beheerde aangepaste afbeelding gebruiken om een groep virtuele machines te maken voor](batch-custom-images.md)meer informatie over het gebruik van aangepaste afbeeldingen.

* Meer informatie over het [Moby-project](https://mobyproject.org/), een framework voor het maken van containergebaseerde systemen.
