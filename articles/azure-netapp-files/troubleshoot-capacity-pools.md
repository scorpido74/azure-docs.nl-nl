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
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651051"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Problemen met de capaciteits pool oplossen

In dit artikel worden oplossingen beschreven voor problemen die u mogelijk hebt bij het beheren van capaciteits groepen. 

## <a name="error-conditions-and-resolutions"></a>Fout voorwaarden en oplossingen 

|     Foutvoorwaarde    |     Oplossing    |
|-|-|
| Problemen met het maken van een capaciteits pool |  Zorg ervoor dat het aantal capaciteits groepen de limiet niet overschrijdt. Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md).  Als de telling kleiner is dan de limiet en u nog steeds problemen ondervindt, kunt u een ondersteunings ticket indienen en de naam van de capaciteits groep opgeven. |
| Problemen met het verwijderen van een capaciteits pool  |  Zorg ervoor dat u alle Azure NetApp Files volumes en moment opnamen verwijdert in het abonnement waarin u de capaciteits groep probeert te verwijderen. <br> Als u alle volumes en moment opnamen al hebt verwijderd en u de capaciteits groep nog steeds niet kunt verwijderen, kunnen er mogelijk nog verwijzingen naar resources bestaan zonder dat ze in de portal worden weer gegeven. In dit geval moet u een ondersteunings ticket indienen en opgeven dat u de hierboven aanbevolen stappen hebt uitgevoerd. |
| Het maken of wijzigen van een volume mislukt met `Requested throughput not available` fout | Beschik bare door Voer voor een volume wordt bepaald door de grootte van de capaciteits groep en het service niveau. Als u onvoldoende door Voer hebt, moet u de pool grootte verg Roten of de bestaande volume doorvoer aanpassen. | 

## <a name="next-steps"></a>Volgende stappen  

* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
