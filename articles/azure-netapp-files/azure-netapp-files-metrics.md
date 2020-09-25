---
title: Metrische gegevens voor Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files voorziet in metrische gegevens over de toegewezen opslag, het werkelijke gebruik van de opslag, het volume-IOPS en de latentie. Gebruik deze metrische gegevens om inzicht te krijgen in het gebruik en de prestaties.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: f83baf7a038ad8cf17421c778deccbc7dc389d97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325552"
---
# <a name="metrics-for-azure-netapp-files"></a>Metrische gegevens voor Azure NetApp Files

Azure NetApp Files voorziet in metrische gegevens over de toegewezen opslag, het werkelijke gebruik van de opslag, het volume-IOPS en de latentie. Door deze metrische gegevens te analyseren, kunt u een beter inzicht krijgen in het gebruiks patroon en de volume prestaties van uw NetApp-accounts.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metrische gegevens over gebruik voor capaciteits Pools

- *Grootte toegewezen geheugen*   
    De ingerichte grootte van de pool.

- *Groep toegewezen aan volume grootte*  
    Het totale volume quotum (GiB) in een opgegeven capaciteits groep (dat wil zeggen, het totaal van de ingerichte grootte van het volume in de capaciteits groep).  
    Deze grootte is het formaat dat u hebt geselecteerd tijdens het maken van het volume.  

- *Verbruikte grootte van pool*  
    Het totale aantal logische ruimte (GiB) dat wordt gebruikt voor alle volumes in een capaciteits pool.  

- *Totale grootte van de moment opname voor de pool*    
    De som van de grootte van de moment opname van alle volumes in de pool.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metrische gegevens over het gebruik voor volumes

<!-- ANF-5023: fixed version: 2020.08, 2020.09
- *Percentage Volume Consumed Size*    
    The percentage of the volume consumed, including snapshots.  
-->
- *Grootte van toegewezen volume*   
    De ingerichte grootte van een volume
- *Volume quotum grootte*    
    De quotum grootte (GiB) waarmee het volume is ingericht.   
- *Grootte van gebruikt volume*   
    Logische grootte van het volume (gebruikte bytes).  
    Deze grootte bevat logische ruimte die wordt gebruikt door actieve bestands systemen en moment opnamen.  
- *Grootte van moment opname van volume*   
   De grootte van alle moment opnamen in een volume.  

## <a name="performance-metrics-for-volumes"></a>Prestatie gegevens voor volumes

- *Gemiddelde lees latentie*   
    De gemiddelde tijd voor lees bewerkingen van het volume in milliseconden.
- *Gemiddelde schrijf latentie*   
    De gemiddelde tijd voor het schrijven van het volume in milliseconden.
- *IOPS lezen*   
    Het aantal lees bewerkingen naar het volume per seconde.
- *IOPS schrijven*   
    Het aantal schrijf bewerkingen naar het volume per seconde.
- *MiB lezen/s*   
    Lees doorvoer in bytes per seconde.
- *MiB/s schrijven*   
    Schrijf doorvoer in bytes per seconde.

<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; future
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Metrische gegevens van de volume replicatie

- *Is de status van de volume replicatie in orde*   
    De voor waarde van de replicatie relatie. De status in orde wordt aangegeven door `1` . Een slechte status wordt aangegeven door `0` .

- *Wordt de overdracht van de volume replicatie*    
    Hiermee wordt aangegeven of de status van de volume replicatie ' Transfer ' is. 
 
- *Vertragings tijd voor volume replicatie*   
    De hoeveelheid tijd in seconden waarmee de gegevens op de mirror lags achter de bron. 

- *Duur van de laatste overdracht van volume replicatie*   
    De hoeveelheid tijd in seconden die het duurt voordat de laatste overdracht is voltooid. 

- *Grootte van laatste overdracht volume replicatie*    
    Het totale aantal bytes dat is overgedragen als onderdeel van de laatste overdracht. 

- *Voortgang van volume replicatie*    
    De totale hoeveelheid gegevens die wordt overgedragen voor de huidige overdrachts bewerking. 

- *Totale overdracht van volume replicatie*   
    De cumulatieve bytes die zijn overgedragen voor de relatie. 

## <a name="next-steps"></a>Volgende stappen

* [Informatie over de opslaghiërarchie van Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
