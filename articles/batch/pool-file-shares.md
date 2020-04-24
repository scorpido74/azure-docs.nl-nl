---
title: Azure-bestands share voor Azure Batch Pools
description: Een Azure Files-share koppelen vanuit reken knooppunten in een Linux-of Windows-groep in Azure Batch.
ms.topic: article
ms.date: 05/24/2018
ms.openlocfilehash: 666ee6bd0e6287545c107427dffcc9f2ccde900a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115445"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Een Azure-bestands share gebruiken met een batch-pool

[Azure files](../storage/files/storage-files-introduction.md) biedt volledig beheerde bestands shares in de cloud die toegankelijk zijn via het SMB-protocol (Server Message Block). Dit artikel bevat informatie en code voorbeelden voor het koppelen en gebruiken van een Azure-bestands share op groeps berekenings knooppunten. De code voorbeelden gebruiken de batch .NET-en python Sdk's, maar u kunt vergelijk bare bewerkingen uitvoeren met andere batch-Sdk's en-hulpprogram ma's.

Batch biedt systeem eigen API-ondersteuning voor het gebruik van Azure Storage blobs om gegevens te lezen en te schrijven. In sommige gevallen wilt u echter een Azure-bestands share openen vanuit uw pool-reken knooppunten. Stel dat u een verouderde werk belasting hebt die afhankelijk is van een SMB-bestands share, of dat uw taken toegang moeten hebben tot gedeelde gegevens of gedeelde uitvoer kunnen produceren. 

## <a name="considerations-for-use-with-batch"></a>Overwegingen voor gebruik met batch

* Overweeg het gebruik van een Azure-bestands share wanneer u Pools hebt die een relatief laag aantal parallelle taken uitvoeren. Bekijk de [prestaties en schaal doelen](../storage/files/storage-files-scale-targets.md) om te bepalen of Azure files (die gebruikmaakt van een Azure Storage-account) moet worden gebruikt, op basis van de verwachte grootte van de groep en het aantal Asset-bestanden. 

