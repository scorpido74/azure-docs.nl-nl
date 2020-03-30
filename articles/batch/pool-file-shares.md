---
title: Azure-bestandsshare voor Azure Batch-groepen | Microsoft Documenten
description: Een Azure-bestandenaandeel monteren vanaf compute-knooppunten in een Linux- of Windows-groep in Azure Batch.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 05/24/2018
ms.author: labrenne
ms.custom: ''
ms.openlocfilehash: 156dad25af5abd1b4d5db32569faf09a23fadfb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022508"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Een Azure-bestandsshare gebruiken met een batchgroep

[Azure Files](../storage/files/storage-files-introduction.md) biedt volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het SMB-protocol (Server Message Block). In dit artikel vindt u informatie en codevoorbeelden voor het monteren en gebruiken van een Azure-bestandsshare op rekenknooppunten voor de groep. De codevoorbeelden gebruiken de Batch .NET en Python SDKs, maar u vergelijkbare bewerkingen uitvoeren met andere Batch-SDK's en hulpprogramma's.

Batch biedt native API-ondersteuning voor het gebruik van Azure Storage-blobs om gegevens te lezen en te schrijven. In sommige gevallen wilt u echter wel toegang krijgen tot een Azure-bestandsshare vanaf uw rekenknooppunten van de groep. U hebt bijvoorbeeld een verouderde workload die afhankelijk is van een smb-bestandsaandeel of uw taken moeten toegang krijgen tot gedeelde gegevens of gedeelde uitvoer produceren. 

## <a name="considerations-for-use-with-batch"></a>Overwegingen voor gebruik met Batch

* Overweeg een Azure-bestandsshare te gebruiken wanneer u pools hebt die een relatief laag aantal parallelle taken uitvoeren. Controleer de [prestatie- en schaaldoelen](../storage/files/storage-files-scale-targets.md) om te bepalen of Azure-bestanden (die een Azure Storage-account gebruiken) moeten worden gebruikt, gezien de verwachte grootte van de groep en het aantal assetbestanden. 

