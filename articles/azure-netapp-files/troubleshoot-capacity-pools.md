---
title: Problemen met de capaciteits pool oplossen voor Azure NetApp Files | Microsoft Docs
description: Hierin worden mogelijke problemen beschreven die zich kunnen voordoen bij het beheren van capaciteits groepen en oplossingen voor de problemen.
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
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343321"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Problemen met de capaciteits pool oplossen

In dit artikel worden oplossingen beschreven voor problemen die u mogelijk hebt bij het beheren van capaciteits groepen. 

## <a name="issues-creating-a-capacity-pool"></a>Problemen met het maken van een capaciteits pool

Zorg ervoor dat het aantal capaciteits groepen de limiet niet overschrijdt. Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md).  Als de telling kleiner is dan de limiet en u nog steeds problemen ondervindt, kunt u een ondersteunings ticket indienen en de naam van de capaciteits groep opgeven.

## <a name="issues-deleting-a-capacity-pool"></a>Problemen met het verwijderen van een capaciteits pool

Zorg ervoor dat u alle Azure NetApp Files volumes en moment opnamen in het abonnement waarvoor u de capaciteits groep probeert te verwijderen, hebt verwijderd.   

Als u alle volumes en moment opnamen al hebt verwijderd en u nog steeds niet kunt verwijderen van de capaciteits groep, kunnen er mogelijk nog verwijzingen naar resources bestaan zonder dat ze in de portal worden weer gegeven. In dit geval moet u een ondersteunings ticket indienen en opgeven dat u de hierboven aanbevolen stappen hebt uitgevoerd. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>Het maken of wijzigen van een volume mislukt met de fout ' aangevraagde door Voer is niet beschikbaar '

Beschik bare door Voer voor een volume wordt bepaald door de grootte van de capaciteits groep en het service niveau. Als u onvoldoende door Voer hebt, moet u de pool grootte verg Roten of de bestaande volume doorvoer aanpassen.


## <a name="next-steps"></a>Volgende stappen  

* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een hand matige QoS-capaciteits groep beheren](manage-manual-qos-capacity-pool.md)
