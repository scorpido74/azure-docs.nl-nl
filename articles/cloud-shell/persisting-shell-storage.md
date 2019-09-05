---
title: Bestanden in Azure Cloud Shell persistent maken | Microsoft Docs
description: Overzicht van de wijze waarop Azure Cloud Shell bestanden persistent wilt maken.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: b2823c935d11ae99ab1d87ae708945721820ad8c
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306739"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Hoe Cloud Shell Storage werkt 
Cloud Shell bestanden persistent maken via de volgende methoden: 
* Er wordt een schijf kopie van `$Home` uw directory gemaakt om alle inhoud in de map te behouden. De schijf installatie kopie wordt opgeslagen in de opgegeven bestands share `acc_<User>.img` , `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`net zoals bij, en de wijzigingen worden automatisch gesynchroniseerd. 
* Koppel uw opgegeven bestands share als `clouddrive` uw `$Home` Directory voor directe interactie via bestands share. `/Home/<User>/clouddrive`is toegewezen aan `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Alle bestanden in uw `$Home` Directory, zoals SSH-sleutels, blijven behouden in de installatie kopie van de gebruikers schijf, die wordt opgeslagen in de gekoppelde bestands share. Pas aanbevolen procedures toe wanneer u gegevens in uw `$Home` Directory en gekoppelde bestands share persistent wilt maken.

## <a name="clouddrive-commands"></a>clouddrive-opdrachten

### <a name="use-the-clouddrive-command"></a>Gebruik de `clouddrive` opdracht
In Cloud shell kunt u een opdracht met de naam `clouddrive`, waarmee u de bestands share die is gekoppeld aan Cloud shell hand matig bijwerken.
![De opdracht ' clouddrive ' uitvoeren](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Orderverzamellijst`clouddrive`
Als u wilt weten welke bestands share is `clouddrive`gekoppeld als, `df` voert u de opdracht uit. 

Het bestandspad naar clouddrive toont de naam van uw opslag account en de bestands share in de URL. Bijvoorbeeld: `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Een nieuwe clouddrive koppelen

#### <a name="prerequisites-for-manual-mounting"></a>Vereisten voor hand matig koppelen
U kunt de bestands share die is gekoppeld aan Cloud shell, bijwerken met behulp van de `clouddrive mount` opdracht.

Als u een bestaande bestands share koppelt, moeten de opslag accounts zich in de geselecteerde Cloud Shell regio bevinden. Haal de locatie op door `env` de `ACC_LOCATION`uit te voeren en te controleren.

#### <a name="the-clouddrive-mount-command"></a>De `clouddrive mount` opdracht

> [!NOTE]
> Als u een nieuwe bestands share koppelt, wordt er een nieuwe gebruikers installatie kopie voor `$Home` uw directory gemaakt. De vorige `$Home` afbeelding wordt bewaard in de vorige bestands share.

Voer de `clouddrive mount` opdracht uit met de volgende para meters:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Voer uit `clouddrive mount -h`om meer details weer te geven, zoals hier wordt weer gegeven:

![De clouddrive-mount'command uitvoeren](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive ontkoppelen
U kunt een bestands share die is gekoppeld aan Cloud Shell, op elk gewenst moment ontkoppelen. Omdat Cloud Shell een gekoppelde bestands share moet worden gebruikt, wordt u gevraagd een andere bestands share te maken en koppelen tijdens de volgende sessie.

1. Voer `clouddrive unmount` uit.
2. Bevestigings-en bevestigings prompts.

De bestands share blijft bestaan, tenzij u deze hand matig verwijdert. Cloud Shell kunt niet meer op volgende sessies naar deze bestands share zoeken. Voer uit `clouddrive unmount -h`om meer details weer te geven, zoals hier wordt weer gegeven:

![De clouddrive-unmount'command uitvoeren](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Hoewel met deze opdracht geen resources worden verwijderd, hand matig verwijderen van een resource groep, opslag account of bestands share die is toegewezen aan Cloud shell, worden de `$Home` schijf kopie van de Directory en alle bestanden in de bestands share gewist. Deze actie kan niet ongedaan worden gemaakt.
## <a name="powershell-specific-commands"></a>Power shell-specifieke opdrachten

### <a name="list-clouddrive-azure-file-shares"></a>Azure `clouddrive` -bestands shares weer geven
Met `Get-CloudDrive` de cmdlet worden de gegevens van de Azure-bestands share `clouddrive` opgehaald die momenteel zijn gekoppeld door de in de Cloud shell. <br>
![Get-CloudDrive uitvoeren](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Ontkoppelen`clouddrive`
U kunt op elk gewenst moment een Azure-bestands share ontkoppelen die aan Cloud Shell is gekoppeld. Als de Azure-bestands share is verwijderd, wordt u gevraagd om een nieuwe Azure-bestands share te maken en te koppelen tijdens de volgende sessie.

Met `Dismount-CloudDrive` de cmdlet ontkoppelt u een Azure-bestands share van het huidige opslag account. Ontkoppelen van de `clouddrive` huidige sessie wordt beëindigd. De gebruiker wordt gevraagd een nieuwe Azure-bestands share te maken en koppelen tijdens de volgende sessie.
![Ontkoppeling uitvoeren-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Opmerking: Als u een functie in een bestand moet definiëren en deze aanroept vanuit de Power shell-cmdlets, moet de punt operator worden opgenomen. Bijvoorbeeld:. .\MyFunctions.ps1

## <a name="next-steps"></a>Volgende stappen
[Snelstartgids Cloud Shell](quickstart.md) <br>
[Meer informatie over de opslag van Microsoft Azure-bestanden](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Meer informatie over opslag Tags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