* Azure-bestandsshares zijn [kostenefficiënt](https://azure.microsoft.com/pricing/details/storage/files/) en kunnen worden geconfigureerd met gegevensreplicatie naar een andere regio, zodat deze wereldwijd overbodig zijn. 

* U een Azure-bestandsshare gelijktijdig monteren vanaf een on-premises computer.

* Zie ook de algemene [planningsoverwegingen](../storage/files/storage-files-planning.md) voor Azure-bestandsshares.


## <a name="create-a-file-share"></a>Een bestandsshare maken

[Maak een bestandsshare](../storage/files/storage-how-to-create-file-share.md) in een opslagaccount dat is gekoppeld aan uw Batch-account of in een afzonderlijk opslagaccount.

## <a name="mount-a-share-on-a-windows-pool"></a>Een aandeel op een Windows-groep monteren

In deze sectie vindt u voorbeelden van stappen en code om een Azure-bestandsshare te monteren en te gebruiken op een groep Windows-knooppunten. Zie voor meer achtergrond de [documentatie](../storage/files/storage-how-to-use-files-windows.md) voor het monteren van een Azure-bestandsshare op Windows. 

In Batch moet u het aandeel elke keer dat een taak wordt uitgevoerd op een Windows-knooppunt, weergeven. Momenteel is het niet mogelijk om de netwerkverbinding tussen taken op Windows-knooppunten voort te zetten.

Voeg bijvoorbeeld een `net use` opdracht toe om de bestandsshare te monteren als onderdeel van elke opdrachtregel voor taken. Om de bestandsshare te monteren, zijn de volgende referenties nodig:

* **Gebruikersnaam**:\\\<AZURE storageaccountname\>,\\bijvoorbeeld AZURE*mystorageaccountname*
* **Wachtwoord** \<: StorageAccountKeyWhichEnds in==> bijvoorbeeld *XXXXXXXXXXXXXXXXXXXXXXXXXXX==*

Met de volgende opdracht wordt een *myfileshare* voor bestandsshare gemonteerd in *mijn accountnaam voor opslagaccount* als de *S:* station:

```
net use S: \\mystorageaccountname.file.core.windows.net\myfileshare /user:AZURE\mystorageaccountname XXXXXXXXXXXXXXXXXXXXX==
```

Voor de eenvoud geven de voorbeelden hier de referenties rechtstreeks in tekst door. In de praktijk raden we u ten zeerste aan de referenties te beheren met behulp van omgevingsvariabelen, certificaten of een oplossing zoals Azure Key Vault.

Als u de houderbewerking wilt vereenvoudigen, blijven optioneel de referenties op de knooppunten bestaan. Vervolgens u het aandeel zonder referenties monteren. Voer de volgende twee stappen uit:

1. Voer `cmdkey` het hulpprogramma voor de opdrachtregel uit met een starttaak in de poolconfiguratie. Dit blijft de referenties op elk Windows-knooppunt. De opdrachtregel voor taak starten is vergelijkbaar met:

   ```
   cmd /c "cmdkey /add:mystorageaccountname.file.core.windows.net /user:AZURE\mystorageaccountname /pass:XXXXXXXXXXXXXXXXXXXXX=="

   ```

2. Monteer het aandeel op elk knooppunt `net use`als onderdeel van elke taak met behulp van . De volgende taakopdrachtregel monteert bijvoorbeeld de bestandsshare als het *S:-station.* Dit zou worden gevolgd door een opdracht of script dat verwijst naar het aandeel. Referenties in cache worden gebruikt `net use`in de aanroep naar . In deze stap wordt ervan uitgegaan dat u dezelfde gebruikersidentiteit gebruikt voor de taken die u hebt gebruikt in de begintaak in de groep, wat niet geschikt is voor alle scenario's.

   ```
   cmd /c "net use S: \\mystorageaccountname.file.core.windows.net\myfileshare" 
   ```

### <a name="c-example"></a>C# voorbeeld
In het volgende c#-voorbeeld ziet u hoe u de referenties in een Windows-groep blijven gebruiken met een starttaak. De naam van de opslagbestandsservice en de opslagreferenties worden doorgegeven als gedefinieerde constanten. Hier wordt de starttaak uitgevoerd onder een standaard (niet-beheerders) automatisch gebruikersaccount met een poolbereik.

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

Nadat u de referenties hebt opgeslagen, gebruikt u de taakopdrachtregels om het aandeel te monteren en verwijst u naar het aandeel in lees- of schrijfbewerkingen. Als basisvoorbeeld gebruikt de opdrachtregel van de `dir` taak in het volgende fragment de opdracht om bestanden in de bestandsshare weer te geven. Zorg ervoor dat elke taaktaak wordt uitgevoerd met dezelfde [gebruikersidentiteit](batch-user-accounts.md) die u hebt gebruikt om de starttaak in de groep uit te voeren. 

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

## <a name="mount-a-share-on-a-linux-pool"></a>Een aandeel op een Linux-pool monteren

Azure-bestandsshares kunnen worden gemonteerd in Linux-distributies met behulp van de [CIFS-kernelclient.](https://wiki.samba.org/index.php/LinuxCIFS) In het volgende voorbeeld ziet u hoe u een bestandsshare monteert op een groep Ubuntu 16.04 LTS-computenodes. Als u een andere Linux-distributie gebruikt, zijn de algemene stappen vergelijkbaar, maar gebruikt u de package manager die geschikt is voor de distributie. Zie [Azure-bestanden gebruiken met Linux](../storage/files/storage-how-to-use-files-linux.md)voor meer informatie en aanvullende voorbeelden.

Installeer eerst onder de identiteit `cifs-utils` van een beheerder het pakket en maak het bevestigingspunt (bijvoorbeeld */mnt/MyAzureFileShare)* in het lokale bestandssysteem. Een map voor een bevestigingspunt kan overal op het bestandssysteem worden gemaakt, `/mnt` maar het is gebruikelijk om dit onder de map te maken. Zorg ervoor dat u geen `/mnt` bevestigingspunt direct op `/mnt/resource` (op Ubuntu) of (op andere distributies) maakt.

```
apt-get update && apt-get install cifs-utils && sudo mkdir -p /mnt/MyAzureFileShare
```

Voer vervolgens `mount` de opdracht uit om de bestandsshare te monteren en geeft deze referenties op:

* **Gebruikersnaam**: \<accountnaam\>van de opslag, bijvoorbeeld *mystorageaccountnaam*
* **Wachtwoord** \<: StorageAccountKeyWhichEnds in==> bijvoorbeeld *XXXXXXXXXXXXXXXXXXXXXXXXXXX==*

Met de volgende opdracht wordt een *myfileshare* voor bestandsshare gemonteerd in *mijnaccountnaam van* het opslagaccount bij */mnt/MyAzureFileShare:* 

```
mount -t cifs //mystorageaccountname.file.core.windows.net/myfileshare /mnt/MyAzureFileShare -o vers=3.0,username=mystorageaccountname,password=XXXXXXXXXXXXXXXXXXXXX==,dir_mode=0777,file_mode=0777,serverino && ls /mnt/MyAzureFileShare
```

Voor de eenvoud geven de voorbeelden hier de referenties rechtstreeks in tekst door. In de praktijk raden we u ten zeerste aan de referenties te beheren met behulp van omgevingsvariabelen, certificaten of een oplossing zoals Azure Key Vault.

In een Linux-pool u al deze stappen combineren in één starttaak of ze uitvoeren in een script. Voer de starttaak uit als beheerdersgebruiker in de groep. Stel uw starttaak in om te wachten om te wachten met het voltooien voordat u verdere taken uitvoert op de groep die naar het aandeel verwijst.

### <a name="python-example"></a>Python voorbeeld

In het volgende Python-voorbeeld ziet u hoe u een Ubuntu-groep configureert om het aandeel in een starttaak te monteren. Het bevestigingspunt, het eindpunt voor bestandsdelen en de opslagreferenties worden doorgegeven als gedefinieerde constanten. De starttaak wordt uitgevoerd onder een automatisch gebruikersaccount van een beheerder met een poolbereik.

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

Nadat u het aandeel hebt gemonteerd en een taak hebt gedefinieerd, gebruikt u het aandeel in de taakopdrachtregels. De volgende basisopdracht wordt `ls` bijvoorbeeld gebruikt om bestanden in de bestandsshare weer te geven.

```python
...
task = batch.models.TaskAddParameter(
    id='mytask',
    command_line="/bin/bash -c \"ls {}\"".format(_COMPUTE_NODE_MOUNT_POINT))

batch_service_client.task.add(job_id, task)
```


## <a name="next-steps"></a>Volgende stappen

* Zie het [overzicht batchfunctie](batch-api-basics.md) en [Taak- en taakuitvoer](batch-task-output.md)voor andere opties om gegevens in Batch te lezen en te schrijven.

* Zie ook de [Batch Shipyard toolkit,](https://github.com/Azure/batch-shipyard) die [Shipyard recepten](https://github.com/Azure/batch-shipyard/tree/master/recipes) bevat om bestandssystemen te implementeren voor Batch container workloads.
