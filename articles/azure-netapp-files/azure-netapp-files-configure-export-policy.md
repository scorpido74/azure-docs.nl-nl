---
title: Beleid voor exporteren configureren voor een NFS-volume met behulp van Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u het beleid voor exporteren configureert om de toegang tot een NFS-volume te beheren met behulp van Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674933"
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
