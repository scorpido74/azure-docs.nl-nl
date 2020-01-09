---
title: Export beleid configureren voor NFS-volume-Azure NetApp Files
description: Hierin wordt beschreven hoe u het beleid voor exporteren configureert om de toegang tot een NFS-volume te beheren met behulp van Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551555"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportbeleid voor een NFS-volume configureren

U kunt desgewenst exportbeleid configureren om de toegang tot een Azure NetApp Files-volume te beheren. Azure NetApp Files export beleid ondersteunt alleen NFS-volumes.  Zowel NFSv3 als NFSv4 worden ondersteund. 

## <a name="steps"></a>Stappen 

1.  Klik op **beleid exporteren** in het navigatie deel venster Azure NetApp files. 

2.  Geef voor de volgende velden informatie op om een exportbeleidsregel te maken:   
    *  **Index**   
        Geef het indexnummer voor de regel op.  
        Een exportbeleid kan uit maximaal vijf regels bestaan. Regels worden geëvalueerd volgens hun volgorde in de lijst met indexnummers. Regels met lagere indexnummers worden als eerste geëvalueerd. De regel met indexnummer 1 wordt bijvoorbeeld geëvalueerd vóór de regel met indexnummer 2. 

    * **Toegestane clients**   
        Geef de waarde in een van de volgende indelingen op:  
        * IPv4-adres, bijvoorbeeld `10.1.12.24` 
        * IPv4-adres met een subnetmasker dat wordt uitgedrukt als een aantal bits, bijvoorbeeld `10.1.12.10/4`

    * **Toegang**  
        Selecteer een van de volgende toegangstypen:  
        * Geen toegang 
        * Lezen en schrijven
        * Alleen-lezen

    ![Exportbeleid](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Volgende stappen 
* [Volumes beheren](azure-netapp-files-manage-volumes.md)
* [Een volume voor virtuele machines koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Momentopnamen beheren](azure-netapp-files-manage-snapshots.md)
