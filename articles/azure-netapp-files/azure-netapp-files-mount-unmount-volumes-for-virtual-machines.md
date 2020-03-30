---
title: Azure NetApp-bestandenvolumes voor virtuele machines monteren
description: Meer informatie over het monteren of ontkoppelen van een volume voor virtuele Windows-machines of Virtuele Linux-machines in Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551536"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen 

U indien nodig een volume voor virtuele Windows- of Linux-machines monteren of ontkoppelen.  De bevestigingsinstructies voor virtuele Linux-machines zijn beschikbaar op Azure NetApp-bestanden.  

1. Klik op het blad **Volumes** en selecteer het volume waarvoor u wilt monteren. 
2. Klik **op Instructies monteren** van het geselecteerde volume en volg de instructies om het volume te monteren. 

    ![Instructies voor monteren NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Montage-instructies SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Als u NFSv4.1 gebruikt, gebruikt u de volgende opdracht om uw bestandssysteem te monteren:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Als u een NFS-volume automatisch wilt laten monteren wanneer een Azure VM wordt `/etc/fstab` gestart of opnieuw opgestart, voegt u een vermelding toe aan het bestand op de host. 

    Bijvoorbeeld: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`is het IP-adres van het Azure NetApp Files-volume dat wordt gevonden in het blad volume-eigenschappen.
    * `$FILEPATH`is het exportpad van het Azure NetApp-bestandenvolume.
    * `$MOUNTPOINT`is de map die is gemaakt op de Linux-host die wordt gebruikt om de NFS-export te monteren.

4. Als u het volume met NFS op Windows wilt monteren:

    a. Monteer het volume eerst op een Unix- of Linux-VM.  
    b. Voer `chmod 777` een `chmod 775` of opdracht uit tegen het volume.  
    c. Monteer het volume via de NFS-client op Windows.

## <a name="next-steps"></a>Volgende stappen

* [NFSv4.1-standaarddomein configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [VEELGESTELDE NFS-veelgestelde vragen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
