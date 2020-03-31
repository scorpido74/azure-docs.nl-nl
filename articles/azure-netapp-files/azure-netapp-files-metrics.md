---
title: Statistieken voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft statistieken voor Azure NetApp-bestanden.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460429"
---
# <a name="metrics-for-azure-netapp-files"></a>Metrische gegevens voor Azure NetApp Files

Azure NetApp Files biedt statistieken over toegewezen opslag, daadwerkelijk opslaggebruik, volume-IOPS en latentie. Door deze statistieken te analyseren, u een beter inzicht krijgen in het gebruikspatroon en de volumeprestaties van uw NetApp-accounts.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Gebruiksstatistieken voor capaciteitsgroepen

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pool toegewezen aan volumegrootte*  
    Het totaal van het volumequotum (GiB) in een bepaalde capaciteitsgroep (dat wil zeggen het totaal van de in gerichte volumes in de capaciteitsgroep).  
    Deze grootte is de grootte die u hebt geselecteerd tijdens het maken van het volume.  
- *Geconsumed Size*  
    Het totaal van logische ruimte (GiB) dat wordt gebruikt voor volumes in een capaciteitspool.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Gebruiksstatistieken voor volumes

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Volume verbruikt grootte*   
    De totale logische ruimte die wordt gebruikt in een volume (GiB).  
    Deze grootte omvat logische ruimte die wordt gebruikt door actieve bestandssystemen en momentopnamen.  
- *Grootte van volumemomentopnamen*   
   De incrementele logische ruimte die wordt gebruikt door momentopnamen in een volume.  

## <a name="performance-metrics-for-volumes"></a>Prestatiestatistieken voor volumes

- *Gemiddelde leeslatentie*   
    De gemiddelde tijd voor reads van het volume in milliseconden.
- *Gemiddelde writelatentie*   
    De gemiddelde tijd voor schrijft van het volume in milliseconden.
- *ReadIops*   
    Het aantal reads naar het volume per seconde.
- *Schrijfiops*   
    Het aantal schrijft aan het volume per seconde.

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
