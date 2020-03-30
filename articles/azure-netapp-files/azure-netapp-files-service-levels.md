---
title: Serviceniveaus voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft doorvoerprestaties voor de serviceniveaus van Azure NetApp-bestanden.
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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832587"
---
# <a name="service-levels-for-azure-netapp-files"></a>Serviceniveau's voor Azure NetApp Files
Serviceniveaus zijn een kenmerk van een capaciteitsgroep. Serviceniveaus worden gedefinieerd en gedifferentieerd door de toegestane maximale doorvoer voor een volume in de capaciteitsgroep op basis van het quotum dat aan het volume is toegewezen.

## <a name="supported-service-levels"></a>Ondersteunde serviceniveaus

Azure NetApp Files ondersteunt drie serviceniveaus: *Ultra,* *Premium*en *Standard*. 

* <a name="Ultra"></a>Ultra-opslag

    De Ultra-opslaglaag biedt maximaal 128 MiB/s doorvoer per toegewezen volumequotum van 1 TiB. 

* <a name="Premium"></a>Premium opslag

    De Premium-opslaglaag biedt maximaal 64 MiB/s doorvoer per toegewezen volumequotum van 1 TiB. 

* <a name="Standard"></a>Standaardopslag

    De standaardopslaglaag biedt maximaal 16 MiB/s doorvoer per 1 TiB toegewezen volumequotum.

## <a name="throughput-limits"></a>Doorvoerlimieten

De doorvoerlimiet voor een volume wordt bepaald door de combinatie van de volgende factoren:
* Het serviceniveau van de capaciteitspool waartoe het volume behoort
* Het aan het volume toegewezen quotum  

Dit concept wordt geïllustreerd in het onderstaande diagram:

![Illustratie serviceniveau](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

In voorbeeld 1 hierboven wordt aan een volume uit een capaciteitsgroep met de Premium-opslaglaag toegewezen die 2 TiB aan quota is toegewezen, een doorvoerlimiet van 128 MiB/s (2 TiB * 64 MiB/s) toegewezen. Dit scenario is van toepassing ongeacht de grootte van de capaciteitspool of het werkelijke volumeverbruik.

In voorbeeld 2 hierboven wordt een volume toegewezen van een capaciteitsgroep met de Premium-opslaglaag aan de premielaag met een quotum van 100 GiB ( 64 MiB/s). Dit scenario is van toepassing ongeacht de grootte van de capaciteitspool of het werkelijke volumeverbruik.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [pagina met prijzen van Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) voor informatie over de prijs van verschillende serviceniveaus
- Zie [Kostenmodel voor Azure NetApp-bestanden](azure-netapp-files-cost-model.md) voor de berekening van het capaciteitsverbruik in een capaciteitsgroep 
- [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
- Zie [SLA (Service Level Agreement) voor Azure NetApp-bestanden](https://azure.microsoft.com/support/legal/sla/netapp/)