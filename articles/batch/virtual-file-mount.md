---
title: Een virtueel bestandssysteem op een groep monteren - Azure Batch | Microsoft Documenten
description: Meer informatie over het monteren van een virtueel bestandssysteem op een batchgroep.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919002"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Een virtueel bestandssysteem op een batchgroep monteren

Azure Batch ondersteunt nu het opzetten van cloudopslag of een extern bestandssysteem op Windows- of Linux-computenodes in uw batchgroepen. Wanneer een compute node lid wordt van een pool, wordt het virtuele bestandssysteem gemonteerd en behandeld als een lokaal station op dat knooppunt. U bestandssystemen zoals Azure Files, Azure Blob-opslag, Network File System (NFS) monteren, inclusief een [Avere vFXT-cache](../avere-vfxt/avere-vfxt-overview.md)of Common Internet File System (CIFS).

In dit artikel leert u hoe u een virtueel bestandssysteem monteren op een groep compute-knooppunten met behulp van de [BatchManagement-bibliotheek voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> Het monteren van een virtueel bestandssysteem wordt ondersteund op batchpools die zijn gemaakt op of na 2019-08-19. Batchpools die vóór 2019-08-19 zijn gemaakt, ondersteunen deze functie niet.
> 
> De API's voor het monteren van bestandssystemen op een compute node maken deel uit van de [Batch .NET-bibliotheek.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet)

## <a name="benefits-of-mounting-on-a-pool"></a>Voordelen van montage op een zwembad

Het opzetten van het bestandssysteem naar de groep, in plaats van taken hun eigen gegevens uit een grote gegevensset te laten ophalen, maakt het eenvoudiger en efficiënter voor taken om toegang te krijgen tot de benodigde gegevens.

Overweeg een scenario met meerdere taken die toegang vereisen tot een algemene set gegevens, zoals het renderen van een film. Elke taak maakt een of meer frames tegelijk uit de scènebestanden. Door een station te monteren dat de scènebestanden bevat, is het eenvoudiger voor compute nodes om toegang te krijgen tot gedeelde gegevens. Bovendien kan het onderliggende bestandssysteem onafhankelijk worden gekozen en geschaald op basis van de prestaties en schaal (doorvoer en IOPS) die vereist zijn door het aantal compute nodes dat gelijktijdig toegang heeft tot de gegevens. Een [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) gedistribueerde in-memory cache kan bijvoorbeeld worden gebruikt om grote renders op filmschaal te ondersteunen met duizenden gelijktijdige renderknooppunten, die toegang hebben tot brongegevens die on-premises aanwezig zijn. Als alternatief kan [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) voor gegevens die zich al in blobopslag in de cloud bevinden, worden gebruikt om deze gegevens te monteren als een lokaal bestandssysteem. Blobfuse is alleen beschikbaar op Linux-knooppunten, maar [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) biedt een vergelijkbare workflow en is beschikbaar op zowel Windows als Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Een virtueel bestandssysteem op een groep monteren  

Door een virtueel bestandssysteem op een groep te monteren, is het bestandssysteem beschikbaar voor elk compute-knooppunt in de groep. Het bestandssysteem wordt geconfigureerd wanneer een compute node lid wordt van een groep of wanneer het knooppunt opnieuw wordt gestart of opnieuw wordt weergegeven.

Als u een bestandssysteem op `MountConfiguration` een groep wilt monteren, maakt u een object. Kies het object dat bij `AzureBlobFileSystemConfiguration`uw `AzureFileShareConfiguration` `NfsMountConfiguration`virtuele `CifsMountConfiguration`bestandssysteem past: , , of .

Alle configuratieobjecten voor het monteren hebben de volgende basisparameters nodig. Sommige mount configuraties hebben parameters die specifiek zijn voor het bestandssysteem wordt gebruikt, die worden besproken in meer detail in de code voorbeelden.

- **Accountnaam of bron:** Als u een virtueel bestandsaandeel wilt maken, hebt u de naam van het opslagaccount of de bron ervan nodig.
- **Relatieve bevestigingspad of bron:** de locatie van het bestandssysteem dat `fsmounts` op het compute-knooppunt `AZ_BATCH_NODE_MOUNTS_DIR`is gemonteerd, ten opzichte van de standaardmap die toegankelijk is op het knooppunt via . De exacte locatie is afhankelijk van het besturingssysteem dat op het knooppunt wordt gebruikt. De fysieke locatie op een Ubuntu-knooppunt is `mnt\batch\tasks\fsmounts`bijvoorbeeld toegewezen aan , en op `mnt\resources\batch\tasks\fsmounts`een CentOS-knooppunt wordt deze toegewezen aan .
- **Opties voor het instellen of blobfuse-opties:** deze opties beschrijven specifieke parameters voor het monteren van een bestandssysteem.

Zodra `MountConfiguration` het object is gemaakt, `MountConfigurationList` wijst u het object toe aan de eigenschap wanneer u de groep maakt. Het bestandssysteem wordt gemonteerd wanneer een knooppunt lid wordt van een pool of wanneer het knooppunt opnieuw wordt gestart of opnieuw wordt weergegeven.

Wanneer het bestandssysteem is gemonteerd, `AZ_BATCH_NODE_MOUNTS_DIR` wordt een omgevingsvariabele gemaakt die verwijst naar de locatie van de gemonteerde bestandssystemen en logbestanden, die nuttig zijn voor het oplossen en debuggen. Logbestanden worden in de sectie [Diagnose-bevestigingsfouten](#diagnose-mount-errors) nader toegelicht.  

> [!IMPORTANT]
> Het maximum aantal gemonteerde bestandssystemen op een pool is 10. Zie [Batchservicequota en -limieten](batch-quota-limit.md#other-limits) voor details en andere limieten.

## <a name="examples"></a>Voorbeelden

De volgende codevoorbeelden tonen het monteren van verschillende bestandsshares aan een pool van compute nodes.

### <a name="azure-files-share"></a>Azure-bestanden delen

Azure Files is het standaard Azure-cloudbestandssysteemdat wordt aangeboden. Zie [Een Azure-bestandsaandeel gebruiken](../storage/files/storage-how-to-use-files-windows.md)voor meer informatie over het zoeken naar een van de parameters in het voorbeeld van de configuratiecode voor het monteren.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob-bestandssysteem

Een andere optie is het gebruik van Azure Blob-opslag via [blobfuse.](../storage/blobs/storage-how-to-mount-container-linux.md) Het monteren van een `AccountKey` `SasKey` blob-bestandssysteem vereist een of voor uw opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../storage/common/storage-account-keys-manage.md)of [Delen van toegangshandtekeningen (SAS) gebruiken voor](../storage/common/storage-dotnet-shared-access-signature-part-1.md)informatie over het ophalen van deze sleutels. Zie de [veelgestelde vragen](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)over het oplossen van problemen met blobfuse voor meer informatie over het gebruik van blobfuse. Voer de taak uit als **administrator**om standaardtoegang te krijgen tot de blobfuse-map. Blobfuse monteert de map op de gebruikersruimte en bij het maken van de pool wordt deze als root gemonteerd. In Linux zijn alle **administratortaken** root. Alle opties voor de FUSE-module worden beschreven op de [referentiepagina VAN FUSE.](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)

Naast de handleiding voor het oplossen van problemen zijn GitHub-problemen in de blobfuse-opslagplaats een handige manier om te controleren op huidige blobfuse-problemen en -oplossingen. Zie [blobfuse-problemen voor](https://github.com/Azure/azure-storage-fuse/issues)meer informatie .

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Netwerkbestandssysteem

Network File Systems (NFS) kunnen ook worden gemonteerd op knooppunten die traditionele bestandssystemen eenvoudig toegankelijk maken voor Azure Batch-knooppunten. Dit kan een enkele NFS-server zijn die in de cloud wordt geïmplementeerd of een on-premises NFS-server die via een virtueel netwerk wordt geopend. U ook profiteren van de [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) gedistribueerde in-memory cache-oplossing, die naadloze connectiviteit biedt met on-premises opslag, gegevens on-demand in de cache leest en hoge prestaties en schaal levert aan cloudgebaseerde compute nodes.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Gemeenschappelijk internetbestandssysteem

Common Internet File Systems (CIFS) kan ook worden gemonteerd op knooppunten die traditionele bestandssystemen gemakkelijk toegankelijk maken voor Azure Batch-knooppunten. CIFS is een protocol voor het delen van bestanden dat een open en cross-platform mechanisme biedt voor het aanvragen van netwerkserverbestanden en -services. CIFS is gebaseerd op de verbeterde versie van microsoft's Server Message Block (SMB) protocol voor het delen van internet en intranet bestanden en wordt gebruikt om externe bestandssystemen op Windows-knooppunten te monteren. Zie [Bestandsserver en smb](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)voor meer informatie over SMB.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Bevestigingsfouten diagnosticeren

Als een bevestigingsconfiguratie mislukt, mislukt het compute-knooppunt in de groep en wordt de knooppuntstatus onbruikbaar. Als u een configuratiefout [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) wilt diagnosticeren, controleert u de eigenschap op details over de fout.

Als u de logboekbestanden voor foutopsporing wilt `*.log` ophalen, gebruikt u [Uitvoerbestanden](batch-task-output-files.md) om de bestanden te uploaden. De `*.log` bestanden bevatten informatie over de `AZ_BATCH_NODE_MOUNTS_DIR` bestandssysteem mount op de locatie. Mount log bestanden hebben `<type>-<mountDirOrDrive>.log` het formaat: voor elke mount. Bijvoorbeeld, een `cifs` mount op een `test` mount directory met de `cifs-test.log`naam zal een mount log bestand met de naam: .

## <a name="supported-skus"></a>Ondersteunde SKU's

| Uitgever | Aanbieding | SKU | Azure-bestanden delen | Blobfuse | NFS-bevestiging | CIFS-bevestiging |
|---|---|---|---|---|---|---|
| batch | rendering-centos73 | rendering | :heavy_check_mark: <br>Opmerking: Compatibel met CentOS 7.7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-advertenties | linux-data-science-vm | linuxdsvm linuxdsvm | :heavy_check_mark: <br>Opmerking: Compatibel met CentOS 7.4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Opmerking: ondersteunt A_8 of 9-opslag</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van een Azure Files-share met [Windows](../storage/files/storage-how-to-use-files-windows.md) of [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Meer informatie over het gebruik en de montage van virtuele bestandssystemen [van blobfuse.](https://github.com/Azure/azure-storage-fuse)
- Zie [overzicht van het netwerkbestandssysteem](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) voor meer informatie over NFS en de bijbehorende toepassingen.
- Zie [overzicht van het Microsoft SMB-protocol en het CIFS-protocol](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) voor meer informatie over CIFS.
