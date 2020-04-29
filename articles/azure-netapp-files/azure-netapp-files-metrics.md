---
title: Metrische gegevens voor Azure NetApp Files | Microsoft Docs
description: Beschrijft de metrische gegevens voor Azure NetApp Files.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460429"
---
# <a name="metrics-for-azure-netapp-files"></a>Metrische gegevens voor Azure NetApp Files

Azure NetApp Files voorziet in metrische gegevens over de toegewezen opslag, het werkelijke gebruik van de opslag, het volume-IOPS en de latentie. Door deze metrische gegevens te analyseren, kunt u een beter inzicht krijgen in het gebruiks patroon en de volume prestaties van uw NetApp-accounts.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metrische gegevens over gebruik voor capaciteits Pools

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Groep toegewezen aan volume grootte*  
    Het totale volume quotum (GiB) in een opgegeven capaciteits groep (dat wil zeggen, het totaal van de ingerichte grootte van het volume in de capaciteits groep).  
    Deze grootte is het formaat dat u hebt geselecteerd tijdens het maken van het volume.  
- *Verbruikte grootte van pool*  
    Het totale aantal logische ruimte (GiB) dat wordt gebruikt voor alle volumes in een capaciteits pool.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metrische gegevens over het gebruik voor volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Grootte van gebruikt volume*   
    De totale logische ruimte die wordt gebruikt in een volume (GiB).  
    Deze grootte bevat logische ruimte die wordt gebruikt door actieve bestands systemen en moment opnamen.  
- *Grootte van moment opname van volume*   
   De incrementele logische ruimte die wordt gebruikt door moment opnamen in een volume.  

## <a name="performance-metrics-for-volumes"></a>Prestatie gegevens voor volumes

- *AverageReadLatency*   
    De gemiddelde tijd voor lees bewerkingen van het volume in milliseconden.
- *AverageWriteLatency*   
    De gemiddelde tijd voor het schrijven van het volume in milliseconden.
- *ReadIops*   
    Het aantal lees bewerkingen naar het volume per seconde.
- *WriteIops*   
    Het aantal schrijf bewerkingen naar het volume per seconde.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