* Azure-bestands shares zijn [rendabel](https://azure.microsoft.com/pricing/details/storage/files/) en kunnen worden geconfigureerd met gegevens replicatie naar een andere regio, waardoor het wereld wijd redundant is. 

* U kunt een Azure-bestands share gelijktijdig koppelen van een on-premises computer.

* Zie ook de algemene [overwegingen](../storage/files/storage-files-planning.md) bij de planning voor Azure-bestands shares.


## <a name="create-a-file-share"></a>Een bestandsshare maken

[Maak een bestands share](../storage/files/storage-how-to-create-file-share.md) in een opslag account dat is gekoppeld aan uw batch-account, of in een afzonderlijk opslag account.

## <a name="mount-a-share-on-a-windows-pool"></a>Een share koppelen aan een Windows-groep

In deze sectie vindt u stappen en code voorbeelden voor het koppelen en gebruiken van een Azure-bestands share op een groep Windows-knoop punten. Zie de [documentatie](../storage/files/storage-how-to-use-files-windows.md) voor het koppelen van een Azure-bestands share in Windows voor aanvullende achtergrond. 

In batch moet u de share koppelen telkens wanneer een taak wordt uitgevoerd op een Windows-knoop punt. Op dit moment is het niet mogelijk om de netwerk verbinding tussen taken op Windows-knoop punten te behouden.

Neem bijvoorbeeld een `net use` opdracht op voor het koppelen van de bestands share als onderdeel van de opdracht regel van elke taak. De volgende referenties zijn nodig om de bestands share te koppelen:

* **Gebruikers naam**: Azure\\\<storageaccountname\>, bijvoorbeeld Azure\\*mystorageaccountname*
* **Wacht woord**: \<StorageAccountKeyWhichEnds in = =>, bijvoorbeeld *XXXXXXXXXXXXXXXXXXXXX = =*

Met de volgende opdracht koppelt u een bestands share *myfileshare* in het opslag account *Mystorageaccountname* als station *S* :

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Ter vereenvoudiging geven de voor beelden hier de referenties direct in de tekst door. In de praktijk raden wij u ten zeerste aan de referenties te beheren met behulp van omgevings variabelen, certificaten of een oplossing zoals Azure Key Vault.

Als u de koppelings bewerking wilt vereenvoudigen, kunt u eventueel de referenties op de knoop punten behouden. Daarna kunt u de share koppelen zonder referenties. Voer de volgende twee stappen uit:

1. Voer het `cmdkey` opdracht regel hulpprogramma uit met behulp van een begin taak in de pool configuratie. Hierdoor blijven de referenties op elk Windows-knoop punt. De opdracht regel van de begin taak is vergelijkbaar met:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Koppel de share op elk knoop punt als onderdeel van elke taak `net use`met behulp van. Met de volgende opdracht regel van de taak wordt bijvoorbeeld de bestands share gekoppeld als het station *S:* . Dit wordt gevolgd door een opdracht of script dat verwijst naar de share. Referenties in de cache worden gebruikt in de aanroep `net use`van. Bij deze stap wordt ervan uitgegaan dat u dezelfde gebruikers-id gebruikt voor de taken die u hebt gebruikt in de begin taak van de pool. Dit is niet geschikt voor alle scenario's.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C#-voor beeld
In het volgende C#-voor beeld ziet u hoe u de referenties voor een Windows-groep persistent maakt met behulp van een begin taak. De naam van de opslag bestands service en de opslag referenties worden door gegeven als gedefinieerde constanten. Hier wordt de start taak uitgevoerd onder een standaard account voor automatische gebruikers (niet-beheerders) met groeps bereik.

```csharp
...
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: PoolId,
    targetDedicatedComputeNodes: PoolNodeCount,
    virtualMachineSize: PoolVMSize,
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start task to store credentials to mount file share
string startTaskCommandLine = String.Format("cmd /c \"cmdkey /add:{0} /user:AZURE\\{1} /pass:{2}\"", StorageFileService, StorageAccountName, StorageAccountKey);

pool.StartTask = new StartTask
{
    CommandLine = startTaskCommandLine,
    UserIdentity = new UserIdentity(new AutoUserSpecification(
        elevationLevel: ElevationLevel.NonAdmin, 
        scope: AutoUserScope.Pool))
};

pool.Commit();
```

Na het opslaan van de referenties gebruikt u de opdracht regels van uw taak om de share te koppelen en te verwijzen naar de share in lees-of schrijf bewerkingen. Als basis voor beeld gebruikt de opdracht regel van de taak in het volgende fragment `dir` de opdracht om bestanden in de bestands share weer te geven. Zorg ervoor dat elke taak taak wordt uitgevoerd met dezelfde [gebruikers-id](batch-user-accounts.md) die u hebt gebruikt om de begin taak in de groep uit te voeren. 

```csharp
...
string taskId = "myTask";
string taskCommandLine = String.Format("cmd /c \"net use {0} {1} & dir {2}\"", ShareMountPoint, StorageFileShare, ShareMountPoint);

CloudTask task = new CloudTask(taskId, taskCommandLine);
task.UserIdentity = new UserIdentity(new AutoUserSpecification(
    elevationLevel: ElevationLevel.NonAdmin,
    scope: AutoUserScope.Pool));
tasks.Add(task);
```

## <a name="mount-a-share-on-a-linux-pool"></a>Een share koppelen aan een Linux-groep

Azure-bestands shares kunnen worden gekoppeld in Linux-distributies met behulp van de [CIFS-kernel-client](https://wiki.samba.org/index.php/LinuxCIFS). In het volgende voor beeld ziet u hoe u een bestands share koppelt aan een groep van Ubuntu 16,04 LTS Compute-knoop punten. Als u een andere Linux-distributie gebruikt, zijn de algemene stappen vergelijkbaar, maar u kunt het pakket beheer gebruiken dat geschikt is voor de distributie. Zie [Azure files gebruiken met Linux](../storage/files/storage-how-to-use-files-linux.md)voor meer informatie en voor beelden.

Installeer eerst het pakket onder een gebruikers-id van `cifs-utils` de beheerder en maak het koppel punt (bijvoorbeeld */mnt/MyAzureFileShare*) in het lokale bestands systeem. U kunt een map voor een koppel punt maken op een wille keurige locatie in het bestands systeem, maar dit is de `/mnt` gebruikelijke Conventie voor het maken van deze in de map. Zorg ervoor dat u geen koppel punt rechtstreeks maakt op `/mnt` (op Ubuntu) of `/mnt/resource` (op andere distributies).

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Voer vervolgens de `mount` opdracht uit om de bestands share te koppelen, zodat u deze referenties kunt opgeven:

* **Gebruikers naam**: \<storageaccountname\>, bijvoorbeeld *mystorageaccountname*
* **Wacht woord**: \<StorageAccountKeyWhichEnds in = =>, bijvoorbeeld *XXXXXXXXXXXXXXXXXXXXX = =*

Met de volgende opdracht koppelt u een bestands share *myfileshare* in het opslag account *mystorageaccountname* op */mnt/MyAzureFileShare*: 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Ter vereenvoudiging geven de voor beelden hier de referenties direct in de tekst door. In de praktijk raden wij u ten zeerste aan de referenties te beheren met behulp van omgevings variabelen, certificaten of een oplossing zoals Azure Key Vault.

In een Linux-groep kunt u al deze stappen combi neren in één start taak of uitvoeren in een script. De start taak uitvoeren als een beheerder gebruiker van de groep. Stel de begin taak zo in dat deze volledig is voltooid voordat verdere taken worden uitgevoerd op de groep die verwijst naar de share.

### <a name="python-example"></a>Python-voor beeld

In het volgende python-voor beeld ziet u hoe u een Ubuntu kunt configureren om de share in een begin taak te koppelen. Het koppel punt, het eind punt van de bestands share en de opslag referenties worden door gegeven als gedefinieerde constanten. De start taak wordt uitgevoerd onder een account voor automatische gebruikers met beheerders rechten voor het groeps bereik.

```python
pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="16.04.0-LTS",
            version="latest"),
        node_agent_sku_id="batch.node.ubuntu 16.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install cifs-utils && mkdir -p {} && mount -t cifs {} {} -o vers=3.0,username={},password={},dir_mode=0777,file_mode=0777,serverino\"".format(
            _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_SHARE_ENDPOINT, _COMPUTE_NODE_MOUNT_POINT, _STORAGE_ACCOUNT_NAME, _STORAGE_ACCOUNT_KEY),
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(pool)
```

Na het koppelen van de share en het definiëren van een taak, gebruikt u de share in de opdracht regels van uw taak. De volgende basis opdracht wordt bijvoorbeeld gebruikt `ls` om bestanden in de bestands share weer te geven.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Volgende stappen

* Voor andere opties voor het lezen en schrijven van gegevens in batch, raadpleegt u het [overzicht van batch-functies](batch-api-basics.md) en de taak- [en taak uitvoer persistent](batch-task-output.md).

* Zie ook de [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit, die [Shipyard recepten](https://github.com/Azure/batch-shipyard/tree/master/recipes) bevat voor het implementeren van bestands systemen voor werk belastingen voor batch-containers.
