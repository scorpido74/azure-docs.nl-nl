---
title: Hand matige QoS-capaciteits groep van Azure NetApp Files | Microsoft Docs
description: Biedt een inleiding tot de hand matige QoS-capaciteits groep en verwijst naar aanvullende informatie.
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935759"
---
# <a name="manual-qos-capacity-pool"></a>Hand matige QoS-capaciteits groep

Dit artikel bevat een inleiding tot de functionaliteit van de capaciteits groep hand matige Quality of Service (QoS).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Hoe hand matige QoS verschilt van automatische QoS

Het [QoS-type](azure-netapp-files-understand-storage-hierarchy.md#qos_types) is een kenmerk van een capaciteits groep. Azure NetApp Files biedt twee QoS-typen capaciteits Pools: automatisch (standaard) en hand matig.  

In een *hand matige* QoS-capaciteits groep kunt u de capaciteit en door Voer voor een volume onafhankelijk toewijzen. De totale doorvoer van alle volumes die met een handmatige QoS-capaciteitspool zijn gemaakt, wordt beperkt door de totale doorvoer van de pool. Deze wordt bepaald door de combinatie van poolgrootte en de doorvoer op serviceniveau. 

In een *automatische* QoS-capaciteits groep wordt de door Voer automatisch toegewezen aan de volumes in de pool, evenredig aan het grootte quotum dat aan de volumes is toegewezen.  

Zie [opslag hiërarchie van Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) en [prestatie overwegingen voor Azure NetApp files](azure-netapp-files-performance-considerations.md) voor overwegingen met betrekking tot QoS-typen.

## <a name="how-to-specify-the-manual-qos-type"></a>Het type hand matige QoS opgeven

Wanneer u [een capaciteits pool maakt](azure-netapp-files-set-up-capacity-pool.md), kunt u opgeven dat de capaciteits groep het hand matige QoS-type moet gebruiken.  U kunt ook [een bestaande capaciteits groep wijzigen](manage-manual-qos-capacity-pool.md#change-to-qos) om het hand matige QoS-type te gebruiken. 

Het capaciteits type instellen op hand matige QoS is een permanente wijziging. U kunt een hand matig QoS-type capaciteits programma niet converteren naar een automatische QoS-capaciteits groep. 

Als u het hand matige QoS-type wilt gebruiken, moet u [de functie registreren](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Volgende stappen

* [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Opslag hiërarchie](azure-netapp-files-understand-storage-hierarchy.md) 
* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Prestatie-overwegingen voor Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Een NFS-volume maken](azure-netapp-files-create-volumes.md)
* [Een SMB-volume maken](azure-netapp-files-create-volumes-smb.md)
* [Een volume met dubbele protocollen maken](create-volumes-dual-protocol.md)
* [Metrische gegevens voor Azure NetApp Files](azure-netapp-files-metrics.md)
* [Problemen met de capaciteits pool oplossen](troubleshoot-capacity-pools.md)
