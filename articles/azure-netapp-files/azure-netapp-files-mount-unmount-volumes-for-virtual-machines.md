---
title: Een Azure NetApp Files-volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines | Microsoft Docs
description: Hierin wordt beschreven hoe u een volume koppelt of ontkoppelt voor virtuele machines of virtuele Linux-machines.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173587"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen 

U kunt indien nodig een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines.  De koppelings instructies voor virtuele Linux-machines zijn beschikbaar op Azure NetApp Files.  

1. Klik op de Blade **volumes** en selecteer vervolgens het volume dat u wilt koppelen. 
2. Klik op **koppelings instructies** van het geselecteerde volume en volg de instructies om het volume te koppelen. 

    ![Koppelings instructies NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Koppelings instructies SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Als u NFSv 4.1 gebruikt, gebruikt u de volgende opdracht om het bestands systeem te koppelen: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Als u een NFS-volume automatisch wilt koppelen wanneer een virtuele machine van Azure wordt gestart of opnieuw is opgestart, voegt u een vermelding toe aan het `/etc/fstab` bestand op de host. 

    Bijvoorbeeld:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` is het IP-adres van het Azure NetApp Files volume dat op de Blade met de volume-eigenschappen is gevonden.
    * `$FILEPATH` het exportpad van het Azure NetApp Files volume is.
    * `$MOUNTPOINT` is de map die is gemaakt op de Linux-host die wordt gebruikt om de NFS-export te koppelen.

## <a name="next-steps"></a>Volgende stappen

* [Het standaard domein NFSv 4.1 configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Veelgestelde vragen over NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
