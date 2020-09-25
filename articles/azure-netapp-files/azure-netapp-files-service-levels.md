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
ms.date: 09/22/2019
ms.author: b-juche
ms.openlocfilehash: 818b3b59b1113875b6486ffe64bc8d2d30d613d3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325454"
---
# <a name="service-levels-for-azure-netapp-files"></a>Serviceniveau's voor Azure NetApp Files
Service niveaus zijn een kenmerk van een capaciteits groep. Service niveaus worden gedefinieerd en onderscheiden van de toegestane maximale door Voer voor een volume in de capaciteits pool op basis van het quotum dat is toegewezen aan het volume.

## <a name="supported-service-levels"></a>Ondersteunde service niveaus

Azure NetApp Files ondersteunt drie service niveaus: *Ultra*, *Premium*en *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    De ultra Storage-laag biedt tot 128 MiB/s aan de door Voer per 1 TiB aan capaciteit. 

* <a name="Premium"></a>Premium-opslag

    De Premium Storage-laag biedt tot 64 MiB/s aan de door Voer per 1 TiB aan capaciteit. 

* <a name="Standard"></a>Standaard opslag

    De Standard-opslaglaag biedt Maxi maal 16 MiB/s aan door Voer per 1 TiB aan capaciteit die is ingericht.

## <a name="throughput-limits"></a>Doorvoerlimieten

De doorvoer limiet voor een volume wordt bepaald door de combi natie van de volgende factoren:
* Het service niveau van de capaciteits groep waartoe het volume behoort
* Het quotum dat is toegewezen aan het volume  
* Het QoS-type (*automatisch* of *hand matig*) van de capaciteits groep  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Voor beelden van doorvoer limieten van volumes in een automatische QoS-capaciteits groep

In het onderstaande diagram ziet u de voor beelden van de doorvoer limiet van volumes in een automatische QoS-capaciteits groep:

![Service niveau-afbeelding](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* In voor beeld 1 hierboven is een volume van een automatische QoS-capaciteits groep met de laag voor Premium-opslag toegewezen aan 2 TiB aan quota een doorvoer limiet van 128 MiB/s (2 TiB * 64 MiB/s). Dit scenario is van toepassing ongeacht de grootte van de capaciteits groep of het werkelijke volume verbruik.

* In voor beeld 2 hierboven krijgt een volume van een automatische QoS-capaciteits groep met de laag voor Premium-opslag toegewezen aan 100 GiB aan quotum een doorvoer limiet van 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Dit scenario is van toepassing ongeacht de grootte van de capaciteits groep of het werkelijke volume verbruik.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Voor beelden van doorvoer limieten van volumes in een hand matige QoS-capaciteits groep 

Als u een hand matige QoS-capaciteits groep gebruikt, kunt u de capaciteit en door Voer voor een volume onafhankelijk toewijzen. Wanneer u een volume in een hand matige QoS-capaciteits groep maakt, kunt u de waarde voor de door Voer (MiB/S) opgeven. De totale door Voer die is toegewezen aan volumes in een hand matige QoS-capaciteits pool is afhankelijk van de grootte van de pool en het service niveau. Het wordt beperkt door (grootte van de capaciteits pool in TiB x door Voer/TiB van de service niveau). Een groep van 10 TiB met het ultra service-niveau heeft bijvoorbeeld een totale doorvoer capaciteit van 1280 MiB/s (10 TiB x 128 MiB/s/TiB) die beschikbaar is voor de volumes.

Voor een SAP HANA systeem kan deze capaciteits groep worden gebruikt om de volgende volumes te maken. Elk volume biedt de afzonderlijke grootte en door Voer om te voldoen aan uw toepassings vereisten:

* SAP HANA gegevens volume: grootte 4 TB met Maxi maal 704 MiB/s
* SAP HANA logboek volume: grootte 0,5 TB met Maxi maal 256 MiB/s
* SAP HANA gedeeld volume: grootte 1 TB met Maxi maal 64 MiB/s
* SAP HANA-back-upvolume: grootte 4,5 TB met Maxi maal 256 MiB/s

In het onderstaande diagram ziet u de scenario's voor de SAP HANA volumes:

![QoS SAP HANA-volume scenario's](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Volgende stappen

- [Pagina met Azure NetApp Files prijzen](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Kostenmodel voor Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
- [Service Level Agreement (SLA) voor Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Het serviceniveau van een volume dynamisch wijzigen](dynamic-change-volume-service-level.md) 
- [Serviceniveau doelstellingen voor replicatie tussen regio's](cross-region-replication-introduction.md#service-level-objectives)