---
title: Service niveaus voor Azure NetApp Files | Microsoft Docs
description: Beschrijft de doorvoer prestaties voor de service niveaus van Azure NetApp Files.
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
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832587"
---
# <a name="service-levels-for-azure-netapp-files"></a>Serviceniveau's voor Azure NetApp Files
Service niveaus zijn een kenmerk van een capaciteits groep. Service niveaus worden gedefinieerd en onderscheiden van de toegestane maximale door Voer voor een volume in de capaciteits pool op basis van het quotum dat is toegewezen aan het volume.

## <a name="supported-service-levels"></a>Ondersteunde service niveaus

Azure NetApp Files ondersteunt drie service niveaus: *Ultra*, *Premium*en *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    De ultra Storage-laag biedt tot 128 MiB/s aan de door Voer per 1 TiB aan toegewezen volume quota. 

* <a name="Premium"></a>Premium-opslag

    De Premium Storage-laag biedt tot 64 MiB/s aan de door Voer per 1 TiB aan toegewezen volume quota. 

* <a name="Standard"></a>Standaard opslag

    De Standard-opslaglaag biedt Maxi maal 16 MiB/s aan door Voer per 1 TiB aan toegewezen volume quota.

## <a name="throughput-limits"></a>Doorvoerlimieten

De doorvoer limiet voor een volume wordt bepaald door de combi natie van de volgende factoren:
* Het service niveau van de capaciteits groep waartoe het volume behoort
* Het quotum dat is toegewezen aan het volume  

Dit concept wordt geïllustreerd in het onderstaande diagram:

![Service niveau-afbeelding](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

In voor beeld 1 hierboven krijgt een volume van een capaciteits groep met de laag voor de Premium-opslaglaag die 2 TiB aan quota toegewezen, een doorvoer limiet van 128 MiB/s (2 TiB * 64 MiB/s) toegewezen. Dit scenario is van toepassing ongeacht de grootte van de capaciteits groep of het werkelijke volume verbruik.

In voor beeld 2 hierboven krijgt een volume van een capaciteits pool met de laag Premium-opslag toegewezen aan 100 GiB aan quotum een doorvoer limiet van 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Dit scenario is van toepassing ongeacht de grootte van de capaciteits groep of het werkelijke volume verbruik.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [pagina met prijzen van Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) voor informatie over de prijs van verschillende serviceniveaus
- Zie het [kosten model voor Azure NetApp files](azure-netapp-files-cost-model.md) voor het berekenen van het capaciteits verbruik in een capaciteits groep 
- [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
- Zie [Service Level Agreement (Sla) voor Azure NetApp files](https://azure.microsoft.com/support/legal/sla/netapp/)