---
title: Export beleid configureren voor NFS-volume-Azure NetApp Files
description: Hierin wordt beschreven hoe u het beleid voor exporteren configureert om de toegang tot een NFS-volume te beheren met behulp van Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533195"
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

    * **Alleen-lezen** en **lezen/schrijven**  
        Als u Kerberos-versleuteling met NFSv 4.1 gebruikt, volgt u de instructies in [Configure nfsv 4.1 Kerberos Encryption](configure-kerberos-encryption.md).  Zie [prestaties beïnvloeden van Kerberos op nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance)voor de prestaties van Kerberos. 

        ![Kerberos-beveiligings opties](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Hoofd toegang**  
        Geef op of het `root` account toegang heeft tot het volume.  Hoofd toegang is standaard ingesteld **op aan**en het `root` account heeft toegang tot het volume.

![Exportbeleid](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Volgende stappen 
* [Een volume voor virtuele machines koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Momentopnamen beheren](azure-netapp-files-manage-snapshots.md)
