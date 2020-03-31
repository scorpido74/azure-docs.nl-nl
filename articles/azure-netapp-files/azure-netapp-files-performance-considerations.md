---
title: Prestatieoverwegingen voor Azure NetApp-bestanden | Microsoft Documenten
description: Beschrijft prestatieoverwegingen voor Azure NetApp-bestanden.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67454137"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Prestatie-overwegingen voor Azure NetApp Files

De [doorvoerlimiet](azure-netapp-files-service-levels.md) voor een volume wordt bepaald door een combinatie van het quotum dat is toegewezen aan het volume en het geselecteerde serviceniveau. Wanneer u prestatieplannen maakt voor Azure NetApp-bestanden, moet u verschillende overwegingen begrijpen. 

## <a name="quota-and-throughput"></a>Quotum en doorvoer  

De doorvoerlimiet is slechts één bepalende factor voor de werkelijke prestaties die zullen worden gerealiseerd.  

Typische overwegingen op het het uitvoeren van opslagprestaties, waaronder lees- en schrijfmix, de overdrachtsgrootte, willekeurige of sequentiële patronen en vele andere factoren zullen bijdragen aan de totale geleverde prestaties.  

De maximale empirische doorvoer die is waargenomen in het testen is 4.500 MiB/s.  Op de opslaglaag Premium biedt een volumequotum van 70,31 TiB een doorvoerlimiet die hoog genoeg is om dit prestatieniveau te bereiken.  

Als u overweegt volumequotumbedragen toe te wijzen boven 70,31 TiB, kan een extra quotum worden toegewezen aan een volume voor het opslaan van aanvullende gegevens. Het toegevoegde quotum zal echter niet leiden tot een verdere toename van de werkelijke doorvoer.  

Zie [Prestatiebenchmarks voor Azure NetApp-bestanden](azure-netapp-files-performance-benchmarks.md) voor meer informatie.

## <a name="overprovisioning-the-volume-quota"></a>Overprovisioning van het volumequotum

Als de prestaties van een werkbelasting doorvoerlimiet gebonden zijn, is het mogelijk om het volumequotum te overprovisioneren om een hoger doorvoerniveau in te stellen en hogere prestaties te bereiken.  

Als een volume in de Premium-opslaglaag bijvoorbeeld slechts 500 GiB-gegevens heeft, maar 128 MiB/s doorvoer vereist, u het quotum instellen op 2 TiB, zodat het doorvoerniveau dienovereenkomstig wordt ingesteld (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Als u een volume consistent overprovisiont voor het bereiken van een hogere doorvoer, u in plaats daarvan overwegen een hoger serviceniveau te gebruiken.  In het bovenstaande voorbeeld u dezelfde doorvoerlimiet bereiken met de helft van het volumequotum door in plaats daarvan de Ultra-opslaglaag (128 MiB/s per TiB * 1 TiB = 128 MiB/s) te gebruiken.

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamisch verhogen of verlagen van volumequota

Als uw prestatievereisten tijdelijk van aard zijn of als u gedurende een bepaalde periode de prestatiebehoefte hebt verhoogd, u het volumequotum dynamisch verhogen of verlagen om de doorvoerlimiet onmiddellijk aan te passen.  Let op de volgende overwegingen: 

* Volumequota kunnen worden verhoogd of verlaagd zonder dat io hoeft te worden onderbroken en de toegang tot het volume wordt niet onderbroken of beïnvloed.  

    U het quotum tijdens een actieve I/O-transactie aanpassen aan een volume.  Houd er rekening mee dat het volumequotum nooit lager kan zijn dan de hoeveelheid logische gegevens die in het volume wordt opgeslagen.

* Wanneer het volumequotum wordt gewijzigd, is de bijbehorende wijziging in doorvoerlimiet vrijwel onmiddellijk. 

    De wijziging onderbreekt of beïnvloedt de volumetoegang of I/O niet.  

* Voor het aanpassen van het volumequotum is een wijziging in de grootte van de capaciteitsgroep vereist.  

    De grootte van de capaciteitspool kan dynamisch en zonder gevolgen voor de beschikbaarheid van het volume of I/O worden aangepast.

## <a name="next-steps"></a>Volgende stappen

- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatiebenchmarks voor Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)