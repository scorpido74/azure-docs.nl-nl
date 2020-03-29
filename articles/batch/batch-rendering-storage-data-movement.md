---
title: Opslag- en gegevensverkeer voor rendering - Azure Batch
description: Meer informatie over de verschillende opties voor opslag- en gegevensverplaatsingvoor het renderen van asset- en uitvoerbestandsworkloads.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390384"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opties voor opslag- en gegevensverplaatsing voor het renderen van activa- en uitvoerbestanden

Er zijn meerdere opties om de scène- en assetbestanden beschikbaar te maken voor de renderingtoepassingen op de chatberichten van de groep:

* [Azure blob-opslag:](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
  * Scène- en assetbestanden worden geüpload naar blob-opslag vanuit een lokaal bestandssysteem. Wanneer de toepassing wordt uitgevoerd door een taak, worden de vereiste bestanden gekopieerd van blob-opslag naar de VM, zodat ze kunnen worden geopend door de rendering-toepassing. De uitvoerbestanden worden door de renderingtoepassing naar de VM-schijf geschreven en vervolgens gekopieerd naar blobopslag.  Indien nodig kunnen de uitvoerbestanden worden gedownload van blob-opslag naar een lokaal bestandssysteem.
  * Azure blob-opslag is een eenvoudige en kosteneffectieve optie voor kleinere projecten.  Zoals alle asset bestanden nodig zijn op elke pool VM, dan zodra het aantal en de grootte van asset bestanden verhoogt zorg moet worden genomen om ervoor te zorgen dat de overdracht van bestanden zijn zo efficiënt mogelijk.  
* Azure-opslag als bestandssysteem met [blobfuse:](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
  * Voor Linux VM's kan een opslagaccount worden blootgesteld en gebruikt als een bestandssysteem wanneer het blobfuse-stuurprogramma voor virtuele bestandssystemen wordt gebruikt.
  * Deze optie heeft het voordeel dat het zeer kosteneffectief is, omdat er geen VM's nodig zijn voor het bestandssysteem, plus blobfuse caching op de VM's vermijdt herhaalde downloads van dezelfde bestanden voor meerdere taken en taken.  Gegevensverplaatsing is ook eenvoudig omdat de bestanden gewoon blobs zijn en standaard API's en hulpprogramma's, zoals azcopy, kunnen worden gebruikt om bestand te kopiëren tussen een on-premises bestandssysteem en Azure-opslag.
* Bestandssysteem of bestandsshare:
  * Afhankelijk van vm-besturingssysteem en prestatie-/schaalvereisten, omvatten de opties [azure-bestanden](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), het gebruik van een VM met gekoppelde schijven voor NFS, het gebruik van meerdere VM's met gekoppelde schijven voor een gedistribueerd bestandssysteem zoals GlusterFS, of het gebruik van een aanbod van derden.
  * [Avere Systems](https://www.averesystems.com/) is nu onderdeel van Microsoft en zal in de nabije toekomst oplossingen hebben die ideaal zijn voor grootschalige, krachtige rendering.  Met de Avere-oplossing kan een NFS- of SMB-cache in Azure worden gemaakt die werkt in combinatie met blobopslag of met on-premises NAS-apparaten.
  * Met een bestandssysteem kunnen bestanden rechtstreeks naar het bestandssysteem worden gelezen of geschreven of kunnen ze worden gekopieerd tussen bestandssysteem en de pool-VM's.
  * Met een gedeeld bestandssysteem kan een groot aantal elementen die worden gedeeld tussen projecten en taken worden gebruikt, waarbij taken worden renderen die alleen toegang hebben tot wat nodig is.

## <a name="using-azure-blob-storage"></a>Azure blob-opslag gebruiken

Er moet een blob-opslagaccount of een v2-opslagaccount voor algemene doeleinden worden gebruikt.  Deze twee opslagaccounttypen kunnen worden geconfigureerd met aanzienlijk hogere limieten in vergelijking met een v1-opslagaccount voor algemene doeleinden, zoals beschreven in [deze blogpost.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)  Wanneer geconfigureerd, zullen de hogere limieten veel betere prestaties en schaalbaarheid toelaten, vooral wanneer er veel pool VM's zijn die toegang hebben tot het opslagaccount.

### <a name="copying-files-between-client-and-blob-storage"></a>Bestanden kopiëren tussen client- en blobopslag

Om bestanden van en naar Azure-opslag te kopiëren, kunnen verschillende mechanismen worden gebruikt, waaronder de storage blob API, de [Azure Storage Data Movement Library,](https://github.com/Azure/azure-storage-net-data-movement)het azcopy command line tool voor [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) of [Linux,](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)en Azure Batch [Explorer](https://azure.github.io/BatchExplorer/).

Met azcopy kunnen bijvoorbeeld alle elementen in een map als volgt worden overgedragen:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Als u alleen gewijzigde bestanden wilt kopiëren, kan de parameter /XO worden gebruikt:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoerassetbestanden kopiëren van blobopslag naar VM's voor batchgroep

Er zijn een paar verschillende benaderingen om bestanden te kopiëren met de beste aanpak bepaald door de grootte van de taak activa.
De eenvoudigste aanpak is om alle assetbestanden te kopiëren naar de koppelingsvm's voor elke taak:

* Wanneer er bestanden zijn die uniek zijn voor een taak, maar die nodig zijn voor alle taken van een taak, kan een [taakvoorbereidingstaak](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) worden opgegeven om alle bestanden te kopiëren.  De taakvoorbereidingstaak wordt eenmaal uitgevoerd wanneer de eerste taaktaak op een vm wordt uitgevoerd, maar niet opnieuw wordt uitgevoerd voor volgende taaktaken.
* Er moet een taak voor [het vrijgeven](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) van taken worden opgegeven om de bestanden per taak te verwijderen zodra de taak is voltooid; dit voorkomt dat de VM-schijf wordt gevuld door alle taakassetbestanden.
* Wanneer er meerdere taken zijn met dezelfde elementen, met alleen incrementele wijzigingen in de assets voor elke taak, worden alle activabestanden nog steeds gekopieerd, zelfs als alleen een subset is bijgewerkt.  Dit zou inefficiënt zijn wanneer er veel grote asset bestanden.

Wanneer assetbestanden worden hergebruikt tussen taken, met alleen incrementele wijzigingen tussen taken, is een efficiëntere, maar iets meer betrokken aanpak het opslaan van assets in de gedeelde map op de VM en het synchroniseren van gewijzigde bestanden.

* De taakvoorbereidingstaak voert de kopie uit met azcopy met de parameter /XO naar de gedeelde VM-map die is opgegeven door AZ_BATCH_NODE_SHARED_DIR omgevingsvariabele.  Hiermee worden alleen gewijzigde bestanden naar elke vm gekopieerd.
* Er zal moeten worden nagedacht over de grootte van alle activa om ervoor te zorgen dat ze passen op de tijdelijke schijf van het zwembad VM's.

Azure Batch heeft ingebouwde ondersteuning voor het kopiëren van bestanden tussen een opslagaccount en batchgroepVM's.  [Taakbronbestanden](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) kopiëren bestanden van opslag naar vm's bij het bundelen en kunnen worden opgegeven voor de taakvoorbereidingstaak.  Helaas, wanneer er honderden bestanden is het mogelijk om een limiet en taken te mislukken hit.  Wanneer er grote aantallen activa zijn, wordt aanbevolen om de opdrachtregel azcopy te gebruiken in de taakvoorbereidingstaak, die jokertekens kan gebruiken en geen limiet heeft.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Uitvoerbestanden kopiëren naar blobopslag vanuit VM's van batchgroep

[Uitvoerbestanden](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) kunnen worden gebruikt voor kopieerbestanden van een pool-vm naar opslag.  Een of meer bestanden kunnen worden gekopieerd van de VM naar een opgegeven opslagaccount zodra de taak is voltooid.  De gerenderde uitvoer moet worden gekopieerd, maar het kan ook wenselijk zijn om logbestanden op te slaan.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Een blobfuse virtueel bestandssysteem gebruiken voor Linux VM-groepen

Blobfuse is een stuurprogramma voor virtueel bestandssysteem voor Azure Blob Storage, waarmee u via het Linux-bestandssysteem toegang hebt tot bestanden die als blobs in een opslagaccount zijn opgeslagen.

Poolknooppunten kunnen het bestandssysteem monteren wanneer deze is gestart of de houder kan gebeuren als onderdeel van een taakvoorbereidingstaak - een taak die alleen wordt uitgevoerd wanneer de eerste taak in een taak wordt uitgevoerd op een knooppunt.  Blobfuse kan worden geconfigureerd om zowel een ramdisk als de lokale VM's te gebruiken voor het incacheren van bestanden, wat de prestaties aanzienlijk verhoogt als meerdere taken op een knooppunt toegang krijgen tot een aantal van dezelfde bestanden.

[Voorbeeldsjablonen zijn beschikbaar](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) voor het uitvoeren van zelfstandige V-Ray-renders met behulp van een blobfuse-bestandssysteem en kunnen worden gebruikt als basis voor sjablonen voor andere toepassingen.

### <a name="accessing-files"></a>Toegang tot bestanden

Taaktaken geven paden op voor invoerbestanden en uitvoerbestanden met behulp van het gemonteerde bestandssysteem.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoerassetbestanden kopiëren van blobopslag naar VM's voor batchgroep

Aangezien bestanden gewoon blobs in Azure Storage zijn, kunnen standaard blob-API's, hulpprogramma's en UI's worden gebruikt om bestanden te kopiëren tussen een on-premises bestandssysteem en blobopslag; bijvoorbeeld azcopy, Storage Explorer, Batch Explorer, enz.

## <a name="using-azure-files-with-windows-vms"></a>Azure-bestanden gebruiken met Windows VM's

[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) biedt volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het SMB-protocol.  Azure Files is gebaseerd op Azure blob-opslag; het is [kostenefficiënt](https://azure.microsoft.com/pricing/details/storage/files/) en kan worden geconfigureerd met gegevensreplicatie naar een andere regio die zo wereldwijd overbodig is.  [Schaaldoelen](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) moeten worden gecontroleerd om te bepalen of Azure-bestanden moeten worden gebruikt, gezien de grootte van de prognosegroep en het aantal assetbestanden.

Er is een [blogpost](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) en [documentatie](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) over het monteren van een Azure File share.

### <a name="mounting-an-azure-files-share"></a>Een Azure-bestanden-share monteren

Om in Batch te kunnen worden gebruikt, moet een houderbewerking worden uitgevoerd telkens wanneer een taak wordt uitgevoerd, omdat het niet mogelijk is om de verbinding tussen taken voort te duren.  De eenvoudigste manier om dit te doen is cmdkey gebruiken om referenties te blijven gebruiken met behulp van de starttaak in de poolconfiguratie en vervolgens het aandeel voor elke taak te monteren.

Voorbeeld gebruik van cmdkey in een poolsjabloon (ontsnapt voor gebruik in JSON-bestand) – merk op dat bij het scheiden van de cmdkey-aanroep de gebruikerscontext voor de starttaak dezelfde moet zijn als die welke wordt gebruikt voor het uitvoeren van de taken:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Opdrachtregel taaktaak:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Toegang tot bestanden

Taaktaken geven paden op voor invoerbestanden en uitvoerbestanden met behulp van het gemonteerde bestandssysteem, met behulp van een toegewezen station of een UNC-pad.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Invoerassetbestanden kopiëren van blobopslag naar VM's voor batchgroep

Azure-bestanden worden ondersteund door alle belangrijke API's en hulpprogramma's die Azure Storage-ondersteuning hebben. bijvoorbeeld azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, enz.

[Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) is beschikbaar om bestanden automatisch te synchroniseren tussen een on-premises bestandssysteem en een Azure File-share.

## <a name="next-steps"></a>Volgende stappen

Zie de uitgebreide documentatie voor meer informatie over de opslagopties:

* [Azure blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Azure-bestanden](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
