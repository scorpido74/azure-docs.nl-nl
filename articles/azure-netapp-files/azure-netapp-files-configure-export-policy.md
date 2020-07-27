---
title: Export beleid configureren voor NFS-volume-Azure NetApp Files
description: Hierin wordt beschreven hoe u het beleid voor exporteren configureert om de toegang tot een NFS-volume te beheren met behulp van Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169826"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportbeleid voor een NFS-volume configureren

U kunt beleid voor exporteren configureren om de toegang tot een Azure NetApp Files volume te beheren. Azure NetApp Files export beleid ondersteunt volumes die gebruikmaken van het NFS-protocol (zowel NFSv3 als NFSv 4.1) als het dubbele Protocol (NFSv3 en SMB). 

U kunt Maxi maal vijf export beleids regels maken.

## <a name="steps"></a>Stappen 

1.  Selecteer op de pagina volumes het volume waarvoor u het export beleid wilt configureren en klik op **beleid exporteren**. 

    U kunt het export beleid ook configureren tijdens het maken van het volume.

2.  Geef voor de volgende velden informatie op om een exportbeleidsregel te maken:   
    *  **TabIndex**   
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

    * **Hoofd toegang**  
        Geef op of het `root` account toegang heeft tot het volume.  Hoofd toegang is standaard ingesteld **op aan**en het `root` account heeft toegang tot het volume.

        ![Exportbeleid](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Volgende stappen 
* [Volumes beheren](azure-netapp-files-manage-volumes.md)
* [Een volume voor virtuele machines koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Momentopnamen beheren](azure-netapp-files-manage-snapshots.md)
